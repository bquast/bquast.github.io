---
layout: single
title: "Hand Coding a Neural Network"
tags: [R, neural network, machine learning]
permalink: handcoding-neural-network
categories:
  - neural-networks
---

Andrew Trask wrote an amazing post at [I am Trask](http://iamtrask.github.io/) called:

> [A Neural Network in 11 lines of Python](http://iamtrask.github.io/2015/07/12/basic-python-network/)

Below I've translated the original `python` code used in the post to `R`.
The original post has an excellent explanation of what each line does.
I've tried to stay as close quto the original code as possible,
all lines and comments correspond directly to the original code.

The code for the Neural Network in 11 lines of R is:


{% highlight r %}
X = matrix(c(0,0,1,0,1,1,1,0,1,1,1,1), nrow=4, byrow=TRUE)
y = matrix(c(0,1,1,0),nrow=4)
syn0 = matrix(runif(n = 12, min=-1, max=1), nrow=3)
syn1 = matrix(runif(n =  4, min=-1, max=1), nrow=4)
for (j in 1:60000) {
  l1 = 1 / ( 1 + exp(-( X%*%syn0)) )
  l2 = 1 / ( 1 + exp(-(l1%*%syn1)) )
  l2_delta = (y-l2) * (l2*(1-l2))
  l1_delta = (l2_delta %*% t(syn1)) * (l1 * (1-l1))
  syn1 = syn1 + t(l1) %*% l2_delta
  syn0 = syn0 + t(X) %*% l1_delta                         }
{% endhighlight %}

The output of this is:


{% highlight r %}
syn0
syn1
{% endhighlight %}



{% highlight text %}
##           [,1]        [,2]       [,3]      [,4]
## [1,]  6.646488  4.03637200  4.4586682  5.708943
## [2,] -4.362702 -1.61649721  4.6813254 -7.354098
## [3,]  1.541886  0.06133004 -0.7624999 -2.481254
##           [,1]
## [1,] -9.027182
## [2,] -3.653207
## [3,]  6.740129
## [4,] 11.972215
{% endhighlight %}

After showing the 11 lines, Andrew builds a more simplistic version of this model in order to explain the workings,
the `R` version of this code is:


{% highlight r %}
# no importing here

# sigmoid function
nonlin = function(x,deriv=FALSE) {
  if(deriv==TRUE)
    return( x*(1-x) )
  return( 1/(1+exp(-x)) )         }

# input dataset
X = matrix(c(0,0,1,
             0,1,1,
             1,0,1,
             1,1,1), nrow=4, byrow=TRUE)

# output dataset
y = matrix(c(0,1,1,0), nrow=4)

# seed random number to make calculation
# deterministic (just a good practice)
set.seed(1)

# initialize weights randomly with mean 0
syn0 = matrix(runif(n = 3, min=-1, max=1), nrow=3)

for (iter in 1:10000) {

  # forward propagation
  l0 = X
  l1 = nonlin(l0%*%syn0)

  # how much did we miss?
  l1_error = y - l1

  # multiply how much we missed by the slope of the
  # sigmoid at the values in L1
  l1_delta = l1_error * nonlin(l1,TRUE)

  # update weights
  syn0 = syn0 + t(l0)%*%l1_delta                    }

print("Output After Training:")
print(l1)
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training:"
##      [,1]
## [1,]  0.5
## [2,]  0.5
## [3,]  0.5
## [4,]  0.5
{% endhighlight %}

Finally a more legible version of the 11 lines model is developed, the `R` equivalent of this model is:


{% highlight r %}
# no importing here

nonlin = function(x,deriv=FALSE) {
  if(deriv==TRUE)
    return( x*(1-x) )

  return( 1/(1+exp(-x)) )
}

X = matrix(c(0,0,1,
             0,1,1,
             1,0,1,
             1,1,1), nrow=4, byrow=TRUE)

y = matrix(c(0,
             1,
             1,
             0),
           nrow=4)

set.seed(1)

# initialize weights randomly with mean 0
syn0 = matrix(runif(n = 12, min=-1, max=1), nrow=3)
syn1 = matrix(runif(n =  4, min=-1, max=1), nrow=4)

for (j in 1:60000) {

  # Feed forward through layers 0, 1, and 2
  l0 = X
  l1 = nonlin(l0%*%syn0)
  l2 = nonlin(l1%*%syn1)

  # how much did we miss the target value?
  l2_error = y - l2

  if (j %% 10000 == 0)
    print(paste("Error:", mean(abs(l2_error))))

  # in what direction is the target value?
  # were we really sure? if so, don't change too much.
  l2_delta = l2_error*nonlin(l2,deriv=TRUE)

  # how much did each L1 value contribute to the error (according to the weights)?
  l1_error = l2_delta %*% t(syn1)

  # in what direction is the target l1?
  # were we really sure? if so, don't change too much.
  l1_delta = l1_error * nonlin(l1, deriv=TRUE)

  syn1 = syn1 + t(l1) %*% l2_delta
  syn0 = syn0 + t(l0) %*% l1_delta                     }
{% endhighlight %}



{% highlight text %}
## [1] "Error: 0.0105538166393651"
## [1] "Error: 0.00729252475321203"
## [1] "Error: 0.0058973637409426"
## [1] "Error: 0.00507859874667397"
## [1] "Error: 0.00452508690333462"
## [1] "Error: 0.00411914123908981"
{% endhighlight %}



{% highlight r %}
print("Output After Training:")
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training:"
{% endhighlight %}



{% highlight r %}
print(l1)
{% endhighlight %}



{% highlight text %}
##              [,1]       [,2]      [,3]         [,4]
## [1,] 0.2458599375 0.88914038 0.3675612 0.4370393632
## [2,] 0.0002438464 0.03235284 0.9803207 0.0272840721
## [3,] 0.9516103977 0.99993716 0.9855892 0.0186708858
## [4,] 0.0144994169 0.98514792 0.9998294 0.0006869615
{% endhighlight %}

Andrew concludes his article with a number of helpful links, as well as the suggestion:

> **Try to rebuild this network from memory.** I know that might sound a bit crazy, but it seriously helps. If you want to be able to create arbitrary architectures based on new academic papers or read and understand sample code for these different architectures, I think that it's a killer exercise. I think it's useful even if you're using frameworks like Torch, Caffe, or Theano. I worked with neural networks for a couple years before performing this exercise, and it was the best investment of time I've made in the field (and it didn't take long).

For `R` users it should hopefully be easier to do this using the code above.
