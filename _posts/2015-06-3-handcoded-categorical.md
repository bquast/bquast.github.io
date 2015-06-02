---
layout: post
title: "Handcoded Categorical Variables"
tags: [R, linear model, lm, ols]
published: false
---

Recap
-----------
In last week's posts we discussed [handcoding a linear model](/handcoded-lm) and [writing a convenient function for this](/handcoded-lm-function),
in today's post we will take this a step further by including a categorical variable.


Swiss life
------------
Since I live in Geneva we will use a built-in data set that is close to home.


{% highlight r %}
data("swiss")
{% endhighlight %}

This data set compares fertility rates in 47 different French-speaking regions (sub-Cantonal) of Switzerland around the year 1888 (for more information see `help("swiss")`).


{% highlight r %}
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



{% highlight r %}
attach(swiss) # this makes the variables inside swiss directly accessible
{% endhighlight %}

In our study we want to look at the effect of religion on fertility.


{% highlight r %}
hist(Fertility)
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/images/source/2015-06-3-handcoded-categorical/unnamed-chunk-3-1.png) 

As we're dealing with Switzerland in the 19th certury,
there are essentially only two religions, Protestant and Roman-Catholic.
As the predominantly protestant authorities in the cities are generally suspicious of the Catholic mountainfolk, this variable is astutely labelled `Catholic` (indicating the percentage of Catholic citizen, the percentage of protestant is therefore `100 - Catholic`).


{% highlight r %}
hist(Catholic)
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/images/source/2015-06-3-handcoded-categorical/unnamed-chunk-4-1.png) 


Categorical Variables
----------------------------------
Handling categorical variables in a statistical model is bit different because they generally do not have a direct numerical representation.

A special case of categorical variable is the dichotonomous variable. This categorical variable can take only two forms, e.g. `Male|Female`, `Infected|Not-Infected`, `Pre-Intervention|Post-Intervention`, etc.

In Switzerland many issues are decided upon by popular vote, with no first-past-the-post or other democracy autrocities.
This means that having minimal majority can drastically change policymaking.
We use this as our justification for re-encoding the percentage variable `Catholic` to a dichotonomous variable `Catholic_D`

`D` here no longer stands for dichotonomous, it stands for dummy. A dummy variable is a variable that either takes the value `1` (TRUE) or `0` (FALSE).
This allows us to quantify the covariance of the question (Catholic?).
It is thus the effect of a region being Catholic rather than Protestant.


{% highlight r %}
Catholic_D <- ifelse(Catholic >= 50, 1, 0)
table(Catholic_D)
{% endhighlight %}



{% highlight text %}
## Catholic_D
##  0  1 
## 29 18
{% endhighlight %}

Let reload our function from [last week](/handcoded-lm-function).


{% highlight r %}
linear_model <- function (y, X, intercept=TRUE) {
  if (intercept) X <- cbind(X, 1)
  solve(t(X)%*%X) %*% t(X)%*%y # solve for beta
}
{% endhighlight %}

We can now call the function with the parameters set.


{% highlight r %}
linear_model(y = Fertility, X = Catholic_D, intercept = TRUE)
{% endhighlight %}



{% highlight text %}
##       [,1]
## X 10.24042
##   66.22069
{% endhighlight %}

Our model gives us an intercept of `66.22` and the coefficient of the variable `Catholic_D` is `10.24`.
Note that it wasn't necesarry to specify `intercept = TRUE` since this is already the default.
The `y = ` and `X = ` parts also are not necessary as long was we enter our object in the correct order,
i.e. in the order that we specified when we created the function (`function(y, X, ...)`.


General categorical Variable
-------------------------
A general categorical variable can take more than two values.
This makes it impossible to be mapped to a dummy variable.
