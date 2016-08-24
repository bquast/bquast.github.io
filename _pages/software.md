---
layout: single
title: Software
permalink: software
image:
  feature: energy.jpg
  credit: Bastiaan Quast
  creditlink: http://qua.st/
---

Below is a list of the R packages that I authored, an overview is available here:

https://cran.r-project.org/web/checks/check_results_bquast_at_gmail.com.html


rnn
--------------
A Recurrent Neural Network in native R, transforms numbers to binaries before adding bit by bit, teaching itself how to carry.


learNN
--------------
Code examples for basic mechanisms of neural networks, such as backpropagation, gradient descent, and dropout.


rddtools
---------
with Mattieu Stigler

Set of functions for Regression Discontinuity Design ('RDD'), for data visualisation, estimation and testing.


diagonals
----------
The [diagonals package](/diagonals) ([CRAN](http://cran.r-project.org/package=diagonals)) implements several tools for dealing with fat diagonals on matrices.
Block-diagonal matrices can be extracted or removed using two small functions implemented here. Furthermore, non-square matrices are supported using two additional functions.
Block-diagonal matrices occur when we combine two dimensions of a data set along one edge of a matrix. For example, trade-flow data in the decompr and gvc package have each `country * industry` combination occur on each edge of the matrix.


decompr
--------
I have developed the R packages [decompr](https://qua.st/decompr) ([CRAN](http://cran.r-project.org/web/packages/decompr/)) which can be used for the **Wang-Wei-Zhu** and **Leontief decomposition** algorithms.


gvc
----
The [gvc package](/gvc) ([CRAN](http://cran.r-project.org/web/packages/gvc/index.html)) can be used for futher analysis of Global Value chains. It implements several tools for this.

- Importing to Export (`i2e()`)
- Exporting to Re-export (`e2r()`)
- New Revealed Comparative Advantage (`nrca()`)


wiod
-----
The [wiod package](/wiod) ([CRAN](http://cran.r-project.org/package=wiod)) contains the World Input-Output Database data in a format that can be used by the decompr and gvc packages.


rstudio-desktop-preview (Arch Linux)
--------------------------------------
Of course I am not the author of [RStudio](http://www.rstudio.com/).
[This package](https://aur.archlinux.org/packages/rstudio-desktop-preview) installs nightly builds from the RStudio S3 server on [Arch Linux](https://www.archlinux.org/).


[more about me](/about)
-----------------------
