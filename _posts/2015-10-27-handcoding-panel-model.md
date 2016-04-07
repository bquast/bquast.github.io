---
title: "Handcoding a Panel Model"
output: html_document
permalink: handcoding-panel-model
tags: [R, linear model, panel model, panel data]
---


{% highlight r %}
# load the PLM library for panel estimation
library(plm)
# load the Crime data set
data(Crime)
{% endhighlight %}

The most basic panel estimation is the Pooled OLS model,
this model combines all data across indices and performs a regular Ordinary Least Squares Estimation.


{% highlight r %}
# define the model
m1 <- formula(crmrte ~ prbarr + prbconv + polpc)

# create a panel data.frame (pdata.frame) object
PanelCrime <- pdata.frame(Crime, index=c("county", "year") )

# estimate Pooled OLS using the basic lm function
lm(formula = m1,
   data    = Crime)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = m1, data = Crime)
## 
## Coefficients:
## (Intercept)       prbarr      prbconv        polpc  
##    0.043643    -0.050993    -0.003251     3.055626
{% endhighlight %}



{% highlight r %}
# estimate the Pooled OLS using the plm package
plm(formula = m1,
    data    = PanelCrime,
    model   = "pooling"  )
{% endhighlight %}



{% highlight text %}
## 
## Model Formula: crmrte ~ prbarr + prbconv + polpc
## 
## Coefficients:
## (Intercept)      prbarr     prbconv       polpc 
##    0.043643   -0.050993   -0.003251    3.055626
{% endhighlight %}

A more complex estimation method is the Fixed-Effect (or within) estimator.
If our data only contains to time-periods,
the results of this estimator are equivalent to a OLS estimation of the first-differenced variables.


{% highlight r %}
# create data.frame with only years 81 and 82
Crime8182      <- subset(Crime, year %in% c(81, 82) )
# put into panel data.frame form (pdata.frame)
PanelCrime8182 <- pdata.frame(Crime8182, index=c("county", "year") )

# first difference the non-panel data.frame
library(dplyr)
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'dplyr'
## 
## The following object is masked from 'package:plm':
## 
##     between
## 
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
{% endhighlight %}



{% highlight r %}
Crime8182FD <- Crime8182 %>%
  group_by(county) %>%
  summarise(crmrte  = diff(crmrte),
            prbarr  = diff(prbarr),
            prbconv = diff(prbconv),
            polpc   = diff(polpc)   )

# use lm to estimate the two-period fixed-effects model
lm (formula = m1,
    data    = Crime8182FD    )
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = m1, data = Crime8182FD)
## 
## Coefficients:
## (Intercept)       prbarr      prbconv        polpc  
##  -6.133e-05   -1.965e-02   -1.537e-03    3.358e+00
{% endhighlight %}



{% highlight r %}
# verify with the plm package
plm(formula = m1,
    data    = PanelCrime8182,
    model   = "fd"           )
{% endhighlight %}



{% highlight text %}
## 
## Model Formula: crmrte ~ prbarr + prbconv + polpc
## 
## Coefficients:
## (intercept)      prbarr     prbconv       polpc 
## -6.1332e-05 -1.9645e-02 -1.5365e-03  3.3584e+00
{% endhighlight %}

If our data set contains more than two time periods,
we need to estimate an proper fixed effects model.
This is done by creating a fixed-effect variable for every level along the cross-sectional index (i.e. the non-time index).
A simple way of doing this, is by encoding the cross-section index as a factor and including that factor in the regression (more on factors/categorical variables in the post on [Handcoding a Linear Model](/handcoding-linear-model)).


{% highlight r %}
fe <- lm (formula = crmrte ~ prbarr + prbconv + polpc + factor(county),
          data    = Crime)
fe$coefficients[2:4]
{% endhighlight %}



{% highlight text %}
##       prbarr      prbconv        polpc 
## -0.008008440 -0.001010476  2.029003066
{% endhighlight %}



{% highlight r %}
      plm(formula = m1,
          data    = PanelCrime,
          model   = "within"   )
{% endhighlight %}



{% highlight text %}
## 
## Model Formula: crmrte ~ prbarr + prbconv + polpc
## 
## Coefficients:
##     prbarr    prbconv      polpc 
## -0.0080084 -0.0010105  2.0290031
{% endhighlight %}
