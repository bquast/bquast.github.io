---
title: "Neural Network Instrumental Variables"
output: pdf_document
permalink: neural-network-instrumental-variables
tags:
- R
- linear model
- neural network
- machine learning
layout: post
---

A simple example


{% highlight r %}
library(AER)
{% endhighlight %}



{% highlight text %}
## Loading required package: car
## Loading required package: lmtest
## Loading required package: zoo
## 
## Attaching package: 'zoo'
## 
## The following objects are masked from 'package:base':
## 
##     as.Date, as.Date.numeric
## 
## Loading required package: sandwich
## Loading required package: survival
{% endhighlight %}



{% highlight r %}
rprice  <- with(CigarettesSW, price/cpi)
{% endhighlight %}



{% highlight text %}
## Error in with(CigarettesSW, price/cpi): object 'CigarettesSW' not found
{% endhighlight %}



{% highlight r %}
tdiff   <- with(CigarettesSW, (taxs - tax)/cpi)
{% endhighlight %}



{% highlight text %}
## Error in with(CigarettesSW, (taxs - tax)/cpi): object 'CigarettesSW' not found
{% endhighlight %}



{% highlight r %}
packs   <- CigarettesSW$packs
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'CigarettesSW' not found
{% endhighlight %}

Estimate using OLS.


{% highlight r %}
lm(packs ~ rprice)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'packs' not found
{% endhighlight %}

Now using instrumental variables.


{% highlight r %}
ivreg(packs ~ rprice | tdiff)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'packs' not found
{% endhighlight %}

Now using the `lm` function.


{% highlight r %}
# first stage
lms1 <- lm(rprice ~ tdiff)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'rprice' not found
{% endhighlight %}



{% highlight r %}
# manually obtain fitted values
lmXhat <- lms1$coefficients[2]*tdiff + lms1$coefficients[1]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'lms1' not found
{% endhighlight %}



{% highlight r %}
# estimate second stage using Xhat
(lms2 <- lm(packs ~ lmXhat) )
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'packs' not found
{% endhighlight %}

Now using a neural network


{% highlight r %}
library(nnet)

# first stage
nns1 <- nnet(rprice ~ tdiff, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'rprice' not found
{% endhighlight %}



{% highlight r %}
# manually obtain fitted values
nnXhat <- nns1$fitted.values
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'nns1' not found
{% endhighlight %}



{% highlight r %}
# estimate second stage using Xhat
(nns2 <- nnet(packs ~ nnXhat, size=0, skip=TRUE, linout=TRUE) )
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'packs' not found
{% endhighlight %}

Compare output.


{% highlight r %}
lms2$coefficients - nns2$wts
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'lms2' not found
{% endhighlight %}

Compare estimates.


{% highlight r %}
library(ggplot2)
{% endhighlight %}



{% highlight text %}
## Loading required package: methods
{% endhighlight %}



{% highlight r %}
qplot(lms2$fitted.values - nns2$fitted.values)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): object 'lms2' not found
{% endhighlight %}


