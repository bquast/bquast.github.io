---
layout: post
title: diagonals package on CRAN
permalink: diagonals-cran
---

[![CRAN Version](http://www.r-pkg.org/badges/version/diagonals)](http://cran.r-project.org/?package=diagonals)
[![Travis-CI Build Status](https://travis-ci.org/bquast/diagonals.svg?branch=master)](https://travis-ci.org/bquast/diagonals) 
[![Coverage Status](https://img.shields.io/coveralls/bquast/diagonals.svg)](https://coveralls.io/r/bquast/diagonals?branch=master)

Block-diagonal matrices can be extracted or removed using two small functions implemented here, in addition, non-square matrices are supported.


- `block_matrix()`
- `minus_block_matrix()`
- `rectangle_matrix()`
- `minus_rectangle_matrix()`

Block diagonal matrices occur when we combine two dimensions of a data set along one edge of a matrix. For example, trade-flow data in the [decompr](http://cran.r-project.org/?package=decompr) and [gvc](http://cran.r-project.org/?package=gvc) package have each country-industry combination occur on each edge of the matrix.

The stable version of the `diagonals` package can be install from [CRAN](http://cran.r-project.org/) using:

{% highlight r %}
install.packages("diagonals")
{% endhighlight %}

The development version, to be used **at your peril**, can be installed using:

{% highlight r %}
if (!require('devtools')) install.packages('devtools')
devtools::install_github("bquast/decompr")
{% endhighlight %}

Following installation, the package can be loaded using:

{% highlight r %}
library(diagonals)
{% endhighlight %}

For information on using the package, please refer to the help files    .

{% highlight r %}
help("diagonals")
help(package = "diagonals")
{% endhighlight %}

For examples of usage, see the function specific help pages

{% highlight r %}
help("block_matrix")
help("minus_block_matrix")
help("rectangle_matrix")
help("minus_rectangle_matrix")
{% endhighlight %}

In addition to the help files we provide long form examples in the **vignettes** .

{% highlight r %}
vignette("diagonals")
vignette("network")

# or
browseVignettes("diagonals")
{% endhighlight %}

An overview of the changes is available in the NEWS file.

{% highlight r %}
news(package="diagonals")
{% endhighlight %}

There is also a blog post with information on my [personal website](http://qua.st/).

 - http://qua.st/diagonals/
 

Development
-------------
Development takes place on the GitHub page.

http://github.com/bquast/diagonals

Bugs can be filed on the issues page on GitHub.

https://github.com/bquast/diagonals/issues
