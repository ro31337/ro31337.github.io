---
layout: post
title: Easiest Way Ever To Profile Your Rails App
---

How much time it takes for Rails to generate your page? We can easily measure that. But what if you want to find bottlenecks, to measure how much time it takes to render partials, to find the code that wastes your CPU? There is one very easy way to find this out. 

Let's add two gems to your `Gemfile`:

{% gist 2f6503a62f02ddb9dd81 %}

Execute `bundle install`, restart rails and open your browser on the page you want to profile, like `http://localhost:3000/list-of-items`. Make sure it works and now just add `?pp=flamegraph` to your url, so it will look like `http://localhost:3000/list-of-items?pp=flamegraph`.

You'll see a flamegraph, just like this:

![Flamegraph](https://cloud.githubusercontent.com/assets/1477672/11157950/95fd6ece-8a09-11e5-835b-f6fcf4cca03f.png)

You can navigate, zoom in, zoom out like you do it on Google Maps. Here is how you can read this graph. It may look similar to other visualizations from profilers, but it is different.

* Each box represents a function in the stack (a "stack frame").
* The y-axis shows stack depth (number of frames on the stack). The top box shows the function that was on-CPU. Everything beneath that is ancestry. The function beneath a function is its parent, just like the stack traces shown earlier.
* The x-axis spans the sample population. It does not show the passing of time from left to right, as most graphs do. The left to right ordering has no meaning (it's sorted alphabetically to maximize frame merging).
* The width of the box shows the total time it was on-CPU or part of an ancestry that was on-CPU (based on sample count). Functions with wide boxes may consume more CPU per execution than those with narrow boxes, or, they may simply be called more often. The call count is not shown (or known via sampling).
* The sample count can exceed elapsed time if multiple threads were running and sampled concurrently.
* The colors aren't significant, and are usually picked at random to be warm colors (other meaningful palettes are supported). This visualization was called a "flame graph" as it was first used to show what is hot on-CPU, and, it looked like flames. 

Links and resources:

* [rack-mini-profiler](https://github.com/MiniProfiler/rack-mini-profiler)
* [flamegraph](https://github.com/SamSaffron/flamegraph)
* [CPU Flame Graphs](http://www.brendangregg.com/FlameGraphs/cpuflamegraphs.html)
