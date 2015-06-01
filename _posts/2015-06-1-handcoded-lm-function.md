---
layout: post
title: "Handcoded LM function"
tags: [R, linear model, lm, ols, function, programming]
---

In [yesterday's post]() we developed a method for constructing a multivariate linear model with an intercept.

Today we will turn the collection of loose commands into an integrated and easy to use `function`.

A small recap from yesterday. We start by loading data and assigning our variables to objects


{% highlight r %}
data(iris)
x1 <- iris$Petal.Width
x2 <- iris$Sepal.Length
y  <- iris$Petal.Length
{% endhighlight %}

We now construction our linear model, the fastest way of doing this is using the QR decomposition.


{% highlight r %}
XI <- cbind(x1, x2, 1) # tie all the independent variables together
solve(t(X1)%*%X1) %*% t(X1)%*%Y # solve for beta
{% endhighlight %}



{% highlight text %}
## Error in t(X1): object 'X1' not found
{% endhighlight %}

In `R` functions are treated as objects, which means that we ascribe our function to a name, the same way we ascribed our variables to a name (e.g. `x1 <- Petal.Length`).

In order to create a function, we use the construction function (a function to construct an object of the type function).
Logically, but perhaps also somewhat confusingly, this constructor function is called `function()`.

Lets start with creating a function, called `squaring`, that takes one numeric argument (parameter) and returns it squared.


{% highlight r %}
squaring <- function(a) a^2
squaring(a = 2)
{% endhighlight %}



{% highlight text %}
## [1] 4
{% endhighlight %}

We can also use negatives of course.


{% highlight r %}
squaring(-3)
{% endhighlight %}



{% highlight text %}
## [1] 9
{% endhighlight %}

Note that in the above call we did not even specify that the input (`-3`) had to be assigned to the argument `a`,
this was automatically deduced by `R`.

We can also construction a function that takes two arguments and multiplies them.


{% highlight r %}
multiplying <- function(a, b) {
  a * b
}
multiplying(5, -6)
{% endhighlight %}



{% highlight text %}
## [1] -30
{% endhighlight %}

Note that because this function was a bit longer, I put the code on a new line.
When doing this, the code has to be enclosed in curly brackets (`{` and `}`).

