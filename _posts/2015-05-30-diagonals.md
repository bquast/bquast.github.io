---
layout: post
title: introducing diagonals
tags: [R, diagonals, matrix]
permalink: diagonals
---

A new R package `diagonals` is now available on CRAN. The package implements several tools for dealing with **fat diagonals** on matrices.

Fat diagonal matrices occur when we combine two dimensions of a data set along one edge of a matrix. For example, trade-flow data in the [decompr](http://cran.r-project.org/package=decompr) and [gvc](http://cran.r-project.org/package=gvc) package have each country-industry combination occur on each edge of the matrix. 

A fat diagonal matrix looks like this.


{% highlight r %}
library(diagonals)
fatdiag(12, steps=3)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "fatdiag"
{% endhighlight %}

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
{% endhighlight %}



{% highlight text %}
## Error in fatdiag(m, steps = 3) <- 5: could not find function "fatdiag<-"
{% endhighlight %}



{% highlight r %}
m
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

As can be seen from the above example, the blocks and matrices do not have to be square.

The diagonal of a matrix can also be extracted.


{% highlight r %}
fatdiag(m, steps=3)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "fatdiag"
{% endhighlight %}

We can also specify the size of the block in stead of the number of steps.


{% highlight r %}
fatdiag(12, size=4)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "fatdiag"
{% endhighlight %}

This also gives us flexibility in terms of having non-square blocks (and consequently matrices).


{% highlight r %}
fatdiag(12, size=c(3,4) )
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "fatdiag"
{% endhighlight %}

The [diagonals packge](http://cran.r-project.org/?package=diagonals) is now [available on CRAN](/diagonals-cran) and can therefore be install directly from inside `R` using:


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
browseVignettes(package = "diagonals"")
{% endhighlight %}

An application of `diagonals` in **Social Network Analysis** is demonstrated in last weeks post: [diagonals in networks](/diagonals-network).

For more information on the package and its development please see yesterday's post [diagonals on CRAN](/diagonals-cran).
