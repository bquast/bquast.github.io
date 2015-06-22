---
layout: post
title: introducing diagonals
tags: [R, diagonals, matrix, package]
permalink: diagonals
---

A new R package `diagonals` is now available on CRAN. The package implements several tools for dealing with **fat diagonals** on matrices.

Block-diagonal matrices can be extracted or removed using two small
functions implemented here.
Furthermore, non-square matrices are
supported using two additional functions.

Block-diagonal matrices occur when we combine two dimensions of a data
set along one edge of a matrix. For example, trade-flow data in the
[decompr](http://cran.r-project.org/web/packages/decompr/index.html) and
[gvc](http://cran.r-project.org/web/packages/gvc/index.html) package
have each `country * industry` combination occur on each edge of the matrix.


{% highlight r %}
m <- matrix(1:64, nrow=8)
m
{% endhighlight %}



{% highlight text %}
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8]
## [1,]    1    9   17   25   33   41   49   57
## [2,]    2   10   18   26   34   42   50   58
## [3,]    3   11   19   27   35   43   51   59
## [4,]    4   12   20   28   36   44   52   60
## [5,]    5   13   21   29   37   45   53   61
## [6,]    6   14   22   30   38   46   54   62
## [7,]    7   15   23   31   39   47   55   63
## [8,]    8   16   24   32   40   48   56   64
{% endhighlight %}

We can now drop everything except a block diagonal.


{% highlight r %}
library(diagonals)
{% endhighlight %}



{% highlight text %}
## 
## D I
## A G
##     O N
##     A L
##         S
{% endhighlight %}



{% highlight r %}
block_matrix(m, steps=4)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "block_matrix"
{% endhighlight %}

Or drop only the block diagonal.


{% highlight r %}
minus_block_matrix(m, steps=2)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "minus_block_matrix"
{% endhighlight %}

The number of `steps` can vary, but dimensions should be multiples of
`steps`. Alternatively we can specify the size of the `size` of the
step.


{% highlight r %}
block_matrix(m, size=2)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "block_matrix"
{% endhighlight %}

Even non-square matrices are supported, as long as each dimension is a
multiple of size.


{% highlight r %}
# create the matrix
nsm <- matrix(1:27, nrow=9, ncol=3 )

# test if the dimensions are multiples of size
# i.e. there are no remainders after the modulo division of dimensions by steps
dim(nsm) %% 3 # we will use 3 (three) steps
{% endhighlight %}



{% highlight text %}
## [1] 0 0
{% endhighlight %}

Now extract the rectangular diagonal.


{% highlight r %}
rectangle_matrix(nsm, steps=3)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "rectangle_matrix"
{% endhighlight %}

Or this.


{% highlight r %}
rectangle_matrix( t(nsm), steps=3 )
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "rectangle_matrix"
{% endhighlight %}

Lastly, we can also choose a replacement parameter other than zero.


{% highlight r %}
block_matrix(m, steps=4, replacement=-1)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "block_matrix"
{% endhighlight %}

or


{% highlight r %}
minus_rectangle_matrix(nsm, steps=3, replacement=666)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "minus_rectangle_matrix"
{% endhighlight %}

The [diagonals packge](http://cran.r-project.org/?package=diagonals) is now [available on CRAN](/diagonals-cran) and can therefore be install directly from inside `R` using:


{% highlight r %}
install.packages("diagonals")
{% endhighlight %}

Subsequently the package can be loaded using:


{% highlight r %}
library(diagonals)
{% endhighlight %}

The above introduction is also available as a vignette that is [included in the package](http://cran.r-project.org/web/packages/diagonals/vignettes/diagonals.html).
It can be accessed from `R` using:


{% highlight r %}
vignette("diagonals")
{% endhighlight %}

An application of `diagonals` in **Social Network Analysis** is demonstrated in last weeks post: [diagonals in networks](/diagonals-network). This post is also available as a vignette that is [included in the package](http://cran.r-project.org/web/packages/diagonals/vignettes/network.html)

For more information on the package and its development please see yesterday's post [diagonals on CRAN](/diagonals-cran).
