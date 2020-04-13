---
layout: single
title: "Handcoding a Logit Model"
permalink: handcoding-logit
tags: [R, logit model, glm model]
---

Below is an example of how to handcode a logit model.


{% highlight r %}
set.seed(123)
n=100
N=1+rpois(n,5)
X1=runif(n)
X2=rexp(n)
s=X2-X1-2
p=exp(s)/(1+exp(s))
vY=NULL
for(i in 1:n){
 Y=rbinom(1,prob=p[i],size=N[i])
 vY=c(vY,Y)
}
db=data.frame(Y=vY,N=N,X1,X2)
head(db,4)
{% endhighlight %}



{% highlight text %}
##   Y N        X1         X2
## 1 3 5 0.5999890 1.80312932
## 2 1 8 0.3328235 0.03005945
## 3 0 5 0.4886130 1.30344055
## 4 0 9 0.9544738 0.19979224
{% endhighlight %}



{% highlight r %}
vY=vX1=vX2=vN=NULL
for(i in 1:n){
 vY=c(vY,c(rep(0,db$N[i]-db$Y[i]),rep(1,db$Y[i])))
 vX1=c(vX1,rep(db$X1[i],db$N[i]))
 vX2=c(vX2,rep(db$X2[i],db$N[i]))
}
largedb=data.frame(Z=vY,X1=vX1,X2=vX2)
head(largedb,16)
{% endhighlight %}



{% highlight text %}
##    Z        X1         X2
## 1  0 0.5999890 1.80312932
## 2  0 0.5999890 1.80312932
## 3  1 0.5999890 1.80312932
## 4  1 0.5999890 1.80312932
## 5  1 0.5999890 1.80312932
## 6  0 0.3328235 0.03005945
## 7  0 0.3328235 0.03005945
## 8  0 0.3328235 0.03005945
## 9  0 0.3328235 0.03005945
## 10 0 0.3328235 0.03005945
## 11 0 0.3328235 0.03005945
## 12 0 0.3328235 0.03005945
## 13 1 0.3328235 0.03005945
## 14 0 0.4886130 1.30344055
## 15 0 0.4886130 1.30344055
## 16 0 0.4886130 1.30344055
{% endhighlight %}


{% highlight r %}
hours <- 	c(0.50,	0.75,	1.00,	1.25,	1.50,	1.75,	1.75,	2.00,	2.25,	2.50,	2.75,	3.00,	3.25,	3.50,	4.00,	4.25,	4.50,	4.75,	5.00,	5.50)
pass <- c(	0,	0,	0,	0,	0,	0,	1,	0,	1,	0,	1,	0,	1,	0,	1,	1,	1,	1,	1,	1)
{% endhighlight %}



{% highlight r %}
glm(pass ~ hours, family = binomial)
{% endhighlight %}



{% highlight text %}
##
## Call:  glm(formula = pass ~ hours, family = binomial)
##
## Coefficients:
## (Intercept)        hours  
##      -4.078        1.505  
##
## Degrees of Freedom: 19 Total (i.e. Null);  18 Residual
## Null Deviance:	    27.73
## Residual Deviance: 16.06 	AIC: 20.06
{% endhighlight %}


{% highlight r %}
# for two hours
1 / ( 1 + exp(-(-4.0777+1.5046*2) ) )
{% endhighlight %}



{% highlight text %}
## [1] 0.2556884
{% endhighlight %}



{% highlight r %}
download.file('http://www.ats.ucla.edu/stat/sas/dae/binary.sas7bdat', destfile = "binary.sas7bdat")
library(haven)
binary <- read_sas('binary.sas7bdat')
names(binary) <- tolower(names(binary))
{% endhighlight %}


{% highlight r %}
glm(admit~gre+gpa+as.factor(rank), family=binomial, data=binary)
{% endhighlight %}



{% highlight text %}
##
## Call:  glm(formula = admit ~ gre + gpa + as.factor(rank), family = binomial,
##     data = binary)
##
## Coefficients:
##      (Intercept)               gre               gpa  
##        -3.989979          0.002264          0.804038  
## as.factor(rank)2  as.factor(rank)3  as.factor(rank)4  
##        -0.675443         -1.340204         -1.551464  
##
## Degrees of Freedom: 399 Total (i.e. Null);  394 Residual
## Null Deviance:	    500
## Residual Deviance: 458.5 	AIC: 470.5
{% endhighlight %}


{% highlight r %}
mle.logreg = function(fmla, data)
{
    # Define the negative log likelihood function
    logl <- function(theta,x,y){
      y <- y
      x <- as.matrix(x)
      beta <- theta[1:ncol(x)]

      # Use the log-likelihood of the Bernouilli distribution, where p is
      # defined as the logistic transformation of a linear combination
      # of predictors, according to logit(p)=(x%*%beta)
      loglik <- sum(-y*log(1 + exp(-(x%*%beta))) - (1-y)*log(1 + exp(x%*%beta)))
      return(-loglik)
    }

    # Prepare the data
    outcome = rownames(attr(terms(fmla),"factors"))[1]
    dfrTmp = model.frame(data)
    x = as.matrix(model.matrix(fmla, data=dfrTmp))
    y = as.numeric(as.matrix(data[,match(outcome,colnames(data))]))

    # Define initial values for the parameters
    theta.start = rep(0,(dim(x)[2]))
    names(theta.start) = colnames(x)

    # Calculate the maximum likelihood
    mle = optim(theta.start,logl,x=x,y=y,hessian=F)

    # Obtain regression coefficients
    beta = mle$par

    # Calculate the Information matrix
    # The variance of a Bernouilli distribution is given by p(1-p)
    p = 1/(1+exp(-x%*%beta))
    V = array(0,dim=c(dim(x)[1],dim(x)[1]))
    diag(V) = p*(1-p)
    IB = t(x)%*%V%*%x

    # Return estimates
    out = list(beta=beta,vcov=solve(IB),dev=2*mle$value)
}

binary$rank = factor(binary$rank) #Treat rank as a categorical variable
fmla = as.formula("admit~gre+gpa+rank") #Create model formula
mylogit = mle.logreg(fmla, binary) #Estimate coefficients
mylogit
{% endhighlight %}



{% highlight text %}
## $beta
##  (Intercept)          gre          gpa        rank2        rank3
## -1.452667967  0.001346342 -0.130762174  0.719615014 -0.217101795
##        rank4
## -0.010104814
##
## $vcov
##               (Intercept)           gre           gpa         rank2
## (Intercept)  1.2042376637 -2.685639e-04 -0.2751006299 -1.187537e-01
## gre         -0.0002685639  1.139736e-06 -0.0001266842  1.221842e-05
## gpa         -0.2751006299 -1.266842e-04  0.1022979333  7.058475e-03
## rank2       -0.1187537263  1.221842e-05  0.0070584755  1.152379e-01
## rank3       -0.1007985536  3.832995e-05 -0.0028178585  8.732829e-02
## rank4       -0.1362108989  2.956123e-05  0.0089986268  8.826868e-02
##                     rank3         rank4
## (Intercept) -1.007986e-01 -1.362109e-01
## gre          3.832995e-05  2.956123e-05
## gpa         -2.817858e-03  8.998627e-03
## rank2        8.732829e-02  8.826868e-02
## rank3        1.384753e-01  8.797479e-02
## rank4        8.797479e-02  1.701939e-01
##
## $dev
## [1] 497.3367
{% endhighlight %}
