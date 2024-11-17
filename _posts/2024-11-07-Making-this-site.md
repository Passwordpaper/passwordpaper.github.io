I have wanted to start writing on my own web site for a while. I needed to find a cheap way to host a web site. Ideally it would have its own domain, and I didn't want it to be plastered with advertising. I would think that there would be a cheap service that did that. If I wanted to host a web site from my house, I could do it on a $5 Raspberry Pi and maybe $10 worth of electricity every year. I assume my web site which will get maybe 100 hits per month costs literal pennies to host at scale each month. But the best I could find was like $4/month on WordPress.

I started looking into self-hosting. If I knew what I was doing, I'm pretty sure I could host a static site on AWS or Azure for pennies, but I was scared that I would make some config error and be hit with a big bill some month. I finally came across GitHub Pages somehow, and realized that I could just host a static site for free directly out of GitHub. I assume that all other software is slightly more expensive than it would otherwise be in order to make this amazing service available to me at no cost, but it feels better than trading away my attention to whatever Mark Zuckerberg is calling his company these days. Anyway, I started with a static site which [looked like utter garbage](https://passwordpaper.com/oldsite/) because it was raw HTML. To be fair, I loved it dearly, but then I was reading the docs and came across Jekyll. Turns out you just have to throw a couple of little text files into a GitHub repository and check some boxes and GitHub will render a folder full of text files into a kind-of-generic but decent-looking web site. So here I am.

If you too want to make a web site like this, you'll need to go read some docs to figure out how, but here's basically the steps I followed:
* Make a github account named whatever you want your site to be called
* Make a repository named [USERNAME].github.io (which will be the url of your web site if you don't purchase a domain)
* Make a _config.yml file. I copied one from the Jekyll docs or something. It defines defaults like how to display your posts.
* Make an index.md file to define your home page
* Make a folder called _posts full of text files named [YEAR]-[MONTH]-[DAY]-[TITLE].md. They will become pages linked from your home page.
* Go into the repository settings and turn on Pages
* You can purchase a domain and set it up to point to your web site for like $10 a year. You have to set it up with your domain registrar and in your user settings on GitHub and in your repository settings.

I had to do some extra configuration to use a non-default theme with a navigation bar. Now I have a blog with an RSS reader.
