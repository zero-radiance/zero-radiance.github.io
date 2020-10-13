---
title: "From Particle to Volume Scattering"
date: 2020-09-20
tags: [
    "Light Scattering",
    "Optics",
    "Radiative Transfer",
    "Radiometry",
    ]
---

I have [previously](/post/analytic-media/) covered the basics of the volume scattering process. It models the macroscopic interaction of light and matter, with the latter represented as a distribution of [small particles](https://doi.org/10.1002/qj.49708436025). Fundamentally, the volume scattering process arises from the microscopic interaction of light with individual atoms, and is typically described using [Maxwell's electromagnetic theory](https://doi.org/10.1364/JOSAA.35.000163). It is the domain of optics, and, in my experience, the connection with the [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer) is neither apparent nor easy to find. The purpose of this blog post is to familiarize the reader with domain-specific terms and ease the adoption of formulas found in the optics literature.

<!--more-->

## Particle Scattering Basics

Imagine a small particle of an arbitrary shape embedded in the host medium with the complex refractive index \\(\eta - i \kappa\\) (by convention, \\(\eta\_0 = 1, \kappa\_0 = 0\\) is reserved for the [vacuum](https://en.wikipedia.org/wiki/Vacuum)).

{{< figure src="/img/linear_plane_wave.png" caption="*Linearly-polarized plane electromagnetic wave. [Image source](https://openstax.org/books/university-physics-volume-2/pages/16-4-momentum-and-radiation-pressure).*">}}

Consider an [electromagnetic wave](https://www.cpp.edu/~alrudolph/classes/phy234/Reading/Summary%20of%20Waves.pdf) represented by the electric field \\(\bm{E}\\) and the magnetic field \\(\bm{B}\\) of a certain frequency and in a certain state of polarization. The direction of propagation and the amount of power per unit area carried by the wave at the time \\(t\\) is given by the [instanteneous Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Formulation_in_terms_of_microscopic_fields) \\(\bm{S}\\)

$$ \tag{1} \bm{S}(t) = \varepsilon\_0 c^2 \Big( \bm{E}(t) \times \bm{B}(t) \Big), $$

where \\(\varepsilon\_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Permittivity) and \\(c\\) is the speed of light.

Since light waves oscillate very rapidly, we are typically interested in the [time-averaged Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Time-averaged_Poynting_vector) \\(\langle \bm{S} \rangle\\)

$$ \tag{2} \langle \bm{S} \rangle = \frac{1}{T} \int\_{0}^{T} \bm{S}(t) dt, $$

which can be used to define the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E\\) (do not confuse it with the electric field vector \\(\bm{E}\\))

$$ \tag{3} E = \vert \langle \bm{S} \rangle \vert. $$

For a [plane wave](https://en.wikipedia.org/wiki/Plane_wave), the expression is particularly [simple](https://en.wikipedia.org/wiki/Irradiance#Property):

$$ \tag{4} E = \frac{1}{2} \varepsilon\_0 c \eta \vert \mathcal{E} \vert^2, $$

where \\(\mathcal{E}\\) is the [complex amplitude](https://en.wikipedia.org/wiki/Phasor) of the electric field vector (which encodes both the magnitude and the [phase](https://en.wikipedia.org/wiki/Phase_(waves))).

The interaction of light with an individual particle is quantified by the [differential scattering cross section](http://glossary.ametsoc.org/wiki/Differential_(scattering)_cross_section) \\(\sigma\_s'\\). It is defined as the ratio of the scattered [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(I\_s\\) for the given direction \\(\bm{\omega\_s}\\) to the incident spectral irradiance \\(E\_i\\) normal with respect to \\(\bm{\omega\_i}\\):

$$ \tag{5}
    \sigma\_s'(\bm{\omega\_i}, \bm{\omega\_s}) =
    \frac{d \sigma\_s}{d \omega\_s} =
    \frac{I\_s}{E\_i}.
$$

These two directions are typically specified in the reference frame of the particle. There is an implicit dependence on the orientation of the particle with respect to the incident wave - it comes into play if the particle is anisotropic.

We can obtain the scattering cross section \\(\sigma\_s\\) by integrating over \\(4 \pi\\) steradians:

$$ \tag{6}
    \sigma\_s(\bm{\omega\_i}) =
    \int\_{4 \pi} \sigma\_s' d \omega\_s =
    \frac{\int\_{4 \pi} I\_s d \omega\_s}{E\_i} =
    \frac{\Phi\_s}{E\_i}. $$

In other words, it is just the ratio of the scattered [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\Phi\_s\\) to the incident spectral irradiance \\(E\_i\\).

The [extinction cross section](http://glossary.ametsoc.org/wiki/Extinction_cross_section) \\(\sigma\_t\\) relates the total spectral flux \\(\Phi\_t\\) scattered or absorbed by the particle (e.i. removed from the incident wave) to the incident spectral irradiance \\(E\_i\\):

$$ \tag{7} \sigma\_t(\bm{\omega\_i}) = \sigma\_s + \sigma\_a = \frac{\Phi\_s}{E\_i} + \frac{\Phi\_a}{E\_i} = \frac{\Phi\_t}{E\_i}. $$

The optical cross sections \\(\sigma\_x\\) are related to the geometric cross section \\(\sigma_g\\) by the [efficiencies](https://doi.org/10.1364/JOSAA.35.000163) \\(Q\_x\\):

$$ \tag{8}
    Q\_a = \frac{\sigma\_a}{\sigma_g} = \frac{\Phi\_a}{\Phi\_i}, \qquad
    Q\_s = \frac{\sigma\_s}{\sigma_g} = \frac{\Phi\_s}{\Phi\_i}, \qquad
    Q\_t = \frac{\sigma\_t}{\sigma_g} = \frac{\Phi\_t}{\Phi\_i}. $$

Note that the value of extinction efficiency can exceed 1. This phenomenon is called the [extinction paradox](https://doi.org/10.1016/j.jqsrt.2010.08.024).

The angular distribution of scattered light is described by the [phase function](http://glossary.ametsoc.org/wiki/Phase_function) \\(f\_p\\):

$$ \tag{9}
    f\_p(\bm{\omega\_i}, \bm{\omega\_s}) =
    \frac{I\_s}{\frac{1}{4 \pi} \int\_{4 \pi} I\_s d \omega\_s} =
    \frac{I\_s}{\frac{1}{4 \pi} \Phi\_s}.
$$

It is the ratio of the energy per unit solid angle scattered in a given direction \\(\bm{\omega\_s}\\) to the average energy per unit solid angle scattered in all directions. Again, there is an implicit dependence on the orientation of the particle with respect to the incident wave. Note that  the integral of the phase function over \\(4 \pi\\) steradians equals \\(4 \pi\\), which appears to be a common [convention](http://glossary.ametsoc.org/wiki/Phase_function) in optics.

Let us compute the product of the scattering cross section and the phase function:

$$ \tag{10}
    \sigma\_s(\bm{\omega\_i}) f\_p(\bm{\omega\_i}, \bm{\omega\_s}) =
    \frac{\Phi\_s}{E\_i} \frac{I\_s}{\frac{1}{4 \pi} \Phi\_s}. $$

The spectral flux cancels out, and we find the connection with the differential scattering cross section:

$$ \tag{11} \sigma\_s'(\bm{\omega\_i}, \bm{\omega\_s}) = \sigma\_s \frac{f\_p}{4 \pi}. $$

## Scattering by a Group of Particles

Let us see how we can extend this theory to scattering from \\(N\\) particles. When an atom interacts with light, it experiences a [periodic perturbation of its electron cloud](http://plaza.ufl.edu/dwhahn/Rayleigh%20and%20Mie%20Light%20Scattering.pdf), which turns it into a source of radiation. Assuming [elastic scattering](https://en.wikipedia.org/wiki/Elastic_scattering) (ignoring the [Raman effect](https://en.wikipedia.org/wiki/Raman_scattering)), this radiation will have the same frequency as the incident light. Additionally, assuming that all the particles are identical (including their orientation), they will all radiate energy in exactly the same way.

The resulting electromagnetic waves will [combine](https://en.wikipedia.org/wiki/Superposition_principle#Wave_superposition) at the detector, potentially experiencing both constructive and destructive [interference](https://en.wikipedia.org/wiki/Superposition_principle#Wave_interference). Since all the particles are identical, the only differentiating factor is their position, which means that the scattered waves will have different phases (due to variation of distances from the particles to the detector). Making the final assumption that these particles are randomly distributed in a small region of space, we can [decorrelate](https://en.wikipedia.org/wiki/Correlation_and_dependence) their phases. This results in [incoherent scattering](http://glossary.ametsoc.org/wiki/Incoherent_scattering), with constructive and destructive interference canceling each other out, and [it can be shown](https://www.nbi.dk/~ogendal/personal/lho/lightscattering_theory_and_practice.pdf) that the *mean* energy carried by the combined wave simply increases by a factor of \\(N\\).

Therefore, we can rewrite  Equation 5 for \\(N\\) particles as

$$ \tag{12} I\_s = N \sigma\_s \frac{f\_p}{4 \pi} E\_i. $$

"Wait a minute", you may object. "Shouldn't we also consider the effect of scattered waves on the particles themselves?" And, in general, indeed, we should. This is a [many-body problem](https://en.wikipedia.org/wiki/Many-body_problem), and it is extremely challenging to solve exactly. In a low-density dielectric (a gas), electromagnetic interaction between the particles is typically neglected - this is sometimes referred to as the [independent scattering approximation](https://doi.org/10.1002/qj.49708436025). For dense dielectrics, one way of tackling the problem is to introduce the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) - induced [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment) per unit volume - which can be used to approximate the [local electric field](https://www.feynmanlectures.caltech.edu/II_32.html).

We can extend Equation 12 to take the volume occupied by particles into account. Given a small number of particles \\(\Delta N\\) contained within a small volume \\(\Delta V\\),

$$ \tag{13} \frac{I\_s}{\Delta V}  = \frac{\Delta N}{\Delta V} \sigma\_s \frac{f\_p}{4 \pi} E\_i. $$

\\(\Delta N / \Delta V\\) is the definition of the [number density](https://en.wikipedia.org/wiki/Number_density) \\(n\\),
and can be used to define the [scattering coefficient](/post/analytic-media/) \\(\mu\_s\\)

$$ \tag{14} \mu\_s = n \sigma\_s. $$

If a distribution of (non-identical) particles is given, we can use its [weighted average](http://eodg.atm.ox.ac.uk/user/grainger/research/book/protected/Chapter5.pdf) properties instead:

$$ \tag{15} \langle \mu_x \rangle = \int n(r) \sigma_x(r) dr, $$

$$ \tag{16} \langle f\_p \rangle = \frac{\int n(r) \sigma\_s(r) f\_p(r) dr}{\int n(r) \sigma\_s(r) dr}. $$

We can reformulate Equation 13 using the definition of the scattering coefficient:

$$ \tag{17} \frac{I\_s}{\Delta V}  = \mu\_s \frac{f\_p}{4 \pi} E\_i. $$

If we orient the direction of scattering along the \\(z\\)-axis, we may write

$$ \tag{18} \frac{I\_s}{\Delta A \Delta z} = \mu\_s \frac{f\_p}{4 \pi} E\_i. $$

The ratio of spectral intensity to the area is the definition of scattered [spectral radiance](https://en.wikipedia.org/wiki/Radiance#Spectral_radiance) \\(L\_s\\):

$$ \tag{19} L\_s = \mu\_s \frac{f\_p}{4 \pi} E\_i \Delta z. $$

Intuitively, the "left-over" \\(\Delta z\\) makes sense. If we increase \\(\Delta z\\), the total volume increases, the density correspondingly decreases, and, since the number of particles is constant, the amount of spectral radiance does not change.

In light transport applications, we typically deal with radiance rather than irradiance. This can be achieved by introducing an integral over \\(4 \pi\\) steradians

$$ \tag{20} L\_s = \int\_{4 \pi} \mu\_s \frac{f\_p}{4 \pi} L_i d \omega_i \Delta z, $$

where the scattering coefficient and the phase function determine the magnitude of the contribution of each incident direction.

Finally, if any part of the integrand varies along \\(z\\), we must replace \\(\Delta z\\) by a differential \\(dz\\) and add an outer line integral

$$ \tag{21} L\_s = \int\_{\bm{x}}^{\bm{y}} T(\bm{x}, \bm{z}) \Bigg( \int\_{4 \pi} \mu\_s \frac{f\_p}{4 \pi} L_i d \omega_i \Bigg) dz, $$
with the transmittance term \\(T\\), which becomes significant since the extent of the volume along the \\(\bm{z}\\)-axis is no longer negligible.

Recursively defining \\(L = L\_i = L\_s\\), we finally obtain the [radiative transfer equation](/post/analytic-media/#radiative-transfer-equation) we are all familiar with.

If we wish to model the [refractive radiative transfer equation](https://doi.org/10.1145/2557605) instead, we must remember to also account for continuous reflection/transmission as well as the associated change in the solid angle.

## Acknowledgments

I would like to thank Eugene d'Eon for his thoughtful comments and feedback, and Don Grainger for his [book](http://eodg.atm.ox.ac.uk/user/grainger/research/book/) and for reviewing this article.

