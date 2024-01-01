---
layout: single
title: "Using Days of the Week to Understand Modulo"
header:
  overlay_image: /assets/images/FHE.jpg
  overlay_filter: 0.5
---

Today is Monday January 1st 2024, what day of the week will it be in 7 days?

> That is pretty simple, a week has seven days, so seven days from now exactly one week will have passed, and it will again be a Monday.

That’s correct, now let’s make this slightly harder, what day of the week will it be in 14 days?

> That is also pretty simple, a week has seven days, 2 times 7 is 14, so in 14 days exactly 2 weeks will have passed and it will again be a Monday.

Excellent, we can of course apply this same trick for any number of weeks, e.g. 21 days, 28 days, 35 days, etc.

Now let’s make it a little bit harder still, what day of the week will it be in 15 days?

> That is still pretty easy, as we calculated above, a period of 2 weeks is 14 days, so if we do:

```
15 - (2 x 7) = 1
```

That gives us the number of days from a Monday that we need to calculate, in this case 1 day after a Monday, it will be on a Tuesday.

So 15 days from Monday today, it will be a Tuesday.

This concept, of removing one or multiple completed cycles of something (like a week) out of a total is something that we actually use a lot.

For instance, look at the below digital alarm clock, it says it is 20:00.

If someone asks you “what time is it?”, you wouldn’t respond “it’s 20 o’clock”. Instead, you would answer “it is eight o’clock”.

The reason for this is that a normal wall clock has 12 hours, after it completes the first 12-hour cycle of the day, it starts again, from 1 o’clock, we indicate this 2nd cycle with PM.

To arrive from 20:00 to eight o’clock, we do this small calculation:

```
20 - 12 = 8
```

In other words, we remove the completed cycle of the clock, so that we go from 20:00 to normal-human language and say “eight o’clock”.

We can do this for more than 1 cycle of the clock. For instance, imagine that 30 hours have passed in the new year, and you want to know what time it is. The calculation for this is:

```
30 - (2 * 12) = 6
```

In other words, we know that we can extract two 12-hour cycles from the 30-hour window, and the remainder will be 6, so it will be 6 AM on January 2nd 2024.

We know that from 30 hours we cannot extract more than two 12-hour cycles of the clock, since three 12-hour cycles would come to 36 hours, which is more than the 30-hour window that we have.

Another example can be the date in a certain number of days from now. Since today is January 1st 2024, if we want to know the date in 370 days from now we do:

```
370 - 365 = 5
```

Using that today is January 1st, we add 5 days to arrive at January 6th (2025). We can also do this for larger number of days, e.g. 755 days from now:

```
755 - (2 * 365) = 25
```

Again using that today is January 1st, in 755 days it will be 1 + 25 = January 26th (2026).

Something more exciting, in skateboarding one of the most popular tricks on a ramp or half pipe is to do a rotation in the air, e.g. half rotation is called a 180, because it is a 180 degree rotation (a full circle is 360 degrees, so half of 360 = 180).

When you get better at skateboarding you might be able to perform multiple rotations, for example a 540 is a rotation-and-a-half.

In order to land your 540 well, you need to know in which direction you are heading when you land, a 180 will mean you will be going forward when you land, a 360 means you will land the same way as you took off, but now heading in the other direction (so backwards).

If we want to know in which direction we are going to be heading when we land, we extract as many 360 degree (a full circle on a compass):

```
540 - 360 = 180
```

So if we perform a 540, we will be landing the same way as when we perform a 180, meaning we land going forward.

This operation — extracting as many full-cycles of a larger number — is called modulo. So if we want to know what time it is 30 hours into the new year, we perform:

30 modulo 12 = 30 - (2x12) = 6

You can also use any internet search engine to perform this, e.g. let’s look at [what time it will be 1,000 hours into the new year](https://www.google.com/search?q=1000+mod+12).

A final example, imagine that you are getting a pizza with 3 people, the pizza is cut into 14 slices. You agree to each have the same number of slices. How many slices of pizza will be left over (to get the right size to-go-box)?

```
16 - (4x3) = 14 - 12 = 2
```

That’s it, you now know how to perform the mathematical operation called the modulo.

# Why is this important? 

As more and more of our information is appearing online, it is flowing everywhere easily. In order to protect that information, we use cryptography, we encrypt our personal data.

Modulo is the key mathematical operation that is used in encryption. I give a simple demonstration in my next article 
