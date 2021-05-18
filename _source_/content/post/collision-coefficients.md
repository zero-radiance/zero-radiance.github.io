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

## Maxwell Equations in the Time Domain

At the fundamental level, optics is built on the theory of fields. What is a field? A *field* is a [mathematical](https://en.wikipedia.org/wiki/Field_(mathematics)) construct; it is a function defined for all points in space \\(\bm{r}\\) and time \\(t\\). In [physics](https://en.wikipedia.org/wiki/Field_(physics)), a typical field has a source, contains energy, and exerts a force.

Since we are primarily concerned with electromagnetic radiation, we shall focus our attention on two real vector fields: the [electric field](https://en.wikipedia.org/wiki/Electric_field) \\(\bm{E}\\) and the [magnetic field](https://en.wikipedia.org/wiki/Magnetic_field#The_B-field) \\(\bm{B}\\).

They satisfy the differential form of the [Maxwell equations](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Formulation_in_SI_units_convention) \[[5](#references) (vol. II, ch. 18), [6](#references) (ch. 2.1)\]:

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

is the [divergence](https://en.wikipedia.org/wiki/Divergence) operator, both given in Cartesian coordinates. \\(\epsilon\_0\\) and \\(\mu\_0\\) are the [vacuum permittivity](https://en.wikipedia.org/wiki/Vacuum_permittivity) and the [vacuum permeability](https://en.wikipedia.org/wiki/Vacuum_permeability), respectively, which are related by the [speed of light](https://en.wikipedia.org/wiki/Speed_of_light)

$$ \tag{1.4}
	c = \frac{1}{\sqrt{\epsilon\_0 \mu\_0}}.
$$

Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, the difference of the divergence of Equation 1.1.3 and the time derivative of Equation 1.1.4 expresses [conservation of charge](https://en.wikipedia.org/wiki/Charge_conservation):

$$ \tag{1.5}
	\nabla \cdot \bm{J}(\bm{r}, t) = - \frac{\partial}{\partial t} \rho(\bm{r}, t).
$$

Equation 1.5 is a [continuity equation](https://en.wikipedia.org/wiki/Continuity_equation) that tells us that a reduction of the charge density can be observed if the charges are carried away by a current.

Sometimes, Equations 1.1 are referred to as the "vacuum version" of the Maxwell equations. This name can be a little misleading; in fact, the matter is right there - it is just represented as a distribution of charged [elementary particles](https://en.wikipedia.org/wiki/Elementary_particle) by the *volume* [charge density](https://en.wikipedia.org/wiki/Charge_density) \\(\rho\\), such that the total amount of charge \\(Q\\) inside the volume \\(V\\) is

$$ \tag{1.6}
	Q(t) = \iiint\_{V} \rho(\bm{r}, t) \thinspace dV.
$$

Moving charged particles constitute a current. If their velocity is \\(\bm{v}\\), the *volume* [current density](https://en.wikipedia.org/wiki/Current_density) \\(\bm{J}\\) is simply

$$ \tag{1.7}
	\bm{J}(\bm{r}, t) = \rho(\bm{r}, t) \bm{v}(\bm{r}, t).
$$

Of course, individual particles may be located at different points, and have different charges and velocities - the density is just a convenient mathematical abstraction.

While this kind of parametrization is simple and useful, due to the sheer number of charged particles in bulk matter, you can't see the forest for the trees. Therefore, applied physics often uses a different representation of matter which we shall now discuss.

First, we identify two types of charges - bound and free. As the name implies, *free* charges are able to effortlessly move around the material, the classic example being the conduction current in a copper wire.

$$ \tag{1.8}
	\bm{J}(\bm{r}, t) = \bm{J\_f}(\bm{r}, t) + \bm{J\_b}(\bm{r}, t), \quad
	\rho(\bm{r}, t) = \rho\_f(\bm{r}, t) + \rho\_b(\bm{r}, t).
$$

 The *bound* [current](https://en.wikipedia.org/wiki/Current_density#Polarization_and_magnetization_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Bound_charge), \\(\bm{J\_b}\\) and \\(\rho\_b\\), are more difficult to reason about. Oscillating bound charges constitute (electric) *polarization* currents \\(\bm{J\_p}\\), and circulating bound charges are attributed to *magnetization* currents \\(\bm{J\_m}\\). They can be characterized in terms of [polarization of matter](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization):

$$ \tag{1.9}
\begin{aligned}
	&\bm{J\_b}(\bm{r}, t) = \bm{J\_p}(\bm{r}, t) + \bm{J\_m}(\bm{r}, t) =
	\frac{\partial}{\partial t} \bm{P}(\bm{r}, t) + \nabla \times \bm{M}(\bm{r}, t), \cr
	&\rho\_b(\bm{r}, t) = -\nabla \cdot \bm{P}(\bm{r}, t),
\end{aligned}
$$

where \\(\bm{P}\\) is the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) (electric dipole moment per unit volume) and \\(\bm{M}\\) is the [magnetic polarization](https://en.wikipedia.org/wiki/Magnetization) (magnetic dipole moment per unit volume).



Substitution of Equations 1.8.1, 1.9.1 into 1.1.3 and Equations 1.8.2, 1.9.2 into 1.1.4 yields

$$ \tag{1.10}
\begin{aligned}
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E}(\bm{r}, t) \big) = \bm{J\_f}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{P}(\bm{r}, t) + \nabla \times \bm{M}(\bm{r}, t), \cr
	&\nabla \cdot \big( \epsilon\_0 \bm{E}(\bm{r}, t) \big) = \rho\_f(\bm{r}, t) - \nabla \cdot \bm{P}(\bm{r}, t).
\end{aligned}
$$

We can group the terms to simplify the expressions:

$$ \tag{1.11}
\begin{aligned}
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) - \bm{M}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t) \big) = \bm{J\_f}(\bm{r}, t), \cr
	&\nabla \cdot \big( \epsilon\_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t) \big) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

A pattern begins to emerge. If we define the [electric displacement](https://en.wikipedia.org/wiki/Electric_displacement_field) \\(\bm{D}\\) and the [magnetic intensity](https://en.wikipedia.org/wiki/Magnetic_field#The_H-field) \\(\bm{H}\\) as

$$ \tag{1.12}
	\bm{D}(\bm{r}, t) = \epsilon\_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t), \quad 
	\bm{H}(\bm{r}, t) = \mu\_0^{-1} \bm{B}(\bm{r}, t) - \bm{M}(\bm{r}, t),
$$

substitute them into Equations 1.11.1-1.11.2, and group the resulting expressions with Equations 1.1.1-1.1.2, we obtain the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) of the Maxwell equations \[[6](#references) (ch. 1.1.1), [7](#references) (ch. 2.1), [8](#references) (ch. 2.1), [9](#references) (ch. 2.1)\]:

$$ \tag{1.13}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, t) - \frac{\partial}{\partial t} \bm{D}(\bm{r}, t) = \bm{J\_f}(\bm{r}, t), &
	&\nabla \cdot  \bm{D}(\bm{r}, t) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

As discussed in \[[5](#references) (vol. II, ch 32.2), [6](#references) (ch. 1.1.1)\], \\(\bm{E}\\) and \\(\bm{B}\\) are the *fundamental fields*, and \\(\bm{D}\\) and \\(\bm{H}\\) are the [auxiliary fields](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) that arise due to the influence of matter. We shall soon see that, in certain cases, these two groups are related in a simple way.

On the conceptual level, we would like the properties of matter to be continuous everywhere, which leads to continuous fields. But, sometimes, it is convenient to introduce a discontinuity to approximate a very rapid (yet continuous) variation of material properties. At the *optical interface*, the fields must satisfy the [boundary conditions](https://en.wikipedia.org/wiki/Interface_conditions_for_electromagnetic_fields) \[[6](#references) (ch. 1.1.3), [7](#references) (ch. 2.10), [8](#references) (ch. 2.4), [9](#references) (ch. 2.2)\]. If \\(\bm{n\_{12}}\\) is a unit normal vector pointing from region 1 to region 2, the normal components of the fields must be such that

$$ \tag{1.14}
	\bm{n\_{12}} \cdot  (\bm{B\_2} - \bm{B\_1}) = 0, \quad
	\bm{n\_{12}} \cdot  (\bm{D\_2} - \bm{D\_1}) = \rho\_n,
$$

where \\(\rho\_n\\) is the *surface* charge density. For the tangential components, it can be shown that

$$ \tag{1.15}
	\bm{n\_{12}} \times (\bm{E\_2} - \bm{E\_1}) = 0, \quad
	\bm{n\_{12}} \times (\bm{H\_2} - \bm{H\_1}) = \bm{J_n},
$$

where \\(\bm{J_n}\\) is the *surface* current density.

In addition to the boundary conditions, the law of [conservation of energy](https://en.wikipedia.org/wiki/Conservation_of_energy) imposes two more restrictions on valid electromagnetic fields:

- the *edge condition* says that the amount of electromagnetic energy stored near an edge must be finite even if the magnitude of the field at the edge becomes infinite;

- the [Sommerfeld radiation condition](https://en.wikipedia.org/wiki/Sommerfeld_radiation_condition) prohibits the existence of sinks collecting an infinite amount of energy arriving from infinity - these "infinite" sinks must be "finite" sources instead.

We take the Maxwell equations as axioms, and use them as a foundation on which we base the theory presented in the following sections.

## Maxwell Equations in the Frequency Domain

the Maxwell equations can be simplified by transforming the fields from the time to the frequency domain.

Define[^1] the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of the electric vector field \\(\bm{E}(\bm{r}, t)\\) as

[^1]: The choice of the sign of the complex exponential is arbitrary; it determines the sign of the imaginary components of both the complex permittivity and the complex refractive index.

$$ \tag{2.1}
	\bm{E}(\bm{r}, \omega)
	= \mathcal{F} \big\lbrace \bm{E}(\bm{r}, t) \big\rbrace 
	= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{-i \omega t} dt,
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
	&= \mathcal{F^{-1}} \big\lbrace \bm{E}(\bm{r}, \omega) \big\rbrace \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega
	 + \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{0} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \Big( \bm{E}(\bm{r}, \omega) e^{i \omega t} + \big[ \bm{E}(\bm{r}, \omega) e^{i \omega t} \big]^{\*} \Big) d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega,
\end{aligned}
$$

where the last equality follows from the definition of the real part of a complex number:

$$ \tag{2.5}
	2 \thinspace \mathcal{Re} \lbrace z \rbrace
	= z + z^{\*}
	= 2 r \cos{ \theta}.
$$

We can define the integral forms of the fields by replacing \\(\bm{E}\\) by \\(\bm{B}\\), \\(\bm{D}\\), \\(\bm{H}\\), \\(\bm{J\_f}\\) or \\(\rho\_f\\) in Equation 2.4. If we substitute these integrals into Equations 1.13 and apply the [Leibniz rule](https://en.wikipedia.org/wiki/Leibniz_integral_rule), the result is the system of integral equations

$$ \tag{2.6}
\begin{aligned}
	&\int\_{-\infin}^{\infin} \Big( \nabla \times \big( \bm{E}(\bm{r}, \omega) e^{i \omega t} \big) + \frac{\partial}{\partial t} \big( \bm{B}(\bm{r}, \omega) e^{i \omega t} \big) \Big) d\omega = 0, \cr
	&\int\_{-\infin}^{\infin} \Big( \nabla \cdot  \big( \bm{B}(\bm{r}, \omega) e^{i \omega t} \big) \Big) d\omega = 0, \cr
	&\int\_{-\infin}^{\infin} \Big( \nabla \times \big( \bm{H}(\bm{r}, \omega) e^{i \omega t} \big) - \frac{\partial}{\partial t} \big( \bm{D}(\bm{r}, \omega) e^{i \omega t} \big) \Big) d\omega
	= \int\_{-\infin}^{\infin} \Big( \bm{J\_f}(\bm{r}, \omega) e^{i \omega t} \Big) d\omega, \cr
	&\int\_{-\infin}^{\infin} \Big( \nabla \cdot \big( \bm{D}(\bm{r}, \omega) e^{i \omega t} \big) \Big) d\omega
	= \int\_{-\infin}^{\infin} \Big( \rho\_f(\bm{r}, \omega) e^{i \omega t} \Big) d\omega.
\end{aligned}
$$

Generally speaking, equality of integrands does not follow from equality of integrals (but the opposite is true). In order to simplify Equations 2.6 further, we must make an assumption that the electromagnetic field[^3] is a [periodic function](https://en.wikipedia.org/wiki/Periodic_function) of time, such that the *fundamental period* \\(\thinspace T\_1 = 2 \pi / \omega_1\\) of both the electric and the magnetic fields is the same \[[5](#references) (vol. I, ch. 50) [6](#references) (ch. 1.3.3, 1.4.3)\]. This restriction is not as severe as it may seem, for we may choose the period to be as large as necessary. And the benefits are great, since it allows us to represent[^4] the fields using the [Fourier series](https://en.wikipedia.org/wiki/Fourier_series#Complex-valued_functions)

[^3]: The fact that the electric and the magnetic fields cannot exist independently can be shown by a simple relativistic argument \[[11](#references) (ch 6.2)\].

[^4]: This discrete representation only allows frequencies that belong to a certain [countable set](https://en.wikipedia.org/wiki/Countable_set). This is noteworthy because it suggests that fields are, in some sense, quantized, even though we are not using the [quantum theory of fields](https://en.wikipedia.org/wiki/Quantum_field_theory).

$$ \tag{2.7}
	\bm{E}(\bm{r}, t)
	= \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{i p \omega_1 t}
	= \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{i \omega_p t},
	= \sum\_{p = -\infin}^{\infin} \ket{u_p} \braket{u_p \vert \bm{E}}.
$$

where we defined \\(\omega_p = p \thinspace \omega_1\\), with the coefficients \\(\bm{E_p}\\) given by the integral

$$ \tag{2.8}
	\bm{E_p}(\bm{r})
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} \bm{E}(\bm{r}, t) e^{-i \omega_p t} dt
	= \braket{u_p \vert \bm{E}}.
$$

Equation 2.8 can be interpreted as the [projection](https://en.wikipedia.org/wiki/Hilbert_space#Fourier_analysis) onto the [discrete Fourier basis](https://en.wikipedia.org/wiki/Fourier_series#Hilbert_space_interpretation), with Equation 2.7 showing the reconstruction. The beautiful thing about the discrete Fourier basis is that it is orthonormal, which means that the individual elements of Equation 2.7 (called *harmonics*) are completely independent of each other:

$$ \tag{2.9}
	\braket{u_p \vert u_q}
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} e^{i (\omega_q - \omega_p) t} dt
	= \delta_{p,q},
$$

where \\(\delta_{p,q}\\) is the [Kronecker delta](https://en.wikipedia.org/wiki/Kronecker_delta#Integral_representations) function.

Similarly to Equation 2.2, if the time-domain field is real,

$$ \tag{2.10}
	\bm{E_p}(\bm{r}) = [\bm{E_{-p}}(\bm{r})]^{\*},
$$

which permits us to simplify Equation 2.7:

$$ \tag{2.11}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	&= \bm{E_0}(\bm{r})
	 + \sum\_{p = 1}^{\infin} \bm{E_p}(\bm{r}) e^{i \omega_p t}
	 + \sum\_{p = -\infin}^{-1} \bm{E_p}(\bm{r}) e^{i \omega_p t} \cr
	&= \bm{E_0}(\bm{r}) + \sum\_{p = 1}^{\infin} \Big( \bm{E_p}(\bm{r}) e^{i \omega_p t} + \big[ \bm{E_p}(\bm{r}) e^{i \omega_p t} \big]^{\*} \Big) \cr
	&= \bm{E_0}(\bm{r}) + \sum\_{p = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{i \omega_p t} \big\rbrace.
\end{aligned}
$$

Intuitively, the harmonics produce an equivalent amount of vibration regardless of the sign of their frequency.

What happens when we Fourier transform a periodic function? Combining Equations 2.1 and 2.7,

$$ \tag{2.12}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{-i \omega t} dt \cr
	&= \frac{1}{\sqrt{2 \pi}} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) \int\_{-\infin}^{\infin} e^{i (\omega_p - \omega) t} dt \cr
	&= \frac{1}{\sqrt{2 \pi}} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) \big( 2 \pi \delta(\omega_p - \omega) \big),
\end{aligned}
$$

where we used the exponential definition of the [Dirac delta](https://en.wikipedia.org/wiki/Dirac_delta_function) function \\(\delta(x)\\).

Since the individual harmonics are orthogonal (and thus independent), analysis of a *time-harmonic* field with \\(N\\) components can be simplified by considering \\(N\\) *single-frequency* vector fields, such as

$$ \tag{2.13}
	\bm{E}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{i \omega_p t} \big\rbrace,
$$

in isolation, and the corresponding phasor fields

$$ \tag{2.14}
	\bm{E}(\bm{r}, \omega)
	= \sqrt{2 \pi} \bm{E_p}(\bm{r}) \delta(\omega_p - \omega).
$$

Substitution of Equation 2.14 (where we can replace \\(\bm{E}\\) by \\(\bm{B}\\), \\(\bm{D}\\), \\(\bm{H}\\), \\(\bm{J\_f}\\) or \\(\rho\_f\\)) into Equations 2.6 yields

$$ \tag{2.15}
\begin{aligned}
	&\nabla \times \big( \bm{E_p}(\bm{r}) e^{i \omega_p t} \big)
	+ \frac{\partial}{\partial t} \big( \bm{B_p}(\bm{r}) e^{i \omega_p t} \big) = 0, &
	&\nabla \cdot  \big( \bm{B_p}(\bm{r}) e^{i \omega_p t} \big) = 0, \cr
	&\nabla \times \big( \bm{H_p}(\bm{r}) e^{i \omega_p t} \big)
	- \frac{\partial}{\partial t} \big( \bm{D_p}(\bm{r}) e^{i \omega_p t} \big)
	= \bm{J\_{f, \thinspace p}}(\bm{r}) e^{i \omega_p t}, &
	&\nabla \cdot  \big( \bm{D_p}(\bm{r}) e^{i \omega_p t} \big)
	= \rho\_{f, \thinspace p}(\bm{r}) e^{i \omega_p t}.
\end{aligned}
$$

Performing differentiation with respect to time and division by the pervasive \\(e^{i \omega_p t}\\) factor yields the Maxwell equations for time-harmonic fields \[[7](#references) (ch. 2.4), [8](#references) (ch. 2.2), [9](#references) (ch. 2.3)\]:

$$ \tag{2.16}
\begin{aligned}
	&\nabla \times \bm{E_p}(\bm{r}) + i \omega_p \bm{B_p}(\bm{r}) = 0, &
	&\nabla \cdot  \bm{B_p}(\bm{r}) = 0, \cr
	&\nabla \times \bm{H_p}(\bm{r}) - i \omega_p \bm{D_p}(\bm{r}) = \bm{J\_{f, \thinspace p}}(\bm{r}), &
	&\nabla \cdot  \bm{D_p}(\bm{r}) = \rho\_{f, \thinspace p}(\bm{r}).
\end{aligned}
$$

For single-frequency fields, we can remove redundant indexing to simplify the notation:

$$ \tag{2.17}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}) + i \omega \bm{B}(\bm{r}) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}) - i \omega \bm{D}(\bm{r}) = \bm{J\_f}(\bm{r}), &
	&\nabla \cdot  \bm{D}(\bm{r}) = \rho\_f(\bm{r}).
\end{aligned}
$$

An interesting consequence of the time-harmonic assumption is the reduction in the number of independent the Maxwell equations from 4 to 2. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, the divergence of Equations 2.17.1 and 2.17.3 (on the left) is mathematically identical to Equations 2.17.2 and 2.17.4 (on the right), respectively.

Using this method, we can also obtain the expression of [conservation of charge](https://en.wikipedia.org/wiki/Charge_conservation):

$$ \tag{2.18}
	\nabla \cdot \bm{J\_f}(\bm{r}) = - i \omega \rho\_f(\bm{r}).
$$

For future convenience, we shall define the decomposition of the *free* charges and currents into the *source* and the *induced* parts:

$$ \tag{2.19}
	\rho\_f = \rho\_s + \rho\_i, \quad
	\bm{J\_f} = \bm{J\_s} + \bm{J\_i}.
$$

Conservation of charge implies that

$$ \tag{2.20}
	\rho\_s(\bm{r}) = \frac{i}{\omega} \nabla \cdot \bm{J\_s}(\bm{r}), \quad
	\rho\_i(\bm{r}) = \frac{i}{\omega} \nabla \cdot \bm{J\_i}(\bm{r}).
$$

## Force, Energy, and Radiometry

Given a mathematical description of electromagnetic radiation in terms of vector fields, we would like to physically characterize it as an energy transfer process. In order to do that, we have to determine how much energy there is in a given volume element of space, and also the rate of energy flow \[[5](#references) (vol. II, ch. 27), [6](#references) (ch. 1.1.4), [7](#references) (ch. 2.11), [8](#references) (ch. 2.5), [9](#references) (ch. 2.4)\].

[Conservation of energy](https://en.wikipedia.org/wiki/Conservation_of_energy) is one of the most important principles of physics. Here is one way to state it: the difference between the amount of external energy \\(\mathcal{E\_{ext}}\\) flowing into the volume \\(V\\) and the amount of internal energy \\(\mathcal{E\_{int}}\\) flowing out equals the amount of work \\(\mathcal{W}\\) done inside.

$$ \tag{3.1}
	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) -
	\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
	\frac{\partial}{\partial t} \mathcal{W}(V, t).
$$

In Equation 3.1, \\(\mathcal{E}\\) refers to a single type of energy: kinetic, electromagnetic, etc. Work transforms a portion of \\(\mathcal{E\_{ext}}\\) into a different type of energy: kinetic into potential, electromagnetic into thermal, and so on. If we account for all types of energy, the total amount of energy is conserved.

Given our focus on electromagnetic energy, we can partition the total amount of energy into the *field energy* and the *matter energy*. In this context, work done by the field on the matter refers to *absorption*, and has a positive sign. Similarly, *emission* is the work done by the matter on the field, and has a negative sign. We shall not consider emissive materials in the analysis presented below.

Mathematically, the [rate of doing work](https://en.wikipedia.org/wiki/Work_(physics)#Mathematical_calculation) is the product of force and velocity:

$$ \tag{3.2}
	\frac{\partial \mathcal{W}}{\partial t} = \bm{F} \cdot \bm{v}.
$$ 

Thus, to compute it, we must recall the definition of electromagnetic force.

If a particle with the charge \\(q\\) moving at the velocity \\(\bm{v}\\) is placed in an electromagnetic field, it experiences the [Lorentz force](https://en.wikipedia.org/wiki/Lorentz_force)

$$ \tag{3.3}
	\bm{F}(\bm{r}, t) = q(\bm{r}, t) \big( \bm{E}(\bm{r}, t) + \bm{v}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big).
$$

For the charge density \\(\rho\\) and the volume element \\(dV\\), the corresponding equation is

$$ \tag{3.4}
	d\bm{F}(\bm{r}, t) = \rho(\bm{r}, t) \big( \bm{E}(\bm{r}, t) + \bm{v}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \thinspace dV.
$$

Therefore, the amount of work done per unit time by the field on the matter inside the volume \\(V\\) is

$$ \tag{3.5}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
	\iiint\_{V} \bm{v}(\bm{r}, t) \cdot d\bm{F}(\bm{r}, t) =
	\iiint\_{V} \bm{E}(\bm{r}, t) \cdot \rho(\bm{r}, t) \bm{v}(\bm{r}, t) \thinspace dV.
$$

Since the current density is just the number of moving charges per unit volume (see Equation 1.7),

$$ \tag{3.6}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
	\iiint\_{V} \bm{E}(\bm{r}, t) \cdot \bm{J}(\bm{r}, t) \thinspace dV.
$$

The Maxwell equations allow us to relate a current to the generated fields. Substitution of Equation 1.1.3 yields

$$ \tag{3.7}
	\iiint\_{V} \bm{E} \cdot \bm{J} \thinspace dV = 
	\iiint\_{V} \bm{E} \cdot \Big( \nabla \times \big( \mu\_0^{-1} \bm{B} \big) -
	\frac{\partial}{\partial t} \big( \epsilon\_0 \bm{E} \big) \Big) dV.
$$

Since [the divergence of the cross product](https://en.wikipedia.org/wiki/Vector_calculus_identities#Cross_product_rule) is

$$ \tag{3.8}
	\nabla \cdot (\bm{E} \times \bm{B}) = (\nabla \times \bm{E}) \cdot \bm{B} - \bm{E} \cdot (\nabla \times \bm{B}),
$$

we can reformulate the integrand of Equation 3.7 as

$$ \tag{3.9}
	\bm{E} \cdot \bm{J} = 
	\mu\_0^{-1} \big( (\nabla \times \bm{E}) \cdot \bm{B} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
	\epsilon\_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

The curl of \\(\bm{E}\\) is also given by the Maxwell equations (see Equation 1.1.1). Then it follows that

$$ \tag{3.10}
	\bm{E} \cdot \bm{J} = 
	\mu\_0^{-1} \big( {-\bm{B}} \cdot \frac{\partial \bm{B}}{\partial t} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
	\epsilon\_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

Moving the dot products under the derivative sign and grouping the derivatives produces a simpler expression

$$ \tag{3.11}
	\bm{E} \cdot \bm{J} =
	{-\mu\_0^{-1}} \nabla \cdot (\bm{E} \times \bm{B}) -
	\frac{\partial}{\partial t} \Big( \frac{\epsilon\_0}{2} (\bm{E} \cdot \bm{E}) +
	\frac{\mu\_0^{-1}}{2} (\bm{B} \cdot \bm{B}) \Big).
$$

We can observe that the rate of doing work is a balance of inflow of \\((\bm{E} \times \bm{B})\\) and the rate of change of the squared magnitudes of the fields. The physical significance of this expression becomes more apparent if we return to the integral form

$$ \tag{3.12}
	\frac{\partial \mathcal{W}}{\partial t}
	= \iiint\_{V} \Big( {-\mu\_0^{-1}} \nabla \cdot (\bm{E} \times \bm{B}) \Big) dV -
	\frac{\partial}{\partial t} \iiint\_{V} \Big( \frac{\epsilon\_0}{2} E^2 + \frac{\mu\_0^{-1}}{2} B^2 \Big) dV 
$$

and use the [divergence theorem](https://en.wikipedia.org/wiki/Divergence_theorem) to replace the leftmost volume integral with an integral taken over the bounding surface \\(\delta V\\) of the volume \\(V\\):

$$ \tag{3.13}
	\frac{\partial \mathcal{W}}{\partial t}
	= \oiint\_{\delta V} \Big( \mu\_0^{-1} (\bm{E} \times \bm{B}) \cdot (-\bm{n}) \Big) dA -
	\frac{\partial}{\partial t} \iiint\_{V} \Big( \frac{\epsilon\_0}{2} E^2 + \frac{\mu\_0^{-1}}{2} B^2 \Big) dV,
$$

where \\(\bm{n}\\) is the outward-facing surface normal.

According to Equation 3.1, the right-hand side of Equation 3.13 represents the difference between the rates of inflow and outflow of energy. Thus, the first term gives the amount of external energy (per unit time) flowing through the bounding surface into the volume,

$$ \tag{3.14}
	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) = 
	\oiint\_{\delta V} \Big( \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \cdot (-\bm{n}) \Big) dA,
$$

and the second term corresponds to the rate at which the amount of energy within the volume decreases:

$$ \tag{3.15}
	-\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
	-\frac{\partial}{\partial t} \iiint\_{V} \Big( \frac{\epsilon\_0}{2} E^2(\bm{r}, t) + \frac{\mu\_0^{-1}}{2} B^2(\bm{r}, t) \Big) dV.
$$

According to this interpretation, the *squared* magnitudes of the fields

$$ \tag{3.16}
	\frac{\partial}{\partial V} \mathcal{E\_e}(\bm{r}, t) = \frac{\epsilon\_0}{2} E^2(\bm{r}, t), \quad
	\frac{\partial}{\partial V} \mathcal{E\_m}(\bm{r}, t) = \frac{\mu\_0^{-1}}{2} B^2(\bm{r}, t), \quad
$$

are the electric and the magnetic energy volume densities, and

$$ \tag{3.17}
	\bm{S}(\bm{r}, t) = \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
$$

is the *instantaneous* [Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector) that represents the direction and the rate of energy flow[^5].

[^5]: This definition of field energy, while widely accepted, is somewhat ambiguous \[[5](#references) (vol. II, ch. 27.4)\]. It may be worth pointing out that there is an alternative definition in terms of the auxiliary fields \[[6](#references) (ch. 1.1.4), [7](#references) (ch. 2.11), [8](#references) (ch. 2.5), [9](#references) (ch. 2.4)\]. We prefer our version because it accounts for bound currents. The two definitions coincide for non-magnetic materials with \\(\mu = \mu_0\\).

Since the electromagnetic fields oscillate so rapidly, the *time-averaged* Poynting vector is often used instead:

$$ \tag{3.18}
	\braket{\bm{S}}
	= \frac{1}{T} \int\_{-T/2}^{\thinspace T/2} \bm{S}(\bm{r}, t + t') \thinspace dt'.
$$

It becomes particularly useful once you consider a fixed direction \\(\bm{n}\\). Then, according to Equation 3.14,

$$ \tag{3.19}
	\mathtt{E}
	= \braket{\bm{S}} \cdot \bm{n}
	= \braket{\bm{S}} \cos{\theta}
$$

is the amount of energy per second per unit area that flows through a surface with the normal \\(\bm{n}\\). That is the definition of [irradiance](https://en.wikipedia.org/wiki/Irradiance) \\(\mathtt{E}\\).

If we solve the Maxwell equations in the frequency domain, we may be interested in the spectral composition of the Poynting vector. Assuming that the electromagnetic field is time-harmonic, Equation 3.17 can be written as

$$ \tag{3.20}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&=  \mu\_0^{-1}
		\Bigg( \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{i \omega_p t} \Bigg) \times
		\Bigg( \sum\_{q = -\infin}^{\infin} \bm{B_q}(\bm{r}) e^{i \omega_q t} \Bigg) \cr
	&=  \mu\_0^{-1} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p}(\bm{r}) e^{i \omega_p t} \big) \times
		\big( \bm{B_q}(\bm{r}) e^{i \omega_q t} \big) \cr
	&=  \mu\_0^{-1} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p}(\bm{r}) e^{i \omega_p t} \big) \times
		\big[ \bm{B_q}(\bm{r}) e^{i \omega_q t} \big]^{\*}.
\end{aligned}
$$

The corresponding equation of the time-averaged Poynting vector is

$$ \tag{3.21}
\begin{aligned}
	\braket{\bm{S}}
	= \mu\_0^{-1} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
	\big( \bm{E_p} e^{i \omega_p t} \big) \times
	\big[ \bm{B_q} e^{i \omega_q t} \big]^{\*}
	\Bigg(
		\frac{1}{T} \int\_{-T/2}^{\thinspace T/2} e^{i (\omega_p - \omega_q) t'} dt'
	\Bigg).
\end{aligned}
$$

First, consider the case when \\(T = T_1\\). Then, according to Equation 2.9,

$$ \tag{3.22}
\begin{aligned}
	\braket{\bm{S}}
	&=  \mu\_0^{-1} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p} e^{i \omega_p t} \big) \times
		\big[ \bm{B_q} e^{i \omega_q t} \big]^{\*}
		\delta_{m,n} \cr
	&=  \mu\_0^{-1} \sum\_{p = -\infin}^{\infin}
		\bm{E_p} \times [\bm{B_p}]^{\*} \cr
	&=  \mu\_0^{-1} (\bm{E_0} \times \bm{B_0})
	  + \mu\_0^{-1} \sum\_{p = 1}^{\infin} \bm{E_p} \times [\bm{B_p}]^{\*}
	  + \mu\_0^{-1} \sum\_{p = -\infin}^{-1} \bm{E_p} \times [\bm{B_p}]^{\*} \cr
	&=  \mu\_0^{-1} (\bm{E_0} \times \bm{B_0})
	  + \mu\_0^{-1} \sum\_{p = 1}^{\infin}
		\Big( 
			\big( \bm{E_p} \times [\bm{B_p}]^{\*} \big)
		  + \big[ \bm{E_p} \times [\bm{B_p}]^{\*} \big]^{\*}
		\Big) \cr
	&=  \mu\_0^{-1} (\bm{E_0} \times \bm{B_0})
	  + \mu\_0^{-1} \sum\_{p = 1}^{\infin}
		2 \thinspace \mathcal{Re} \big\lbrace \bm{E_p} \times [\bm{B_p}]^{\*} \big\rbrace
\end{aligned}
$$

is a real vector, as expected.

In the case where \\(T > T_1\\), the total value of \\(\braket{\bm{S}}\\) is a sum of the contribution from a number of whole periods (which is given by Equation 3.22) plus the contribution from a fraction of a period. If the period used for time-averaging is very large \\((T \gg T_1)\\), the *relative* contribution from a fraction of a period will be much smaller than the *relative* contribution from a large number of whole periods, so the formula of Equation 3.22 should serve as a good approximation.

Equation 3.22 allows us to define the *time-averaged* Poynting phasor

$$ \tag{3.23}
	\braket{\bm{S_p}} = \mu\_0^{-1} \big( \bm{E_p}(\bm{r}) \times [\bm{B_p}(\bm{r})]^{\*} \big)
$$

such that (cf. Equation 2.11)

$$ \tag{3.24}
	\braket{\bm{S}}
	= \braket{\bm{S_0}}
	+ \sum\_{p = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \negthinspace \braket{\bm{S_p}} \negthinspace \big\rbrace.
$$

It produces a more compact expression of irradiance \\(\mathtt{E}\\)

$$ \tag{3.25}
	\mathtt{E}
	= \braket{\bm{S}} \cdot \bm{n}
	= \braket{\bm{S_0}} \cdot \bm{n}
	+ \sum\_{p = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cdot \bm{n}
$$

that can be written in terms of [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(\mathtt{E_p}\\)

$$ \tag{3.26}
	\mathtt{E_p}
	= \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cdot \bm{n}
	= \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cos{\theta}.
$$

Having defined both irradiance and spectral irradiance, we can compute all other [radiometric quantities](https://en.wikipedia.org/wiki/Radiometry) using integration and differentiation techniques as discussed in the previous article \[[3](#references)\].

## Constitutive Relations

Considered in isolation, the Maxwell equations in the frequency domain is an [undetermined system](https://en.wikipedia.org/wiki/Underdetermined_system) - it has fewer equations than unknowns. This makes it necessary to specify the way the material responds to the applied electromagnetic field. Such assumptions about the material are called the *material equations*, or the [constitutive relations](https://en.wikipedia.org/wiki/Constitutive_equation#Electromagnetism).

The simplest way is to assume that the material responds to weak electromagnetic fields in an approximately linear manner[^6]. Under this assumption, we may express \\(\bm{D}\\) and \\(\bm{H}\\) as [bilinear](https://en.wikipedia.org/wiki/Bilinear_map) functions of \\(\bm{E}\\) and \\(\bm{B}\\) \[[8](#references) (ch. 8.22), [10](#references)\]:

[^6]: This means that omitting higher-order terms in the series expansion results in a negligible error.

$$ \tag{4.1}
	\begin{bmatrix}
		\bm{D} \cr
		\bm{H} \cr
	\end{bmatrix} =
	\begin{bmatrix}
		\bm{c_{11}} & \bm{c_{12}} \cr
		\bm{c_{21}} & \bm{c_{22}} \cr
	\end{bmatrix}
	\begin{bmatrix}
		\bm{E} \cr
		\bm{B} \cr
	\end{bmatrix}.
$$

In general, media may be *dispersive* (dependent on the frequency), *lossy* (absorptive), and *anisotropic* (dependent on the orientation). This implies that each matrix entry \\(\bm{c_{ij}}\\) is a complex [tensor field](https://en.wikipedia.org/wiki/Tensor#Tensor_fields) that can be represented by a 3x3 matrix.

A radical simplification[^7] of the Maxwell equations can be achieved by assuming that the material is both *linear* and *isotropic* \[[6](#references) (ch. 1.1.2), [7](#references) (ch. 2.3), [8](#references) (ch. 2.3), [9](#references) (ch. 2.1)\]:

[^7]: Equations 4.2-4.3 make a big assumption that is not appropriate for certain types of real materials. For instance, [crystals](https://en.wikipedia.org/wiki/Crystal) have a well-defined [internal structure](https://en.wikipedia.org/wiki/Crystal_structure) which makes them inherently anisotropic \[[5](#references) (vol. II, ch. 30)\]. Since our application is primarily concerned with simple fluids, a linear, isotropic material is reasonable assumption that offers significant theoretical advantages (such as simplicity). However, this assumption is potentially not suitable for a more general application \[[6](#references) (ch. 2)\].

$$ \tag{4.2}
\begin{aligned}
	&\bm{J\_i}(\bm{r}, \omega) \approx \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{D}   (\bm{r}, \omega) \approx \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{B}   (\bm{r}, \omega) \approx \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega),
\end{aligned}
$$

where \\(\sigma\\) is the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity), \\(\epsilon\\) is the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity), and \\(\mu\\) is the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)).

We can also combine Equations 2.20.2 and 4.2.1 to derive the relation of the induced charge density:

$$ \tag{4.3}
	\rho\_i(\bm{r}) \approx \frac{i}{\omega} \nabla \cdot \big( \sigma(\bm{r}, \omega) \bm{E}(\bm{r}) \big).
$$

If the constitutive relations hold, the Maxwell equations for time-harmonic fields take the following form:

$$ \tag{4.4}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}) + i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}) = 0, \cr
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}) - i \omega \epsilon(\bm{r}, \omega) \bm{E}(\bm{r})
	= \bm{J\_s}(\bm{r}) + \sigma(\bm{r}, \omega) \bm{E}(\bm{r}), \cr
	&\nabla \cdot  \big( \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}) \big) = \rho\_s(\bm{r}) + \frac{i}{\omega} \nabla \cdot \big( \sigma(\bm{r}, \omega) \bm{E}(\bm{r}) \big).
\end{aligned}
$$

Using the definition of the [complex permittivity](https://en.wikipedia.org/wiki/Permittivity#Complex_permittivity) \[[7](#references) (ch. 2.8), [8](#references) (ch. 2.3), [9](#references) (ch. 2.3)\]

$$ \tag{4.5}
	\varepsilon(\bm{r}, \omega) = \epsilon(\bm{r}, \omega) - \frac{i}{\omega} \sigma(\bm{r}, \omega),
$$

we obtain the Maxwell equations for *linear, isotropic* media:

$$ \tag{4.6}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}) + i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}) = 0, &
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}) - i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}) = \bm{J\_s}(\bm{r}), &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}) \big) = \rho\_s(\bm{r}).
\end{aligned}
$$

## Helmholtz Equation

Consider a region of space without any source currents or charges. Physically, this means that there are no sources of fields in this region; but it doesn't mean the field contains no energy. The idea is to explore all solutions of the Maxwell equations (we can pick a particular solution once we specify the source or the boundary conditions), and determine how these solutions evolve over time.

Setting \\(\bm{J\_s} = \rho\_s = 0\\) in Equations 4.6, we obtain a coupled system of first-order linear homogeneous [partial differential equations](https://en.wikipedia.org/wiki/Partial_differential_equation):

$$ \tag{5.1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}) + i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}) = 0, &
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}) - i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}) = 0, &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}) \big) = 0.
\end{aligned}
$$

