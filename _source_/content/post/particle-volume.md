---
title: "From Particle to Volume Scattering"
date: 2020-09-20
categories: [ "Optics" ]
tags: [
    "Volume Rendering",
    "Radiometry"
    ]
draft: true
---

I have [previously](https://zero-radiance.github.io/post/analytic-media/) covered the basics of the volume scattering process. It models the macroscopic interaction of light and matter, with the latter represented by a distribution of [small particles](https://doi.org/10.1002/qj.49708436025). Fundamentally, the volume scattering process arises from the microscopic interaction of light with individual atoms, and is typically described using [Maxwell's electromagnetic theory](https://doi.org/10.1364/JOSAA.35.000163). It is the domain of optics, and, in my experience, the connection with the [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer) is neither apparent nor easy to find in the literature. The purpose of this blog post is to familiarize the reader with domain-specific terms and ease the adoption of formulas found in the optics literature.

<!--more-->

## Particle Scattering Basics

Imagine a small particle of an arbitrary shape embedded in the host medium with the complex refractive index \\(\tilde{\eta}\_1 - i \tilde{\kappa}\_1\\) (by convention, \\(\eta_0 = 1\\) is reserved for the [vacuum](https://en.wikipedia.org/wiki/Vacuum)).

{{< figure src="/img/linear_plane_wave.png" caption="*Linearly-polarized plane electromagnetic wave. [Image source](https://openstax.org/books/university-physics-volume-2/pages/16-4-momentum-and-radiation-pressure).*">}}

Consider an [electromagnetic wave](https://www.cpp.edu/~alrudolph/classes/phy234/Reading/Summary%20of%20Waves.pdf) represented by the electric field \\(\bm{\tilde{E}}\\) and the magnetic field \\(\bm{\tilde{B}}\\) of a certain frequency and in a certain state of polarization. The direction of propagation and the amount of power per unit area carried by the wave at the time \\(t\\) is given by the [instanteneous Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Formulation_in_terms_of_microscopic_fields) \\(\bm{\tilde{S}}\\)

$$ \tag{1} \bm{\tilde{S}}(t) = \varepsilon_0 c^2 \Big( \bm{\tilde{E}}(t) \times \bm{\tilde{B}}(t) \Big), $$

where \\(\varepsilon_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Permittivity) and \\(c\\) is the speed of light.

Since light waves oscillate very rapidly, we are typically interested in the [time-averaged Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Time-averaged_Poynting_vector) \\(\langle \bm{\tilde{S}} \rangle\\)

$$ \tag{2} \langle \bm{\tilde{S}} \rangle = \frac{1}{T} \int\_{0}^{T} \bm{\tilde{S}}(t) dt, $$

which can be used to define the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(\tilde{E}\\) (do not confuse it with the electric field vector \\(\bm{\tilde{E}}\\))

$$ \tag{3} \tilde{E} = \vert \langle \bm{\tilde{S}} \rangle \vert. $$

For a [plane wave](https://en.wikipedia.org/wiki/Plane_wave), the expression is particularly [simple](https://en.wikipedia.org/wiki/Irradiance#Property):

$$ \tag{4} \tilde{E} = \frac{1}{2} \varepsilon_0 c \tilde{\eta}\_1 \vert \mathcal{\tilde{E}} \vert^2, $$

where \\(\mathcal{\tilde{E}}\\) is the [complex amplitude](https://en.wikipedia.org/wiki/Phasor) of the electric field vector (which encodes both the magnitude and the [phase](https://en.wikipedia.org/wiki/Phase_(waves))).

The interaction of light with an individual particle is described in terms of the [differential scattering cross section](http://glossary.ametsoc.org/wiki/Differential_(scattering)_cross_section) \\(\tilde{\sigma}\_s'\\). It is defined as the ratio of the scattered [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(\tilde{I}\_s\\) for a given direction \\(\bm{\hat{\omega}\_s}\\) to the incident spectral irradiance \\(\tilde{E}\_i\\) normal with respect to \\(\bm{\hat{\omega}\_i}\\):

$$ \tag{5}
    \tilde{\sigma}\_s'(\bm{\hat{\omega}\_i}, \bm{\hat{\omega}\_s}) =
    \frac{d \tilde{\sigma}\_s}{d \hat{\omega}\_s} =
    \frac{\tilde{I}\_s}{\tilde{E}\_i}.
$$

These two directions are typically specified in a local coordinate system of the particle. There is an implicit dependence on the orientation of the particle with respect to the incident wave - it comes into play if the particle is anisotropic.

We can obtain the scattering cross section \\(\tilde{\sigma}\_s\\) by integrating over \\(4 \pi\\) steradians:

$$ \tag{6}
    \tilde{\sigma}\_s(\bm{\hat{\omega}\_i}) =
    \int\_{4 \pi} \tilde{\sigma}\_s' d \hat{\omega}\_s =
    \frac{\int\_{4 \pi} \tilde{I}\_s d \hat{\omega}\_s}{\tilde{E}\_i} =
    \frac{\tilde{\Phi}\_s}{\tilde{E}\_i}. $$

In other words, it is just the ratio of the scattered [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\tilde{\Phi}\_s\\) to the incident spectral irradiance \\(\tilde{E}\_i\\).

The [attenuation (or extinction) cross section](http://glossary.ametsoc.org/wiki/Extinction_cross_section) \\(\tilde{\sigma}\_t\\) relates the total spectral flux \\(\tilde{\Phi}\_t\\) scattered or absorbed by the particle (e.i. removed from the incident wave) to the incident spectral irradiance \\(\tilde{E}\_i\\):

$$ \tag{7} \tilde{\sigma}\_t(\bm{\hat{\omega}\_i}) = \tilde{\sigma}\_s + \tilde{\sigma}\_a = \frac{\tilde{\Phi}\_s}{\tilde{E}\_i} + \frac{\tilde{\Phi}\_a}{\tilde{E}\_i} = \frac{\tilde{\Phi}\_t}{\tilde{E}\_i}. $$

The optical cross sections \\(\tilde{\sigma}\_x\\) are related to the geometric cross section \\(\tilde{\sigma}\_g\\) by the [efficiencies](https://doi.org/10.1364/JOSAA.35.000163) \\(\tilde{Q}\_x\\):

$$ \tag{8}
    \tilde{Q}\_a = \frac{\tilde{\sigma}\_a}{\tilde{\sigma}\_g} = \frac{\tilde{\Phi}\_a}{\tilde{\Phi}\_i}, \qquad
    \tilde{Q}\_s = \frac{\tilde{\sigma}\_s}{\tilde{\sigma}\_g} = \frac{\tilde{\Phi}\_s}{\tilde{\Phi}\_i}, \qquad
    \tilde{Q}\_t = \frac{\tilde{\sigma}\_t}{\tilde{\sigma}\_g} = \frac{\tilde{\Phi}\_t}{\tilde{\Phi}\_i}. $$

Note that the value of extinction efficiency can exceed 1. This phenomenon is called the [extinction paradox](https://doi.org/10.1016/j.jqsrt.2010.08.024).

The angular distribution of scattered light is described by the [phase function](http://glossary.ametsoc.org/wiki/Phase_function) \\(\tilde{f}\_p\\):

$$ \tag{9}
    \tilde{f}\_p(\bm{\hat{\omega}\_i}, \bm{\hat{\omega}\_s}) =
    \frac{\tilde{I}\_s}{\frac{1}{4 \pi} \int\_{4 \pi} \tilde{I}\_s d \hat{\omega}\_s} =
    \frac{\tilde{I}\_s}{\frac{1}{4 \pi} \tilde{\Phi}\_s}.
$$

It is the ratio of the energy per unit solid angle scattered in a given direction \\(\bm{\hat{\omega}\_s}\\) to the average energy per unit solid angle scattered in all directions. Again, there is an implicit dependence on the orientation of the particle with respect to the incident wave. Note that  the integral of the phase function over \\(4 \pi\\) steradians equals \\(4 \pi\\), which appears to be a common [convention](http://glossary.ametsoc.org/wiki/Phase_function) in optics.

Let's compute the product of the scattering cross section and the phase function:

$$ \tag{10}
    \tilde{\sigma}\_s(\bm{\hat{\omega}\_i}) \tilde{f}\_p(\bm{\hat{\omega}\_i}, \bm{\hat{\omega}\_s}) =
    \frac{\tilde{\Phi}\_s}{\tilde{E}\_i} \frac{\tilde{I}\_s}{\frac{1}{4 \pi} \tilde{\Phi}\_s}. $$

The spectral flux cancels out, and we find the connection with the differential scattering cross section:

$$ \tag{11} \tilde{\sigma}\_s'(\bm{\hat{\omega}\_i}, \bm{\hat{\omega}\_s}) = \tilde{\sigma}\_s \frac{\tilde{f}\_p}{4 \pi}. $$

## Scattering by a Group of Particles

Let us see how we can extend the theory to scattering from \\(N\\) particles. When an atom interacts with light, it experiences a [periodic perturbation of its electron cloud](http://plaza.ufl.edu/dwhahn/Rayleigh%20and%20Mie%20Light%20Scattering.pdf), which turns it into a source of radiation. Assuming [elastic scattering](https://en.wikipedia.org/wiki/Elastic_scattering) (ignoring the [Raman effect](https://en.wikipedia.org/wiki/Raman_scattering)), this radiation will have the same frequency as the incident light. Additionally, assuming that all the particles are identical (including their orientation), they will all radiate energy in exactly the same way.

The resulting electromagnetic waves will [combine](https://en.wikipedia.org/wiki/Superposition_principle#Wave_superposition) at the detector, potentially experiencing both constructive and destructive [interference](https://en.wikipedia.org/wiki/Superposition_principle#Wave_interference). Since all the particles are identical, the only differentiating factor is their position, which means that the scattered waves will have different phases (due to variation of distances from the particles to the detector). Making the final assumption that these particles are randomly distributed in a small region of space, we [decorrelate](https://en.wikipedia.org/wiki/Correlation_and_dependence) their phases. This results in [incoherent scattering](http://glossary.ametsoc.org/wiki/Incoherent_scattering), with constructive and destructive interference canceling each other out, and [it can be shown](https://www.nbi.dk/~ogendal/personal/lho/lightscattering_theory_and_practice.pdf) that the *mean* energy carried by the combined wave simply increases by a factor of \\(N\\).

Therefore, we can rewrite the Equation 5 for \\(N\\) particles as

$$ \tag{12} \tilde{I}\_s = N \tilde{\sigma}\_s \frac{\tilde{f}\_p}{4 \pi} \tilde{E}\_i. $$

"Wait a minute", you may object. "Shouldn't we also consider the effect of scattered waves on the particles themselves?" And, in general, indeed, we should. This is a [many-body problem](https://en.wikipedia.org/wiki/Many-body_problem), and it is extremely challenging to solve exactly. In a low-density dielectric (a gas), electromagnetic interaction between the particles is typically neglected - this is sometimes referred to as the [independent scattering approximation](https://doi.org/10.1002/qj.49708436025). For dense dielectrics, one way of tackling the problem is to introduce the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) - induced [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment) per unit volume - which can be used to approximate the [local electric field](https://www.feynmanlectures.caltech.edu/II_32.html).

Finally, we can extend the Equation 12 to take the volume \\(dV\\) occupied by particles into account:

$$ \tag{13} \frac{d}{dV} \tilde{I}\_s = \frac{d}{dV} N \tilde{\sigma}\_s \frac{\tilde{f}\_p}{4 \pi} \tilde{E}\_i. $$

For a small volume \\(dV\\), the incident spectral irradiance \\(\tilde{E}\_i\\) is approximately constant:

$$ \tag{14} \frac{d}{dV} \tilde{I}\_s = \tilde{E}\_i \frac{d}{dV} N \tilde{\sigma}\_s \frac{\tilde{f}\_p}{4 \pi}. $$

Assuming that all the particles are identical (including their orientation), the phase function and the scattering cross section can be taken outside the derivative as well:

$$ \tag{15} \frac{d}{dV} \tilde{I}\_s = \tilde{E}\_i \frac{\tilde{f}\_p}{4 \pi} \tilde{\sigma}\_s \frac{dN}{dV}. $$

\\(dN/dV\\) is the definition of the [number density](https://en.wikipedia.org/wiki/Number_density) \\(n\\),
and can be used to define the [scattering coefficient](https://zero-radiance.github.io/post/analytic-media/) \\(\mu_s\\):

$$ \tag{16} \mu_s = n \tilde{\sigma}\_s. $$

Equation 15 then becomes

$$ \tag{17} \frac{d}{dV} \tilde{I}\_s = \mu_s \frac{\tilde{f}\_p}{4 \pi} \tilde{E}\_i. $$

If a distribution of (non-identical) particles is given, we can use its [weighted average](http://eodg.atm.ox.ac.uk/user/grainger/research/book/protected/Chapter5.pdf) properties instead:

$$ \tag{18} \langle \mu_x \rangle = \int n(r) \sigma_x(r) dr, $$

$$ \tag{19} \langle \tilde{f}\_p \rangle = \frac{\int n(r) \tilde{\sigma}\_s(r) \tilde{f}\_p(r) dr}{\int n(r) \tilde{\sigma}\_s(r) dr}. $$

If we orient the direction of propagation of incident light along the \\(z\\) axis, we can write

$$ \tag{20} \frac{d^2 \tilde{I}\_s}{dA dz} = \mu_s \frac{\tilde{f}\_p}{4 \pi} \tilde{E}\_i. $$

This allows us to find the amount of scattered [spectral radiance](https://en.wikipedia.org/wiki/Radiance#Spectral_radiance) \\(L_s\\)

$$ \tag{21} d L_s = \mu_s \frac{\tilde{f}\_p}{4 \pi} \tilde{E}\_i dz. $$

Finally, we can express the incident spectral irradiance \\(\tilde{E}\_i\\) in terms of the incident spectral radiance \\(L_i\\):

$$ \tag{22} L_s = \int\_{4 \pi} \mu_s \frac{\tilde{f}\_p}{4 \pi} L_i d \omega_i dz, $$

where \\(dz\\) reminds us that this is a part of a [line integral](https://zero-radiance.github.io/post/analytic-media/).

## Acknowledgments

I would like to thank Eugene d'Eon for his thoughtful comments and feedback, and Don Grainger for his book and for answering my questions.
