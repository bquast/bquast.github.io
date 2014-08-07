---
layout: post
title: How to customise a jekyll blog?
permalink: customise-jekyll-blog
---

In [yesterday's post](/jeyll-blog) I described how to setup I set up a basic jekyll blog [using Github Pages](https://pages.github.com/).
In this post I will describe in more detail how I set up this blog,
with the customisations that I added to it.
Most of the idea I used [Joshua Lande's blog](http://joshualande.com/), which he describes in [this post](http://joshualande.com/jekyll-github-pages-poole/).


## Theme

Johsua Lande uses the [poole theme](https://github.com/poole/poole), of which I use the [lanyon variation](https://github.com/poole/lanyon). These are both responsive theme's which means that they work will with different screen sizes, mobile devices, etc.


## Custom domain
If you intend to link a custom domain to your site then this should be your very first step. In my case I registered the `http://qua.st/`. I registerd this because it is nice and short, and the domain TLD `.st` relatively well know, which means people won't forget or remember wrongly to quickly.

The most important thing is obviously to registerd your custom domain with a domain registar, this will depend on things such as your choise of TLD, the country you live in etc.

Once you have done this, the next step is to setup an `ALIAS`, `ANAME`, or `A` file at your registar, GitHub [describes how to do this](https://help.github.com/articles/tips-for-configuring-an-a-record-with-your-dns-provider).

After this has been configured, you need to create a [CNAME record]() in your repository, GitHub can also [help you with this]((https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages).

At this point, both the registar as well as GitHub pages are aware of your custom URL, the last them is to make jekyll aware of this, which will do in the global options.


## Global options

As I mentioned in [yesterday's post](/jeyll-blog), general setting can be set in the `_config.yml` file.
The contents of the file are dependent on the theme default, but they will generally look very similar. In my case the file looks like this.

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



Archive

```
---
layout: page
title: Archive
---

## Blog Posts

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}
```
