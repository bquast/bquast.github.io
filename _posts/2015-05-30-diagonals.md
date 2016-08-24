---
layout: single
title: introducing diagonals
tags: [R, diagonals, matrix, package]
permalink: diagonals
---

A new R package `diagonals` is now available on CRAN. The package implements several tools for dealing with **fat diagonals** on matrices, such as this one:


{% highlight text %}
##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
##  [1,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [2,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [3,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [4,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [5,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [6,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [7,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [8,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [9,]    0    0    0    0    0    0    0    0    1     1     1     1
## [10,]    0    0    0    0    0    0    0    0    1     1     1     1
## [11,]    0    0    0    0    0    0    0    0    1     1     1     1
## [12,]    0    0    0    0    0    0    0    0    1     1     1     1
{% endhighlight %}

Fat diagonal matrices occur when we combine two dimensions of a data set along one edge of a matrix. For example, trade-flow data in the [decompr](http://cran.r-project.org/package=decompr) and [gvc](http://cran.r-project.org/package=gvc) package have each country-industry combination occur on each edge of the matrix.

The workhorse function of this package is the `fatdiag` function, which tries behave similarly to the `diag` function from the `base` package, but then with diagonals being **fat**.

We can also use the function to assign values to the diagonal.


{% highlight r %}
( m <- matrix(111, nrow=6, ncol=9) )
{% endhighlight %}



{% highlight text %}
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9]
## [1,]  111  111  111  111  111  111  111  111  111
## [2,]  111  111  111  111  111  111  111  111  111
## [3,]  111  111  111  111  111  111  111  111  111
## [4,]  111  111  111  111  111  111  111  111  111
## [5,]  111  111  111  111  111  111  111  111  111
## [6,]  111  111  111  111  111  111  111  111  111
{% endhighlight %}



{% highlight r %}
fatdiag(m, steps=3) <- 5
m
{% endhighlight %}



{% highlight text %}
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9]
## [1,]    5    5    5  111  111  111  111  111  111
## [2,]    5    5    5  111  111  111  111  111  111
## [3,]  111  111  111    5    5    5  111  111  111
## [4,]  111  111  111    5    5    5  111  111  111
## [5,]  111  111  111  111  111  111    5    5    5
## [6,]  111  111  111  111  111  111    5    5    5
{% endhighlight %}

As can be seen from the above example, the blocks and matrices do not have to be square.

The diagonal of a matrix can also be extracted.


{% highlight r %}
fatdiag(m, steps=3)
{% endhighlight %}



{% highlight text %}
##  [1] 5 5 5 5 5 5 5 5 5 5 5 5 5 5 5 5 5 5
{% endhighlight %}

We can also specify the size of the block in stead of the number of steps.


{% highlight r %}
fatdiag(12, size=4)
{% endhighlight %}



{% highlight text %}
##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
##  [1,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [2,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [3,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [4,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [5,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [6,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [7,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [8,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [9,]    0    0    0    0    0    0    0    0    1     1     1     1
## [10,]    0    0    0    0    0    0    0    0    1     1     1     1
## [11,]    0    0    0    0    0    0    0    0    1     1     1     1
## [12,]    0    0    0    0    0    0    0    0    1     1     1     1
{% endhighlight %}

This also gives us flexibility in terms of having non-square blocks (and consequently matrices).


{% highlight r %}
fatdiag(12, size=c(3,4) )
{% endhighlight %}



{% highlight text %}
##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
##  [1,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [2,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [3,]    1    1    1    1    0    0    0    0    0     0     0     0
##  [4,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [5,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [6,]    0    0    0    0    1    1    1    1    0     0     0     0
##  [7,]    0    0    0    0    0    0    0    0    1     1     1     1
##  [8,]    0    0    0    0    0    0    0    0    1     1     1     1
##  [9,]    0    0    0    0    0    0    0    0    1     1     1     1
{% endhighlight %}

Installation
--------------
The [diagonals packge](http://cran.r-project.org/?package=diagonals) is now [available on CRAN](/diagonals-cran) and can therefore be installed directly from inside `R` using:


{% highlight r %}
install.packages("diagonals")
{% endhighlight %}

Subsequently the package can be loaded using:


{% highlight r %}
library(diagonals)
{% endhighlight %}

The above introduction is also available as a vignette that is [included in the package](http://cran.r-project.org/web/packages/diagonals/vignettes/fatdiag.html).
It can be accessed from `R` using:


{% highlight r %}
vignette("fatdiag")

# or
browseVignettes(package = "diagonals")
{% endhighlight %}


Development
-------------
[![CRAN Version](http://www.r-pkg.org/badges/version/diagonals)](http://cran.r-project.org/?package=diagonals)
[![Travis-CI Build Status](https://travis-ci.org/bquast/diagonals.svg?branch=master)](https://travis-ci.org/bquast/diagonals)
[![Coverage Status](https://img.shields.io/coveralls/bquast/diagonals.svg)](https://coveralls.io/r/bquast/diagonals?branch=master)

The development version, to be used **at your peril**, can be installed using:


{% highlight r %}
if (!require('devtools')) install.packages('devtools')
devtools::install_github("bquast/decompr")
{% endhighlight %}

Development takes place on the [GitHub page](http://github.com/bquast/diagonals),
bugs can be filed on the [issues page](https://github.com/bquast/diagonals/issues).
