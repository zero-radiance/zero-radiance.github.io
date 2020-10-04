---
title: "Origin of the Collision Coefficients"
date: 2020-10-04T15:08:10-07:00
draft: true
---

We have previously discussed the [radiative transfer equation](/post/analytic-media/) [[1](#references)] and its connection to [light scattered by individual particles](/post/particle-volume/) [[2](#references)] in some detail. If the distribution of particles is known, once we obtain the efficiencies (and the phase function) of the individual particles, we can readily compute the volume collision coefficients that determine the outcome of the radiative transfer process. However, so far, little has been said about the nature and the origin of these coefficients.

To give an example, consider a problem of atmospheric radiative transfer. We may wish to simulate a clear blue sky created by Earth's atmosphere. What should our coefficients be set to?

"The sky is blue, so the RGB color coefficient should be blue", you may say. While the answer is not completely wrong, unfortunately, it leaves the problem underspecified. For one, we have not one, but several collision coefficients - absorption, scattering and attenuation (or extinction). Secondly, we can represent them (as a set) in several different ways. We may decide to use an "artist-friendly" parametrization, such as the combination of the single-scattering albedo and the mean free path. Unfortunately, using a blue albedo and an (arbitrarily) large mean free path will not produce the expected result, particularly at sunset.

While we may spend more time and energy trying various different combinations of parameters to determine what works and what doesn't, there is another way. A more fun way. The scientific way.

<!--more-->


## Acknowledgments

Wenzel Jakob

## References

1. Golubev E., [Sampling Analytic Participating Media](/post/analytic-media/), 2020.
2. Golubev E., [From Particle to Volume Scattering](/post/particle-volume/), 2020.
