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

We have previously discussed the [radiative transfer equation](/post/analytic-media/) \[[1](#references), [2](#references)\] and its connection to [light scattered by small particles](/post/particle-volume/) \[[3](#references), [4](#references)\] in some detail. If the distribution of particles is known, once we obtain the efficiencies (and the phase function) of the individual particles, we can readily compute the volume collision coefficients (and the aggregate phase function) that determine the outcome of the radiative transfer process. However, so far, little has been said about the nature and the origin of these coefficients.

To give an example, consider the following problem of atmospheric radiative transfer. We wish to simulate a clear blue sky created by Earth's atmosphere. What should our coefficients be set to?

"The sky is blue, so the RGB color coefficient must be blue as well", you may say. While the answer is not completely wrong, unfortunately, it leaves the problem underspecified. For one, we have not one, but several collision coefficients - absorption, scattering and extinction. Secondly, we can represent them (as a set) in several different ways. We may decide to use an "artist-friendly" parametrization, such as the combination of the single-scattering albedo and the mean free path. Unfortunately, using a blue albedo and an (arbitrarily) large mean free path will not produce the expected result, particularly at sunset.

While we may spend more time and energy trying various different combinations of parameters to determine what works and what doesn't, there is another way. A more fun way. The scientific way.

<!--more-->

The method can be briefly outlined as follows:

1. We describe radiation incident on the particle using Maxwell's wave theory of light.
2. We describe the shape, the size and the composition of the particle.
3. The incident wave interacts with the particle, producing a scattered wave.
4. Given the description of the incident and the scattered waves, we can compute the scattering and extinction efficiencies.

You may wonder why we have to use *wave optics*. One of the reasons is that *geometrical optics* fail to model light transport when the size of the geometric features is on the order of the wavelength of light. However, to obtain the correct solution, we must employ wave optics even when dealing with large particles. Below, I have reproduced the statement of Hendrik van de Hulst on the matter \[[4](#references) (ch. 12.1)\].

{{< figure src="/img/geom_vs_wave.png">}}

It is not expected that you understand the prior statement fully before reading the rest of the article. However, it should (hopefully) encourage you to learn a little bit about wave optics. A modest introduction is given below.

## Scalar Waves

The simplest type of wave is a [harmonic plane wave](https://www.feynmanlectures.caltech.edu/II_20.html) \[Chandra?, [4](#references) (ch. 4), [5](#references) (vol. II, ch. 20), [6](#references) (ch. 1.3), [7](#references) (pp. 5-7)\]. Mathematically, the reason to consider harmonic plane waves is that they form the simplest set of solutions of the wave equation \[[6](#references) (ch. 1.2), others?\].

So, what is a plane wave, exactly? While plane waves are often introduced using [Fresnel's formulation of Huygen's principle](https://en.wikipedia.org/wiki/Huygens%E2%80%93Fresnel_principle) \[[4](#references) (ch. 3.1)\], I will attempt to give another, (hopefully) simpler explanation.

Consider an infinitesimal (punctual) isotropic source of "disturbance". It generates *spherical waves* that can be imagined as spheres expanding at constant velocity. We can decompose these spheres into sets of individual points. At any given point in time, all these points are at the same distance from the source. And if we fix a certain distance from the source, all points at this distance have been generated at the same point in time. Loci of such points are said to form surfaces of constant *phase*, and these surfaces are called *wavefronts*.

In the case of *plane waves*, surfaces of constant phase are planes rather than spheres. Plane waves can be generated in a multitude of ways; one mathematical trick is to imagine a source at a large distance. Then, as the distance tends infinity, the curvature of the wavefront approaches zero, and, for a small solid angle, we may approximate a section of the wavefront as planar.

By *time-harmonic*, we mean that the amplitude of the wave is a [harmonic function](https://en.wikipedia.org/wiki/Harmonic_function) of time. The *real amplitude* of the "disturbance" caused by the scalar wave at the point \\(\bm{r}\\) and the time \\(t\\) can be mathematically described [^1] as

[^1]: We use the "classic" sign convention of the authors cited.

$$ \tag{1} a(\bm{r}, t) = a\_m(\bm{r}) \cos(\phi(\bm{r}) + \omega t), $$

where \\(a\_m\\) is the *maximum amplitude* and \\(\omega\\) is the *angular frequency*. Surfaces of \\(\phi = \mathrm{const}\\) are called *cophasal*, or *wavefronts*. Note that, in general, surfaces of constant amplitude do not coincide with surfaces of constant phase [[6](#references) (p. 18)\].

{{< figure src="/img/wave_diagram.png" caption="*Wave diagram. [Image source](https://tsunamiphysics.webnode.com/waves-review/).*">}}

Some simple examples of Equation 1 include spherical waves

$$ \tag{2} a(r,t) = \frac{a\_m}{r} \cos(\phi(r) + \omega t), $$

and plane waves traveling along the the \\(z\\)-axis

$$ \tag{3} a(z,t) = a\_m \cos(\phi(z) + \omega t). $$

Typically, the real amplitude of a plane wave in a medium is described as

$$ \tag{4} a(z,t) = a\_m \cos(\delta - k z + \omega t), $$

where \\(\delta\\) is the *phase shift* and \\(k\\) is the *complex wavenumber*.

If the medium has the [complex refractive index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) (IOR) \\(\eta - i \kappa\\), the [complex wavenumber](https://en.wikipedia.org/wiki/Wavenumber#Complex) is[^2]

[^2]: The IOR (and, therefore, the complex wavenumber) is a function of position, time and frequency. Variation with frequency causes *dispersion*.

$$ \tag{5} k = k\_0 (\eta - i \kappa)
			 = \frac{2 \pi}{\lambda\_0} (\eta - i \kappa)
			 = \frac{\omega}{c} (\eta - i \kappa), $$

where \\(k\_0\\) and \\(\lambda\_0\\) are the real *wavenumber and wavelength in vacuum*, respectively, and \\(c\\) is the *speed of light*.

The complex IOR itself can be expressed in terms of the complex [relative permittivity](https://en.wikipedia.org/wiki/Relative_permittivity) (a.k.a. the *dielectric constant*) \\(\varepsilon\_r\\) and the real [relative permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \\(\mu\_r\\) of the medium:

$$ \tag{6} \eta - i \kappa = \sqrt{\varepsilon\_r \mu\_r}. $$

Mathematically, it is convenient to describe a wave using the *complex amplitude*

$$ \tag{7} u(\bm{r}, t) = a\_m(\bm{r}) e^{i \theta(\bm{r}, t)} = a\_m(\bm{r}) e^{i\phi(\bm{r})} e^{i \omega t}. $$

Combining Equations 4, 5, and 7, we arrive at the explicit description of a plane wave

$$ \tag{8} u(z,t) = a\_m e^{i (\delta - (\omega / c) (\eta - i \kappa) z + \omega t)}
			      = a\_m e^{-\kappa (\omega z / c)} e^{i (\delta - \eta (\omega z / c) + \omega t)}. $$

We can take the argument to extract the *phase angle*

$$ \tag{9} \theta(z,t) = \mathrm{Arg} \big\lbrace u(z,t) \big\rbrace = \delta - \eta (\omega z / c) + \omega t, $$

and taking the real part allows us to recover the real amplitude

$$ \tag{10} a(z,t) = \mathrm{Re} \big\lbrace u(z,t) \big\rbrace
			      = a\_m e^{-\kappa (\omega z / c)} \cos{\theta(z,t)} . $$

When examining Equations 8-10, the first thing to notice is that \\(\omega z / c\\) and \\(\omega t\\) are both measured in radians. With that in mind, the role of the complex IOR becomes apparent. The real part of the IOR, \\(\eta\\), reduces the [phase velocity](https://www.feynmanlectures.caltech.edu/I_48.html) \\(v\_p\\) of the wave from \\(c\\) to \\(c / \eta\\) \[[5](#references) (vol. I, ch. 48), [6](#references) (ch. 1.3.3)\]. The imaginary part of the IOR, \\(\kappa\\), causes [exponential absorption](https://www.feynmanlectures.caltech.edu/I_31.html) with distance \[[5](#references) (vol. I, ch. 31), [6](#references) (???)\].

## Transverse Waves

While scalar waves are characterized only by the amplitude and the direction of propagation, [transverse](http://hyperphysics.phy-astr.gsu.edu/hbase/Sound/tralon.html) waves have an orientation[^3] as well \[[6](#references) (ch. 1.4), [9](#references)\].

[^3]: The "disturbance" is a vector tangent to the surface of constant phase.

In the case of the electric vector \\(\bm{E}\\), it just means the plane harmonic wave has two scalar components:

$$ \tag{11} \bm{E}(z,t) =
	\begin{bmatrix}
    	E\_x(z,t) \cr
    	E\_y(z,t)
	\end{bmatrix} =
	\begin{bmatrix}
    	E\_{m,x} e^{-\kappa\_x (\omega z / c)} \cos(\delta\_x - \eta\_x (\omega z / c) + \omega t) \cr
    	E\_{m,y} e^{-\kappa\_y (\omega z / c)} \cos(\delta\_y - \eta\_y (\omega z / c) + \omega t)
	\end{bmatrix}. $$

{{< figure src="/img/electric_vector.png" caption="*Electric vector \[[7](#references) (p. 6)\].*">}}

The frequency is the same for both components, but this is where similarities end. The vector components may oscillate out of phase, which is mathematically modeled by independent phase shifts. Additionally, certain materials (with a [crystal lattice](https://www.feynmanlectures.caltech.edu/II_30.html) \[[5](#references) (vol. II, ch. 30)\]) are *anisotropic* and have a complex IOR that depends on the orientation of the crystal with respect to the incident light. If the refractive index \\(\eta\\) of the material has axial dependence, the crystal is said to be doubly refractive or [birefringent](https://en.wikipedia.org/wiki/Birefringence). If the absorption index \\(\kappa\\) differs instead, the crystal is called [dichroic](https://en.wikipedia.org/wiki/Dichroism) (from the Greek *dikhroos*, two-colored).

What about the magnetic vector? From Maxwell's equations, we know that the magnetic flux density \\(\bm{B}\\) is [orthogonal](/post/particle-volume/) to both \\(\bm{E}\\) and the direction of propagation of the wave. This can be accomplished by rotating (adding a -90 degree phase shift to) both components of the wave. In other words,

$$ \tag{12} \bm{B}(z,t) =
	\begin{bmatrix}
    	B\_x(z,t) \cr
    	B\_y(z,t)
	\end{bmatrix} =
	\begin{bmatrix}
    	B\_{m,x} e^{-\kappa\_x (\omega z / c)} \sin(\delta\_x - \eta\_x (\omega z / c) + \omega t) \cr
    	B\_{m,y} e^{-\kappa\_y (\omega z / c)} \sin(\delta\_y - \eta\_y (\omega z / c) + \omega t)
	\end{bmatrix}. $$

The only new quantity introduced by Equation 12 is the maximum magnitude of \\(\bm{B}\\). Turns out, for time-harmonic plane waves propagating in a linear medium (without currents), the following relation holds:

$$ \begin{aligned} \tag{13}
	B\_m &= \mu H_m \cr
	\mathrm{Re} \lbrace \sqrt{\varepsilon} \rbrace E\_m &= \sqrt{\mu} H_m \cr
	B\_m = \mathrm{Re} \lbrace \sqrt{ \varepsilon \mu } \rbrace E_m
		 &= \mathrm{Re} \lbrace \sqrt{ (\varepsilon\_r \varepsilon\_0) (\mu\_r \mu\_0) } \rbrace E_m
		 = \frac{\eta}{c} E\_m = \frac{E\_m}{v\_p},
\end{aligned} $$

where \\(\varepsilon\_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Vacuum_permittivity) and \\(\mu\_0\\) is the [vacuum permeability](https://en.wikipedia.org/wiki/Vacuum_permeability).

Therefore, for simple time-harmonic waves, the amplitudes of the electric and the magnetic field vectors are connected by the phase velocity, and it is typically sufficient to just consider the electric field.

The vector nature of light waves (in mathematics) is referred to as the polarization of light (in physics).

<!--
## Polarization of Light

We have just seen that transverse waves are composed of two independent scalar components. Can we separate them? The answer is yes.

In the framework of geometric optics, light is represented by oriented ray. When this ray encounters a birefringent crystal (such as calcite), double refraction occurs, and the original light ray splits into the *ordinary* and *extraordinary* rays.

The idea behind [polarization of light](http://hyperphysics.phy-astr.gsu.edu/hbase/phyopt/polarcon.html#c1) \[Chandra?, [4](#references) (ch. 5), [5](#references) (vol. I, ch. 33), [6](#references) (ch. 1.4), [7](#references), [8](#references) (p. 527-533), [9](#references)\] (not to be confused with [polarization of matter](http://hyperphysics.phy-astr.gsu.edu/hbase/electric/dielec.html#c1) \[[4](#references) (ch. 4.5), [5](#references) (vol. II, ch. 32), [6](#references) (ch. 2.3), [9](#references)\]) is simple. Consider a fixed point[^4] in space; at that point, imagine the plane[^5] containing the electric and the magnetic vectors. Over time, the tip of the electric (and the magnetic) vector traces a closed[^6] curve. That shape of that curve is called polarization.

To determine this shape, consider a plane wave, this time formulated in a slightly different way:

$$ \tag{14} \bm{E}(z,t) =
	\begin{bmatrix}
    	E\_{m,x} \cos(\omega t + \phi\_x(z)) \cr
    	E\_{m,y} \cos(\omega t + \phi\_y(z))
	\end{bmatrix} =
	\begin{bmatrix}
    	E\_{m,x} \cos(\omega t + \phi\_x(z)) \phantom{-----} \cr
    	E\_{m,y} \cos(\omega t + \phi\_x(z) + \Delta\phi\_{yx}(z))
	\end{bmatrix},
$$

where \\(\Delta\phi\_{yx} = \phi\_{y} - \phi\_{x}\\).

$$ \tag{15} \bm{E}(z,t) =
	\begin{bmatrix}
    	E\_{m,x} \cos\theta\_x \phantom{-------------.} \cr
    	E\_{m,y} \cos\theta\_x \cos\Delta\phi\_{yx} - E\_{m,y} \sin\theta\_x \sin\Delta\phi\_{yx}
	\end{bmatrix},
$$

[^4]: In general, polarization depends on position \[[6](#references) (p. 38)\].

[^5]: It would be natural to call it the *plane of polarization*. However, the term has been used (and misused) in so many different contexts during the course of development of the field of optics that the only way to avoid ambiguity is to not use the term at all \[[6](#references) (p. 29)\].

[^6]: Since the wave is time-harmonic, the wave function is periodic.

... equation of an ellipse ...

-->

## Acknowledgments

Pharr, Jakob, NASA people

## References

<!-- Modified APA style -->

1. Golubev, E. [Sampling analytic participating media](/post/analytic-media/) (2020).
2. Chandrasekhar, S. [Radiative transfer](https://doi.org/10.1002/qj.49707633016). (1950).
3. Golubev, E. [From particle to volume scattering](/post/particle-volume/) (2020).
4. van de Hulst, H. C. [Light scattering by small particles](https://doi.org/10.1002/qj.49708436025) (1957).
5. Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman lectures on physics](https://www.feynmanlectures.caltech.edu/) (1963).
6. Born, M., & Wolf, E. [Principles of optics](https://doi.org/10.1017/CBO9781139644181), 7th edition (1999).
7. Collett, E. [Field Guide to Polarization](https://doi.org/10.1117/3.626141) (2005).
8. Hansen, J. E., & Travis, L. D. [Light scattering in planetary atmospheres](https://doi.org/10.1007/BF00168069) (1974).
9. Nave, C. R. [HyperPhysics](http://hyperphysics.phy-astr.gsu.edu/hbase/index.html) (2017).
