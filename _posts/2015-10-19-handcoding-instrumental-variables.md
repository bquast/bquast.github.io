---
layout: post
title: "Hand Coding Instumental Vairables"
tags: [R, hand coding, linear model, lm, iv, instumental variables, 2sls, tsls]
permalink: handcoding-instrumental-variables
---

In a previous post we discussed the [linear model](/handcoding-lm)
and how to write a [function that performs a linear regression](/handcoding-lm-function).
In this post we will use that linear model function to perform a [Two-Stage Least Squares estimation].
This estimation allows us to [...]

[Recall](/handcoding-lm-function) that we built the follow linear model function.


{% highlight r %}
ols <- function (y, X, intercept=TRUE) {
  if (intercept) X <- cbind(X, 1)
  solve(t(X)%*%X) %*% t(X)%*%y # solve for beta
}
{% endhighlight %}

We used the following data.


{% highlight r %}
data(iris)
x1 <- iris$Petal.Width
x2 <- iris$Sepal.Length
y  <- iris$Petal.Length
{% endhighlight %}

This allowed us to estimate a linear model.


{% highlight r %}
X <- cbind(x1, x2)
ols(y = y, X = X)
{% endhighlight %}



{% highlight text %}
##          [,1]
## x1  1.7481029
## x2  0.5422556
##    -1.5071384
{% endhighlight %}

Which includes the intercept, since the default value it `TRUE` (see function definition above),
we could estimate it without an intercept using.


{% highlight r %}
ols(y = y, X = X, intercept = FALSE)
{% endhighlight %}



{% highlight text %}
##         [,1]
## x1 1.9891655
## x2 0.2373159
{% endhighlight %}

Having revisited the above, we can continue with instumental variables.
We will replicate an example from the `AER` (Applied Econometric Regressions) package.


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
data("CigarettesSW")
rprice  <- with(CigarettesSW, price/cpi)
tdiff   <- with(CigarettesSW, (taxs - tax)/cpi)
packs   <- CigarettesSW$packs
{% endhighlight %}

We first need to obtain our first stage estimate (putting the whole function between parentheses allows us to both write it to the object `s1` and print it.


{% highlight r %}
( s1 <- ols(y = rprice, X = tdiff) )
{% endhighlight %}



{% highlight text %}
##        [,1]
## X  4.163002
##   91.103739
{% endhighlight %}

We can now obtain the predicted (fitted) values


{% highlight r %}
Xhat <- s1[1]*tdiff + s1[2]
{% endhighlight %}

Using these fitted values, we can finally estimate our second stage.


{% highlight r %}
ols(y = packs, X = Xhat)
{% endhighlight %}



{% highlight text %}
##         [,1]
## X  -1.019485
##   219.576384
{% endhighlight %}

Now compare this to the results using the `AER` package.


{% highlight r %}
library(AER)
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

When we compare this estimate to the estimate from the linear model, we find that the effect of the price is significantly overestimated there.


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

We can also do this using `R`'s built in `lm` function.


{% highlight r %}
# first stage
s1 <- lm(rprice ~ tdiff)

# estimate second stage using fitted values (Xhat)
lm(packs ~ s1$fitted.values)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = packs ~ s1$fitted.values)
## 
## Coefficients:
##      (Intercept)  s1$fitted.values  
##          219.576            -1.019
{% endhighlight %}

As an intermediate form, we can  manually calculate `Xhat` (`fitted.values`) and estimate using that.


{% highlight r %}
# manually obtain fitted values
Xhat <- s1$coefficients[2]*tdiff + s1$coefficients[1]

# estimate second stage using Xhat
lm(packs ~ Xhat)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = packs ~ Xhat)
## 
## Coefficients:
## (Intercept)         Xhat  
##     219.576       -1.019
{% endhighlight %}

Note that if you estimate a TSLS using the `lm` function,
that you can **only** use the coefficients,
the error terms will be **wrong**. 
