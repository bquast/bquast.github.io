---
title: "Neural Network Instrumental Variables"
output: pdf_document
permalink: neural-network-instrumental-variables
tags: [R, linear model, neural network, machine learning]
published: false
---

A simple example


{% highlight r %}
library(AER)
data("CigarettesSW")
rprice  <- with(CigarettesSW, price/cpi)
tdiff   <- with(CigarettesSW, (taxs - tax)/cpi)
packs   <- CigarettesSW$packs
{% endhighlight %}

Estimate using OLS.


{% highlight r %}
lm(packs ~ rprice)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = packs ~ rprice)
## 
## Coefficients:
## (Intercept)       rprice  
##     222.209       -1.044
{% endhighlight %}

Now using instrumental variables.


{% highlight r %}
ivreg(packs ~ rprice | tdiff)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## ivreg(formula = packs ~ rprice | tdiff)
## 
## Coefficients:
## (Intercept)       rprice  
##     219.576       -1.019
{% endhighlight %}

Now using the `lm` function.


{% highlight r %}
# first stage
lms1 <- lm(rprice ~ tdiff)

# manually obtain fitted values
lmXhat <- lms1$coefficients[2]*tdiff + lms1$coefficients[1]

# estimate second stage using Xhat
(lms2 <- lm(packs ~ lmXhat) )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = packs ~ lmXhat)
## 
## Coefficients:
## (Intercept)       lmXhat  
##     219.576       -1.019
{% endhighlight %}

Now using a neural network


{% highlight r %}
library(nnet)
set.seed(123)

# first stage
nns1 <- nnet(rprice ~ tdiff, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## # weights:  2
## initial  value 1123401.708750 
## final  value 14467.562948 
## converged
{% endhighlight %}



{% highlight r %}
# manually obtain fitted values
nnXhat <- nns1$fitted.values

# estimate second stage using Xhat
nns2 <- nnet(packs ~ nnXhat, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## # weights:  2
## initial  value 335265.176965 
## final  value 48851.806790 
## converged
{% endhighlight %}



{% highlight r %}
summary(nns2)
{% endhighlight %}



{% highlight text %}
## a 1-0-1 network with 2 weights
## options were - skip-layer connections  linear output units 
##   b->o  i1->o 
## 219.58  -1.02
{% endhighlight %}

Compare output.


{% highlight r %}
lms2$coefficients - nns2$wts
{% endhighlight %}



{% highlight text %}
##   (Intercept)        lmXhat 
##  4.880515e-05 -4.206591e-07
{% endhighlight %}

Compare estimates.


{% highlight r %}
library(ggplot2)
qplot(lms2$fitted.values - nns2$fitted.values)
{% endhighlight %}

![plot of chunk qplot](/images/source/2015-10-20-neural-network-instrumental-variables/qplot-1.png) 
