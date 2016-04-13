---
title: "Linear Model and Neural Network"
output: pdf_document
permalink: linear-model-neural-network
tags: [R, linear model, neural network, machine learning]
---

In this short post I want to quickly demonstrate how the most basic neural network (no hidden layer) gives us the same results as the linear model.

First we need data


{% highlight r %}
data(swiss)
str(swiss)
{% endhighlight %}



{% highlight text %}
## 'data.frame':	47 obs. of  6 variables:
##  $ Fertility       : num  80.2 83.1 92.5 85.8 76.9 76.1 83.8 92.4 82.4 82.9 ...
##  $ Agriculture     : num  17 45.1 39.7 36.5 43.5 35.3 70.2 67.8 53.3 45.2 ...
##  $ Examination     : int  15 6 5 12 17 9 16 14 12 16 ...
##  $ Education       : int  12 9 5 7 15 7 7 8 7 13 ...
##  $ Catholic        : num  9.96 84.84 93.4 33.77 5.16 ...
##  $ Infant.Mortality: num  22.2 22.2 20.2 20.3 20.6 26.6 23.6 24.9 21 24.4 ...
{% endhighlight %}

We can now specify a model.


{% highlight r %}
m1 <- formula(Fertility ~ Agriculture + Examination + Education + Catholic + Infant.Mortality)
{% endhighlight %}

Let's start by estimating a linear model.


{% highlight r %}
lm1 <- lm(formula=m1, data=swiss)
summary(lm1)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = m1, data = swiss)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.2743  -5.2617   0.5032   4.1198  15.3213 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      66.91518   10.70604   6.250 1.91e-07 ***
## Agriculture      -0.17211    0.07030  -2.448  0.01873 *  
## Examination      -0.25801    0.25388  -1.016  0.31546    
## Education        -0.87094    0.18303  -4.758 2.43e-05 ***
## Catholic          0.10412    0.03526   2.953  0.00519 ** 
## Infant.Mortality  1.07705    0.38172   2.822  0.00734 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.165 on 41 degrees of freedom
## Multiple R-squared:  0.7067,	Adjusted R-squared:  0.671 
## F-statistic: 19.76 on 5 and 41 DF,  p-value: 5.594e-10
{% endhighlight %}

Now lets use the built in `nnet` package to estimate a neural network without a hidden layer.


{% highlight r %}
library(nnet)
nn1 <- nnet(formula=m1, data=swiss, size=0, skip=TRUE, linout=TRUE)
{% endhighlight %}



{% highlight text %}
## # weights:  6
## initial  value 151023.931697 
## iter  10 value 2105.042930
## iter  10 value 2105.042930
## iter  10 value 2105.042930
## final  value 2105.042930 
## converged
{% endhighlight %}



{% highlight r %}
summary(nn1)
{% endhighlight %}



{% highlight text %}
## a 5-0-1 network with 6 weights
## options were - skip-layer connections  linear output units 
##  b->o i1->o i2->o i3->o i4->o i5->o 
## 66.92 -0.17 -0.26 -0.87  0.10  1.08
{% endhighlight %}

Let look at the results next to each other.


{% highlight r %}
lm1
summary(nn1)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = m1, data = swiss)
## 
## Coefficients:
##      (Intercept)       Agriculture       Examination  
##          66.9152           -0.1721           -0.2580  
##        Education          Catholic  Infant.Mortality  
##          -0.8709            0.1041            1.0770  
## 
## a 5-0-1 network with 6 weights
## options were - skip-layer connections  linear output units 
##  b->o i1->o i2->o i3->o i4->o i5->o 
## 66.92 -0.17 -0.26 -0.87  0.10  1.08
{% endhighlight %}

We can also estimate this model using the `neuralnet` package (this package needs to be installed first using `install.packages("neuralnet")`). This package allows us to nicely visualise the results.


{% highlight r %}
library(neuralnet)
{% endhighlight %}



{% highlight text %}
## Loading required package: grid
{% endhighlight %}



{% highlight text %}
## Loading required package: MASS
{% endhighlight %}



{% highlight r %}
nn1a <- neuralnet(formula=m1, data=swiss, hidden=0, linear.output=TRUE)
plot(nn1a, rep='best')
{% endhighlight %}

![plot of chunk neuralnet](/images/source/2015-08-28-linear-model-neural-network/neuralnet-1.png)

Of course it only becomes interesting if we include a hidden layer.


{% highlight r %}
nn2a <- neuralnet(formula=m1, data=swiss, hidden=2)
plot(nn2a, rep='best')
{% endhighlight %}

![plot of chunk hidden](/images/source/2015-08-28-linear-model-neural-network/hidden-1.png)
