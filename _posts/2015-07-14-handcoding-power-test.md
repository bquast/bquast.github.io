---
layout: single
title: "Hand Coding the Power of a Test"
tags: [R, hand coding, linear model, power, test, hypthesis]
permalink: handcoding-power-test
---

We want to test if our population average is different from twenty.

We therefore specify the follow zero hypothesis.

$$
H_0: \mu = 20
$$

The alternative hypothesis is

$$
H_a: \mu \neq 20
$$


Accepting   



{% highlight r %}
mu <- 40
sd <- 8
n  <- 20
ci <- qnorm(0.975)*sd/sqrt(n)
(left <- mu - ci)
{% endhighlight %}



{% highlight text %}
## [1] 36.49391
{% endhighlight %}



{% highlight r %}
(right <- mu + ci)
{% endhighlight %}



{% highlight text %}
## [1] 43.50609
{% endhighlight %}
