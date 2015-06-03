---
layout: post
title: "Handcoded Categorical Variables"
tags: [R, linear model, lm, ols]
published: false
---

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

![plot of chunk hist_Fertility](/images/source/2015-06-3-handcoded-categorical/hist_Fertility-1.png) 

As we're dealing with Switzerland in the 19th certury,
there are essentially only two religions, Protestant and Roman-Catholic.
As the predominantly protestant authorities in the cities are generally suspicious of the Catholic mountainfolk, this variable is astutely labelled `Catholic` (indicating the percentage of Catholic citizen, the percentage of protestant is therefore `100 - Catholic`).


{% highlight r %}
hist(Catholic)
{% endhighlight %}

![plot of chunk hist_Catholic](/images/source/2015-06-3-handcoded-categorical/hist_Catholic-1.png) 


Dichotonomous Categorical Variables
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
Religion <- ifelse(Catholic >= 50, "Catholic", "Protestant")
Catholic_D <- ifelse(Religion == "Catholic", 1, 0)
table(Catholic_D)
{% endhighlight %}



{% highlight text %}
## Catholic_D
##  0  1 
## 29 18
{% endhighlight %}

Let's reload our function from [last week](/handcoded-lm-function).


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
i.e. in the order that we specified when we created the function (`function(y, X, ...)`).

Let's quickly verify if we obtain the same results from the built in function.


{% highlight r %}
lm(Fertility ~ Catholic_D)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = Fertility ~ Catholic_D)
## 
## Coefficients:
## (Intercept)   Catholic_D  
##       66.22        10.24
{% endhighlight %}


General categorical Variables
-----------------------------
A general categorical variable can take more than two values.

Mapping a dichotonomous variable to a dummy works so well,
because the two possible values that the varible can take are both,
mutually exclusive as well as collectively exhausting.
In plain terms this means that it is one or the other, never both and never neither.

Knowing this, we can use one dummy variable to describe one value (Catholic)
and the other value is implicit, since not-Catholic (`Catholic == 0`) means Protestant.

For categorical variables with more than two possible values this is not true.
Consider, not `A`, could mean `B`, or it could mean `C` (if we had three possible values).
This means that we need to create a dummy variable for each possible value.


Handcoding
------------

We start by creating a categorical variable with three possible values.
We can use our existing `Religion` variable for this.


{% highlight r %}
table(Religion)
{% endhighlight %}



{% highlight text %}
## Religion
##   Catholic Protestant 
##         18         29
{% endhighlight %}

Now we have to choose a region for which we are going to change the religion.


{% highlight r %}
row.names(swiss)
{% endhighlight %}



{% highlight text %}
##  [1] "Courtelary"   "Delemont"     "Franches-Mnt" "Moutier"     
##  [5] "Neuveville"   "Porrentruy"   "Broye"        "Glane"       
##  [9] "Gruyere"      "Sarine"       "Veveyse"      "Aigle"       
## [13] "Aubonne"      "Avenches"     "Cossonay"     "Echallens"   
## [17] "Grandson"     "Lausanne"     "La Vallee"    "Lavaux"      
## [21] "Morges"       "Moudon"       "Nyone"        "Orbe"        
## [25] "Oron"         "Payerne"      "Paysd'enhaut" "Rolle"       
## [29] "Vevey"        "Yverdon"      "Conthey"      "Entremont"   
## [33] "Herens"       "Martigwy"     "Monthey"      "St Maurice"  
## [37] "Sierre"       "Sion"         "Boudry"       "La Chauxdfnd"
## [41] "Le Locle"     "Neuchatel"    "Val de Ruz"   "ValdeTravers"
## [45] "V. De Geneve" "Rive Droite"  "Rive Gauche"
{% endhighlight %}

The nineth position is occupied by Gruyere.


{% highlight r %}
row.names(swiss)[9]
{% endhighlight %}



{% highlight text %}
## [1] "Gruyere"
{% endhighlight %}

This is a mountain region know for its dairy products such a cheese and chocolate.

Let us suppose that due to a large inflow this region is now predominantly Eastern-Orthodox.


{% highlight r %}
Religion[9] <- "Orthodox"
table(Religion)
{% endhighlight %}



{% highlight text %}
## Religion
##   Catholic   Orthodox Protestant 
##         17          1         29
{% endhighlight %}

