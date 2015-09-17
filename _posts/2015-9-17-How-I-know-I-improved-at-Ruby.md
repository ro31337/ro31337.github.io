---
layout: post
title: How I know I improved at Ruby
---

While first starting out with Ruby, I coded a Caesar Cipher algorithm (shift each character in a string by n, so 'c' becomes 'e' if n=2, 'f' becomes 'h', etc.). And, to contrast, ten few minutes ago I was going through some challenges for practice and came across the same problem. I searched through my own code base and found another solution I wrote ~3 years back.

So now I have three versions of my Caesar Cipher implementation in Ruby:

* The very first one (it's not the solution actually, because it's buggy), dated 2010 or so
* The second one, 2 years back
* Current one

My first solution while super new at Ruby. I would even call it Java-style approach. I noticed that this doesn't properly iterate past z/Z.

{% gist ebd5d962039b2a05236c %}

Second, much better now:

{% gist d3a98196474a439aba4b %}

What I coded ten minutes ago (in about two minutes):

{% gist e2443ef124c48c203fd5 %}

One liner and supports unicode! Not bad, huh.
