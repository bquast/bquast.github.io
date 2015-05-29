---
layout: post
title: gvc package on CRAN
permalink: diagonals
---

A new R package **diagonals** is now available on CRAN. The package implements several tools for dealing with **fat diagonals** on matrices.

- `block_matrix()`
- `minu_block_matrix()`
- `rectangle_matrix()`
- `minus_rectangle_matrix()`

The **diagonals** package can now be install directly from **R** using:

{% highlight r linenos %}
install.packages("diagonals")
{% endhighlight %}

In addition to this, a **development version** is available on GitHub, this version is to be used **at your own risk**, it can be install using:

{% highlight r linenos %}
if (!require('devtools')) install.packages('devtools')
devtools::install_github("bquast/diagonals")
{% endhighlight %}
