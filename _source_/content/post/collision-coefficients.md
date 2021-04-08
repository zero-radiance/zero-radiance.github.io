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

## Maxwell's Equations in the Time Domain

At the fundamental level, optics is built on the theory of fields. What is a field? A *field* is a [mathematical](https://en.wikipedia.org/wiki/Field_(mathematics)) construct; it is a function defined for all points in space \\(\bm{r}\\) and time \\(t\\). In [physics](https://en.wikipedia.org/wiki/Field_(physics)), a typical field has a source, contains energy, and exerts a force.

Since we are primarily concerned with electromagnetic radiation, we shall focus our attention on two real vector fields: the [electric field](https://en.wikipedia.org/wiki/Electric_field) \\(\bm{E}\\) and the [magnetic field](https://en.wikipedia.org/wiki/Magnetic_field#The_B-field) \\(\bm{B}\\).

They satisfy the differential form of [Maxwell's equations](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Formulation_in_SI_units_convention) \[[5](#references) (vol. II, ch. 18)\]:

$$ \tag{1.1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E}(\bm{r}, t) \big) = \bm{J}(\bm{r}, t), &
	&\nabla \cdot \big( \epsilon\_0 \bm{E}(\bm{r}, t) \big) = \rho(\bm{r}, t),
\end{aligned}
$$

where

$$ \tag{1.2}
	\mathrm{curl}(\bm{E}) =
	\nabla \times \bm{E} =
	\begin{bmatrix}
		\partial / \partial x \cr
		\partial / \partial y \cr
		\partial / \partial z
	\end{bmatrix} \times \bm{E} =
	\begin{bmatrix}
		\partial E\_z / \partial y - \partial E\_y / \partial z \cr
		\partial E\_x / \partial z - \partial E\_z / \partial x \cr
		\partial E\_y / \partial x - \partial E\_x / \partial y
	\end{bmatrix}
$$

is the [curl](https://en.wikipedia.org/wiki/Curl_(mathematics)) operator and

$$ \tag{1.3}
	\mathrm{div}(\bm{E}) =
	\nabla \cdot \bm{E} =
	\begin{bmatrix}
		\partial / \partial x \cr
		\partial / \partial y \cr
		\partial / \partial z
	\end{bmatrix} \cdot \bm{E} =
	\frac{\partial E\_x}{\partial x} + \frac{\partial E\_y}{\partial y} + \frac{\partial E\_z}{\partial z}
$$

is the [divergence](https://en.wikipedia.org/wiki/Divergence) operator, both given in Cartesian coordinates. \\(\epsilon\_0\\) and \\(\mu\_0\\) are the [vacuum permittivity](https://en.wikipedia.org/wiki/Vacuum_permittivity) and the [vacuum permeability](https://en.wikipedia.org/wiki/Vacuum_permeability), respectively, and are connected by the [speed of light](https://en.wikipedia.org/wiki/Speed_of_light)

$$ \tag{1.4}
	c = (\epsilon\_0 \mu\_0)^{-\frac{1}{2}}.
$$

Sometimes, Equations 1.1 are referred to as the "vacuum version" of Maxwell's equations. This name can be a little misleading; in fact, the matter is right there - it is just represented as a distribution of charged [elementary particles](https://en.wikipedia.org/wiki/Elementary_particle) by the *volume* [charge density](https://en.wikipedia.org/wiki/Charge_density) \\(\rho\\), such that the total charge \\(Q\\) inside the volume \\(V\\) is

$$ \tag{1.5}
	Q(t) = \iiint\_{V} \rho(\bm{r}, t) \thinspace dV.
$$

Moving charges form a current. If their velocity is \\(\bm{v}\\), the *volume* [current density](https://en.wikipedia.org/wiki/Current_density) \\(\bm{J}\\) is simply

$$ \tag{1.6}
	\bm{J}(\bm{r}, t) = \rho(\bm{r}, t) \bm{v}(\bm{r}, t).
$$

Of course, the individual electric charges may be located at different points, have different charges and velocities - the density is just a convenient mathematical abstraction.

While this kind of parametrization is simple and useful, due to the sheer number of charges in the bulk matter, you can't see the forest for the trees. Therefore, applied physics often uses a different representation of matter, which we shall now discuss.

First, we identify two types of charges - bound and free. As the name implies, *free* charges are able to effortlessly move around the material, or even outside it, the typical example being the conduction current in a copper wire. The *bound* [current](https://en.wikipedia.org/wiki/Current_density#Polarization_and_magnetization_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Bound_charge), \\(\bm{J\_b}\\) and \\(\rho\_b\\), are more difficult to reason about. They can be characterized in terms of [polarization of matter](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization):

$$ \tag{1.7}
	\bm{J\_b}(\bm{r}, t) = \nabla \times \bm{M}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{P}(\bm{r}, t), \quad
	\rho\_b(\bm{r}, t) = -\nabla \cdot \bm{P}(\bm{r}, t),
$$

where \\(\bm{P}\\) is the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) (electric dipole moment per unit volume), \\(\bm{M}\\) is the [magnetic polarization](https://en.wikipedia.org/wiki/Magnetization) (magnetic dipole moment per unit volume).

Substitution of Equation 1.7.1 into 1.1.3 and 1.7.2 into 1.1.4 yields

$$ \tag{1.8}
\begin{aligned}
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E}(\bm{r}, t) \big) = \bm{J\_f}(\bm{r}, t) + \nabla \times \bm{M}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{P}(\bm{r}, t), \cr
	&\nabla \cdot \big( \epsilon\_0 \bm{E}(\bm{r}, t) \big) = \rho\_f(\bm{r}, t) - \nabla \cdot \bm{P}(\bm{r}, t).
\end{aligned}
$$

We can group the terms to simplify the expressions:

$$ \tag{1.9}
\begin{aligned}
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) - \bm{M}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t) \big) = \bm{J\_f}(\bm{r}, t), \cr
	&\nabla \cdot \big( \epsilon\_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t) \big) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

A pattern begins to emerge. If we define the [electric displacement](https://en.wikipedia.org/wiki/Electric_displacement_field) \\(\bm{D}\\) and the [magnetic intensity](https://en.wikipedia.org/wiki/Magnetic_field#The_H-field) \\(\bm{H}\\) as

$$ \tag{1.10}
	\bm{D}(\bm{r}, t) = \epsilon\_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t), \quad 
	\bm{H}(\bm{r}, t) = \mu\_0^{-1} \bm{B}(\bm{r}, t) - \bm{M}(\bm{r}, t),
$$

substitute them into Equations 1.9.1 and 1.9.2, and group the resulting expressions with Equations 1.1.1 and 1.1.2, we obtain the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) of Maxwell's equations \[[6](#references) (ch. 1.1.1), [7](#references) (ch. 2.1), [8](#references) (ch. 2.1)\]:

$$ \tag{1.11}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, t) - \frac{\partial}{\partial t} \bm{D}(\bm{r}, t) = \bm{J\_f}(\bm{r}, t), &
	&\nabla \cdot  \bm{D}(\bm{r}, t) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

