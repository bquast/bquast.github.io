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
  synapse_1 = synapse_1 - alpha * ( t(layer_1) %*% layer_2_delta )
  synapse_0 = synapse_0 - alpha * ( t(X) %*% layer_1_delta )                 }
{% endhighlight %}

The output of this is:


{% highlight r linenos %}
synapse_0
{% endhighlight %}



{% highlight text %}
##            [,1]      [,2]      [,3]      [,4]
## [1,]  0.6599928 -4.112528 -6.566531  5.152019
## [2,] -0.2885111  4.197566  6.513230 -5.273546
## [3,]  0.1117825  2.538704 -3.402528 -2.994635
{% endhighlight %}



{% highlight r linenos %}
synapse_1
{% endhighlight %}



{% highlight text %}
##            [,1]
## [1,] -0.6862487
## [2,] -5.9624483
## [3,] 11.8858211
## [4,]  7.6466318
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
  
  for (j in 1:60000) {
    
    # Feed forward through layers 0, 1, and 2
    layer_0 = X
    layer_1 = sigmoid(layer_0%*%synapse_0)
    layer_2 = sigmoid(layer_1%*%synapse_1)
    
    # how much did we miss the target value?
    layer_2_error = layer_2 - y
    
    if (j %% 10000 == 0)
      print(paste("Error after", j, "iterations:", mean(abs(layer_2_error))))
    
    # in what direction is the target value?
    # were we really sure? if so, don't change too much.
    layer_2_delta = layer_2_error * signmoid_output_to_derivative(layer_2)
    
    # how much did each l1 value contribute to the l2 error (according to the weights)?
    layer_1_error = layer_2_delta %*% t(synapse_1)
    
    # in what direction is the target l1?
    # were we really sure? if so, don't change too much.
    layer_1_delta = layer_1_error * signmoid_output_to_derivative(layer_1)
    
    syanpse_1 = synapse_1 - alpha * ( t(layer_1) %*% layer_2_delta )
    synapse_0 = synapse_0 - alpha * ( t(layer_0)%*%layer_1_delta   )                   }  }
{% endhighlight %}



{% highlight text %}
## [1] "Training With Alpha 0.001"
## [1] "Error after 10000 iterations: 0.498823347999877"
## [1] "Error after 20000 iterations: 0.498917387108483"
## [1] "Error after 30000 iterations: 0.499018853229654"
## [1] "Error after 40000 iterations: 0.499121375353957"
## [1] "Error after 50000 iterations: 0.499219572353048"
## [1] "Error after 60000 iterations: 0.499308750697558"
## [1] "Training With Alpha 0.01"
## [1] "Error after 10000 iterations: 0.499497826959898"
## [1] "Error after 20000 iterations: 0.498641326465821"
## [1] "Error after 30000 iterations: 0.496956955112539"
## [1] "Error after 40000 iterations: 0.493890610439056"
## [1] "Error after 50000 iterations: 0.488671091119026"
## [1] "Error after 60000 iterations: 0.482015340894521"
## [1] "Training With Alpha 0.1"
## [1] "Error after 10000 iterations: 0.458964864668801"
## [1] "Error after 20000 iterations: 0.440296873961302"
## [1] "Error after 30000 iterations: 0.435721552956624"
## [1] "Error after 40000 iterations: 0.433741459806895"
## [1] "Error after 50000 iterations: 0.432645250195108"
## [1] "Error after 60000 iterations: 0.431951283530493"
## [1] "Training With Alpha 1"
## [1] "Error after 10000 iterations: 0.430648089011645"
## [1] "Error after 20000 iterations: 0.42973270388057"
## [1] "Error after 30000 iterations: 0.429425130948803"
## [1] "Error after 40000 iterations: 0.42926004089335"
## [1] "Error after 50000 iterations: 0.429152167876871"
## [1] "Error after 60000 iterations: 0.429075022172101"
## [1] "Training With Alpha 10"
## [1] "Error after 10000 iterations: 0.428909279036476"
## [1] "Error after 20000 iterations: 0.428783822612087"
## [1] "Error after 30000 iterations: 0.428744034006972"
## [1] "Error after 40000 iterations: 0.428724732170788"
## [1] "Error after 50000 iterations: 0.428713362098269"
## [1] "Error after 60000 iterations: 0.428705874754754"
## [1] "Training With Alpha 100"
## [1] "Error after 10000 iterations: 0.428691131215767"
## [1] "Error after 20000 iterations: 0.428680295041344"
## [1] "Error after 30000 iterations: 0.428676727569616"
## [1] "Error after 40000 iterations: 0.428674952802699"
## [1] "Error after 50000 iterations: 0.428673890942781"
## [1] "Error after 60000 iterations: 0.428673184328116"
## [1] "Training With Alpha 1000"
## [1] "Error after 10000 iterations: 0.452360975310751"
## [1] "Error after 20000 iterations: 0.452359991803577"
## [1] "Error after 30000 iterations: 0.452359694858478"
## [1] "Error after 40000 iterations: 0.452359550631165"
## [1] "Error after 50000 iterations: 0.452359465308302"
## [1] "Error after 60000 iterations: 0.452359408902304"
{% endhighlight %}



{% highlight r linenos %}
print("Output After Training:")
print(layer_1)
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training:"
##              [,1]         [,2]         [,3]         [,4]
## [1,] 4.324337e-07 9.999967e-01 8.963235e-07 9.998340e-01
## [2,] 1.456566e-08 1.668968e-06 9.999985e-01 8.425039e-05
## [3,] 1.079468e-08 1.145600e-06 7.084046e-19 5.782283e-05
## [4,] 3.635971e-10 6.288507e-18 5.205575e-07 8.091203e-13
{% endhighlight %}
