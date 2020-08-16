---
title: "Quantitative Analysis of Z-Buffer Precision"
date: 2020-08-15T12:22:10-07:00
categories: [ "Graphics", "Math" ]
tags: [
    "Z-Buffer",
    "Precision",
    "Analysis",
    ]
draft: true
---

High Z-buffer precision is something that we take for granted these days. Since the introduction of [reverse-Z-buffering](https://doi.org/10.1145/311534.311579) (and, assuming you use all the standard tricks like [camera-relative transforms](https://pharr.org/matt/blog/2018/03/02/rendering-in-camera-space.html)), most depth precision issues are a thing of the past. You ask the the rasterizer to throw triangles at the screen and, almost magically, and they appear at the right place and in the right order.

There are many existing articles about Z-buffer precision ([1](https://doi.org/10.1145/311534.311579), [2](https://mynameismjp.wordpress.com/2010/03/22/attack-of-the-depth-buffer/), [3](http://www.geometry.caltech.edu/pubs/UD12.pdf), [4](http://www.humus.name/Articles/Persson_CreatingVastGameWorlds.pdf), [5](https://outerra.blogspot.com/2012/11/maximizing-depth-buffer-range-and.html), [6](http://dev.theomader.com/depth-precision/), [7](https://developer.nvidia.com/content/depth-precision-visualized)), as well as sections in the [Real-Time Rendering](http://www.realtimerendering.com/book.html) and the [Foundations of Game Engine Development](https://foundationsofgameenginedev.com/#fged2) books. So, why write another one? While there's nothing wrong with the intuition and the results presented there, I find the numerical analysis part lacking, which means that, even after reading all these articles, I still don't feel that I have achieved what Jim Blinn would call the [ultimate understanding](https://doi.org/10.1109/38.210494) of Z-buffer precision in practice.

<!--more-->

With that in mind, the goals of this article are:

1. Use actual 32-bit floating-point depth values, as well as a realistic depth range.
2. Show all steps of the analysis in detail.
3. Analyze the numerical results and draw conclusions.

Without further ado, let's get started.

## Radiative Transfer Equation

lol