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

Some simulated data, borrowed from [this post](http://jacobsimmering.com/2014/01/10/InstrumentalVariables.html).


{% highlight r %}
library(MASS)
# same random numbers
set.seed(123)

# the means and errors for the multivariate distribution
MUs    <- c(10,15)
SIGMAs <- matrix(c(1,   0.5,
                   0.5, 2   ),
                 nrow=2,
                 ncol=2       )

# the multivariate distribution
mdist <- mvrnorm(n     = 1000,
                 mu    = MUs,
                 Sigma = SIGMAs)

# create unobserved covariate
c <- mdist[ , 2]

# create the instrumental variable
z <- rnorm(1000)

# create observed variable
x <- mdist[ , 1] + z

# constuct the dependent variable
y <- 1 + x + c + rnorm(1000, 0, 0.5)
{% endhighlight %}

Check if the variables behave as expected


{% highlight r %}
cor(x, c)
{% endhighlight %}



{% highlight text %}
## [1] 0.1986307
{% endhighlight %}



{% highlight r %}
cor(z, c)
{% endhighlight %}



{% highlight text %}
## [1] -0.0120011
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
##      0.9079       1.0156       0.9955
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
##      13.787        1.226
{% endhighlight %}

Now using instrumental variables.


{% highlight r %}
library(AER)
{% endhighlight %}



{% highlight text %}
## Error in library(AER): there is no package called 'AER'
{% endhighlight %}



{% highlight r %}
ivreg(y ~ x | z)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): could not find function "ivreg"
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
##      15.949        1.008
{% endhighlight %}

Now using a neural network


{% highlight r %}
library(nnet)

# first stage
nns1 <- nnet(x ~ z, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## # weights:  2
## initial  value 98765.653982 
## final  value 924.804075 
## converged
{% endhighlight %}



{% highlight r %}
# manually obtain fitted values
nnXhat <- nns1$fitted.values

# estimate second stage using Xhat
nns2 <- nnet(y ~ nnXhat, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## # weights:  2
## initial  value 874286.766246 
## final  value 4019.409973 
## converged
{% endhighlight %}



{% highlight r %}
summary(nns2)
{% endhighlight %}



{% highlight text %}
## a 1-0-1 network with 2 weights
## options were - skip-layer connections  linear output units 
##  b->o i1->o 
## 15.95  1.01
{% endhighlight %}

Compare output.


{% highlight r %}
lms2$coefficients - nns2$wts
{% endhighlight %}



{% highlight text %}
## (Intercept)      lmXhat 
##  1.0366e-06 -1.1273e-07
{% endhighlight %}

Compare estimates.


{% highlight r %}
library(ggplot2)
qplot(lms2$fitted.values - nns2$fitted.values)
{% endhighlight %}

![plot of chunk qplot](/images/source/2015-10-21-neural-network-iv-simulated/qplot-1.png) 
