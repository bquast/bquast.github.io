---
layout: post
title: "Hand Coding Gradient Descent"
tags: [R, neural network, gradient, descent, machine learning]
permalink: handcoding-gradient-descent
---

Andrew Trask wrote an amazing post at [I am Trask](http://iamtrask.github.io/) called:

> [A Neural Network in 11 lines of Python](http://iamtrask.github.io/2015/07/12/basic-python-network/)

In the post [Hand Coding a Neural Network](/handcoding-neural-network) I've translated the Python code into R.

In a follow up post called:

> [A Neural Network in 13 lines of Python](http://iamtrask.github.io/2015/07/27/python-network-part2/)

Andrew shows how to improve the network with optimisation through gradient descent.

Below I've translated the original Python code used in the post to R.
The original post has an excellent explanation of what each line does.
I've tried to stay as close quto the original code as possible,
all lines and comments correspond directly to the original code.

The code for the Neural Network in 13 lines of R is:


{% highlight r linenos %}
# no importing here
X = matrix(c(0,0,1,0,1,1,1,0,1,1,1,1), nrow=4, byrow=TRUE)
y = matrix(c(0,1,1,0),nrow=4)
alpha = 0.5; hidden_dim = 4
synapse_0 = matrix(runif(n = 3*hidden_dim, min=-1, max=1), nrow=3)
synapse_1 = matrix(runif(n = hidden_dim, min=-1, max=1), ncol=1)
for (j in 1:60000) {
  layer_1 = 1 / ( 1 + exp(-( X%*%synapse_0)) )
  layer_2 = 1 / ( 1 + exp(-(layer_1%*%synapse_1)) )
  layer_2_delta = (layer_2-y)*(layer_2*(1-layer_2))
  layer_1_delta = (layer_2_delta %*% t(synapse_1)) * (layer_1*(1-layer_1))
  synapse_1 = synapse_1 - (alpha * t(layer_1) %*% layer_2_delta )
  synapse_0 = synapse_0 - (alpha * t(X) %*% layer_1_delta )                 }
{% endhighlight %}

The output of this is:


{% highlight r linenos %}
synapse_0
{% endhighlight %}



{% highlight text %}
##            [,1]       [,2]      [,3]      [,4]
## [1,]  1.6749966  4.0108150 -8.123309 -6.448839
## [2,] -0.3972389 -8.1330857  4.281592 -6.195072
## [3,]  1.7729916 -0.8021902 -1.193611  1.797256
{% endhighlight %}



{% highlight r linenos %}
synapse_1
{% endhighlight %}



{% highlight text %}
##           [,1]
## [1,] -5.039078
## [2,] 10.382043
## [3,]  9.738095
## [4,] -8.276506
{% endhighlight %}

After showing the 13 lines, Andrew builds a more simplistic version of this model in order to explain the workings,
the `R` version of this code is:


{% highlight r linenos %}
# no importing here

# compute sigmoid nonlinearity
sigmoid = function(x) {
  output = 1 / (1+exp(-x))
  return(output)            }

signmoid_output_to_derivative = function(output) {
  return( output*(1-output) )                      }

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
synapse_0 = matrix(runif(n = 3, min=-1, max=1), nrow=3)

for (iter in 1:10000) {
  
  # forward propagation
  layer_0 = X
  layer_1 = sigmoid(layer_0%*%synapse_0)
  
  # how much did we miss?
  layer_1_error = layer_1 - y
  
  # multiply how much we missed by the
  # slope of the sigmoid at the values in layer_1
  layer_1_delta = layer_1_error * signmoid_output_to_derivative(layer_1)
  syanpse_0_derivative = t(layer_0) %*% layer_1_delta
  
  # update weights
  synapse_0 = synapse_0 + t(layer_0)%*%layer_1_delta                     }
  
print("Output After Training:")
print(layer_1)
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training:"
##              [,1]
## [1,] 4.095320e-03
## [2,] 4.030840e-05
## [3,] 4.002302e-05
## [4,] 3.923468e-07
{% endhighlight %}

Finally a more legible version of the 13 lines model is developed, the `R` equivalent of this model is:


{% highlight r linenos %}
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



{% highlight r linenos %}
print("Output After Training:")
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training:"
{% endhighlight %}



{% highlight r linenos %}
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

> Try to rebuild this network from memory.

For `R` users it should hopefully be easier to do this using the code above.
