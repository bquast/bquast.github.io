---
layout: single
title: A jekyll blog
permalink: jekyll-blog
---
What are **jekyll**, **markdown**, and **git(hub)**?
and why would you need all of this for a blog, in stead of a simple Blogspot of Wordpress page?
The short answer is **more control**,
by having fewer and more transparent layers, you retain more control over the content and layout of your blog.
Since launching this blog last week,
I have received a number of question about how to set up something similar.
Below I briefly describe the main steps for setting up a jekyll blog,
and tomorrow I will go into the details of how I customised this one.

I will start by reviewing the tools needed, if you are already familiar with these tools, then you can skip this part.

First of all, [jekyll](http://jekyllrb.com/) (as in [The Strange Case of Dr. Jekyll and Mr. Hyde](http://en.wikipedia.org/wiki/Strange_Case_of_Dr_Jekyll_and_Mr_Hyde))
is a markdown rendering engine which describes itself as a **blog-aware, static site generator**.
That is to say, jekyll is built with blogging in mind, but does not focus on blogging exclusively.
For example, my [decompr mini page](http://qua.st/decompr) is a separate jekyll site, which does not have a blogging element.
Jekyll is written by one of the [Github](https://github.com/) co-founder Tom Preston-Werner, and works very well with Github.
In fact this site [is hosted at github](https://github.com/bquast/bquast.github.io/), and uses a [CNAME record](http://en.wikipedia.org/wiki/CNAME_record) to display the qua.st domain.
Github automatically renders certain repositories and branches as jekyll pages, unless indicated otherwise.
If you create a repository called **username**.github.**io** (e.g. [bquast.github.io](https://github.com/bquast/bquast.github.io)), then this repo will be considered a markdown site,
and will be made publicly available at **username**.github.**io**.

So Jekyll renders markdown pages as HTML websites, but what is markdown exactly?
Markdown is a plain-text formatting syntax, which is easy to read, even when not formatted.
If you have ever edited a wikipedia page  than you have probably used markdown to do so.
The advantage of writing in markdown is that it is very easy to read when writing the 'code',
and at the same time, it allows you to use the most common formatting elements, such as:
**bold**, [links](http://en.wikipedia.org/wiki/Hyperlinks), and most importantly for this blog:

{% highlight r linenos %}
# code blocks
with(highlighting_in_eg_R)
{% endhighlight %}

Lastly, [Git](http://git-scm.com/) and [Github](https://github.com/).
Git is a version control system, originally designed for software development,
but other used in other fields (such as academia).
It helps you organise different iterations of code files (such as your markdown blog posts).
Git also allows you to store a copy of (all versions) of your files on a **remote** server.
Git**hub** is a service which allows you to store your files on their servers.
In the context of jekyll sites, git is mostly used as a easy method to upload and update your posts and pages.
The functionality whereby Github hosts your repository as a website is called [Github Pages](https://pages.github.com/).

## Setting up jekyll
As I mentioned above, this site is hosted on Github, which automatically runs jekyll on the repository
**username**.github.**io**. The first step is thus to create this repository.
Do not add anything to the repository, we will get to this soon enough.
Now **clone** this github repository to your local computer.

The second step is now to add all the files that jekyll expect there to be. The easiest way to do this, is to use a pre-designed [theme](http://jekyllthemes.org/) (I used [lanyon](https://github.com/poole/lanyon), which is a varation of [poole](https://github.com/poole/poole)). Copy all of the file from the theme to your local repository and push then to the remote (`origin/master`).

If you now access the domain **username**.github.**io**, you will find your website there.

At this point you need to start configuring your website. Basic global options can be configured in the `_config.yml` file. Example posts can be found in the `_post` folder, and advanced layout and colour options can be edited by editing the `.css` files in the `css` folder.
