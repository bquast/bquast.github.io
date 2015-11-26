---
title: "Handcoding a Difference in Differences"
output: html_document
permalink: handcoding-difference-in-differences
tags: [R, linear model, panel model, panel data]
published: false
---

In this post we will discuss how to manually implement a Difference-in-Differences (DiD) estimator in R,
using simulated data.


{% highlight r %}
# reproducible random numbers
set.seed(123)

# untreated and treated independent variable
# for period 0
xutr  <- rnorm(1000, mean=5)
xtr   <- rnorm(1000, mean=1)

# create a data.frame with the dep. var., indep. var., time and id vars
# for period 0
dfutr <- data.frame(time = 0, id=   1:1000, y=xutr+15+rnorm(1000), x=xutr)
dftr  <- data.frame(time = 0, id=1001:2000, y=xtr +9+rnorm(1000), x=xtr )
df0    <- rbind(dfutr, dftr)

# repeat for period 1, different means
xutr  <- rnorm(1000, mean=10)
xtr   <- rnorm(1000, mean=2 )

# repeat for period 1
dfutr <- data.frame(time = 1, id=   1:1000, y=xutr+20+rnorm(1000), x=xutr)
dftr  <- data.frame(time = 1, id=1001:2000, y=xtr +18+rnorm(1000), x=xtr )
df1    <- rbind(dfutr, dftr)

# repeat for period 2, different means again
xutr  <- rnorm(1000, mean=15)
xtr   <- rnorm(1000, mean=3 )

# repeat for period 2, now with different effect on dep. var for treated (id=1001:2000)
dfutr <- data.frame(time = 2, id=   1:1000, y=xutr+25+rnorm(1000), x=xutr)
dftr  <- data.frame(time = 2, id=1001:2000, y=xtr +22+rnorm(1000), x=xtr )
df2    <- rbind(dfutr, dftr)
{% endhighlight %}

Let have a look if the data came out the way we wanted it to.


{% highlight r %}
# summarise data
summary(df0)
{% endhighlight %}



{% highlight text %}
##       time         id               y                x         
##  Min.   :0   Min.   :   1.0   Min.   : 5.615   Min.   :-2.048  
##  1st Qu.:0   1st Qu.: 500.8   1st Qu.:10.052   1st Qu.: 1.055  
##  Median :0   Median :1000.5   Median :14.800   Median : 3.009  
##  Mean   :0   Mean   :1000.5   Mean   :15.015   Mean   : 3.029  
##  3rd Qu.:0   3rd Qu.:1500.2   3rd Qu.:19.957   3rd Qu.: 5.008  
##  Max.   :0   Max.   :2000.0   Max.   :24.548   Max.   : 8.241
{% endhighlight %}



{% highlight r %}
summary(df1)
{% endhighlight %}



{% highlight text %}
##       time         id               y               x         
##  Min.   :1   Min.   :   1.0   Min.   :15.96   Min.   :-1.262  
##  1st Qu.:1   1st Qu.: 500.8   1st Qu.:20.03   1st Qu.: 2.030  
##  Median :1   Median :1000.5   Median :24.57   Median : 6.289  
##  Mean   :1   Mean   :1000.5   Mean   :24.99   Mean   : 6.001  
##  3rd Qu.:1   3rd Qu.:1500.2   3rd Qu.:29.94   3rd Qu.: 9.967  
##  Max.   :1   Max.   :2000.0   Max.   :34.11   Max.   :13.446
{% endhighlight %}



{% highlight r %}
summary(df2)
{% endhighlight %}



{% highlight text %}
##       time         id               y               x         
##  Min.   :2   Min.   :   1.0   Min.   :20.57   Min.   :-0.402  
##  1st Qu.:2   1st Qu.: 500.8   1st Qu.:24.91   1st Qu.: 2.912  
##  Median :2   Median :1000.5   Median :32.11   Median : 8.800  
##  Mean   :2   Mean   :1000.5   Mean   :32.51   Mean   : 8.986  
##  3rd Qu.:2   3rd Qu.:1500.2   3rd Qu.:40.03   3rd Qu.:15.006  
##  Max.   :2   Max.   :2000.0   Max.   :44.14   Max.   :18.848
{% endhighlight %}

We now combine the observations into a single data.frame and add some dummy variables required for the estimation.


{% highlight r %}
# combine data
df <- rbind(df0, df1, df2)

# create a dummy for post-treatment
df$post        <- ifelse(df$time  >= 2, TRUE, FALSE)
df$treated     <- ifelse(df$id <= 1000, TRUE, FALSE)
df$interaction <- df$post*df$treated
{% endhighlight %}

The below plot illustrates what the common trend assumption does.


{% highlight r %}
library(ggplot2)
library(dplyr)

df %>%
  group_by(time, treated) %>%
  summarise(y=mean(y)) %>%
  ggplot(aes(x=time)) + 
    geom_line(aes(y=y, colour=treated)) + 
    geom_abline(intercept=10, slope=10, colour="red", linetype=2) # assumed common trend
{% endhighlight %}

![plot of chunk plot](/images/source/2015-11-01-handcoding-difference-in-differences/plot-1.png) 

Compute the DiD estimator.


{% highlight r %}
lm(y ~ x + post + interaction, data=df)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = y ~ x + post + interaction, data = df)
## 
## Coefficients:
## (Intercept)            x     postTRUE  interaction  
##      12.116        1.746        7.706       -6.005
{% endhighlight %}
