---
title: "Sampling Burley's Normalized Diffusion Profiles"
date: 2019-12-16
categories: [ "Graphics", "Math" ]
tags: [
    "Importance Sampling",
    "Subsurface Scattering",
    "Diffusion",
    ]
---

A couple of years ago, I worked on an [implementation](http://advances.realtimerendering.com/s2018/Efficient%20screen%20space%20subsurface%20scattering%20Siggraph%202018.pdf) of Burley's Normalized Diffusion (a.k.a. Disney SSS). The original [paper](https://graphics.pixar.com/library/ApproxBSSRDF/paper.pdf) claims that the CDF is not analytically invertible. I have great respect for both authors, Brent Burley and Per Christensen, so I haven't questioned their claim for a second. Turns out, "[Question Everything](https://www.psychologytoday.com/us/blog/connect-creativity/201311/question-everything-everywhere-forever)" is probably a better mindset.

I've been recently alerted by [@stirners_ghost](https://twitter.com/stirners_ghost) on Twitter (thank you!) that the CDF is analytically invertible. In fact, the inversion process is almost trivial, as I will demonstrate below.

<!--more-->

The diffusion model is radially symmetric, and is defined as

$$ \tag{1} R(r) = \frac{A s}{8 \pi r} \Big( e^{-s r} + e^{-s r / 3} \Big). $$

The PDF must be normalized:

$$ \tag{2}
    \int\_{-\infty}^{\infty} \int\_{-\infty}^{\infty} \frac{R(\sqrt{x^2+y^2})}{A} dx dy =
    \int\_{0}^{2 \pi} \int\_{0}^{\infty} \frac{R(r)}{A} r dr d\theta =
    \int\_{0}^{2 \pi} \int\_{0}^{\infty} p_r(r, \theta) dr d\theta = 1.
$$

Therefore, in polar coordinates, the PDF is given as

$$ \tag{3} p_r(r, \theta) = \frac{R(r)}{A} r = \frac{s}{8 \pi} \Big( e^{-s r} + e^{-s r / 3} \Big). $$

The marginal CDF is then

$$ \tag{4} P(r) = \int\_{0}^{2 \pi} \int_{0}^{r} p_r(z, \theta) dz d\theta = 1 - \frac{1}{4} e^{-s r} - \frac{3}{4} e^{-s r / 3}. $$

Let us define \\(x = s r\\). If we ask [Mathematica](https://www.wolfram.com/mathematica/) to solve \\(y = P(x)\\) for \\(x\\) in order to obtain the inverse \\(x = P^{-1}(y)\\), we get a somewhat terrifying (but, of course, correct) output:

{{< figure src="/img/solve1.png">}}

The trick is to solve for the complimentary CDF \\(u = 1 - y\\) instead:

{{< figure src="/img/solve2.png">}}

To get rid of the imaginary part, we can set the free constant \\(c_{1} = 0\\), which results in the following inverse:

$$ \tag{5} x = s r = 3 \log{\Bigg(\frac{1 + G(u)^{-1/3} + G(u)^{1/3}}{4 u} \Bigg)}, $$

where

$$ \tag{6} G(u) = 1 + 4 u \Big( 2 u + \sqrt{1 + 4 u^2} \Big). $$

We can verify that it works by feeding the CCDF with its inverse.

{{< figure src="/img/solve3.png">}}

We can uniformly sample either the complementary or the regular CDF - it makes no difference.

## Acknowledgments

I would like to thank [@stirners_ghost](https://twitter.com/stirners_ghost) for informing me that the CDF is invertible, and Brent Burley for spotting a missing minus sign in the derivation.