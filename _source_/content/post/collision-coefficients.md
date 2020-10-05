---
title: "Origin of the Collision Coefficients"
date: 2020-10-04
tags: [
    "Collision Coefficients",
    "Light Scattering",
    "Optics",
    ]
draft: true
---

We have previously discussed the [radiative transfer equation](/post/analytic-media/) [[1](#references)] and its connection to [light scattered by individual particles](/post/particle-volume/) [[2](#references)] in some detail. If the distribution of particles is known, once we obtain the efficiencies (and the phase function) of the individual particles, we can readily compute the volume collision coefficients that determine the outcome of the radiative transfer process. However, so far, little has been said about the nature and the origin of these coefficients.

To give an example, consider a problem of atmospheric radiative transfer. We may wish to simulate a clear blue sky created by Earth's atmosphere. What should our coefficients be set to?

"The sky is blue, so the RGB color coefficient should be blue", you may say. While the answer is not completely wrong, unfortunately, it leaves the problem underspecified. For one, we have not one, but several collision coefficients - absorption, scattering and attenuation (or extinction). Secondly, we can represent them (as a set) in several different ways. We may decide to use an "artist-friendly" parametrization, such as the combination of the single-scattering albedo and the mean free path. Unfortunately, using a blue albedo and an (arbitrarily) large mean free path will not produce the expected result, particularly at sunset.

While we may spend more time and energy trying various different combinations of parameters to determine what works and what doesn't, there is another way. A more fun way. The scientific way.

<!--more-->

The method can be briefly outlined as follows:

1. We describe radiation incident on the particle using [Maxwell's wave theory](https://www.feynmanlectures.caltech.edu/I_28.html) of light [[3](#references)].
2. We describe the shape, the size and the composition of the particle.
3. The incident wave interacts with the particle, producing a scattered wave.
4. Given the description of the incident and the scattered waves, we can compute scattering and extinction efficiencies.

Since we will be treating light as a [transverse](https://vivadifferences.com/similarities-differences-between-longitudinal-and-transverse-waves/) electromagnetic wave [[4](#references)], we must first make a brief acquaintance with the phenomenon of polarization.

## Polarization of Light





## Acknowledgments

Wenzel Jakob

## References

1. Golubev E., [Sampling Analytic Participating Media](/post/analytic-media/), 2020.
2. Golubev E., [From Particle to Volume Scattering](/post/particle-volume/), 2020.
3. Feynman R. and Leighton R. and Sands M., [The Feynman Lectures on Physics, Volume I](https://www.feynmanlectures.caltech.edu/I_toc.html), 1963.
4. VivaDifferences, [What Are Similarities & Differences Between Longitudinal And Transverse Waves?](https://vivadifferences.com/similarities-differences-between-longitudinal-and-transverse-waves/), 2019.