---
layout: post
title: "Handcoded Linear Model"
tags: [R, linear model, lm, ols]
---

It can be very insightful to hand code a linear model.

Load a basic data set


{% highlight r %}
data("iris")
{% endhighlight %}

Inspect the data set.


{% highlight r %}
summary(iris)
{% endhighlight %}



{% highlight text %}
##   Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
##  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
##  1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300  
##  Median :5.800   Median :3.000   Median :4.350   Median :1.300  
##  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
##  3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800  
##  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
##        Species  
##  setosa    :50  
##  versicolor:50  
##  virginica :50  
##                 
##                 
## 
{% endhighlight %}

Asign our variables to object (in the global environment)


{% highlight r %}
Y <- iris$Petal.Length
X <- iris$Petal.Width
{% endhighlight %}

Use the built in command.


{% highlight r %}
lm( Y ~ X - 1 )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = Y ~ X - 1)
## 
## Coefficients:
##     X  
## 2.875
{% endhighlight %}

Now we estimate our beta ourselves.


{% highlight r %}
(t(X)%*%X)^-1 %*% t(X)%*%Y
{% endhighlight %}



{% highlight text %}
##          [,1]
## [1,] 2.874706
{% endhighlight %}

Now lets estimate with an intercept


{% highlight r %}
lm( Y ~ X )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = Y ~ X)
## 
## Coefficients:
## (Intercept)            X  
##       1.084        2.230
{% endhighlight %}

To hand code this, we need to add a vector of ones (1s)


{% highlight r %}
X1 <- cbind(X, 1)
head(X1)
{% endhighlight %}



{% highlight text %}
##        X  
## [1,] 0.2 1
## [2,] 0.2 1
## [3,] 0.2 1
## [4,] 0.2 1
## [5,] 0.2 1
## [6,] 0.4 1
{% endhighlight %}

We also have to correct a small cheat which we used above. `^-1` is not correct syntax for a matrix inversion. In the above case it works correctly because our `X` matrix is in fact a vector. If we pre-multiply this vector with the transpose of itself, we obtain a scalar.


{% highlight r %}
dim( t(X) %*% X )
{% endhighlight %}



{% highlight text %}
## [1] 1 1
{% endhighlight %}

This is the reason that our inversion using `^-1` worked correctly. However, for matrices wider that one this is not the case.


{% highlight r %}
dim( t(X1) %*% X1 )
{% endhighlight %}



{% highlight text %}
## [1] 2 2
{% endhighlight %}



{% highlight r %}
(t(X1)%*%X1)^-1
{% endhighlight %}



{% highlight text %}
##             X            
## X 0.003307644 0.005558644
##   0.005558644 0.006666667
{% endhighlight %}



We therefore use a different tool, the Generalised Inverse `ginv()` from the `MASS` package.

Now we can use this matrix to estimate a model with a 


{% highlight r %}
library(MASS)
ginv( (t(X1)%*%X1) ) %*% t(X1)%*%Y
{% endhighlight %}



{% highlight text %}
##          [,1]
## [1,] 2.229940
## [2,] 1.083558
{% endhighlight %}

Now lets look at which method is faster.


{% highlight r %}
system.time(lm( Y ~ X ))
{% endhighlight %}



{% highlight text %}
##    user  system elapsed 
##   0.001   0.000   0.001
{% endhighlight %}



{% highlight r %}
system.time(ginv(t(X1)%*%X1) %*% t(X1)%*%Y)
{% endhighlight %}



{% highlight text %}
##    user  system elapsed 
##       0       0       0
{% endhighlight %}

The faster way to do this (using the QR decomposition) is:


{% highlight r %}
solve(t(X1)%*%X1) %*% t(X1)%*%Y
{% endhighlight %}



{% highlight text %}
##       [,1]
## X 2.229940
##   1.083558
{% endhighlight %}



{% highlight r %}
system.time( solve(t(X)%*%X) %*% t(X)%*%Y )
{% endhighlight %}



{% highlight text %}
##    user  system elapsed 
##       0       0       0
{% endhighlight %}
