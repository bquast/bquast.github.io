---
layout: post
title: "Hand Coding Hilton's Dropout"
tags: [R, neural network, hilton, dropout, machine learning]
permalink: handcoding-dropout
---

Andrew Trask wrote an amazing post at [I am Trask](http://iamtrask.github.io/) called:

> [A Neural Network in 11 lines of Python](http://iamtrask.github.io/2015/07/12/basic-python-network/)

In the post [Hand Coding a Neural Network](/handcoding-neural-network) I've translated the Python code into R.

In a follow up post called:

> [A Neural Network in 13 lines of Python](http://iamtrask.github.io/2015/07/27/python-network-part2/)

Andrew shows how to improve the network with optimisation through gradient descent.

The third post called:

> [Hinton's Dropout in 3 Lines of Python](http://iamtrask.github.io/2015/07/28/dropout/)

explains a feature called dropout. The `R` version of the code is posted below.

Below I've translated the original Python code used in the post to R.
The original post has an excellent explanation of what each line does.
I've tried to stay as close quto the original code as possible,
all lines and comments correspond directly to the original code.


{% highlight r %}
# no importing here
X = matrix(c(0,0,1,0,1,1,1,0,1,1,1,1), nrow=4, byrow=TRUE)
y = matrix(c(0,1,1,0),nrow=4)
alpha = 0.5; hidden_dim = 4; dropout_percent = 0.2; do_dropout = TRUE
synapse_0 = matrix(runif(n = 3*hidden_dim, min=-1, max=1), nrow=3)
synapse_1 = matrix(runif(n = hidden_dim, min=-1, max=1), ncol=1)
for (j in 1:60000) {
  layer_1 = 1 / ( 1 + exp(-( X%*%synapse_0)) )
  if (do_dropout) {
    layer_1 = layer_1 * matrix(rbinom(n=4*hidden_dim,size=1,prob=1-dropout_percent), nrow=hidden_dim) * ( 1/(1*dropout_percent) ) }
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
##           [,1]     [,2]      [,3]      [,4]
## [1,]  554.4577 414.1806  496.4543 -7.651356
## [2,]  534.1158 552.6160  522.1801  7.646160
## [3,] 1062.5671 983.2091 1026.9585 -1.521438
{% endhighlight %}



{% highlight r %}
synapse_1
{% endhighlight %}



{% highlight text %}
##             [,1]
## [1,]   0.2387679
## [2,]   0.4412015
## [3,]   0.3448019
## [4,] -14.1149737
{% endhighlight %}
