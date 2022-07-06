---
layout: single
title: "Coding a Recurrent Neural Network from Scratch in R"
permalink: rnn-from-scratch-in-R
tags: [R, recurrent neural network, RNN, scratch]
categories:
  - neural-networks
---

This is an example of how to build a Recurrent Neural Network in R.


{% highlight r %}
# Set the seed to obtain identical random values
# set.seed(0)
{% endhighlight %}



{% highlight r %}
# compute sigmoid nonlinearity
sigmoid = function(x)
	1 / (1+exp(-x))
{% endhighlight %}



{% highlight r %}
# convert output of sigmoid function to its derivative
sigmoid_output_to_derivative = function(output)
  output*(1-output)
{% endhighlight %}



{% highlight r %}
# training dataset generation
# int2binary =
binary_dim = 8

largest_number = 2^binary_dim
int2binary = function(x) {
  tail(rev(as.integer(intToBits(x))), binary_dim) }
# for (i in 1:largest_number) {
#  int2binary[i] = binary[i]   }
{% endhighlight %}



{% highlight r %}
# input variables
alpha = 0.1
input_dim = 2
hidden_dim = 16
output_dim = 1
{% endhighlight %}



{% highlight r %}
# initialize neural network weights
synapse_0 = matrix(runif(n = input_dim*hidden_dim, min=-1, max=1), nrow=input_dim)
synapse_1 = matrix(runif(n = hidden_dim*output_dim, min=-1, max=1), nrow=hidden_dim)
synapse_h = matrix(runif(n = hidden_dim*hidden_dim, min=-1, max=1), nrow=hidden_dim)

synapse_0_update = matrix(0, nrow = input_dim, ncol = hidden_dim)
synapse_1_update = matrix(0, nrow = hidden_dim, ncol = output_dim)
synapse_h_update = matrix(0, nrow = hidden_dim, ncol = hidden_dim)
{% endhighlight %}



{% highlight r %}
# training logic
for (j in 1:10000) {

  # generate a simple addition problem (a + b = c)
  a_int = sample(1:(largest_number/2), 1) # int version
  a = int2binary(a_int) # binary encoding

  b_int = sample(1:(largest_number/2), 1) # int version
  b = int2binary(b_int)

  # true answer
  c_int = a_int + b_int
  c = int2binary(c_int)

  # where we'll store our best guesss (binary encoded)
  d = matrix(0, nrow = 1, ncol = binary_dim)

  overallError = 0

  layer_2_deltas = matrix(0)
  layer_1_values = matrix(0, nrow=1, ncol = hidden_dim)
  # layer_1_values = rbind(layer_1_values, matrix(0, nrow=1, ncol=hidden_dim))

  # moving along the positions in the binary encoding
  for (position in 0:(binary_dim-1)) {

    # generate input and output
    X = cbind(a[binary_dim - position],b[binary_dim - position])
    y = c[binary_dim - position]

    # hidden layer (input ~+ prev_hidden)
    layer_1 = sigmoid((X%*%synapse_0) + (layer_1_values[dim(layer_1_values)[1],] %*% synapse_h))

    # output layer (new binary representation)
    layer_2 = sigmoid(layer_1 %*% synapse_1)

    # did we miss?... if so, by how much?
    layer_2_error = y - layer_2
    layer_2_deltas = rbind(layer_2_deltas, layer_2_error * sigmoid_output_to_derivative(layer_2))
    overallError = overallError + abs(layer_2_error)

    # decode estimate so we can print it out
    d[binary_dim - position] = round(layer_2)

    # store hidden layer so we can print it out
    layer_1_values = rbind(layer_1_values, layer_1)                                                  }

  future_layer_1_delta = matrix(0, nrow = 1, ncol = hidden_dim)

  for (position in 0:(binary_dim-1)) {

    X = cbind(a[position+1], b[position+1])
    layer_1 = layer_1_values[dim(layer_1_values)[1]-position,]
    prev_layer_1 = layer_1_values[dim(layer_1_values)[1]-(position+1),]

    # error at output layer
    layer_2_delta = layer_2_deltas[dim(layer_2_deltas)[1]-position,]
    # error at hidden layer
    layer_1_delta = (future_layer_1_delta %*% t(synapse_h) + layer_2_delta %*% t(synapse_1)) * sigmoid_output_to_derivative(layer_1)

    # let's update all our weights so we can try again
    synapse_1_update = synapse_1_update + matrix(layer_1) %*% layer_2_delta
    synapse_h_update = synapse_h_update + matrix(prev_layer_1) %*% layer_1_delta
    synapse_0_update = synapse_0_update + t(X) %*% layer_1_delta

    future_layer_1_delta = layer_1_delta                             }


  synapse_0 = synapse_0 + ( synapse_0_update * alpha )
  synapse_1 = synapse_1 + ( synapse_1_update * alpha )
  synapse_h = synapse_h + ( synapse_h_update * alpha )

  synapse_0_update = synapse_0_update * 0
  synapse_1_update = synapse_1_update * 0
  synapse_h_update = synapse_h_update * 0

  # print out progress
  if(j %% 1000 ==0) {
    print(paste("Error:", overallError))
    print(paste("Pred:", paste(d, collapse = " ")))
    print(paste("True:", paste(c, collapse = " ")))
    out = 0
    for (x in 1:length(d)) {
      out[x] = rev(d)[x]*2^(x-1) }
    print(paste(a_int, "+", b_int, "=", sum(out)))
    print("----------------")                     }             }
{% endhighlight %}



