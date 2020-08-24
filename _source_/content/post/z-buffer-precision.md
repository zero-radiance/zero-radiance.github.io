---
title: "Quantitative Analysis of Z-buffer Precision"
date: 2020-08-15T12:22:10-07:00
categories: [ "Graphics", "Math" ]
tags: [
    "Analysis",
    "Z-buffer",
    "Precision",
    ]
draft: true
---

High Z-buffer precision is something that we take for granted these days. Since the introduction of [reversed floating-point Z-buffering](https://doi.org/10.1145/311534.311579) (and, assuming you use the standard tricks like [camera-relative transforms](https://pharr.org/matt/blog/2018/03/02/rendering-in-camera-space.html)), most depth precision issues are a thing of the past. You ask the the rasterizer to throw triangles at the screen and, almost magically, and they appear at the right place and in the right order.

There are many existing articles concerning Z-buffer precision ([1](https://doi.org/10.1145/311534.311579), [2](https://mynameismjp.wordpress.com/2010/03/22/attack-of-the-depth-buffer/), [3](http://www.geometry.caltech.edu/pubs/UD12.pdf), [4](http://www.humus.name/Articles/Persson_CreatingVastGameWorlds.pdf), [5](https://outerra.blogspot.com/2012/11/maximizing-depth-buffer-range-and.html), [6](http://dev.theomader.com/depth-precision/), [7](http://www.reedbeta.com/blog/depth-precision-visualized/), the last one being my favorite), as well as sections in the [Real-Time Rendering](http://www.realtimerendering.com/book.html) and the [Foundations of Game Engine Development](https://foundationsofgameenginedev.com/#fged2) books. So, why bother writing another one? While there is nothing wrong with the intuition and the results presented there, I find the numerical analysis part (specifically, its presentation) somewhat lacking. It is clear that "the quasi-logarithmic distribution of floating-point somewhat cancels the \\(1/z\\) nonlinearity", but what does that mean *exactly*? What is happening at the binary level? Is the resulting distribution of depth values linear? Logarithmic? Is it something else entirely? And what about the depth resolution? Even after reading all these articles, I still had these nagging questions that made me feel that I had failed to achieve what Jim Blinn would call the [ultimate understanding](https://doi.org/10.1109/38.210494) of the concept.

In short, we know that reversed Z-buffering *is* good, and we know *why* it is good; what we would like to know is *what good really means*. This blog post is not meant to replace the existing articles but, rather, to complement them.

<!--more-->

With that in mind, the goals of this article are:

1. Consider a typical case using actual 32-bit floating-point depth values, as well as a realistic depth range.
2. Analyze numerical results and draw conclusions.
3. Show all steps of the analysis in detail (get my Mathematica script [here](here)).

If that is something that interests you, please read on.

## Reversed Z-Buffering Basics

We can perform a perspective transformation of a column vector representing a view-space point using the matrix

$$ \tag{1} P_r =
\begin{bmatrix}
    p/a & 0 & 0       & 0 \cr
    0   & p & 0       & 0 \cr
    0   & 0 & n/(n-f) & -n f / (n-f) \cr
    0   & 0 & 1       & 0
\end{bmatrix},
$$

where \\(a\\) is the aspect ratio of the sensor, and \\(n,f,p\\) are the distances to the near, far and projection planes, respectively. This particular projection matrix is [symmetric](http://www.songho.ca/opengl/gl_projectionmatrix.html), and does not have an [oblique near plane](http://www.terathon.com/lengyel/Lengyel-Oblique.pdf); these cases are less common, and they make analysis more complicated.

The resulting vector will have 4 components: \\([x',y',z',w]^{\mathrm{T}}\\). We can see that the \\(w\\) component is equal to the third coordinate of the original view-space point (the so-called "linear depth"), and we shall label it this way. We can obtain the value stored in the Z-buffer value by means of perspective division: \\(z=z'/w\\).

Stated more precisely,

$$ \tag{2} z(w) = \frac{n}{n-f} -\frac{1}{w} \frac{n f}{n-f}. $$

Then, either by inverting the projection matrix, or solving Equation (2) for \\(w\\), we can recover

$$ \tag{3} w(z) = \frac{n f}{n - z (n - f)}. $$

We shall examine a fairly typical open-world case of \\(n=0.1\\) and \\(f=10000\\) meters. First, let us plot \\(w\\) against \\(z\\).

{{< figure src="/img/z-buffer_basics_1.png" >}}

This behavior is typical for functions of \\(\mathrm{O}(1/z)\\): there is a strong peak as \\(z \to 0\\) and a long tail as \\(z \to 1\\).

Plotting the same graph on a logarithmic scale allows us to take a better look:

{{< figure src="/img/z-buffer_basics_2.png" >}}

## Fixed-Point Z-buffer

In order to become more familiar with the process of analysis, and to obtain a comparison point, let us consider a simple case using an unsigned, normalized, fixed-point Z-buffer. Assuming 24-bit binary storage \\((k=24)\\) that we interpret as a decimal number \\(d\\), we can compute the depth value it represents as follows:

$$ \tag{4} u(d) = \frac{d}{2^k-1} = \frac{d}{16777215}. $$

Plugging \\(u\\) instead of \\(z\\) into Equation (3) allows us to map the values stored within the Z-buffer to the original linear depth values they represent.

{{< figure src="/img/fixed_point_1.png" >}}

Since we obtain \\(u\\) by linearly transforming (scaling) \\(d\\), the graph looks identical to the one from the previous section. Unfortunately, the result is that over 90% of values of \\(d\\) are used for objects less than 1 meter away from the viewer.

Since we are interested in *precision* of the Z-buffer, we should find a way to plot its *resolution*. By resolution, we mean the smallest positive linear depth difference that results in a different binary value of \\(z\\). In other words, for a certain decimal number \\(d\\), it is the absolute difference \\(\Delta w\\) between the linear depth value \\(d\\) corresponds to and the linear depth value of its immediate neighbor.

Mathematically, for a continuous function, we can obtain the desired graph by plotting the absolute value of the derivative

$$ \tag{5} \Delta w(d) \approx \Bigg\lvert \frac{\partial w}{\partial d} \Bigg\rvert. $$

{{< figure src="/img/fixed_point_2.png" >}}

The graph looks remarkably similar to the previous one (except for the scale). But, in fact, it is not - the derivative of \\(\mathrm{O}(1/z)\\) is \\(\mathrm{O}(1/z^2)\\). The logarithmic scale we use for plotting can be a little misleading.

We can make the resulting graph easier to read by converting the \\(d\\)-axis to the \\(w\\)-axis. Intuitively, what we want to do is to replace the horizontal axis of the current graph with the vertical axis of the previous one. We can do that by (literally) converting the graph into a large list of pairs \\(\lbrace d, \Delta w \rbrace\\), and then, for each one, replacing the number \\(d\\) with the value of \\(w\\) it corresponds to, and plotting these pairs as points.

{{< figure src="/img/fixed_point_3.png" >}}

Because of the Z-reversal, the new graph appears transposed. And now it is more clear that, for instance, resolution falls below 1 meter at distances exceeding 1 kilometer.

## Floating-Point Z-buffer

We proceed in a similar fashion by defining a function that interprets a decimal number as a binary representation of a floating-point number. In lieu of *reinterpret_cast*, this function can be defined as

$$ \tag{6} f(d) = s_f (1 + t_f) 2^{e_f-b}, $$

where

$$ \tag{7} \begin{aligned}
	s\_f(d) &= 1 - 2 \left\lfloor 2^{1-k} d \right\rfloor, \cr
	e\_f(d) &= \left\lfloor 2^{-t} d \right\rfloor - 2^{k-t-1} \left\lfloor 2^{1-k} d \right\rfloor, \cr
	t\_f(d) &= \sum_{i=0}^{t-1} 2^{i-t} \left(\left\lfloor 2^{-i} d \right\rfloor -2 \left\lfloor 2^{-i-1} d \right\rfloor \right),
\end{aligned}
$$

are the sign, the biased exponent and the trailing significand, respectively. The [IEEE standard](https://ieeexplore.ieee.org/document/8766229) defines \\(k=32,t=23,b=127\\) as encoding parameters for 32-bit floating-point numbers. Additionally, we should keep in mind that the values of \\(z\\) are restricted to the unit interval, so, assuming denormals are flushed to zero, \\(d\\) can either be zero or take on values in the range \\([1 \times 2^{23}, 127 \times 2^{23}] = [8388608, 1065353216]\\). That is less than a quarter of all 32-bit decimal numbers, yet 63 times more than what a 24-bit Z-buffer can uniquely represent. Additionally, this puts a well-defined upper bound on potential precision improvements that could result from expanding the range of values of \\(z\\).

Plotting Equation 6 as a function of \\(d\\) reveals that the first billion values of \\(f\\) are very small.

{{< figure src="/img/floating_point_1.png" >}}

The quasi-exponential nature of the function can be clearly observed if we zoom in near \\(f = 1\\).

{{< figure src="/img/floating_point_2.png" >}}

Zooming in some more, we can see that the function we defined is, in fact, constant between subsequent values of \\(d\\).

{{< figure src="/img/floating_point_3.png" >}}

This is the limit of floating-point precision. All values that fall in-between must be rounded to machine-representable ones.

Substitution of \\(f\\) for \\(z\\) in Equation (3) allows us to plot linear depth against \\(d\\) (on a logarithmic scale).

{{< figure src="/img/floating_point_4.png" >}}

You may find the graph surprising ([I certainly did](https://twitter.com/zalbard/status/1179532228885237760)). We only see an exponential distribution of \\(w\\) appear after around 900 million values. To give you some idea of the numbers on the horizontal line, \\(f(7 \times 10^8) \approx 8.22242 \times 10^{-14}\\) and \\(w \big(f(7 \times 10^8)\big) \approx 9999.9999\\). If we consider only the last 10% of the values of \\(d\\), they cover 90% of the viewing range. So, in fact, the *actual* useful range of decimal values is even smaller than originally anticipated.

What about precision, then? Since \\(f\\) is constant between (and discontinuous at) subsequent decimal values, we cannot really use standard methods of real analysis and simply take the derivative. But since we are just interested in the difference between linear depth values for successive decimal numbers, we can approximate \\(\partial w / \partial d\\) using [finite differences](https://en.wikipedia.org/wiki/Finite_difference) which operate exactly that way:

$$ \tag{8} \Delta w(d) \approx \Bigg\lvert \frac{w(d+1) -w(d-1)}{2} \Bigg\rvert. $$

{{< figure src="/img/floating_point_5.png" >}}

Do you recall the quasi-piecewise-linear segments of \\(f\\) we saw earlier? The derivative of a linear function is a constant, so that is exactly what we see on the left side of the graph (where \\(w\\) is almost constant). We have an absurdly high level of precision at the far plane \\((d \to 0)\\), but, most importantly, the resolution stays fairly high throughout the whole range.

For completeness, we can zoom in near \\(f = 1\\) again (on a linear scale to facilitate comparison).

{{< figure src="/img/floating_point_6.png" >}}

At this point \\(w\\) is exponentially decreasing, so the interaction between \\(1/z\\) and the quasi-exponential floating-point behavior is more complex. Personally, I find the large gaps rather curious. If you have a theory, please feel free to share it with the rest of the community on Twitter.

As before, we can swap the \\(d\\)-axis for the \\(w\\)-axis.

{{< figure src="/img/floating_point_7.png" >}}

This really puts things into perspective (I encourage you to compare the graph of \\(\Delta w(d)\\) with this one). First, we have sub-millimeter resolution across the entire depth range. More interestingly, it remains approximately constant, except for the regions next to the near and far planes, where we allocate most of the available bits. In fact, inclusion of the far plane is skewing the graph so much that, if we only consider \\(w < 9000 \enspace (d > 0.9 \times 10^9)\\), it paints a rather different picture:

{{< figure src="/img/floating_point_8.png" >}}

With so much precision available for the region near the far plane, it is natural to wonder how the distance to the far plane affects the resolution. Can we push it [infinitely far away](http://www.terathon.com/gdc07_lengyel.pdf) "for free"?

{{< figure src="/img/floating_point_9.png" >}}

Turns out is that, up to around 1000-2000 meters, precision is barely affected. As we get farther away from the camera, precision no longer remains constant, and starts to drop. We can see that if we create a plot with both axes using a logarithmic scale.

{{< figure src="/img/floating_point_10.png" >}}

Considering the expanded viewing range, that is a perfectly reasonable trade-off.

You may wonder how it compares to a 32-bit fixed-point [logarithmic depth](http://tulrich.com/geekstuff/log_depth_buffer.txt) buffer, the gold standard of Z-buffering. By definition, it has constant relative precision, such that \\(\frac{\Delta w}{w} \in \mathrm{O}(1)\\). More intuitively, \\(\Delta w \in \mathrm{O}(w)\\), which implies that resolution scales linearly with distance.

Turns out, if we plot both (using \\(f = 10^{37}\\) to maintain the scale of the graph), they are surprisingly close.

{{< figure src="/img/floating_point_11.png" >}}

The floating-point Z-buffer is slightly behind in terms of resolution, which can be partially explained by the fact that it only uses around a quarter of available values of \\(d\\).

{{< figure src="/img/floating_point_12.png" >}}

Zooming in, we see that (in the limit as \\(f \to \infty\\)) the floating-point Z-buffer is an approximation of a logarithmic Z-buffer. It maintains the shape of the curve, but not its continuity. Nevertheless, it has a major performance advantage, since logarithmic depth [cannot be linearly interpolated](http://tulrich.com/geekstuff/log_depth_buffer.txt), which makes it less useful in practice.

## Conclusion

For a reasonable viewing range, a reversed floating-point Z-buffer will offer sufficient precision (sub-millimeter throughout the entire range) for most applications. Switching to an infinite far plane maintains the same precision for a considerable distance from the camera (around 1000-2000 meters from my testing), after which point the depth resolution degrades in a manner similar to a true logarithmic Z-buffer. Assuming an LOD system is in place, and certain care is exercised when building the scene, this should not pose a problem.

A big caveat is that this article *only* considers Z-buffer resolution. All the math has been done using infinite-precision arithmetic. There is no transformation pipeline, no rounding error, and no interpolation. These are the things that will likely cause more issues in practice.
