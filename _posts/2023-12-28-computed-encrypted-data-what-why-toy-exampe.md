---
layout: single
title: "Computing on Encrypted Data - What? Why? Toy example"
header:
  overlay_image: /assets/images/FHE.jpg
  overlay_filter: 0.5
---
Encryption is typically used to protect information like written communication from evedropping, such as messages sent over the internet.

However, encryption can also be used to encrypt data, moreso using homomorphic encryption, the data can still be computed upon while encrypted, the results will still be encrypted (but valid), and can be decrypted again by the orignal data owner/supplier.

A couple of years ago I was skiing on Mont Blanc, I had a bad fall and an X-ray was made. This being a mountain village (Chamonix) there was not medical docter available, so a vetinarian (there are lots of cows in those mountains) took it. A vetinarian may be able to operate the X-ray machine, but they cannot interpret the photograph (all true up to here).

Now imagine that an American multinational cloud provider offers to analyze my X-ray photo using their AI. For legal reasons, they have to do this on a server in the US, however, equally for legal reasons, the medical image cannot be sent outside of Europe for an American for-profit company.

Here is where homomorphic encryption comes in, we can encrypt the X-ray, so that the information cannot be interpreted. This makes it possible to send the encrypted version of the X-ray to the US, to analyze is using their AI algorithm.

The result — let’s say it is a bounding box around any potential fractures, will still be encrypted, but it can be sent back to Chamonix for decryption, allowing me to see if I need to get medical attention.

How is this possible? How can we compute on data while it is encrypted? Isn’t it just jiberish?

Yes, the data is indeed just jiberish, both to humans and to computers (without the decryption key). However, homomorphic means it is a shape-preserving form of encryption. This shape-preserving property is what makes the results still valid.

Let’s look at a toy example. Imagine that you know that you are going to receive $ 456,333.- plus an additional $ 89,666.- next month. You want to know how much you are going to receive in total, but you don’t have a calculator to compute this.

I do have a calculator and I offer to compute it for you, but you don’t want me to know the exact values of the sums.

One thing you could do is add a small amount to each of the amounts(something that you can compute manually, using pen and paper). You then give me these “obfuscated” values to sum up using my calculator. When I present you with the outcome, you remove again the two small amounts that you added, to obtain a valid result.

Let’s try doing that:

```
456,333 + 2 = 456,335
 89,222 + 1 =  89,223
```

You now give me the obfuscated values 456,335 and 89,223.

Using my calculator I now compute the result:

```
456,335 + 89,223 = 545,558
```

I return the outcome of the (obfuscated) computation to you. You then remove the two small amounts you had added to the original values, to obtain the valid sum:

```
545,558 - 2 - 1 = 545,555
```

You now obtained a valid result for you computation ($ 545,555.-), without having to share the actual values with me.

This is how homomorphic encryption works, of course, we *for actual security, we need to use much more sopisticated forms of encryption*.

Additionally, in this example, transformed the values by adding values to them, this gives valid results for sumation (as we showed) or distraction, but in gives invalid results for e.g. multiplication or division.

Therefore, we need a form of transformation that actually encrypts the results strongly and at the same time also allow e.g. multiplication, giving valid results.

