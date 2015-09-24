---
layout: post
title: "Hand Coding the Linear Model"
tags: [R, hand coding, linear model, lm, ols]
permalink: handcoding-lm
---

> In order to understand statistics, you have to do the calculations yourself!

Warnings such as these are often given in statistics courses and for good reason too!
Doing the work yourself really cements the understanding of statistics.

Yet when it comes to econometrics, the main take aways from the workshops are primarily in terms of the **syntax** of yet another computer program.

In this series of post titled: **handcoded**, I show how many workhorses of the econometrician's toolbox can be implemented in a very simple manner.
This manual implementation greatly helps in keeping an understanding of what actually happens when we call e.g. `MCMC..` with a few parameters.


The Linear Model
----------------------
Then using the **Ordinary Least Squares** approach to solving a model, we start with the following equation of the OLS model for a univariate regression.

$$
y_i = \beta_0 + \beta_1 x_1 + \epsilon
$$

This [can be solver for](https://en.wikipedia.org/wiki/Simple_linear_regression#Fitting_the_regression_line)
the following (hat denotes the estimator, bar denotes the mean):

$$
\hat{\beta_1} = \frac{ \sumˆn_{i=1} (x_i - \bar{x} )(y_i - \bar{y} ) }{(x_i - \bar{x})ˆ2 }
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

We can now estimate the slope parameter:


{% highlight r %}
x_m <- mean(x) # x bar in our equation
y_m <- mean(y) # y bar in our equation
numerator   <- sum( (x-x_m) * (y-y_m) )
denominator <- sum( (x-x_m)*(x-x_m) ) 
( beta1_hat   <- numerator / denominator )
{% endhighlight %}



{% highlight text %}
## [1] 2.22994
{% endhighlight %}

Using the slope parameter we can now compute the intercept.


{% highlight r %}
( beta0_hat <- y_m - ( beta1_hat * x_m ) )
{% endhighlight %}



{% highlight text %}
## [1] 1.083558
{% endhighlight %}

Lets check this using the built in command.


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



The matrix model
------------------
In matrix form we can specify our general equation as:

$$
y = \beta X + \epsilon
$$

From which we can derive our estimator:

$$
\beta = (X^T *X)^{-1} * (X^T*y)
$$


The matrix estimation
-----------------------

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

Now we estimate our beta ourselves, the function used to invert is called `solve()`.


{% highlight r %}
solve(t(x)%*%x) %*% t(x)%*%y
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

We also have to discuss the above `solve()` function, `^-1` is not correct syntax for a matrix inversion. In the above case it would still work correctly because our `X` matrix is in fact a vector. If we pre-multiply this vector with the transpose of itself, we obtain a scalar.


{% highlight r %}
dim( t(x) %*% x )
{% endhighlight %}



{% highlight text %}
## [1] 1 1
{% endhighlight %}

However, for matrices wider than one column this is not the case.


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

We therefore use a different tool, the`solve()` function from the `base` package.
This function implements the [QR decomposition](https://en.wikipedia.org/wiki/QR_decomposition),
which is an efficient way of deriving an inverse of a matrix.

Now we can use this matrix to estimate a model with an intercept.


{% highlight r %}
solve( (t(XI)%*%XI) ) %*% t(XI)%*%y
{% endhighlight %}



{% highlight text %}
##       [,1]
## x 2.229940
##   1.083558
{% endhighlight %}

Note that this is programmatically exactly the same the way that the `lm()` function does this.

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

We have now constructed a univariate (uni**variate**) model, however, from a programmatic point of view, the hurdles of multivariate modelling have already been overcome by estimating a model with an intercept (making `X` a matrix).

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
solve( (t(XI)%*%XI) ) %*% t(XI)%*%y
{% endhighlight %}



{% highlight text %}
##          [,1]
## x1  1.7481029
## x2  0.5422556
##    -1.5071384
{% endhighlight %}

And that's all! The leap from univariate to multivariate modelling was truly very small.

**EDIT:** in [tomorrow's post](/handcoding-lm-function) we use the method we developed here to create an easy to use function.
