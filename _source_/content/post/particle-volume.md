---
title: "From Particle to Volume Scattering"
date: 2020-04-10
categories: [ "Optics" ]
tags: [
    "Volume Rendering",
    "Radiometry"
    ]
---

I have [previously](https://zero-radiance.github.io/post/analytic-media/) covered the basics of the volume scattering process. It describes the macroscopic interaction of light and matter, with the latter represented by a distribution of particles. Ultimately, the volume scattering process arises from the microscopic interaction of light with individual molecules, typically described using [Maxwell's electromagnetic theory](https://www.osapublishing.org/josaa/abstract.cfm?URI=josaa-35-1-163). It is the domain of the optics literature, and, in my experience, the connection with the [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer) is not apparent (and somewhat difficult to discover). The purpose of this blog post is to familiarize the reader with domain-specific terms and ease the adoption of formulas found in the optics literature.

<!--more-->

Consider an incident electromagnetic wave represented by the electric field \\(\bm{E_i}\\) and the magnetic field \\(\bm{B_i}\\).
The amount of power carried by the wave per unit area at the time \\(t\\) is given by the magnitude of the [instanteneous Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Formulation_in_terms_of_microscopic_fields) \\(\bm{S}\\)

$$ \tag{1} \bm{S}(\lambda, t) = \varepsilon c^2 \Big( \bm{E}(\lambda, t) \times \bm{B}(\lambda, t) \Big), $$

where \\(\varepsilon\\) is the [absolute permittivity](https://en.wikipedia.org/wiki/Permittivity) of the medium and \\(c\\) is the speed of light.

Since electromagnetic waves oscillate very rapidly, we are interested in the [time-averaged Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Time-averaged_Poynting_vector) \\(\langle \bm{S} \rangle\\)

$$ \tag{2} \langle \bm{S}(\lambda) \rangle = \frac{1}{T} \int_{0}^{T} \bm{S}(\lambda, t) dt, $$

which is used to define the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E(\lambda)\\)

$$ \tag{3} E(\lambda) = \Vert \langle \bm{S}(\lambda) \rangle \Vert. $$