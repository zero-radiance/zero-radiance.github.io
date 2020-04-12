---
title: "From Particle to Volume Scattering"
date: 2020-04-10
categories: [ "Optics" ]
tags: [
    "Volume Rendering",
    "Radiometry"
    ]
---

I have [previously](https://zero-radiance.github.io/post/analytic-media/) covered the basics of the volume scattering process. It describes the macroscopic interaction of light and bulk matter, with the latter represented as a distribution of particles. Fundamentally, the volume scattering process arises from the microscopic interaction of light with individual molecules, typically described using [Maxwell's electromagnetic theory](https://www.osapublishing.org/josaa/abstract.cfm?URI=josaa-35-1-163). It is the domain of optics, and, in my experience, the connection with the [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer) is not apparent (and is somewhat difficult to discover). The purpose of this blog post is to familiarize the reader with domain-specific terms and ease the adoption of formulas found in the optics literature.

<!--more-->

Consider an incident electromagnetic wave represented by the electric field \\(\bm{E}\\) and the magnetic field \\(\bm{B}\\).
The direction of propagation and the amount of power per unit area carried by the wave at the time \\(t\\) is given by the [instanteneous Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Formulation_in_terms_of_microscopic_fields) \\(\bm{S}\\)

$$ \tag{1} \bm{S}(t) = \varepsilon_0 c^2 \Big( \bm{E}(t) \times \bm{B}(t) \Big), $$

where \\(\varepsilon_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Permittivity) of the medium and \\(c\\) is the speed of light.

Since electromagnetic waves oscillate very rapidly, we are interested in the [time-averaged Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Time-averaged_Poynting_vector) \\(\langle \bm{S} \rangle\\)

$$ \tag{2} \langle \bm{S} \rangle = \frac{1}{T} \int\_{0}^{T} \bm{S}(t) dt, $$

which can be used to define the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E\\)

$$ \tag{3} E = \Vert \langle \bm{S} \rangle \Vert. $$

For a [linearly-polarized](https://en.wikipedia.org/wiki/Linear_polarization) [plane wave](https://en.wikipedia.org/wiki/Plane_wave), the expression is particularly [simple](https://en.wikipedia.org/wiki/Irradiance#Property):

$$ \tag{4} E = \frac{1}{2} \varepsilon_0 \eta c E_0^2, $$

where \\(\eta\\) is the [index of refraction](https://en.wikipedia.org/wiki/Refractive_index) of the surrounding medium.

The interaction of light with an individual particle is described in terms of the [differential scattering cross section](http://glossary.ametsoc.org/wiki/Differential_(scattering)_cross_section) \\(\sigma_s'\\). It is defined as the ratio of the [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(I_s\\) scattered in a given direction \\(\omega_s\\) to the incident spectral irradiance \\(E_i\\):

$$ \tag{5} \sigma_s'(\omega_s, \omega_i) = \frac{d \sigma_s}{d \omega_s} = \frac{I_s}{E_i}. $$

Note the implicit dependence on the orientation of the particle with respect to the incident wave - it comes into play for scattering from anisotropic particles.

We can obtain the scattering cross section by integration over 4π steradians:

$$ \tag{6} \sigma_s(\omega_i) = \frac{\int\_{4 \pi} I_s d \omega_s}{E_i} = \frac{\Phi_s}{E_i}, $$

which is just the ratio of the scattered [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\Phi_s\\) to the incident spectral irradiance \\(E\\).

The angular distribution of scattered light is described by the [phase function](http://glossary.ametsoc.org/wiki/Phase_function) \\(f_p\\):

$$ \tag{7} f_p(\omega_s, \omega_i) = \frac{I_s}{\frac{1}{4 \pi} \int\_{4 \pi} I_s d \omega_s}
                                   = \frac{I_s}{\frac{1}{4 \pi} \Phi_s}. $$

It is the ratio of the ratio of the energy scattered per unit solid angle in a given direction to the average energy scattered per unit solid angle in all directions. Again, there is an implicit dependence on the orientation of the particle with respect to the incident wave. Note that  the integral of the phase function over 4π steradians equals 4π, which appears to be a common [convention](http://glossary.ametsoc.org/wiki/Phase_function) in optics.

It's instructive to find the product of the scattering cross section and the phase function:

$$ \tag{8} \sigma_s(\omega_i) f_p(\omega_s, \omega_i) = \frac{\Phi_s}{E_i} \frac{I_s}{\frac{1}{4 \pi} \Phi_s} = \frac{I_s}{\frac{1}{4 \pi} E_i}. $$

The spectral flux cancels out, and we find the relation with the differential scattering cross section:

$$ \tag{9} \sigma_s'(\omega_s, \omega_i) = \sigma_s \frac{f_p}{4 \pi}. $$



