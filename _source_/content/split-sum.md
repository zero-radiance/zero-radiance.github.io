---
title: "Alternative Take on the Split Sum Approximation for Cubemap Pre-filtering"
date: 2019-03-17
categories: [ "Graphics", "Math" ]
tags: [
	"Cubemap Pre-filtering",
	"Split Sum Approximation",
	"IBL",
	]
---

Pre-filtered cubemaps remain an important source of indirect illumination for those of us who still haven't purchased a Turing graphics card.

To my knowledge, most implementations use the [split sum approximation](https://cdn2.unrealengine.com/Resources/files/2013SiggraphPresentationsNotes-26915738.pdf) originally introduced by Brian Karis. It's a simple technique, and generally works well given the inherent view independence limitation.

But why does it work so well, and is there a better way to pre-filter? Let's find out.  <!--more-->

To compute the outgoing radiance in the view direction, we need to evaluate the integral of the incoming radiance weighted by the BRDF over the projected hemisphere.

$$ L\_o = \int\_{\Omega} L\_i(l) f(\alpha,v,n,l) \langle n,l \rangle dl $$

Brian's solution is to split the integral in two:

$$ L\_o \approx \int\_{\Omega} L\_i(l) \langle n,l \rangle dl \int\_{\Omega} f(\alpha,v,n,l) \langle n,l \rangle dl $$

The first integral is stored in the pre-filtered cubemap, and is the biggest source of error, since it does not account for the view direction. The second integral can be precomputed and reconstructed exactly at runtime.
In practice, during the cubemap pre-filtering step, Brian suggests to place samples according to the GGX distribution, which [may be surprising](https://seblagarde.files.wordpress.com/2015/07/course_notes_moving_frostbite_to_pbr_v32.pdf) given the absence of the BRDF within the integral.

We can do things slightly differently. What follows is the implementation we've been using in Unity's HD Render Pipeline for a couple of years.

First, we note that we can compute the exact value of the second integral, so it makes perfect sense to keep it. Therefore, we can start with the following expression:

$$ L\_o = L\_p(\alpha,v,n) \int\_{\Omega} f(\alpha,v,n,l) \langle n,l \rangle dl $$

After substituting into the first equation and solving for _L<sub>p</sub>_, we arrive at the following expression:

$$ L\_o = \frac{ \int\_{\Omega} L\_i(l) f(\alpha,v,n,l) \langle n,l \rangle dl }{ \int\_{\Omega} f(\alpha,v,n,l) \langle n,l \rangle dl} \int\_{\Omega} f(\alpha,v,n,l) \langle n,l \rangle dl $$

In this form, the IBL solution is exact. In practice, since we are memory-limited, we retain Brian's approximation by assuming that the normal and the view vectors are aligned. This results in a normalized radially symmetric filter around the normal.

$$ L\_p(\alpha,v,n) \approx \frac{ \int\_{\Omega} L\_i(l) f(\alpha,n,n,l) \langle n,l \rangle dl }{ \int\_{\Omega} f(\alpha,n,n,l) \langle n,l \rangle dl} $$

It is now more clear why the pre-filtering step requires sampling according to the BRDF.

We still solve the integral using Monte-Carlo. After importance sampling and simplification, we arrive at the following expression:

$$ L\_p(\alpha,v,n) \approx \frac{ \sum L_i(l) F(\langle h,l \rangle) G(\alpha,n,l) }{ \sum F(\langle h,l \rangle) G(\alpha,n,l) } = \frac{ \sum L_i(l) W }{ \sum W } $$

The only remaining snag is the Fresnel term, which depends both on the reflectance and the half-angle. Using

$$ F \approx const $$

will cause it to cancel out. I tried using the full Fresnel-Schlick expression with different reflectance values, and observed no visible differences in practice. To be honest, I am not sure why.

Finally, does the new method produce superior results in practice? The only difference is the weighting function, which in Brian's case is further approximated by the following expression:

$$ W = FG \approx \langle n,l \rangle $$

I did some testing, and the results are pretty similar. You may notice a slight difference in saturation at the bottom of the sphere, but there's nothing groundbreaking.

{{< figure src="/img/split_sum.png" alt="Comparison of two techniques" >}}

It is possible to further improve the quality of the results by pre-filtering using MIS, or performing more intelligent runtime filtering of the pre-filtered cubemap at runtime. But that's that's another story for another time.
