---
title: "Neural Network IV with Simulated Data"
output: html_document
permalink: neural-network-iv-simulated
tags:
- R
- linear model
- neural network
- machine learning
layout: post
---

Some simulated data, borrow from [this post](http://jacobsimmering.com/2014/01/10/InstrumentalVariables.html).


{% highlight r %}
library(MASS)
# same random numbers
set.seed(123)
# we are really generating x* and c and using a common variance
xStarAndC <- mvrnorm(1000, c(20, 15), matrix(c(1, 0.5, 0.5, 1), 2, 2))
xStar <- xStarAndC[, 1]
c <- xStarAndC[, 2]
z <- rnorm(1000)
x <- xStar + z
# using 1 makes it easy to estimate how 'wrong' an estimator is and toss
# some noise on y
y <- 1 + x + c + rnorm(1000, 0, 0.5)
{% endhighlight %}

Let's look at the true model.


{% highlight r %}
lm(y ~ x + c)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ x + c)
## 
## Coefficients:
## (Intercept)            x            c  
##      0.8202       1.0165       0.9897
{% endhighlight %}

Estimate using OLS.


{% highlight r %}
lm(y ~ x)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ x)
## 
## Coefficients:
## (Intercept)            x  
##      10.481        1.278
{% endhighlight %}

Now using instrumental variables.


{% highlight r %}
library(AER)
ivreg(y ~ x | z)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## ivreg(formula = y ~ x | z)
## 
## Coefficients:
## (Intercept)            x  
##      15.570        1.023
{% endhighlight %}

Now using the `lm` function.


{% highlight r %}
# first stage
lms1 <- lm(x ~ z)

# manually obtain fitted values
lmXhat <- lms1$coefficients[2]*z + lms1$coefficients[1]

# estimate second stage using Xhat
(lms2 <- lm(y ~ lmXhat) )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ lmXhat)
## 
## Coefficients:
## (Intercept)       lmXhat  
##      15.570        1.023
{% endhighlight %}

Now using a neural network


{% highlight r %}
library(nnet)

# first stage
nns1 <- nnet(x ~ z, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## # weights:  2
## initial  value 401097.863581 
## final  value 915.649356 
## converged
{% endhighlight %}



{% highlight r %}
# manually obtain fitted values
nnXhat <- nns1$fitted.values

# estimate second stage using Xhat
(nns2 <- nnet(y ~ nnXhat, size=0, skip=TRUE, linout=TRUE) )
{% endhighlight %}



{% highlight text %}
## # weights:  2
## initial  value 869603.061622 
## final  value 3188.205431 
## converged
{% endhighlight %}



{% highlight text %}
## a 1-0-1 network with 2 weights
## inputs: nnXhat 
## output(s): y 
## options were - skip-layer connections  linear output units
{% endhighlight %}



{% highlight r %}
summary(nns2)
{% endhighlight %}



{% highlight text %}
## a 1-0-1 network with 2 weights
## options were - skip-layer connections  linear output units 
##  b->o i1->o 
## 15.57  1.02
{% endhighlight %}

Compare output.


{% highlight r %}
lms2$coefficients - nns2$wts
{% endhighlight %}



{% highlight text %}
##   (Intercept)        lmXhat 
##  6.950751e-05 -3.589171e-06
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
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
{% endhighlight %}

![plot of chunk qplot](/images/source/2015-10-21-neural-network-iv-simulated/qplot-1.png) 
