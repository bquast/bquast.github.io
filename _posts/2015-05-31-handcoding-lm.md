---
layout: post
title: "Hand Coding the Linear Model"
tags: [R, hand coding, linear model, lm, ols]
permalink: handcoding-lm
---

> In order to understand statistics, you have to do the calculations yourself!

Warnings such as these are often given in statistics courses and for good reason too!
Doing the work yourself really cements the understanding of statistics.

Yet when it comes to econometrics, the main take-aways from the workshops are primarily in terms of the **syntax** of yet another computer program.

In this series of post titled: **handcoded**, I show how many workhorses of the econometrician's toolbox can be implemented in a very simple manner.
This manual implementation greatly helps in keeping an understanding of what actually happens when we call e.g. `MCMC..` with a few paramters.


The Linear Model
----------------------
Then using the **Ordinary Least Squares** approach to solving a model, we start with the following equation.

$$
y = \beta \mathbf{X} + \epsilon
$$

Which we solve for $$ \beta $$.

$$
\beta = (\mathbf{X}^T\mathbf{X})^{-1} \mathbf{X}^Ty
$$

We start by loading a basic data set.


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

Assign our variables to objects (in the global environment)


{% highlight r %}
y <- iris$Petal.Length
x <- iris$Petal.Width
{% endhighlight %}

Use the built in command.


{% highlight r %}
lm( y ~ x -1 ) # the -1 ensures that we don't have an intercept
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ x - 1)
## 
## Coefficients:
##     x  
## 2.875
{% endhighlight %}

Now we estimate our beta ourselves (`solve`! it's coming, read on...).


{% highlight r %}
(t(x)%*%x)^-1 %*% t(x)%*%y
{% endhighlight %}



{% highlight text %}
##          [,1]
## [1,] 2.874706
{% endhighlight %}

Now lets estimate with an intercept


{% highlight r %}
lm( y ~ x )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ x)
## 
## Coefficients:
## (Intercept)            x  
##       1.084        2.230
{% endhighlight %}

To hand code this, we need to add a vector of ones (`1`s).
Note that the single `1` that we are binding to the vector `X` will be repeated until it is the same length.


{% highlight r %}
XI <- cbind(x, 1) # create a new object XI (I for Intercept)
head(XI) # inspect the first few rows
{% endhighlight %}



{% highlight text %}
##        x  
## [1,] 0.2 1
## [2,] 0.2 1
## [3,] 0.2 1
## [4,] 0.2 1
## [5,] 0.2 1
## [6,] 0.4 1
{% endhighlight %}

We also have to correct a small cheat which we used above. `^-1` is not correct syntax for a matrix inversion. In the above case it works correctly because our `X` matrix is in fact a vector. If we pre-multiply this vector with the transpose of itself, we obtain a scalar.


{% highlight r %}
dim( t(x) %*% x )
{% endhighlight %}



{% highlight text %}
## [1] 1 1
{% endhighlight %}

This is the reason that our inversion using `^-1` worked correctly. However, for matrices wider than one column this is not the case.


{% highlight r %}
dim( t(XI) %*% XI )
{% endhighlight %}



{% highlight text %}
## [1] 2 2
{% endhighlight %}

This `^-1` will invert every **individual** number in the matrix, rather than the matrix as a whole.


{% highlight r %}
(t(XI)%*%XI)^-1
{% endhighlight %}



{% highlight text %}
##             x            
## x 0.003307644 0.005558644
##   0.005558644 0.006666667
{% endhighlight %}

We want to obtain to obtain the inverse of the matrix, because this will allow us to pre-multiply on both sides, eliminating `XI` on the **Right-Hand Side** (RHS).

We therefore use a different tool, the Generalised Inverse `ginv()` function from the `MASS` package.

Now we can use this matrix to estimate a model with an intercept.


{% highlight r %}
library(MASS) # we only need this once after starting R
ginv( (t(XI)%*%XI) ) %*% t(XI)%*%y
{% endhighlight %}



{% highlight text %}
##          [,1]
## [1,] 2.229940
## [2,] 1.083558
{% endhighlight %}

Note that this is programmatically exactly the same the way that the `lm()` funcios this.

We can suppress the automatic intercept and include our `XI` variable and we will obtain the same results.


{% highlight r %}
lm(y ~ XI -1 )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ XI - 1)
## 
## Coefficients:
##   XIx     XI  
## 2.230  1.084
{% endhighlight %}

We have now constructed a univariate (uni**variate**) model, however, from a programmic poitn of view, the hurdles of multivariate modelling have already beeno overcome by estimating a model with an intercept (making `X` a matrix).

It is therefore very easy to use the same method in a case with two independent variables.


{% highlight r %}
x1 <- iris$Petal.Width
x2 <- iris$Sepal.Length
y  <- iris$Petal.Length
{% endhighlight %}

we start by binding the two independent variables together (with vector of `1`s, since we want an intercept).


{% highlight r %}
XI <- cbind(x1, x2, 1)
head(XI)
{% endhighlight %}



{% highlight text %}
##       x1  x2  
## [1,] 0.2 5.1 1
## [2,] 0.2 4.9 1
## [3,] 0.2 4.7 1
## [4,] 0.2 4.6 1
## [5,] 0.2 5.0 1
## [6,] 0.4 5.4 1
{% endhighlight %}

Now we estimate our model


{% highlight r %}
# make sure the MASS package is still loaded
ginv( (t(XI)%*%XI) ) %*% t(XI)%*%y
{% endhighlight %}



{% highlight text %}
##            [,1]
## [1,]  1.7481029
## [2,]  0.5422556
## [3,] -1.5071384
{% endhighlight %}

And that's all! The leap from univariate to multivariat modelling was truely very small.

There is one last thing to note here.
Using [QR decompostion](https://en.wikipedia.org/wiki/QR_decomposition),
we can obtain the inverse that we need in a computationally much more efficient way.
The function that we use for this is `solve()`.


{% highlight r %}
system.time(lm( y ~ XI ))
{% endhighlight %}



{% highlight text %}
##    user  system elapsed 
##   0.001   0.000   0.002
{% endhighlight %}



{% highlight r %}
system.time(ginv(t(XI)%*%XI) %*% t(XI)%*%y)
{% endhighlight %}



{% highlight text %}
##    user  system elapsed 
##       0       0       0
{% endhighlight %}

So far we have been calculating the inverse for pre-multiplication. The faster way to do this is using the QR decomposition (`solve()`).


{% highlight r %}
solve(t(XI)%*%XI) %*% t(XI)%*%y
{% endhighlight %}



{% highlight text %}
##          [,1]
## x1  1.7481029
## x2  0.5422556
##    -1.5071384
{% endhighlight %}



{% highlight r %}
system.time( solve(t(XI)%*%XI) %*% t(XI)%*%y )
{% endhighlight %}



{% highlight text %}
##    user  system elapsed 
##   0.000   0.000   0.001
{% endhighlight %}

EDIT: in [tomorrow's post](/handcoded-lm-function) we use the method we developed here to create an easy to use function.