{% highlight text %}
## [1] "Error: 3.81649822582355"
## [1] "Pred: 1 1 1 1 1 1 1 1"
## [1] "True: 1 1 0 1 1 0 0 1"
## [1] "101 + 116 = 255"
## [1] "----------------"
## [1] "Error: 4.14830099040879"
## [1] "Pred: 0 0 0 1 1 0 0 0"
## [1] "True: 0 1 1 0 0 0 1 1"
## [1] "71 + 28 = 24"
## [1] "----------------"
## [1] "Error: 3.66757955972057"
## [1] "Pred: 1 0 1 0 1 1 0 0"
## [1] "True: 1 1 0 0 1 1 0 0"
## [1] "86 + 118 = 172"
## [1] "----------------"
## [1] "Error: 4.17065609215719"
## [1] "Pred: 0 0 1 1 1 0 1 1"
## [1] "True: 0 1 0 0 0 0 1 0"
## [1] "53 + 13 = 59"
## [1] "----------------"
## [1] "Error: 3.0150689961351"
## [1] "Pred: 1 1 0 1 0 1 0 1"
## [1] "True: 1 1 0 1 0 1 0 1"
## [1] "114 + 99 = 213"
## [1] "----------------"
## [1] "Error: 1.57097151104127"
## [1] "Pred: 1 0 1 1 1 0 1 0"
## [1] "True: 1 0 1 1 1 0 1 0"
## [1] "61 + 125 = 186"
## [1] "----------------"
## [1] "Error: 1.19476602300567"
## [1] "Pred: 1 0 0 1 0 0 1 0"
## [1] "True: 1 0 0 1 0 0 1 0"
## [1] "35 + 111 = 146"
## [1] "----------------"
## [1] "Error: 0.788522354842796"
## [1] "Pred: 0 0 1 1 1 1 1 1"
## [1] "True: 0 0 1 1 1 1 1 1"
## [1] "40 + 23 = 63"
## [1] "----------------"
## [1] "Error: 0.369834653195442"
## [1] "Pred: 1 0 1 0 0 1 1 0"
## [1] "True: 1 0 1 0 0 1 1 0"
## [1] "66 + 100 = 166"
## [1] "----------------"
## [1] "Error: 0.315997944714675"
## [1] "Pred: 0 1 1 0 0 1 0 1"
## [1] "True: 0 1 1 0 0 1 0 1"
## [1] "37 + 64 = 101"
## [1] "----------------"
{% endhighlight %}