As discussed in \[[5](#references) (vol. II, ch 32.2), [6](#references) (ch. 1.1.1)\], \\(\bm{E}\\) and \\(\bm{B}\\) are considered fundamental fields, and \\(\bm{D}\\) and \\(\bm{H}\\) are [auxiliary fields](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) that arise due to the influence of matter. We shall soon see that, in many cases, they are related in a simple way.

On the conceptual level, we would like the properties of matter to be continuous everywhere, which leads to continuous electromagnetic fields. But, sometimes, it is convenient to introduce a discontinuity to approximate a very rapid (yet continuous) variation of optical properties. At the *optical interface*, the fields must satisfy the [boundary conditions](https://en.wikipedia.org/wiki/Interface_conditions_for_electromagnetic_fields) \[[6](#references) (ch. 1.1.3), [7](#references) (ch. 2.2), [8](#references) (ch. 2.4)\]. If \\(\bm{n\_{12}}\\) is a unit normal vector pointing from region 1 to region 2, the normal components of the fields must be such that

$$ \tag{1.12}
	\bm{n\_{12}} \cdot  (\bm{B\_2} - \bm{B\_1}) = 0, \quad
	\bm{n\_{12}} \cdot  (\bm{D\_2} - \bm{D\_1}) = \rho\_n,
$$

where \\(\rho\_n\\) is the *surface* charge density. For the tangential components, it can be shown that

$$ \tag{1.13}
	\bm{n\_{12}} \times (\bm{E\_2} - \bm{E\_1}) = 0, \quad
	\bm{n\_{12}} \times (\bm{H\_2} - \bm{H\_1}) = \bm{J\_n},
$$

where \\(\bm{J\_n}\\) is the *surface* current density.

We take Maxwell's equations as axioms and use them as a foundation on which we base the theory presented in the following sections.

## Maxwell's Equations in the Frequency Domain

Maxwell's equations can be simplified by transforming the fields from the time to the frequency domain.

Define[^1] the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of the electric vector field \\(\bm{E}(\bm{r}, t)\\) as

[^1]: The choice of the sign of the complex exponential is arbitrary, and determines the sign of the imaginary components of both the complex permittivity and the complex refractive index.

$$ \tag{2.1}
	\bm{E}(\bm{r}, \omega) = \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{-i \omega t} dt,
$$

where \\(\omega\\) is the *angular frequency*. \\(\bm{E}(\bm{r}, \omega)\\) is the electric [phasor](https://en.wikipedia.org/wiki/Phasor) field - a field of complex (phase) vectors[^2].

[^2]: This implies that all factors comprising the expression of the complex field are also complex.

\\(\bm{E}(\bm{r}, t)\\), on the other hand, is real, which means its Fourier transform is [Hermitian](https://en.wikipedia.org/wiki/Hermitian_function):

$$ \tag{2.2}
	\bm{E}(\bm{r}, \omega) = [\bm{E}(\bm{r}, -\omega)]^{\*},
$$

where the star denotes the [complex conjugate](https://en.wikipedia.org/wiki/Complex_conjugate)

$$ \tag{2.3}
	z^{\*} = \big[ r e^{i \theta} \big]^{\*} = r e^{-i \theta}.
$$

[Insert picture of a FT of a real function here]

We can go back to the time domain by using the inverse Fourier transform:

$$ \tag{2.4}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega
	+ \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{0} [\bm{E}(\bm{r}, -\omega)]^{\*} e^{i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \big( \bm{E}(\bm{r}, \omega) e^{i \omega t} + [\bm{E}(\bm{r}, \omega)]^{\*} e^{-i \omega t} \big) d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \big( \bm{E}(\bm{r}, \omega) e^{i \omega t} + \big[ \bm{E}(\bm{r}, \omega) e^{i \omega t} \big]^{\*} \big) d\omega \cr
	&= \sqrt{\frac{2}{\pi}} \int\_{0}^{\infin} \mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega
\end{aligned}
$$

which comes from the definition of the real part of a complex number:

$$ \tag{2.5}
	\mathrm{Re} \lbrace z \rbrace = \frac{1}{2} \big( z + z^{\*} \big) = \frac{1}{2} \big(r e^{i \theta} + r e^{-i \theta} \big).
$$

We can define integral forms of the fields by replacing \\(\bm{E}\\) by \\(\bm{B}\\), \\(\bm{D}\\), \\(\bm{H}\\), \\(\bm{J\_f}\\) or \\(\rho\_f\\) in Equation 2.4. If we substitute these integrals into Equations 1.11, and if we assume that we can perform [differentiation under the integral sign](https://en.wikipedia.org/wiki/Leibniz_integral_rule), the integrands can be expressed as

$$ \tag{2.6}
\begin{aligned}
	&\nabla \times \big( \bm{E}(\bm{r}, \omega) e^{i \omega t} \big) + \frac{\partial}{\partial t} \big( \bm{B}(\bm{r}, \omega) e^{i \omega t} \big) = 0, &
	&\nabla \cdot  \big( \bm{B}(\bm{r}, \omega) e^{i \omega t} \big) = 0, \cr
	&\nabla \times \big( \bm{H}(\bm{r}, \omega) e^{i \omega t} \big) - \frac{\partial}{\partial t} \big( \bm{D}(\bm{r}, \omega) e^{i \omega t} \big) = \bm{J\_f}(\bm{r}, \omega) e^{i \omega t}, &
	&\nabla \cdot  \big( \bm{D}(\bm{r}, \omega) e^{i \omega t} \big) = \rho\_f(\bm{r}, \omega) e^{i \omega t}.
\end{aligned}
$$

Performing differentiation with respect to time and division by the pervasive \\(e^{i \omega t}\\) factor yields Maxwell's equations for monochromatic fields \[[7](#references) (ch. 2.3), [8](#references) (ch. 2.2)\]:

$$ \tag{2.7}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \bm{D}(\bm{r}, \omega) = \bm{J\_f}(\bm{r}, \omega), &
	&\nabla \cdot  \bm{D}(\bm{r}, \omega) = \rho\_f(\bm{r}, \omega).
\end{aligned}
$$

Dependence on time is completely gone, which is advantageous since we are interested in the steady state of the field measured over the time interval that is large in comparison to the period of an oscillation.

If we take divergence of Equation 2.7.3, combine the result with Equation 2.7.4, and use the fact that [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, we obtain the [continuity equation](https://en.wikipedia.org/wiki/Continuity_equation) that expresses the [conservation of charge](https://en.wikipedia.org/wiki/Charge_conservation):

$$ \tag{2.8}
	\nabla \cdot \bm{J\_f}(\bm{r}, \omega) = - i \omega \rho\_f(\bm{r}, \omega).
$$

For future convenience, we shall define the decomposition of the *free* charges and currents into the *source* and the *induced* parts \[[8](#references) (ch. 2.2)\]:

$$ \tag{2.9}
	\rho\_f = \rho\_s + \rho\_i, \quad
	\bm{J\_f} = \bm{J\_s} + \bm{J\_i}.
$$

Continuity of charge implies that

$$ \tag{2.10}
	\rho\_s(\bm{r}, \omega) = \frac{i}{\omega} \nabla \cdot \bm{J\_s}(\bm{r}, \omega), \quad
	\rho\_i(\bm{r}, \omega) = \frac{i}{\omega} \nabla \cdot \bm{J\_i}(\bm{r}, \omega).
$$

## Constitutive Relations

Considered in isolation, Maxwell's system has fewer equations than unknowns. This makes it necessary to specify the way the material responds to the electromagnetic field. One way to do that is to express the auxiliary fields in terms of polarization of matter (as per Equations 1.7 and 1.10). Turns out, this leads to a non-linear solution \[[9](#references)\] that is relatively difficult to manipulate.

Fortunately, many materials respond to weak electromagnetic fields in optical frequencies in an approximately linear manner. This behavior is captured by the [constitutive relations](https://en.wikipedia.org/wiki/Constitutive_equation#Electromagnetism) \[[6](#references) (ch. 1.1.2), [7](#references) (ch. 2.1), [8](#references) (ch. 2.3)\]:

$$ \tag{3.1}
\begin{aligned}
	&\bm{J\_i}(\bm{r}, \omega) \approx \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{D}   (\bm{r}, \omega) \approx \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{B}   (\bm{r}, \omega) \approx \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega).
\end{aligned}
$$

We can also combine Equations 2.10.2 and 3.1.1 to derive the relation of the induced charge density:

$$ \tag{3.2}
	\rho\_i(\bm{r}, \omega) \approx \frac{i}{\omega} \nabla \cdot \big( \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big).
$$

In general, media may be *dispersive* (dependent on the frequency), *lossy* (absorptive), and *anisotropic* (dependent on the orientation). This means that the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity) \\(\hat{\sigma}\\), the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity) \\(\hat{\epsilon}\\), and the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \\(\hat{\mu}\\) are frequency-dependent complex [tensor fields](https://en.wikipedia.org/wiki/Tensor#Tensor_fields). For instance,

$$ \tag{3.3}
	\bm{D}(\bm{r}, \omega) \approx \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) =
	\begin{bmatrix}
		\epsilon\_{11} & \epsilon\_{12} & \epsilon\_{13} \cr
		\epsilon\_{21} & \epsilon\_{22} & \epsilon\_{23} \cr
		\epsilon\_{31} & \epsilon\_{32} & \epsilon\_{33}
	\end{bmatrix}
	\begin{bmatrix}
		E\_x \cr
		E\_y \cr
		E\_z
	\end{bmatrix}.
$$

After substitution of the constitutive relations, Maxwell's equations take the following form:

$$ \tag{3.4}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \cdot  \big( \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J\_s}(\bm{r}, \omega) + \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\nabla \cdot  \big( \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega) + \frac{i}{\omega} \nabla \cdot \big( \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big).
\end{aligned}
$$

Using the definition of the [complex permittivity](https://en.wikipedia.org/wiki/Permittivity#Complex_permittivity)

$$ \tag{3.5}
	\hat{\varepsilon}(\bm{r}, \omega) = \hat{\epsilon}(\bm{r}, \omega) - \frac{i}{\omega} \hat{\sigma}(\bm{r}, \omega),
$$

we obtain Maxwell's equations for *linear* media:

$$ \tag{3.6}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J\_s}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega).
\end{aligned}
$$

## Helmholtz's Equation

Consider a region of space without any source currents or charges. Physically, this means that there are no sources of fields; but it doesn't mean the field contains no energy. The idea is to explore all solutions of Maxwell's equations (we can pick a particular solution once we specify the source), and determine how these solutions evolve over time.

Setting \\(\bm{J\_f} = \rho\_f = 0\\) in Equations 3.6, we obtain a coupled system of homogeneous linear differential equations:

$$ \tag{4.1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = 0.
\end{aligned}
$$

Mathematically, the consequence is that a linear combination of several solutions is also a valid solution.

Assume that the medium is *homogeneous* (constant in space)[^3]:

[^3]: See \[[6](#references) (ch. 1.2)\] for an inhomogeneous solution. The stationary approximation is valid for optical frequencies because the time period of an oscillation is very short. 

$$ \tag{4.2}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{E}(\bm{r}, \omega) = 0.
\end{aligned}
$$

Let us focus on the electric phasor \\(\bm{E}\\). Take curl of Equation 4.2.1 and substitute Equation 4.2.3:

$$ 
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) + i \omega \nabla \times \big( \hat{\mu}(\omega) \bm{H}(\bm{r}, \omega) \big) = 0
$$

$$ 
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) + i \omega \nabla \times \bm{B}(\bm{r}, \omega) = 0
$$

Now what? Fix me!!! Check Ishimaru?

$$ \tag{4.3}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\omega) \big( i \omega \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) \big) = 0.
$$

Introduce the [curl-of-curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity

$$ \tag{4.4}
	\nabla \times (\nabla \times \bm{E}) = \nabla (\nabla \cdot \bm{E}) - \nabla^2 \bm{E},
$$

where 

$$ \tag{4.5}
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

$$ \tag{4.6}
	\mathrm{div} \big( \mathrm{grad}(\bm{E}) \big) =
	\nabla^2 \bm{E} =
	(\nabla \cdot \nabla) \bm{E} =
	\frac{\partial^2 \bm{E}}{\partial x^2} + \frac{\partial^2 \bm{E}}{\partial y^2} + \frac{\partial^2 \bm{E}}{\partial z^2}
$$

is the *vector* [Laplace](https://en.wikipedia.org/wiki/Laplace_operator) operator (*scalar* Laplace operator applied to each vector component).

Use it to expand Equation 4.3:

$$ \tag{4.7}
	\nabla \big( \nabla \cdot \bm{E}(\bm{r}, \omega) \big) - \nabla^2 \bm{E}(\bm{r}, \omega) - \omega^2 \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

Substitution of Equation 4.2.4 into 4.7 yields the equation of the electric phasor \\(\bm{E}\\):

$$ \tag{4.8}
	\nabla^2 \bm{E}(\bm{r}, \omega) + \omega^2 \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

Similarly, we can obtain an expression[^4] of the magnetic phasor \\(\bm{B}\\):

[^4]: If you solve for \\(\bm{H}\\) rather than \\(\bm{B}\\), you will obtain an equation with the \\(\hat{\varepsilon} \hat{\mu}\\) term instead of \\(\hat{\mu} \hat{\varepsilon}\\) found in the equation of \\(\bm{E}\\). Since, in general, tensor products do not commute, this means that the \\(\bm{H}\\) field propagates differently from the \\(\bm{E}\\) field, and solving for the \\(\bm{H}\\) field is a mistake.

$$ \tag{4.9}
	\nabla^2 \bm{B}(\bm{r}, \omega) + \omega^2 \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \bm{B}(\bm{r}, \omega) = 0.
$$

Since anisotropy arises due to the [crystal structure](https://en.wikipedia.org/wiki/Crystal_structure) of the material[^5], this tensor product is [normal](https://en.wikipedia.org/wiki/Normal_matrix), and we can perform a [change of basis](https://en.wikipedia.org/wiki/Change_of_basis) to [diagonalize](https://en.wikipedia.org/wiki/Diagonalizable_matrix#How_to_diagonalize_a_matrix) it:

[^5]: A more elaborate argument is presented in \[[6](#references) (ch. 15.1)\].

$$ \tag{4.10}
	\nabla^2 \bm{E}(\bm{r}, \omega) + \omega^2
		\begin{bmatrix}
			\mu\_{1} \varepsilon\_{1} & 0 & 0 \cr
			0 & \mu\_{2} \varepsilon\_{2} & 0 \cr
			0 & 0 & \mu\_{3} \varepsilon\_{3}
		\end{bmatrix} \bm{E}(\bm{r}, \omega) = 0,
$$

which allows us to separate the variables into three *scalar* [Helmholtz's equations](https://en.wikipedia.org/wiki/Helmholtz_equation):

$$ \tag{4.11}
\begin{aligned}
	\big( \nabla^2 + k\_{1}^2(\omega) \big) E\_x(\bm{r}, \omega) = 0, \cr
	\big( \nabla^2 + k\_{2}^2(\omega) \big) E\_y(\bm{r}, \omega) = 0, \cr
	\big( \nabla^2 + k\_{3}^2(\omega) \big) E\_z(\bm{r}, \omega) = 0,
\end{aligned}
$$

where we defined

$$ \tag{4.12}
	k(\omega) = \omega \sqrt{\mu(\omega) \varepsilon(\omega)}.
$$

To find a solution, let us first consider a simpler one-dimensional Helmholtz's equation

$$ \tag{4.13}
	\frac{d^2 s(x)}{d x^2} = -k^2 s(x).
$$

If the derivative of a function is the function itself (times a constant), the function is clearly an exponential:

$$ \tag{4.14}
	s(x) =
	s(0) e^{\pm i k x},
$$

where \\(s(0)\\) is a complex constant.

Extension to three dimensions is straightforward. If we rotate the coordinate frame so that the \\(x\\)-axis points along the unit vector \\(\bm{n}\\),

$$ \tag{4.15}
	s(\bm{r}, \bm{n}) =
	s(0, \bm{n}) e^{ \pm i k (\bm{r} \cdot \bm{n})}.
$$

is a valid solution for a certain value of \\(\bm{n}\\). To avoid clutter, we shall adhere to a common convention with the negative sign; the positive solution can be obtained by reversing the direction of \\(\bm{n}\\).

How should we choose the direction of \\(\bm{n}\\)? It depends on the location of sources and optical interfaces. In general, we can use *any* value of \\(\bm{n}\\). And, since the Helmholtz equation is both linear and homogeneous, we can actually use *every* value of \\(\bm{n}\\) by invoking the [superposition principle](https://en.wikipedia.org/wiki/Superposition_principle). In our particular case, we can write the general solution as an integral taken over the surface of the unit sphere \\(\mathbb{S}^2\\) [measured](https://en.wikipedia.org/wiki/Lebesgue_integration#Construction) by the [solid angle](https://en.wikipedia.org/wiki/Solid_angle) \\(d\Omega\_n\\):

$$ \tag{4.16}
	s(\bm{r}) =
	\oiint\_{\mathbb{S}^2} s(\bm{r}, \bm{n}) \thinspace d\Omega\_n =
	\oiint\_{\mathbb{S}^2} s(0, \bm{n}) e^{-i k (\bm{r} \cdot \bm{n})} d\Omega\_n.
$$

Equation 4.16 can be used to solve Equation 4.11:

$$ \tag{4.17}
	\bm{E}(\bm{r}, \omega) = \oiint\_{\mathbb{S}^2}
	\begin{bmatrix}
		E\_x(0, \bm{n}, \omega) \exp(-i k\_1(\omega) (\bm{r} \cdot \bm{n})) \cr
		E\_y(0, \bm{n}, \omega) \exp(-i k\_2(\omega) (\bm{r} \cdot \bm{n})) \cr
		E\_z(0, \bm{n}, \omega) \exp(-i k\_3(\omega) (\bm{r} \cdot \bm{n}))
	\end{bmatrix}
	d\Omega\_n.
$$

That is a solution of Maxwell's equations in the frequency domain.

## Time-Harmonic Waves

We can find a solution of Maxwell's equations in the time domain by substituting Equation 4.17 into 2.4:

$$ \tag{5.1}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	&= \sqrt{\frac{2}{\pi}} \int\_{0}^{\infin} \mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega \cr
	&= \sqrt{\frac{2}{\pi}} \int\_{0}^{\infin} \oiint\_{\mathbb{S}^2} \mathrm{Re} \Bigg\lbrace
	\begin{bmatrix}
		E\_x(0, \bm{n}, \omega) \exp(-i k\_1(\omega) (\bm{r} \cdot \bm{n}) + i \omega t) \cr
		E\_y(0, \bm{n}, \omega) \exp(-i k\_2(\omega) (\bm{r} \cdot \bm{n}) + i \omega t) \cr
		E\_z(0, \bm{n}, \omega) \exp(-i k\_3(\omega) (\bm{r} \cdot \bm{n}) + i \omega t)
	\end{bmatrix}
	\Bigg\rbrace d\Omega\_n \thinspace d\omega.
\end{aligned}
$$

This double integral represents a [wave packet](https://en.wikipedia.org/wiki/Wave_packet). It is a collection of time-harmonic *vector* [plane waves](https://en.wikipedia.org/wiki/Plane_wave) \[[6](#references) (ch. 1.4.2), [7](#references) (ch. 3.1), [8](#references) (ch. 3.2)\] of the form

$$ \tag{5.2}
\begin{bmatrix}
	E\_x(0, \bm{n}, \omega) \exp(-i k\_1(\omega) (\bm{r} \cdot \bm{n}) + i \omega t) \cr
	E\_y(0, \bm{n}, \omega) \exp(-i k\_2(\omega) (\bm{r} \cdot \bm{n}) + i \omega t) \cr
	E\_z(0, \bm{n}, \omega) \exp(-i k\_3(\omega) (\bm{r} \cdot \bm{n}) + i \omega t)
\end{bmatrix}
$$

each composed of three *scalar* plane waves such as

$$ \tag{5.3}
	E(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} e^{i \omega t}
$$

expressed in terms the complex [wave number](https://en.wikipedia.org/wiki/Wavenumber) \\(k\\) (see Equation 4.12):

$$ \tag{5.4}
	k(\omega) = \omega \sqrt{\mu(\omega) \varepsilon(\omega)}.
$$

If we define the [relative permittivity](https://en.wikipedia.org/wiki/Relative_permittivity)[^6] \\(\varepsilon\_r\\) and the [relative permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)#Relative_permeability_and_magnetic_susceptibility) \\(\mu\_r\\) using vacuum as reference,

[^6]: Historically called the [relative complex dielectric constant](https://en.wikipedia.org/wiki/Relative_permittivity#Lossy_medium).

$$ \tag{5.5}
	\varepsilon\_r(\omega) = \frac{\varepsilon(\omega)}{\epsilon\_0}, \quad
	\mu\_r(\omega) = \frac{\mu(\omega)}{\mu\_0},
$$

the square root of Equation 5.4 can be expressed as

$$ \tag{5.6}
	\sqrt{\varepsilon(\omega) \mu(\omega)} =
	\sqrt{\epsilon\_0 \mu\_0} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	\frac{1}{c} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)}.
$$

It is convenient to use a parametrization that does not involve taking a square root. Thus, we define two positive real numbers, the [refractive index](https://en.wikipedia.org/wiki/Refractive_index) \\(\eta\\) and the [attenuation index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\kappa\\) \[[7](#references) (ch. 3.1), [8](#references) (ch. 2.3)\], by

$$ \tag{5.7}
	\eta(\omega) - i \kappa(\omega) =
	\sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	c \sqrt{\epsilon(\omega) \mu(\omega) - \frac{i}{\omega} \sigma(\omega) \mu(\omega)}.
$$

For high frequencies such as those encountered in optics, we may perform a [Laurent series](https://en.wikipedia.org/wiki/Laurent_series) [expansion](https://www.wolframalpha.com/input/?i=series+sqrt%28a-I%2Fw*b%29) at infinity:

$$ \tag{5.8}
	\eta - i \kappa = 
	c \sqrt{\epsilon \mu - \frac{i}{\omega} \sigma \mu} \approx
	c \Big( \sqrt{\epsilon \mu} - i \frac{\sigma \mu}{2 \omega \sqrt{\epsilon \mu}} \Big),
$$

which gives an approximate mapping between the optical and the physical parameters[^7].

If the refractive index \\(\eta\\) of the material has axial dependence, the crystal is said to be doubly refractive or [birefringent](https://en.wikipedia.org/wiki/Birefringence). If the attenuation index \\(\kappa\\) varies instead, the crystal is called [dichroic](https://en.wikipedia.org/wiki/Dichroism) (from the Greek *dikhroos*, two-colored).

[^7]: Keep in mind that, in general, the permittivity, the permeability, and the conductivity are complex.

In order to develop some intuition about the role of the refractive index \[[5](#references) (vol. II, ch. 32.4)\], consider the phasor component of a scalar plane wave

$$ \tag{5.9}
\begin{aligned}
	E(\bm{r}, \bm{n}, \omega)
	&= E\_0 e^{-i k (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{i \delta} e^{-i (\omega / c) (\eta - i \kappa) (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{-\omega (\kappa / c) (\bm{r} \cdot \bm{n})} e^{i \delta - i \omega (\eta / c) (\bm{r} \cdot \bm{n})} \cr
	&= |E(\bm{r}, \bm{n}, \omega)| e^{i \phi(\bm{r}, \bm{n}, \omega)}.
\end{aligned}
$$

As any complex number, it can be expressed in terms of the *magnitude* \\(|E|\\)  and the *phase* \\(\phi\\), both of which are [real-valued functions](https://en.wikipedia.org/wiki/Real-valued_function).

Multiplication by \\(e^{i \omega t}\\) by yields the full expression of a scalar plane wave:

$$ \tag{5.10}
\begin{aligned}
	E(\bm{r}, \bm{n}, \omega) e^{i \omega t}
	&= |E\_0| e^{-\omega (\kappa / c) (\bm{r} \cdot \bm{n})} e^{i \delta - i \omega (\eta / c) (\bm{r} \cdot \bm{n})} e^{i \omega t} \cr
	&= |E\_0| e^{-\omega (\kappa / c) (\bm{r} \cdot \bm{n})} e^{i \delta + i \omega (t - (\eta / c) (\bm{r} \cdot \bm{n}))}.
\end{aligned}
$$

Let's perform [dimensional analysis](https://en.wikipedia.org/wiki/Dimensional_analysis) of Equation 5.10. Begin by taking the [argument](https://en.wikipedia.org/wiki/Argument_(complex_analysis)) of the expression

$$ \tag{5.11}
	\theta(\bm{r}, t) =
	\mathrm{Arg} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace =
	\delta - \omega (\eta / c) \big(\bm{r} \cdot \bm{n} - (c / \eta) t \big).
$$

Notice that, for any \\(\Delta t\\),

$$ \tag{5.12}
	\theta(\bm{r}, t) = \theta(\bm{r} + \bm{n} (c / \eta) \Delta t, \thinspace t + \Delta t).
$$

This implies that \\(\theta\\) represents a plane propagating along its normal \\(\bm{n}\\) at the [phase velocity](https://en.wikipedia.org/wiki/Phase_velocity) \\(c / \eta\\).

Taking the real part of Equation 5.10 allows us to uncover the *wave amplitude*

$$ \tag{5.13}
 	\mathrm{Re} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace = 
 	|E(0, \bm{n}, \omega)| e^{-\omega (\kappa / c) (\bm{r} \cdot \bm{n})} \cos{\theta}.
$$

If the absorption index \\(\kappa = 0\\), Equation 5.13 represents a regular sine wave.

[Insert Picture Here]

On the other hand, \\(\kappa > 0\\) produces an exponential decay characteristic of an [evanescent wave](https://en.wikipedia.org/wiki/Evanescent_field).

[Insert Picture Here]

To determine how the electric and the magnetic fields of a monochromatic plane wave are related, consider a single directional component of the electric phasor \\(\bm{E}\\). Take curl of the integrand of Equation 4.17

$$ \tag{5.14}
\begin{aligned}
	\nabla \times \bm{E}(\bm{r}, \bm{n}, \omega)
	&= \begin{bmatrix}
			-i n\_y k\_3(\omega) E\_z(\bm{r}, \bm{n}, \omega) + i n\_z k\_2(\omega) E\_y(\bm{r}, \bm{n}, \omega) \cr
			-i n\_z k\_1(\omega) E\_x(\bm{r}, \bm{n}, \omega) + i n\_x k\_3(\omega) E\_z(\bm{r}, \bm{n}, \omega) \cr
			-i n\_x k\_2(\omega) E\_y(\bm{r}, \bm{n}, \omega) + i n\_y k\_1(\omega) E\_x(\bm{r}, \bm{n}, \omega)
		\end{bmatrix} \cr
	&= -\bm{n} \times
		\begin{bmatrix}
			i k\_{1}(\omega) & 0 & 0 \cr
			0 & i k\_{2}(\omega) & 0 \cr
			0 & 0 & i k\_{3}(\omega)
		\end{bmatrix} \bm{E}(\bm{r}, \bm{n}, \omega)
\end{aligned}
$$

and substitute it into Equation 2.7.1:

$$ \tag{5.15}
	\bm{n} \times
	\begin{bmatrix}
		i k\_{1}(\omega) & 0 & 0 \cr
		0 & i k\_{2}(\omega) & 0 \cr
		0 & 0 & i k\_{3}(\omega)
	\end{bmatrix} \bm{E}(\bm{r}, \bm{n}, \omega)
	= i \omega \bm{B}(\bm{r}, \bm{n}, \omega).
$$

If we recall the definition of \\(k\\) given by Equation 4.12, it is apparent that

$$ \tag{5.16}
	\bm{n} \times \Big( \big( \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \big)^{\frac{1}{2}} \bm{E}(\bm{r}, \bm{n}, \omega) \Big) = \bm{B}(\bm{r}, \bm{n}, \omega),
$$

or, alternatively,

$$ \tag{5.16}
	\bm{n} \times \Big( \big( \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \big)^{\frac{1}{2}} \bm{E}(\bm{r}, \bm{n}, \omega) \Big) = \bm{B}(\bm{r}, \bm{n}, \omega),
$$

\\(\bm{n}\\) is a real vector, while the phasors \\(\bm{E}\\) and \\(\bm{B}\\) are complex. Using the distributive property of the cross product,

$$ \tag{5.17}
	\bm{n} \times \mathrm{Re} \big\lbrace \big( \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \big)^{\frac{1}{2}} \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	= \mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace.
$$

A similar derivation for \\(\bm{B}\\) using Equations 3.1.3 and 4.2.3 leads to

$$ \tag{5.18}
	\bm{n} \times \mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	=  -\mathrm{Re} \big\lbrace \big( \hat{\mu}(\omega) \hat{\varepsilon}(\omega) \big)^{\frac{1}{2}} \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace.
$$

Both Equations 5.17 and 5.18 are simultaneously true only if these three vectors are mutually orthogonal. Thus, they define the geometric configuration of a plane wave.

[Insert Picture Here]

## Force, Energy, and Radiometry

Given a mathematical description of electromagnetic radiation in terms of vector waves, we would like to physically characterize it as an energy transfer process. In order to do that, we have to determine how much energy there is in a given volume element of space, and also the rate of energy flow \[[5](#references) (vol. II, ch. 27), [6](#references) (ch. 1.1.4), [7](#references) (ch. 2.4), [8](#references) (ch. 2.5)\].

[Conservation of energy](https://en.wikipedia.org/wiki/Conservation_of_energy) is one of the most important principles of physics. Here is one way to state it: the difference between the amount of external energy \\(\mathcal{E\_{ext}}\\) flowing into the volume \\(V\\) and the amount of internal energy \\(\mathcal{E\_{int}}\\) flowing out equals the amount of work \\(\mathcal{W}\\) done inside.

$$ \tag{6.1}
 	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) -
 	\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
 	\frac{\partial}{\partial t} \mathcal{W}(V, t).
$$

In Equation 6.1, \\(\mathcal{E}\\) refers to a single type of energy: kinetic, electromagnetic, etc. Work transforms a portion of \\(\mathcal{E}\\) into a different type of energy: kinetic into potential, electromagnetic into thermal, and so on. If we account for all types of energy, the total amount of energy is conserved.

Given our focus on electromagnetic energy, we can partition the total amount of energy into the *field energy* and the *matter energy*. In this context, work done by the field on the matter refers to *absorption*, and has a positive sign. Similarly, *emission* is the work done by the matter on the field, and has a negative sign. We shall not consider emissive materials in the analysis presented below.

Mathematically, the [rate of doing work](https://en.wikipedia.org/wiki/Work_(physics)#Mathematical_calculation) is the product of force and velocity:

$$ \tag{6.2}
	\frac{\partial \mathcal{W}}{\partial t} = \bm{F} \cdot \bm{v}.
$$ 

Thus, to compute it, we must recall the definition of the electromagnetic force.

If a particle with the electric charge \\(q\\) moving at the velocity \\(\bm{v}\\) is placed in an electromagnetic field, it experiences the [Lorentz force](https://en.wikipedia.org/wiki/Lorentz_force)

$$ \tag{6.3}
	\bm{F}(\bm{r}, t) = q(\bm{r}, t) \big( \bm{E}(\bm{r}, t) + \bm{v}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big).
$$

For the charge density \\(\rho\\) and the volume element \\(dV\\), the corresponding equation is

$$ \tag{6.4}
	d\bm{F}(\bm{r}, t) = \rho(\bm{r}, t) \big( \bm{E}(\bm{r}, t) + \bm{v}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \thinspace dV.
$$

Therefore, the amount of work done per unit time by the field on the matter is

$$ \tag{6.5}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
	\iiint\_{V} \bm{v}(\bm{r}, t) \cdot d\bm{F}(\bm{r}, t) =
    \iiint\_{V} \bm{E}(\bm{r}, t) \cdot \rho(\bm{r}, t) \bm{v}(\bm{r}, t) \thinspace dV.
$$

Since the current density is just a collection of moving charges per unit volume (see Equation 1.6),

$$ \tag{6.6}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
    \iiint\_{V} \bm{E}(\bm{r}, t) \cdot \bm{J}(\bm{r}, t) \thinspace dV.
$$

Maxwell's equations allow us to express a current in terms of fields. Substitution of Equation 1.1.3 yields

$$ \tag{6.7}
	\iiint\_{V} \bm{E} \cdot \bm{J} \thinspace dV = 
    \iiint\_{V} \bm{E} \cdot \Big( \nabla \times \big( \mu\_0^{-1} \bm{B} \big) -
    \frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E} \big) \Big) dV.
$$

Since [divergence of cross product](https://en.wikipedia.org/wiki/Vector_calculus_identities#Cross_product_rule) is

$$ \tag{6.8}
	\nabla \cdot (\bm{E} \times \bm{B}) = (\nabla \times \bm{E}) \cdot \bm{B} - \bm{E} \cdot (\nabla \times \bm{B}),
$$

we can reformulate the integrand of Equation 6.7 as

$$ \tag{6.9}
	\bm{E} \cdot \bm{J} = 
    \mu\_0^{-1} \big( (\nabla \times \bm{E}) \cdot \bm{B} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
    \epsilon\_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

The curl of \\(\bm{E}\\) is also given by Maxwell's equations (see Equation 1.1.1). Thus we can write

$$ \tag{6.10}
	\bm{E} \cdot \bm{J} = 
    \mu\_0^{-1} \big( {-\bm{B}} \cdot \frac{\partial \bm{B}}{\partial t} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
    \epsilon\_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

Moving the dot products under the derivative sign and grouping the derivatives produces a simpler expression

$$ \tag{6.11}
	\bm{E} \cdot \bm{J} =
	-\mu\_0^{-1} \nabla \cdot (\bm{E} \times \bm{B}) -
	\frac{\partial}{\partial t} \Big( \frac{\epsilon\_0}{2} (\bm{E} \cdot \bm{E}) +
	\frac{\mu\_0^{-1}}{2} (\bm{B} \cdot \bm{B}) \Big).
$$

We can observe that the rate of doing work is a balance of inflow of \\((\bm{E} \times \bm{B})\\) and the rate of change of the squared magnitudes of the fields. The physical significance of this expression becomes more apparent if we return to the integral form

$$ \tag{6.12}
	\frac{\partial \mathcal{W}}{\partial t} = 
    \iiint\_{V} -\mu\_0^{-1} \nabla \cdot (\bm{E} \times \bm{B}) \thinspace dV -
    \frac{\partial}{\partial t} \iiint\_{V} \Big( \frac{\epsilon\_0}{2} E^2 + \frac{\mu\_0^{-1}}{2} B^2 \Big) dV 
$$

and use the [divergence theorem](https://en.wikipedia.org/wiki/Divergence_theorem) to replace the leftmost volume integral with an integral taken over the bounding surface \\(\delta V\\) of the volume \\(V\\):

$$ \tag{6.13}
	\frac{\partial \mathcal{W}}{\partial t} = 
    \oiint\_{\delta V} \mu\_0^{-1} (\bm{E} \times \bm{B}) \cdot (-\bm{n}) \thinspace dA -
    \frac{\partial}{\partial t} \iiint\_{V} \Big( \frac{\epsilon\_0}{2} E^2 + \frac{\mu\_0^{-1}}{2} B^2 \Big) dV,
$$

where \\(\bm{n}\\) is the outward-facing surface normal.

According to Equation 6.1, the right-hand side of Equation 6.14 represents the difference between the rates of inflow and outflow of energy. Thus, the first term on the right gives the amount of external energy (per unit time) flowing through the bounding surface into the volume,

$$ \tag{6.14}
 	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) = 
 	\oiint\_{\delta V} \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \cdot (-\bm{n}) \thinspace dA,
$$

and the second term corresponds to the rate at which the amount of energy within the volume decreases:

$$ \tag{6.15}
 	-\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
 	-\frac{\partial}{\partial t} \iiint\_{V} \Big( \frac{\epsilon\_0}{2} E^2(\bm{r}, t) + \frac{\mu\_0^{-1}}{2} B^2(\bm{r}, t) \Big) dV.
$$

According to this interpretation, the *squared* magnitudes of the fields

$$ \tag{6.16}
	\frac{\partial}{\partial V} \mathcal{E\_e}(\bm{r}, t) = \frac{\epsilon\_0}{2} E^2(\bm{r}, t), \quad
 	\frac{\partial}{\partial V} \mathcal{E\_m}(\bm{r}, t) = \frac{\mu\_0^{-1}}{2} B^2(\bm{r}, t), \quad
$$

are the electric and the magnetic energy densities, and

$$ \tag{6.17}
	\bm{S}(\bm{r}, t) = \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
$$

is the [Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector) that represents the direction and the rate of energy flow[^8].

[^8]: This definition of field energy, while widely accepted, is somewhat ambiguous \[[5](#references) (vol. II, ch. 27.4)\]. We should also note that there is yet another popular definition in terms of the auxiliary fields \[[6](#references) (ch. 1.1.4)\]. There are several problems with the latter definition. First, both the Maxwell's equations and the Lorentz force are given in terms of the fundamental fields, \\(\bm{E}\\) and \\(\bm{B}\\). Secondly, these are also the fields that should be used to define a plane wave, as we have noted previously. And finally, most derivations that start from the macroscopic Maxwell's equations make a mistake of omitting the bound current from the definition of work; taking the bound current into account produces precisely the results at we have arrived above.

We are particularly interested in a steady-state solution of Maxwell's equations. Specifically, rather than consider the *instantaneous* Poynting vector \\(\bm{S}\\) (which may change trillions of times per second), we focus on the *time-averaged* Poynting vector instead:

$$ \tag{6.18} \langle \bm{S\_t} \rangle = \frac{1}{T} \int\_{-T/2}^{\thinspace T/2} \bm{S}(\bm{r}, t + t') \thinspace dt'. $$

It becomes especially useful once you consider a fixed direction \\(\bm{n}\\). Then, according to Equation 6.14,

$$ \tag{6.19}
	\mathtt{E\_e}(\bm{r}, \bm{n}, t)
	= \vert \langle \bm{S\_t} \rangle \cdot \bm{n} \vert
	= \vert \langle \bm{S\_t} \rangle \cos{\theta} \vert $$

is the time-averaged amount of energy per second per unit area that flows through a surface with the normal \\(\bm{n}\\). That is the definition of [irradiance](https://en.wikipedia.org/wiki/Irradiance) \\(\mathtt{E\_e}\\).

---

$$
	\bm{S}(\bm{r}, \omega)
	= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{S}(\bm{r}, t) e^{-i \omega t} dt,
$$

$$
	\bm{S}(\bm{r}, \omega)
	= \frac{\mu\_0^{-1}}{(2 \pi)^{\frac{3}{2}}} \int\_{-\infin}^{\infin}  \Bigg( \int\_{-\infin}^{\infin} \mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega \Bigg)
	\times \Bigg( \int\_{-\infin}^{\infin} \mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega \Bigg) e^{-i \omega t} dt
$$

$$
	\bm{S}(\bm{r}, \omega)
	= \frac{\mu\_0^{-1}}{(2 \pi)^{\frac{3}{2}}} \iiint\_{-\infin}^{\infin}
		\mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega') e^{i \omega' t} \big\rbrace
	\times \mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \omega'') e^{i \omega'' t} \big\rbrace e^{-i \omega t} dt \thinspace d\omega' d\omega''
$$

!!! EXPAND USING COMPLEX CONJUGATE !!!

$$
\begin{aligned}
	S\_x(\bm{r}, \omega)
	= \frac{\mu\_0^{-1}}{(2 \pi)^{\frac{3}{2}}} \iiint\_{-\infin}^{\infin} \big(
		|E\_y(\bm{r}, \omega')| \cos(\phi'\_y(\bm{r}, \omega') + \omega' t)
		|B\_z(\bm{r}, \omega'')| \cos(\phi''\_z(\bm{r}, \omega'') + \omega'' t) \cr
		- |E\_z(\bm{r}, \omega')| \cos(\phi'\_z(\bm{r}, \omega') + \omega' t)
		|B\_y(\bm{r}, \omega'')| \cos(\phi''\_y(\bm{r}, \omega'') + \omega'' t)
	\big) e^{-i \omega t} dt \thinspace d\omega' \thinspace d\omega''
\end{aligned}
$$

Now, consider the inner integral over t and move all time-invariant constants outside

$$
	\int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, \omega') + \omega' t)
		\cos(\phi''\_z(\bm{r}, \omega'') + \omega'' t) e^{-i \omega t} dt 
$$

Since

$$
	\cos(\alpha) \cos(\beta) = \frac{1}{2} \big( \cos(\alpha + \beta) + \cos(\alpha - \beta) \big)
$$

consider

$$
	\int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, \omega') + \phi''\_z(\bm{r}, \omega'') + (\omega' + \omega'') t) e^{-i \omega t} dt
	= \int\_{-\infin}^{\infin} \cos(a + \omega\_0 t) e^{-i \omega t} dt
$$

$$
	\int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, \omega') - \phi''\_z(\bm{r}, \omega'') + (\omega' - \omega'') t) e^{-i \omega t} dt
	= \int\_{-\infin}^{\infin} \cos(b + \omega\_1 t) e^{-i \omega t} dt
$$

Now https://en.wikipedia.org/wiki/Fourier_transform#Functional_relationships,_one-dimensional


$$
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \cos(\omega\_0 t) e^{-i \omega t} dt
	= \frac{1}{2} \big( \delta(\omega + \omega\_0) + \delta(\omega - \omega\_0) \big)
$$

$$
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \sin(\omega\_0 t) e^{-i \omega t} dt
	= \frac{i}{2} \big(\delta(\omega + \omega\_0) - \delta(\omega - \omega\_0) \big)
$$

so

$$
\begin{aligned}
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \cos(a + \omega\_0 t) e^{-i \omega t} dt
	&= \frac{1}{2} \cos(a) \big( \delta(\omega + \omega\_0) + \delta(\omega - \omega\_0) \big) \cr
	&- \frac{i}{2} \sin(a) \big( \delta(\omega + \omega\_0) - \delta(\omega - \omega\_0) \big)
\end{aligned}
$$

and 

$$
\begin{aligned}
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \big( \cos(a + \omega\_0 t) + \cos(b + \omega\_1 t) \big) e^{-i \omega t} dt
	&= \frac{1}{2} \cos(a) \big( \delta(\omega + \omega\_0) + \delta(\omega - \omega\_0) \big) \cr
	&+ \frac{1}{2} \cos(b) \big( \delta(\omega + \omega\_1) + \delta(\omega - \omega\_1) \big) \cr
	&- \frac{i}{2} \sin(a) \big( \delta(\omega + \omega\_0) - \delta(\omega - \omega\_0) \big) \cr
	&- \frac{i}{2} \sin(b) \big( \delta(\omega + \omega\_1) - \delta(\omega - \omega\_1) \big)
\end{aligned}
$$

Applying this to the actual problem,

$$
\begin{aligned}
	\frac{1}{\sqrt{2 \pi}} &\int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, \omega') + \omega' t) \cos(\phi''\_z(\bm{r}, \omega'') + \omega'' t) e^{-i \omega t} dt \cr
	&= \frac{1}{4} \cos(\phi'\_y(\bm{r}, \omega') + \phi''\_z(\bm{r}, \omega'')) \big( \delta(\omega + (\omega' + \omega'')) + \delta(\omega - (\omega' + \omega'')) \big) \cr
	&+ \frac{1}{4} \cos(\phi'\_y(\bm{r}, \omega') - \phi''\_z(\bm{r}, \omega'')) \big( \delta(\omega + (\omega' - \omega'')) + \delta(\omega - (\omega' - \omega'')) \big) \cr
	&- \frac{i}{4} \sin(\phi'\_y(\bm{r}, \omega') + \phi''\_z(\bm{r}, \omega'')) \big( \delta(\omega + (\omega' + \omega'')) - \delta(\omega - (\omega' + \omega'')) \big) \cr
	&- \frac{i}{4} \sin(\phi'\_y(\bm{r}, \omega') - \phi''\_z(\bm{r}, \omega'')) \big( \delta(\omega + (\omega' - \omega'')) - \delta(\omega - (\omega' - \omega'')) \big)
\end{aligned}
$$

and with more integrals

$$
\begin{aligned}
	\frac{1}{\sqrt{2 \pi}} &\iiint\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, \omega') + \omega' t) \cos(\phi''\_z(\bm{r}, \omega'') + \omega'' t) e^{-i \omega t} dt \thinspace d\omega' d\omega'' \cr
	&= \int\_{-\infin}^{\infin} \frac{1}{4} \Big( \cos(\phi'\_y(\bm{r}, -\omega - \omega'') + \phi''\_z(\bm{r}, \omega'')) + \cos(\phi'\_y(\bm{r}, \omega - \omega'') + \phi''\_z(\bm{r}, \omega'')) \Big) d\omega'' \cr
	&+ ...
\end{aligned}
$$

$$
\begin{aligned}
	\int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, -\omega - \omega'') + \phi''\_z(\bm{r}, \omega'')) \thinspace d\omega''
	&= \int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, -\omega - \omega'')) \cos(\phi''\_z(\bm{r}, \omega'')) \thinspace d\omega'' \cr
	&- \int\_{-\infin}^{\infin} \sin(\phi'\_y(\bm{r}, -\omega - \omega'')) \sin(\phi''\_z(\bm{r}, \omega'')) \thinspace d\omega''
\end{aligned}
$$

https://en.wikipedia.org/wiki/Convolution

$$
\begin{aligned}
	\int\_{-\infin}^{\infin} \cos(\phi'\_y(\bm{r}, -\omega - \omega'')) \cos(\phi''\_z(\bm{r}, \omega'')) \thinspace d\omega''
	= \int\_{-\infin}^{\infin} f(-\omega - \omega'') g(\omega'') \thinspace d\omega''
\end{aligned}
$$


---

To determine the spectral composition of irradiance, we must consider the solution of Maxwell's equation in the frequency domain. If we perform a Fourier transform of the fields, Equation 6.17 reads

$$ \tag{6.20}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \Bigg( \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega \Bigg) \times \Bigg( \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega \Bigg).
$$

To simplify this expression, it is necessary to bring both fields under the same integral sign:

$$ \tag{6.21}
	\bm{S}(\bm{r}, t)
	= \frac{\mu\_0^{-1}}{2 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace \times
		\mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \omega') e^{i \omega' t} \big\rbrace
	d\omega d\omega'.
$$

Time-averaged...

$$ \tag{6.22}
	\langle \bm{S\_t} \rangle
	= \frac{\mu\_0^{-1}}{2 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin} \frac{1}{T} \int\_{-T/2}^{\thinspace T/2}
		\mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega (t + t')} \big\rbrace \times
		\mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \omega') e^{i \omega' (t + t')} \big\rbrace
	dt' \thinspace d\omega \thinspace d\omega'.
$$

Consider a single component...

Integral of product of cosines... Dirac delta: https://web.physics.ucsb.edu/~fratus/phys103/Disc/disc_notes2_pdf.pdf

Superposition principle, plane wave decomposition (delta function w'):

$$
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \frac{\mu\_0^{-1}}{2 \pi} \int\_{-\infin}^{\infin} \oiint\_{\mathbb{S}^2}
		\mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace \times
		\mathrm{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	d\Omega\_n \thinspace d\omega.
\end{aligned}
$$

Note that this gives a S as an integral over the spectrum

In order to obtain a real part of a complex number \\(z\\), one can use the [complex conjugate](https://en.wikipedia.org/wiki/Complex_conjugate) \\(z^{\*}\\):

$$ \tag{6.22}
	\mathrm{Re} \lbrace z \rbrace = \frac{1}{2}(z + z^{\*}) = \frac{1}{2} \big(r e^{i \theta} + r e^{-i \theta} \big).
$$

This formula extends to vectors in a natural way:

$$ \tag{6.23}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	= \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		&\big( \bm{E}(\bm{r}, \omega) e^{i \omega t} + \bm{E}^{\*}(\bm{r}, \omega) e^{-i \omega t} \big) \cr \times
		&\big( \bm{B}(\bm{r}, \omega') e^{i \omega' t} + \bm{B}^{\*}(\bm{r}, \omega') e^{-i \omega' t} \big)
	d\omega d\omega'.
\end{aligned}
$$

Next, expand the cross product

$$ \tag{6.24}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}(\bm{r}, \omega) \times \bm{B}(\bm{r}, \omega') \big) e^{i (\omega + \omega') t} d\omega d\omega' \cr
	&+ \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}(\bm{r}, \omega) \times \bm{B}^{\*}(\bm{r}, \omega') \big) e^{i (\omega - \omega') t} d\omega d\omega' \cr
	&+ \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}^{\*}(\bm{r}, -\omega) \times \bm{B}(\bm{r}, \omega') \big) e^{-i (\omega - \omega') t} d\omega d\omega' \cr
	&+ \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}^{\*}(\bm{r}, \omega) \times \bm{B}^{\*}(\bm{r}, \omega') \big) e^{-i (\omega + \omega') t} d\omega d\omega'.
\end{aligned}
$$

Now get rid of minus

$$ \tag{6.24}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}(\bm{r}, \omega) \times \bm{B}(\bm{r}, \omega') \big) e^{i (\omega + \omega') t} d\omega d\omega' \cr
	&+ \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}(\bm{r}, \omega) \times \bm{B}^{\*}(\bm{r}, -\omega') \big) e^{i (\omega + \omega') t} d\omega d\omega' \cr
	&+ \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}^{\*}(\bm{r}, -\omega) \times \bm{B}(\bm{r}, \omega') \big) e^{i (\omega + \omega') t} d\omega d\omega' \cr
	&+ \frac{\mu\_0^{-1}}{8 \pi} \int\_{-\infin}^{\infin} \int\_{-\infin}^{\infin}
		\big( \bm{E}^{\*}(\bm{r}, -\omega) \times \bm{B}^{\*}(\bm{r}, -\omega') \big) e^{i (\omega + \omega') t} d\omega d\omega'.
\end{aligned}
$$

https://en.wikipedia.org/wiki/Radiometry

Cite \[[6](#references) (ch. 1.4.3, 4.8.1)\]

<!--
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

Non-paraxial polarization ???

-->

## Acknowledgments

Pharr, Jakob, NASA people

## References

<!-- Modified APA style -->

1. Golubev, E. [Sampling analytic participating media](/post/analytic-media/) (2020).
2. Chandrasekhar, S. [Radiative transfer](https://doi.org/10.1002/qj.49707633016). (1950).
3. Golubev, E. [From particle to volume scattering of light](/post/particle-volume/) (2020).
4. van de Hulst, H. C. [Light scattering by small particles](https://doi.org/10.1002/qj.49708436025) (1957).
5. Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman lectures on physics](https://www.feynmanlectures.caltech.edu/) (1963).
6. Born, M., & Wolf, E. [Principles of optics](https://doi.org/10.1017/CBO9781139644181), 7th edition (1999).
7. Mishchenko, M. I. [Electromagnetic Scattering by Particles and Particle Groups](https://www.cambridge.org/us/academic/subjects/earth-and-environmental-science/atmospheric-science-and-meteorology/electromagnetic-scattering-particles-and-particle-groups-introduction) (2014).
8. Ishimaru, A. [Electromagnetic Wave Propagation, Radiation, and Scattering](https://doi.org/10.1002/9781119079699), 2nd Edition (2017).
9. Boyd, R. W. [Nonlinear optics](https://www.elsevier.com/books/nonlinear-optics/boyd/978-0-12-811002-7), 4th Edition (2020).
10. Hansen, J. E., & Travis, L. D. [Light scattering in planetary atmospheres](https://doi.org/10.1007/BF00168069) (1974).