Mathematically, the consequence is that a linear combination of several solutions is also a valid solution.

Next, assume that the medium is *homogeneous* (constant in space):

$$ \tag{5.2}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}) + i \omega \mu(\omega) \bm{H}(\bm{r}) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}) - i \omega \varepsilon(\omega) \bm{E}(\bm{r}) = 0, &
	&\nabla \cdot  \bm{E}(\bm{r}) = 0.
\end{aligned}
$$

Let us focus on the electric phasor \\(\bm{E}\\). Take curl of Equation 5.2.1 and substitute Equation 5.2.3:

$$ \tag{5.3}
	\nabla \times \nabla \times \bm{E}(\bm{r}) + i \omega \mu(\omega) \big( i \omega \varepsilon(\omega) \bm{E}(\bm{r}) \big) = 0.
$$

Introduce the [curl of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity

$$ \tag{5.4}
	\nabla \times (\nabla \times \bm{E}) = \nabla (\nabla \cdot \bm{E}) - \nabla^2 \bm{E},
$$

where 

$$ \tag{5.5}
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

$$ \tag{5.6}
	\mathrm{div} \big( \mathrm{grad}(\bm{E}) \big) =
	\nabla^2 \bm{E} =
	(\nabla \cdot \nabla) \bm{E} =
	\frac{\partial^2 \bm{E}}{\partial x^2} + \frac{\partial^2 \bm{E}}{\partial y^2} + \frac{\partial^2 \bm{E}}{\partial z^2}
$$

is the *vector* [Laplace](https://en.wikipedia.org/wiki/Laplace_operator) operator (*scalar* Laplace operator applied to each vector component).

Use Equation 5.4 to expand Equation 5.3:

$$ \tag{5.7}
	\nabla \big( \nabla \cdot \bm{E}(\bm{r}) \big) - \nabla^2 \bm{E}(\bm{r}) - \omega^2 \mu(\omega) \varepsilon(\omega) \bm{E}(\bm{r}) = 0.
$$

Substitution of Equation 5.2.4 into 5.7 yields the equation of the electric phasor \\(\bm{E}\\):

$$ \tag{5.8}
	\nabla^2 \bm{E}(\bm{r}) + \omega^2 \mu(\omega) \varepsilon(\omega) \bm{E}(\bm{r}) = 0.
$$

Similarly, we can obtain an expression of the magnetic phasor \\(\bm{B}\\):

$$ \tag{5.9}
	\nabla^2 \bm{B}(\bm{r}) + \omega^2 \mu(\omega) \varepsilon(\omega) \bm{B}(\bm{r}) = 0.
$$

If we define

$$ \tag{5.10}
	k(\omega) = \omega \sqrt{\mu(\omega) \varepsilon(\omega)},
$$

we can separate the *vector* Equation 5.8 into three *homogeneous* [Helmholtz equations](https://en.wikipedia.org/wiki/Helmholtz_equation):

$$ \tag{5.11}
\begin{aligned}
	\big( \nabla^2 + k^2(\omega) \big) E\_x(\bm{r}) = 0, \cr
	\big( \nabla^2 + k^2(\omega) \big) E\_y(\bm{r}) = 0, \cr
	\big( \nabla^2 + k^2(\omega) \big) E\_z(\bm{r}) = 0. \cr
\end{aligned}
$$

To find a solution, let us first consider a simpler 1-dimensional Helmholtz equation

$$ \tag{5.12}
	\frac{d^2 \psi(x)}{d x^2} = -k^2 \psi(x).
$$

If the derivative of a function is the function itself (times a constant), the function is clearly an exponential:

$$ \tag{5.13}
	\psi(x) =
	\psi(0) e^{\pm i k x},
$$

where \\(\psi(0)\\) is a complex constant.

Extension to 3 dimensions is straightforward. If we rotate the coordinate frame so that the \\(x\\)-axis points along the unit vector \\(\bm{n}\\),

$$ \tag{5.14}
	\psi(\bm{r}, \bm{n}) =
	\psi(0, \bm{n}) e^{ \pm i k (\bm{r} \cdot \bm{n})}
$$

is a valid solution for a certain value of \\(\bm{n}\\). To avoid clutter, we shall adhere to a common convention with the negative sign; the positive solution can be obtained by reversing the direction of \\(\bm{n}\\).

How should we choose the direction of \\(\bm{n}\\)? It depends on the location of sources and optical interfaces. In general, we can use *any* value of \\(\bm{n}\\). And, since the Helmholtz equation is both linear and homogeneous, we can actually use *every* value of \\(\bm{n}\\) by invoking the [superposition principle](https://en.wikipedia.org/wiki/Superposition_principle). Thus the general solution is an integral taken over the surface of the unit sphere \\(\mathbb{S^2}\\) [measured](https://en.wikipedia.org/wiki/Lebesgue_integration#Construction) by the [solid angle](https://en.wikipedia.org/wiki/Solid_angle) \\(d\Omega\_n\\):

$$ \tag{5.15}
	\psi(\bm{r}) =
	\oiint\_{\mathbb{S}^2} \psi(\bm{r}, \bm{n}) \thinspace d\Omega\_n =
	\oiint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{-i k (\bm{r} \cdot \bm{n})} d\Omega\_n.
$$

Equation 5.15 can be used to solve Equations 5.11 and 5.8:

$$ \tag{5.16}
	\bm{E}(\bm{r}) =
	\oiint\_{\mathbb{S}^2} \bm{E}(\bm{r}, \bm{n}) \thinspace d\Omega\_n =
	\oiint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega\_n.
$$

That is a solution of the Maxwell equations in the frequency domain.

## Plane Waves

We can find a solution of the Maxwell equations in the time domain by substituting Equation 5.16 into 2.11:

$$ \tag{6.1}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	&= \bm{E_0}(\bm{r}) + \sum\_{n = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{i \omega_p t} \big\rbrace \cr
	&= \oiint\_{\mathbb{S}^2} \bm{E_0}(0, \bm{n}) d\Omega\_n
	 + \sum\_{p = 1}^{\infin} \oiint\_{\mathbb{S}^2} 2 \thinspace \mathcal{Re} \big\lbrace \bm{E_p}(0, \bm{n}) e^{-i k(\omega_p) (\bm{r} \cdot \bm{n})} e^{i \omega_p t} \big\rbrace d\Omega\_n.
\end{aligned}
$$

This sum of integrals represents a [wave packet](https://en.wikipedia.org/wiki/Wave_packet). It is a collection of monochromatic *vector* [plane waves](https://en.wikipedia.org/wiki/Plane_wave) \[[6](#references) (ch. 1.4.2), [8](#references) (ch. 3.2), [9](#references) (ch. 3.1)\] of the form

$$ \tag{6.2}
	\bm{E}(\bm{r}, \bm{n}) e^{i \omega t}
	= \bm{E}(0, \bm{n}) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} e^{i \omega t}
$$

each composed of three *scalar* plane waves such as

$$ \tag{6.3}
	E(\bm{r}, \bm{n}) e^{i \omega t}
	= E(0, \bm{n}) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} e^{i \omega t}
$$

expressed in terms the *complex* [wave number](https://en.wikipedia.org/wiki/Wavenumber#Complex)

$$ \tag{6.4}
	k(\omega) = \omega \sqrt{\varepsilon(\omega) \mu(\omega)}.
$$

If we define the [relative permittivity](https://en.wikipedia.org/wiki/Relative_permittivity)[^9] \\(\varepsilon\_r\\) and the [relative permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)#Relative_permeability_and_magnetic_susceptibility) \\(\mu\_r\\) using vacuum as reference,

[^9]: Historically called the [relative complex dielectric constant](https://en.wikipedia.org/wiki/Relative_permittivity#Lossy_medium).

$$ \tag{6.5}
	\varepsilon\_r(\omega) = \frac{\varepsilon(\omega)}{\epsilon\_0}, \quad
	\mu\_r(\omega) = \frac{\mu(\omega)}{\mu\_0},
$$

the complex wave number can be expressed as

$$ \tag{6.6}
	k(\omega) =
	\omega \sqrt{\varepsilon(\omega) \mu(\omega)} =
	\omega \sqrt{\epsilon\_0 \mu\_0} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	\frac{\omega}{c} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)}.
$$

It is convenient to use a parametrization that does not involve taking a square root. Thus, we define two positive real numbers, the [refractive index](https://en.wikipedia.org/wiki/Refractive_index) \\(\eta\\) and the [attenuation index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\kappa\\) \[[6](#references) (ch. 14.1), [8](#references) (ch. 2.3), [9](#references) (ch. 3.1)\], by

$$ \tag{6.7}
	\eta(\omega) - i \kappa(\omega) =
	\sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	c \sqrt{\Big( \epsilon(\omega) - \frac{i}{\omega} \sigma(\omega) \Big) \mu(\omega)}.
$$

For high frequencies such as those encountered in optics, we may perform a [Laurent series](https://en.wikipedia.org/wiki/Laurent_series) [expansion](https://www.wolframalpha.com/input/?i=series+sqrt%28a-I%2Fw*b%29) at infinity:

$$ \tag{6.8}
	\eta(\omega) - i \kappa(\omega) \approx
	c \Big( \sqrt{\epsilon \mu} - i \frac{\sigma \mu}{2 \omega \sqrt{\epsilon \mu}} \Big),
$$

which gives an approximate mapping between the optical and the physical parameters[^10].

[^10]: Keep in mind that, in general, the permittivity, permeability, and conductivity are complex.

A combination of Equations 6.6-6.7 produces the [dispersion relation](https://en.wikipedia.org/wiki/Dispersion_relation)

$$ \tag{6.9}
	k(\omega)
	= \omega \Bigg( \frac{\eta(\omega)}{c} - i \frac{\kappa(\omega)}{c} \Bigg)
	= \omega \Bigg( \frac{1}{v_p(\omega)} - \frac{i}{v_a(\omega)} \Bigg),
$$

where \\(v_p = c/\eta\\) is the [phase velocity](https://en.wikipedia.org/wiki/Phase_velocity) and \\(v_a=c/\kappa\\) is the *amplitude velocity*. It shows that, in a dispersive medium, waves of different frequencies propagate at different rates.

In order to develop some intuition about the role of the refractive index \[[5](#references) (vol. II, ch. 32.4)\], consider the spatial component of a scalar plane wave

$$ \tag{6.10}
\begin{aligned}
	E(\bm{r}, \bm{n})
	&= E\_0 e^{-i k (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{i \delta} e^{-i \omega (1/v_p - i/v_a) (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} e^{i \delta - i \omega (\bm{r} \cdot \bm{n}) / v_p} \cr
	&= |E(\bm{r}, \bm{n})| e^{i \theta(\bm{r}, \bm{n})}.
\end{aligned}
$$

As any complex number, it can be expressed in terms of the *magnitude* \\(|E|\\) and the *phase* \\(\theta\\), both of which are [real-valued functions](https://en.wikipedia.org/wiki/Real-valued_function).

Multiplication by \\(e^{i \omega t}\\) by yields the full expression of a scalar plane wave:

$$ \tag{6.11}
\begin{aligned}
	E(\bm{r}, \bm{n}) e^{i \omega t}
	&= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} e^{i \delta - i \omega (\bm{r} \cdot \bm{n}) / v_p} e^{i \omega t}.
\end{aligned}
$$

Let's perform [dimensional analysis](https://en.wikipedia.org/wiki/Dimensional_analysis) of Equation 6.11.

First, notice that \\((\bm{r} \cdot \bm{n}) / v\\) has units of time. Then, take the [argument](https://en.wikipedia.org/wiki/Argument_(complex_analysis)) of the expression

$$ \tag{6.12}
	\theta(\bm{r}, t)
	= \mathcal{Arg} \big\lbrace E(\bm{r}, \bm{n}) e^{i \omega t} \big\rbrace
	= \theta(\bm{r}, \bm{n}) + \omega t
	= \delta - \omega \big(\bm{r} \cdot \bm{n} - v_p t \big) / v_p.
$$

Notice that, for any \\(\Delta t\\),

$$ \tag{6.13}
	\theta(\bm{r}, t) = \theta(\bm{r} + (v_p \Delta t) \bm{n}, \thinspace t + \Delta t).
$$

This implies that \\(\theta\\) represents a plane propagating along its normal \\(\bm{n}\\) at the phase velocity \\(v_p\\). In general, surfaces of constant phase are called *cophasal*, or *wavefronts*.

Taking the real part of Equation 6.11 allows us to uncover the *wave amplitude*

$$ \tag{6.14}
	\mathcal{Re} \big\lbrace E(\bm{r}, \bm{n}) e^{i \omega t} \big\rbrace = 
	|E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} \cos{\theta(\bm{r}, t)}.
$$

If the attenuation index \\(\kappa = 0\\), the amplitude velocity \\(v_a = \infty\\), and Equation 6.14 represents a regular sine wave.

[Insert Picture Here]

On the other hand, \\(\kappa > 0\\) produces an exponential decay characteristic of an [evanescent wave](https://en.wikipedia.org/wiki/Evanescent_field).

[Insert Picture Here]

It's easy to show that, for linear, isotropic media, the field vectors oscillate in the plane of the wave. The Maxwell equations (c.f. Equation 5.1) tell us that, in the absence of sources,

$$ \tag{6.15}
	\nabla \cdot \bm{B}(\bm{r}) = 0, \quad
	\nabla \cdot \bm{D}(\bm{r}) = 0.
$$

More specifically, for linear, isotropic media (c.f. Equation 5.2),

$$ \tag{6.16}
	\nabla \cdot \bm{H}(\bm{r}) = 0, \quad
	\nabla \cdot \bm{E}(\bm{r}) = 0.
$$

Taking \\(\bm{E}\\) as an example, substitution of the integrand of Equation 5.16 

$$ \tag{6.17}
	\bm{E}(\bm{r}, \bm{n}) = \bm{E}(0, \bm{n}) e^{-i k(\omega) (\bm{r} \cdot \bm{n})}
$$

into Equation 6.16.2 yields

$$ \tag{6.18}
	\nabla \cdot \bm{E}(\bm{r}, \bm{n})
	= -i k(\omega) \bm{n} \cdot \bm{E}(\bm{r}, \bm{n})
	= 0.
$$

After division by the constant \\(i k\\) and multiplication by \\(e^{i \omega t}\\), it is clear that the field vector is orthogonal to the plane normal:

$$ \tag{6.19}
	\bm{n} \cdot \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}) e^{i \omega t} \big\rbrace
	= 0.
$$

Similarly, we can relate the electric and the magnetic field vectors. According to Equation 5.1.1,

$$ \tag{6.20}
	\nabla \times \bm{E}(\bm{r}) = - i \omega \bm{B}(\bm{r}).
$$

Expand the expression of the curl

$$ \tag{6.21}
	\nabla \times \bm{E}(\bm{r}, \bm{n})
	= \begin{bmatrix}
		-i k(\omega) n\_y E\_z(\bm{r}, \bm{n}) + i k(\omega) n\_z E\_y(\bm{r}, \bm{n}) \cr
		-i k(\omega) n\_z E\_x(\bm{r}, \bm{n}) + i k(\omega) n\_x E\_z(\bm{r}, \bm{n}) \cr
		-i k(\omega) n\_x E\_y(\bm{r}, \bm{n}) + i k(\omega) n\_y E\_x(\bm{r}, \bm{n})
	  \end{bmatrix}
	= -i k(\omega) \bm{n} \times \bm{E}(\bm{r}, \bm{n})
$$

and substitute it into Equation 6.20:

$$ \tag{6.22}
	-i k(\omega) \bm{n} \times \bm{E}(\bm{r}, \bm{n}) = - i \omega \bm{B}(\bm{r}, \bm{n}).
$$

Recall the definition of the wave number given by Equation 6.9

$$ \tag{6.23}
	\bm{n} \times \Bigg( \frac{1}{c} \big(\eta(\omega) - i \kappa(\omega) \big) \bm{E}(\bm{r}, \bm{n}) \Bigg)
	= \bm{B}(\bm{r}, \bm{n}),
$$

If we take the real part of this expression multiplied by \\(e^{i \omega t}\\), the result

$$ \tag{6.24}
	\frac{\eta(\omega)}{c} \bm{n} \times \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}) e^{i \omega t} \big\rbrace
	= \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}) e^{i \omega t} \big\rbrace
$$

shows us that the electric and the magnetic field vectors are orthogonal. Thus, \\(\lbrace \bm{E}, \bm{B}, \bm{n} \rbrace\\) is an orthogonal triad of vectors that defines the geometric configuration of a plane wave in a linear, isotropic medium:

$$ \tag{6.25}
	\bm{n} \times \frac{\bm{E}(\bm{r}, t)}{|\bm{E}(\bm{r}, t)|} = \frac{\bm{B}(\bm{r}, t)}{|\bm{B}(\bm{r}, t)|}.
$$

[Insert Picture Here]

We have seen the \\(\bm{E} \times \bm{B}\\) expression before (c.f. Equation 3.17). Thus, 

$$ \tag{6.26}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
	= \mu\_0^{-1} |\bm{E}(\bm{r}, t)| |\bm{B}(\bm{r}, t)| \bm{n}
$$

is the expression of the Poynting vector of the plane wave.

Since the vectors are orthogonal, Equations 6.24-6.25 define the ratio of wave amplitudes:

$$ \tag{6.27}
	\frac{\eta(\omega)}{c} |\bm{E}(\bm{r}, t)| = |\bm{B}(\bm{r}, t)|.
$$

This equality has two consequences. First, it says that the electric and the magnetic vectors of a plane wave oscillate *in phase*, with the magnitude of both vectors rising and falling at the same point in space and time, so the locations of peaks and troughs match.

[Insert Picture Here]

Stated more precisely, if we align the triad \\(\lbrace \bm{E}, \bm{B}, \bm{n} \rbrace\\) with the \\(\lbrace x,y,z \rbrace\\) axes of a Cartesian coordinate system, then

$$ \tag{6.28}
	\mathcal{Arg} \big\lbrace E_x(0, \bm{n}) \big\rbrace =
	\mathcal{Arg} \big\lbrace B_y(0, \bm{n}) \big\rbrace =
	\delta.
$$

Secondly, it means we can compute the Poynting vector without explicit consideration of the magnetic field:

$$ \tag{6.29}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \frac{\eta(\omega)}{c} |\bm{E}(\bm{r}, t)|^2 \bm{n}
	= \Big(\epsilon\_0 \eta c |E\_0|^2 e^{-2 \omega (\bm{r} \cdot \bm{n}) (\kappa / c)} \cos^2{\theta(\bm{r}, t)} \Big) \bm{n},
$$

where we expanded the squared amplitude as per Equation 6.13.

The average value of a squared cosine is 1/2, so the corresponding irradiance[^11] value (c.f. Equation 3.19) is

$$ \tag{6.30}
	\mathtt{E}
	= \braket{\bm{S}} \cdot \bm{n}
	= \frac{1}{2} \epsilon\_0 \eta c |E\_0|^2 e^{-2 \omega (\bm{r} \cdot \bm{n}) (\kappa / c)}
	= \frac{1}{2} \epsilon\_0 \eta c |E\_0|^2 e^{-\beta_a (\bm{r} \cdot \bm{n})},
$$

[^11]: Since \\(\bm{E}(\bm{r})\\) is a single-frequency field, the expressions of irradiance and spectral irradiance are identical.

where we combined several constants into the [absorption coefficient](https://en.wikipedia.org/wiki/Attenuation_coefficient#Absorption_and_scattering_coefficients)

$$ \tag{6.31}
	\beta_a(\omega) = 2 \omega \frac{\kappa(\omega)}{c}.
$$

## Electromagnetic Potential

The solutions of the Maxwell equations we have found so far have a limited range of validity. The medium must be both isotropic and homogeneous (or divided into several bounded homogeneous regions), and the material's response to the incident electromagnetic field may only be purely linear. Moreover, while the method of solving a system of differential equations augmented with boundary conditions (the so-called [boundary value problem](https://en.wikipedia.org/wiki/Boundary_value_problem)) is appropriate for many simple cases (such as reflection and transmission of a plane wave at a planar interface, which leads to [Fresnel equations](https://en.wikipedia.org/wiki/Fresnel_equations)), it quickly becomes unwieldy for more complex problems. Another approach tends to become more viable, where one reformulates the field in terms of the [electromagnetic potential](https://en.wikipedia.org/wiki/Electromagnetic_four-potential), which leads to an integral (rather than a differential) solution of the Maxwell equations.

Take another look at the Maxwell equations. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, Equation 1.1.2 can be written as

$$ \tag{7.1}
	\nabla \cdot \bm{B}(\bm{r}, t) = \nabla \cdot \big( \nabla \times \bm{A}(\bm{r}, t) \big) = 0,
$$

where \\(\bm{A}\\) is called a *vector potential* \[[5](#references) (vol. II, ch. 18), [6](#references) (ch. 2.1), [8](#references) (ch. 2.6)\].

Next, substitute the definition of \\(\bm{A}\\) into Equation 1.1.1 to obtain

$$ \tag{7.2}
	\nabla \times \Big( \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{A}(\bm{r}, t) \Big) = 0.
$$

As the [curl of gradient](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_gradient_is_zero) is zero, Equation 7.2 can be used to define the *scalar potential* \\(\phi\\):

$$ \tag{7.3}
	\bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{A}(\bm{r}, t) = -\nabla \phi(\bm{r}, t).
$$

Thus both the electric and the magnetic fields are uniquely defined by the vector and the scalar potentials:

$$ \tag{7.4}
\begin{aligned}
	&\bm{E}(\bm{r}, t) = -\frac{\partial}{\partial t} \bm{A}(\bm{r}, t) - \nabla \phi(\bm{r}, t), &
	&\bm{B}(\bm{r}, t) = \nabla \times \bm{A}(\bm{r}, t).
\end{aligned}
$$

The converse is not true. Since the [curl of gradient](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_gradient_is_zero) is zero, the vector potential

$$ \tag{7.5}
	\bm{A'}(\bm{r}, t) = \bm{A}(\bm{r}, t) + \nabla \chi(\bm{r}, t)
$$

corresponds to the same magnetic field

$$ \tag{7.6}
	\bm{B}(\bm{r}, t) = \nabla \times \bm{A'}(\bm{r}, t) = \nabla \times \bm{A}(\bm{r}, t).
$$

If we substitute Equation 7.5 into 7.3 and group the terms under the gradient sign, we obtain the expression of the transformed scalar potential:

$$ \tag{7.7}
	\phi'(\bm{r}, t) = \phi(\bm{r}, t) - \frac{\partial}{\partial t} \chi(\bm{r}, t).
$$

Since the electric and the magnetic field are independent of a particular choice of \\(\chi\\), they are said to be *invariant* under a [gauge transformation](https://en.wikipedia.org/wiki/Gauge_theory#Classical_electromagnetism) given by Equations 7.5 and 7.7.

What is a good choice of \\(\chi\\)? That depends on the mathematical formulation of a particular problem. Usually, we want \\(\bm{A}\\) to have a certain desirable property that simplifies some equation.

In general, a vector field \\(\bm{A}\\) can be represented as a sum of a divergence-free component \\(\bm{A'}\\) and a curl-free component \\(\bm{A''}\\):

$$ \tag{7.8}
\begin{aligned}
	&\bm{A}(\bm{r}, t) = \bm{A'}(\bm{r}, t) + \bm{A''}(\bm{r}, t), &
	&\nabla \cdot  \bm{A' }(\bm{r}, t) = 0, &
	&\nabla \times \bm{A''}(\bm{r}, t) = 0. &
\end{aligned}
$$

This implies that there is a way to manipulate the divergence of \\(\bm{A}\\) without affecting its curl:

$$ \tag{7.9}
\begin{aligned}
	& \nabla \times \bm{A}(\bm{r}, t) = \nabla \times \bm{A'}(\bm{r}, t) = \bm{B}(\bm{r}, t), \cr
	& \nabla \cdot \bm{A}(\bm{r}, t) = \nabla \cdot \bm{A''}(\bm{r}, t) = -\nabla^2 \chi(\bm{r}, t).
\end{aligned}
$$

We shall relate \\(\chi\\) to \\(\phi\\) in a rather non-obvious (but, as we shall soon see, convenient) manner:

$$ \tag{7.10}
	\nabla^2 \chi(\bm{r}, t) = \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t).
$$

The combination of Equations 7.9.2 and 7.10 yields the [Lorenz condition](https://en.wikipedia.org/wiki/Lorenz_gauge_condition)

$$ \tag{7.11}
\begin{aligned}
	\nabla \cdot \bm{A}(\bm{r}, t) = -\frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t).
\end{aligned}
$$

Let us justify our choice of the gauge transformation by expressing the Maxwell equations in terms of the electromagnetic potential. Begin by substituting Equations 7.4 into 1.1.3-1.1.4:

$$ \tag{7.12}
\begin{aligned}
	&\nabla \times \nabla \times \bm{A}(\bm{r}, t) + \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	+ \nabla \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t)
	= \frac{\bm{J}(\bm{r}, t)}{\epsilon\_0 c^2}, \cr
	&-\frac{\partial}{\partial t} \nabla \cdot \bm{A}(\bm{r}, t) - \nabla^2 \phi(\bm{r}, t)
	= \frac{\rho(\bm{r}, t)}{\epsilon\_0}.
\end{aligned}
$$

We can immediately use the Lorenz condition to eliminate \\(\bm{A}\\) from Equation 7.12.2. The result is an inhomogeneous scalar *wave equation*

$$ \tag{7.13}
	\nabla^2 \phi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \phi(\bm{r}, t)
	= -\frac{\rho(\bm{r}, t)}{\epsilon\_0}.
$$

Equation 7.12.1 can be expanded using the [curl of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity:

$$ \tag{7.14}
	\nabla \big( \nabla \cdot \bm{A}(\bm{r}, t) \big) - \nabla^2 \bm{A}(\bm{r}, t) + \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	+ \nabla \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t)
	= \frac{\bm{J}(\bm{r}, t)}{\epsilon\_0 c^2}.
$$

Once again, we use the Lorenz condition, but this time, we do it in order to eliminate \\(\phi\\). We obtain an inhomogeneous vector *wave equation*

$$ \tag{7.15}
	\nabla^2 \bm{A}(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	= -\frac{\bm{J}(\bm{r}, t)}{\epsilon\_0 c^2}.
$$

Putting it all together, the formulation of the Maxwell equations in terms of the electromagnetic potential results in an decoupled system of partial differential equations:

$$ \tag{7.16}
\begin{aligned}
	&\nabla^2 \bm{A}(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	= -\frac{\bm{J}(\bm{r}, t)}{\epsilon\_0 c^2}, &
	&\bm{B}(\bm{r}, t) = \nabla \times \bm{A}(\bm{r}, t), \cr
	&\nabla^2 \phi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \phi(\bm{r}, t)
	= -\frac{\rho(\bm{r}, t)}{\epsilon\_0}, &
	&\bm{E}(\bm{r}, t) = -\frac{\partial}{\partial t} \bm{A}(\bm{r}, t) - \nabla \phi(\bm{r}, t).
\end{aligned}
$$

## Green Functions

Compare Equation 7.16.3 to a single component of Equation 7.16.1 in a Cartesian coordinate system. Both have the same form

$$ \tag{8.1}
	\nabla^2 \psi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \psi(\bm{r}, t)
	= \xi(\bm{r}, t).
$$

In this context, \\(\xi\\) is a *source function*, \\(\psi\\) is a *wave function*, and \\(c\\) is the *velocity* of the waves.

Just as before, the problem becomes simpler in the frequency domain:

$$ \tag{8.2}
	\int\_{-\infin}^{\infin} \nabla^2 \psi(\bm{r}, \omega) e^{i \omega t} d\omega
	- \frac{1}{c^2} \int\_{-\infin}^{\infin} \frac{\partial^2}{\partial t^2} \big( \psi(\bm{r}, \omega) e^{i \omega t} \big) d\omega
	= \int\_{-\infin}^{\infin} \xi(\bm{r}, \omega) e^{i \omega t} d\omega.
$$

After invoking the time-harmonic assumption, the integral sign vanishes, and we are left with

$$ \tag{8.3}
	\big( \nabla^2 + k_0^2(\omega) \big) \psi(\bm{r}) = \xi(\bm{r}),
$$

which is an *inhomogeneous* [Helmholtz equation](https://en.wikipedia.org/wiki/Helmholtz_equation#Inhomogeneous_Helmholtz_equation), where \\(k_0 = \omega / c\\) is the *free-space* [wave number](https://en.wikipedia.org/wiki/Wavenumber).

\\(\big( \nabla^2 + k_0^2(\omega) \big)\\) is called a Helmholtz operator, and it is a linear operator in 3 dimensions. Therefore, Equation 8.3 represents a linear transformation

$$ \tag{8.4}
	\mathcal{L} \big\lbrace \psi(\bm{r}) \big\rbrace = \xi(\bm{r}).
$$

Unfortunately, unlike its homogeneous counterpart (Equation 5.11), Equation 8.3 does not have a [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression) solution. Yet, we can still solve it analytically using a neat mathematical trick.

Use the [sifting property](https://en.wikipedia.org/wiki/Dirac_delta_function#Translation) of the Dirac delta function to express the source function

$$ \tag{8.5}
	\xi(\bm{r}) = \iiint\_{\mathbb{R^3}} \xi(\bm{r'}) \delta(\bm{r} - \bm{r'}) dV'
$$

as a volume integral (a 3-dimensional [convolution](https://en.wikipedia.org/wiki/Convolution)) taken over the whole real space.

While the resulting Helmholtz equation

$$ \tag{8.6}
	\big( \nabla^2 + k_0^2(\omega) \big) \psi(\bm{r}) = \iiint\_{\mathbb{R^3}} \xi(\bm{r'}) \delta(\bm{r} - \bm{r'}) dV',
$$

does not appear to be simpler, as it turns out, we can easily solve the Helmholtz equation for a point source

$$ \tag{8.7}
	\mathcal{L} \big\lbrace g(\bm{r}) \big\rbrace = \big( \nabla^2 + k_0^2 \big) g_0(\bm{r}) = \delta(\bm{r}).
$$

Its solution is the *scalar* [Green function](https://en.wikipedia.org/wiki/Green%27s_function) \\(g\\). It represents an [impulse response](https://en.wikipedia.org/wiki/Impulse_response) of the linear operator \\(\mathcal{L}\\).

Equation 8.7 is an inhomogeneous linear differential equation. Consequently, its solution depends on the linear operator in question, its domain of validity, as well as the initial or boundary conditions. According to Equation 8.5, our domain is the whole real space, and the only (physical) restriction is the Sommerfeld radiation condition. Thus, it can be shown that the *free-space* scalar Green function \\(g_0\\) takes the form of an outgoing scalar *sphere wave* \[[12](#references)\]:

$$ \tag{8.8}
	g_0(\bm{r}) = -\frac{e^{-i k_0 |\bm{r}|}}{4 \pi |\bm{r}|}.
$$

In order to reconcile Equations 8.6 and 8.7, replace \\(\bm{r}\\) with \\(\bm{r} - \bm{r'}\\) and multiply both sides by \\(\xi(\bm{r'})\\):

$$ \tag{8.9}
	\big( \nabla^2 + k_0^2 \big) \xi(\bm{r'}) g_0(\bm{r} - \bm{r'}) = \xi(\bm{r'}) \delta(\bm{r} - \bm{r'}).
$$

Note that the Laplacian depends on \\(\bm{r}\\) rather than \\(\bm{r'}\\). Keeping this in mind, take the volume integral

$$ \tag{8.10}
	\big( \nabla^2 + k_0^2 \big) \iiint\_{\mathbb{R^3}} \xi(\bm{r'}) g_0(\bm{r} - \bm{r'}) dV' = \xi(\bm{r}),
$$

and compare the result to Equation 8.3. We can see that we have found our solution:

$$ \tag{8.11}
	\psi(\bm{r}) = \iiint\_{\mathbb{R^3}} \xi(\bm{r'}) g_0(\bm{r} - \bm{r'}) dV'.
$$

Equation 8.11 represents a *special* solution of the *inhomogeneous* Helmholtz equation. Since a solution of a *homogeneous* equation doesn't change its right-hand side, due to linearity, we can obtain the *general* solution of Equation 8.3 by adding Equations 5.15 and 8.11:

$$ \tag{8.12}
	\psi(\bm{r}) 
	= \oiint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{-i \omega (\bm{r} \cdot \bm{n}) / c} d\Omega\_n
	+ \iiint\_{\mathbb{R^3}} -\xi(\bm{r'}) \frac{e^{-i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} dV'.
$$

This equation has a simple optical interpretation: the first integral on the right-hand side can be seen as an *incident* (or externally-generated) wave, and the second term corresponds to a *scattered* (or internally-generated) wave. Both waves propagate at the speed of light.

We can immediately use Equation 8.12 to find the expressions of both the scalar and the vector potentials in the frequency domain by substituting the charge and the the current densities in place of the source function:

$$ \tag{8.13}
\begin{aligned}
	&\bm{A}(\bm{r})
	= \oiint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{-i \omega (\bm{r} \cdot \bm{n}) / c} d\Omega\_n
	+ \iiint\_{\mathbb{R^3}} \frac{\bm{J}(\bm{r'})}{\epsilon\_0 c^2} \frac{e^{-i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} dV', \cr
	&\phi(\bm{r})
	= \oiint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{-i \omega (\bm{r} \cdot \bm{n}) / c} d\Omega\_n
	+ \iiint\_{\mathbb{R^3}} \frac{\rho(\bm{r'})}{\epsilon\_0} \frac{e^{-i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} dV'.
\end{aligned}
$$

The corresponding expressions in the time domain ...

Physical interpretation ...

<!--
## Polarization of Light

We have just seen that transverse waves are composed of two independent scalar components. Can we separate them? The answer is yes.

In the framework of geometric optics, light is represented by oriented ray. When this ray encounters a birefringent crystal (such as calcite), double refraction occurs, and the original light ray splits into the *ordinary* and *extraordinary* rays.

The idea behind [polarization of light](http://hyperphysics.phy-astr.gsu.edu/hbase/phyopt/polarcon.html#c1) \[Chandra?, [4](#references) (ch. 5), [5](#references) (vol. I, ch. 33), [6](#references) (ch. 1.4), [7](#references), [8](#references) (p. 527-533), [9](#references)\] (not to be confused with [polarization of matter](http://hyperphysics.phy-astr.gsu.edu/hbase/electric/dielec.html#c1) \[[4](#references) (ch. 4.5), [5](#references) (vol. II, ch. 32), [6](#references) (ch. 2.3), [9](#references)\]) is simple. Consider a fixed point[^5] in space; at that point, imagine the plane[^6] containing the electric and the magnetic vectors. Over time, the tip of the electric (and the magnetic) vector traces a closed[^7] curve. That shape of that curve is called polarization.

[^5]: In general, polarization depends on position \[[6](#references) (p. 38)\].

[^6]: It would be natural to call it the *plane of polarization*. However, the term has been used (and misused) in so many different contexts during the course of development of the field of optics that the only way to avoid ambiguity is to not use the term at all \[[6](#references) (p. 29)\].

[^7]: Since the wave is time-harmonic, the wave function is periodic.

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
	\bm{E}(\bm{r},t) = \mathcal{Re}
	\begin{Bmatrix}
		\phantom{\pm} \bar{E}\_a(\bm{r}) \phantom{e^{-i \pi/2}} e^{i(\phi(\bm{r}) + \omega t)} \cr
				 \pm  \bar{E}\_b(\bm{r}) 		  e^{-i \pi/2}  e^{i(\phi(\bm{r}) + \omega t)}
	\end{Bmatrix} =
	\mathcal{Re} \big\lbrace \bm{\bar{E}}(\bm{r}) e^{i \phi(\bm{r})} e^{i \omega t} \big\rbrace,
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

Pharr, Jakob, Humphreys, NASA

## References

<!-- Modified APA style -->

1. Golubev, E. [Sampling analytic participating media](/post/analytic-media/) (2020).
2. Chandrasekhar, S. [Radiative transfer](https://doi.org/10.1002/qj.49707633016) (1950).
3. Golubev, E. [From particle to volume scattering of light](/post/particle-volume/) (2020).
4. van de Hulst, H. C. [Light scattering by small particles](https://doi.org/10.1002/qj.49708436025) (1957).
5. Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman lectures on physics](https://www.feynmanlectures.caltech.edu/) (1965).
6. Born, M., & Wolf, E. [Principles of Optics](https://doi.org/10.1017/CBO9781139644181) (7th ed., 1999).
7. Novotny, L., & Hecht, B. [Principles of Nano-Optics](https://doi.org/10.1017/CBO9780511794193) (2nd ed., 2012).
8. Ishimaru, A. [Electromagnetic Wave Propagation, Radiation, and Scattering](https://doi.org/10.1002/9781119079699) (2nd ed., 2017).
9. Mishchenko, M. [Electromagnetic Scattering by Particles and Particle Groups](https://doi.org/10.1017/CBO9781139019064) (2014).
10. Keller, S. M., & Carman, G. P. [Electromagnetic wave propagation in (bianisotropic) magnetoelectric materials](https://doi.org/10.1177/1045389X12467518) (2013). 
11. Fleisch, D. [A Student's Guide to Vectors and Tensors](https://doi.org/doi:10.1017/CBO9781139031035) (2011).
12. Talalai, G. [Derivation of the Free-Space Green's Function and Radiation from a Hertzian Dipole](https://www.researchgate.net/publication/324729639_Derivation_of_the_Free-Space_Green's_Function_and_Radiation_from_a_Hertzian_Dipole) (2016).
99. Hansen, J. E., & Travis, L. D. [Light scattering in planetary atmospheres](https://doi.org/10.1007/BF00168069) (1974).
