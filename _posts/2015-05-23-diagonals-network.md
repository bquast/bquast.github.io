---
layout: post
title: diagonals in a network
permalink: diagonals-network
---

A typical example in which `diagonals` can be helpful is Social Network
Analysis. For example, if we use matrices to represent friendship
perceptions between individuals, then we need a dyadic matrix.


{% highlight r %}
# generate a dyadic matrix for 3 individuals
m <- matrix(sample(0:1, 9, replace=TRUE), nrow=3, ncol=3)
m
{% endhighlight %}



{% highlight text %}
##      [,1] [,2] [,3]
## [1,]    1    0    1
## [2,]    1    1    0
## [3,]    0    0    1
{% endhighlight %}

Let says that we want to look at second-order connections (i.e. friends
of friends). If we now want to represent the data from both time period
in a single object, we need a 4-dimensional array. Higher-order arrays
are hard to visualise, another way of doing this is by representing two
dimensions along each of the two edges of a matrix. We can do this using
the **Knonecker Product** (denoted ⊗), which we can call in `R` using
the alias `%x%`.


{% highlight r %}
M <- m %x% m
M
{% endhighlight %}



{% highlight text %}
##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9]
##  [1,]    1    0    1    0    0    0    1    0    1
##  [2,]    1    1    0    0    0    0    1    1    0
##  [3,]    0    0    1    0    0    0    0    0    1
##  [4,]    1    0    1    1    0    1    0    0    0
##  [5,]    1    1    0    1    1    0    0    0    0
##  [6,]    0    0    1    0    0    1    0    0    0
##  [7,]    0    0    0    0    0    0    1    0    1
##  [8,]    0    0    0    0    0    0    1    1    0
##  [9,]    0    0    0    0    0    0    0    0    1
{% endhighlight %}

Feelings of friendship towards oneself aren't always particularly
insightful. We can now use the `diagonals` library to eliminate those.


{% highlight r %}
# load the library
library(diagonals)

# remove the elements along the diagonal of width 2
minus_block_matrix(M, size=3)
{% endhighlight %}



{% highlight text %}
##       [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9]
##  [1,]    0    0    0    0    0    0    1    0    1
##  [2,]    0    0    0    0    0    0    1    1    0
##  [3,]    0    0    0    0    0    0    0    0    1
##  [4,]    1    0    1    0    0    0    0    0    0
##  [5,]    1    1    0    0    0    0    0    0    0
##  [6,]    0    0    1    0    0    0    0    0    0
##  [7,]    0    0    0    0    0    0    0    0    0
##  [8,]    0    0    0    0    0    0    0    0    0
##  [9,]    0    0    0    0    0    0    0    0    0
{% endhighlight %}

The [diagonals](http://cran.r-project.org/?package=diagonals) package now [available on CRAN](/diagonals-cran) and can therefore be install directly from inside `R` using:


{% highlight r %}
install.packages("diagonals")
{% endhighlight %}
Subsequently the package can be loaded using:


{% highlight r %}
library(diagonals)
{% endhighlight %}

The above demonstration is also available as a vignette that is [included in the package](http://cran.r-project.org/web/packages/diagonals/vignettes/network.html).
It can be accessed from `R` using:


{% highlight r %}
vignette("network")
{% endhighlight %}

A general introduction to `diagonals` is available in next weeks post: [diagonals](/diagonals). This post is also available as a vignette that is [included in the package](http://cran.r-project.org/web/packages/diagonals/vignettes/diagonals.html)

For more information on the package and its development please see yesterday's post [diagonals on CRAN](/diagonals-cran).
