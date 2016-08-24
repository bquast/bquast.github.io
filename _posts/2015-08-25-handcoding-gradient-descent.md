---
layout: single
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


{% highlight r %}
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


{% highlight r %}
synapse_0
{% endhighlight %}



{% highlight text %}
##            [,1]      [,2]      [,3]       [,4]
## [1,]  1.5366228 -6.771185 -5.500138  1.6577091
## [2,] -3.1873558 -6.775025 -5.572573 -0.6846272
## [3,] -0.9829552  2.857047  8.212964  1.7307744
{% endhighlight %}



{% highlight r %}
synapse_1
{% endhighlight %}



{% highlight text %}
##            [,1]
## [1,]   1.893171
## [2,] -11.847653
## [3,]  10.593323
## [4,]  -5.995991
{% endhighlight %}

After showing the 13 lines, Andrew builds a more simplistic version of this model in order to explain the workings,
the `R` version of this code is:


{% highlight r %}
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



{% highlight r %}
# no importing here

alphas = c(0.001,0.01,0.1,1,10,100,1000)
hiddenSize = 32

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
  synapse_0 = matrix(runif(n = 3*hiddenSize, min=-1, max=1), nrow=3)
  synapse_1 = matrix(runif(n = hiddenSize,   min=-1, max=1), ncol=1)

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
## [1] "Error after 10000 iterations: 0.496753340920726"
## [1] "Error after 20000 iterations: 0.494320217213883"
## [1] "Error after 30000 iterations: 0.491939692637302"
## [1] "Error after 40000 iterations: 0.489508049016763"
## [1] "Error after 50000 iterations: 0.486968980988299"
## [1] "Error after 60000 iterations: 0.484275190192169"
## [1] "Training With Alpha 0.01"
## [1] "Error after 10000 iterations: 0.47140878741352"
## [1] "Error after 20000 iterations: 0.430673004261222"
## [1] "Error after 30000 iterations: 0.391667343526469"
## [1] "Error after 40000 iterations: 0.355829177161297"
## [1] "Error after 50000 iterations: 0.324381160777944"
## [1] "Error after 60000 iterations: 0.296668486981059"
## [1] "Training With Alpha 0.1"
## [1] "Error after 10000 iterations: 0.219750597899847"
## [1] "Error after 20000 iterations: 0.147197211385351"
## [1] "Error after 30000 iterations: 0.120375186596677"
## [1] "Error after 40000 iterations: 0.105615467258296"
## [1] "Error after 50000 iterations: 0.0960583884638825"
## [1] "Error after 60000 iterations: 0.089372679886035"
## [1] "Training With Alpha 1"
## [1] "Error after 10000 iterations: 0.0749170413011143"
## [1] "Error after 20000 iterations: 0.0615658613361215"
## [1] "Error after 30000 iterations: 0.0555012252698068"
## [1] "Error after 40000 iterations: 0.0516962046045831"
## [1] "Error after 50000 iterations: 0.0490667414942408"
## [1] "Error after 60000 iterations: 0.047142847902833"
## [1] "Training With Alpha 10"
## [1] "Error after 10000 iterations: 0.0427029626972791"
## [1] "Error after 20000 iterations: 0.0383657458731247"
## [1] "Error after 30000 iterations: 0.0365183572133577"
## [1] "Error after 40000 iterations: 0.0354705851933649"
## [1] "Error after 50000 iterations: 0.0347890309528084"
## [1] "Error after 60000 iterations: 0.0343070171400147"
## [1] "Training With Alpha 100"
## [1] "Error after 10000 iterations: 0.12586144441154"
## [1] "Error after 20000 iterations: 0.124447474721783"
## [1] "Error after 30000 iterations: 0.123878063119214"
## [1] "Error after 40000 iterations: 0.123717125305205"
## [1] "Error after 50000 iterations: 0.12361973819046"
## [1] "Error after 60000 iterations: 0.123550366461951"
## [1] "Training With Alpha 1000"
## [1] "Error after 10000 iterations: 0.255542564895953"
## [1] "Error after 20000 iterations: 0.253337230286856"
## [1] "Error after 30000 iterations: 0.253229365154443"
## [1] "Error after 40000 iterations: 0.253134749163861"
## [1] "Error after 50000 iterations: 0.252853108979217"
## [1] "Error after 60000 iterations: 0.23941413106148"
{% endhighlight %}



{% highlight r %}
print("Output After Training (transposed):")
print(t(layer_1))
{% endhighlight %}



{% highlight text %}
## [1] "Output After Training (transposed):"
##               [,1]         [,2]         [,3]         [,4]
##  [1,] 9.757442e-01 2.741785e-01 4.777918e-06 4.486658e-08
##  [2,] 9.988992e-01 3.502226e-02 1.000000e+00 9.999858e-01
##  [3,] 2.928999e-04 9.908191e-01 1.016584e-11 3.744591e-06
##  [4,] 2.517921e-03 9.792622e-01 9.999987e-01 1.000000e+00
##  [5,] 9.999590e-01 9.999999e-01 9.999997e-01 1.000000e+00
##  [6,] 9.993254e-01 2.079943e-02 1.000000e+00 9.999914e-01
##  [7,] 9.992337e-01 6.676035e-03 4.972981e-07 2.563006e-12
##  [8,] 1.152518e-04 9.958097e-01 6.879570e-13 1.418382e-06
##  [9,] 2.157096e-03 9.829423e-01 9.999989e-01 1.000000e+00
## [10,] 9.996606e-01 1.031031e-02 1.000000e+00 9.999957e-01
## [11,] 9.997538e-01 7.669805e-03 1.000000e+00 9.999969e-01
## [12,] 9.923878e-01 1.000000e+00 1.658098e-05 9.999266e-01
## [13,] 1.077501e-05 1.996064e-04 2.474681e-11 4.585195e-10
## [14,] 1.050615e-03 9.674246e-01 4.755116e-10 1.342709e-05
## [15,] 9.999998e-01 1.000000e+00 9.999999e-01 1.000000e+00
## [16,] 1.891564e-02 1.476229e-10 9.999684e-01 2.423862e-04
## [17,] 7.572678e-05 1.355462e-07 3.392421e-07 6.071766e-10
## [18,] 1.011417e-04 3.220864e-05 7.002276e-07 2.229726e-07
## [19,] 9.995498e-01 3.841928e-03 3.161348e-07 5.491814e-13
## [20,] 9.998206e-01 5.677852e-03 1.000000e+00 9.999977e-01
## [21,] 4.520084e-04 9.861686e-01 3.618782e-11 5.705624e-06
## [22,] 9.997765e-01 7.040108e-03 1.000000e+00 9.999972e-01
## [23,] 1.207689e-04 6.112888e-05 9.125956e-07 4.618958e-07
## [24,] 9.993511e-01 9.999962e-01 9.999993e-01 1.000000e+00
## [25,] 1.747343e-04 9.943971e-01 2.232583e-12 2.267243e-06
## [26,] 8.296226e-03 9.999974e-01 6.322735e-05 9.996568e-01
## [27,] 1.287223e-03 2.699739e-03 4.801540e-06 1.008465e-05
## [28,] 9.996950e-01 9.148528e-03 1.000000e+00 9.999962e-01
## [29,] 3.642942e-04 9.886684e-01 1.927824e-11 4.615453e-06
## [30,] 9.952445e-01 4.397392e-02 2.644504e-06 5.812192e-10
## [31,] 9.973765e-01 9.760660e-01 9.999824e-01 9.998358e-01
## [32,] 9.994870e-01 4.232134e-03 3.814081e-07 8.320943e-13
{% endhighlight %}
