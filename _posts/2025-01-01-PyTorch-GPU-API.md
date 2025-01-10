---
title: GPU-Accelerated Scientific Code in Python
---

This is my personal blog, not a coding blog, but I spent the last two weeks obsessing over code, so coding is personal. If you don't care about computer science, skim the post for the warm fuzzy feeling of seeing how happy I am doing computational research.

As part of my physics research, I've been using some C++ code which performs quantum mechanics calculations. I need to do the same set of linear algebra calculations millions of times with independently generated random inputs, and it ocurred to me that this sounded like the sort of thing GPUs would be good at. GPUs are designed to perform a lot of simple calculations at the same time. My old code is made to run on CPUs which are good doing a lot of complicated calculations one after another. For those who are unfamiliar, the recent AI revolution is largely the product of researchers turning a prediction algorithm based on linear algebra into many (relatively) smaller calculations that may all be run at the same time by a GPU. If you know how to multiply matrices and want some flavor for how things like ChatGPT are just linear algebra, [this page](https://prvnsmpth.github.io/animated-transformer/) seemed correct to me as of Jan 1, 2025. Anyway, the important thing is that I knew that a bunch of engineers had put a lot of effort into making many large linear algebra calculations run quickly at the same time on GPUs, and it seemed a shame not to use that for physics. I've been itching to recode my research code to take advantage of modern machine learning tools for months, and I finally started doing it a few weeks ago. I decided to use PyTorch, because I have access to a lot of tools which make rapidly testing Python code easier than testing C++, and the research computers at my university already have it installed. Python is much much slower than C++ code, but that shouldn't matter for reasons I explain below.

So far it seems like it's working! I think I've figured out how to turn my calculation into operations that PyTorch knows how to batch together, but I need to fix the mathematical details of the code. My parallel code is not actually faster than the old linear code if you feed it a calculation which in physics terms has many particles or many spatial points, but that was the whole point for me. I wanted to be able to get large calculations back faster for testing purposes, even if it makes more sense to use the robust linear code when I perform the calculations that I intend to publish. My hope was that I would be able to run the code on my laptop for testing. PyTorch has support for the GPU in my laptop, so I was excited to throw the calculation at my laptop after I showed that it was incredibly fast on the research GPU I tested it on. It didn't work at first. If I turned off GPU acceleration and ran it on the CPU, it worked fine, but PyTorch told me that the function I wanted (matrix determinants!) was not supported on my GPU. My friend was confused when I complained about this, and he asked me why it was possible for PyTorch to do some simple calculations on one backend but not another. The short answer is that CUDA is a different API than Metal, so you can’t use the same “source code” to talk to NVIDIA and M-series GPUs.

## CUDA isn’t necessarily machine code, it can be a set of API calls

Machine code is a set of ones and zeros which you can put directly on a processor to make it do useful stuff for you. An API is an interface for two machines to talk to each other. At the level of reality, machine code and API calls are both sequences of voltage shifts that you send on a wire to something. The primary difference is that machine code goes directly onto the registers of a processor and causes the processor to do stuff in order to give you an output. API calls are interpretted by a processor into machine code which then runs on some processor that gives you an output. It turns out that while the CUDA compiler can create machine code for NVIDIA GPUs, the code would be specific to a single type of GPU, and so [CUDA also includes an API](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#compilation-workflow) to make generalized code which talk to any of its GPUs via drivers. [Metal is the API which Apple provides](https://developer.apple.com/metal/) which allows you to make CPU code which can talk to any of its GPUs. When you make code using CUDA or Metal in their API forms, you run the code through a compiler which generates CPU machine code which makes API calls to GPU drivers. The machine code also needs to interpret the output of the GPUs into the answers to the calculations that you wanted. Moving data back and forth is much slower than calculation, so in practice, the output will often actually be the machine equivalent of "ok I did that and I'm holding onto the answer, so what should I do with it now?" and then the CPU and GPU go back and forth a few times and the answer only goes back to the CPU where it can be viewed once the entire calulation is complete. I assume that the PyTorch team doesn't want to have many different versions of PyTorch each compiled for every possible combination of popular CPU and GPU, so they use the API version of CUDA. This means that you install the version of PyTorch that works with your CPU (in practice, just install PyTorch with pip, and it will use its knowledge of what CPU it runs on to grab the right version of PyTorch), and that program will be able to talk to any NVIDIA GPU, including ones which come out after the version of PyTorch you're using was created.

The PyTorch extension which I am using works within Python, which is a program which turns lines of text into machine code that feeds into the processor one line at a time (this is called an "interpreter"). This is not a compiler, which takes a whole lot of lines of text and finds an efficient way to combine them all into machine code at once. Python code tends to run much slower than CPU code, because interpretting lines of code into machine code on the fly is slower than running all of the code at once when you already compiled it ahead of time. The CPU backend for PyTorch has some algorithms precompiled to run faster on common processors given a single Python command, and the compiler they used can turn source code into instructions for any supported CPU. That’s what compilers are for, so the CPU backend just works everywhere that PyTorch can run at all. CUDA is a set of instructions which allow CPUs to tell NVIDIA GPUs what to do, and torch wraps up a bunch of GPU instructions which do certain tasks into functions which python can use, but they specifically use the API version of CUDA, so you can’t send them to arbitrary GPUs. Metal is the API available for Apple M series GPUs, and it’s probably possible to rewrite everything for them that works in CUDA, but it’s not like there’s drop-in replacements between CUDA and Metal, so each function in PyTorch which knows how to make CUDA calls has to be rewritten to make Metal calls instead. This is implemented as PyTorch's MPS backend, which either performs tasks on M series GPUs or apolegetically tells you that the task isn't supported yet.

## Talking about me again

That whole thing aout how must of the time a CPU is sitting around waiting for the GPU to say it's ready for the next step is why I think I can use Python code for my enormous calculations. If everything I send to the GPU is bundled up into such a large task that it takes a second to run, then it doesn't matter whether I use C++ code which can run a million lines of code per second or Python code which can only run a hundred lines of code per second. I only need the CPU to talk to the GPU once per second. I found [this page](https://horace.io/brrr_intro.html) helpful when I was thinking about how to effectively accelerate calculations with GPUs. Based off of my testing so far, I think I can do things like wrap up 2000 sets of 4 subtasks which used to take a minute when I ran them all linearly on a CPU into four batches of 2000 tasks, but each batch of 2000 is sent to a GPU at once to be performed in parallel which takes a few seconds.

Unfortunately, PyTorch has, as far as I can tell, implemented the MPS backend based on what people actually use (or rather which things people have asked them to make available for MPS) rather than whether it was easy to implement functions based on the functions they already implemented. As a funny example, they support the function which does an LU decomposition and returns it in one array that looks like a matrix, but not the function which does an LU decomposition and returns it as literally the exact same numbers split up between two separate matrices with zeros in all of the extra slots. I doubt there’s any difference between those algorithms mathematically or on a processor, but I assume that formatting the arrays is nonzero effort and writing in all of the optional flags available to each function takes more effort. It took me literally three minutes to turn this LU function that was supported on my Mac GPU into a JIT compiled determinant function which worked on my GPU, even though the native determinant function wasn't supported in the MPS backend. I won't actually use that function because it doesn't support complex numbers, and I think I can accelerate enough of the rest of my calculation that running the matrix determinants on CPU won't slow me down much. I can even write my code so that I can get the next GPU task going while my CPU chews on matrix determinants.

Hopefully this moderately technical post isn't too much for general consumption, but I thought other people might be interested in some of the details of how modern machine learning tools could be used for scientific research outside of the machine learning regime.