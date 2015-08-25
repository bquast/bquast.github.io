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
##           [,1]       [,2]      [,3]      [,4]
## [1,]  3.545988 -0.7574938 -4.059472 -6.855083
## [2,]  3.546789 -0.6330600 -4.065121 -6.851793
## [3,] -5.511110  0.1934818  6.271507  2.991949
{% endhighlight %}



{% highlight r linenos %}
synapse_1
{% endhighlight %}



{% highlight text %}
##             [,1]
## [1,]  -7.4613936
## [2,]   0.1730553
## [3,]   7.0278715
## [4,] -12.8437782
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



{% highlight r linenos %}
# no importing here

alphas = c(0.001,0.01,0.1,1,10,100,1000)

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
y = matrix(c(0,
             1,
             1,
             0), nrow=4)

for (alpha in alphas) {
  # print "\nTraining With Alpha:" + str(alpha)
  print(paste("Training With Alpha", alpha))
  set.seed(1)
  
  # randomly initialize our weights with mean 0
  synapse_0 = matrix(runif(n = 3*4, min=-1, max=1), nrow=3)
  synapse_1 = matrix(runif(n = 4,   min=-1, max=1), ncol=1)
  
  for (iter in 1:60000) {
    
    # Feed forward through layers 0, 1, and 2
    layer_0 = X
    layer_1 = sigmoid(layer_0%*%synapse_0)
    layer_2 = sigmoid(layer_1%*%synapse_1)
    
    # how much did we miss the target value?
    layer_2_error = layer_2 - y
    
    if (j %% 10000 == 0)
      print(paste("Error after", j, "iterations:", mean(abs(l2_error))))
    
    # in what direction is the target value?
    # were we really sure? if so, don't change too much.
    layer_2_delta = layer_2_error * signmoid_output_to_derivative(layer_2)
    
    # how much did each l1 value contribute to the l2 error (according to the weights)?
    layer_1_error = layer_2_delta %*% t(synapse_1)
    
    # in what direction is the target l1?
    # were we really sure? if so, don't change too much.
    layer_1_delta = layer_1_error * signmoid_output_to_derivative(layer_1)
    
    syanpse_1 = synapse_1 - t(layer_1) %*% layer_2_delta
    synapse_0 = synapse_0 + t(layer_0)%*%layer_1_delta                     }  }
{% endhighlight %}



{% highlight text %}
## Error in mean(abs(l2_error)): object 'l2_error' not found
{% endhighlight %}



{% highlight r linenos %}
print("Output After Training:")
print(layer_1)
{% endhighlight %}



{% highlight text %}
## [1] "Training With Alpha 0.001"
## [1] "Output After Training:"
##           [,1]      [,2]      [,3]      [,4]
## [1,] 0.5363624 0.6892851 0.5642007 0.3436915
## [2,] 0.4725164 0.5498694 0.6410268 0.2253184
## [3,] 0.4198776 0.8338545 0.7590684 0.1789740
## [4,] 0.3591562 0.7343018 0.8129318 0.1079971
{% endhighlight %}



{% highlight r linenos %}
print("Output After Training:")
print(layer_1)
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training:"
##           [,1]      [,2]      [,3]      [,4]
## [1,] 0.5363624 0.6892851 0.5642007 0.3436915
## [2,] 0.4725164 0.5498694 0.6410268 0.2253184
## [3,] 0.4198776 0.8338545 0.7590684 0.1789740
## [4,] 0.3591562 0.7343018 0.8129318 0.1079971
{% endhighlight %}
