I have wanted to start writing on my own web site for a while. The only thing I like about social media is seeing what my friends are up to, and I don't like the idea of all of my actions being recorded and monetized by some company. I don't like it when I go to look at some link someone sent me, and I need to log in to view the thing or install some app on my phone that can track my location. I wish that all of my friends had blogs with an RSS feed, and I could plug those into my RSS reader and only get notifications when people I actually want to hear about say something instead of needing to go scroll through some attention-stealing mire of irrelevant content every few days on the off-chance someone I care about said something. But I can't really go around wishing people would do what I want if I won't do it, so here I am.

So, I needed to find a cheap way to host a web site. Ideally it would have its own domain, so people could go right too it, and I didn't want it to be plastered with advertising. You would think that it would be easy to do that. If I wanted to host a web site from my house, I could do it on a $5 Raspberry Pi for maybe $1 per month in electricity. I assume my web site which will get maybe 100 hits per month costs literal pennies to host at scale. But the best I could do was like $4/month on WordPress.

I started looking into self-hosting. If I knew what I was doing, I'm pretty sure I could host a static site on AWS or Azure for pennies, but I was scared that I would make some config error and be hit with a big bill some month. I finally came across GitHub Pages somehow, and realized that I could just host a static site for free. I assume that all other software is slightly more expensive than it would otherwise be in order to make this amazing service available to me at no cost, but it feels better than trading away my attention to whatever Mark Zuckerberg is calling his company these days. Anyway, I started with a static site which looked like utter garbage because it was raw HTML. I loved it. But then I was reading the docs and came across Jekyll. Turns out you just have to throw a couple of text files into a GitHub repository and check some boxes and GitHub will render a folder full of text files into a kind-of-generic but decent-looking web site. So here I am.

If you too want to make a web site like this, this isn't a tutorial, but here's basically the steps I followed:
* Make a github account named whatever you want your site to be called
* Make a repository named [USERNAME].github.io (which will be the url of your web site if you don't purchase a domain)
* Make a _config.yml file. I copied one from Jekyll docs or something idk. It defines defaults like how to display your posts.
* Make an index.md file to modify the stuff that appears above your list of posts on your home page
* Make a navigation.yml file in a folder called _data if you want to have a custom home bar
* Make a folder called _posts full of text files named [YEAR]-[MONTH]-[DAY]-[TITLE].md. They will become pages linked from your home page.
* Go into your account settings and turn on Pages

It makes its own RSS feed for me! This is great!
