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

At the fundamental level, optics is built on the theory of fields. What is a field? A *field* is a [mathematical](https://en.wikipedia.org/wiki/Field_(mathematics)) construct; it is a function defined for all points in space and time. In [physics](https://en.wikipedia.org/wiki/Field_(physics)), a field typically has a source, contains energy, and exerts a force.

Since we are primarily concerned with electromagnetic radiation, we shall focus our attention on the the [electric field](https://en.wikipedia.org/wiki/Electric_field) \\(\bm{E}\\) and the [magnetic induction](https://en.wikipedia.org/wiki/Magnetic_field#The_B-field) \\(\bm{B}\\). These are real vectors defined at every point in space \\(\bm{r}\\) and time \\(t\\).

They satisfy the differential form of [Maxwell's equations](https://en.wikipedia.org/wiki/Maxwell%27s_equation) \[[6](#references) (ch. 1.1)\]:

$$ \tag{1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial \bm{B}(\bm{r}, t)}{\partial t} = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, t) - \frac{\partial \bm{D}(\bm{r}, t)}{\partial t} = \bm{J\_f}(\bm{r}, t), &
	&\nabla \cdot  \bm{D}(\bm{r}, t) = \rho\_f(\bm{r}, t),
\end{aligned}
$$

where \\(\bm{J\_f}\\) and \\(\rho\_f\\) are the *free* [current](https://en.wikipedia.org/wiki/Current_density#Free_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Free_charge_density), respectively,

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

As discussed in \[[5](#references) (vol. II, ch 32.2), [6](#references) (ch. 1.1)\], \\(\bm{E}\\) and \\(\bm{B}\\) are considered fundamental fields, and the [electric displacement](https://en.wikipedia.org/wiki/Electric_displacement_field) \\(\bm{D}\\) and the [magnetic field](https://en.wikipedia.org/wiki/Magnetic_field#The_H-field) \\(\bm{H}\\) are [auxiliary fields](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) that arise due to the influence of matter[^101]. They are related to the fundamental fields by

[^101]: We can obtain the [microscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Formulation_in_SI_units_convention) (without auxiliary fields) by [substitution](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) of the definitions of the auxiliary fields into Maxwell's equations. For optical applications, the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) is more useful.

$$ \tag{4}
	\bm{D}(\bm{r}, t) = \epsilon\_0   \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t), \quad 
	\bm{H}(\bm{r}, t) = \frac{1}{\mu\_0} \bm{B}(\bm{r}, t) - \bm{M}(\bm{r}, t),
$$

where \\(\bm{P}\\) is the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) (electric dipole moment per unit volume), \\(\bm{M}\\) is the [magnetic polarization](https://en.wikipedia.org/wiki/Magnetization) (magnetic dipole moment per unit volume), and \\(\epsilon\_0\\) and \\(\mu\_0\\) are the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity) and the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) of vacuum, respectively.

Polarization of matter is related to the *bound* [current](https://en.wikipedia.org/wiki/Current_density#Polarization_and_magnetization_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Bound_charge), \\(\bm{J\_b}\\) and \\(\rho\_b\\):

$$ \tag{5}
	\bm{J\_b}(\bm{r}, t) = \nabla \times \bm{M}(\bm{r}, t) + \frac{\partial \bm{P}(\bm{r}, t)}{\partial t}, \quad 
	\rho\_b(\bm{r}, t) = -\nabla \cdot \bm{P}(\bm{r}, t),
$$

with the *total* current and charge densities composed of the *free* and the *bound* parts.

On the conceptual level, we would like the properties of matter to be continuous everywhere, which leads to continuous electromagnetic fields. But, sometimes, it is convenient to introduce a discontinuity to approximate very rapid (yet continuous) variation of optical properties. At the *optical interface*, the fields must satisfy the [boundary conditions](https://en.wikipedia.org/wiki/Interface_conditions_for_electromagnetic_fields) \[[6](#references) (ch. 1.1)\]. If \\(\bm{n\_{12}}\\) is a unit normal vector pointing from region 1 to region 2, the normal components of the fields must be such that

$$ \tag{6}
	\bm{n\_{12}} \cdot  (\bm{B\_2} - \bm{B\_1}) = 0, \quad
	\bm{n\_{12}} \cdot  (\bm{D\_2} - \bm{D\_1}) = \rho\_n,
$$

where \\(\rho\_n\\) is the *surface* [charge density](https://en.wikipedia.org/wiki/Charge_density). For the tangential components, it can be shown that

$$ \tag{7}
	\bm{n\_{12}} \times (\bm{E\_2} - \bm{E\_1}) = 0, \quad
	\bm{n\_{12}} \times (\bm{H\_2} - \bm{H\_1}) = \bm{J\_n},
$$

where \\(\bm{J\_n}\\) is the *surface* [current density](https://en.wikipedia.org/wiki/Current_density).

We take Maxwell's equations as axioms, and use them as a foundation from which we derive the theory presented in the following sections.

## Maxwell's Equations in the Frequency Domain

Maxwell's equations can be simplified by transforming the fields from the time to the frequency domain.

Define the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of the electric field \\(\bm{E}(\bm{r}, t)\\) as

$$ \tag{8}
	\bm{E}(\bm{r}, \omega) = \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{-i \omega t} dt.
$$

\\(\bm{E}(\bm{r}, \omega)\\) is called a phase vector, or a [phasor](https://en.wikipedia.org/wiki/Phasor), and is, in general, complex[^102]. \\(\bm{E}(\bm{r}, t)\\), on another hand, is real:

[^102]: This implies that all factors comprising the expression of the complex field are also complex.

$$ \tag{9}
	\bm{E}(\bm{r}, t) =
	\mathrm{Re} \Bigg\lbrace \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega \Bigg\rbrace =
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega.
$$

We can define integral forms of the fields by replacing \\(\bm{E}\\) by \\(\bm{B}\\), \\(\bm{D}\\), \\(\bm{H}\\), \\(\bm{J\_f}\\) or \\(\rho\_f\\) in Equation 9. If we substitute these integrals into Equation 1, and if we assume that we can perform [differentiation under the integral sign](https://en.wikipedia.org/wiki/Leibniz_integral_rule), the integrands can be expressed as

$$ \tag{10}
\begin{aligned}
	&\nabla \times \big( \bm{E}(\bm{r}, \omega) e^{i \omega t} \big) + \frac{\partial \big( \bm{B}(\bm{r}, \omega) e^{i \omega t} \big)}{\partial t} = 0, &
	&\nabla \cdot  \big( \bm{B}(\bm{r}, \omega) e^{i \omega t} \big) = 0, \cr
	&\nabla \times \big( \bm{H}(\bm{r}, \omega) e^{i \omega t} \big) - \frac{\partial \big( \bm{D}(\bm{r}, \omega) e^{i \omega t} \big)}{\partial t} = \bm{J\_f}(\bm{r}, \omega) e^{i \omega t}, &
	&\nabla \cdot  \big( \bm{D}(\bm{r}, \omega) e^{i \omega t} \big) = \rho\_f(\bm{r}, \omega) e^{i \omega t}.
\end{aligned}
$$

Performing differentiation with respect to time and division by the pervasive \\(e^{i \omega t}\\) factor yields 

$$ \tag{11}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \bm{D}(\bm{r}, \omega) = \bm{J\_f}(\bm{r}, \omega), &
	&\nabla \cdot  \bm{D}(\bm{r}, \omega) = \rho\_f(\bm{r}, \omega).
\end{aligned}
$$

Dependence on time is completely gone, which is advantageous since we are interested in the steady state of the field measured over the time interval that is large in comparison to the period of an oscillation of the field.

If we take divergence of Equation 11.3, combine it with equation 11.4, and use the fact that [divergence of curl is zero](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero), we obtain the [continuity equation](https://en.wikipedia.org/wiki/Continuity_equation) of charge:

$$ \tag{12}
	\nabla \cdot \bm{J\_f}(\bm{r}, \omega) = - i \omega \rho\_f(\bm{r}, \omega).
$$

For future convenience, we shall define the decomposition of the *free* charges and currents into the *source* and the *induced* parts:

$$ \tag{13}
	\rho\_f = \rho\_s + \rho\_i, \quad
	\bm{J\_f} = \bm{J\_s} + \bm{J\_i}.
$$

Continuity of charge implies

$$ \tag{14}
	\rho\_s(\bm{r}, \omega) = \frac{i}{\omega} \nabla \cdot \bm{J\_s}(\bm{r}, \omega), \quad
	\rho\_i(\bm{r}, \omega) = \frac{i}{\omega} \nabla \cdot \bm{J\_i}(\bm{r}, \omega).
$$

## Constitutive Relations

Considered in isolation, Maxwell's system has fewer equations than unknowns. This forces us to specify the way the material responds to the electromagnetic field. One way to do this is to define the auxiliary fields in terms of polarization of matter (as shown in Equations 4 and 5). Turns out, this leads to a non-linear solution \[[10](#references)\] that is relatively difficult to manipulate.

Fortunately, many materials respond to weak electromagnetic fields in optical frequencies in an approximately linear way. This behavior is captured by the [constitutive relations](https://en.wikipedia.org/wiki/Constitutive_equation#Electromagnetism):

$$ \tag{15}
\begin{aligned}
	&\bm{J\_i}(\bm{r}, \omega) \approx \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{D}   (\bm{r}, \omega) \approx \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{H}   (\bm{r}, \omega) \approx \frac{1}{\hat{\mu}(\bm{r}, \omega)} \bm{B}(\bm{r}, \omega).
\end{aligned}
$$

We can also combine Equations 14.2 and 15.1 to define the relation of the indiced charge density:

$$ \tag{16}
	\rho\_i(\bm{r}, \omega) \approx \frac{i}{\omega} \nabla \cdot \big( \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big).
$$

In general, media may be *dispersive* (dependent on the frequency), *lossy* (absorptive), and *anisotropic* (dependent on the orientation). This means that the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity) \\(\hat{\sigma}\\), the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity) \\(\hat{\epsilon}\\), and the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \\(\hat{\mu}\\) are frequency-dependent complex [tensor fields](https://en.wikipedia.org/wiki/Tensor#Tensor_fields). For instance,

$$ \tag{17}
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

For *linear* media (after substitution of the constitutive relations), Maxwell's equations take the following form:

$$ \tag{18}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \cdot  \big( \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J\_s}(\bm{r}, \omega) + \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\nabla \cdot  \big( \hat{\epsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega) + \frac{i}{\omega} \nabla \cdot \big( \hat{\sigma}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big).
\end{aligned}
$$

If we define the [complex permittivity](https://en.wikipedia.org/wiki/Permittivity#Complex_permittivity) \\(\hat{\varepsilon}\\) as

$$ \tag{19}
	\hat{\varepsilon}(\bm{r}, \omega) = \hat{\epsilon}(\bm{r}, \omega) - \frac{i}{\omega} \hat{\sigma}(\bm{r}, \omega),
$$

then Equation 18 can be shortened to

$$ \tag{20}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J\_s}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega).
\end{aligned}
$$

## Helmholtz's Equation

Consider a region of space without any source currents or charges. Physically, this means that there are no sources of fields. This may seem strange at first; the idea is explore all solutions (we can pick a particular one once we add a source) and determine how they evolve over time.

Setting \\(\bm{J\_f} = \rho\_f = 20\\) in Equation 20, we obtain a coupled system of homogeneous linear differential equations:

$$ \tag{21}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \hat{\mu}(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \hat{\varepsilon}(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = 0.
\end{aligned}
$$

Mathematically, the consequence is that a linear combination of several solutions is also a valid solution.

Assume that the medium is *homogeneous* and *stationary* (constant in space and time)[^103]:

[^103]: See \[[6](#references) (ch. 1.2)\] for an inhomogeneous solution.

$$ \tag{22}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{E}(\bm{r}, \omega) = 0.
\end{aligned}
$$

Take curl of Equation 22.1 and substitute Equation 22.2:

$$ \tag{23}
\begin{aligned}
	&\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) + i \omega \hat{\mu}(\omega) \big( i \omega \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) \big) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{E}(\bm{r}, \omega) = 0.
\end{aligned}
$$

Introduce the [curl-of-curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity

$$ \tag{24}
	\nabla \times (\nabla \times \bm{V}) = \nabla (\nabla \cdot \bm{V}) - \nabla^2 \bm{V},
$$

where 

$$ \tag{25}
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

$$ \tag{26}
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

is the *vector* [Laplace](https://en.wikipedia.org/wiki/Laplace_operator) operator (*scalar* Laplace operator applied to each vector component).

Use it to expand Equation 23.1:

$$ \tag{27}
\begin{aligned}
	&\nabla (\nabla \cdot \bm{E}(\bm{r}, \omega)) - \nabla^2 \bm{E}(\bm{r}, \omega) - \omega^2 \hat{\varepsilon}(\omega) \hat{\mu}(\omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \hat{\varepsilon}(\omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{E}(\bm{r}, \omega) = 0.
\end{aligned}
$$

Substitution of Equation 27.4 into 27.1 yields the equation of the electric field \\(\bm{E}\\):

$$ \tag{28}
	\nabla^2 \bm{E}(\bm{r}, \omega) + \omega^2 \hat{\varepsilon}(\omega) \hat{\mu}(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

We can define a tensor \\(\hat{v}\\) composed of three complex column vectors \\(\bm{v\_1}\\), \\(\bm{v\_2}\\), \\(\bm{v\_3}\\) such that

$$ \tag{29}
	\hat{v}^2(\omega) = \hat{\varepsilon}(\omega) \hat{\mu}(\omega).
$$

Since this tensor is [symmetric](https://en.wikipedia.org/wiki/Symmetric_matrix) \[[6](#references) (ch. 15.1)\], we can perform a coordinate transformation to [diagonalize](https://en.wikipedia.org/wiki/Diagonalizable_matrix) it:

$$ \tag{30}
	\nabla^2 \bm{E}(\bm{r}, \omega) + \omega^2
		\begin{bmatrix}
			v\_x^2 & 0 & 0 \cr
			0 & v\_y^2 & 0 \cr
			0 & 0 & v\_z^2
		\end{bmatrix} \bm{E}(\bm{r}, \omega) = 0,
$$

which allows us separate the variables into three *scalar* [Helmholtz's equations](https://en.wikipedia.org/wiki/Helmholtz_equation):

$$ \tag{31}
\begin{aligned}
	\big( \nabla^2 + k\_x^2(\omega) \big) E\_x(\bm{r}, \omega) = 0, \cr
	\big( \nabla^2 + k\_y^2(\omega) \big) E\_y(\bm{r}, \omega) = 0, \cr
	\big( \nabla^2 + k\_z^2(\omega) \big) E\_z(\bm{r}, \omega) = 0,
\end{aligned}
$$

where

$$ \tag{32}
	k(\omega) = \omega v(\omega) = \omega \varepsilon(\omega) \mu(\omega)
$$

is called the [wave number](https://en.wikipedia.org/wiki/Wavenumber). The reason behind this name will become apparent shortly.

## Wave Equation

For simplicity, consider a one-dimensional Helmholtz's equation (with no dependence on \\(y\\) or \\(z\\))

$$ \tag{33}
	\frac{\partial^2 s(x)}{\partial x^2} = -k^2 s(x).
$$

If the derivative of a function is the function itself (times a constant), the function is clearly an exponential:

$$ \tag{34}
	s(x) = \bar{a} e^{\pm i k x}.
$$

Extension to three dimensions is straightforward. If we rotate the coordinate frame so that the \\(x\\)-axis points along the unit vector \\(\bm{n}\\),

$$ \tag{35}
	s(\bm{r}) = \bar{a} e^{\pm i k (\bm{n} \cdot \bm{r})} = \bar{a} e^{\pm i \bm{k} \cdot \bm{r}}.
$$

where \\(\bm{k}\\) is called the [wave vector](https://en.wikipedia.org/wiki/Wave_vector). This formula can be used to solve Equations 31.1-31.3:

$$ \tag{36}
	\bm{E}(\bm{r}, \omega) = \bm{\bar{E}}(\omega)
	\begin{bmatrix}
		\exp(\pm i \bm{k\_x}(\omega) \cdot \bm{r}) \cr
		\exp(\pm i \bm{k\_y}(\omega) \cdot \bm{r}) \cr
		\exp(\pm i \bm{k\_z}(\omega) \cdot \bm{r})
	\end{bmatrix}.
$$

After solving Maxwell's equations in the frequency domain, we can find the solution in the time domain by substitution into Equation 9:

$$ \tag{37}
	\bm{E}(\bm{r}, t) =
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \mathrm{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega = 
	\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{\bar{E}}(\omega)
		\begin{bmatrix}
			\cos(\pm i \bm{k\_x}(\omega) \cdot \bm{r} + i \omega t) \cr
			\cos(\pm i \bm{k\_y}(\omega) \cdot \bm{r} + i \omega t) \cr
			\cos(\pm i \bm{k\_z}(\omega) \cdot \bm{r} + i \omega t)
		\end{bmatrix} d\omega.
$$


 How can we solve it? Unfortunately, the only way is to guess the answer \[[5](#references) (vol. II, ch. 20), [6](#references) (ch. 1.3)\]. We use [d'Alembert's formula](https://en.wikipedia.org/wiki/D%27Alembert%27s_formula) and assume that the solution takes the form

$$ \tag{21}
	s(x, t) = f(x \pm v t),
$$

which can be readily verified by applying the [chain rule](https://en.wikipedia.org/wiki/Chain_rule). This is the equation of a *plane wave*, which can be seen as a plane propagating along the direction of its normal (the \\(x\\)-axis) at the velocity \\(v\\).

Extension to three dimensions is straightforward. If we rotate the plane normal \\(\bm{n}\\) away from the \\(x\\)-axis,

$$ \tag{22}
	s(\bm{r}, t) = f(\bm{r} \cdot \bm{n} \pm v t).
$$

This formula can be used to solve Equations 19.1-19.3.

Notice that we deliberately do not impose any restrictions on \\(f\\) or \\(\bm{n}\\). Any linear combination of plane waves such as this one

$$ \tag{23}
	s(\bm{r}, t) = w\_1 f\_1(\bm{r} \cdot \bm{n\_1} + v t) + w\_2 f\_2(\bm{r} \cdot \bm{n\_2} - v t)
$$

is a perfectly valid solution of the scalar wave equation. This fact is referred to as the [superposition principle](https://www.feynmanlectures.caltech.edu/I_48.html).

---

It is convenient to rescale the parameter by a constant \\(k = \omega / v \\) so that

$$ \tag{22}
	s(x, t) = f(k x \pm k v t) = f(k x \pm \omega t).
$$

This allows us to extend our solution to three dimensions

$$ \tag{23}
	s(\bm{r}, t) = f(\bm{k} \cdot \bm{r} \pm \omega t),
$$

which is simply Equation 22 with the \\(x\\)-axis rotated to the \\(k\\)-axis.


We can 

Since the wave equation is homogeneous, a *superposition* of two waves is also a valid solution:

$$ \tag{22}
	s(x, t) = a\_1 f(x - v t) + a\_2 g(x + v t),
$$



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
10. Boyd, R. W. [Nonlinear optics](https://www.elsevier.com/books/nonlinear-optics/boyd/978-0-12-811002-7) (2020).