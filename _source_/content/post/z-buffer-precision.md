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

High Z-buffer precision is something that we take for granted these days. Since the introduction of [reversed floating-point depth buffering](https://doi.org/10.1145/311534.311579) (and, assuming you use the standard tricks like [camera-relative transforms](https://pharr.org/matt/blog/2018/03/02/rendering-in-camera-space.html)), most depth precision issues are a thing of the past. You ask the the rasterizer to throw triangles at the screen and, almost magically, and they appear at the right place and in the right order.

There are many existing articles concerning Z-buffer precision ([1](https://doi.org/10.1145/311534.311579), [2](https://mynameismjp.wordpress.com/2010/03/22/attack-of-the-depth-buffer/), [3](http://www.geometry.caltech.edu/pubs/UD12.pdf), [4](http://www.humus.name/Articles/Persson_CreatingVastGameWorlds.pdf), [5](https://outerra.blogspot.com/2012/11/maximizing-depth-buffer-range-and.html), [6](http://dev.theomader.com/depth-precision/), [7](https://developer.nvidia.com/content/depth-precision-visualized)), as well as sections in the [Real-Time Rendering](http://www.realtimerendering.com/book.html) and the [Foundations of Game Engine Development](https://foundationsofgameenginedev.com/#fged2) books. So, why write another one? While there is nothing wrong with the intuition and the results presented there, I find the numerical analysis part (specifically, its presentation) a bit lacking, so that, even after reading all these articles, I still do not feel that I have achieved what Jim Blinn would call the [ultimate understanding](https://doi.org/10.1109/38.210494) of the concept and the way it actually works in practice.

<!--more-->

With that in mind, the goals of this article are:

1. Consider a test case using actual 32-bit floating-point depth values, as well as a realistic depth range.
2. Analyze numerical results and draw conclusions.
3. Show all steps of the analysis in detail (get my Mathematica script [here](here)).

In short, we know that reverse Z-buffering *is* good, and we know *why* it is good (when used with a floating-point depth buffer); what we would like to know is *what good really means* in practice. This blog post is not meant to replace the existing articles but, rather, to complement them.

If that is something that interests you, please read on.

## Reverse Z-Buffering Basics

We can perform a perspective transformation of a column vector representing a view-space point using the matrix

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

In order to warm up and to obtain a comparison point, let us consider an unsigned, normalized, fixed-point depth buffer. Assuming 32-bit binary storage \\((k=32)\\) that we interpret as a decimal number \\(d\\), we can compute the depth value it represents as follows:

$$ \tag{4} u = \frac{d}{2^k-1}. $$

Plugging \\(u\\) instead of \\(z\\) into Equation (3) allows us to map the values stored within the depth buffer to the original linear depth values they represent.

{{< figure src="/img/fixed_point_1.png" >}}

The graph looks like a bad joke, but it is very much real. Almost all depth values are allocated for objects close to the viewer. But that is no surprise - that is basically how the graph of \\(1/z\\) looks like, and a fixed-point depth buffer representation does absolutely nothing to counter this behavior.

Plotting the same graph on a logarithmic scale allows us to take a better look:

{{< figure src="/img/fixed_point_2.png" >}}

We can see that more than 3.5 billion depth values are used for objects less than 1 meter away from the viewer.

Since we are interested in *precision* of the depth buffer, we should find a way to plot its *resolution*. By resolution, we mean the smallest linear depth difference that results in a different binary value of \\(z\\). In other words, for a certain decimal number \\(d\\), it is the absolute difference between the linear depth value \\(d\\) corresponds to and the linear depth value of its immediate neighbor.

Mathematically, since (i) \\(w\\) is an continuous function of \\(z\\) and (ii) \\(u\\) is an continuous function of \\(d\\) and (iii) their domains suitably match, we can obtain the desired graph by plotting the absolute value of the derivative \\(\partial w(u) / \partial d\\).

{{< figure src="/img/fixed_point_3.png" >}}

Unfortunately, the resulting graph is somewhat hard to read. While we can tell that the resolution at the far plane is around 1 decimeter, and that it is well below 1 nanometer at the near plane, it is rather difficult to tell what is going on in-between without doing many back-and-forth comparisons between the previous graph and the current one.

We can overcome this predicament by (literally) converting the graph above into a large list of pairs \\(\lbrace d, \partial w / \partial d \rbrace\\), and then, for each one, replacing the decimal number \\(d\\) with the corresponding value of \\(w\\), and plotting these pairs as points. Because of the \\(z\\)-reversal, the new graph will appear transposed. You can ignore the spotty appearance of the graph - it is an artifact of plotting only a small subset of 4 billion depth values.

{{< figure src="/img/fixed_point_4.png" >}}

After switching the the abscissa to \\(w\\), it is immediately clear that, for example, we can expect sub-millimeter resolution at distances smaller than 500 meters.

## Floating-Point Depth Buffer

We proceed in a similar fashion by defining a function that interprets a decimal number as a binary representation of a floating-point number. In lieu of *reinterpret_cast*, this function can be defined as

$$ \tag{5} f = s_f (1 + t_f) 2^{e_f-b}, $$

where

$$ \tag{6} \begin{aligned}
	s\_f &= 1 - 2 \left\lfloor 2^{1-k} d \right\rfloor, \cr
	e\_f &= \left\lfloor 2^{-t} d \right\rfloor - 2^{k-t-1} \left\lfloor 2^{1-k} d \right\rfloor, \cr
	t\_f &= \sum_{i=0}^{t-1} 2^{i-t} \left(\left\lfloor 2^{-i} d \right\rfloor -2 \left\lfloor 2^{-i-1} d \right\rfloor \right),
\end{aligned}
$$

are the sign, the biased exponent and the trailing significand, respectively. The [IEEE standard](https://ieeexplore.ieee.org/document/8766229) defines \\(k=32,t=23,b=127\\) as encoding parameters for 32-bit floating-point numbers. Additionally, we should keep in mind that the values of \\(z\\) are restricted to the unit interval, so, assuming denormals are flushed to zero, \\(d\\) can either be zero or take on values in the range \\([8388608, 1065353216]\\). That is fewer than a quarter of the number of values utilized by a 32-bit fixed-point depth buffer. On the other hand, that puts a well-defined upper bound on potential precision improvements that could result from expanding the range of values of \\(z\\).

{{< figure src="/img/floating_point_1.png" >}}

As you can tell, the first billion values of \\(f\\) are very small. Therefore, plotting it on a logarithmic scale is more appropriate.

{{< figure src="/img/floating_point_2.png" >}}

The quasi-exponential nature of the function can be clearly observed if we zoom in.

{{< figure src="/img/floating_point_3.png" >}}

Substitution of \\(f\\) for \\(z\\) in Equation (3) allows us to plot linear depth as a function of \\(d\\).

{{< figure src="/img/floating_point_4.png" >}}

You may find the graph surprising ([I certainly did](https://twitter.com/zalbard/status/1179532228885237760)). We only see an exponential distribution of \\(w\\) appear after around 900 million values. To give you some idea of numbers on the horizontal line, \\(f(7 \times 10^8) \approx 8.22242 \times 10^{-14}\\) and \\(w(f(7 \times 10^8)) \approx 9999.9999\\). So, in fact, the *actual* useful range of decimal values is even smaller than originally anticipated.

What about precision, then? Since \\(f\\) is constant between (and discontinuous at) subsequent decimal values, we cannot really use the standard methods of real analysis and simply take the derivative. But since we are just interested in the difference between linear depth values for successive decimal numbers, we can approximate \\(\partial w(f) / \partial d\\) using [finite differences](https://en.wikipedia.org/wiki/Finite_difference) (which operate exactly that way).

{{< figure src="/img/floating_point_5.png" >}}

Do you recall piecewise-linear segments of \\(f\\) we saw earlier? The derivative of a linear function is a constant, so that is exactly what we see on the left side of the graph (\\(1/z\\) essentially "flatlines" very quickly). We have an absurdly high level of precision at the far plane, but, most importantly, the resolution stays fairly high throughout the whole range.

As before, we can swap the \\(d\\)-axis for the \\(w\\)-axis.

{{< figure src="/img/floating_point_6.png" >}}

This really puts things into perspective. First, we have sub-millimeter resolution across the entire depth range. More interestingly, it remains approximately constant, except for the regions next to the near and far planes, where we allocate most of the available bits. If we consider only the last 10% of the values of \\(d\\), \\([900000000, 1065353216]\\), we see that they cover 90% of the viewing range.

{{< figure src="/img/floating_point_7.png" >}}

With so much precision available for the region near the far plane, it is natural to wonder how the distance to the far plane affects the resolution. Can we push it infinitely far away "for free"?

{{< figure src="/img/floating_point_8.png" >}}

Turns out is that, up to around 1000-2000 meters, precision is barely affected. As we get farther away from the camera, precision no longer remains constant, and starts to drop.

{{< figure src="/img/floating_point_9.png" >}}

Considering the expanded viewing range, that is a perfectly reasonable trade-off.

## Conclusion

For a reasonable viewing range, a reversed floating-point depth buffer will offer sufficient precision (sub-millimeter throughout the entire range) for most applications. Objects close to the camera will enjoy up to two orders-of-magnitude higher depth resolution. Using an infinite far plane maintains precision up to a considerable distance from the camera (around 1000-2000 meters from my testing), after which point the depth resolution degrades. Assuming an LOD system is in place, and certain care is exercised when building the scene, this should not pose a problem.

A big caveat is that this article *only* considers depth buffer resolution. All the math has been done using infinite-precision arithmetic. There is no transformation pipeline, no rounding error, and no interpolation. These are the things that will likely cause more issues in practice.