---
layout: single
title: "Backpropagation by Hand"
permalink: backpropagation-by-hand
tags: [machine learning, neural network]
mathjax: true
---

> doing backpropagation using pen and paper

We begin with a simple problem, we have two guys, Joris and Carl, who both want to be tall.

Joris is dutch and so he drinks a pint of milk a day, so that he may grow tall.

Carl is Swedish and so he eat meatballs with jam to grow tall.

We can represent this in a matrix: 

\`X = [[1,0],[0,1]] \`
{: .kdmath}

It turns out that Joris' plan worked and Carl's didn't, so out output matrix \` y \` is \` [[1],[0]] \`,
where Joris gets a 1 for being tall and Carl gets a 0 for being short.

Let's now use a neural network to learn this information about the effects of milk and swedish meatball with jam on the growing tall.

We "randomly", initiate our weights which gives us \` w_1 = [[0],[1]] \`, 
as we know this is precisely wrong, since this will predict that drinking a pint of milk a day doesn't make one tall,
but eating swedish meatballs with jam will. Using \` X * w_1 = hat y_1 \`, we get

\` hat y_1 = [[0],[1]] \`

We can now compute by how much the network's prediction missed the true values using \` hat y_1 - y \`, which gives us our 

\` [[0],[1]] - [[1],[0]] = [[-1],[1]] = delta_1\`

We now use \` delta_1 \` to update our weights: \` w_1 - delta_1 = w_2\`:

\` [[0],[1]] - [[-1],[1]] = [[1],[0]] = w_2 \`

This is the backpropagation.

We can now make a new prediction using \` X * w_2 = hat y_2 \`, which gives

\` [[1],[0]] \`

Which is equal to the true values.

The network has now learned through backpropagation that drinking milk helps one grow tall, but eating swedish meatballs with jam does not.
