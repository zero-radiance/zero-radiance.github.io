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

To find the corresponding PDF, it must be normalized:

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

The trick is to solve \\(P(x) = 1 - u\\) instead (where \\(u = 1 - P(x)\\) is the [complementary CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function#Complementary_cumulative_distribution_function_(tail_distribution))):

{{< figure src="/img/solve2.png">}}

To get rid of the imaginary part, we can set the free constant \\(c_{1} = 0\\), which results in the following inverse:

$$ \tag{5} x = s r = 3 \log{\Bigg(\frac{1 + G(u)^{-1/3} + G(u)^{1/3}}{4 u} \Bigg)}, $$

where

$$ \tag{6} G(u) = 1 + 4 u \Big( 2 u + \sqrt{1 + 4 u^2} \Big). $$

We can verify that it works by feeding the complementary CDF with its inverse.

{{< figure src="/img/solve3.png">}}

For importance sampling, we can uniformly sample either the complementary or the regular CDF - it makes no difference \\((\\)except for reversing the order of samples, s.t. \\(r(0) = \infty\\) and \\(r(1) = 0 )\\).

Optimized implementation is listed below.

```c++
// Performs sampling of a Normalized Burley diffusion profile in polar coordinates.
// 'u' is the random number (the value of the CDF): [0, 1).
// rcp(S) = 1 / ShapeParam = ScatteringDistance.
// 'r' is the sampled radial distance.
// rcp(Pdf) is the reciprocal of the corresponding PDF value.
void SampleBurleyDiffusionProfile(float u, float rcpS, out float r, out float rcpPdf)
{
    u = 1 - u; // Convert cCDF to CDF s.t. (r(0) = 0) and (r(1) = Inf)

    // assert(0 <= u && u < 1);

    float g = 1 + (4 * u) * (2 * u + sqrt(1 + (4 * u) * u));
    float n = exp2(log2(g) * (-1.0/3.0));                    // g^(-1/3)
    float p = (g * n) * n;                                   // g^(+1/3)
    float c = 1 + p + n;                                     // 1 + g^(+1/3) + g^(-1/3)
    float d = (3 / LOG2_E * 2) + (3 / LOG2_E) * log2(u);     // 3 * Log[4 * u]
    float x = (3 / LOG2_E) * log2(c) - d;                    // 3 * Log[c / (4 * u)]

    // x      = S * r
    // exp_13 = Exp[-x/3] = Exp[-1/3 * 3 * Log[c / (4 * u)]]
    // exp_13 = Exp[-Log[c / (4 * u)]] = (4 * u) / c
    // exp_1  = Exp[-x] = exp_13 * exp_13 * exp_13
    // expSum = exp_1 + exp_13 = exp_13 * (1 + exp_13 + exp_13)
    // rcpExp = rcp(expSum) = c^3 / ((4 * u) * (c^2 + 16 * u^2))
    float rcpExp = ((c * c) * c) * rcp((4 * u) * ((c * c) + (4 * u) * (4 * u)));

    r      = x * rcpS;
    rcpPdf = (8 * PI * rcpS) * rcpExp; // (8 * Pi) / S / (Exp[-S * r / 3] + Exp[-S * r])
}
```

## Acknowledgments

I would like to thank [@stirners_ghost](https://twitter.com/stirners_ghost) for informing me that the CDF is invertible, and Brent Burley for spotting a missing minus sign in the derivation.