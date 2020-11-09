---
title: "From Particle to Volume Scattering of Light"
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

## Radiometry Crash Course

Imagine a light sensor (or a [photon](https://en.wikipedia.org/wiki/Photon) counter, with each photon carrying \\(h \nu\\) joules of [energy](https://en.wikipedia.org/wiki/Photon_energy)) with the *surface area* \\(\sigma\_n\\). We would like to measure the amount of [radiant energy](https://en.wikipedia.org/wiki/Radiant_energy) \\(Q\_n\\) absorbed by the sensor over the period of *time* \\(t\\). This can be done in several different ways, depending on the parametrization of the incident radiation.

PICTURE

If the [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\Phi\_n\\) in the *frequency range* \\(d\nu\\) reaches the sensor,

$$ \tag{i} d^2Q\_n = \Phi\_n d\nu dt. $$

The flux is very coarse quantity in the sense that it does not tell us where the radiation is coming from and to what degree the different parts of the sensor are affected.

In case the flux density on the surface of the sensor is known, it is given by the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E\_n\\):

$$ \tag{ii} d^3Q\_n = E\_n d\sigma\_n d\nu dt. $$

If we can only determine the directional characteristics of the flux, it can be represented using the [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(I\_n\\):

$$ \tag{iii} d^3Q\_n = I\_n d\Omega\_n d\nu dt, $$

where \\(d\Omega\_n\\) is the *solid angle* [measure](https://en.wikipedia.org/wiki/Lebesgue_integration#Measure_theory) associated with the direction of the intensity.

The [spectral radiance](https://en.wikipedia.org/wiki/Radiance#Spectral_radiance) \\(L\_i\\) specifies[^1] both the spatial and the directional characteristics of the flux:

[^1]: Historically, radiance is called *intensity* and spectral radiance is called *specific intensity*. Personally, I don't find this name to be specific enough.

$$ \tag{iv} d^4Q\_n = L\_i \cos{\theta} d\Omega\_n d\sigma\_n d\nu dt, $$

where \\(\cos{\theta} = \bm{i} \cdot \bm{n}\\) is the cosine[^2] of the angle between the *incident direction* \\(\bm{i}\\) and the *surface normal* [^3] \\(\bm{n}\\).

[^2]: Certain expressions can be simplified by using the *projected area* measure \\(d\sigma\_i = d\sigma\_n \cos{\theta}\\)  or the *projected solid angle* measure \\(d\Omega\_i = d\Omega\_n \cos{\theta}\\).

[^3]: If the sensor is not planar, the normal \\(\bm{n}\\) is a function of position \\(\bm{x}\\). However, it is simpler to think of the sensor as a [manifold](https://mathworld.wolfram.com/Manifold.html) composed of infinitesimal rectangles.

It is important to understand that while both \\(E\_n\\) and \\(I\_n\\) are defined with respect to the surface normal \\(\bm{n}\\), \\(L\_i\\) is measured across the direction \\(\bm{i}\\) of the incident radiation. Comparing Equations ii-iv, it is clear that

$$ \tag{v}  dE\_n = dE\_i \cos{\theta} = L\_i \cos{\theta} d\Omega\_n, $$
$$ \tag{vi} dI\_n = dI\_i \cos{\theta} = L\_i \cos{\theta} d\sigma\_n. $$

Independence from the surface parametrization coupled with invariance along the ray makes radiance a very useful quantity for light transport applications.

In practice, most authors write Equation iv this way:

$$ \tag{vii} d^4Q\_i = L\_i \cos{\theta} d\Omega\_i d\sigma\_i d\nu dt, $$

with all the \\(X\_n\\) quantities considered *incident* and thus written as \\(X\_i\\).

By [reciprocity](https://en.wikipedia.org/wiki/Helmholtz_reciprocity), if we turn the sensor into an emitter and replace \\(\bm{i}\\) with \\(\bm{o}\\), the resulting equations remain valid.

For additional details, see Chapter 3 of [Veach's PhD thesis](http://graphics.stanford.edu/papers/veach_thesis/) and the references listed therein.

## Light Scattering by a Single Particle

The interaction of light with an individual particle is quantified by the [differential scattering cross section](http://glossary.ametsoc.org/wiki/Differential_(scattering)_cross_section) \\(\sigma\_s'\\). It is defined as the ratio of the [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(I\_o\\) scattered in the given direction \\(\bm{o}\\) to the incident spectral irradiance \\(E\_i\\) normal[^4] with respect to \\(\bm{i}\\):

$$ \tag{5}
    \sigma\_s'(\bm{i}, \bm{o}) =
    \frac{d \sigma\_s}{d \Omega\_o} =
    \frac{I\_o}{E\_i}.
$$

[^4]: It is not measured with respect to the surface of the particle, so there are no cosine factors.

These two directions are typically specified in the reference frame of the particle. There is an implicit dependence on the orientation of the particle with respect to the incident wave - it comes into play if the particle is anisotropic.

We can obtain the [scattering cross section](https://www.feynmanlectures.caltech.edu/I_32.html) \\(\sigma\_s\\) by integrating over \\(4 \pi\\) steradians:

$$ \tag{6}
    \sigma\_s(\bm{i}) =
    \int\_{4 \pi} \sigma\_s' d \Omega\_o =
    \frac{\int\_{4 \pi} I\_o d \Omega\_o}{E\_i} =
    \frac{\Phi\_o}{E\_i}. $$

In other words, it is just the ratio of the scattered [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\Phi\_o\\) to the incident spectral irradiance \\(E\_i\\). It has the dimensions of an area; however, since it's not a geometric area, it can be imagined as a little disk normal with respect to the incident beam \\(\bm{i}\\).

PICTURE

The [extinction cross section](http://glossary.ametsoc.org/wiki/Extinction_cross_section) \\(\sigma\_t\\) relates the total spectral flux \\(\Phi\_t\\) scattered or absorbed by the particle (e.i. removed from the incident wave) to the incident spectral irradiance \\(E\_i\\):

$$ \tag{7} \sigma\_t(\bm{i}) = \sigma\_s + \sigma\_a = \frac{\Phi\_o}{E\_i} + \frac{\Phi\_a}{E\_i} = \frac{\Phi\_t}{E\_i}. $$

The optical cross sections \\(\sigma\_x\\) are related to the geometric cross section \\(\sigma_g\\) by the [efficiencies](https://doi.org/10.1364/JOSAA.35.000163) \\(Q\_x\\):

$$ \tag{8}
    Q\_a = \frac{\sigma\_a}{\sigma_g} = \frac{\Phi\_a}{\Phi\_i}, \qquad
    Q\_s = \frac{\sigma\_s}{\sigma_g} = \frac{\Phi\_o}{\Phi\_i}, \qquad
    Q\_t = \frac{\sigma\_t}{\sigma_g} = \frac{\Phi\_t}{\Phi\_i}. $$

Note that the value of extinction efficiency can exceed 1. This phenomenon is called the [extinction paradox](https://doi.org/10.1016/j.jqsrt.2010.08.024).

The angular distribution of scattered light is described by the [phase function](http://glossary.ametsoc.org/wiki/Phase_function) \\(f\_p\\):

$$ \tag{9}
    f\_p(\bm{i}, \bm{o}) =
    \frac{I\_o}{\frac{1}{4 \pi} \int\_{4 \pi} I\_o d \Omega\_o} =
    \frac{I\_o}{\frac{1}{4 \pi} \Phi\_o}.
$$

It is the ratio of the energy per unit solid angle scattered in a given direction \\(\bm{o}\\) to the average energy per unit solid angle scattered in all directions. Again, there is an implicit dependence on the orientation of the particle with respect to the incident wave. Note that  the integral of the phase function over \\(4 \pi\\) steradians equals \\(4 \pi\\), which appears to be a common [convention](http://glossary.ametsoc.org/wiki/Phase_function) in optics.

Let us compute the product of the scattering cross section and the phase function:

$$ \tag{10}
    \sigma\_s(\bm{i}) f\_p(\bm{i}, \bm{o}) =
    \frac{\Phi\_o}{E\_i} \frac{I\_o}{\frac{1}{4 \pi} \Phi\_o}. $$

The spectral flux cancels out, and we find the connection with the differential scattering cross section:

$$ \tag{11} \sigma\_s'(\bm{i}, \bm{o}) = \sigma\_s \frac{f\_p}{4 \pi}. $$

For a more elaborate derivation, see Born and Wolf ...

## Scattering by a Group of Particles

Let us see how we can extend this theory to scattering from \\(N\\) particles. When an atom interacts with light, it experiences a [periodic perturbation of its electron cloud](http://plaza.ufl.edu/dwhahn/Rayleigh%20and%20Mie%20Light%20Scattering.pdf), which turns it into a source of radiation. Assuming [elastic scattering](https://en.wikipedia.org/wiki/Elastic_scattering) (ignoring the [Raman effect](https://en.wikipedia.org/wiki/Raman_scattering)), this radiation will have the same frequency as the incident light. Additionally, assuming that all the particles are identical (including their orientation), they will all radiate energy in exactly the same way.

The resulting electromagnetic waves will [combine](https://en.wikipedia.org/wiki/Superposition_principle#Wave_superposition) at the detector, potentially experiencing both constructive and destructive [interference](https://en.wikipedia.org/wiki/Superposition_principle#Wave_interference). Since all the particles are identical, the only differentiating factor is their position, which means that the scattered waves will have different phases (due to variation of distances from the particles to the detector). Making the final assumption that these particles are randomly distributed in a small region of space, we can [decorrelate](https://en.wikipedia.org/wiki/Correlation_and_dependence) their phases. This results in [incoherent scattering](http://glossary.ametsoc.org/wiki/Incoherent_scattering), with constructive and destructive interference [canceling each other out](https://www.feynmanlectures.caltech.edu/I_32.html) (as we average over space and time), and [it can be shown](https://www.nbi.dk/~ogendal/personal/lho/lightscattering_theory_and_practice.pdf) that the *mean* energy carried by the combined wave simply increases by a factor of \\(N\\).

Therefore, we can rewrite  Equation 5 for \\(N\\) particles as

$$ \tag{12} I\_o = N \sigma\_s \frac{f\_p}{4 \pi} E\_i. $$

"Wait a minute", you may object. "Shouldn't we also consider the effect of scattered waves on the particles themselves?" And, in general, indeed, we should. This is a [many-body problem](https://en.wikipedia.org/wiki/Many-body_problem), and it is extremely challenging to solve exactly. In a low-density dielectric (a gas), electromagnetic interaction between the particles is typically neglected - this is sometimes referred to as the [independent scattering approximation](https://doi.org/10.1002/qj.49708436025). For dense dielectrics, one way of tackling the problem is to introduce the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) - induced [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment) per unit volume - which can be used to approximate the [local electric field](https://www.feynmanlectures.caltech.edu/II_32.html).

We can extend Equation 12 to take the volume occupied by particles into account. Given a number of particles \\(\Delta N\\) contained within a small volume \\(\Delta V\\),

$$ \tag{13} \frac{I\_o}{\Delta V}  = \frac{\Delta N}{\Delta V} \sigma\_s \frac{f\_p}{4 \pi} E\_i. $$

\\(\Delta N / \Delta V\\) is the definition of the [number density](https://en.wikipedia.org/wiki/Number_density) \\(n\\),
and can be used to define the [scattering coefficient](/post/analytic-media/) \\(\beta\_s\\)

$$ \tag{14} \beta\_s = n \sigma\_s. $$

If a distribution of (non-identical) particles is given, we can use its [weighted average](http://eodg.atm.ox.ac.uk/user/grainger/research/book/protected/Chapter5.pdf) properties instead:

$$ \tag{15} \langle \beta_x \rangle = \int n(r) \sigma_x(r) dr, $$

$$ \tag{16} \langle f\_p \rangle = \frac{\int n(r) \sigma\_s(r) f\_p(r) dr}{\int n(r) \sigma\_s(r) dr}. $$

We can reformulate Equation 13 using the definition of the scattering coefficient:

$$ \tag{17} \frac{I\_o}{\Delta V}  = \beta\_s \frac{f\_p}{4 \pi} E\_i. $$

If we orient the direction of scattering along the \\(z\\)-axis, we may write

$$ \tag{18} \frac{I\_o}{\Delta A \Delta z} = \beta\_s \frac{f\_p}{4 \pi} E\_i. $$

The ratio of spectral intensity to the area is the definition of scattered [spectral radiance](https://en.wikipedia.org/wiki/Radiance#Spectral_radiance) \\(L\_o\\):

$$ \tag{19} L\_o = \beta\_s \frac{f\_p}{4 \pi} E\_i \Delta z. $$

Intuitively, the "left-over" \\(\Delta z\\) makes sense. If we increase \\(\Delta z\\), the total volume increases, the density correspondingly decreases, and, since the number of particles is constant, the amount of spectral radiance does not change.

In light transport applications, we typically deal with radiance rather than irradiance. This can be achieved by introducing an integral over \\(4 \pi\\) steradians

$$ \tag{20} L\_o = \int\_{4 \pi} \beta\_s \frac{f\_p}{4 \pi} L_i d \Omega_i \Delta z, $$

where the scattering coefficient and the phase function determine the magnitude of the contribution of each incident direction.

Finally, if any part of the integrand varies along \\(z\\), we must replace \\(\Delta z\\) by a differential \\(dz\\) and add an outer line integral

$$ \tag{21} L\_o = \int\_{\bm{x}}^{\bm{y}} T(\bm{x}, \bm{z}) \Bigg( \int\_{4 \pi} \beta\_s \frac{f\_p}{4 \pi} L_i d \Omega_i \Bigg) dz, $$
with the transmittance term \\(T\\), which becomes significant since the extent of the volume along the \\(\bm{z}\\)-axis is no longer negligible.

Recursively defining \\(L = L\_i = L\_o\\), we finally obtain the [radiative transfer equation](/post/analytic-media/#radiative-transfer-equation) we are all familiar with.

If we wish to model the [refractive radiative transfer equation](https://doi.org/10.1145/2557605) instead, we must remember to also account for continuous reflection/transmission as well as the associated change in the solid angle.

For an alternative derivation, see Chandrasekhar ...

## Acknowledgments

I would like to thank Eugene d'Eon for his thoughtful comments and feedback, and Don Grainger for his [book](http://eodg.atm.ox.ac.uk/user/grainger/research/book/) and for reviewing this article.

