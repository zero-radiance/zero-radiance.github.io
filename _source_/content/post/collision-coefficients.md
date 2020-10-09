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

We have previously discussed the [radiative transfer equation](/post/analytic-media/) [[1](#references), [2](#references)] and its connection to [light scattered by individual particles](/post/particle-volume/) [[3](#references), [4](#references)] in some detail. If the distribution of particles is known, once we obtain the efficiencies (and the phase function) of the individual particles, we can readily compute the volume collision coefficients (and the aggregate phase function) that determine the outcome of the radiative transfer process. However, so far, little has been said about the nature and the origin of these coefficients.

To give an example, consider the following problem of atmospheric radiative transfer. We wish to simulate a clear blue sky created by Earth's atmosphere. What should our coefficients be set to?

"The sky is blue, so the RGB color coefficient must be blue as well", you may say. While the answer is not completely wrong, unfortunately, it leaves the problem underspecified. For one, we have not one, but several collision coefficients - absorption, scattering and attenuation (or extinction). Secondly, we can represent them (as a set) in several different ways. We may decide to use an "artist-friendly" parametrization, such as the combination of the single-scattering albedo and the mean free path. Unfortunately, using a blue albedo and an (arbitrarily) large mean free path will not produce the expected result, particularly at sunset.

While we may spend more time and energy trying various different combinations of parameters to determine what works and what doesn't, there is another way. A more fun way. The scientific way.

<!--more-->

The method can be briefly outlined as follows:

1. We describe radiation incident on the particle using [Maxwell's wave theory](https://www.feynmanlectures.caltech.edu/I_28.html) of light [[5](#references) (vol. I, ch. 28)].
2. We describe the shape, the size and the composition of the particle.
3. The incident wave interacts with the particle, producing a scattered wave.
4. Given the description of the incident and the scattered waves, we can compute the scattering and extinction efficiencies.

Since we will be treating light as a [transverse](https://vivadifferences.com/similarities-differences-between-longitudinal-and-transverse-waves/) wave [[6](#references)], we must first make a brief acquaintance with the phenomenon of polarization.

## Polarization of Light





## Acknowledgments

Wenzel Jakob

## References

<!--- APA style, with the edition and the (original publication) date at the end -->

1. Golubev, E. [Sampling analytic participating media](/post/analytic-media/). (2020).
2. Chandrasekhar, S. [Radiative transfer](https://doi.org/10.1002/qj.49707633016). (1950).
3. Golubev, E. [From particle to volume scattering](/post/particle-volume/). (2020).
4. van de Hulst, H. C. [Light scattering by small particles](https://doi.org/10.1002/qj.49708436025). (1957).
5. Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman lectures on physics](https://www.feynmanlectures.caltech.edu/). (1963).
6. VivaDifferences. [Similarities & differences between longitudinal and transverse waves](https://vivadifferences.com/similarities-differences-between-longitudinal-and-transverse-waves/). (2019).
7. Born, M., & Wolf, E. [Principles of optics](https://doi.org/10.1017/CBO9781139644181), 7th edition. (1999).
