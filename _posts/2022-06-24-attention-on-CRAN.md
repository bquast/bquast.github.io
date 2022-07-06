---
layout: single
title: "attention package on CRAN"
permalink: attention 
tags: [R, attention, self-attention, transformers, machine learning]
---

The `attention` `R` package, describing how to implement from scratch the [attention mechanism](https://en.m.wikipedia.org/wiki/Attention_(machine_learning)) - which forms the basis of [transformers](https://en.m.wikipedia.org/wiki/Transformer_(machine_learning_model)) - in the [R language](https://en.m.wikipedia.org/wiki/R_(programming_language)) is now available on [CRAN](https://cran.r-project.org/package=attention).

A key example of the results that were achieved using (much larger and more complex forms of) `transformers` is the change from [AlphaFold (1)](https://en.m.wikipedia.org/wiki/AlphaFold#AlphaFold_1,_2018) (which relied primarily on `LSTM`) to [AlphaFold2](https://en.m.wikipedia.org/wiki/AlphaFold#AlphaFold_2,_2020) (which is primarily based on `transformers`). This change pushed the results in the protein folding competition [CASP-14](https://predictioncenter.org/casp14/) to a level of accuracy that made the protein structure prediction accurate enough for practical purposes. A major scientific breakthrough, the impact of which can barely overstated.

The role of the `attention` mechanism here is key. The 3D structures of protein can be such that they "fold back onto themselves", this means that the amino acids that constitute the protein, can be spaced far apart in the sequence, but nevertheless be spatially in close proximity, and hence interact with each other. `LSTM` has a limited ability to model this, whereas the `attention` algorithm, does not have a limit on how far sequentially apart elements can be in order to interact with each other.

The `attention` package can be installed simply from within `R` by running:
```
install.packages('attention')
```

It does not have any dependencies, `C++`, `Fortran`, `Java`, or any other type of complexities. It is written purely in `base` `R`, so it should install without any issue on any R version.

Following installation, you can load the package using:
```
library(attention)
```

The package contains two `HTML` vignettes: `simple_attention` and `complete_attention`. Both vignettes implement the `attention` algorithm identically. However, `simple_attention` uses a number of helper functions included in the `attention` package, in order to present the algorithm in an accessible form. The `complete_attention` vignette does not use any helper functions, and simply uses `base` `R` functions.

The suggested way to work through this is to start with `simple_attention`, which you can load using:

```
vignette('simple_attention')
```

After having worked through the vignette, you can then dive a bit deeper into the same example with `complete_attention` using:

```
vignette('complete_attention')
```

Development takes place on GitHub:

<https://github.com/bquast/attention>

You can also file any bugs reports there:

<https://github.com/bquast/attention/issues>

The code is based to a large extent on last week's post: [Self-Attention from Scratch in R](/attention-in-R).

