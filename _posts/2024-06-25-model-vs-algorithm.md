---
layout: single
title: "Model vs. Algorithm"

---

As discussion about Artificial Intelligence has become a mainstream topic, we hear a lot of terms used in loosely. 

One key example is model and algorithm, typically calling everything an algorithm (because it sounds more scientific?).

In fact, when we are talking about AI in applications (in society) we are almost always talking about models, not about algorithms.

A model is a stylized way to describe a relation between two things. Just like a London supermodel is a stylized way to show clothes.

An algorithm is a mathematic procedure to compute the model, much like a recipe to bake a cake. Or, say, the eating and exercise regimen you need to follow to become a fashion supermodel.

You are already very familiar with thinking about models, although they are rarely called that. Think about the scatter plot:

![Scatter Plot](/assets/images/scatter.png))

As you know, all the dots on the chart represent countries. As you can see there is a general south-west to north-east trend in the point cloud. This trend represents the fact that the greater a country's proportion of bank account holders, the more well off the country is (we are taking this as correlation, not going into causation).

We can illustrate this trend by drawing a line in the direction of the general trend in the point cloud (as indeed is done in this chart). This line is a stylized / simplified / synthesized way of describing the trend. This trend line is a vizualization of a model (as we used the term above).

The model here consists of 2 numbers, the first number is the height on the y-axis where the trend line intersects with it (in this example that section of the trend is not displaye, but it lies around 10 on the y-axis).

The second number is the steepness of the trend line, the greater the number the steeper the line, negative numbers describe a downward trend, very high numbers describe an almost vertical line (note that the 0-100 distance is greater on the x-axis in this chart than the y-axis, so the visual is a bit warped).

These two numbers - the y-intercept and the steepness parameter - together are a linear model. 

Now that we have established what a model is, what is an algorithm, and why would we even need something more?

The algorithm is a precise procedure for compute the parameters (numbers) of the model.

In the case of the linear model that we are discussing here the most popular algorithm to compute the parameters is called Ordinary Least Squares (OLS).

When we put all the values of the observations (the countries) in a matrix (a table) the OLS algorithm describes the mathematical operations to perform with the matrices to arrive at the two parameters of the model (y-intercept + trend steepness).

Note that like with cooking, there are often multiple recipes to arrive at the outcome (cake/model), for instance we could also use Newton's algorithm (which is a key part of what are now called "neural networks").

The concept algorithm was named after the 8th century B.C.  Persian mathematician [Muḥammad ibn Mūsā al-Khwārizmī](https://en.wikipedia.org/wiki/Al-Khwarizmi).
