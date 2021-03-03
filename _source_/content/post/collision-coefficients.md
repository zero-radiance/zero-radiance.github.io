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

We have previously discussed the [radiative transfer equation](/post/analytic-media/) \[[1](#references), [2](#references)\] and its connection to [scattering by small particles](/post/particle-volume/) \[[3](#references), [4](#references)\] in some detail. The short version is that the radiative transfer equation is formulated using the scattering and absorption characteristics of a volume element (on a large scale); and while these characteristics may be rather complex, they can be understood in a more simple way by considering the corpuscular nature of matter (on a small scale). If the distribution of particles contained within the volume element is known, we can compute the volume collision coefficients and the aggregate phase function that determine the outcome of the radiative transfer process. However, so far, little has been said about the nature and the origin of these coefficients.

To give an example, consider the following problem of atmospheric radiative transfer: we wish to simulate a clear blue sky created by Earth's atmosphere. What should our coefficients be set to?

"The sky is blue, so the RGB color coefficient must be blue as well", you may say. While the answer is not completely wrong, unfortunately, it leaves the problem underspecified. For one, we have not one, but several collision coefficients - absorption, scattering, and extinction. Secondly, we can represent them (as a set) in several different ways. For instance, we may decide to use an "artist-friendly" parametrization, such as the combination of the single-scattering albedo and the mean free path. Unfortunately, using a blue albedo and an (arbitrarily?) large mean free path will not produce the expected result, particularly at sunset.

While we may spend more time and energy fiddling with various combinations of parameters to determine what works and what doesn't, there is another way. A more fun way. The scientific way.

<!--more-->

The method can be briefly outlined as follows:

1. We describe the shape, the size, and the composition of an individual particle.
2. We describe radiation incident on the particle using Maxwell's electromagnetic wave theory.
3. The incident wave interacts with the particle, producing a scattered wave.
4. Given the description of the incident and the scattered waves, we find the scattering and extinction efficiencies of the particle.
5. Using the distribution of particles within the volume element, we compute the volume collision coefficients.

You may wonder why we have to use *wave optics*. One of the reasons is that *geometrical optics* fails to model light transport when the size of the geometric features is on the order of the wavelength of light. However, to obtain the correct solution, we must employ wave optics even when dealing with large particles. Below, I have reproduced the statement of Hendrik van de Hulst on the matter \[[4](#references) (ch. 12.1)\].

{{< figure src="/img/geom_vs_wave.png">}}

It is not expected that you understand the prior statement fully before reading the rest of the article. However, it should (hopefully) encourage you to learn a little bit about wave optics. A modest introduction is given below.

## Maxwell's Equations

At the fundamental level, optics is built on the theory of fields. What is a field? A *field* is a [mathematical](https://en.wikipedia.org/wiki/Field_(mathematics)) construct; it is a function defined for all points in space (and time). In [physics](https://en.wikipedia.org/wiki/Field_(physics)), a field typically has a source, contains energy, and exerts a force.

Since we are primarily concerned with electromagnetic radiation, we shall focus our attention on the the [electric field](https://en.wikipedia.org/wiki/Electric_field) \\(\bm{E}\\) and the [magnetic induction](https://en.wikipedia.org/wiki/Magnetic_field#The_B-field) \\(\bm{B}\\).

They satisfy [Maxwell's equations in matter](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation):

$$ \tag{1}
	\nabla \times \bm{E} + \frac{\partial \bm{B}}{\partial t} = 0, \quad
	\nabla \cdot  \bm{B} = 0, \quad
	\nabla \times \bm{H} - \frac{\partial \bm{D}}{\partial t} = \bm{J\_f}, \quad
	\nabla \cdot  \bm{D} = \rho\_f,
$$

where

$$ \tag{2}
	\mathrm{curl}(\bm{V}) =
	\nabla \times \bm{V} =
	\begin{bmatrix}
		\partial / \partial x \cr
		\partial / \partial y \cr
		\partial / \partial z
	\end{bmatrix} \times \bm{V} =
	\begin{bmatrix}
		\partial V\_z / \partial y - \partial V\_y / \partial z \cr
		\partial V\_x / \partial z - \partial V\_z / \partial x \cr
		\partial V\_y / \partial x - \partial V\_x / \partial y
	\end{bmatrix}
$$

is the [curl](https://en.wikipedia.org/wiki/Curl_(mathematics)) operator and

$$ \tag{3}
	\mathrm{div}(\bm{V}) =
	\nabla \cdot \bm{V} =
	\begin{bmatrix}
		\partial / \partial x \cr
		\partial / \partial y \cr
		\partial / \partial z
	\end{bmatrix} \cdot \bm{V} =
	\frac{\partial V\_x}{\partial x} + \frac{\partial V\_y}{\partial y} + \frac{\partial V\_z}{\partial z}
$$

is the [divergence](https://en.wikipedia.org/wiki/Divergence) operator, both given in Cartesian coordinates.

As discussed in [B&W, Feynman], \\(\bm{E}\\) and \\(\bm{B}\\) are considered fundamental fields, and the [electric displacement](https://en.wikipedia.org/wiki/Electric_displacement_field) \\(\bm{D}\\) and the [magnetic field](https://en.wikipedia.org/wiki/Magnetic_field#The_H-field) \\(\bm{H}\\) are [auxiliary fields](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) that arise due to the influence of matter[^101]. \\(\bm{J\_f}\\) and \\(\rho\_f\\) are the *free* [current](https://en.wikipedia.org/wiki/Current_density#Free_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Free_charge_density). They are defined using the [material equations](https://en.wikipedia.org/wiki/Constitutive_equation#Electromagnetism):

[^101]: We can obtain the [microscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Formulation_in_SI_units_convention) (without auxiliary fields) by [substitution](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) of the material equations. For our application, the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) is more useful.

$$ \tag{4}
	\bm{J\_f} = \hat{\sigma} \bm{E} + \mathellipsis, \quad
	\bm{D} = \varepsilon\_0 \bm{E} + \bm{P} = \hat{\varepsilon} \bm{E} + \mathellipsis, \quad
	\bm{H} = \frac{1}{\mu\_0} \bm{B} - \bm{M} = \frac{1}{\hat{\mu}} \bm{B} - \mathellipsis.
$$

In the first approximation, for a *linear* material, they are directly related to the electric and magnetic field intensities by the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity) \\(\hat{\sigma}\\), the [relative permittivity](https://en.wikipedia.org/wiki/Relative_permittivity) \\(\hat{\varepsilon}\\), and the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \\(\hat{\mu}\\). These are properties of the medium, and they are expected to be continuous in the region of space for Maxwell's equations to be valid.

Materials may be *dispersive* (dependent on the frequency), *absorptive* (cause energy loss), and *anisotropic* (dependent on the orientation). Generally, this means that the conductivity, the permittivity, and the permeability are frequency-dependent complex-valued [tensor fields](https://en.wikipedia.org/wiki/Tensor#Tensor_fields). For instance,

$$ \tag{5}
	\bm{D}(\bm{r}, t, \omega) \approx \hat{\varepsilon}(\bm{r}, t, \omega) \bm{E}(\bm{r}, t, \omega) =
	\begin{bmatrix}
		\varepsilon\_{r11} - i \varepsilon\_{i11} & \varepsilon\_{r12} - i \varepsilon\_{i12} & \varepsilon\_{r13} - i \varepsilon\_{i13} \cr
		\varepsilon\_{r21} - i \varepsilon\_{i21} & \varepsilon\_{r22} - i \varepsilon\_{i22} & \varepsilon\_{r23} - i \varepsilon\_{i23} \cr
		\varepsilon\_{r31} - i \varepsilon\_{i31} & \varepsilon\_{r32} - i \varepsilon\_{i32} & \varepsilon\_{r33} - i \varepsilon\_{i33}
	\end{bmatrix}
	\begin{bmatrix}
		E\_x \cr
		E\_y \cr
		E\_z
	\end{bmatrix}.
$$

Often, it is convenient to introduce an optical discontinuity[^102] to the medium. At the *optical interface*, the fields (on both sides) must satisfy [boundary conditions](https://en.wikipedia.org/wiki/Interface_conditions_for_electromagnetic_fields). If \\(\bm{n\_{12}}\\) is a unit normal vector pointing from region 1 to region 2, the normal components of the fields must be such that

[^102]: It is a convenient way to represent a very rapid continuous variations of optical properties.

$$ \tag{6}
	\bm{n\_{12}} \cdot  (\bm{B\_2} - \bm{B\_1}) = 0, \quad
	\bm{n\_{12}} \cdot  (\bm{D\_2} - \bm{D\_1}) = \rho\_s,
$$

where \\(\rho\_s\\) is the *surface* [charge density](https://en.wikipedia.org/wiki/Charge_density). For the tangential components, it can be shown that

$$ \tag{7}
	\bm{n\_{12}} \times (\bm{E\_2} - \bm{E\_1}) = 0, \quad
	\bm{n\_{12}} \times (\bm{H\_2} - \bm{H\_1}) = \bm{J\_s},
$$

where \\(\bm{J\_s}\\) is the *surface* [current density](https://en.wikipedia.org/wiki/Current_density).

We will not use the equations in their most general form; however, it is much easier to simplify the theory by introducing additional assumptions than to do it the other way around.

<!---
[^60]: [Maxwell's equations](http://www.maxwells-equations.com/) are defined using 5 [vector fields](https://en.wikipedia.org/wiki/Vector_field): \\(\bm{E}\\) is the *electric vector*, \\(\bm{H}\\) is the *magnetic vector*, \\(\bm{j}\\) is the *electric current density*, \\(\bm{D}\\) is the *electric flux density* (a.k.a. the *electric displacement*), and \\(\bm{B}\\) is the *magnetic flux density* (a.k.a. the *magnetic induction*). \\(\bm{E}\\) and \\(\bm{B}\\) are considered the fundamental fields, and \\(\bm{j}, \bm{D}, \bm{H}\\) arise due to the influence of matter. For more details, refer to 1) ch. 1.1. of Born, M., & Wolf, E. [Principles of optics](https://doi.org/10.1017/CBO9781139644181), 7th edition (1999); 2) vol. II, ch 32.2 of Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman lectures on physics](https://www.feynmanlectures.caltech.edu/II_32.html) (1963); 3) Hill, W. T. [E, D, B & H: What do they all mean?](http://www.physics.umd.edu/courses/Phys263/wth/fall04/downloads/EDBH/edbh.pdf) (2004).
--->

## Wave Equation

Consider a region of space without any free currents or charges. Physically, this means that there are no sources of fields. This may seem strange at first; the idea is to see what kind of fields could *exist* (if we add them to the system) and how they would *evolve* over time.

Setting \\(\bm{J\_f} = \rho\_f = 0\\) in Equation 1, we obtain a coupled system of homogeneous linear differential equations:

$$ \tag{8}
	\nabla \times \bm{E} + \frac{\partial \bm{B}}{\partial t} = 0, \quad
	\nabla \cdot  \bm{B} = 0, \quad
	\nabla \times \bm{H} - \frac{\partial \bm{D}}{\partial t} = 0, \quad
	\nabla \cdot  \bm{D} = 0.
$$

Mathematically, the consequence is that a linear combination of several solutions is also a valid solution.

Next, we assume that the medium is linear[^103]. Substitution of Equation 4 into Equation 8 yields

[^103]: Lectures on nonlinear optics here: https://kth.diva-portal.org/smash/record.jsf?pid=diva2%3A25333&dswid=-8275

$$ \tag{9}
	\nabla \times \bm{E} + \frac{\partial ({\hat{\mu}} \bm{H})}{\partial t} = 0, \quad
	\nabla \cdot  ({\hat{\mu}} \bm{H}) = 0, \quad
	\nabla \times \bm{H} - \frac{\partial (\hat{\varepsilon} \bm{E})}{\partial t} = 0, \quad
	\nabla \cdot  (\hat{\varepsilon} \bm{E}) = 0.
$$

Further, we assume that the medium is *homogeneous* and *static* (constant in space and time)[^104]:

[^104]: See born and wolf p 11 for a more complex case 

$$ \tag{10}
	\nabla \times \bm{E} + {\hat{\mu}} \frac{\partial \bm{H}}{\partial t} = 0, \quad
	\nabla \cdot  \bm{H} = 0, \quad
	\nabla \times \bm{H} - \hat{\varepsilon} \frac{\partial \bm{E}}{\partial t} = 0, \quad
	\nabla \cdot  \bm{E} = 0.
$$

Now we can easily find a solution for the electric field \\(\bm{E}\\). Take curl of Equation 10.1 and time derivative of Equation 10.3 (assume that we can interchange the order of differentiation with respect to space and time):

$$ \tag{11}
	\nabla \times \nabla \times \bm{E} + {\hat{\mu}} \Big( \nabla \times \frac{\partial \bm{H}}{\partial t} \Big) = 0, \quad
	\nabla \times \frac{\partial \bm{H}}{\partial t} = \hat{\varepsilon} \frac{\partial^2 \bm{E}}{\partial t^2}, \quad
	\nabla \cdot  \bm{E} = 0.
$$

Introduce the [curl-of-curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity

$$ \tag{12}
	\nabla \times (\nabla \times \bm{V}) = \nabla (\nabla \cdot \bm{V}) - \nabla^2 \bm{V},
$$

where 

$$ \tag{13}
	\mathrm{grad}(s) =
	\nabla s =
	\begin{bmatrix}
		\partial / \partial x \cr
		\partial / \partial y \cr
		\partial / \partial z
	\end{bmatrix} s =
	\begin{bmatrix}
		\partial s / \partial x \cr
		\partial s / \partial y \cr
		\partial s / \partial z
	\end{bmatrix}
$$

is the [gradient](https://en.wikipedia.org/wiki/Gradient) operator and

$$ \tag{14}
	\nabla^2 \bm{V} =
	\mathrm{div}(\mathrm{grad}(\bm{V})) =
	(\nabla \cdot \nabla) \bm{V} =
	\Bigg[\frac{\partial}{\partial x}, \frac{\partial}{\partial y}, \frac{\partial}{\partial z}\Bigg]^{\mathrm{T}}
	\begin{bmatrix}
		\partial / \partial x \cr
		\partial / \partial y \cr
		\partial / \partial z
	\end{bmatrix} \bm{V} =
	\frac{\partial^2 \bm{V}}{\partial x^2} + \frac{\partial^2 \bm{V}}{\partial y^2} + \frac{\partial^2 \bm{V}}{\partial z^2}
$$

is the [vector Laplace](https://en.wikipedia.org/wiki/Laplace_operator#Vector_Laplacian) operator (scalar Laplace operator applied to each vector component).

Use it to expand Equation 11.1:

$$ \tag{15}
	\nabla (\nabla \cdot \bm{E}) - \nabla^2 \bm{E} + {\hat{\mu}} \Big( \nabla \times \frac{\partial \bm{H}}{\partial t} \Big) = 0, \quad
	\nabla \times \frac{\partial \bm{H}}{\partial t} = \hat{\varepsilon} \frac{\partial^2 \bm{E}}{\partial t^2}, \quad
	\nabla \cdot  \bm{E} = 0.
$$

Substitution of Equations 15.2 and 15.3 into 15.1 yields the *vector* [wave equation](https://en.wikipedia.org/wiki/Wave_equation) of the electric field \\(\bm{E}\\):

$$ \tag{16}
	\nabla^2 \bm{E} - \hat{\varepsilon} {\hat{\mu}} \frac{\partial^2 \bm{E}}{\partial t^2} = 0.
$$

Performing the same steps allows us to obtain the wave equation of the magnetic field \\(\bm{H}\\):

$$ \tag{17}
	\nabla^2 \bm{H} - \hat{\varepsilon} {\hat{\mu}} \frac{\partial^2 \bm{H}}{\partial t^2} = 0.
$$

Both Equation 16 and Equation 17 form a coupled system of scalar wave equations (one per vector component).

## Solution of the Scalar Wave Equation. Scalar Waves


Applying the curl-of-curl operator to \\(\bm{E}\\) in Equation 9 yields

???

Waves are ubiquitous in nature. Some notable examples include water waves, sound waves, light waves, and gravitational waves. Regardless of the type of the wave, all of them arise from the [wave equation](https://www.feynmanlectures.caltech.edu/I_47.html) \[Feynman, B&W\] of the form

$$ \tag{1} \frac{1}{c^2} \frac{\partial^2 v}{\partial t^2} = \nabla^2 v, $$

where \\(c\\) is a constant and \\(\nabla^2\\) is the [Laplacian](https://en.wikipedia.org/wiki/Laplace_operator) (given below in Cartesian coordinates of a 3-dimensional space):

$$ \tag{2} \nabla^2 v = \frac{\partial^2 v}{\partial x^2} + \frac{\partial^2 v}{\partial y^2} + \frac{\partial^2 v}{\partial z^2}. $$

It is a homogeneous linear differential equation, which means that a linear combination of several solutions is also a valid solution.

The general solution[^99] of the wave equation \[W&W\] is a combination of complex exponentials on a unit sphere

$$ \tag{3} v = \int\_{-\pi}^{\pi} \int\_{0}^{\pi} f(k, \theta, \phi) e^{i k (-x \sin \theta \cos \phi - y \sin \theta \sin \phi - z \cos \theta + c t)} d\theta d\phi. $$

[^99]: All the signs may be reversed. \\(k\\) may be real or complex.

If we define

$$ \tag{4} \bm{r} =
\begin{bmatrix}
    x \cr
    y \cr
    z
\end{bmatrix} $$

and

$$ \tag{5} \bm{k}(\theta, \phi) =
k \begin{bmatrix}
    \sin \theta \cos \phi \cr
    \sin \theta \sin \phi \cr
    \cos \theta
\end{bmatrix}, $$

we can represent the general solution in a vector form:

$$ \tag{6} v(\bm{r}, t) = \int\_{-\pi}^{\pi} \int\_{0}^{\pi} f(k, \theta, \phi) e^{i \big( -\bm{k}(\theta, \phi) \cdot \bm{r} + (c/k) t \big)} d\theta d\phi. $$

To find a special solution for a particular problem, one must define a region where the equation holds and [boundary conditions](https://en.wikipedia.org/wiki/Boundary_value_problem) at the borders.

## Scalar Waves

The simplest type of wave is a [plane wave](https://www.feynmanlectures.caltech.edu/II_20.html) \[Chandra?, [4](#references) (ch. 4), [5](#references) (vol. II, ch. 20), [6](#references) (ch. 1.3), [7](#references) (pp. 5-7)\]. Mathematically, the reason to consider plane waves is that they form the simplest set of solutions of the wave equation \[[6](#references) (ch. 1.2), others?\].

So, what is a plane wave, exactly? While plane waves are often introduced using [Fresnel's formulation of Huygen's principle](https://en.wikipedia.org/wiki/Huygens%E2%80%93Fresnel_principle) \[[4](#references) (ch. 3.1)\], I will attempt to give another, (hopefully) simpler explanation.

Consider an infinitesimal (punctual) isotropic source of "disturbance". It generates *spherical waves* that can be imagined as spheres expanding at constant velocity. We can decompose these spheres into sets of individual points. At any given point in time, all these points are at the same distance from the source. And if we fix a certain distance from the source, all points at this distance have been generated at the same point in time. Loci of such points are said to form surfaces of constant *phase*, and these surfaces are called *wavefronts*.

In the case of *plane waves*, surfaces of constant phase are planes rather than spheres. Plane waves can be generated in a multitude of ways; one mathematical trick is to imagine a source at a large distance. Then, as the distance tends infinity, the curvature of the wavefront approaches zero, and, for a small solid angle, we may approximate a section of the wavefront as planar.

We are particularly interested in waves that are *time-harmonic*. This property implies that the amplitude of the wave is a [harmonic function](https://en.wikipedia.org/wiki/Harmonic_function) of time. The *real amplitude* of the "disturbance" caused by the scalar wave at the point \\(\bm{r}\\) and the time \\(t\\) can be mathematically described [^1] as

[^1]: We use the "classic" sign convention of the authors cited.

$$ \tag{1} a(\bm{r}, t) = \bar{a}(\bm{r}) \cos(\phi(\bm{r}) + \omega t), $$

where \\(\bar{a}\\) is the *maximum amplitude* and \\(\omega\\) is the *angular frequency*. Surfaces of \\(\phi = \mathrm{const}\\) are called *cophasal*, or *wavefronts*. Note that, in general, surfaces of constant amplitude do not coincide with surfaces of constant phase [[6](#references) (p. 18)\].

Time-harmonic waves are the foundation of the Fourier optics, which handles arbitrarily-complex waves by decomposing them into their Fourier series.

{{< figure src="/img/wave_diagram.png" caption="*Wave diagram. [Image source](https://tsunamiphysics.webnode.com/waves-review/).*">}}

Some simple examples of Equation 1 include spherical waves

$$ \tag{2} a(r,t) = \frac{\bar{a}}{r} \cos(\phi(r) + \omega t), $$

and plane waves traveling along the the \\(z\\)-axis

$$ \tag{3} a(z,t) = \bar{a} \cos(\phi(z) + \omega t). $$

Typically, the real amplitude of a plane wave in a medium is described as

$$ \tag{4} a(z,t) = \bar{a} \cos(\delta - k z + \omega t), $$

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

$$ \tag{7} u(\bm{r}, t) = \bar{a}(\bm{r}) e^{i \theta(\bm{r}, t)} = \bar{a}(\bm{r}) e^{i\phi(\bm{r})} e^{i \omega t}. $$

Combining Equations 4, 5, and 7, we arrive at the explicit description of a plane wave

$$ \tag{8} u(z,t) = \bar{a} e^{i (\delta - (\omega / c) (\eta - i \kappa) z + \omega t)}
			      = \bar{a} e^{-\kappa (\omega z / c)} e^{i (\delta - \eta (\omega z / c) + \omega t)}. $$

We can take the argument to extract the *phase angle*

$$ \tag{9} \theta(z,t) = \mathrm{Arg} \big\lbrace u(z,t) \big\rbrace = \delta - \eta (\omega z / c) + \omega t, $$

and taking the real part allows us to recover the real amplitude

$$ \tag{10} a(z,t) = \mathrm{Re} \big\lbrace u(z,t) \big\rbrace
			      = \bar{a} e^{-\kappa (\omega z / c)} \cos{\theta(z,t)} . $$

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
    	\bar{E}\_x e^{-\kappa\_x (\omega z / c)} \cos(\delta\_x - \eta\_x (\omega z / c) + \omega t) \cr
    	\bar{E}\_y e^{-\kappa\_y (\omega z / c)} \cos(\delta\_y - \eta\_y (\omega z / c) + \omega t)
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
    	\bar{B}\_x e^{-\kappa\_x (\omega z / c)} \sin(\delta\_x - \eta\_x (\omega z / c) + \omega t) \cr
    	\bar{B}\_y e^{-\kappa\_y (\omega z / c)} \sin(\delta\_y - \eta\_y (\omega z / c) + \omega t)
	\end{bmatrix}. $$

The only new quantity introduced by Equation 12 is the maximum magnitude of \\(\bm{B}\\). Turns out, for time-harmonic plane waves propagating in a linear medium (without currents), the following relation holds:

$$ \begin{aligned} \tag{13}
	\bar{B} &= \mu \bar{H} \cr
	\mathrm{Re} \lbrace \sqrt{\varepsilon} \rbrace \bar{E} &= \sqrt{\mu} \bar{H} \cr
	\bar{B} = \mathrm{Re} \lbrace \sqrt{ \varepsilon \mu } \rbrace \bar{E}
		 &= \mathrm{Re} \lbrace \sqrt{ (\varepsilon\_r \varepsilon\_0) (\mu\_r \mu\_0) } \rbrace \bar{E}
		 = \frac{\eta}{c} \bar{E} = \frac{\bar{E}}{v\_p},
\end{aligned} $$

where \\(\varepsilon\_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Vacuum_permittivity) and \\(\mu\_0\\) is the [vacuum permeability](https://en.wikipedia.org/wiki/Vacuum_permeability).

Therefore, for time-harmonic waves, the amplitudes of the electric and the magnetic vectors are connected by the phase velocity, and propagation of the magnetic vector is often handled implicitly.

The vector nature of light waves (in mathematics) is referred to as the polarization of light (in physics).

Non-paraxial polarization ???

<!--
Imagine a small particle of an arbitrary shape embedded in the host medium with the [complex refractive index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) (IOR)[^50] \\(\eta - i \kappa\\).

[^50]: By convention, \\(\eta\_0 = 1, \kappa\_0 = 0\\) is reserved for the [vacuum](https://en.wikipedia.org/wiki/Vacuum).

{{< figure src="/img/linear_plane_wave.png" caption="*Linearly-polarized plane electromagnetic wave. [Image source](https://openstax.org/books/university-physics-volume-2/pages/16-4-momentum-and-radiation-pressure).*">}}

Consider an [electromagnetic wave](https://www.cpp.edu/~alrudolph/classes/phy234/Reading/Summary%20of%20Waves.pdf) represented[^60] by the electric vector \\(\bm{E}\\) and the magnetic flux density \\(\bm{B}\\) of a certain frequency and in a certain state of polarization[^70]. The direction of propagation and the amount of power[^80] per unit area carried by the wave at the time \\(t\\) is given by the [instanteneous Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Formulation_in_terms_of_microscopic_fields) \\(\bm{S}\\)

[^60]: [Maxwell's equations](http://www.maxwells-equations.com/) are defined using 5 [vector fields](https://en.wikipedia.org/wiki/Vector_field): \\(\bm{E}\\) is the *electric vector*, \\(\bm{H}\\) is the *magnetic vector*, \\(\bm{j}\\) is the *electric current density*, \\(\bm{D}\\) is the *electric flux density* (a.k.a. the *electric displacement*), and \\(\bm{B}\\) is the *magnetic flux density* (a.k.a. the *magnetic induction*). \\(\bm{E}\\) and \\(\bm{B}\\) are considered the fundamental fields, and \\(\bm{j}, \bm{D}, \bm{H}\\) arise due to the influence of matter. For more details, refer to 1) ch. 1.1. of Born, M., & Wolf, E. [Principles of optics](https://doi.org/10.1017/CBO9781139644181), 7th edition (1999); 2) vol. II, ch 32.2 of Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman lectures on physics](https://www.feynmanlectures.caltech.edu/II_32.html) (1963); 3) Hill, W. T. [E, D, B & H: What do they all mean?](http://www.physics.umd.edu/courses/Phys263/wth/fall04/downloads/EDBH/edbh.pdf) (2004).

[^70]: \\(\bm{E}\\) and \\(\bm{B}\\) are mutually orthogonal.

[^80]: While confirmed experimentally, this definition is, mathematically, somewhat arbitrary. For a discussion and further references, refer to p. 10 of Born, M., & Wolf, E. [Principles of optics](https://doi.org/10.1017/CBO9781139644181), 7th edition (1999).

$$ \tag{1} \bm{S}(t) = \frac{1}{\mu\_0} \Big( \bm{E}(t) \times \bm{B}(t) \Big), $$

where \\(\mu\_0\\) is the [vacuum permeability](https://en.wikipedia.org/wiki/Vacuum_permeability) of the medium.

Since light waves oscillate very rapidly, we are typically interested in the [time-averaged Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Time-averaged_Poynting_vector) \\(\langle \bm{S} \rangle\\)

$$ \tag{2} \langle \bm{S} \rangle = \frac{1}{T} \int\_{0}^{T} \bm{S}(t) dt, $$

which can be used to define the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E\\) (do not confuse it with the electric vector \\(\bm{E}\\))

$$ \tag{3} E = \vert \langle \bm{S} \rangle \vert \cos{\theta} = \langle \bm{S} \rangle \cdot \bm{v}, $$

where \\(\theta\\) is the angle between \\(\bm{S}\\) and the viewing direction \\(\bm{v}\\).

For a [plane wave](https://en.wikipedia.org/wiki/Plane_wave), the expression is particularly [simple](https://en.wikipedia.org/wiki/Irradiance#Property):

$$ \tag{4} E = \frac{1}{2} \frac{\eta}{\mu\_0 c} \bar{E}^2 = \frac{1}{2} \varepsilon\_0 \eta c \bar{E}^2, $$
$$ \tag{4} E = \frac{1}{2} \frac{\eta}{\mu\_0 c} \bar{E}^2 = \frac{1}{2} \varepsilon\_0 \eta c \bar{E}^2, $$

where \\(\varepsilon\_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Vacuum_permittivity) and \\(\bar{E}\\) is the maximum amplitude of the electric vector.

-->


## Polarization of Light



We have just seen that transverse waves are composed of two independent scalar components. Can we separate them? The answer is yes.

In the framework of geometric optics, light is represented by oriented ray. When this ray encounters a birefringent crystal (such as calcite), double refraction occurs, and the original light ray splits into the *ordinary* and *extraordinary* rays.

The idea behind [polarization of light](http://hyperphysics.phy-astr.gsu.edu/hbase/phyopt/polarcon.html#c1) \[Chandra?, [4](#references) (ch. 5), [5](#references) (vol. I, ch. 33), [6](#references) (ch. 1.4), [7](#references), [8](#references) (p. 527-533), [9](#references)\] (not to be confused with [polarization of matter](http://hyperphysics.phy-astr.gsu.edu/hbase/electric/dielec.html#c1) \[[4](#references) (ch. 4.5), [5](#references) (vol. II, ch. 32), [6](#references) (ch. 2.3), [9](#references)\]) is simple. Consider a fixed point[^4] in space; at that point, imagine the plane[^5] containing the electric and the magnetic vectors. Over time, the tip of the electric (and the magnetic) vector traces a closed[^6] curve. That shape of that curve is called polarization.

[^4]: In general, polarization depends on position \[[6](#references) (p. 38)\].

[^5]: It would be natural to call it the *plane of polarization*. However, the term has been used (and misused) in so many different contexts during the course of development of the field of optics that the only way to avoid ambiguity is to not use the term at all \[[6](#references) (p. 29)\].

[^6]: Since the wave is time-harmonic, the wave function is periodic.

To determine this shape, consider a time-harmonic wave at a fixed position \\(\bm{r}\\)

$$ \tag{14} \bm{E}(\bm{r},t) =
	\begin{bmatrix}
    	\bar{E}\_x(\bm{r}) \cos(\phi\_x(\bm{r}) + \omega t) \cr
    	\bar{E}\_y(\bm{r}) \cos(\phi\_y(\bm{r}) + \omega t)
	\end{bmatrix},
$$

where \\(\bar{E}\_x(\bm{r})\\) and \\(\bar{E}\_y(\bm{r})\\) include the absorption effects. Expanding the cosine of the sum of angles,

$$ \tag{15}
\begin{cases}
   E\_x / \bar{E}\_x = \cos{\phi\_x} \cos{\omega t} - \sin{\phi\_x} \sin{\omega t} \cr
   E\_y / \bar{E}\_y = \cos{\phi\_y} \cos{\omega t} - \sin{\phi\_y} \sin{\omega t}
\end{cases}
$$

Multiply Equation 15a by \\(\sin{\phi\_y}\\) and subtract Equation 15b multiplied by \\(\sin{\phi\_x}\\). Similarly, multiply Equation 15a by \\(\cos{\phi\_y}\\) and subtract Equation 15b multiplied by \\(\cos{\phi\_x}\\). The result is

$$ \tag{16}
\begin{cases}
   (E\_x / \bar{E}\_x) \sin{\phi\_y} - (E\_y / \bar{E}\_y) \sin{\phi\_x} = \sin(\phi\_y - \phi\_x) \cos{\omega t} \cr
   (E\_x / \bar{E}\_x) \cos{\phi\_y} - (E\_y / \bar{E}\_y) \cos{\phi\_x} = \sin(\phi\_y - \phi\_x) \sin{\omega t}
\end{cases}
$$

Squaring and adding the equations results in an equation of a [conic section](https://brilliant.org/wiki/conics-discriminant/):

$$ \tag{17}
   (E\_x / \bar{E}\_x)^2 - 2 (E\_x / \bar{E}\_x) (E\_y / \bar{E}\_y) \cos(\phi\_y - \phi\_x) + (E\_y / \bar{E}\_y)^2 = \sin^2(\phi\_y - \phi\_x).
$$

Computing the discriminant

$$ \tag{18}
   d = 4 (E\_x / \bar{E}\_x)^2 (E\_y / \bar{E}\_y)^2 \cos^2(\phi\_y - \phi\_x) - 4 (E\_x / \bar{E}\_x)^2 (E\_y / \bar{E}\_y)^2 \leq 0
$$

reveals that it is the equation of an ellipse. This means that we can find a set of rotated coordinate axes such that

$$ \tag{19}
	\bm{E}(\bm{r},t) =
	\begin{bmatrix}
    	E\_a(\bm{r},t) \cr
    	E\_b(\bm{r},t)
	\end{bmatrix} =
	\begin{bmatrix}
    	\phantom{\pm} \bar{E}\_a(\bm{r}) \cos(\phi(\bm{r}) + \omega t) \cr
    	\pm   	      \bar{E}\_b(\bm{r}) \sin(\phi(\bm{r}) + \omega t)
	\end{bmatrix},
$$

where \\(\phi(\bm{r})\\) is isotropic, and the phase difference between the two axes is \\(\pi/2\\). The sign in front of the \\(\bar{E}\_b\\) is used to distinguish the orientation of the ellipse (clockwise vs counterclockwise).

The significance of Equation 19 can be better seen if we express it using complex numbers

$$ \tag{??}
	\bm{E}(\bm{r},t) = \mathrm{Re}
	\begin{Bmatrix}
    	\phantom{\pm} \bar{E}\_a(\bm{r}) \phantom{e^{-i \pi/2}} e^{i(\phi(\bm{r}) + \omega t)} \cr
    			 \pm  \bar{E}\_b(\bm{r}) 		  e^{-i \pi/2}  e^{i(\phi(\bm{r}) + \omega t)}
	\end{Bmatrix} =
	\mathrm{Re} \big\lbrace \bm{\bar{E}}(\bm{r}) e^{i \phi(\bm{r})} e^{i \omega t} \big\rbrace,
$$

which makes it the vector analog of the complex amplitude of a scalar time-harmonic wave.

But can it be characterized by a single amplitude and orientation? Intensity? Discuss...

If the angle between the \\(x\\)-axis and the \\(a\\)-axis is \\(\psi\\),

$$ \tag{20}
\begin{cases}
   E\_a = E\_x \cos{\psi} + E\_y \sin{\psi} \cr
   E\_b = E\_y \cos{\psi} - E\_x \sin{\psi}
\end{cases}
$$

Combining Equations 19 and 20, we obtain

$$ \tag{21}
\begin{cases}
   \phantom{\pm} \bar{E}\_a \cos(\phi + \omega t) = E\_x \cos{\psi} + E\_y \sin{\psi} \cr
   \pm           \bar{E}\_b \sin(\phi + \omega t) = E\_y \cos{\psi} - E\_x \sin{\psi}
\end{cases}
$$

Substitution of Equations 15a and 15b and expansion of sines and cosines of sums yields

$$ \begin{aligned} \tag{22a}
   \bar{E}\_a (\cos{\phi} \cos{\omega t} - \sin{\phi} \sin{\omega t})
   &= \bar{E}\_x (\cos{\phi\_x} \cos{\omega t} - \sin{\phi\_x} \sin{\omega t}) \cos{\psi} \cr
   &+ \bar{E}\_y (\cos{\phi\_y} \cos{\omega t} - \sin{\phi\_y} \sin{\omega t}) \sin{\psi}
\end{aligned} $$

$$ \begin{aligned} \tag{22b}
   \pm \bar{E}\_b (\sin{\phi} \cos{\omega t} + \cos{\phi} \sin{\omega t})
   &= \bar{E}\_y (\cos{\phi\_y} \cos{\omega t} - \sin{\phi\_y} \sin{\omega t}) \cos{\psi} \cr
   &- \bar{E}\_x (\cos{\phi\_x} \cos{\omega t} - \sin{\phi\_x} \sin{\omega t}) \sin{\psi}
\end{aligned} $$

Decomposition into groups of coefficients of \\(\cos{\omega t}\\) and \\(\sin{\omega t}\\) results in

$$ \tag{23}
\begin{cases}
   \phantom{\pm} \bar{E}\_a \cos{\phi} = \phantom{-} \bar{E}\_x \cos{\phi\_x} \cos{\psi} + \bar{E}\_y \cos{\phi\_y} \sin{\psi} \cr
            \pm  \bar{E}\_b \sin{\phi} = \phantom{-} \bar{E}\_y \cos{\phi\_y} \cos{\psi} - \bar{E}\_x \cos{\phi\_x} \sin{\psi} \cr
   \phantom{\pm} \bar{E}\_a \sin{\phi} = \phantom{-} \bar{E}\_x \sin{\phi\_x} \cos{\psi} + \bar{E}\_y \sin{\phi\_y} \sin{\psi} \cr
            \pm  \bar{E}\_b \cos{\phi} =          -  \bar{E}\_y \sin{\phi\_y} \cos{\psi} + \bar{E}\_x \sin{\phi\_x} \sin{\psi}
\end{cases}
$$

Squaring Equations 23a-23d and adding them allows us to obtain the identity

$$ \tag{24} \bar{E}\_a^2 + \bar{E}\_b^2 = \bar{E}\_x^2 + \bar{E}\_y^2. $$

Similarly, multiplying Equations 23a by 23d and 23b by 23c and computing their sum, we get

$$ \tag{25} \pm \bar{E}\_a \bar{E}\_b = \bar{E}\_x \bar{E}\_y \sin(\phi\_x - \phi\_y). $$

The combination of Equations 24 and 25 can be used to compute the width and the height of the ellipse.

To compute the rotation angle \\(\psi\\), multiply Equation 23a by 23b and Equation 23c by 24d, and simplify the resulting identity:

$$ \tag{26} \Big( \bar{E}\_x^2 - \bar{E}\_y^2 \Big) \sin{2 \psi} = 2 \bar{E}\_x \bar{E}\_y \cos{2 \psi} \cos(\phi\_x - \phi\_y), $$

$$ \tag{27} \tan{2 \psi} = \frac{2 \bar{E}\_x \bar{E}\_y \cos(\phi\_x - \phi\_y)}{\bar{E}\_x^2 - \bar{E}\_y^2}. $$

Auxiliary angle ...


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
10. Fleisch, D. [A Student's Guide to Maxwell's Equations](https://doi.org/10.1017/CBO9780511984624) (2008).