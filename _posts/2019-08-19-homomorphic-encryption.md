---
layout: single
title: "homomorphic encryption in R"
output: html_document
permalink: homomorphic-encryption 
tags: [homomorphic encryption]
categories:
  - homomorphic-encryption
---

Homomorphic encryption is allows computations to be performed on encrypted data.
This has enormous potential in areas of machine learning that deal with private data, such as medical records.

Below is an implementation of homomorphic encryption in R.

It encrypts two pieces of data `m=10` and `m1=2`, once they are encrypted (as `cipher` and `cipher2` respectively), 
the two encrypted forms can be added up together (`cyphertotal`).

They can then be descrypted to reveal `mess2` to equal `12` (i.e. the sum of `10` and `2`).

Note that the "computation" could be done by a other party that does not have access to the unencrypted data.

This can be used e.g. to send medical records to another party that has experience building machine learning models, without revealing the private medical data to this other party.

```
library(numbers)

# greater common divisor function
gcd <- function(a,b) {
  rk_1 <- a
  rk_2 <- b
  while (rk_2 > 0) {
    rk   <- rk_1 %% rk_2
    rk_1 <- rk_2
    rk_2 <- rk
  }
  return( rk_1 )
}

# least common multiplier function
lcm <- function(a,b) {
  (a*b)/gcd(a,b)
}

# extended_euclidean_algorithm
extended_euclidean_algorithm <- extGCD
 
# inverse_of
inverse_of <- modinv

p = 17
q = 19

# define the message
m = 10

n = p * q

gLambda = lcm(p-1,q-1)

r = floor(runif(1, 20, 150))

g = floor(runif(1, 20, 150))

l <- floor( (modpower(g, gLambda, n*n)-1)/n )
gMu = inverse_of(l,n)

k1 = modpower(g, m, n*n)
k2 = modpower(r, n, n*n)

cipher = (k1*k2) %% (n*n)

l <- floor( (modpower(cipher, gLambda, n*n)-1)/n )

mess = (l * g)

m1 = 2

k3 = modpower(g, m1, n*n)

cipher2 = (k3*k2) %% (n*n)
ciphertotal = (cipher*cipher2) %% (n*n)
l = floor( (modpower(ciphertotal, gLambda, n*n)-1)/n )
mess2 = (l * gMu) %% n
```
