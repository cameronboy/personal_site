---
title: "List Comprehensions vs Loops"
date: 2018-01-13T10:54:54-07:00
draf: false
categories:
- Python
tags:
- python
- list comprehension
- for loop
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->



I've always thought list comprehensions in Python were really nifty. When you need to gather a subset from some list or create a new list and in either case logic is necessary, there's really no other option.

And on top of that, they're dang easy to read. Take a simple routine to grab the even numbers 0-99999:

```python
evens = [x for x in range(100000) if x % 2 == 0]
```
One line, and it looks so sublime :+1:. Compare that to the loop variant doing the same thing:

```python
evens = list()
for x in range(100000):
    if x % 2 == 0:
        evens.append(x)
```
eesh.. :-1:

It's still functional, works quite well and for those coming from other languages this may be the route taken.

## Performance
On top of looking a lot nicer and consuming three less lines of code, it is also faster  I am told. I did some *crude* (Don't judge) tests that are in no way scientific. I just wanted to play around with the ```%%timeit ``` [jupter cell magic](http://ipython.readthedocs.io/en/stable/interactive/magics.html) command to see difference :wink:. For a more comprehensive comparison between loops and comprehensions (and map), I would take a look at [this post](http://ipython.readthedocs.io/en/stable/interactive/magics.html).

The list comprehension was indeed faster. This operation took on average 6.7 milliseconds to complete with about two thirds of the tests coming between 6.6 - 6.9 milliseconds.

The loop on the other hand was on average ~50% slower: 9.8ms per operation with two thirds of its tests coming between 9.4ms- 10ms

We're looking at super quick times for this simple operation, but when scaled up in complexity the list comprehensions will typically run faster and will be faster to comprehend for us, the human trying to read it.
