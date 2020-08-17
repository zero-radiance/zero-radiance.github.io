---
title: "Quantitative Analysis of Z-Buffer Precision"
date: 2020-08-15T12:22:10-07:00
categories: [ "Graphics", "Math" ]
tags: [
    "Analysis",
    "Z-Buffer",
    "Precision",
    ]
draft: true
---

High Z-buffer precision is something that we take for granted these days. Since the introduction of [reverse Z-buffering](https://doi.org/10.1145/311534.311579) (and, assuming you use all the standard tricks like [camera-relative transforms](https://pharr.org/matt/blog/2018/03/02/rendering-in-camera-space.html)), most depth precision issues are a thing of the past. You ask the the rasterizer to throw triangles at the screen and, almost magically, and they appear at the right place and in the right order.

There are many existing articles about Z-buffer precision ([1](https://doi.org/10.1145/311534.311579), [2](https://mynameismjp.wordpress.com/2010/03/22/attack-of-the-depth-buffer/), [3](http://www.geometry.caltech.edu/pubs/UD12.pdf), [4](http://www.humus.name/Articles/Persson_CreatingVastGameWorlds.pdf), [5](https://outerra.blogspot.com/2012/11/maximizing-depth-buffer-range-and.html), [6](http://dev.theomader.com/depth-precision/), [7](https://developer.nvidia.com/content/depth-precision-visualized)), as well as sections in the [Real-Time Rendering](http://www.realtimerendering.com/book.html) and the [Foundations of Game Engine Development](https://foundationsofgameenginedev.com/#fged2) books. So, why write another one? While there's nothing wrong with the intuition and the results presented there, I find the numerical analysis part (specifically, its presentation) a bit lacking, so that, even after reading all these articles, I still don't feel that I have achieved what Jim Blinn would call the [ultimate understanding](https://doi.org/10.1109/38.210494) of the concept and the way it actually works in practice.

<!--more-->

With that in mind, the goals of this article are:

1. Use actual 32-bit floating-point depth values, as well as a realistic depth range as an example.
2. Analyze the numerical results and draw conclusions.
3. Show all steps of the analysis in detail.

All steps are very simple, and can be easily reproduced using my [Mathematica script](here).

Without further ado, let us get started.

## Reverse Z-Buffering Basics

We can perform a perspective transformation of a column vector representing a view-space point using the following matrix:

$$ \tag{1} P_r =
\begin{bmatrix}
    p/a & 0 & 0       & 0 \cr
    0   & p & 0       & 0 \cr
    0   & 0 & n/(n-f) & -n f / (n-f) \cr
    0   & 0 & 1       & 0
\end{bmatrix},
$$

where \\(a\\) is the aspect ratio of the sensor, and \\(n,f,p\\) are the distances to the near, far and projection planes, respectively.

The resulting vector will have 4 components: \\([x',y',z',w]^{\mathrm{T}}\\). We can see that the \\(w\\) component is equal to the third coordinate of the original view-space point (the so-called "linear depth"), and we shall label it this way. We can obtain the value stored in the depth buffer value by means of perspective division: \\(z=z'/w\\).

Stated more precisely,

$$ \tag{2} z = \frac{n}{n-f} -\frac{1}{w} \frac{n f}{n-f}. $$

Then, either by inverting the projection matrix, or solving Equation (2) for \\(w\\), we can recover

$$ \tag{3} w = \frac{n f}{n - z (n - f)}. $$

We shall examine a fairly typical open-world case of \\(n=0.1\\) and \\(f=10000\\) meters.

## Fixed-Point Depth Buffer

In order to warm up and to obtain a comparison point, let us consider an unsigned, normalized, fixed-point depth buffer. Assuming 32-bit \\((k=32)\\) binary storage that we interpret as a decimal number \\(d\\), we can compute the depth value it represents as follows:

$$ \tag{4} u = \frac{d}{2^k-1}. $$

Plugging \\(u\\) instead of \\(z\\) in Equation (3) allows us to map the values stored within the depth buffer to the original linear depth values they represent.

{{< figure src="/img/fixed_point_1.png" >}}

The graph looks like a bad joke, but it is very much real. Almost all depth values are allocated for objects close to the viewer. Plotting the same graph on a logarithmic scale allows us to take a better look:

{{< figure src="/img/fixed_point_2.png" >}}

We can see that more than 3.5 billion depth values are used for objects less than 1 meter away from the viewer.

Since we are interested in precision of the depth buffer, we should find a way to plot its resolution. By *resolution* we mean the smallest linear depth difference that results in a different binary value of \\(z\\). In other words, for a certain decimal number \\(d\\), it is the absolute difference between the linear depth value \\(d\\) corresponds to and the linear depth value of its immediate neighbor.

Mathematically, since (i) \\(w\\) is an analytic function of \\(z\\) and (ii) \\(u\\) is an analytic function of \\(d\\), we can obtain the desired graph by plotting the absolute value of the derivative \\(\partial w(u) / \partial d\\).

{{< figure src="/img/fixed_point_3.png" >}}

Unfortunately, the resulting graph is somewhat hard to read. While we can tell that the resolution at the far plane is around 1 decimeter, and is well below 1 nanometer at the near plane, it is kind of hard to tell what is going on in-between without doing many back-and-forth comparisons between the previous graph and the current one.

We can overcome this predicament by (literally) converting the graph above into a large list of pairs \\(\lbrace d, \partial w / \partial d \rbrace\\), and then, for each one, replacing the decimal number \\(d\\) with the corresponding value of \\(w\\), and plotting these pairs as points. Because of the \\(z\\)-reversal, the new graph will appear transposed. You can ignore the spotty appearance of the graph - it is an artifact of plotting only a small subset of 4 billion values.

{{< figure src="/img/fixed_point_4.png" >}}

It is now immediately clear that, for example, we can expect sub-millimeter resolution at distances smaller than 500 meters.

## Floating-Point Depth Buffer

