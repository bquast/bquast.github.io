---
layout: post
title: Learning R and Git
permalink: learning-R-git
---

In [yesterday's post](/replicable-development-economics) I discussed some of the principles I use to make my work replicable and - to an extent - reproducible.
In this post I want to collect some resources which I think will be very helpful in getting a grasp of the basics of R and Git.


R
----------------------
An easy way to start with R without having to install it is using the online tutorial
[Try R by Code School](http://tryr.codeschool.com/).

After you have completed this tutorial, have a look at the [Datacamp R tutorial](https://www.datacamp.com/courses/introduction-to-r).
There is also a [companion course](https://www.datacamp.com/courses/data-analysis-and-statistical-inference_mine-cetinkaya-rundel-by-datacamp) to the [Coursera](https://www.coursera.org/) [Data Science](https://www.coursera.org/specialization/jhudatascience/1) specialisation (more on this later).

For those of you have previously done quantitative work in excel, then have a look at [How to Transition from Excel to R](https://districtdatalabs.silvrback.com/intro-to-r-for-microsoft-excel-users).



moRe
----------------------
If want try an learn something about R on the go, you might want to have a look at [R instructor](https://play.google.com/store/apps/details?id=appinventor.ai_RInstructor.R2) (Android).

If you are on iOS, there is
[an implementation of the R programming language](https://itunes.apple.com/us/app/r-programming-language/id540809637),
which will give you the ability to run R on your iOS device.

If you have internet access but do not have R installed, then you might want to try [R fiddle](http://www.r-fiddle.org/),
which gives you an online (mobile friendly) environment to execute R code.

If you want to start using R for serious work but don't know how to get started,
consider using a **G**raphical **U**ser **I**nterface (GUI) for R.
The advantage of this is that you can click through the dialogues, which is an easy process.
The program then simply outputs both the results, as well as the underlying code used to produce these result.
Reading the code produced by the GUI is a very easy way to learn new methods.

A good GUI for constructing plots and graphics is [GrapheR](http://cran.r-project.org/web/packages/GrapheR/vignettes/manual_en.pdf).

A more general purpose GUI (as in, it also does modelling) is [Rcmdr](http://socserv.mcmaster.ca/jfox/Misc/Rcmdr/).


Git
---------------------------------
An easy way to start with Git without having to install it using the online tutorial
[Try Git by Code School](https://try.github.io/) (notice that this site is hosted on Github pages).

After having completed this tutorial, you might want to have a look at this cool [Git Branching Game](http://pcottle.github.io/learnGitBranching/),
which will really cement your understanding what control is and how to use Git to manage it.


moRe still
----------------------------------------
On a day to day basis, you will not need more than a few git commands.
Once you have mastered the basics,
there is not that much more to learn which will be useful for you analysis.

With R however, it is a different story altogether, R is both much more expansive,
as well as constantly evolving.
For this reason I am adding a few more resources which might come in handy for acquiring a deeper understanding of R.

Famous mathamatician [John D. Cook](http://www.johndcook.com/) also has a guide called
[R programming for those coming from other languages](http://www.johndcook.com/R_language_for_programmers.html).

Once you have become quite familiar with R and want to really learn some more fundamental topics,
then have a look at [Hadley Wickam](http://had.co.nz/)'s [Advanced R](http://adv-r.had.co.nz/).


RStudio
-----------------------------
I am mentioning this R tool last, but it is by far the most important R tool.
[RStudio](http://www.rstudio.com/) is an **I**ntegrated **D**evelopment **E**nvironment (IDE),
which is used by almost all professional R users.
The developers at RStudio are also very much at the forefront of developing new R packages, such knitr, rmarkdown, shiny, etc.


Data Science specialisation
-----------------------------
As mentioned above the online learning platform [Coursera](https://www.coursera.org/) has a collection of courses called the [Data Science Specialisation](https://www.coursera.org/specialization/jhudatascience/1).

I recently finished the specialisation, which consists of nine courses and a capstone project (not available yet).

The courses are taught by the Johns Hopkins School of Public Health Biostatistics department and have a heavy relience on R(Studio).
Thematically, the courses focus on public health case studies, but the lessons translate well to other fields.
