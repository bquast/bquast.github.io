---
layout: post
title: How to customise a jekyll blog
permalink: customise-jekyll-blog
---

In [yesterday's post](/jekyll-blog) I described how to setup I set up a basic jekyll blog [using Github Pages](https://pages.github.com/).
In this post I will describe in more detail how I set up this blog,
with the customisations that I added to it.
Most of the ideas I used come from [Joshua Lande's blog](http://joshualande.com/), which he describes in [this post](http://joshualande.com/jekyll-github-pages-poole/).
This post lays out a few very useful customisations, such as,
adding an archive, the disqus platform for comments, as well as a twitter plug.
I followed most of his suggestions, with a few minor changes, which I describe below.


## Theme

Johsua Lande uses the [poole theme](https://github.com/poole/poole), of which I use the [lanyon variation](https://github.com/poole/lanyon). These are both responsive theme's which means that they work will with different screen sizes, mobile devices, etc.


## Custom domain
If you intend to link a custom domain to your site, then this should be your very first step.
In my case I registered the `http://qua.st/`.
I registerd this because it is nice and short, and the domain TLD `.st` relatively well know,
which means people won't forget or remember wrongly too quickly.

The most important thing is obviously to registerd your custom domain with a domain registar,
this will depend on things such as your choice of TLD, the country you live in etc.

Once you have done this, the next step is to setup an `ALIAS`, `ANAME`, or `A` file at your registar,
GitHub [describes how to do this](https://help.github.com/articles/tips-for-configuring-an-a-record-with-your-dns-provider).

After this has been configured, you need to create a [CNAME record](http://en.wikipedia.org/wiki/CNAME_record) in your repository, GitHub can also [help you with this](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages).

At this point, both the registar and GitHub Pages are aware of your custom URL,
the last step is to also make jekyll aware of this, which will do in the global options.


## Global options

As I mentioned in [yesterday's post](/jeyll-blog), general setting can be set in the `_config.yml` file.
The exact contents of this file depend on the theme defaults, but they will generally look very similar.
In my case the file looks like this.

```
# Dependencies
markdown:         redcarpet
highlighter:      pygments

# Permalinks
permalink:        pretty

# Setup
title:            Bastiaan Quast
tagline:          R, Git and replicable Development Economics
description:      'A personal blog about making Development Economics reproducible using R and Git'
url:              http://qua.st/
baseurl:          '/'
paginate:         5

author:
  name:           'Bastiaan Quast'
  url:            https://twitter.com/baquast

# Custom vars
version:          1.0.0

github:
  repo:           https://github.com/bquast/bquast.github.io

exclude:          [rmarkdown]
```
The main things you will want to edit in the `_config` file are:

* title
* tagline
* description
* url
* author
    * name
    * url
* github repo

The last line of the `_config.yml` file instructs jekyll not to look in the `rmarkdown` folder for content.
I use this folder for [rmarkdown](http://rmarkdown.rstudio.com/) files,
which are rendered to markdown and then added to my posts folder.
This enables me to always have access to the source files,
without having jekyll add them to the webpage.

## Social media
Joshua Lande describes how he adds a twitter plug to his page, I used the same method.
In addition, I added a Google+ plug as well. The code for suggesting a Google+ post is:

```
<a href="https://plus.google.com/share?url={{ site.url }}{{ page.url }}" target="_blank">
  Google+
</a>
```
Unfortunately Google+ doesnot allow you to add yourself to you post,
which means that you will not be informed if someone post your article to Google+ unless they manually tag you.

The complete social media code can be seen [here](https://github.com/bquast/bquast.github.io/blob/master/_includes/social_media.html).

In order to automatically add this to every post I added it to the [_layouts/post.html](https://github.com/bquast/bquast.github.io/blob/master/_layouts/post.html) template,
by adding the following code.

```
  {% include social_media.html %}
```

I addition to this, I added the disqus code here also (in stead of to the default page, like Joshua Lande does).

```
  {% include comments.html %}
```

This makes sure that the disqus comment stream is added beneath every post, but not to the main page.

Lastly, I added a couple of links to other profiles on the web such as [ORCID](http://orcid.org/0000-0002-2951-3577), [GitHub](https://github.com/bquast/), [Google+](https://plus.google.com/+BastiaanQuast), and [Twitter](https://twitter.com/baquast) to sidebar. I added these directly into the HTML code, the reason for this, is that I wanted those external pages to open in a new tab/window, which HTML can do using `taget="_blank"`, but markdown does not seem to allow.

The fact that jekyll does not allow this is also my main frustration with it, since I think articles should like to helpful resources as much as possible, however, having the links open in the same tab drives away your visitors. If anybody has an idea of how to fix this, I would be very grateful.


