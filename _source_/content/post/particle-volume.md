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

Imagine a light sensor (or a [photon](https://en.wikipedia.org/wiki/Photon) detector, with each photon carrying \\(h \nu\\) joules of [energy](https://en.wikipedia.org/wiki/Photon_energy)) with the *surface area* \\(\sigma\_n\\). We would like to measure the amount of [radiant energy](https://en.wikipedia.org/wiki/Radiant_energy) \\(Q\_n\\) absorbed by the sensor over the period of *time* \\(t\\). This can be done in several different ways, depending on the parametrization of the incident radiation.

If the [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\Phi\_n\\) in the *frequency interval* \\(d\nu\\) reaches the sensor,

$$ \tag{1} dQ\_n = \Phi\_n d\nu dt. $$

The flux is a very coarse quantity in the sense that it does not tell us where the radiation is coming from and to what degree the different parts of the sensor are affected.

In case the flux density on the surface of the sensor is known, it is given by the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E\_n\\):

$$ \tag{2} dQ\_n = E\_n d\sigma\_n d\nu dt. $$

If we can only determine the directional characteristics of the flux, it can be represented using the [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(I\_n\\):

$$ \tag{3} dQ\_n = I\_n d\Omega\_n d\nu dt, $$

where \\(d\Omega\_n\\) is the *solid angle* [measure](https://en.wikipedia.org/wiki/Lebesgue_integration#Measure_theory) associated with the direction of the intensity. More commonly, this equation is used to describe emission (or scattering) by point sources (or scatters) which have no associated surface normal or area.

The [spectral radiance](https://en.wikipedia.org/wiki/Radiance#Spectral_radiance) \\(L\_i\\) specifies[^1] both the spatial and the directional characteristics of the flux:

[^1]: Historically, radiance is called *intensity* and spectral radiance is called *specific intensity*. Personally, I don't find this name to be specific enough.

$$ \tag{4} dQ\_n = L\_i \cos{\theta} d\Omega\_n d\sigma\_n d\nu dt, $$

where \\(\cos{\theta} = \bm{i} \cdot \bm{n}\\) is the cosine[^2] of the angle between the *incident direction* \\(\bm{i}\\) and the *surface normal* [^3] \\(\bm{n}\\).

[^2]: Certain expressions can be simplified by using the *projected area* measure \\(d\sigma\_i = d\sigma\_n \cos{\theta}\\)  or the *projected solid angle* measure \\(d\Omega\_i = d\Omega\_n \cos{\theta}\\).

[^3]: If the sensor is not planar, the normal \\(\bm{n}\\) is a function of position \\(\bm{x}\\). However, it is simpler to think of the sensor as a [manifold](https://mathworld.wolfram.com/Manifold.html) composed of infinitesimal rectangles.

It is important to understand that while both \\(E\_n\\) and \\(I\_n\\) are defined with respect to the surface normal \\(\bm{n}\\), \\(L\_i\\) is measured across the direction \\(\bm{i}\\) of the incident radiation. Comparing Equations 2-4, it is clear that

$$ \tag{5} dE\_n = dE\_i \cos{\theta} = L\_i \cos{\theta} d\Omega\_n. $$
$$ \tag{6} dI\_n = dI\_i \cos{\theta} = L\_i \cos{\theta} d\sigma\_n, $$

{{< figure src="/img/radiometry.png" caption="*Radiance vs irradiance.*" >}}

Independence from the surface parametrization coupled with invariance along the ray makes radiance a very useful quantity for light transport applications.

In practice, most authors write Equation 4 this way:

$$ \tag{7} dQ\_i = L\_i \cos{\theta} d\Omega\_i d\sigma\_i d\nu dt, $$

with all the \\(X\_n\\) quantities considered *incident* and thus written as \\(X\_i\\). We shall adopt this convention as well.

By [reciprocity](https://en.wikipedia.org/wiki/Helmholtz_reciprocity), if we turn the sensor into an emitter and replace \\(\bm{i}\\) with \\(\bm{o}\\), the resulting equations remain valid.

For additional details, see Chapter 3 of Veach's [Ph.D. thesis](http://graphics.stanford.edu/papers/veach_thesis/) and the references listed therein.

## Light Scattering by a Single Particle

The interaction of light with an individual particle is quantified by the [differential scattering cross section](http://glossary.ametsoc.org/wiki/Differential_(scattering)_cross_section) \\(\sigma\_s'\\). It is defined as the ratio of the spectral intensity[^4] \\(I\_o\\) scattered in the given direction \\(\bm{o}\\) to the  spectral irradiance \\(E\_i\\) incident from the direction \\(\bm{i}\\):

$$ \tag{8}
    \sigma\_s'(\bm{i}, \bm{o}) =
    \frac{d \sigma\_s}{d \Omega\_o} =
    \frac{I\_o}{E\_i}.
$$

There is an implicit dependence on the orientation of the particle with respect to the incident wave - it comes into play if the particle is anisotropic.

[^4]: The reason we use intensity rather than radiance is that the scatterer acts as a point charge (or a point source, if you will). While the particle does have an area, its entire volume scatters light, which means that a volume (rather than an area) energy density measure would be more appropriate.

We can obtain the [scattering cross section](https://www.feynmanlectures.caltech.edu/I_32.html) \\(\sigma\_s\\) by integrating over \\(4 \pi\\) steradians:

$$ \tag{9}
    \sigma\_s(\bm{i}) =
    \int\_{4 \pi} \sigma\_s' d \Omega\_o =
    \frac{\int\_{4 \pi} I\_o d \Omega\_o}{E\_i} =
    \frac{\Phi\_o}{E\_i}. $$

In other words, it is just the ratio of the scattered spectral flux \\(\Phi\_o\\) to the incident spectral irradiance \\(E\_i\\). It has the dimensions of an area; however, it is not a geometrical area. It can be imagined as a small disk[^5] normal with respect to the incident beam \\(\bm{i}\\) (don't be confused by the subscript \\(s\\)).

[^5]: The "small disk" model is convenient in problems of light scattering by spherical particles since their geometrical cross section is also a disk. In the volume scattering case, the volume element is typically a rectangular cuboid, which makes the "little square" model more appropriate for describing the cross sections.

The [extinction cross section](http://glossary.ametsoc.org/wiki/Extinction_cross_section) \\(\sigma\_t\\) relates the total spectral flux \\(\Phi\_t\\) scattered or absorbed by the particle (e.i. removed from the incident wave) to the incident spectral irradiance \\(E\_i\\):

$$ \tag{10} \sigma\_t(\bm{i}) = \sigma\_s + \sigma\_a = \frac{\Phi\_o}{E\_i} + \frac{\Phi\_a}{E\_i} = \frac{\Phi\_t}{E\_i}. $$

The optical cross sections \\(\sigma\_x\\) are related to the geometrical cross section \\(\sigma_g\\) by the [efficiencies](https://doi.org/10.1364/JOSAA.35.000163) \\(Q\_x\\):

$$ \tag{11}
    Q\_a = \frac{\sigma\_a}{\sigma_g} = \frac{\Phi\_a}{\Phi\_i}, \qquad
    Q\_s = \frac{\sigma\_s}{\sigma_g} = \frac{\Phi\_o}{\Phi\_i}, \qquad
    Q\_t = \frac{\sigma\_t}{\sigma_g} = \frac{\Phi\_t}{\Phi\_i}. $$

{{< figure src="/img/particle_cross_section.png" caption="*Geometrical cross section of a particle.*" >}}

Note that the value of extinction efficiency can exceed 1. This phenomenon is called the [extinction paradox](https://doi.org/10.1016/j.jqsrt.2010.08.024).

The angular distribution of scattered light is described by the [phase function](http://glossary.ametsoc.org/wiki/Phase_function) \\(f\_p\\):

$$ \tag{12}
    f\_p(\bm{i}, \bm{o}) =
    \frac{I\_o}{\frac{1}{4 \pi} \int\_{4 \pi} I\_o d \Omega\_o} =
    \frac{I\_o}{\frac{1}{4 \pi} \Phi\_o}.
$$

It is the ratio of the energy per unit solid angle scattered in a given direction \\(\bm{o}\\) to the average energy per unit solid angle scattered in all directions. Again, there is an implicit dependence on the orientation of the particle with respect to the incident wave. Note that  the integral of the phase function over \\(4 \pi\\) steradians equals \\(4 \pi\\), which appears to be a common [convention](http://glossary.ametsoc.org/wiki/Phase_function) in optics.

Let us compute the product of the scattering cross section and the phase function:

$$ \tag{13}
    \sigma\_s(\bm{i}) f\_p(\bm{i}, \bm{o}) =
    \frac{\Phi\_o}{E\_i} \frac{I\_o}{\frac{1}{4 \pi} \Phi\_o}. $$

The spectral flux cancels out, and we find the connection with the differential scattering cross section:

$$ \tag{14} \sigma\_s'(\bm{i}, \bm{o}) = \sigma\_s \frac{f\_p}{4 \pi}. $$

For a more elaborate derivation based on wave optics, see Chapter 13 of Born & Wolf, [Principles of optics](https://doi.org/10.1017/CBO9781139644181), or Chapter 2 of Mishchenko, Travis & Lacis, [Scattering, Absorption, and Emission of Light by Small Particles](https://www.cambridge.org/9780521782524).

## Scattering by a Group of Particles

Let us see how we can extend this theory to scattering by \\(N\\) particles. When an atom interacts with light, it experiences a [periodic perturbation of its electron cloud](http://plaza.ufl.edu/dwhahn/Rayleigh%20and%20Mie%20Light%20Scattering.pdf), which turns it into a source of radiation. Assuming [elastic scattering](https://en.wikipedia.org/wiki/Elastic_scattering) (ignoring the [Raman effect](https://en.wikipedia.org/wiki/Raman_scattering)), this radiation will have the same frequency as the incident light. Additionally, assuming that all the particles are identical (including their orientation), they will all radiate energy in exactly the same way.

The resulting electromagnetic waves will [combine](https://en.wikipedia.org/wiki/Superposition_principle#Wave_superposition) at the detector, potentially experiencing both constructive and destructive [interference](https://en.wikipedia.org/wiki/Superposition_principle#Wave_interference). Since all the particles are identical, the only differentiating factor is their position, which means that the scattered waves will have different phases (due to variation of distances from the particles to the detector). Making the final assumption that these particles are randomly distributed in a small region of space, we can [decorrelate](https://en.wikipedia.org/wiki/Correlation_and_dependence) their phases. This results in [incoherent scattering](http://glossary.ametsoc.org/wiki/Incoherent_scattering), with constructive and destructive interference [canceling each other out](https://www.feynmanlectures.caltech.edu/I_32.html) (as we average over space and time), and [it can be shown](https://www.nbi.dk/~ogendal/personal/lho/lightscattering_theory_and_practice.pdf) that the *mean energy* carried by the combined wave simply increases by a factor of \\(N\\).

Therefore, we can rewrite Equations 8 and 14 for \\(N\\) particles as

$$ \tag{15} I\_o = N \sigma\_s \frac{f\_p}{4 \pi} E\_i. $$

One way to see it is as if the scattering cross section increases by a factor of \\(N\\).

"Wait a minute", you may object. "Shouldn't we also consider the effect of scattered waves on the particles themselves?" And, in general, indeed, we should. This is a [many-body problem](https://en.wikipedia.org/wiki/Many-body_problem), and it is extremely challenging to solve exactly. In a low-density dielectric (a gas), electromagnetic interaction between the particles is typically neglected - this is sometimes referred to as the [independent scattering approximation](https://doi.org/10.1002/qj.49708436025). For dense dielectrics, one way of tackling the problem is to introduce the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) - induced [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment) per unit volume - which can be used to approximate the [local electric field](https://www.feynmanlectures.caltech.edu/II_32.html).

We can extend Equation 15 to take the volume occupied by particles into account. Given a number of particles \\(dN\\) contained within a small volume \\(dV\\),

$$ \tag{16} \frac{dI\_o}{dV}  = \frac{dN}{dV} \sigma\_s \frac{f\_p}{4 \pi} E\_i. $$

\\(dN / dV\\) is the definition of the [number density](https://en.wikipedia.org/wiki/Number_density) \\(n\\),
and can be used to define the [scattering coefficient](/post/analytic-media/) \\(\beta\_s\\)

$$ \tag{17} \beta\_s = n \sigma\_s. $$

Substitution yields

$$ \tag{18} \frac{dI\_o}{dV}  = \beta\_s \frac{f\_p}{4 \pi} E\_i. $$

If a distribution of (non-identical) particles is given, under the same assumptions as above, we can use its [weighted average](http://eodg.atm.ox.ac.uk/user/grainger/research/book/protected/Chapter5.pdf) properties instead:

$$ \tag{19} \langle \beta_x \rangle = \int n(r) \sigma_x(r) dr, $$

$$ \tag{20} \langle f\_p \rangle = \frac{\int n(r) \sigma\_s(r) f\_p(r) dr}{\int n(r) \sigma\_s(r) dr}. $$

If we orient the direction of scattering along the \\(z\\)-axis, we may write

$$ \tag{21} \frac{dI\_o}{d\sigma dz} = \beta\_s \frac{f\_p}{4 \pi} E\_i. $$

The ratio of spectral intensity (normal) to the area is the definition of scattered spectral radiance \\(L\_o\\):

$$ \tag{22} \frac{dL\_o}{dz} = \beta\_s \frac{f\_p}{4 \pi} E\_i. $$

Intuitively, the "left-over" \\(dz\\) makes sense. If we increase \\(dz\\), the total volume increases, the density correspondingly decreases, and, since the number of particles remains constant, the amount of spectral radiance does not change.

In light transport applications, we typically deal with radiance rather than irradiance. This can be achieved by substituting[^6] Equation 5 and introducing an integral over \\(4 \pi\\) steradians:

[^6]: The cosine factor does not appear in the calculation because the scattering cross section is, by definition, normal to the direction of incidence.

$$ \tag{23} dL\_o = \int\_{4 \pi} \beta\_s \frac{f\_p}{4 \pi} L_i d \Omega_i dz. $$

{{< figure src="/img/volume_cross_section.png" caption="*Scattering by a volume element.*" >}}

Finally, we must perform line integration along the \\(z\\)-axis

$$ \tag{24} L\_o = \int\_{\bm{x}}^{\bm{y}} T(\bm{x}, \bm{z}) \int\_{4 \pi} \beta\_s \frac{f\_p}{4 \pi} L_i d \Omega_i dz, $$

and add the transmittance term \\(T\\) to account for absorption and scattering over the (non-infinitesimal) distance from the scatterer to the sensor.

By recursively defining \\(L = L\_i = L\_o\\), we obtain[^7] the familiar [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer).

[^7]: If we wish to model the [refractive radiative transfer equation](https://doi.org/10.1145/2557605) instead, we must remember to also account for continuous reflection/transmission as well as the associated change in the solid angle.

For a derivation from the volume scattering viewpoint, see Chapter 1 of Chandrasekhar, [Radiative transfer](https://doi.org/10.1002/qj.49707633016).

## Acknowledgments

I would like to thank Eugene d'Eon for his thoughtful comments and feedback, and Don Grainger for his [book](http://eodg.atm.ox.ac.uk/user/grainger/research/book/) and for reviewing this article.