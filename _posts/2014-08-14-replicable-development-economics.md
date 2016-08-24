---
layout: single
title: Replicable Development Economics
permalink: replicable-development-economics
---

The tagline of this blog says something **replicable development economics using R and git**.
So far, I have posted gimmicks on new R tools such as shiny, rmarkdown, and my own package.
Also, I have posted on how to use Git, Github, and Jekyll to write a website/blog.
However, I have never brought the two together and how this features into creating replicable research.

In this post I will briefly describe what Git and R are, and how I use them for my work. I hope to post something tomorrow about useful resources for mastering both these tools ([tomorrow's post](/learning-R-git)).

First and foremost, R. Like so many statistical / computational tools, R is essentially two things. Firstly, it is a computer program which executes computations. Secondly, and perhaps more relevant for day to day use, R is a language for programming in this program. In effect, R is a computer program on your computer, but the R language is lines of code which are interpreted by this program.

It is these lines of code which we store in a text file simply known as an R file. The main advantage of having operation recorded as code lines is that is makes **replicability** (finally, I use the word!) more straight forward, compared to e.g. describing how to navigate the dialogues of a **G**raphical **U**ser **I**nterface (GUI).

Secondly, Git. Git is a version control software, it was written by Linus Torvalds, who also wrote the Linux kernel (basis of the operating system). Version control manages changes in plain text files. Here, we are in particularl interested in changes to our R code file.

The idea is that one can always revert to previous versions in case something breaks. Furthermore, the entire research process can be retraced by other researchers trying to **replicate** (again!) your work.

Another very interesting development in the field is using continuous integration. Using this method, your R code will be tested whenever you are editiing it, to see if no errors occur. I have not been able to work out how to do this best, but will try to and then post about it soon.

Another interesting resource which I just stumbled across is [Webcite](http://www.webcitation.org/) (as in **cit**ation).