We are now in a position where we can no longer map our `Religion` variable to a dummy variable.
We can however create a dummy variable for every possible value.


{% highlight r %}
Catholic_D <- ifelse(Religion == "Catholic", 1, 0)
Protestant_D <- ifelse(Religion == "Protestant", 1, 0)
Orthodox_D <- ifelse(Religion == "Orthodox", 1, 0)
Religion_D <- cbind(Catholic_D, Protestant_D, Orthodox_D)
Religion_D
{% endhighlight %}



{% highlight text %}
##       Catholic_D Protestant_D Orthodox_D
##  [1,]          0            1          0
##  [2,]          1            0          0
##  [3,]          1            0          0
##  [4,]          0            1          0
##  [5,]          0            1          0
##  [6,]          1            0          0
##  [7,]          1            0          0
##  [8,]          1            0          0
##  [9,]          0            0          1
## [10,]          1            0          0
## [11,]          1            0          0
## [12,]          0            1          0
## [13,]          0            1          0
## [14,]          0            1          0
## [15,]          0            1          0
## [16,]          0            1          0
## [17,]          0            1          0
## [18,]          0            1          0
## [19,]          0            1          0
## [20,]          0            1          0
## [21,]          0            1          0
## [22,]          0            1          0
## [23,]          0            1          0
## [24,]          0            1          0
## [25,]          0            1          0
## [26,]          0            1          0
## [27,]          0            1          0
## [28,]          0            1          0
## [29,]          0            1          0
## [30,]          0            1          0
## [31,]          1            0          0
## [32,]          1            0          0
## [33,]          1            0          0
## [34,]          1            0          0
## [35,]          1            0          0
## [36,]          1            0          0
## [37,]          1            0          0
## [38,]          1            0          0
## [39,]          0            1          0
## [40,]          0            1          0
## [41,]          0            1          0
## [42,]          0            1          0
## [43,]          0            1          0
## [44,]          0            1          0
## [45,]          0            1          0
## [46,]          1            0          0
## [47,]          1            0          0
{% endhighlight %}

Let's check we don't have any regions for which no value is true, or regions for which more than one variable is true.


{% highlight r %}
rowSums(Religion_D)
{% endhighlight %}



{% highlight text %}
##  [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
## [34] 1 1 1 1 1 1 1 1 1 1 1 1 1 1
{% endhighlight %}

Yup! that looks good. Time to run a regression.


{% highlight r %}
linear_model(y = Fertility, X = Religion_D, intercept = TRUE)
{% endhighlight %}



{% highlight text %}
## Error in solve.default(t(X) %*% X): Lapack routine dgesv: system is exactly singular: U[4,4] = 0
{% endhighlight %}

What happened here? Why didn't this work? All our rows summed up to `1`!

Our matrix is singular precisely because of that reason. The fact that all dummy variables sum to `1`,
and that a row with one `1`s is included (remember the [intercept](/handcoded-lm)) makes the matrix singular.

There are two ways to solve this, we can either drop the intercept, or drop one of the dummies.
Dropping the intercept would lead to a very distorted estimation of our coefficients.
Additionally, dropping of of the dummy variables is a good idea. It will make this value the baseline, which makes comparison easier.

If you think about it, removing the last dummy is what we did implicitly in the dichotonomous case.
If we would have created two dummies there, the second one would have been implicit in the first one (as its' negation).
The same occurs here, if a region is not `Catholic` and is not either `Orthodox`, then it must be protestant.

Since `Protestant` was our baseline in the previous estimation (since there was dummy for Catholic), we will stick with that.


{% highlight r %}
Religion_D <- cbind(Catholic_D, Orthodox_D)
linear_model(y = Fertility, X = Religion_D, intercept = TRUE)
{% endhighlight %}



{% highlight text %}
##                 [,1]
## Catholic_D  9.891075
## Orthodox_D 16.179310
##            66.220690
{% endhighlight %}

Finally, lets see if this is what we get if we use the built in model.


{% highlight r %}
lm(Fertility ~ Religion_D)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = Fertility ~ Religion_D)
## 
## Coefficients:
##          (Intercept)  Religion_DCatholic_D  Religion_DOrthodox_D  
##               66.221                 9.891                16.179
{% endhighlight %}

Which gives us the same result!
