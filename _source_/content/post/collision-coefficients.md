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

While we may spend more time and energy fiddling with various combinations of parameters to determine what works and what does not, there is another way. A more fun way. The scientific way.

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

## I. Classical Electrodynamics

### 1. Maxwell Equations in the Time Domain

[Classical electrodynamics](https://en.wikipedia.org/wiki/Classical_electromagnetism) is a branch of physics that studies the interactions between moving particles that carry [electric charge](https://en.wikipedia.org/wiki/Electric_charge) (or, simply, "charges"). These interactions are embodied by the [electromagnetic field](https://en.wikipedia.org/wiki/Electromagnetic_field).

What is a field? A *field* is a [mathematical](https://en.wikipedia.org/wiki/Field_(mathematics)) construct; it is a function defined for all points in space \\(\bm{r}\\) and time \\(t\\). In [physics](https://en.wikipedia.org/wiki/Field_(physics)), a typical field has a source, contains energy, and exerts a force.

The electromagnetic field can be viewed as a combination of two real vector fields: the [electric field](https://en.wikipedia.org/wiki/Electric_field) \\(\bm{E}\\) and the [magnetic field](https://en.wikipedia.org/wiki/Magnetic_field#The_B-field) \\(\bm{B}\\). They satisfy the differential form of the [Maxwell equations](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Formulation_in_SI_units_convention) \[[5](#references) (vol. II, ch. 18), [6](#references) (ch. 2.1)\]:

$$ \tag{1.1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon_0 \bm{E}(\bm{r}, t) \big) = \bm{J}(\bm{r}, t), &
	&\nabla \cdot \big( \epsilon_0 \bm{E}(\bm{r}, t) \big) = \rho(\bm{r}, t),
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

is the [divergence](https://en.wikipedia.org/wiki/Divergence) operator, both given in Cartesian coordinates. \\(\epsilon_0\\) and \\(\mu\_0\\) are the [vacuum permittivity](https://en.wikipedia.org/wiki/Vacuum_permittivity) and the [vacuum permeability](https://en.wikipedia.org/wiki/Vacuum_permeability), respectively, which are related to the [speed of light](https://en.wikipedia.org/wiki/Speed_of_light)

$$ \tag{1.4}
	c = \frac{1}{\sqrt{\epsilon_0 \mu\_0}}.
$$

Since the electromagnetic field only interacts with charged particles, the matter is represented[^4] by the *volume* [charge density](https://en.wikipedia.org/wiki/Charge_density) \\(\rho\\), such that the total amount of charge \\(Q\\) inside the volume \\(V\\) is

[^4]: In the statistical sense, which matches the point of view of quantum mechanics.

$$ \tag{1.5}
	Q(V, t) = \int\_{V} \rho(\bm{r}, t) dV.
$$

Moving charges constitute a current. If their mean velocity is \\(\bm{v}\\), the *volume* [current density](https://en.wikipedia.org/wiki/Current_density) \\(\bm{J}\\) is simply

$$ \tag{1.6}
	\bm{J}(\bm{r}, t) = \rho(\bm{r}, t) \bm{v}(\bm{r}, t).
$$

Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, the difference of the divergence of Equation 1.1.3 and the time derivative of Equation 1.1.4 expresses [conservation of charge](https://en.wikipedia.org/wiki/Charge_conservation):

$$ \tag{1.7}
	\nabla \cdot \bm{J}(\bm{r}, t) = - \frac{\partial}{\partial t} \rho(\bm{r}, t).
$$

Equation 1.7 is a [continuity equation](https://en.wikipedia.org/wiki/Continuity_equation) that tells us that a reduction of the charge density is observed if the charges are carried away by a current.

Sometimes, Equations 1.1.1-1.1.4 are referred to as the the Maxwell equations *in vacuum*. This name highlights the fact that these equations are based on the atomic theory of matter as a collection of particles (electrons, protons, neutrons) embedded in vacuum. It also explains the presence of the "vacuum factors" \\(\epsilon_0\\) and \\(\mu\_0\\).

The fields generated by such *microscopic* objects have extremely high spatial frequency. In addition, certain effects are not present (or cannot be detected) when measurements are performed at the *macroscopic* scale. Effectively, due to the sheer number of charges in bulk matter, you sometimes cannot see the forest for the trees.

This leads us to the idea of *Lorentz averaging*: we determine the values of the microscopic fields using Equations 1.1.1-1.1.4, and compute their spatial average

$$ \tag{1.8}
	\bm{E}(\bm{r}, t) = \frac{1}{V} \int\_{V} \bm{E\_{\mu}}(\bm{r} + \bm{r'}, t) dV'
$$

over a region that is sufficiently large to contain a great number of atoms.

Application of this technique allows us to transform the *microscopic* Maxwell equations in vacuum into the *macroscopic* Maxwell equations in bulk matter. The former set of equations is often used in theoretical physics, while the latter is dominant in applied branches, such as [wave optics](https://en.wikipedia.org/wiki/Physical_optics).

Because Equations 1.1.1-1.1.2 are independent of the description of matter, and are also linear, a linear transformation (such as the one given by Equation 1.8) does not change their form, since we may change the order of operations (move the derivatives inside or outside the integral). On the other hand, Equations 1.1.3-1.1.4 are defined in terms of current and charge densities, and must be modified.

First, we identify two types of charges - bound and free. As the name suggests, *free* charges are able to move effortlessly around the material, the classic example being the conduction current in a copper wire.

$$ \tag{1.9}
	\bm{J} = \bm{J\_b} + \bm{J\_f}, \quad
	\rho   = \rho\_b + \rho\_f.
$$

 The *bound* [current](https://en.wikipedia.org/wiki/Current_density#Polarization_and_magnetization_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Bound_charge), \\(\bm{J\_b}\\) and \\(\rho\_b\\), are more difficult to reason about. A short, but unsatisfactory, explanation is that oscillating bound charges constitute (electric) *polarization* currents \\(\bm{J\_p}\\), and circulating bound charges are attributed to *magnetization* currents \\(\bm{J\_m}\\). They can be characterized in terms of [polarization of matter](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization):

$$ \tag{1.10}
\begin{aligned}
	&\bm{J\_b}(\bm{r}, t) = \bm{J\_p}(\bm{r}, t) + \bm{J\_m}(\bm{r}, t) =
	\frac{\partial}{\partial t} \bm{P}(\bm{r}, t) + \nabla \times \bm{M}(\bm{r}, t), \cr
	&\rho\_b(\bm{r}, t) = -\nabla \cdot \bm{P}(\bm{r}, t),
\end{aligned}
$$

where \\(\bm{P}\\) is the [electric polarization](https://en.wikipedia.org/wiki/Polarization_density) (electric dipole moment per unit volume) and \\(\bm{M}\\) is the [magnetic polarization](https://en.wikipedia.org/wiki/Magnetization) (magnetic dipole moment per unit volume). Note that, according to the [Gauss law](https://en.wikipedia.org/wiki/Gauss%27s_law_for_magnetism) given by Equation 1.1.2, no magnetic charges exist.

Substitution of Equations 1.9.1, 1.10.1 into 1.1.3 and Equations 1.9.2, 1.10.2 into 1.1.4 yields

$$ \tag{1.11}
\begin{aligned}
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon_0 \bm{E}(\bm{r}, t) \big) = \bm{J\_f}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{P}(\bm{r}, t) + \nabla \times \bm{M}(\bm{r}, t), \cr
	&\nabla \cdot \big( \epsilon_0 \bm{E}(\bm{r}, t) \big) = \rho\_f(\bm{r}, t) - \nabla \cdot \bm{P}(\bm{r}, t).
\end{aligned}
$$

We can group the terms to simplify the expressions:

$$ \tag{1.12}
\begin{aligned}
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, t) - \bm{M}(\bm{r}, t) \big) - \frac{\partial}{\partial t} \big( \epsilon_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t) \big) = \bm{J\_f}(\bm{r}, t), \cr
	&\nabla \cdot \big( \epsilon_0 \bm{E}(\bm{r}, t) + \bm{P}(\bm{r}, t) \big) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

A pattern begins to emerge. If we define the [electric displacement](https://en.wikipedia.org/wiki/Electric_displacement_field) \\(\bm{D}\\) and the [magnetic intensity](https://en.wikipedia.org/wiki/Magnetic_field#The_H-field) \\(\bm{H}\\) as

$$ \tag{1.13}
	\bm{D} = \epsilon_0 \bm{E} + \bm{P}, \quad
	\bm{H} = \mu\_0^{-1} \bm{B} - \bm{M},
$$

substitute them into Equations 1.12.1-1.12.2, and group the resulting expressions with Equations 1.1.1-1.1.2, we obtain the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) of the Maxwell equations \[[6](#references) (ch. 1.1.1), [7](#references) (ch. 2.1), [8](#references) (ch. 2.1)\]:

$$ \tag{1.14}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, t) - \frac{\partial}{\partial t} \bm{D}(\bm{r}, t) = \bm{J\_f}(\bm{r}, t), &
	&\nabla \cdot  \bm{D}(\bm{r}, t) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

As discussed in \[[5](#references) (vol. II, ch 32.2), [6](#references) (ch. 1.1.1)\], \\(\bm{E}\\) and \\(\bm{B}\\) are the *fundamental fields*, and \\(\bm{D}\\) and \\(\bm{H}\\) are the [auxiliary fields](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) that arise due to the influence of matter. We shall soon see that, in certain cases, these two groups are related in a simple way.

The Maxwell equations is coupled system of [partial differential equations](https://en.wikipedia.org/wiki/Partial_differential_equation). The left-hand side of each equation is composed of derivatives of the unknowns (the fields), and the right-hand side contains the sources (the currents and the charges). Since a derivative is a linear operator, these equations form a [linear system](https://en.wikipedia.org/wiki/Linear_system). As such, the Maxwell equations obey the [superposition principle](https://en.wikipedia.org/wiki/Superposition_principle) - if we determine the values of the fields for one source, and then, *separately*, for another, and assuming that these sources are not "disturbed" by the fields, then the solution for the configuration with *both* sources is precisely the sum of the fields found for each individual source:

$$ \tag{1.15}
	\lbrace \bm{J_1} + \bm{J_2}, \rho_1 + \rho_2 \rbrace \implies
	\lbrace \bm{E_1} + \bm{E_2}, \bm{B_1} + \bm{B_2} \rbrace.
$$

Since the microscopic properties of matter are continuous, this leads to continuous fields. But, sometimes, it is convenient to introduce a macroscopic discontinuity to approximate a very rapid (yet continuous) variation of material properties. At the *optical interface*, the fields must satisfy the [boundary conditions](https://en.wikipedia.org/wiki/Interface_conditions_for_electromagnetic_fields) \[[6](#references) (ch. 1.1.3), [7](#references) (ch. 2.10), [8](#references) (ch. 2.2)\]. If \\(\bm{n\_{12}}\\) is a unit normal vector pointing from region 1 to region 2, the normal components of the fields must be such that

$$ \tag{1.16}
	\bm{n\_{12}} \cdot  (\bm{B\_2} - \bm{B\_1}) = 0, \quad
	\bm{n\_{12}} \cdot  (\bm{D\_2} - \bm{D\_1}) = \rho\_n,
$$

where \\(\rho\_n\\) is the *surface* charge density. For the tangential components, it can be shown that

$$ \tag{1.17}
	\bm{n\_{12}} \times (\bm{E\_2} - \bm{E\_1}) = 0, \quad
	\bm{n\_{12}} \times (\bm{H\_2} - \bm{H\_1}) = \bm{J_n},
$$

where \\(\bm{J_n}\\) is the *surface* current density.

In addition to the boundary conditions, the law of conservation of energy imposes two additional restrictions on valid electromagnetic fields:

- the *edge condition* says that the amount of electromagnetic energy stored near an edge must be finite even if the magnitude of the field at the edge becomes infinite;

- the [Sommerfeld radiation condition](https://en.wikipedia.org/wiki/Sommerfeld_radiation_condition) prohibits the existence of sinks collecting (an infinite amount of) energy arriving from infinity - these "infinite" sinks must be "finite" sources instead.

Finally, it is worth pointing out that the Maxwell equations (both 1.1 and 1.14) are not independent \[[Stratton](#references) (ch 1.3)\]. For instance, take the divergence of Equations 1.14.1 and 1.14.3. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, the resulting expression may be simplified to

$$ \tag{1.18}
	\nabla \cdot \bigg( \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) \bigg) = 0,
	\quad
	- \nabla \cdot \bigg( \frac{\partial}{\partial t} \bm{D}(\bm{r}, t) \bigg)
	= \nabla \cdot \bm{J\_f}(\bm{r}, t)
	= - \frac{\partial}{\partial t} \rho_f(\bm{r}, t),
$$

where the last equality is a consequence of the conservation of charge (Equation 1.7).

At all ordinary points, all fields and their first derivatives are assumed to be continuous. That allows us to interchange the order of the space and the time derivatives to obtain

$$ \tag{1.19}
	\frac{\partial}{\partial t} \bigg( \nabla \cdot \bm{B}(\bm{r}, t) \bigg) = 0,
	\quad
	\frac{\partial}{\partial t} \bigg( \nabla \cdot \bm{D}(\bm{r}, t) \bigg)
	= \frac{\partial}{\partial t} \rho_f(\bm{r}, t).
$$

The first equation tells us that the divergence of the magnetic field is does not change over time. Assuming that the sources of the field have existed for a finite amount of time, this constant is zero, which is consistent with Equation 1.14.2. Using similar logic, the constant of integration of Equation 1.19 is also zero, thus reproducing Equation 1.14.4.

We take the Maxwell equations as axioms, and use them as the foundation on which we base the theory presented in the following sections.

### Force and Energy

Given a mathematical description of electromagnetic radiation in terms of vector fields, we would like to physically characterize it as an energy transfer process. In order to do that, we have to determine how much energy there is in a given volume element of space, and also the rate of energy flow \[[5](#references) (vol. II, ch. 27), [6](#references) (ch. 1.1.4), [7](#references) (ch. 2.11), [8](#references) (ch. 2.4)\].

[Conservation of energy](https://en.wikipedia.org/wiki/Conservation_of_energy) is one of the most important principles of physics. Here is one way to state it: the difference between the amount of external energy \\(\mathcal{E\_{ext}}\\) flowing into the volume \\(V\\) and the amount of internal energy \\(\mathcal{E\_{int}}\\) flowing out equals the amount of work \\(\mathcal{W}\\) done inside.

$$ \tag{2.1}
	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) -
	\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
	\frac{\partial}{\partial t} \mathcal{W}(V, t).
$$

In Equation 2.1, \\(\mathcal{E}\\) refers to a single type of energy: kinetic, electromagnetic, etc. Work transforms a portion of \\(\mathcal{E\_{ext}}\\) into a different type of energy: kinetic into potential, electromagnetic into thermal, and so on. If we account for all types of energy, the total amount of energy is conserved.

Given our focus on electromagnetic energy, we can partition the total amount of energy into the *field energy* and the *matter energy*. In this context, work done by the field on the matter refers to *absorption*, and has a positive sign. Similarly, *emission* is the work done by the matter on the field, and has a negative sign. We shall not consider emissive materials in the analysis presented below.

Mathematically, the [rate of doing work](https://en.wikipedia.org/wiki/Work_(physics)#Mathematical_calculation) is the dot product of the force and the velocity:

$$ \tag{2.2}
	\frac{\partial \mathcal{W}}{\partial t} = \bm{F} \cdot \bm{v}.
$$

Thus, to compute it, we must recall the definition of electromagnetic force.

If a particle with the charge \\(q\\) moving at the velocity \\(\bm{v}\\) is placed in an electromagnetic field, it experiences the [Lorentz force](https://en.wikipedia.org/wiki/Lorentz_force)

$$ \tag{2.3}
	\bm{F}(\bm{r}, t) = q(\bm{r}, t) \big( \bm{E}(\bm{r}, t) + \bm{v}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big).
$$

For the charge density \\(\rho\\) and the volume element \\(dV\\), the corresponding equation is

$$ \tag{2.4}
	d\bm{F}(\bm{r}, t) = \rho(\bm{r}, t) \big( \bm{E}(\bm{r}, t) + \bm{v}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) dV.
$$

Therefore, the amount of work done per unit time by the field on the matter inside the volume \\(V\\) is

$$ \tag{2.5}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
	\int\_{V} \bm{v}(\bm{r}, t) \cdot d\bm{F}(\bm{r}, t) =
	\int\_{V} \bm{E}(\bm{r}, t) \cdot \rho(\bm{r}, t) \bm{v}(\bm{r}, t) dV.
$$

Since the current density is just the number of moving charges per unit volume (see Equation 1.6),

$$ \tag{2.6}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
	\int\_{V} \bm{E}(\bm{r}, t) \cdot \bm{J}(\bm{r}, t) dV.
$$

The Maxwell equations allow us to relate a current to the generated fields. Substitution of Equation 1.1.3 yields

$$ \tag{2.7}
	\int\_{V} \bm{E} \cdot \bm{J} dV =
	\int\_{V} \bm{E} \cdot \Big( \nabla \times \big( \mu\_0^{-1} \bm{B} \big) -
	\frac{\partial}{\partial t} \big( \epsilon_0 \bm{E} \big) \Big) dV.
$$

Since [the divergence of the cross product](https://en.wikipedia.org/wiki/Vector_calculus_identities#Cross_product_rule) is

$$ \tag{2.8}
	\nabla \cdot (\bm{E} \times \bm{B}) = (\nabla \times \bm{E}) \cdot \bm{B} - \bm{E} \cdot (\nabla \times \bm{B}),
$$

we can reformulate the integrand of Equation 2.7 as

$$ \tag{2.9}
	\bm{E} \cdot \bm{J} =
	\mu\_0^{-1} \big( (\nabla \times \bm{E}) \cdot \bm{B} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
	\epsilon_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

The curl of \\(\bm{E}\\) is also given by the Maxwell equations (see Equation 1.1.1). Then it follows that

$$ \tag{2.10}
	\bm{E} \cdot \bm{J} =
	\mu\_0^{-1} \big( {-\bm{B}} \cdot \frac{\partial \bm{B}}{\partial t} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
	\epsilon_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

Moving the dot products under the derivative sign and grouping the derivatives produces a simpler expression

$$ \tag{2.11}
	\bm{E} \cdot \bm{J} =
	{-\mu\_0^{-1}} \nabla \cdot (\bm{E} \times \bm{B}) -
	\frac{\partial}{\partial t} \Big( \frac{\epsilon_0}{2} (\bm{E} \cdot \bm{E}) +
	\frac{\mu\_0^{-1}}{2} (\bm{B} \cdot \bm{B}) \Big).
$$

We can observe that the rate of doing work is a balance of inflow of \\((\bm{E} \times \bm{B})\\) and the rate of change of the squared magnitudes of the fields. The physical significance of this expression becomes more apparent if we return to the integral form

$$ \tag{2.12}
	\frac{\partial \mathcal{W}}{\partial t}
	= \int\_{V} \Big( {-\mu\_0^{-1}} \nabla \cdot (\bm{E} \times \bm{B}) \Big) dV -
	\frac{\partial}{\partial t} \int\_{V} \Big( \frac{\epsilon_0}{2} E^2 + \frac{\mu\_0^{-1}}{2} B^2 \Big) dV
$$

and use the [divergence theorem](https://en.wikipedia.org/wiki/Divergence_theorem) to replace the leftmost volume integral with an integral taken over the bounding surface \\(\delta V\\) of the volume \\(V\\):

$$ \tag{2.13}
	\frac{\partial \mathcal{W}}{\partial t}
	= \oint\_{\delta V} \Big( \mu\_0^{-1} (\bm{E} \times \bm{B}) \cdot (-\bm{n}) \Big) dA -
	\frac{\partial}{\partial t} \int\_{V} \Big( \frac{\epsilon_0}{2} E^2 + \frac{\mu\_0^{-1}}{2} B^2 \Big) dV,
$$

where \\(\bm{n}\\) is the outward-facing surface normal.

According to Equation 2.1, the right-hand side of Equation 2.13 represents the difference between the rates of inflow and outflow of energy. Thus, the first term gives the amount of external energy (per unit time) flowing through the bounding surface into the volume,

$$ \tag{2.14}
	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) =
	\oint\_{\delta V} \Big( \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \cdot (-\bm{n}) \Big) dA,
$$

and the second term corresponds to the rate at which the amount of energy within the volume decreases:

$$ \tag{2.15}
	-\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
	-\frac{\partial}{\partial t} \int\_{V} \Big( \frac{\epsilon_0}{2} E^2(\bm{r}, t) + \frac{\mu\_0^{-1}}{2} B^2(\bm{r}, t) \Big) dV.
$$

According to this interpretation, the *squared* magnitudes of the fields

$$ \tag{2.16}
	\frac{\partial}{\partial V} \mathcal{E\_e}(V, t) = \frac{\epsilon_0}{2} E^2(\bm{r}, t), \quad
	\frac{\partial}{\partial V} \mathcal{E\_m}(V, t) = \frac{\mu\_0^{-1}}{2} B^2(\bm{r}, t), \quad
$$

are the electric and the magnetic energy densities, and

$$ \tag{2.17}
	\bm{S}(\bm{r}, t) = \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
$$

is the *instantaneous* [Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector) that represents the direction and the rate of energy flow.

Definitions given in Equations 2.16-2.17 transform Equation 2.11 into a statement of the [Poynting theorem](https://en.wikipedia.org/wiki/Poynting%27s_theorem):

$$ \tag{2.18}
	\frac{\partial^2 \mathcal{W}}{\partial V \partial t} =
	-\nabla \cdot \bm{S} -
	\frac{\partial^2 \mathcal{E\_{em}}}{\partial V \partial t}.
$$

A close examination of 2.18 reveals the fact that our definitions of the field energy and the Poynting vector are not unique, since one may also express the latter as

$$ \tag{2.19}
	\bm{S'}(\bm{r}, t) = \bm{S}(\bm{r}, t) + \nabla \times \bm{X}(\bm{r}, t)
$$

without violating the Poynting theorem \[[5](#references) (vol. II, ch. 27.4)\]. Yet, to the best of the author's knowledge, no disagreement with experiments has been found.

A more pressing concern is whether our formulas, which were found using the microscopic formulation of the Maxwell equations, are valid at the microscopic scale. The answer is: almost always.

A typical derivation[^5] replaces \\(\bm{J}\\) with \\(\bm{J_f}\\) in Equation 2.6, which, after substitution of Equation 1.14.3, leads to the macroscopic counterpart of Equation 2.7 \[[6](#references) (ch. 1.1.4), [8](#references) (ch. 2.4)\]:

[^5]: Work done on the bound current is implicit, since the latter is used to define \\(\bm{D}\\) and \\(\bm{H}\\).

$$ \tag{2.20}
	\int\_{V} \bm{E} \cdot \bm{J_f} \thinspace dV =
	\int\_{V} \bm{E} \cdot \bigg( \nabla \times \bm{H} - \frac{\partial \bm{D}}{\partial t} \bigg) dV.
$$

After performing the same transformations as in Equations 2.8-2.11, we arrive at the expression

$$ \tag{2.21}
	\bm{E} \cdot \bm{J_f} =
	-\nabla \cdot (\bm{E} \times \bm{H}) - \bigg(
	\bm{E} \cdot \frac{\partial \bm{D}}{\partial t} +
	\bm{B} \cdot \frac{\partial \bm{H}}{\partial t} \bigg)
$$

that matches the form required by the Poynting theorem.

Thus, at the macroscopic level, we can define the Poynting vector as

$$ \tag{2.22}
	\bm{S}(\bm{r}, t) = \bm{E}(\bm{r}, t) \times \bm{H}(\bm{r}, t)
$$

with the corresponding rate of change of the electromagnetic energy density

$$ \tag{2.23}
	\frac{\partial^2}{\partial V \partial t}\mathcal{E\_{em}}(V, t)
	= \bm{E}(\bm{r}, t) \cdot \frac{\partial}{\partial t}\bm{D}(\bm{r}, t)
	+ \bm{B}(\bm{r}, t) \cdot \frac{\partial}{\partial t}\bm{H}(\bm{r}, t).
$$

Comparison of Equations 2.17 and 2.22 shows that both formulations of the Poynting vector are equivalent if the measurement is performed inside a *non-magnetic* material. That explains the \\(\mu\_0^{-1}\\) factor: according to the microscopic formulation of the Maxwell equations, an electron (or a proton) is simply a charged region of vacuum. In comparison, due to the spatially-varying refractive index, propagation of electromagnetic fields at the macroscopic scale can be considerably more complex.

Since electromagnetic fields oscillate so rapidly, one typically measures the *time-averaged* Poynting vector

$$ \tag{2.24}
	\braket{\bm{S}}
	= \frac{1}{T} \int\_{-T/2}^{\thinspace T/2} \bm{S}(\bm{r}, t + t') dt'.
$$

It becomes particularly useful once you consider a fixed direction \\(\bm{n}\\). Then, according to Equation 2.14,

$$ \tag{2.25}
	\Epsilon
	= \braket{\bm{S}} \cdot \bm{n}
	= \braket{\bm{S}} \cos{\theta}
$$

is the amount of energy per second per unit area that flows through a surface with the normal \\(\bm{n}\\). That is the definition of [irradiance](https://en.wikipedia.org/wiki/Irradiance) \\(\Epsilon\\).

### Maxwell Equations in the Frequency Domain

The Maxwell equations can be simplified by transforming the fields from the time to the frequency domain.

Define[^1] the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of the electric *vector* field \\(\bm{E}(\bm{r}, t)\\) as

[^1]: The choice of the sign of the complex exponential is arbitrary; we use the modern convention. It directly determines the sign of the imaginary components of both the complex permittivity and the complex refractive index. The symmetric [choice of constants](https://en.wikipedia.org/wiki/Fourier_transform#Other_conventions) ensures that the transformation is [unitary](https://en.wikipedia.org/wiki/Unitary_transformation).

$$ \tag{3.1}
	\bm{E}(\bm{r}, \omega)
	= \mathcal{F} \big\lbrace \bm{E}(\bm{r}, t) \big\rbrace
	= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{i \omega t} dt,
$$

where \\(\omega\\) is the *angular frequency*. \\(\bm{E}(\bm{r}, \omega)\\) is the electric [phasor](https://en.wikipedia.org/wiki/Phasor) field - a field of complex (phase) vectors[^2].

[^2]: Operations and identities of real vector algebra can be applied to any complex vector \\(\bm{c} = \bm{a} + i \bm{b}\\) by expressing it as a combination of two real vectors \\(\bm{a}\\) and \\(\bm{b}\\). The magnitude of the complex vector \\(|\bm{c}| = \bm{c} \cdot \bm{c^{\*}} = \sqrt{\sum{|c_n|^2}}\\) can be computed most efficiently by writing each component in its polar form \\(c_n = r_n \exp(i \theta_n)\\).

\\(\bm{E}(\bm{r}, t)\\), on the other hand, is real, which means its Fourier transform is [Hermitian](https://en.wikipedia.org/wiki/Hermitian_function):

$$ \tag{3.2}
	\bm{E}(\bm{r}, \omega) = [\bm{E}(\bm{r}, -\omega)]^{\*},
$$

where the star denotes the [complex conjugate](https://en.wikipedia.org/wiki/Complex_conjugate)

$$ \tag{3.3}
	z^{\*} = \big[ r e^{i \theta} \big]^{\*} = r e^{-i \theta}.
$$

[Insert picture of a FT of a real function here]

We can go back to the time domain by using the inverse Fourier transform:

$$ \tag{3.4}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	&= \mathcal{F^{-1}} \big\lbrace \bm{E}(\bm{r}, \omega) \big\rbrace \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{-i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \bm{E}(\bm{r}, \omega) e^{-i \omega t} d\omega
	 + \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{0} \bm{E}(\bm{r}, \omega) e^{-i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \Big( \bm{E}(\bm{r}, \omega) e^{-i \omega t} + \big[ \bm{E}(\bm{r}, \omega) e^{-i \omega t} \big]^{\*} \Big) d\omega \cr
	&= \sqrt{\frac{2}{\pi}} \int\_{0}^{\infin} \thinspace \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace d\omega,
\end{aligned}
$$

where the last equality follows from the definition of the real part of a complex number:

$$ \tag{3.5}
	\mathcal{Re} \lbrace z \rbrace
	= \frac{1}{2} \Big( z + z^{\*} \Big)
	= r \cos{ \theta}.
$$

We can define the integral forms of the fields by replacing \\(\bm{E}\\) by \\(\bm{B}\\), \\(\bm{D}\\), \\(\bm{H}\\), \\(\bm{J\_f}\\) or \\(\rho\_f\\) in Equation 3.4. If we substitute these integrals into Equations 1.14.1-1.14.4, the result is a system of integro-differential equations

$$ \tag{3.6}
\begin{aligned}
	&\nabla \times \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{-i \omega t} d\omega +
	 \frac{\partial}{\partial t} \int\_{-\infin}^{\infin} \bm{B}(\bm{r}, \omega) e^{-i \omega t} d\omega = 0, \cr
	&\nabla \cdot  \int\_{-\infin}^{\infin} \bm{B}(\bm{r}, \omega) e^{-i \omega t} d\omega = 0, \cr
	&\nabla \times \int\_{-\infin}^{\infin} \bm{H}(\bm{r}, \omega) e^{-i \omega t} d\omega -
	 \frac{\partial}{\partial t} \int\_{-\infin}^{\infin} \bm{D}(\bm{r}, \omega) e^{-i \omega t} d\omega =
	 \int\_{-\infin}^{\infin} \bm{J_f}(\bm{r}, \omega) e^{-i \omega t} d\omega, \cr
	&\nabla \cdot \int\_{-\infin}^{\infin} \bm{D}(\bm{r}, \omega) e^{-i \omega t} d\omega =
	 \int\_{-\infin}^{\infin} \rho_f(\bm{r}, \omega) e^{-i \omega t} d\omega.
\end{aligned}
$$

It can be further simplified by applying the [Leibniz rule](https://en.wikipedia.org/wiki/Leibniz_integral_rule), taking the time derivatives, and grouping the terms:

$$ \tag{3.7}
\begin{aligned}
	& \int\_{-\infin}^{\infin} \big( \nabla \times \bm{E}(\bm{r}, \omega)
	- i \omega \bm{B}(\bm{r}, \omega) \big) e^{-i \omega t} d\omega = 0, \cr
	& \int\_{-\infin}^{\infin} \big( \nabla \cdot \bm{B}(\bm{r}, \omega) \big) e^{-i \omega t} d\omega = 0, \cr
	& \int\_{-\infin}^{\infin} \big( \nabla \times \bm{H}(\bm{r}, \omega)
	+ i \omega \bm{D}(\bm{r}, \omega) \big) e^{-i \omega t} d\omega
	= \int\_{-\infin}^{\infin} \bm{J_f}(\bm{r}, \omega) e^{-i \omega t} d\omega, \cr
	& \int\_{-\infin}^{\infin} \big( \nabla \cdot \bm{D}(\bm{r}, \omega) \big) e^{-i \omega t} d\omega
	= \int\_{-\infin}^{\infin} \rho_f(\bm{r}, \omega) e^{-i \omega t} d\omega.
\end{aligned}
$$

Equivalently, using the transform operator notation,

$$ \tag{3.8}
\begin{aligned}
	& \mathcal{F^{-1}} \big\lbrace \nabla \times \bm{E}(\bm{r}, \omega)
	- i \omega \bm{B}(\bm{r}, \omega) \big\rbrace = 0, \cr
	& \mathcal{F^{-1}} \big\lbrace \nabla \cdot \bm{B}(\bm{r}, \omega) \big\rbrace = 0, \cr
	& \mathcal{F^{-1}} \big\lbrace \nabla \times \bm{H}(\bm{r}, \omega)
	+ i \omega \bm{D}(\bm{r}, \omega) \big\rbrace
	= \mathcal{F^{-1}} \big\lbrace \bm{J_f}(\bm{r}, \omega) \big\rbrace, \cr
	& \mathcal{F^{-1}} \big\lbrace \nabla \cdot \bm{D}(\bm{r}, \omega) \big\rbrace
	= \mathcal{F^{-1}} \big\lbrace \rho_f(\bm{r}, \omega) \big\rbrace.
\end{aligned}
$$

Generally speaking, the equality of integrands (or function arguments) does not follow from the equality of integrals (or function values). Luckily for us, the inverse Fourier transform is [uniquely defined](https://en.wikipedia.org/wiki/Fourier_inversion_theorem) for a large class of functions. So, under quite general [conditions](https://en.wikipedia.org/wiki/Fourier_inversion_theorem#Conditions_on_the_function), we may say that

$$ \tag{3.9}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \bm{D}(\bm{r}, \omega) = \bm{J_f}(\bm{r}, \omega), &
	&\nabla \cdot  \bm{D}(\bm{r}, \omega) = \rho_f(\bm{r}, \omega).
\end{aligned}
$$

These are the *macroscopic* Maxwell equations for electromagnetic fields with *arbitrary* time dependence.

For future reference, we must also mention the frequency-domain representation of the *microscopic* Maxwell equations. The first two equations are the same, so their Fourier transforms remain unchanged; the third and the fourth equations can be quickly obtained by replacing \\(\bm{J_f}\\) with \\(\bm{J}\\), \\(\bm{D}\\) with \\(\epsilon_0 \bm{E}\\), and \\(\bm{H}\\) with \\(\mu\_0^{-1} \bm{B}\\):

$$ \tag{3.10}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, \omega) \big) + i \omega \big( \epsilon_0 \bm{E}(\bm{r}, \omega) \big) = \bm{J}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \epsilon_0 \bm{E}(\bm{r}, \omega) \big) = \rho(\bm{r}, \omega).
\end{aligned}
$$

Recall that the Maxwell equations are not independent. Since the divergence of curl is zero, taking the divergence of the two equations on the left produces the equations on the right. Of course, if we reduce the total number of equations from four to two, we must also add the expression of conservation of charge, which, in the frequency domain, is

$$ \tag{3.11}
	\rho(\bm{r}, \omega) = \frac{1}{i \omega} \nabla \cdot \bm{J}(\bm{r}, \omega).
$$

Finally, observe that we only need to find the expression of the electric field. From Equations 3.9.1 and 3.10.1, it follows that

$$ \tag{3.12}
	\bm{B}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla \times \bm{E}(\bm{r}, \omega).
$$

In certain situations, it may be advantageous to do it the other way around: find the expression of the magnetic field first, and then use Equation 3.9.3 or 3.10.3 to calculate

$$ \tag{3.13}
\begin{aligned}
	\bm{D}(\bm{r}, \omega)
	&= \frac{1}{i \omega} \Big( \bm{J_f}(\bm{r}, \omega) - \nabla \times \bm{H}(\bm{r}, \omega) \Big), \cr
	\epsilon_0 \bm{E}(\bm{r}, \omega)
	&= \frac{1}{i \omega} \Big( \bm{J}(\bm{r}, \omega) - \nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, \omega) \big) \Big).
\end{aligned}
$$

### Time-Harmonic Fields

If we solve the Maxwell equations in the frequency domain, we may be interested in the spectral composition of the Poynting vector. In order to decompose it, we must express the fields using the inverse Fourier transform:

$$ \tag{4.1}
	\bm{S}(\bm{r}, t)
	= \bm{E}(\bm{r}, t) \times \bm{H}(\bm{r}, t)
	= \mathcal{F^{-1}} \big\lbrace \bm{E}(\bm{r}, \omega) \big\rbrace
	\times \mathcal{F^{-1}} \big\lbrace \bm{H}(\bm{r}, \omega) \big\rbrace \Big).
$$

The expression of the corresponding Poynting phasor is

$$ \tag{4.2}
	\bm{S}(\bm{r}, \omega)
	= \mathcal{F} \big\lbrace \bm{S}(\bm{r}, t) \big\rbrace
	= \mathcal{F} \Big\lbrace
		\mathcal{F^{-1}} \big\lbrace \bm{E}(\bm{r}, \omega)
		\times \mathcal{F^{-1}} \big\lbrace \bm{H}(\bm{r}, \omega) \big\rbrace
	\Big\rbrace.
$$

But that is just a [convolution](https://en.wikipedia.org/wiki/Convolution_theorem#Convolution_theorem_for_inverse_Fourier_transform) of the electric and magnetic phasors:

$$ \tag{4.3}
	\bm{S}(\bm{r}, \omega)
	= \int\_{-\infin}^{\infin}
	\bm{E}(\bm{r}, \omega') \times \bm{H}(\bm{r}, \omega - \omega') d\omega'.
$$

The expression is compact and simple, but not particularly useful, since the value of the Poynting phasor for a particular frequency depends on the entire electromagnetic spectrum.

We can obtain a more practical result at the cost of some generality. Specifically, we must make an assumption that the electromagnetic field is a [periodic function](https://en.wikipedia.org/wiki/Periodic_function) of time, such that the *fundamental period* \\(\thinspace T\_1 = 2 \pi / \omega_1\\) of both[^3] the electric and the magnetic fields is the same \[[5](#references) (vol. I, ch. 50) [6](#references) (ch. 1.3.3, 1.4.3)\]. This limitation is not particularly severe: the fields can often be repeated virtually (outside the time interval of interest), and we may choose the period to be as large as necessary.

[^3]: The fact that the electric and the magnetic fields cannot exist independently can be shown by a simple relativistic argument \[[13](#references) (ch 6.2)\].

One particular feature of periodic functions is the existence of the [Fourier series](https://en.wikipedia.org/wiki/Fourier_series#Complex-valued_functions) representation:

$$ \tag{4.4}
	\bm{E}(\bm{r}, t)
	= \frac{1}{2} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{-i p \omega_1 t}
	= \frac{1}{2} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{-i \omega_p t}
	= \frac{1}{2} \sum\_{p = -\infin}^{\infin} \ket{u_p} \braket{u_p \vert \bm{E}}.
$$

where we defined \\(\omega_p = p \thinspace \omega_1\\), with the *Fourier coefficients* \\(\bm{E_p}\\) given by the integral[^8]

[^8]: In order to obtain the same results found in the physics literature, we had to multiply the series by 1/2, and the coefficients by 2.

$$ \tag{4.5}
	\bm{E_p}(\bm{r})
	= \frac{2}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} \bm{E}(\bm{r}, t) e^{i \omega_p t} dt
	= 2 \braket{u_p \vert \bm{E}}.
$$

Equation 4.5 can be interpreted as the [projection](https://en.wikipedia.org/wiki/Hilbert_space#Fourier_analysis) onto the discrete [Fourier basis](https://en.wikipedia.org/wiki/Fourier_series#Hilbert_space_interpretation), with Equation 4.4 showing the reconstruction. The individual elements of Equation 4.4 (called *harmonics*) possess a key property encapsulated in the *orthonormalization relation*

$$ \tag{4.6}
	\braket{u_p \vert u_q}
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} e^{i (\omega_p - \omega_q) t} dt
	= \delta_{p,q}
$$

which serves as an integral representation of the [Kronecker delta](https://en.wikipedia.org/wiki/Kronecker_delta#Integral_representations) function \\(\delta_{p,q}\\).

Similarly to Equation 3.2, if the time-domain field is real,

$$ \tag{4.7}
	\bm{E_p}(\bm{r}) = [\bm{E_{-p}}(\bm{r})]^{\*},
$$

which permits us to simplify Equation 4.4:

$$ \tag{4.8}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	& = \frac{1}{2} \bm{E_0}(\bm{r})
	+ \frac{1}{2} \sum\_{p = 1}^{\infin} \bm{E_p}(\bm{r}) e^{-i \omega_p t}
	+ \frac{1}{2} \sum\_{p = -\infin}^{-1} \bm{E_p}(\bm{r}) e^{-i \omega_p t} \cr
	&= \frac{1}{2} \bm{E_0}(\bm{r})
	+ \frac{1}{2} \sum\_{p = 1}^{\infin} \Big( \bm{E_p}(\bm{r}) e^{-i \omega_p t} + \big[ \bm{E_p}(\bm{r}) e^{-i \omega_p t} \big]^{\*} \Big) \cr
	& = \frac{1}{2} \bm{E_0}(\bm{r})
	+ \sum\_{p = 1}^{\infin} \thinspace \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{-i \omega_p t} \big\rbrace.
\end{aligned}
$$

Intuitively, a harmonic produces an equivalent amount of vibration regardless of the sign of its frequency.

What happens when we Fourier transform a periodic function? Combining Equations 3.1 and 4.4,

$$ \tag{4.9}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{i \omega t} dt \cr
	&= \frac{1}{2 \sqrt{2 \pi}} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) \int\_{-\infin}^{\infin} e^{i (\omega - \omega_p) t} dt \cr
	&= \sqrt{\frac{\pi}{2}} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) \delta(\omega - \omega_p) \cr
	&= \sqrt{\frac{\pi}{2}} \bigg( \bm{E_0}(\bm{r}) \delta(\omega) +
	\sum\_{p = 1}^{\infin} \Big( \bm{E_p}(\bm{r}) \delta(\omega - \omega_p) + \big[ \bm{E_p}(\bm{r}) \big]^{\*} \delta(\omega + \omega_p) \Big) \bigg),
\end{aligned}
$$

where we used the exponential form of the [Dirac delta](https://en.wikipedia.org/wiki/Dirac_delta_function) function

$$ \tag{4.10}
	\delta(x)
	= \frac{1}{2 \pi} \int\_{-\infin}^{\infin} e^{i x y} dy.
$$

Since the individual harmonics are orthogonal (and, as a result, independent), analysis of a *polychromatic* field with \\(N\\) components can be simplified by considering \\(N\\) *monochromatic* (or *time-harmonic*)[^13] vector fields, such as

[^13]: Most authors abuse the notation by writing Equation 4.11 as \\(\mathcal{Re} \big\lbrace \bm{E}(\bm{r}) e^{-i \omega t} \big\rbrace\\) or \\(\mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace\\).

$$ \tag{4.11}
	\bm{E}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{-i \omega_p t} \big\rbrace,
$$

in isolation, with the corresponding phasor fields

$$ \tag{4.12}
	\bm{E}(\bm{r}, \omega)
	= \sqrt{\frac{\pi}{2}} \bigg( \bm{E_p}(\bm{r}) \delta(\omega - \omega_p) + \big[ \bm{E_p}(\bm{r}) \big]^{\*} \delta(\omega + \omega_p) \bigg).
$$

In practice, it is not necessary to solve the Maxwell equations for the complex conjugate. We simply take \\(\bm{E_p}(\bm{r}) \delta(\omega - \omega_p)\\) and substitute it in place of \\(\bm{E}(\bm{r}, \omega)\\) into Equation 3.7. As a result, we obtain a system formally equivalent to Equation 3.9, with \\(\bm{E}(\bm{r}, \omega)\\) replaced by \\(\bm{E_p}(\bm{r})\\), and \\(\omega\\) by \\(\omega_p\\). After solving for \\(\bm{E_p}(\bm{r})\\), we use Equation 4.11 to obtain the expression of the real electric vector \\(\bm{E}(\bm{r}, t)\\).

Let us put the math to work. Assuming that the electromagnetic field is time-harmonic, Equation 2.22 of the Poynting vector can be written as

$$ \tag{4.13}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&=  \Bigg( \frac{1}{2} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{-i \omega_p t} \Bigg) \times
		\Bigg( \frac{1}{2} \sum\_{q = -\infin}^{\infin} \bm{H_q}(\bm{r}) e^{-i \omega_q t} \Bigg) \cr
	&=  \frac{1}{4} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p}(\bm{r}) e^{-i \omega_p t} \big) \times
		\big( \bm{H_q}(\bm{r}) e^{-i \omega_q t} \big) \cr
	&=  \frac{1}{4} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p}(\bm{r}) e^{-i \omega_p t} \big) \times
		\big[ \bm{H_q}(\bm{r}) e^{-i \omega_q t} \big]^{\*}.
\end{aligned}
$$

The corresponding expression of the time-averaged Poynting vector (cf. Equation 2.24) is

$$ \tag{4.14}
\begin{aligned}
	\braket{\bm{S}}
	= \frac{1}{4} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
	\bm{E_p}(\bm{r}) \times \big[ \bm{H_q}(\bm{r}) \big]^{\*}
	\Bigg(
		\frac{1}{T} \int\_{-T/2}^{\thinspace T/2} e^{i (\omega_q - \omega_p) t'} dt'
	\Bigg).
\end{aligned}
$$

First, consider the case when \\(T = T_1\\). We can immediately apply the orthonormalization relation to obtain

$$ \tag{4.15}
\begin{aligned}
	\braket{\bm{S}}
	&=  \frac{1}{4} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\bm{E_p} \times [\bm{H_q}]^{\*} \thinspace \delta_{q,p} \cr
	&=  \frac{1}{4} \sum\_{p = -\infin}^{\infin}
		\bm{E_p} \times [\bm{H_p}]^{\*} \cr
	&=  \frac{1}{4} (\bm{E_0} \times \bm{H_0})
	  + \frac{1}{4} \sum\_{p = 1}^{\infin} \bm{E_p} \times [\bm{H_p}]^{\*}
	  + \frac{1}{4} \sum\_{p = -\infin}^{-1} \bm{E_p} \times [\bm{H_p}]^{\*} \cr
	&=  \frac{1}{4} (\bm{E_0} \times \bm{H_0})
	  + \frac{1}{4} \sum\_{p = 1}^{\infin}
		\Big(
				  \bm{E_p} \times [\bm{H_p}]^{\*}
		  + \big[ \bm{E_p} \times [\bm{H_p}]^{\*} \big]^{\*}
		\Big) \cr
	&=  \frac{1}{4} (\bm{E_0} \times \bm{H_0})
	  + \frac{1}{2} \sum\_{p = 1}^{\infin}
		\thinspace \mathcal{Re} \big\lbrace \bm{E_p} \times [\bm{H_p}]^{\*} \big\rbrace,
\end{aligned}
$$

which is a real vector, as expected.

In the case when \\(T > T_1\\), the total value of \\(\braket{\bm{S}}\\) is a sum of the contribution from a number of whole periods (given by Equation 4.15) and the contribution from a fraction of the period. If the period used for time-averaging is very large \\((T \gg T_1)\\), the *relative* contribution from a fraction of the period will be much smaller than the *relative* contribution from a large number of whole periods, so the formula of Equation 4.15 should serve as a good approximation.

Equation 4.15 allows us to define the *time-averaged* Poynting phasor \[[8](#references) (ch. 2.4)\]

$$ \tag{4.16}
	\braket{\bm{S_p}} = \frac{1}{2} \bm{E_p}(\bm{r}) \times \big[ \bm{H_p}(\bm{r}) \big]^{\*}
$$

such that (cf. Equation 4.8)

$$ \tag{4.17}
	\braket{\bm{S}}
	= \frac{1}{2} \braket{\bm{S_0}}
	+ \sum\_{p = 1}^{\infin} \mathcal{Re} \big\lbrace \negthinspace \braket{\bm{S_p}} \negthinspace \big\rbrace.
$$

It produces a more compact expression of irradiance

$$ \tag{4.18}
	\Epsilon
	= \braket{\bm{S}} \cdot \bm{n}
	= \frac{1}{2} \braket{\bm{S_0}} \cdot \bm{n}
	+ \sum\_{p = 1}^{\infin} \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cdot \bm{n}
$$

that can be written in terms of [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance)

$$ \tag{4.19}
	\mathrm{E_p}
	= \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cdot \bm{n}
	= \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cos{\theta}.
$$

Having defined both irradiance and spectral irradiance, we can easily compute all other [radiometric quantities](https://en.wikipedia.org/wiki/Radiometry) using integration and differentiation techniques as discussed in the previous article \[[3](#references)\].

### Constitutive Relations

Considered in isolation, the macroscopic formulation of the Maxwell equations is an [undetermined system](https://en.wikipedia.org/wiki/Underdetermined_system) - it has fewer equations than unknowns. This makes it necessary to specify the way the material responds to the applied electromagnetic field. Such assumptions about the material are called the *material equations*, or the [constitutive relations](https://en.wikipedia.org/wiki/Constitutive_equation#Electromagnetism).

The simplest way is to assume that the material responds to weak electromagnetic fields in an approximately linear manner[^6]. In this case, we may express \\(\bm{D}\\) and \\(\bm{H}\\) as [bilinear](https://en.wikipedia.org/wiki/Bilinear_map) functions of \\(\bm{E}\\) and \\(\bm{B}\\) \[[11](#references) (ch. 3.2)\]:

[^6]: In general, the values of the coefficients depend on the intensity of the field. We make an assumption that omitting the higher order terms in the corresponding Taylor series expansion results in a negligible error.

$$ \tag{5.1}
	\begin{bmatrix}
		\bm{D} \cr
		\bm{H} \cr
	\end{bmatrix} =
	\begin{bmatrix}
		\mathcal{C_{11}} & \mathcal{C_{12}} \cr
		\mathcal{C_{21}} & \mathcal{C_{22}} \cr
	\end{bmatrix}
	\begin{bmatrix}
		\bm{E} \cr
		\bm{B} \cr
	\end{bmatrix}.
$$

In general, media may be *dispersive* (dependent on the frequency), *lossy* (absorptive), and *anisotropic* (dependent on the orientation). This implies that each coefficient \\(\mathcal{C_{ij}}\\) is a complex [tensor field](https://en.wikipedia.org/wiki/Tensor#Tensor_fields) that can be represented by a 3x3 matrix.

A radical simplification[^7] of the Maxwell equations can be achieved by assuming that the material is both *linear* and *isotropic*, which means that it can be characterized by three complex numbers:

[^7]: Equations 5.2.1-5.2.3 make a big assumption that is not appropriate for certain types of real materials. For instance, [crystals](https://en.wikipedia.org/wiki/Crystal) have a well-defined [internal structure](https://en.wikipedia.org/wiki/Crystal_structure) which makes them inherently anisotropic \[[5](#references) (vol. II, ch. 30)\]. Since our application is primarily concerned with simple fluids, a linear, isotropic material is reasonable assumption that offers significant theoretical advantages (such as simplicity). However, this assumption is potentially not suitable for a more general application \[[6](#references) (ch. 2)\].

$$ \tag{5.2}
\begin{aligned}
	&\bm{J\_i}(\bm{r}, \omega) \approx \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{D}   (\bm{r}, \omega) \approx \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{B}   (\bm{r}, \omega) \approx \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega),
\end{aligned}
$$

where \\(\sigma\\) is the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity), \\(\epsilon\\) is the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity), and \\(\mu\\) is the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \[[6](#references) (ch. 1.1.2), [7](#references) (ch. 2.3), [8](#references) (ch. 2.1)\].

Care must taken when transforming Equations 5.2.1-5.2.3 to the time domain, since a multiplication in the frequency domain leads to a convolution in the time domain.

Conservation of charge implies that

$$ \tag{5.3}
	\rho_i(\bm{r}, \omega) = \frac{1}{i \omega} \nabla \cdot \bm{J_i}(\bm{r}, \omega).
$$

Equations 5.2.1 and 5.3 assume that the *free* charges and currents may be decomposed into the *source* and the *induced* parts:

$$ \tag{5.4}
	\bm{J\_f} = \bm{J\_s} + \bm{J\_i}, \quad
	\rho\_f = \rho\_s + \rho\_i.
$$

If the constitutive relations hold, the Maxwell equations in the frequency domain take the following form:

$$ \tag{5.5}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega)
	= \bm{J\_s}(\bm{r}, \omega) + \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\nabla \cdot  \big( \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega) + \frac{1}{i \omega} \nabla \cdot \big( \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big).
\end{aligned}
$$

Using the definition of the [complex permittivity](https://en.wikipedia.org/wiki/Permittivity#Complex_permittivity) \[[7](#references) (ch. 2.8), [8](#references) (ch. 2.3)\]

$$ \tag{5.6}
	\varepsilon(\bm{r}, \omega) = \epsilon(\bm{r}, \omega) + i \frac{\sigma(\bm{r}, \omega)}{\omega},
$$

we obtain the Maxwell equations for *linear, isotropic* media:

$$ \tag{5.7}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J\_s}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega).
\end{aligned}
$$

Alternatively, the same set of equations can be expressed in terms of the \\(\bm{B}\\) field:

$$ \tag{5.8}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \big( \mu^{-1}(\bm{r}, \omega) \bm{B}(\bm{r}, \omega) \big) + i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J_s}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho_s(\bm{r}, \omega).
\end{aligned}
$$

Writing the Maxwell equations this way allows us to compare the macroscopic Equations 5.8 with the microscopic Equations 3.10. Observe that, when \\(\varepsilon\\) and \\(\mu\\) are independent from \\(\bm{r}\\) (or if the spatially-varying component is attributed to the current term on the right-hand side), their solutions are formally the same.

### Helmholtz Equation

Consider a region of space without any source charges or currents. Physically, this means that the region is *source-free*; but that does not mean it contains no energy. To find an expression of the internal field, we shall explore all general solutions of the Maxwell equations (we can find a special solution once we specify the boundary conditions), and determine how these solutions evolve over time.

Setting \\(\bm{J\_s} = \rho\_s = 0\\) in Equations 5.7.3-5.7.4, we obtain a coupled system of linear homogeneous equations:

$$ \tag{6.1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = 0.
\end{aligned}
$$

Next, assume that the medium is *homogeneous* (constant in space):

$$ \tag{6.2}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \mu(\omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \varepsilon(\omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{E}(\bm{r}, \omega) = 0.
\end{aligned}
$$

Let us focus on the electric field \\(\bm{E}\\). Take the curl of Equation 6.2.1 and substitute \\(\nabla \times \bm{H}\\) from Equation 6.2.3:

$$ \tag{6.3}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - \omega^2 \mu(\omega) \varepsilon(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

It is convenient to group the constants by defining

$$ \tag{6.4}
	k(\omega) = \omega \sqrt{\mu(\omega) \varepsilon(\omega)},
$$

so that the Equation 6.3 can be written as

$$ \tag{6.5}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - k^2(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

We can simplify this equation further by recalling the [curl of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity

$$ \tag{6.6}
	\nabla \times \nabla \times \bm{E} = \nabla (\nabla \cdot \bm{E}) - \nabla^2 \bm{E},
$$

where

$$ \tag{6.7}
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

$$ \tag{6.8}
	\mathrm{div} \big( \mathrm{grad}(\bm{E}) \big) =
	\nabla^2 \bm{E} =
	(\nabla \cdot \nabla) \bm{E} =
	\frac{\partial^2 \bm{E}}{\partial x^2} + \frac{\partial^2 \bm{E}}{\partial y^2} + \frac{\partial^2 \bm{E}}{\partial z^2}
$$

is the *vector* [Laplace](https://en.wikipedia.org/wiki/Laplace_operator) operator (*scalar* Laplace operator applied to each vector component).

Use Equation 6.6 to expand Equation 6.5:

$$ \tag{6.9}
	\nabla \big( \nabla \cdot \bm{E}(\bm{r}, \omega) \big) - \nabla^2 \bm{E}(\bm{r}, \omega) - k^2(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

Substitution of Equation 6.2.4 into 6.9 yields the vector equation of the electric field \\(\bm{E}\\):

$$ \tag{6.10}
	\nabla^2 \bm{E}(\bm{r}, \omega) + k^2(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$


By considering each vector component in a Cartesian coordinate system, we obtain a system of three *homogeneous* [Helmholtz equations](https://en.wikipedia.org/wiki/Helmholtz_equation):

$$ \tag{6.11}
\begin{aligned}
	\big( \nabla^2 + k^2(\omega) \big) E\_x(\bm{r}, \omega) = 0, \cr
	\big( \nabla^2 + k^2(\omega) \big) E\_y(\bm{r}, \omega) = 0, \cr
	\big( \nabla^2 + k^2(\omega) \big) E\_z(\bm{r}, \omega) = 0. \cr
\end{aligned}
$$

To find a solution, let us first consider a simpler 1-dimensional Helmholtz equation

$$ \tag{6.12}
	\frac{d^2 \psi(x)}{d x^2} = -k^2 \psi(x).
$$

If the derivative of a function is the function itself (times a constant), the function is clearly an exponential:

$$ \tag{6.13}
	\psi(x) =
	\psi(0) e^{\pm i k x},
$$

where \\(\psi(0)\\) is a complex constant.

Extension to 3 dimensions is straightforward. If we rotate the coordinate frame so that the \\(x\\)-axis points along the unit vector \\(\bm{n}\\),

$$ \tag{6.14}
	\psi(\bm{r}, \bm{n}) =
	\psi(0, \bm{n}) e^{ \pm i k (\bm{r} \cdot \bm{n})}
$$

is a valid solution for a certain value of \\(\bm{n}\\). To avoid clutter, we shall adhere to the modern space convention with the positive sign (the opposite of our time convention); the negative solution can be obtained by reversing the direction of \\(\bm{n}\\).

How should we choose the direction of \\(\bm{n}\\)? It depends on the location of sources. Unless they are explicitly specified, we can use *any* value of \\(\bm{n}\\). And because the Helmholtz equation is both linear and homogeneous, we can actually use *every* value of \\(\bm{n}\\) by invoking the superposition principle. Thus, the general solution is an integral taken over the surface of the unit sphere \\(\mathbb{S^2}\\) [measured](https://en.wikipedia.org/wiki/Lebesgue_integration#Construction) by the [solid angle](https://en.wikipedia.org/wiki/Solid_angle) \\(d\Omega\\):

$$ \tag{6.15}
	\psi(\bm{r}) =
	\oint\_{\mathbb{S}^2} \psi(\bm{r}, \bm{n}) d\Omega =
	\oint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{i k (\bm{r} \cdot \bm{n})} d\Omega.
$$

Equation 6.15 can be used to solve Equation 6.10:

$$ \tag{6.16}
	\bm{E}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E}(\bm{r}, \bm{n}, \omega) d\Omega =
	\oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega.
$$

That is a general solution of the Maxwell equations for *linear, isotropic, homogeneous, source-free* media.

### Plane waves

We can find a solution of the Maxwell equations in the time domain by performing the inverse Fourier transform of Equation 6.16:

$$ \tag{7.1}
\begin{aligned}
	\bm{E}(\bm{r}, t)
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} e^{-i \omega t} d\Omega d\omega \cr
	&= \sqrt{\frac{2}{\pi}} \int\_{0}^{\infin} \oint\_{\mathbb{S}^2} \mathcal{Re} \big\lbrace \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} e^{-i \omega t} \big\rbrace d\Omega d\omega.
\end{aligned}
$$

This triple integral represents a [wave packet](https://en.wikipedia.org/wiki/Wave_packet). It is a collection of *vector* [plane waves](https://en.wikipedia.org/wiki/Plane_wave) \[[6](#references) (ch. 1.4.2), [8](#references) (ch. 3.1)\] of the form

$$ \tag{7.2}
	\bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t}
	= \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} e^{-i \omega t}
	= \bm{E_0}(\bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n}) - i \omega t},
$$

each composed of three *scalar* plane waves such as

$$ \tag{7.3}
	E(\bm{r}, \bm{n}, \omega) e^{-i \omega t}
	= E(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} e^{-i \omega t}
	= E_0(\bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n}) - i \omega t},
$$

with the optical properies of the medium encapsulated in the [complex wavenumber](https://en.wikipedia.org/wiki/Wavenumber#Complex)

$$ \tag{7.4}
	k(\omega) = \omega \sqrt{\varepsilon(\omega) \mu(\omega)}.
$$

\\(\bm{E_0}\\) is called the *polarization phasor* (for reasons that go beyond the scope of this article).

If we define the [relative complex permittivity](https://en.wikipedia.org/wiki/Relative_permittivity#Lossy_medium) \\(\varepsilon\_r\\) and the [relative permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)#Relative_permeability_and_magnetic_susceptibility) \\(\mu\_r\\) using the vacuum as reference,

$$ \tag{7.5}
	\varepsilon\_r(\omega) = \frac{\varepsilon(\omega)}{\epsilon_0}, \quad
	\mu\_r(\omega) = \frac{\mu(\omega)}{\mu\_0},
$$

the complex wavenumber can be redefined as

$$ \tag{7.6}
	k(\omega) =
	\omega \sqrt{\varepsilon(\omega) \mu(\omega)} =
	\omega \sqrt{\epsilon_0 \mu\_0} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	\frac{\omega}{c} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)}.
$$

It is convenient to use a parametrization that does not involve taking a square root. Thus, we define two positive real numbers, the [refractive index](https://en.wikipedia.org/wiki/Refractive_index) \\(\eta\\) and the [attenuation index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\kappa\\) \[[6](#references) (ch. 14.1), [8](#references) (ch. 3.1)\], by

$$ \tag{7.7}
	\eta(\omega) + i \kappa(\omega) =
	\sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	c \sqrt{\Big( \epsilon(\omega) + i \frac{\sigma(\omega)}{\omega} \Big) \mu(\omega)}.
$$

For high frequencies, such as those encountered in optics, we may perform a [Laurent series](https://en.wikipedia.org/wiki/Laurent_series) [expansion](https://www.wolframalpha.com/input/?i=series+sqrt%28a-I%2Fw*b%29) at infinity:

$$ \tag{7.8}
	\eta(\omega) + i \kappa(\omega) \approx
	c \bigg( \sqrt{\epsilon \mu} + i \frac{\sigma \mu}{2 \omega \sqrt{\epsilon \mu}} \bigg),
$$

which gives an approximate mapping between the optical and the physical parameters[^10].

[^10]: Keep in mind that, in general, the permittivity, permeability, and conductivity are complex numbers.

The combination of Equations 7.6-7.7 produces the [dispersion relation](https://en.wikipedia.org/wiki/Dispersion_relation)

$$ \tag{7.9}
	k(\omega)
	= \frac{\omega}{c} \big( \eta(\omega) + i \kappa(\omega) \big)
	= \omega \bigg( \frac{1}{v_p(\omega)} + \frac{i}{v_a(\omega)} \bigg),
$$

where \\(v_p = c/\eta\\) is the [phase velocity](https://en.wikipedia.org/wiki/Phase_velocity) and \\(v_a=c/\kappa\\) is the *amplitude velocity*. It shows that, in a dispersive medium, waves of different frequencies propagate at different rates.

In order to develop some intuition about the role of the refractive index \[[5](#references) (vol. II, ch. 32.4)\], consider the spatial component of a scalar plane wave

$$ \tag{7.10}
\begin{aligned}
	E(\bm{r}, \bm{n}, \omega)
	&= E\_0 e^{i k (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{i \delta} e^{i \omega (1/v_p + i/v_a) (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} e^{i \delta + i \omega (\bm{r} \cdot \bm{n}) / v_p}.
\end{aligned}
$$

Multiplication by the temporal component \\(\exp(-i \omega t)\\) yields the full expression of a scalar plane wave:

$$ \tag{7.11}
\begin{aligned}
	E(\bm{r}, \bm{n}, \omega) e^{-i \omega t}
	&= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} e^{i \delta + i \omega (\bm{r} \cdot \bm{n}) / v_p} e^{-i \omega t}.
\end{aligned}
$$

As any complex number, it can be expressed in terms of the *amplitude* \\(|E|\\) and the *phase* \\(\theta\\), both of which are [real-valued functions](https://en.wikipedia.org/wiki/Real-valued_function).

Let us perform [dimensional analysis](https://en.wikipedia.org/wiki/Dimensional_analysis) of Equation 7.11.

First, notice that \\((\bm{r} \cdot \bm{n}) / v\\) has units of time. Then, take the [argument](https://en.wikipedia.org/wiki/Argument_(complex_analysis)) of the expression

$$ \tag{7.12}
	\theta(\bm{r}, t)
	= \mathcal{Arg} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace
	= \delta + \omega \big(\bm{r} \cdot \bm{n} - v_p t \big) / v_p.
$$

Notice that, for any \\(\Delta t\\),

$$ \tag{7.13}
	\theta(\bm{r}, t) = \theta(\bm{r} + (v_p \Delta t) \bm{n}, \thinspace t + \Delta t).
$$

This implies that \\(\theta\\) represents a plane propagating along its normal \\(\bm{n}\\) at the phase velocity \\(v_p\\). In general, surfaces of constant phase are called *cophasal*, or *wavefronts*.

Taking the real part of Equation 7.11 allows us to uncover the *wave amplitude*

$$ \tag{7.14}
	\mathcal{Re} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace
	= |E| \cos{\theta}
	= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} \cos{\theta},
$$

where \\(|E|\\) is the spatially-varying *peak amplitude*.

If the attenuation index \\(\kappa = 0\\), the amplitude velocity \\(v_a = \infty\\), and Equation 7.14 represents a regular sine wave.

[Insert Picture Here]

On the other hand, \\(\kappa > 0\\) produces an exponential decay characteristic of an [evanescent wave](https://en.wikipedia.org/wiki/Evanescent_field).

[Insert Picture Here]

Equation 7.1 can be seen as a decomposition of the electric field into plane waves interfering with each other. By [interference](https://en.wikipedia.org/wiki/Wave_interference), we simply mean that when two waves (often assumed to be of the same frequency) are added together, the peak amplitude of the resulting wave is, in general, different from the sum of the peak amplitudes unless the waves oscillate *in-phase* (in such a way that the phase difference is zero), in which case interference is said to be *constructive*. In the worst case scenario, the peaks align with the troughs, and *destructive* interference causes the waves to completely cancel each other. A typical *out-of-phase* case falls in between these two extremes \[[5](#references) (vol. I, ch. 29)\].

[Insert Picture Here]

It is easy to show that, under favorable conditions, the field vectors oscillate in the plane of the wave. In general, the Maxwell equations (cf. Equations 3.9.2, 3.9.4) tell us that

$$ \tag{7.15}
	\nabla \cdot \bm{B}(\bm{r}, \omega) = 0, \quad
	\nabla \cdot \bm{D}(\bm{r}, \omega) = \rho_s(\bm{r}, \omega).
$$

More specifically, in a source-free region of a linear, isotropic, homogeneous medium (cf. Equation 6.2.2, 6.2.4),

$$ \tag{7.16}
	\nabla \cdot \bm{H}(\bm{r}, \omega) = 0, \quad
	\nabla \cdot \bm{E}(\bm{r}, \omega) = 0.
$$

Take \\(\bm{E}\\) as an example. Substitute Equation 6.16 into 7.16.2:

$$ \tag{7.17}
	\nabla \cdot \oint\_{\mathbb{S}^2} \bm{E}(\bm{r}, \bm{n}, \omega) d\Omega =
	\nabla \cdot \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega = 0.
$$

Moving divergence under the integral sign leads to

$$ \tag{7.18}
	\nabla \cdot \bm{E}(\bm{r}, \bm{n}, \omega)
	= \Big( \nabla e^{i k(\omega) (\bm{r} \cdot \bm{n})} \Big) \cdot \bm{E}(0, \bm{n}, \omega)
	= i k(\omega) \bm{n} \cdot \bm{E}(\bm{r}, \bm{n}, \omega)
	= 0.
$$

After division by the constant \\(i k\\) and multiplication by \\(\exp(-i \omega t)\\), it is clear that the electric field vector is orthogonal to the plane normal at all times:

$$ \tag{7.19}
	\bm{n} \cdot \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace
	= 0.
$$

We can prove that the magnetic vector possesses the same property in a similar manner. According to Equation 3.9.1,

$$ \tag{7.20}
	\nabla \times \bm{E}(\bm{r}, \omega) = i \omega \bm{B}(\bm{r}, \omega).
$$

Substitute Equation 6.16 into the left-hand side and expand the expression of the curl

$$ \tag{7.21}
	\nabla \times \bm{E}(\bm{r}, \bm{n}, \omega)
	= \Big( \nabla e^{i k(\omega) (\bm{r} \cdot \bm{n})} \Big) \times \bm{E}(0, \bm{n}, \omega)
	= i k(\omega) \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega).
$$

After division by \\(i \omega\\), Equation 7.20 becomes equivalent to

$$ \tag{7.22}
	\frac{k(\omega)}{\omega} \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega) = \bm{B}(\bm{r}, \bm{n}, \omega).
$$

By taking the dot product of the resulting expression with \\(\bm{n}\\), we obtain an analog of Equation 7.18:

$$ \tag{7.23}
	\frac{k(\omega)}{\omega} \bm{n} \cdot \big( \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega) \big)
	= \bm{n} \cdot \bm{B}(\bm{r}, \bm{n}, \omega)
	= 0,
$$

where the last equality follows from the property of the [triple product](https://en.wikipedia.org/wiki/Triple_product) \\(\bm{n} \cdot (\bm{n} \times \bm{E}) = \bm{E} \cdot (\bm{n} \times \bm{n})\\).

Thus, the magnetic field vector is always orthogonal to the plane normal:

$$ \tag{7.24}
	\bm{n} \cdot \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace
	= 0.
$$

With \\(\bm{E}\\) and \\(\bm{B}\\) both orthogonal to \\(\bm{n}\\), the only remaining question is regarding their mutual orientation. Using the definition of the wavenumber given by Equation 7.9, Equation 7.22 can be written as

$$ \tag{7.25}
	\frac{1}{c} \big(\eta(\omega) + i \kappa(\omega) \big) \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega)
	= \bm{B}(\bm{r}, \bm{n}, \omega).
$$

If we multiply the both sides by \\(\exp(-i \omega t)\\) and take the real part, the result

$$ \tag{7.26}
	\bm{n} \times \bigg( \frac{\eta(\omega)}{c} \mathcal{Re} \big\lbrace \bm{E} e^{-i \omega t} \big\rbrace
	- \frac{\kappa(\omega)}{c} \mathcal{Im} \big\lbrace \bm{E} e^{-i \omega t} \big\rbrace \bigg)
	= \mathcal{Re} \big\lbrace \bm{B} e^{-i \omega t} \big\rbrace
$$

shows us that the electric and the magnetic field vectors of a plane wave are perpendicular only if \\(\kappa = 0\\). If that is the case, \\(\lbrace \bm{E}, \bm{B}, \bm{n} \rbrace\\) is an orthogonal triad of vectors that defines the geometric configuration of a plane wave in a non-absorptive, linear, isotropic, homogeneous, source-free medium:

$$ \tag{7.27}
	\bm{n} \times \frac{\mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace}{\big\vert \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big\vert}
	= \frac{\mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace}{\big\vert \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big\vert}.
$$

[Insert Picture Here]

Recall that we have seen the \\(\bm{E} \times \bm{B}\\) expression before (cf. Equation 2.17). Assuming the fields are monochromatic, and the medium where we perform the measurements - non-magnetic, Equation 7.27 suggests that

$$ \tag{7.28}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
	= \mu\_0^{-1} |\bm{E}(\bm{r}, t)| |\bm{B}(\bm{r}, t)| \bm{n}
$$

is the expression of the Poynting vector of a plane wave.

If the field vectors are mutually orthogonal, Equations 7.26-7.27 define the ratio of wave amplitudes:

$$ \tag{7.29}
	\frac{\big\vert \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big\vert}{\big\vert \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big\vert}
	= \frac{\eta(\omega)}{c}.
$$

This equality has two consequences. First, it says that the electric and the magnetic vectors of a plane wave oscillate in-phase, with the magnitude of both vectors rising and falling at the same point in space and time. Stated mathematically, if we align the triad \\(\lbrace \bm{E_0}, \bm{B_0}, \bm{n} \rbrace\\) with the \\(\lbrace x,y,z \rbrace\\) axes of a Cartesian coordinate system, then

$$ \tag{7.30}
\begin{aligned}
	\mathcal{Arg} \big\lbrace E_x(0, \bm{n}, \omega) \big\rbrace &=
	\mathcal{Arg} \big\lbrace B_y(0, \bm{n}, \omega) \big\rbrace =
	\delta, \cr
	\mathcal{Arg} \big\lbrace E_y(0, \bm{n}, \omega) \big\rbrace &=
	\mathcal{Arg} \big\lbrace B_x(0, \bm{n}, \omega) \big\rbrace =
	\frac{\pi}{2} \pm \pi n.
\end{aligned}
$$

Secondly, it means we can compute the Poynting vector without explicit consideration of the magnetic field:

$$ \tag{7.31}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \mu\_0^{-1} \frac{\eta(\omega)}{c} {\big\vert \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big\vert}^2 \bm{n} \cr
	&= \mu\_0^{-1} \frac{\eta}{c} \Big( |E_{0,x}|^2 \cos^2{\theta_x} + |E_{0,y}|^2 \cos^2{\theta_y} \Big) \bm{n},
\end{aligned}
$$

where we have utilized the expression of the wave amplitude of Equation 7.14.

The average value of a squared cosine is \\(\frac{1}{2}\\), so the corresponding irradiance[^11] value (cf. Equation 2.25) is

[^11]: Since the electromagnetic fields is monochromatic, the expressions of irradiance and spectral irradiance are identical.

$$ \tag{7.32}
	\Epsilon
	= \braket{\bm{S}} \cdot \bm{n'}
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} |\bm{E_0}|^2 \big( \bm{n} \cdot \bm{n'} \big).
$$

Without proof, we shall state an interesting fact. The time-averaged energy density (cf. Equations 2.23-2.24) of the field of a plane wave is

$$ \tag{7.33}
	\bigg\langle \frac{\partial \mathcal{E\_{em}}}{\partial V} \bigg\rangle
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta^2}{c^2} |\bm{E_0}|^2.
$$

This implies that the amount of energy that goes through a unit area per unit time is the amount of energy per unit volume times the velocity at which the energy flows \[[5](#references) (vol. II, ch. 27)\]:

$$ \tag{7.34}
	\Epsilon
	= \frac{c}{\eta} \bigg\langle \frac{\partial \mathcal{E\_{em}}}{\partial V} \bigg\rangle \big( \bm{n} \cdot \bm{n'} \big).
$$

If the medium is absorptive or magnetic, the formula of the *instantaneous* Poynting vector becomes more complicated. Nevertheless, we can easily find the *time-averaged* Poynting vector using Equations 4.16-4.17:

$$ \tag{7.35}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \Big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) \times \big[ \bm{H}(\bm{r}, \bm{n}, \omega) \big]^{\*} \Big\rbrace.
$$

Now, according to Equations 5.2.3 and 7.22,

$$ \tag{7.36}
	\frac{k(\omega)}{\omega \mu(\omega)} \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega) = \bm{H}(\bm{r}, \bm{n}, \omega).
$$

If we take the complex conjugate of Equation 7.36, substitute it into Equation 7.35, and apply the [vector triple product](https://en.wikipedia.org/wiki/Triple_product#Vector_triple_product) identity

$$ \tag{7.37}
	\bm{a} \times (\bm{b} \times \bm{c}) =
	\bm{b} (\bm{a} \cdot \bm{c}) -
	\bm{c} (\bm{a} \cdot \bm{b}),
$$

which is valid for both real and complex vectors, we obtain the expression

$$ \tag{7.38}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \bigg\lbrace
		\frac{k^{\*}}{\omega \mu^{\*}}
		\Big( \bm{E} \times \bm{n} \times \bm{E^{\*}} \Big)
	\bigg\rbrace
	= \frac{1}{2} \mathcal{Re} \bigg\lbrace
		\frac{k^{\*}}{\omega \mu^{\*}}
		\Big( \bm{n} \big( \bm{E} \cdot \bm{E^{\*}} \big) -
		\bm{E^{\*}} \big( \bm{E} \cdot \bm{n} \big) \Big)
	\bigg\rbrace.
$$

From the definitions of \\(\bm{E}\\) and \\(k\\) given by Equations 7.2 and 7.9, respectively, it follows that

$$ \tag{7.39}
	\bm{E} \cdot \bm{E^{\*}}
	= |\bm{E}|^2
	= |\bm{E_0}|^2 e^{-(2 \kappa \omega / c) (\bm{r} \cdot \bm{n})}
$$

is a real number. The constants in the exponential may be combined, giving rise to the [absorption coefficient](https://en.wikipedia.org/wiki/Attenuation_coefficient#Absorption_and_scattering_coefficients)

$$ \tag{7.40}
	\alpha(\omega)
	= 2 \kappa(\omega) \frac{\omega}{c}
	= 4 \pi \frac{\kappa(\omega)}{\lambda_0},
$$

where \\(\lambda_0 = 2 \pi c / \omega\\) is the *free-space* wavelength.

Furthermore, the alternative definition of the complex wavenumber (cf. Equation 7.6) suggests that

$$ \tag{7.41}
	\mathcal{Re} \bigg\lbrace
		\frac{k^{\*}}{\omega \mu^{\*}}
	\bigg\rbrace
	= \mathcal{Re} \bigg\lbrace
		\frac{ \omega \sqrt{\varepsilon^{\*} \mu^{\*}} }{ \omega \mu^{\*} }
	\bigg\rbrace
	= \mathcal{Re} \bigg\lbrace
		\sqrt{ \frac{\varepsilon^{\*}}{\mu^{\*}} }
	\bigg\rbrace
	= \mathcal{Re} \bigg\lbrace
		\sqrt{ \frac{\varepsilon}{\mu} }
	\bigg\rbrace.
$$

Finally, according to Equation 7.18, \\(\bm{E} \cdot \bm{n} = 0\\). Thus, if we combine Equations 7.38-7.41, we obtain the general expression of the time-averaged Poynting vector that accounts for absorption and magnetization:

$$ \tag{7.42}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \bigg\lbrace
		\sqrt{ \frac{\varepsilon}{\mu} }
	\bigg\rbrace |\bm{E_0}|^2 e^{-\alpha (\bm{r} \cdot \bm{n})} \bm{n}.
$$

### Electromagnetic Potential

The solutions of the Maxwell equations we have found so far have a limited range of validity. The medium must be isotropic and either homogeneous (which prohibits scattering) or divided into several bounded homogeneous regions (which permits scattering only at the boundaries), and the material's response to the incident electromagnetic field may only be purely linear. Moreover, while the method of solving a system of differential equations augmented with boundary conditions (the so-called [boundary value problem](https://en.wikipedia.org/wiki/Boundary_value_problem)) is suitable for many simple cases (such as reflection and transmission of a plane wave at a planar interface, which leads to the [Fresnel equations](https://en.wikipedia.org/wiki/Fresnel_equations)), it quickly becomes unwieldy for more complex problems. Another approach tends to become more viable, where one reformulates the field in terms of the [electromagnetic potential](https://en.wikipedia.org/wiki/Electromagnetic_four-potential), as this leads to an integral (rather than a differential) solution of the Maxwell equations.

Take another look at the equation of the magnetic field. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, Equation 1.1.2 can be written as

$$ \tag{8.1}
	\nabla \cdot \bm{B}(\bm{r}, t) = \nabla \cdot \big( \nabla \times \bm{A}(\bm{r}, t) \big) = 0,
$$

where \\(\bm{A}\\) is called a *vector potential* \[[5](#references) (vol. II, ch. 18), [6](#references) (ch. 2.1)\].

Next, substitute the definition of \\(\bm{A}\\) into Equation 1.1.1 to obtain

$$ \tag{8.2}
	\nabla \times \Big( \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{A}(\bm{r}, t) \Big) = 0.
$$

As the [curl of gradient](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_gradient_is_zero) is zero, Equation 8.2 can be used to define the *scalar potential* \\(\phi\\):

$$ \tag{8.3}
	\bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{A}(\bm{r}, t) = -\nabla \phi(\bm{r}, t).
$$

Thus both the electric and the magnetic fields are uniquely defined by the vector and the scalar potentials:

$$ \tag{8.4}
\begin{aligned}
	&\bm{E}(\bm{r}, t) = -\frac{\partial}{\partial t} \bm{A}(\bm{r}, t) - \nabla \phi(\bm{r}, t), &
	&\bm{B}(\bm{r}, t) = \nabla \times \bm{A}(\bm{r}, t).
\end{aligned}
$$

The converse is not true. Since the [curl of gradient](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_gradient_is_zero) is zero, the vector potential

$$ \tag{8.5}
	\bm{A'}(\bm{r}, t) = \bm{A}(\bm{r}, t) + \nabla \chi(\bm{r}, t)
$$

corresponds to the same magnetic field

$$ \tag{8.6}
	\bm{B}(\bm{r}, t) = \nabla \times \bm{A}(\bm{r}, t) = \nabla \times \bm{A'}(\bm{r}, t).
$$

If we substitute Equation 8.5 into 8.3 and group the terms under the gradient sign, we obtain the expression of the transformed scalar potential:

$$ \tag{8.7}
	\phi'(\bm{r}, t) = \phi(\bm{r}, t) - \frac{\partial}{\partial t} \chi(\bm{r}, t).
$$

Since the electric and the magnetic field are independent of a particular choice of \\(\chi\\), they are said to be *invariant* under a [gauge transformation](https://en.wikipedia.org/wiki/Gauge_theory#Classical_electromagnetism) given by Equations 8.5 and 8.7.

What is a good choice of \\(\chi\\)? That depends on the mathematical formulation of a particular problem. Usually, we want \\(\bm{A}\\) to have a certain desirable property that simplifies some equation.

In general, a smooth vector field \\(\bm{A}\\) can be [decomposed](https://en.wikipedia.org/wiki/Helmholtz_decomposition) into a sum of a divergence-free ([solenoidal](https://en.wikipedia.org/wiki/Solenoidal_vector_field)) component \\(\bm{A'}\\) and a curl-free ([irrotational](https://en.wikipedia.org/wiki/Conservative_vector_field#Irrotational_vector_fields)) component \\(\bm{A''}\\):

$$ \tag{8.8}
\begin{aligned}
	&\bm{A}(\bm{r}, t) = \bm{A'}(\bm{r}, t) + \bm{A''}(\bm{r}, t), &
	&\nabla \cdot  \bm{A' }(\bm{r}, t) = 0, &
	&\nabla \times \bm{A''}(\bm{r}, t) = 0. &
\end{aligned}
$$

This indicates a way to manipulate the divergence of \\(\bm{A}\\) without affecting its curl:

$$ \tag{8.9}
\begin{aligned}
	& \nabla \times \bm{A}(\bm{r}, t) = \nabla \times \bm{A'}(\bm{r}, t) = \bm{B}(\bm{r}, t), \cr
	& \nabla \cdot \bm{A}(\bm{r}, t) = \nabla \cdot \bm{A''}(\bm{r}, t) = -\nabla^2 \chi(\bm{r}, t).
\end{aligned}
$$

We shall relate \\(\chi\\) to \\(\phi\\) in a rather non-obvious (but, as we shall soon see, convenient) manner:

$$ \tag{8.10}
	\nabla^2 \chi(\bm{r}, t) = \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t).
$$

The combination of Equations 8.9.2 and 8.10 yields the [Lorenz condition](https://en.wikipedia.org/wiki/Lorenz_gauge_condition)

$$ \tag{8.11}
\begin{aligned}
	\nabla \cdot \bm{A}(\bm{r}, t) = -\frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t).
\end{aligned}
$$

Let us justify our choice of the gauge transformation by expressing the microscopic Maxwell equations in terms of the electromagnetic potential. Begin by substituting Equations 8.4 into 1.1.3-1.1.4:

$$ \tag{8.12}
\begin{aligned}
	&\nabla \times \nabla \times \bm{A}(\bm{r}, t) + \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	+ \nabla \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t)
	= \frac{\bm{J}(\bm{r}, t)}{\mu_0^{-1}}, \cr
	&-\frac{\partial}{\partial t} \nabla \cdot \bm{A}(\bm{r}, t) - \nabla^2 \phi(\bm{r}, t)
	= \frac{\rho(\bm{r}, t)}{\epsilon_0}.
\end{aligned}
$$

We can immediately use the Lorenz condition to eliminate \\(\bm{A}\\) from Equation 8.12.2. The result is an inhomogeneous scalar *wave equation*

$$ \tag{8.13}
	\nabla^2 \phi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \phi(\bm{r}, t)
	= -\frac{\rho(\bm{r}, t)}{\epsilon_0}.
$$

Equation 8.12.1 can be expanded using the curl of curl identity given by Equation 6.6:

$$ \tag{8.14}
	\nabla \big( \nabla \cdot \bm{A}(\bm{r}, t) \big) - \nabla^2 \bm{A}(\bm{r}, t) + \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	+ \nabla \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t)
	= \frac{\bm{J}(\bm{r}, t)}{\mu_0^{-1}}.
$$

Once again, we use the Lorenz condition, but this time, we do it in order to eliminate \\(\phi\\). We obtain an inhomogeneous vector *wave equation*

$$ \tag{8.15}
	\nabla^2 \bm{A}(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	= -\frac{\bm{J}(\bm{r}, t)}{\mu_0^{-1}}.
$$

Putting it all together, the microscopic formulation of the Maxwell equations in terms of the electromagnetic potential results in an decoupled system of partial differential equations:

$$ \tag{8.16}
\begin{aligned}
	&\nabla^2 \bm{A}(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	= -\frac{\bm{J}(\bm{r}, t)}{\mu_0^{-1}}, &
	&\bm{B}(\bm{r}, t) = \nabla \times \bm{A}(\bm{r}, t), \cr
	&\nabla^2 \phi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \phi(\bm{r}, t)
	= -\frac{\rho(\bm{r}, t)}{\epsilon_0}, &
	&\bm{E}(\bm{r}, t) = -\frac{\partial}{\partial t} \bm{A}(\bm{r}, t) - \nabla \phi(\bm{r}, t).
\end{aligned}
$$

### Green Functions

Compare Equation 8.16.3 to a single component of Equation 8.16.1 in the Cartesian coordinate system. Both have the same form

$$ \tag{9.1}
	\nabla^2 \psi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \psi(\bm{r}, t)
	= -\xi(\bm{r}, t).
$$

Formally, Equation 9.1 describes the behavior of electromagnetic waves generated by sources in the vacuum of [free space](https://en.wikipedia.org/wiki/Vacuum#Electromagnetism). In this context, \\(\xi\\) is a *source function*, \\(\psi\\) is a *wave function*, and \\(c\\) is the *velocity* of the waves.

Just as before, the problem becomes simpler in the frequency domain:

$$ \tag{9.2}
	\int\_{-\infin}^{\infin} \nabla^2 \psi(\bm{r}, \omega) e^{-i \omega t} d\omega
	- \frac{1}{c^2} \int\_{-\infin}^{\infin} \frac{\partial^2}{\partial t^2} \big( \psi(\bm{r}, \omega) e^{-i \omega t} \big) d\omega
	= \int\_{-\infin}^{\infin} -\xi(\bm{r}, \omega) e^{-i \omega t} d\omega.
$$

Apply the Leibniz rule, take the time derivative, and simplify. The result is

$$ \tag{9.3}
	\int\_{-\infin}^{\infin} \big( \nabla^2 + \omega^2 / c^2 \big) \psi(\bm{r}, \omega) e^{-i \omega t} d\omega
	= \int\_{-\infin}^{\infin} -\xi(\bm{r}, \omega) e^{-i \omega t} d\omega.
$$

Assume that the Fourier transform is uniquely defined. This permits us to remove the integral sign:

$$ \tag{9.4}
	\big( \nabla^2 + \omega^2 / c^2 \big) \psi(\bm{r}, \omega) e^{-i \omega t}
	= -\xi(\bm{r}, \omega) e^{-i \omega t}.
$$

Thus we are lead to consider an *inhomogeneous* [Helmholtz equation](https://en.wikipedia.org/wiki/Helmholtz_equation#Inhomogeneous_Helmholtz_equation)

$$ \tag{9.5}
	\big( \nabla^2 + k_0^2 \big) \psi(\bm{r}) = -\xi(\bm{r}),
$$

where \\(k_0 = \omega / c\\) is the *free-space* wavenumber.

The Helmholtz operator \\(\big( \nabla^2 + k_0^2 \big)\\) is a linear operator \\(\mathcal{H}\\) in 3 dimensions. Therefore, Equation 9.5 represents a linear transformation

$$ \tag{9.6}
	\mathcal{H} \big\lbrace \psi(\bm{r}) \big\rbrace = -\xi(\bm{r}).
$$

Unfortunately, unlike its homogeneous counterpart (Equation 6.11), Equation 9.5 does not have a [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression) solution. Yet, we can still solve it analytically using a neat mathematical trick.

Use the [sifting property](https://en.wikipedia.org/wiki/Dirac_delta_function#Translation) of the Dirac delta function to express the source function

$$ \tag{9.7}
	\xi(\bm{r}) = \int\_{\mathbb{R^3}} \delta(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV'
$$

as a volume integral (a 3-dimensional [convolution](https://en.wikipedia.org/wiki/Convolution)) taken over the whole real space.

While the resulting Helmholtz equation

$$ \tag{9.8}
	\big( \nabla^2 + k_0^2 \big) \psi(\bm{r})
	= \int\_{\mathbb{R^3}} -\delta(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV',
$$

does not appear to be simpler, as it turns out, we can easily solve the Helmholtz equation for a point source

$$ \tag{9.9}
	  \big( \nabla^2 + k_0^2 \big) g_0(\bm{r} - \bm{r'})
	= \mathcal{H} \big\lbrace g_0(\bm{r} - \bm{r'}) \big\rbrace
	= -\delta(\bm{r} - \bm{r'}).
$$

Its solution is the *scalar* [Green function](https://en.wikipedia.org/wiki/Green%27s_function) \\(g\\). It represents an [impulse response](https://en.wikipedia.org/wiki/Impulse_response) of the linear operator \\(\mathcal{H}\\).

Equation 9.9 is an inhomogeneous linear differential equation. Consequently, its solution depends on the linear operator in question, its domain of validity, as well as the associated (initial or boundary) conditions. According to Equation 9.7, our domain is the whole real space, and the only applicable restriction is the Sommerfeld radiation condition. Thus, it can be shown that the *free-space* scalar Green function \\(g_0\\) takes the form[^9] of an diverging *scalar* [spherical wave](https://en.wikipedia.org/wiki/Wave_equation#Spherical_waves) \[[7](#references) (ch. 2.12), [8](#references) (ap. B), [14](#references)\]:

$$ \tag{9.10}
	g_0(\bm{r}) = g(\bm{r}, k_0) = \frac{e^{i k_0 |\bm{r}|}}{4 \pi |\bm{r}|}.
$$

[^9]: Mathematicians often move the minus sign from Equation 9.9 to 9.10. This introduces a significant amount of bookkeeping and is, ultimately, undesirable.

In order to reconcile Equations 9.8 and 9.9, multiply both sides of the latter by \\(\xi(\bm{r'})\\):

$$ \tag{9.11}
	\big( \nabla^2 + k_0^2 \big) g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) = -\delta(\bm{r} - \bm{r'}) \xi(\bm{r'}).
$$

Note that the Laplace operator depends on \\(\bm{r}\\) rather than \\(\bm{r'}\\). Keeping this in mind, take the volume integral

$$ \tag{9.12}
	\big( \nabla^2 + k_0^2 \big) \int\_{\mathbb{R^3}} g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV' = -\xi(\bm{r}),
$$

and compare the result to Equation 9.5. We can see that we have found our solution:

$$ \tag{9.13}
	\psi(\bm{r}) = \int\_{\mathbb{R^3}} g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV'.
$$

Note that, despite the \\(1/|\bm{r} - \bm{r'}|\\) factor in the scalar Green function making the integral [improper](https://en.wikipedia.org/wiki/Improper_integral), it is convergent provided that \\(\xi\\) is a piecewise-continuous function \[[7](#references) (ch. 3.2)\].

Equation 9.13 allows us to find the expressions of the scalar and the vector potentials given by Equations 8.16.1 and 8.16.3 in the frequency domain:

$$ \tag{9.14}
\begin{aligned}
	&\bm{A}(\bm{r}, \omega)
	= \int\_{V} g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'
	= \int\_{V} \frac{e^{i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV', \cr
	&\phi(\bm{r}, \omega)
	= \int\_{V} g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\rho(\bm{r'}, \omega)}{\epsilon_0} dV'
	= \int\_{V} \frac{e^{i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\rho(\bm{r'}, \omega)}{\epsilon_0} dV',
\end{aligned}
$$

where we have replaced \\(\mathbb{R^3}\\) by \\(V\\) since charges tend to occupy a finite region of space.

Note that Equation 9.13 represents a *special* solution of the *inhomogeneous* Helmholtz equation. Since, due to linearity, adding a solution of the *homogeneous* equation does not change the right-hand side, we can obtain the *general* solution of Equation 9.5 by summing Equations 6.15 and 9.13:

$$ \tag{9.15}
\begin{aligned}
	\psi(\bm{r})
	&= \psi_i(\bm{r}) + \psi_s(\bm{r}) \cr
	&= \oint\_{\mathbb{S}^2} \psi_i(0, \bm{n}) e^{i k_0 (\bm{r} \cdot \bm{n})} d\Omega
	 + \int\_{\mathbb{R^3}} \frac{e^{i k_0 |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \xi(\bm{r'}) dV'.
\end{aligned}
$$

Since Equation 9.14 integrates over all currents and charges, all sources of electromagnetic fields are already accounted for, and the homogeneous part of the solution is 0. However, it is often convenient to specify the primary sources separately. In order to do that, we must recall the current and charge decomposition given by Equations 1.9 and 5.4. After substitution, the solution may be alternatively written as

$$ \tag{9.16}
\begin{aligned}
	&\bm{A}(\bm{r}, \omega)
	= \bm{A_i}(\bm{r}, \omega)
	+ \int\_{V} \frac{e^{i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J_b}(\bm{r'}, \omega) + \bm{J_i}(\bm{r'}, \omega)}{\mu_0^{-1}} dV', \cr
	&\phi(\bm{r}, \omega)
	= \phi_i(\bm{r}, \omega)
	+ \int\_{V} \frac{e^{i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\rho_b(\bm{r'}, \omega) + \rho_i(\bm{r'}, \omega)}{\epsilon_0} dV'.
\end{aligned}
$$

These equations have a simple optical interpretation: the first term on the right-hand side can be seen as the *incident* field (generated by primary sources), and the integral corresponds to the *scattered* field (produced by secondary sources).

Let us complete the derivation by finding the expressions of the electric and the magnetic phasors. First, let us establish some context by deriving the differential equation we are planning to solve. We start with Equations 3.10.1 and 3.10.3,

$$ \tag{9.17}
\begin{aligned}
	& \nabla \times \bm{E}(\bm{r}, \omega) - i \omega \bm{B}(\bm{r}, \omega) = 0, \cr
	& \nabla \times \bm{B}(\bm{r}, \omega) = -i \omega \mu\_0 \epsilon_0 \bm{E}(\bm{r}, \omega) + \mu\_0 \bm{J}(\bm{r}, \omega),
\end{aligned}
$$

and substitute \\(\nabla \times \bm{B}\\) from the second equation into the curl of the first one. The result is

$$ \tag{9.18}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - k_0^2(\omega) \bm{E}(\bm{r}, \omega)
	= i \omega \frac{\bm{J}(\bm{r}, \omega)}{\mu\_0^{-1}}, \cr
	& \bm{B}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla \times \bm{E}(\bm{r}, \omega).
\end{aligned}
$$

The corresponding solution in terms of potentials is given by Equation 8.4. In the frequency domain, it takes the following form:

$$ \tag{9.19}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

You may recall that we have used the Lorenz condition to relate the potentials. After performing the Fourier transform, Equation 8.11 becomes

$$ \tag{9.20}
\begin{aligned}
	\nabla \cdot \bm{A}(\bm{r}, \omega) = \frac{i \omega}{c^2} \phi(\bm{r}, \omega).
\end{aligned}
$$

Remarkably, this implies we do not need to consider \\(\phi\\) at all:

$$ \tag{9.21}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = i \omega \bm{A}(\bm{r}, \omega) - \frac{c^2}{i \omega} \nabla \big( \nabla \cdot \bm{A}(\bm{r}, \omega) \big), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

As we group the terms, we encounter a curious operator acting on \\(\bm{A}\\):

$$ \tag{9.22}
	\bm{E}(\bm{r}, \omega)
	= i \omega \Big( \mathcal{I} + \frac{c^2}{\omega^2} \nabla \nabla \cdot \Big) \bm{A}(\bm{r}, \omega),
$$

where \\(\mathcal{I}\\) is the *identity tensor*.

Note that the gradient is a column vector, and the divergence can be visualized as a row vector. In this specific order, they represent an [tensor product](https://en.wikipedia.org/wiki/Tensor_product):

$$ \tag{9.23}
	\bm{E}(\bm{r}, \omega) =
	i \omega \Big( \mathcal{I} + \frac{c^2}{\omega^2} \nabla \otimes \nabla \Big) \bm{A}(\bm{r}, \omega).
$$

Let us now substitute the definition of \\(\bm{A}\\) given by Equation 9.14.1:

$$ \tag{9.24}
	\bm{E}(\bm{r}, \omega)
	= i \omega \Big( \mathcal{I} + \frac{1}{k_0^2(\omega)} \nabla \otimes \nabla \Big) \int\_{V}
	g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

Unfortunately, a complication arises when we try to move the new operator under the integral sign - if \\(\bm{r}\\) is inside \\(V\\), this action introduces a *singularity* (a pole of order 3) at \\(\bm{r} = \bm{r'}\\). We can sidestep this issue by creating a tiny cavity \\(V_{\delta}\\) centered at \\(\bm{r}\\), and separately evaluating the field produced by the piece of matter excised from the cavity. The result is

$$ \tag{9.25}
	\bm{E}(\bm{r}, \omega)
	= i \omega \bigg( \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'
	- \frac{\mathcal{L}(\bm{r})}{k_0^2(\omega)} \frac{\bm{J}(\bm{r}, \omega)}{\mu_0^{-1}} \bigg),
$$

where \\(\mathcal{L}\\) is the dimensionless *depolarization tensor*, the form of which depends on the shape (but not the size) of the cavity \[[7](#references) (ch. 3.9)\], and

$$ \tag{9.26}
	\mathcal{G_{e}}(\bm{r}, \bm{r'}, k)
	= \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) g(\bm{r} - \bm{r'}, k)
	= \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) \frac{e^{i k |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|}
$$

is the *electric tensor* Green function \[[7](#references) (ch. 7.9)\]. As a special case, \\(\mathcal{G\_0}(\bm{r}, \bm{r'}) = \mathcal{G\_{e}}(\bm{r}, \bm{r'}, k\_0)\\) is called the *free-space tensor* Green function[^12].

[^12]: The tensor Green functions are also known as the dyadic Green functions. A [dyadic](https://en.wikipedia.org/wiki/Dyadics) is a second order tensor that uses a special notation that is considered to be relatively obsolete.

Equation 9.25 can be cast in a more compact form by using the [principal value](https://en.wikipedia.org/wiki/Cauchy_principal_value) of the electric tensor. If we define the *total electric tensor* Green function as

$$ \tag{9.27}
	\mathcal{G}(\bm{r}, \bm{r'}, k)
	= \mathcal{P.V.} \big\lbrace \mathcal{G_{e}}(\bm{r}, \bm{r'}, k) \big\rbrace - \frac{\mathcal{L}(\bm{r})}{k^2} \delta(\bm{r} - \bm{r'}),
$$

the expression of the electric field is reduced to

$$ \tag{9.28}
	\bm{E}(\bm{r}, \omega)
	= i \omega \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

To find the integral form of the magnetic field, we must expand Equation 9.21.2 using 9.14.1:

$$ \tag{9.29}
	\bm{B}(\bm{r}, \omega)
	= \nabla \times \int\_{V}
	g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

Using the [curl identity](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_2)

$$ \tag{9.30}
	\nabla \times (g \bm{J}) = g (\nabla \times \bm{J}) + \nabla g \times \bm{J},
$$

and noting that \\(\bm{J}\\) does not depend on \\(\bm{r}\\), we obtain a convergent integral

$$ \tag{9.31}
	\bm{B}(\bm{r}, \omega)
	= \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G_{m}} \big( \bm{r}, \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV',
$$

that features the *magnetic tensor* Green function \[[7](#references) (ch. 7.9)\]

$$ \tag{9.32}
	\mathcal{G_{m}}(\bm{r}, \bm{r'}, k)
	= \nabla g(\bm{r} - \bm{r'}, k) \times \mathcal{I}
	= \nabla \frac{e^{i k |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \times \mathcal{I}
$$

that may be expressed using the [matrix form of the cross product](https://en.wikipedia.org/wiki/Cross_product#Conversion_to_matrix_multiplication). You may also recognize that

$$ \tag{9.33}
	\mathcal{G_{m}}(\bm{r}, \bm{r'}, k)
	= \frac{1}{i \omega} \nabla \times \mathcal{G_{e}}(\bm{r}, \bm{r'}, k).
$$

Therefore, our results are consistent with Equation 3.12.

### Electric and Magnetic Polarization

The physical meaning of the electromagnetic potential is more apparent in the time domain. With this goal in mind, perform the inverse Fourier transform of Equation 9.14.2:

$$ \tag{10.1}
\begin{aligned}
	\phi(\bm{r}, t)
	= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \int\_{V}
		\frac{\rho(\bm{r'}, \omega)}{\epsilon_0} \frac{e^{i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} e^{-i \omega t}
	dV' d\omega.
\end{aligned}
$$

After interchanging the integrals and grouping the terms, we obtain the following expression:

$$ \tag{10.2}
\begin{aligned}
	\phi(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V} \Bigg(
		\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin}
			\frac{\rho(\bm{r'}, \omega)}{|\bm{r} - \bm{r'}|} e^{-i \omega (t - |\bm{r} - \bm{r'}| / c)}
		d\omega
	\Bigg) dV'.
\end{aligned}
$$

Notice that we have identified another inverse Fourier transform - the one that corresponds to an *earlier point in time*. The lag is precisely the amount of time it takes to traverse the distance from the source to the observation point at the speed of light.

For this reason, \\(\bm{A}\\) and \\(\phi\\) are called the [retarded potentials](https://en.wikipedia.org/wiki/Retarded_potential) \[[5](#references) (vol. II, ch. 21), [6](#references) (ch. 2.1)\]:

$$ \tag{10.3}
\begin{aligned}
	&\bm{A}(\bm{r}, t)
	= \frac{1}{4 \pi \mu_0^{-1}} \int\_{V} \frac{\bm{J}(\bm{r'}, t - |\bm{r} - \bm{r'}| / c)}{|\bm{r} - \bm{r'}|} dV', \cr
	&\phi(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V} \frac{\rho(\bm{r'}, t - |\bm{r} - \bm{r'}| / c)}{|\bm{r} - \bm{r'}|} dV'.
\end{aligned}
$$

The simplicity of Equation 10.3 is deceptive. It tells us nothing about what kind of charges there are, or how they behave. It is thus advantageous to return to the macroscopic picture of matter by describing the bound currents and charges in terms of polarization of matter as per Equation 1.9. For simplicity, we assume that the material is non-conducting, so that \\(\bm{J\_f} = \rho\_f = 0\\).

To keep the resulting expressions compact, let us introduce the notion of [retarded time](https://en.wikipedia.org/wiki/Retarded_time)

$$ \tag{10.4}
	t' = t - \frac{|\bm{r} - \bm{r'}|}{c},
$$

which is simply the point in time at which the wave was generated by the source located at \\(\bm{r'}\\).

This definition couples the space and time variables. For instance, consider the following expression:

$$ \tag{10.5}
	\frac{\partial}{\partial x} f \big(\bm{r}, t'(\bm{r}) \big)
	= \frac{\partial f(\bm{r}, t)}{\partial x} \Big\vert_{t=t'}
	+ \frac{\partial f(\bm{r}, t)}{\partial t} \Big\vert_{t=t'} \frac{\partial t'(\bm{r})}{\partial x},
$$

where

$$ \tag{10.6}
	\frac{\partial t'(\bm{r})}{\partial x}
	= -\frac{1}{c} \frac{\partial}{\partial x} \big( (\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'}) \big)^{1/2}
	= -\frac{\bm{r} - \bm{r'}}{c |\bm{r} - \bm{r'}|} \cdot \frac{\partial}{\partial x} (\bm{r} - \bm{r'}).
$$

Since \\(t'\\) depends on \\(\bm{r}\\), this "simple" \\(x\\)-derivative of Equation 10.6 requires application of the [chain rule](https://en.wikipedia.org/wiki/Chain_rule#Multivariable_case).

Equation 10.5 can be written in a more clear way by utilizing the *retarded value* notation:

$$ \tag{10.7}
	\frac{\partial}{\partial x} \Big\lfloor f(\bm{r}, t) \Big\rfloor
	= \Big\lfloor \frac{\partial f(\bm{r}, t)}{\partial x} \Big\rfloor
	+ \Big\lfloor \frac{\partial f(\bm{r}, t)}{\partial t} \Big\rfloor \frac{\partial t'}{\partial x},
$$

where the brackets tell us that we must first evaluate the expression inside, and then substitute \\(t = t'\\).

We can use our new notation to write the expression of the potential of the scattered field:

$$ \tag{10.8}
\begin{aligned}
	&\bm{A_s}(\bm{r}, t)
	= \frac{1}{4 \pi \mu_0^{-1}} \int\_{V} \frac{\lfloor \frac{\partial}{\partial t} \bm{P}(\bm{r'}, t) \rfloor + \lfloor \nabla' \times \bm{M}(\bm{r'}, t) \rfloor}{|\bm{r} - \bm{r'}|} dV', \cr
	&\phi_s(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V} \frac{- \lfloor \nabla' \cdot \bm{P}(\bm{r'}, t) \rfloor}{|\bm{r} - \bm{r'}|} dV',
\end{aligned}
$$

where \\(\nabla'\\) tells us take derivatives with respect to \\(\bm{r'}\\) rather than \\(\bm{r}\\).

In order to be able to write these equation directly in terms of \\(t'\\), we must move the space derivatives outside the brackets. We can achieve this goal by using the chain rule as demonstrated by Equations 10.5-10.6:

$$ \tag{10.9}
\begin{aligned}
	& \nabla' \times \lfloor \bm{M}(\bm{r'}, t) \rfloor
	= \lfloor \nabla' \times \bm{M}(\bm{r'}, t) \rfloor
	+ \frac{\bm{r} - \bm{r'}}{c |\bm{r} - \bm{r'}|} \times \Big\lfloor \frac{\partial}{\partial t} \bm{M}(\bm{r'}, t) \Big\rfloor, \cr
	& \nabla' \cdot \lfloor \bm{P}(\bm{r'}, t) \rfloor
	= \lfloor \nabla' \cdot \bm{P}(\bm{r'}, t) \rfloor
	+ \frac{\bm{r} - \bm{r'}}{c |\bm{r} - \bm{r'}|} \cdot \Big\lfloor \frac{\partial}{\partial t} \bm{P}(\bm{r'}, t) \Big\rfloor.
\end{aligned}
$$

Substitution produces the following expressions:

$$ \tag{10.10}
\begin{aligned}
	&\bm{A_s}(\bm{r}, t)
	= \frac{1}{4 \pi \mu_0^{-1}} \int\_{V} \frac{\lfloor \partial \bm{P} / \partial t \rfloor + \nabla' \times \lfloor \bm{M} \rfloor - \frac{\bm{r} - \bm{r'}}{c |\bm{r} - \bm{r'}|} \times \lfloor \partial \bm{M} / \partial t \rfloor}{|\bm{r} - \bm{r'}|} dV', \cr
	&\phi_s(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V} \frac{- \nabla' \cdot \lfloor \bm{P} \rfloor + \frac{\bm{r} - \bm{r'}}{c |\bm{r} - \bm{r'}|} \cdot \lfloor \partial \bm{P} / \partial t \rfloor}{|\bm{r} - \bm{r'}|} dV',
\end{aligned}
$$

where we omitted the \\(\bm{r'}\\) and \\(t\\) parameters for the sake of brevity.

Furthermore, we can use the [product rule](https://en.wikipedia.org/wiki/Vector_calculus_identities#Product_rule_for_multiplication_by_a_scalar) to show that

$$ \tag{10.11}
\begin{aligned}
	& \nabla' \cdot \frac{\bm{P}}{|\bm{r} - \bm{r'}|}
	= \frac{1}{|\bm{r} - \bm{r'}|} \nabla' \cdot \bm{P}
	+ \bm{P} \cdot \nabla' \frac{1}{|\bm{r} - \bm{r'}|} \cr
	& \nabla' \times \frac{\bm{M}}{|\bm{r} - \bm{r'}|}
	= \frac{1}{|\bm{r} - \bm{r'}|} \nabla' \times \bm{M}
	- \bm{M} \times \nabla' \frac{1}{|\bm{r} - \bm{r'}|}.
\end{aligned}
$$

Application of the [divergence theorem](https://en.wikipedia.org/wiki/Divergence_theorem) yields

$$ \tag{10.12}
\begin{aligned}
	& \int\_{V} \nabla' \cdot \frac{\bm{P}}{|\bm{r} - \bm{r'}|} dV'
	= \oint\_{\partial V}  \frac{\bm{n} \cdot \bm{P}}{|\bm{r} - \bm{r'}|} dA', \cr
	& \int\_{V} \nabla' \times \frac{\bm{M}}{|\bm{r} - \bm{r'}|} dV'
	= \oint\_{\partial V} \frac{\bm{n} \times \bm{M}}{|\bm{r} - \bm{r'}|} dA'. \cr
\end{aligned}
$$

Assuming that the region containing charges is finite, we can increase the the size of the bounding volume without affecting the value of the integrals on the left-hand side. This will cause the integrals (on the right-hand side) taken over the extended bounding surface to vanish. Consequently, the volume integrals of Equations 10.11.1-10.11.2 take the following form:

$$ \tag{10.13}
\begin{aligned}
	& \int\_{V} \frac{-1}{|\bm{r} - \bm{r'}|} \nabla' \cdot \bm{P} dV'
	= \int\_{V} \bm{P} \cdot \nabla' \frac{1}{|\bm{r} - \bm{r'}|} dV', \cr
	& \int\_{V} \frac{1}{|\bm{r} - \bm{r'}|} \nabla' \times \bm{M} dV'
	= \int\_{V} \bm{M} \times \nabla' \frac{1}{|\bm{r} - \bm{r'}|} dV'.
\end{aligned}
$$

Let us combine Equations 10.10 and 10.13. Using the shorthand notation \\(\bm{R} = \bm{r} - \bm{r'}, R = |\bm{R}|\\), the result is

$$ \tag{10.14}
\begin{aligned}
	&\bm{A_s}(\bm{r}, t)
	= \frac{1}{4 \pi \mu_0^{-1}} \int\_{V}
		  \lfloor \bm{M} \rfloor \times \nabla' \frac{1}{R}
		+ \frac{\lfloor \partial \bm{P} / \partial t \rfloor}{R}
		- \frac{\bm{R} \times \lfloor \partial \bm{M} / \partial t \rfloor}{c R^2} dV', \cr
	&\phi_s(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V}
		  \lfloor\bm{P} \rfloor \cdot \nabla' \frac{1}{R}
		+ \frac{\bm{R} \cdot \lfloor \partial \bm{P} / \partial t \rfloor}{c R^2} dV'.
\end{aligned}
$$

Equations 10.14.1-10.14.2 have the following interpretation in terms of the atomic theory of matter \[[6](#references) (ch. 2.2)\]. The [nucleus](https://en.wikipedia.org/wiki/Atomic_nucleus) of an atom contains [protons](https://en.wikipedia.org/wiki/Proton) with the charge \\(+q_e\\), and is surrounded by one or several [electrons](https://en.wikipedia.org/wiki/Electron) with the charge \\(-q_e\\). A molecule is an electrically neutral group of several atoms. Thus, an atom or a molecule is a system of several charges, half being negative, and the other half - positive, separated (on average) by a certain distance \\(d\\). If this distance is not zero, it is said that the the molecule is polarized. We can use this property to model an atom or a small molecule as a [dipole](https://en.wikipedia.org/wiki/Dipole) with an associated [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment)

$$ \tag{10.15}
	\bm{p} = q \bm{d}.
$$

Notice that the moment of the dipole does not depend on its location, \\(\bm{r_0}\\). That is because it describes the displacement from the mean, which is relative by definition. It is easy to show that by expressing the electric dipole moment in terms of the bound charge density:

$$ \tag{10.16}
	\bm{p}(V, t)
	= \int\_{V} \rho_b(\bm{r}, t) (\bm{r} - \bm{r_0}) dV
	= - Q_b(V, t) \bm{r_0} + \int\_{V} \rho_b(\bm{r}, t) \bm{r} dV ,
$$

where \\(Q_b\\) is the total bound charge (also called the [electric monopole moment](https://en.wikipedia.org/wiki/Multipole_expansion)):

$$ \tag{10.17}
	Q_b(V, t) = \int\_{V} \rho_b(\bm{r}, t) dV,
$$

which is zero for electrically neutral objects.

On the other hand, the electric dipole moment per unit volume \\(\bm{P}\\) is position-dependent:

$$ \tag{10.18}
	\bm{p}(V, t) = \int\_{V} \bm{P}(\bm{r}, t) dV,
	\qquad
	\bm{p}(V, \omega) = \int\_{V} \bm{P}(\bm{r}, \omega) dV.
$$

This expression is useful, since we can directly relate it to the definition of the polarization current density \\(\bm{J_p}\\) given by Equation 1.9.1:

$$ \tag{10.19}
	\frac{\partial}{\partial t} \bm{p}(V, t) = \int\_{V} \bm{J_p}(\bm{r}, t) dV,
	\qquad
	-i \omega \bm{p}(V, \omega) = \int\_{V} \bm{J_p}(\bm{r}, \omega) dV.
$$

Equation 10.19 implies that we can represent an arbitrary polarization current density by oscillating electric dipole moments:

$$ \tag{10.20}
	\bm{J_p}(\bm{r}, t) = \frac{\partial}{\partial t} \sum_n \bm{p_n}(V_n, t) \delta(\bm{r} - \bm{r_n}),
	\qquad
	\bm{J_p}(\bm{r}, \omega) = -i \omega \sum_n \bm{p_n}(V_n, \omega) \delta(\bm{r} - \bm{r_n}).
$$

A molecule can become polarized for a variety of reasons \[[5](#references) (vol. II, ch. 11)\]. If the separation of charges (such as the displacement of the electron cloud relative to the nucleus) occurs due the influence of an electric field, one speaks of *induced polarization*. If we assume that the effect is linear, the response is characterized by the [molecular polarizability](https://en.wikipedia.org/wiki/Electric_susceptibility#Molecular_polarizability) tensor \\(\mathcal{\Alpha_m}\\):

$$ \tag{10.21}
	\bm{p}(V, \omega) \approx \mathcal{\Alpha_m}(V, \omega) \epsilon_0 \bm{E_{\mu}}(\bm{r_0}, \omega).
$$

If there are \\(N\\) identical electric dipoles per unit volume, we can define the electric polarization \\(\bm{P}\\) as

$$ \tag{10.22}
	\bm{P}(\bm{r}, \omega)
	= N(\bm{r}) \bm{p}(V, \omega)
	\approx N(\bm{r}) \mathcal{\Alpha_m}(V, \omega) \epsilon_0 \bm{E_{\mu}}(\bm{r_0}, \omega).
$$

Note that, in general, the microscopic field \\(\bm{E_{\mu}}\\) acting on the dipole is different from the macroscopic field \\(\bm{E}\\). The reason is that the microscopic field varies very rapidly inside the matter - it is very strong next to the nucleus, and relatively weak in the gaps between the molecules. Thus, the density of matter plays an important role. If the dipoles are randomly distributed, we can assume that the dipole under consideration is located within a spherical cavity of a uniformly polarized material. For an isotropic material, it can be shown that the two fields are related by the equation

$$ \tag{10.23}
	\epsilon_0 \bm{E_{\mu}}(\bm{r_0}, \omega) = \epsilon_0 \bm{E}(\bm{r}, \omega) + \frac{1}{3} \bm{P}(\bm{r}, \omega),
$$

where \\(\bm{P}\\) describes the electric polarization of matter surrounding the dipole \[[5](#references) (vol. II, ch. 11), [6](#references) (ch. 2.3-2.4)\].

By combining Equations 10.22 and 10.23, and setting \\(\mathcal{\Alpha_m} = \alpha_m\\),

$$ \tag{10.24}
	\bm{P}(\bm{r}, \omega)
	\approx \frac{N(\bm{r})\alpha_m(V, \omega)}{1 - \frac{1}{3} N(\bm{r})\alpha_m(V, \omega)} \epsilon_0 \bm{E}(\bm{r}, \omega),
$$

we can incorporate the correction for the *microscopic* field into the *macroscopic* theory.

In order to be able to use Equation 10.24, we need to determine both the density and the polarizability of the material. For an isotropic dielectric composed of a single type of small (compared to the wavelength) molecules, their product is given by the [Clausius–Mossotti relation](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation):

$$ \tag{10.25}
	\frac{\epsilon\_r(\bm{r}, \omega) - 1}{\epsilon\_r(\bm{r}, \omega) + 2} = \frac{1}{3} N(\bm{r}) \alpha\_m(V, \omega).
$$

Thus, Equation 10.24 can be expressed very simply in terms of the [relative permittivity](https://en.wikipedia.org/wiki/Relative_permittivity) (dielectric constant) \\(\epsilon_r\\):

$$ \tag{10.26}
	\bm{P}(\bm{r}, \omega)
	= \bm{D}(\bm{r}, \omega) - \epsilon_0 \bm{E}(\bm{r}, \omega)
	\approx \big( \epsilon_r(\bm{r}, \omega) - 1 \big) \epsilon_0 \bm{E}(\bm{r}, \omega),
$$

with the "exact" Equation 1.13.1 shown for comparison.

While the electric dipole moment \\(\bm{p}\\) is produced by oscillating atomic charges, the [magnetic dipole moment](https://en.wikipedia.org/wiki/Magnetic_moment#Amperian_loop_model) \\(\bm{m}\\) is generated by circulating atomic currents (charges going in circles):

$$ \tag{10.27}
	\bm{m} = I \bm{s},
$$

where \\(I\\) is the current, and \\(\bm{s}\\) is the oriented area of the loop.

Formally, the magnetic dipole moment can be expressed in terms of the magnetization current by the integral

$$ \tag{10.28}
	\bm{m}(t)
	= \frac{1}{2} \int\_{V} \bm{r} \times \bm{J_m}(\bm{r}, t) dV,
$$

or, in terms of the magnetic polarization per unit volume \\(\bm{M}\\),

$$ \tag{10.29}
	\bm{m}(t)
	= \int\_{V} \bm{M}(\bm{r}, t) dV.
$$

Like in the case of polarization, magnetism comes in different forms \[[5](#references) (vol. II, ch. 34)\]. Unfortunately, none of them appear to have a satisfactory explanation in terms of classical physics. In particular, to the best of the author's knowledge, there is no simple analog of Equation 10.21 for induced magnetization.

For weak fields in [diamagnets](https://en.wikipedia.org/wiki/Diamagnet) and [paramagnets](https://en.wikipedia.org/wiki/Paramagnet), it is possible to combine Equations 1.13.2 and 5.2.3 to express the magnetic polarization \\(\bm{M}\\) in terms of the relative permeability \\(\mu\_r\\),

$$ \tag{10.30}
	\bm{M}(\bm{r}, \omega)
	= \mu\_0^{-1} \bm{B}(\bm{r}, \omega) - \bm{H}(\bm{r}, \omega)
	\approx \big( 1 - \mu_r^{-1}(\bm{r}, \omega) \big) \mu\_0^{-1} \bm{B}(\bm{r}, \omega),
$$

but for [ferromagnets](https://en.wikipedia.org/wiki/Ferromagnet), this expression is invalid due to the [magnetic hysteresis](https://en.wikipedia.org/wiki/Magnetic_hysteresis) effect \[[5](#references) (vol. II, ch. 36)\].

Fortunately, for optical applications, the situation is rather simple. Paramagnetism and diamagnetism are both very weak in general; the same is true for ferromagnetism at optical frequencies in particular. This leads to \\(\mu\_r \approx 1\\) \[[10](#references) (vol. I, ch. 27)\].

In some sense, dipoles can be considered elementary field generators: an electric dipole produces the \\(\bm{E}\\) field, and a magnetic dipole is a source of the \\(\bm{B}\\) field. They can be used to describe any (bound) current and charge distributions, which makes them particularly useful for solving electromagnetic radiation and scattering problems.

[Insert pictures of field lines of electric and magnetic dipoles]

Finally, we would like to emphasize certain properties of the solution of the Maxwell equations in terms of potentials. Perhaps the most obvious one is that the scattered field is expressed as a volume integral. While the geometry of the volume containing the charges plays an important role, it is now apparent that the interior of the volume is the actual source of what we consider the surface reflection \[[5](#references) (vol. I, ch. 31)\]. Furthermore, Equation 10.3 says that the microscopic electromagnetic field always propagates at the speed of light. The dipole field interferes with the incident field in such a way that the total macroscopic field appears to have a lower phase velocity, which is used as the definition of the refractive index. This mechanism is described by the [Ewald-Oseen extinction theorem](https://en.wikipedia.org/wiki/Ewald%E2%80%93Oseen_extinction_theorem) \[[6](#references) (ch. 2.4)\].

## II. Electromagnetic Scattering

### From Maxwell Equations to Radiative Transfer

In practice, it is very difficult to solve the Maxwell equations exactly, except under certain (idealized) conditions. Thus, one usually employs various approximations that are carefully chosen to minimize both the error and the complexity of the computation.

One of the most important considerations is the scale of the observation. Evidently, even something as large and complex as a star can be approximated by a point source if the observer is sufficiently far away. A similar line of reasoning justifies modeling an electron as a point charge at the microscopic scale.

Stepping farther away, we can represent the average atom (or a molecule) as a dipole aligned with the driving field, thereby greatly restricting the expected behavior of microscopic charges and currents. In this approximation, constructive interference reinforces the fields produced by the charges moving in sync, while the effects of motion that differs from the average are suppressed by both destructive interference and the lower probability of occurrence.

Mathematically, the field of a (macroscopic) piece of matter is the (vector) sum of the fields of its (microscopic) parts. Yet, at the macroscopic scale, the field of a single dipole often cannot be distinguished at all! The field of a tremendous number of identical dipoles acting together manifests itself by bending light rays (according to the refractive index) and reducing their intensity (proportionally to the attenuation index). In order for light scattering effects to appear, the medium must be inhomogeneous. Macroscopically, such a medium features spatially-varying *optical properties*; microscopically, this implies that the underlying distribution of dipoles is not the same throughout the medium. Clearly, a homogeneous medium is an idealization, yet it can serve as an effective approximation for tiny patches of uniformly dense matter (composed of scatterers smaller than the wavelength of light).

[Insert picture here?]

The macroscopic distribution of optical properties can (at least in theory) be completely arbitrary. In order to arrive at a concrete (and practical) solution, we shall consider the special case of sparse inhomogeneous particles embedded in a homogeneous medium. Notable examples of such *composite media* include [gases](https://en.wikipedia.org/wiki/Gas) and [colloids](https://en.wikipedia.org/wiki/Colloid).

[Insert picture here]

Given the source illuminating the medium, we can determine the *total field* that arises after propagation and scattering of electromagnetic waves. Clearly, if there are no particles, the medium is homogeneous everywhere, and the magnitude of the field at the destination is the same as its magnitude at the source (before accounting for attenuation with distance). We shall refer to it as the *incident field*. The (mathematical) difference between the total field (with particles) and the incident field (without particles) constitutes the *scattered field*. The task of determining the scattered field is at the core of the *scattering problem*.

[Insert picture here]

In order to rigorously solve the scattering problem, we must specify not only the incident field, but also the composite medium *in its entirety*, since, according to the Maxwell equations, the value of the electromagnetic field at a certain point is the sum of the fields produced by all charges, everywhere. Clearly, this is highly inconvenient and, often, unnecessary. Thus, we make a compromise: we "chop" the composite medium into little pieces (that effectively shrink to a point), each filled with small particles, analogously to a region of the macroscopic medium formed by a distribution of microscopic dipoles. Extending the analogy further, just as the combined action of dipoles leads to the definition of optical properties,
the *radiative properties* of the large-scale medium are determined by solving the Maxwell equations for the distribution of particles within each little piece. Finally, we use the *radiative transfer equation* to integrate the contribution of all little pieces, which is a process that is conceptually similar to combining the fields of all charges according to the Maxwell equations.

### Scattering Problem

Participating media can be broadly divided into two categories - homogeneous and inhomogeneous. This suggests that we may split any medium into two regions: 1) infinite homogeneous, and 2) the remaining space of finite volume \\(V\\). The latter can be interpreted as a single particle or a particle group, or, more generally, as a scattering object (or a *scatterer* for short).

[Picture?]

According to the Maxwell equations in the frequency domain (Equation 3.9.1), the following relation holds in both regions:

$$ \tag{11.1}
	\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \bm{B}(\bm{r}, \omega) = 0.
$$

On the other hand, the second Maxwell equation (3.9.3) depends on the region under consideration:

$$ \tag{11.2}
	\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \bm{D}(\bm{r}, \omega) = \bm{J_f}(\bm{r}, \omega).
$$

Assuming that the media are linear, isotropic, and source-free, Equation 11.2 can be replaced by Equation 6.1.3:

$$ \tag{11.3}
\begin{aligned}
	& \nabla \times \big( \mu_1^{-1}(\omega) \bm{B}(\bm{r_1}, \omega) \big)
	= -i \omega \varepsilon_1(\omega)  \bm{E}(\bm{r_1}, \omega), \cr
	& \nabla \times \big( \mu_2^{-1}(\bm{r_2}, \omega) \bm{B}(\bm{r_2}, \omega) \big)
	= -i \omega \varepsilon_2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega),
\end{aligned}
$$

where the first equation only holds in the (homogeneous) region 1, and the second - in the (inhomogeneous) region 2.

Further, assume that the region 1 is non-magnetic, and that the magnetic permeability is constant within the region 2. This implies that

$$ \tag{11.4}
\begin{aligned}
	& \nabla \times \bm{B}(\bm{r_1}, \omega)
	= -i \omega \mu_0 \varepsilon_1(\omega) \bm{E}(\bm{r_1}, \omega), \cr
	& \nabla \times \bm{B}(\bm{r_2}, \omega)
	= -i \omega \mu_2(\omega) \varepsilon_2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega),
\end{aligned}
$$

These equations are ready for substitution into the curl of Equation 11.1. The result is

$$ \tag{11.5}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r_1}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_1}, \omega) = 0, \cr
	& \nabla \times \nabla \times \bm{E}(\bm{r_2}, \omega) - k_2^2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega) = 0,
\end{aligned}
$$

with the constants grouped according to the definition of the complex wavenumber \\(k\\) given by Equation 7.4.

To make these two equations more alike, we can subtract \\(k_1^2 \bm{E}\\) from both sides of Equation 11.5.2:

$$ \tag{11.6}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r_1}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_1}, \omega) = 0, \cr
	& \nabla \times \nabla \times \bm{E}(\bm{r_2}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_2}, \omega)
	= \big( k_2^2(\bm{r_2}, \omega) - k_1^2(\omega) \big) \bm{E}(\bm{r_2}, \omega).
\end{aligned}
$$

We are now in a good position to state a single equation that unifies the two region. By introducing the term

$$ \tag{11.7}
\bm{J'}(\bm{r}, \omega) =
\begin{cases}
   k_1^2(\omega) \big( m^2(\bm{r}, \omega) - 1 \big) \bm{E}(\bm{r}, \omega) &\text{if } \bm{r} \in V, \cr
   0 &\text{otherwise},
\end{cases}
$$

such that the *relative refractive index* is

$$ \tag{11.8}
	m(\bm{r}, \omega)
	= \frac{k_2(\bm{r}, \omega)}{k_1(\omega)}
	= \sqrt{ \frac{\varepsilon_2(\bm{r}, \omega) \mu_2(\omega)}{\varepsilon_1(\omega) \mu_0} },
$$

we can replace Equations 11.6.1 and 11.6.2 by a single equation

$$ \tag{11.9}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - k_1^2(\omega) \bm{E}(\bm{r}, \omega)
	= \bm{J'}(\bm{r}, \omega).
$$

We have already encountered a mathematically identical problem shown in Equation 9.18.1. After matching the constants \\( \big( \bm{J'} = i \omega \bm{J} / \mu_0^{-1} \big) \\), the solution is readily given by Equation 9.25:

$$ \tag{11.10}
	\bm{E_s}(\bm{r}, \omega)
	= \int\_{V} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \bm{J'}(\bm{r'}, \omega) dV'
	\qquad
	\text{if } \bm{r} \notin V,
$$

where have neglected the singularity term by assuming that \\(\bm{r}\\) is located in the homogeneous region.

Similarly, the magnetic field is given by Equation 9.31:

$$ \tag{11.11}
	\bm{B_s}(\bm{r}, \omega)
	= \frac{1}{i \omega} \int\_{V} \mathcal{G_{m}} \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \bm{J'}(\bm{r'}, \omega) dV'
	\qquad
	\text{if } \bm{r} \notin V.
$$

Equation 11.10 is a special solution of the inhomogeneous Equation 11.9 (just like Equation 9.13 is a special solution of Equation 9.5). To obtain a general solution, we must combine Equation 11.10 with the solution of the homogeneous equation, which physically amounts to adding the primary sources. In our particular case, the homogeneous equation is mathematically equivalent to Equation 6.5, which allows us to directly use the solution given by Equation 6.16:

$$ \tag{11.12}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \bm{E_i}(\bm{r}, \omega) + \bm{E_s}(\bm{r}, \omega) \cr
	&= \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ \int\_{V} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \bm{J'}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In addition to the mathematical approach presented above, we can arrive at the same result in a more "physical" way.

Consider an object embedded in vacuum. The solution in terms of potentials is given by Equation 9.16:

$$ \tag{11.13}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i k_0(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ \int\_{V} \frac{e^{i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J_b}(\bm{r'}, \omega) + \bm{J_i}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
\end{aligned}
$$

The expression of the bound current is given by Equation 1.10.1. In the frequency domain, the corresponding equation is

$$ \tag{11.14}
	\bm{J\_b}(\bm{r}, \omega)
	= \bm{J\_p}(\bm{r}, \omega) + \bm{J\_m}(\bm{r}, \omega)
	= -i \omega \bm{P}(\bm{r}, \omega) + \nabla \times \bm{M}(\bm{r}, \omega).
$$

Using Equation 10.26 that relates polarization to the macroscopic optical properties, we may write

$$ \tag{11.15}
	\bm{J\_p}(\bm{r}, \omega) = -i \omega \big( \epsilon_2(\bm{r}, \omega) - \epsilon_0 \big)  \bm{E}(\bm{r}, \omega).
$$

Similarly, for magnetization, we may adopt Equation 10.30:

$$ \tag{11.16}
\begin{aligned}
	\bm{J\_m}(\bm{r}, \omega)
	&= \mu\_0^{-1} \nabla \times \bm{B}(\bm{r}, \omega) - \nabla \times \bm{H}(\bm{r}, \omega) \cr
	&= \big( \mu\_2(\omega) / \mu\_0 - 1 \big) \nabla \times \bm{H}(\bm{r}, \omega) \cr
	&= \big( \mu\_2(\omega) / \mu\_0 - 1 \big) \big( \sigma_2(\bm{r}, \omega) - i \omega \epsilon_2(\bm{r}, \omega) \big) \bm{E}(\bm{r}, \omega),
\end{aligned}
$$

where we additionally utilized Equation 5.2.3 and 5.5.3 to expand the expression of the curl.

If the object is conductive, Equation 5.2.1 says that

$$ \tag{11.17}
	\bm{J\_i}(\bm{r}, \omega) = \sigma_2(\bm{r}, \omega) \bm{E}(\bm{r}, \omega).
$$

Let us now introduce

$$ \tag{11.18}
	\bm{J'}(\bm{r}, \omega)
	= i \omega \frac{\bm{J_b}(\bm{r}, \omega) + \bm{J_i}(\bm{r}, \omega)}{\mu_0^{-1}}
	= \omega^2 \big( \varepsilon_2(\bm{r}, \omega) \mu_2(\omega) - \epsilon_0 \mu_0 \big) \bm{E}(\bm{r}, \omega).
$$

We may write it in terms of wave velocities using Equations 1.4, 7.4, and 7.9:

$$ \tag{11.19}
	\bm{J'}(\bm{r}, \omega)
	= \omega^2 \Bigg( \bigg( \frac{1}{v_p(\bm{r}, \omega)} + \frac{i}{v_a(\bm{r}, \omega)} \bigg)^2 - \frac{1}{c^2} \Bigg) \bm{E}(\bm{r}, \omega).
$$

Substitution into Equation 11.13 yields

$$ \tag{11.20}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i \omega (\bm{r} \cdot \bm{n}) / c} d\Omega \cr
	&- i \omega \int\_{V} \frac{e^{i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} \Bigg( \bigg( \frac{1}{v_p(\bm{r'}, \omega)} + \frac{i}{v_a(\bm{r'}, \omega)} \bigg)^2 - \frac{1}{c^2} \Bigg) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

If the volume integral vanishes, we are left with the incident wave propagating at the speed of light \\(c\\). If we wish to replace vacuum with another homogeneous medium, we must reduce the wave velocities in the exterior region by a factor of \\((\eta_1 + i \kappa_1)^{-1}\\). Specifically, in our case, this means we must substitute \\(\varepsilon_1\\) for \\(\epsilon_0\\) and, as a consequence, \\((\varepsilon_1 \mu_0)^{-1/2}\\) for \\(c\\). These modifications do not affect the wave velocities in the interior region. The result is

$$ \tag{11.21}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i \omega \sqrt{\varepsilon_1(\omega) \mu\_0} (\bm{r} \cdot \bm{n})} d\Omega \cr
	&- i \omega \int\_{V} \frac{e^{i \omega \sqrt{\varepsilon_1(\omega) \mu\_0} |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( \varepsilon_2(\bm{r'}, \omega) \mu\_2(\omega) - \varepsilon_1(\omega) \mu\_0 \big) \bm{E}(\bm{r'}, \omega) dV',
\end{aligned}
$$

or, using the wavenumber notation,

$$ \tag{11.22}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ \frac{1}{i \omega} \int\_{V} \frac{e^{i k_1(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( k_2^2(\bm{r'}, \omega) - k_1^2(\omega) \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

Since the definition of the Lorenz condition is based on the Maxwell equations in vacuum, it must also be modified. Replacing \\(c\\) in Equations 8.11 and 9.20 leads us to the modified version of Equation 9.23:

$$ \tag{11.23}
	\bm{E}(\bm{r}, \omega) =
	i \omega \Big( \mathcal{I} + \frac{1}{k_1^2(\omega)} \nabla \otimes \nabla \Big) \bm{A}(\bm{r}, \omega).
$$

After performing the substitution of Equation 11.22, recalling the definition of \\(m\\) given by Equation 11.8, and assuming that \\(\bm{r}\\) lies outside the inhomogeneous region, we obtain an expanded version of Equation 11.12:

$$ \tag{11.24}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ k_1^2(\omega) \int\_{V} \Big( \mathcal{I} + \frac{1}{k_1^2(\omega)} \nabla \otimes \nabla \Big) \frac{e^{i k_1(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In the future, when no ambiguity arises, we may drop redundant indexing by writing \\(k = k_1\\) and \\(\varepsilon = \varepsilon_1\\):

$$ \tag{11.25}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	= k^2(\omega) \int\_{V} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k(\omega) \big) \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega)
	= \frac{k^2(\omega)}{i \omega} \int\_{V} \mathcal{G_{m}} \big( \bm{r}, \bm{r'}, k(\omega) \big) \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In the literature, Equation 11.25 is often called the *volume integral equation* \[[8](#references) (ch. 4)\]

### Dipole Radiation

Typically, it is not possible to evaluate the integrals of Equations 11.25 in closed form, since the value of the electric field in the interior of the volume is not known. Thus, we must make certain assumptions and employ various approximations in order to make computations feasible. This leads to a number of special cases.

One of the simplest problems that can be solved using our mathematical framework (of classical physics) is that of a single atom or a small molecule embedded in a homogeneous medium. This case corresponds to a tiny particle in vacuum, or an [impurity](https://en.wikipedia.org/wiki/Impurity) in an otherwise pure material. We represent it as an electric dipole - an oriented point source.

The necessary and sufficient condition is that the particle of radius \\(a\\) must be small compared to the wavelength \\(\lambda\\) computed both inside and outside the particle \[[4](#references) (ch. 6.4)\]. Since \\(k = 2 \pi / \lambda\\), we require that

$$
	ka \ll 1, \quad |m|ka \ll 1.
$$

This requirement leads to three simplifications:

1. we may neglect the effect of the fields generated by the particle on itself;

2. the magnitude, the orientation, and the phase of the field driving the charges (equal to the incident field) is the same throughout the particle;

3. the resulting source of radiation is sufficiently localized, which permits us to evaluate the fields at practically any distance from the particle.

Assume that the particle is non-magnetic and non-conducting. Equation 11.18 can then be simplified to

$$ \tag{12.1}
	\bm{J'}(\bm{r}, \omega)
	= i \omega \frac{\bm{J_p}(\bm{r}, \omega)}{\mu_0^{-1}}.
$$

According to Equation 10.20, polarization current may be attributed to an oscillating dipole:

$$ \tag{12.2}
	\bm{J_p}(\bm{r}, \omega) = -i \omega \bm{p}(V, \omega) \delta(\bm{r} - \bm{r_0}),
$$

with the dipole moment defined by Equation 10.21:

$$ \tag{12.3}
	\bm{p}(V, \omega) \approx \mathcal{\Alpha_m}(V, \omega) \varepsilon(\omega) \bm{E_i}(\bm{r_0}, \omega).
$$

where we replaced \\(\epsilon_0\\) with \\(\varepsilon\\) (to account for the properties of the surrounding medium) and \\(\bm{E_{\mu}}\\) with \\(\bm{E_i}\\) (according to the simplification discussed above).

The combination of Equations 12.1-12.3 results in

$$ \tag{12.4}
	\bm{J'}(\bm{r}, \omega)
	= k^2(\omega) \mathcal{\Alpha_m}(V, \omega) \bm{E_i}(\bm{r_0}, \omega) \delta(\bm{r} - \bm{r_0}).
$$

The tensor form of \\(\mathcal{\Alpha_m}\\) is a convenient way to model polar molecules (or asymmetric particles); it acts by rotating and non-uniformly scaling the electric field phasor. To make the resulting formulas easier to interpret, we shall assume that the particle is isotropic, so that \\(\mathcal{\Alpha_m} = \alpha_m\\).

Let us substitute Equation 12.4 into Equations 11.10-11.11:

$$ \tag{12.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	= k^2(\omega) \mathcal{G_{e}} \big( \bm{r}, \bm{r_0}, k(\omega) \big) \alpha_m(V, \omega) \bm{E_i}(\bm{r_0}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	= \frac{k^2(\omega)}{i \omega} \mathcal{G_{m}} \big( \bm{r}, \bm{r_0}, k(\omega) \big) \alpha_m(V, \omega) \bm{E_i}(\bm{r_0}, \omega).
\end{aligned}
$$

Comparison with Equation 11.25 shows that Equation 12.5 corresponds to the contribution of a volume element occupied by a single atom or a small molecule, with the internal field replaced by the incident field, and with the polarizability \\(\alpha_m\\) taking the role of the relative refractive index \\(m\\).

Note that the polarizability \\(\alpha_m\\) has units of volume \[[4](#references) (ch. 6.22)\]:

$$ \tag{12.6}
	\alpha_m(V, \omega)
	= \int_V \big( m^2(\bm{r}, \omega) - 1 \big) dV.
$$

This can be shown by combining Equations 10.18.2 and 10.26

$$ \tag{12.7}
	\bm{p}(V, \omega)
	= \int_V \bm{P}(\bm{r}, \omega) dV
	= \int_V \big( m^2(\bm{r}, \omega) - 1 \big) \varepsilon(\omega) \bm{E}(\bm{r}, \omega) dV
$$

and comparing the result with Equation 12.3.

In order to compute the scattered fields according to Equation 12.5, we need to evaluate the tensor Green functions. Let us first consider the electric tensor defined by Equation 9.26. Its matrix representation in the Cartesian coordinate system is

$$ \tag{12.8}
\begin{aligned}
	\mathcal{G_{e}}(\bm{r}, \bm{r'}, k)
	= \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) g(\bm{r} - \bm{r'}, k)
	= \mathcal{I} g + \frac{1}{k^2}
	\begin{bmatrix}
		\frac{\partial^2 g}{\partial x^2} & \frac{\partial^2 g}{\partial x \partial y} & \frac{\partial^2 g}{\partial x \partial z} \cr
		\frac{\partial^2 g}{\partial y \partial x} & \frac{\partial^2 g}{\partial y^2} & \frac{\partial^2 g}{\partial y \partial z} \cr
		\frac{\partial^2 g}{\partial z \partial x} & \frac{\partial^2 g}{\partial z \partial y} & \frac{\partial^2 g}{\partial z^2} \cr
	\end{bmatrix},
\end{aligned}
$$

where the scalar Green function \\(g\\) is given by Equation 9.10.

Ignoring the multiplicative constant \\(1/(4 \pi k^2)\\), the matrix elements have the form

$$ \tag{12.9}
	\frac{\partial^2}{\partial \\_ \partial \\_ } \Bigg( \frac{e^{i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg),
$$

where we may substitute \\(x\\), \\(y\\), or \\(z\\) into the blanks.

Under the previous assumption that \\(\bm{r} \neq \bm{r'}\\), the scalar Green function and its partial derivatives are continuous, which means the order of partial differentiation makes no difference. Consequently, the matrix of Equation 12.8 is [symmetric](https://en.wikipedia.org/wiki/Symmetric_matrix), such that \\(\mathcal{G_{e}} = \mathcal{G_{e}}^T\\).

The process of differentiation is straightforward: we use the identity

$$ \tag{12.10}
	\frac{\partial}{\partial x} |\bm{r} - \bm{r'}|^{n}
	= \frac{\partial}{\partial x} \big( (\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'}) \big)^{n/2}
	= n \frac{\bm{r} - \bm{r'}}{|\bm{r} - \bm{r'}|^{2-n}} \cdot \frac{\partial}{\partial x} (\bm{r} - \bm{r'})
$$

and repeatedly apply the [product rule](https://en.wikipedia.org/wiki/Product_rule). Skipping the details of the calculation, and using the the shorthand notation \\(\bm{R} = \bm{r} - \bm{r'}, R = |\bm{R}|\\), a typical first derivative is

$$ \tag{12.11}
\begin{aligned}
	\frac{\partial}{\partial x} \Bigg( \frac{e^{i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg)
	= -e^{i k R} \frac{R_x}{R}
		\bigg(
			  \frac{1}{R^2}
			- \frac{i k}{R}
		\bigg),
\end{aligned}
$$

the off-diagonal elements of the matrix have the form

$$ \tag{12.12}
\begin{aligned}
	\frac{\partial^2}{\partial y \partial x} \Bigg( \frac{e^{i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg)
	= e^{i k R} \frac{R_x R_y}{R^2}
		\bigg(
			  \frac{3}{R^3}
			- \frac{3 i k}{R^2}
			- \frac{k^2}{R}
		\bigg),
\end{aligned}
$$

while the elements on the main diagonal are equivalent to

$$ \tag{12.13}
\begin{aligned}
	\frac{\partial^2}{\partial x^2} \Bigg( \frac{e^{i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg)
	= e^{i k R} \frac{R_x^2}{R^2}
		\bigg(
			  \frac{3}{R^3}
			- \frac{3 i k}{R^2}
			- \frac{k^2}{R}
		\bigg)
	- e^{i k R}
		\bigg(
			  \frac{1}{R^3}
			- \frac{i k}{R^2}
		\bigg).
\end{aligned}
$$

Take a look at the individual factors in Equations 12.11-12.13: those outside the brackets oscillate between 0 and 1, while the terms  inside greatly depend on the distance between the observation point \\(\bm{r}\\) and the source \\(\bm{r'}\\). This suggests that we may divide the entire space into zones based on the proximity to the observation point. If we are interested in the value of the field located near the source (the so-called *near field*), we say that the observation point belongs to the [near zone](https://en.wikipedia.org/wiki/Near_and_far_field). Similarly, far-away points (and the *far field*) are said to be located in the [far zone](https://en.wikipedia.org/wiki/Near_and_far_field) (also known as the *radiation zone*). Between them is a region called the *transition zone*.

If we fix a value of \\(k\\), we may decompose the electric tensor into the near-, transition-, and far-field components:

$$ \tag{12.14}
	\mathcal{G_{e}}
	= \mathcal{G_{en}}
	+ \mathcal{G_{et}}
	+ \mathcal{G_{ef}},
$$

such that

$$ \tag{12.15}
\begin{aligned}
	& \mathcal{G_{en}}(\bm{R}, k)
	= -\frac{1}{k^2 R^2} \bigg(\mathcal{I} - 3 \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) g(\bm{R}, k), \cr
	& \mathcal{G_{et}}(\bm{R}, k)
	= \frac{i}{k R} \bigg(\mathcal{I} - 3 \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) g(\bm{R}, k), \cr
	& \mathcal{G_{ef}}(\bm{R}, k)
	= \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) g(\bm{R}, k),
\end{aligned}
$$

with the scalar Green function containing an additional \\(R^{-1}\\) factor, so that

$$ \tag{12.16}
	k^{-1} \mathcal{G_{en}}
	\varpropto (k R)^{-3},
	\qquad
	k^{-1} \mathcal{G_{et}}
	\varpropto (k R)^{-2},
	\qquad
	k^{-1} \mathcal{G_{ef}}
	\varpropto (k R)^{-1}.
$$

Let us analyze the magnetic tensor in the same way. Write Equation 9.32 in the Cartesian coordinate system:

$$ \tag{12.17}
\begin{aligned}
	\mathcal{G_{m}}(\bm{r}, \bm{r'}, k)
	= \nabla g(\bm{r} - \bm{r'}, k) \times \mathcal{I}
	= \begin{bmatrix}
		\partial g / \partial x \cr
		\partial g / \partial y \cr
		\partial g / \partial z \cr
	\end{bmatrix} \times \mathcal{I}
	= \begin{bmatrix}
		0 & -\frac{\partial g}{\partial z} & \frac{\partial g}{\partial y} \cr
		\frac{\partial g}{\partial z} & 0 & -\frac{\partial g}{\partial x} \cr
		-\frac{\partial g}{\partial y} & \frac{\partial g}{\partial x} & 0 \cr
	\end{bmatrix}.
\end{aligned}
$$

Clearly, the matrix is anti-symmetric: \\(\mathcal{G_{m}} = -\mathcal{G_{m}}^T\\).

According to the expression of the first derivative given by Equation 12.11, the tensor only has two components:

$$ \tag{12.18}
	\mathcal{G_{m}} = \mathcal{G_{mt}} + \mathcal{G_{mf}},
$$

such that

$$ \tag{12.19}
\begin{aligned}
	& \mathcal{G_{mt}}(\bm{R}, k)
	= -\frac{1}{R} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) g(\bm{R}, k), \cr
	& \mathcal{G_{mf}}(\bm{R}, k)
	= i k \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) g(\bm{R}, k).
\end{aligned}
$$

Note that, as expected from Equation 9.33, the constants of proportionality are different in comparison to the electric tensor:

$$ \tag{12.20}
	k^{-2} \mathcal{G_{mt}}
	\varpropto (k R)^{-2},
	\qquad
	k^{-2} \mathcal{G_{mf}}
	\varpropto (k R)^{-1}.
$$

### Spherical Waves

Let us focus on the case when the observation point is far away from the source. It is important for two reasons: first, if we are dealing with small particles, a typical observation falls into this category, and second, because this assumption greatly simplifies the behavior of the scattered wave, as we shall soon demonstrate.

The idea behind the far-field approximation is simple: we discard certain terms in Equations 12.14 and 12.18. In order to do that, we must consider their magnitudes relative to each other:

$$ \tag{13.1}
	\mathcal{G_{e}}
	= \bigg( 1 + \frac{\mathcal{G_{et}}}{\mathcal{G_{ef}}} + \frac{\mathcal{G_{en}}}{\mathcal{G_{ef}}} \bigg) \mathcal{G_{ef}}, \qquad
	\mathcal{G_{m}}
	= \bigg( 1 + \frac{\mathcal{G_{mt}}}{\mathcal{G_{mf}}} \bigg) \mathcal{G_{mf}}.
$$

According to Equation 12.15, for any fixed direction, the relative difference between the three electric tensors primarily arises from the leading scalar terms. Moreover, the two magnetic tensors given by Equation 12.19 exhibit identical angular dependence. Following this logic, we use Equations 12.16 and 12.20 to approximate

$$ \tag{13.2}
	\mathcal{G_{e}}
	= \bigg( 1 + \mathrm{O} \Big( (k R)^{-1} \Big) + \mathrm{O} \Big( (k R)^{-2} \Big) \bigg) \mathcal{G_{ef}}, \qquad
	\mathcal{G_{m}}
	= \bigg( 1 + \mathrm{O} \Big( (k R)^{-1} \Big) \bigg)
	\mathcal{G_{mf}}.
$$

If \\(k R \gg 1\\), the values of the expressions in the brackets approach 1. This allows us to write Equation 12.5 as

$$ \tag{13.3}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\approx k^2(\omega) \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) \frac{e^{i k(\omega) R}}{4 \pi R} \alpha_m(V, \omega) \bm{E_i}(\bm{r'}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k^3(\omega)}{\omega} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) \frac{e^{i k(\omega) R}}{4 \pi R} \alpha_m(V, \omega) \bm{E_i}(\bm{r'}, \omega),
\end{aligned}
$$

where we renamed \\(\bm{r_0}\\) to \\(\bm{r'}\\) in order to avoid introducing additional auxiliary variables.

Let us stop for a moment to consider what \\(k R\\) represents. According to Equation 13.3, it is the [dimensionless](https://en.wikipedia.org/wiki/Dimensionless_quantity) phase difference (measured in radians) between the observation point \\(\bm{r}\\) and the location of the dipole \\(\bm{r'}\\). We may confirm this by dimensional analysis of Equations 7.6-7.7:

$$ \tag{13.4}
	k R = (\eta + i \kappa) \omega c^{-1} R = [\text{1}] [\text{1/s}] [\text{s/m}] [\text{m}] = [\text{1}].
$$

Intuitively, \\(k R \gg 1\\) implies that the wavelet generated by the dipole will go through a great number of cycles before reaching the observation point.

Equation 13.3 can be reduced to a simpler form by aligning the origin of the coordinate system with the location of the dipole (so that \\(\bm{r'} = 0\\) and \\(\bm{R} = \bm{r}\\)) and introducing the direction of observation \\(\bm{n} = \bm{r}/|\bm{r}| = \bm{r} / r\\):

$$ \tag{13.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\approx k^2(\omega) \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \frac{e^{i k(\omega) r}}{4 \pi r} \alpha_m(V, \omega) \bm{E_i}(0, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k^3(\omega)}{\omega} \big(\bm{n} \times \mathcal{I} \big) \frac{e^{i k(\omega) r}}{4 \pi r} \alpha_m(V, \omega) \bm{E_i}(0, \omega).
\end{aligned}
$$

Let us return to the general case of a scattering object. Assume that the observation point is in the far zone with respect to each individual volume element, so that \\(k R \gg 1\\). Equation 11.25 then takes the form

$$ \tag{13.6}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\approx k^2(\omega) \int\_{V} \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) \frac{e^{i k(\omega) R}}{4 \pi R} \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k^3(\omega)}{\omega} \int\_{V} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) \frac{e^{i k(\omega) R}}{4 \pi R} \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

Bound the object by a sphere of radius \\(a\\) centered at the origin of the coordinate system. This allows us to express the requirement that \\(k R \gg 1\\) for each individual volume element by \\(k (r - a) \gg 1\\).

Consider the expression of the radial distance between the observation point and the volume element:

$$ \tag{13.7}
\begin{aligned}
	R
	&= \sqrt{(\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'})} \cr
	&= \sqrt{|\bm{r}|^2 - 2 (\bm{r} \cdot \bm{r'}) + |\bm{r'}|^2} \cr
	&= r \sqrt{1 - 2 (\bm{n} \cdot \bm{n'}) (r'/r) + (r'/r)^2},
\end{aligned}
$$

where \\(\bm{n'} = \bm{r'}/|\bm{r'}| = \bm{r'}/r\\).

We may expand the square root (and its reciprocal) in the Taylor series of \\(r'/r\\) around zero:

$$ \tag{13.8}
\begin{aligned}
	& k R
	= k r \bigg( 1 - \big( \bm{n} \cdot \bm{n'} \big) (r'/r) + \frac{1}{2} \Big( 1 - \big( \bm{n} \cdot \bm{n'} \big)^2 \Big) (r'/r)^2 + \mathrm{O} \Big( (r'/r)^3 \Big) \bigg), \cr
	& \frac{1}{R}
	= \frac{1}{r} \bigg( 1 + \big( \bm{n} \cdot \bm{n'} \big) (r'/r) + \mathrm{O} \Big( (r'/r)^2 \Big) \bigg).
\end{aligned}
$$

In order for \\(r'/r \ll 1\\) for all \\(r'\\), we require that \\(a/r \ll 1\\) (or, alternatively, \\(kr \gg ka\\)). Further assuming that \\(\frac{1}{2} k a (a/r) \ll 1\\), which is formally equivalent to \\(kr \gg \frac{1}{2} (ka)^2\\), we arrive at the approximation

$$ \tag{13.9}
	k R \approx k r - k (\bm{r'} \cdot \bm{n}), \qquad
	\frac{1}{R} \approx \frac{1}{r}.
$$

Next, we shall examine the expression of the directions of observation of individual volume element:

$$ \tag{13.10}
\begin{aligned}
	\frac{\bm{R}}{R}
	&= \frac{\bm{r} - \bm{r'}}{r \sqrt{1 - 2 \big( \bm{n} \cdot \bm{n'} \big) (r'/r) + (r'/r)^2}} \cr
	&= \bigg( \bm{n} - (r'/r) \bm{n'} \bigg) \bigg( 1 + \big( \bm{n} \cdot \bm{n'} \big) (r'/r) + \mathrm{O} \Big( (r'/r)^2 \Big) \bigg).
\end{aligned}
$$

Assume that the sphere covers a small solid angle when viewed from the observation point, which implies that \\(a/r \ll 1\\) . As the bounding volume shrinks to a point, the directions of observation corresponding to individual volume elements align, and we may write

$$ \tag{13.11}
	\frac{\bm{R}}{R} \approx \bm{n}.
$$

Substitution of Equations 13.9 and 13.11 into Equation 13.6 produces the *far-field approximation* of the volume integral equation:

$$ \tag{13.12}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) \approx k^2(\omega)
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega) \approx \frac{k^3(\omega)}{\omega}
	\big(\bm{n} \times \mathcal{I} \big)
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV', \cr
\end{aligned}
$$

subject to the conditions \[[8](#references) (ch. 5)\]

$$ \tag{13.13}
	k r - k a \gg 1, \qquad
	kr \gg ka, \qquad
	kr \gg \frac{1}{2} (k a)^2.
$$

We would like to caution that these conditions are, strictly speaking, only valid for Equation 13.6. If one considers a derived quantity (such as the time-averaged Poynting vector), the results of the preceding error analysis are no longer accurate, and the final expression should be examined instead.

Equations 13.5 and 13.12 show an example of *vector* spherical waves, since the global phase factor \\(\exp(i k r)\\) does not depend on the direction of observation. Note that surfaces of constant phase do not coincide with surfaces of constant amplitude, since the latter may depend on the direction of observation.

An important property of the far-field solution is the fact that the scattered field vectors are transverse with respect to the direction of observation:

$$ \tag{13.14}
\begin{aligned}
	& \bm{n} \cdot \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)
	= \Big( \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)^{T} \bm{n} \Big)^{T}
	= \big( \bm{n} - \bm{n} (\bm{n} \cdot \bm{n}) \big)^{T}
	= 0, \cr
	& \bm{n} \cdot \big( \bm{n} \times \mathcal{I} \big)
	= \Big( \big( \bm{n} \times \mathcal{I} \big)^{T} \bm{n} \Big)^{T}
	= \big( {-\bm{n}} \times \bm{n} \big)^{T}
	= 0.
\end{aligned}
$$

Above, we utilized the symmetry properties in addition to the alternative expression of the tensor product given by Equations 9.22-9.23.

We can also show that, under certain conditions, the electric and the magnetic fields are mutually orthogonal. We begin by factoring out the common term

$$ \tag{13.15}
	\bm{K}(\bm{r}, \omega)
	= k^2(\omega) \frac{e^{i k(\omega) r}}{4 \pi r} \int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV',
$$

which simplifies Equation 13.12 to

$$ \tag{13.16}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\approx \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \bm{K}(\bm{r}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k(\omega)}{\omega} \big(\bm{n} \times \mathcal{I} \big) \bm{K}(\bm{r}, \omega).
\end{aligned}
$$

Now, take the cross product of the direction of observation with Equation 13.16.1, multiply the result by \\(k/\omega\\), and compare it to Equation 13.16.2. The expressions are clearly identical:

$$ \tag{13.17}
	\frac{k(\omega)}{\omega} \bm{n} \times \bm{E_s}(\bm{r}, \omega)
	\approx \bm{B_s}(\bm{r}, \omega)
$$

Furthermore, notice the resemblance of Equation 13.17 to 7.22.

Next, multiply Equation 13.17 by \\(\exp(-i \omega t)\\), expand the expression of the complex wavenumber as per Equation 7.9, and take the real part of both sides of the equation:

$$ \tag{13.18}
	\bm{n} \times \bigg(
	\frac{\eta(\omega)}{c} \mathcal{Re} \big\lbrace \bm{E_s} e^{-i \omega t} \big\rbrace
	- \frac{\kappa(\omega)}{c} \mathcal{Im} \big\lbrace \bm{E_s} e^{-i \omega t} \big\rbrace \bigg)
	\approx \mathcal{Re} \big\lbrace \bm{B_s} e^{-i \omega t} \big\rbrace.
$$

We encounter a situation similar to vector plane waves, where the absorptive character of the homogeneous medium poses a complication. If we assume that \\(\kappa = 0\\), the field vectors become orthogonal, and we obtain a counterpart of Equation 7.29:

$$ \tag{13.19}
	\frac{\big\vert \mathcal{Re} \big\lbrace \bm{B_s}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace \big\vert}{\big\vert \mathcal{Re} \big\lbrace \bm{E_s}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace \big\vert}
	= \frac{\eta(\omega)}{c}.
$$

Thus, at a large distance, for a fixed direction of observation, a spherical wave is an analog of a plane wave (cf. Equation 7.2) with the amplitude decreasing as \\(r^{-1}\\):

$$ \tag{13.20}
	\bm{E_s}(\bm{r}, \omega) e^{-i \omega t}
	= \frac{1}{r} \bm{E_1}(\bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n}) - i \omega t}
	\quad
	\text{where}
	\quad
	\bm{n} = \frac{\bm{r}}{r},
$$

where the absorption coefficient \\(\alpha\\) is defined according to Equation 7.40.

[Picture]

If the field vectors are orthogonal, and the ratio of their magnitudes is known, the (monochromatic) Poynting vector has the form

$$ \tag{13.21}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \frac{\eta(\omega)}{c} {\big\vert \mathcal{Re} \big\lbrace \bm{E_s}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace \big\vert}^2 \bm{n}.
$$

Similarly to the case of plane waves, we may form a Cartesian coordinate frame using the set of vectors \\(\lbrace \bm{E_1}, \bm{B_1}, \bm{n} \rbrace\\), we obtain the expressions of the Poynting vector (cf. Equation 7.31)

$$ \tag{13.22}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \frac{\eta}{c} \bigg( \frac{|E_{1,x}|^2}{r^2} \cos^2{\theta_x} + \frac{|E_{1,y}|^2}{r^2} \cos^2{\theta_y} \bigg) \bm{n}
$$

and irradiance (cf. Equation 7.32)

$$ \tag{13.23}
	\Epsilon
	= \braket{\bm{S}} \cdot \bm{n'}
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} \frac{|\bm{E_1}|^2}{r^2} \big( \bm{n} \cdot \bm{n'} \big)
$$

that follow the [inverse-square law](https://en.wikipedia.org/wiki/Inverse-square_law).

For completeness, we would like to point out that it is possible to support the case when the host medium is absorptive and magnetic. The absorption requirement makes \\(k\\) complex; in addition, we must globally replace \\(\mu\_0\\) by \\(\mu_1(\omega)\\) starting from Equation 11.4. Since the vectors are no longer orthogonal, Equations 13.21-13.23 become invalid, and we must follow the Poynting phasor route given by Equations 4.16-4.17 instead:

$$ \tag{13.24}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \Big\lbrace \bm{E_s}(\bm{r}, \omega) \times \big[ \bm{B_s}(\bm{r}, \omega) \big]^{\*} \Big\rbrace.
$$

Compare Equation 13.24 to 7.35; except for the names of the fields, their properties are the same (up to the \\(1/r\\) factor). Thus, the analysis performed in Equations 7.36-7.41 still applies, and we thus arrive at the analog of Equation 7.42:

$$ \tag{13.26}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \bigg\lbrace
		\sqrt{ \frac{\varepsilon}{\mu} }
	\bigg\rbrace \frac{|\bm{E_1}|^2}{r^2} e^{-\alpha (\bm{r} \cdot \bm{n})} \bm{n}.
$$

### Transition Operator

The volume integral equation can be reduced to a relatively simple expression by assuming that the observation point is located in the far zone of the scatterer. However, the value of the electric field in the interior remains unknown. For a single atom or a small molecule, we can reasonably assume that it doesn't drive itself, which makes the field driving the molecule the same as the incident field, with the resulting (simplified) expression of Equation 13.12 given by Equation 13.5. Yet, in general, this assumption does not hold. It is easy to convince ourselves by considering a highly reflective or absorptive particle - it seems fairly obvious that the total field deep inside the particle has (on average) a lower magnitude in comparison to the incident field at the same location if the particle wasn't there.

Let us recall the mechanics of scattering. In a dielectric, the incident field drives the dipoles, which in turn act as sources of scattered wavelets interfering with the incident field according to the superposition principle. We may continue this line of thinking by considering the effect of a dipole as a secondary source on all other dipoles surrounding it, effectively treating the dipole field as the secondary incident field. Repeated application of this iterative approach leads to evaluation of successive orders of scattering one by one. This is the general idea behind the [Born series](https://en.wikipedia.org/wiki/Born_series) \[[6](#references) (ch. 13.1)\].

We can formalize this approach in the following way \[[8](#references) (ch. 4.5)\]. We start with Equation 11.12, where we substitute the total electric tensor (cf. Equations 9.27-9.28) to ensure that the integral remains valid even if the integration point is inside the volume \\(V\\):

$$ \tag{14.1}
	\bm{E}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega)
	+ \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k(\omega) \big) \bm{J'}(\bm{r'}, \omega) dV'.
$$

Next, for we substitute the expression of the source term \\(\bm{J'}\\) given by Equation 11.7:

$$ \tag{14.2}
	\bm{E}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega)
	+ \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k(\omega) \big) k^2(\omega) \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
$$

For convenience, we define the *potential function*

$$ \tag{14.3}
u(\bm{r}, k, \omega) =
\begin{cases}
   k^2 \big( m^2(\bm{r}, \omega) - 1 \big) &\text{if } \bm{r} \in V, \cr
   0 &\text{otherwise},
\end{cases}
$$

that allows us to write Equation 14.2 in a more compact form:

$$ \tag{14.4}
	\bm{E}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega)
	+ \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k(\omega) \big) u \big( \bm{r'}, k(\omega), \omega \big)  \bm{E}(\bm{r'}, \omega) dV'.
$$

The resulting equation is recursive. Substitution makes it fairly obvious that the total electric field can be expressed as volume integral of the transformed incident field. While this fact can be rigorously derived from the properties of the tensor Green functions \[[12](#references) (ch. 5.1)\], for simplicity, let us assume that the solution exists:

$$ \tag{14.5}
	u \big( \bm{r'}, k(\omega), \omega \big) \bm{E}(\bm{r'}, \omega)
	= \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, k(\omega), \omega \big) \bm{E_i}(\bm{r''}, \omega) dV'',
$$

where \\(\mathcal{T}\\) is the *transition tensor*, the expression of which is yet to be determined.

Recursive equations written in the integral form quickly become unwieldy. Instead, we shall convert them into the operator form by using the [bra-ket notation](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation). Additionally, any dependence on the frequency or the wavenumber shall be kept implicit.

Our building blocks are state vectors (or *kets*), such as \\(\ket{E}\\), and linear functionals (or *bras*), such as \\(\bra{r}\\). Put together, they represent the projection (the [inner product](https://en.wikipedia.org/wiki/Inner_product)) of the two vectors:

$$ \tag{14.6}
	  \braket{r \vert E}
	= \big( \ket{r}, \ket{E} \big)
	= \bm{E}(\bm{r}).
$$

In addition, we can define the *potential operator* \\(U\\), such that, in the position representation,

$$ \tag{14.7}
	\bra{r} U = u(\bm{r}) \bra{r}.
$$

Similarly, we define the *Green operator* \\(G\\) and the *transition operator* \\(T\\) as

$$ \tag{14.8}
	\braket{r \vert G \vert r'} = \mathcal{G}(\bm{r}, \bm{r'}), \quad
	\braket{r \vert T \vert r'} = \mathcal{T}(\bm{r}, \bm{r'}).
$$

Finally, we must define the *orthonormalization* relation

$$ \tag{14.9}
	  \braket{r \vert r'} = \delta(\bm{r} - \bm{r'})
$$

and the *closure* relation

$$ \tag{14.10}
	\int dV \ket{r} \bra{r} = \mathcal{I},
$$

which, when taken together, say that the set of \\(\ket{r}\\) vectors constitutes a basis.

In the new notation, Equation 14.4 can be written as

$$ \tag{14.11}
	\braket{r \vert E}
	= \braket{r \vert E_i}
	+ \int dV' \braket{r \vert G \vert r'} \braket{r' \vert U \vert E},
$$

or, in the representation-independent form,

$$ \tag{14.12}
	\ket{E} = \ket{E_i} + G U \ket{E}.
$$

Equation 14.12 is formally identical to the [Lippmann–Schwinger equation](https://en.wikipedia.org/wiki/Lippmann%E2%80%93Schwinger_equation).

Similarly, Equation 14.5 can be transformed into

$$ \tag{14.13}
	U \ket{E} = T \ket{E_i},
$$

which shows a transition from the state \\(\ket{E_i}\\) to \\(\ket{E}\\).

Substitution of Equation 14.13 into 14.12 produces an alternative expression of the total field

$$ \tag{14.14}
	\ket{E} = \ket{E_i} + G T \ket{E_i}.
$$

In the position representation,

$$ \tag{14.15}
	\braket{r \vert E}
	= \braket{r \vert E_i}
	+ \int dV' \int dV'' \braket{r \vert G \vert r'}
	\braket{r' \vert T \vert r''} \braket{r'' \vert E},
$$

and the corresponding integral form is

$$ \tag{14.16}
	\bm{E}(\bm{r})
	= \bm{E_i}(\bm{r})
	+ \int\_{V} \mathcal{G} (\bm{r}, \bm{r'}) \int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}) \bm{E_i}(\bm{r''}) dV'' dV'.
$$

Note that Equation 14.16 is identical to the combination of Equations 14.4 and 14.5.

Now, let us multiply both sides of Equation 14.14 by \\(U\\):

$$ \tag{14.17}
	U \ket{E}
	= U \ket{E_i} + U G T \ket{E_i}
	= U (I + G T) \ket{E_i},
$$

and substitute Equation 14.13 once more:

$$ \tag{14.18}
	T \ket{E_i} = U (I + G T) \ket{E_i}.
$$

Since the ket \\(\ket{E_i}\\) is arbitrary, it means the transition operator does not depend on the incident field:

$$ \tag{14.19}
	T = U (I + G T)
$$

It does, however, depend on the geometry and the structure of scattering object.

Notice that, when we combine Equations 14.13 and 14.19, the potential operator cancels out, and we obtain a series expansion

$$ \tag{14.20}
\begin{aligned}
	\ket{E}
	&= (I + G T) \ket{E_i} \cr
	&= \big( I + G U (I + G T) \big) \ket{E_i} \cr
	&= \ket{E_i} + G U \ket{E_i} + (G U)^2 \ket{E_i} + \dots
\end{aligned}
$$

that corresponds to the recursive substitution of Equation 14.4.

Let us now complete the derivation by writing Equation 14.19 in the position representation:

$$ \tag{14.21}
\begin{aligned}
	\braket{r \vert T \vert r'}
	&= \braket{r \vert U (I + G T) \vert r'} \cr
	&= u(\bm{r}) \braket{r \vert I + G T \vert r'} \cr
	&= u(\bm{r}) \bigg( \negmedspace \braket{r \vert r'} + \int dV \braket{r \vert G \vert r''} \braket{r'' \vert T \vert r'} \negmedspace \bigg).
\end{aligned}
$$

Translation of Equation 14.21 into the integral form yields the definition of the transition tensor:

$$ \tag{14.22}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= u(\bm{r}, k) \bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I} + \int_V \mathcal{G} (\bm{r}, \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg)
	\cr
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I} + \int_V \mathcal{G} (\bm{r}, \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

The total electric tensor (defined in Equation 9.27) can be expanded in two different ways. One way is to write it as a product, and take the factor that contains derivatives the integral (thus avoiding the singularity), as was done in Equation 9.24:

$$ \tag{14.23}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&+ \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) \int\_{V}
	g( \bm{r} - \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

The second way is to represent it by a sum of the electric tensor \\(\mathcal{G_e}\\) and the depolarization tensor \\(\mathcal{L}\\), like in Equation 9.25:

$$ \tag{14.24}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&- \frac{1}{k^2} \mathcal{L}(\bm{r}) \mathcal{T} (\bm{r}, \bm{r'})
	+ \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G_e} (\bm{r}, \bm{r''}) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

Notice that the \\(1/k^2\\) term in front of the depolarization tensor cancels out. After rearranging the terms, we obtain the full definition of the transition tensor:

$$ \tag{14.25}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big)
	\Big( \mathcal{I} + \big( m^2(\bm{r}) - 1 \big) \mathcal{L}(\bm{r}) \Big)^{-1} \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&+ \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G_e} (\bm{r}, \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

### Scattering Matrix

An arbitrary stream of electromagnetic radiation can decomposed into a number of individual plane waves. Specifically, in a *linear, isotropic, homogeneous* medium, the expression of the incident field is given by Equation 6.16:

$$ \tag{15.1}
	\bm{E_i}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E_i}(\bm{r}, \bm{n_i}, \omega) d\Omega_i =
	\oint\_{\mathbb{S}^2} \bm{E_i}(0, \bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i})} d\Omega_i.
$$

We have also learned (cf. Equation 14.16) that the scattered field is a linear function of the incident field. This allows us to utilize the superposition principle, according to which, the scattered field is a superposition of scattered waves generated by individual incident plane waves. Stated mathematically,

$$ \tag{15.2}
	\bm{E_s}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E_s}(\bm{r}, \bm{n_i}, \omega) d\Omega_i,
$$

where

$$ \tag{15.3}
\begin{aligned}
	\bm{E_s}(\bm{r}, \bm{n_i}, \omega)
	&= \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k(\omega) \big) \int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) \bm{E_i}(\bm{r''}, \bm{n_i}, \omega) dV'' dV'
	\cr
	&= \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k(\omega) \big) \int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) \bm{E_i}(0, \bm{n_i}, \omega) e^{i k(\omega) (\bm{r''} \cdot \bm{n_i})} dV'' dV'.
\end{aligned}
$$

By taking the polarization phasor \\(\bm{E_i}(0)\\) outside the integral, we find the that the scattered field is related to the incident field by the *scattering tensor* \\(\mathcal{S}\\) \[[8](#references) (ch. 5.3)\]:

$$ \tag{15.4}
	\bm{E_s}(\bm{r}, \bm{n_i}, \omega)
	= \frac{e^{i k(\omega) r}}{r} \mathcal{S_e} (\bm{r}, \bm{n_i}, \omega) \bm{E_i}(0, \bm{n_i}, \omega).
$$

In general, the scattering tensor depends on the position of the observation point relative to the scatterer. However, if the observation point is very far away, we can utilize the far-field expression of the Green function (cf. Equations 13.12, 14.3, and 14.5) to write

$$ \tag{15.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) \approx
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big)
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s})} \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, k(\omega), \omega \big) \bm{E_i}(\bm{r''}, \omega) dV'' dV', \cr
	& \bm{B_s}(\bm{r}, \omega) \approx
	\big(\bm{n_s} \times \mathcal{I} \big) \frac{k(\omega)}{\omega}
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s})} \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, k(\omega), \omega \big) \bm{E_i}(\bm{r''}, \omega) dV'' dV',
\end{aligned}
$$

making the scattering tensor independent from the distance to the observation point:

$$ \tag{15.6}
\begin{aligned}
	& \bm{E_s}(r \bm{n_s}, \bm{n_i}, \omega)
	\approx \frac{e^{i k(\omega) r}}{r} \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \bm{E_i}(0, \bm{n_i}, \omega), \cr
	& \bm{B_s}(r \bm{n_s}, \bm{n_i}, \omega)
	\approx \frac{e^{i k(\omega) r}}{r} \mathcal{S_{mf}} (\bm{n_s}, \bm{n_i}, \omega) \bm{E_i}(0, \bm{n_i}, \omega).
\end{aligned}
$$

For completeness, the expressions of the scattering tensors for the electric and the magnetic fields are given below:

$$ \tag{15.7}
\begin{aligned}
	& \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \approx \frac{1}{4 \pi}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big)
	\iint\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s} - \bm{r''} \cdot \bm{n_i})} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) dV'' dV', \cr
	& \mathcal{S_{mf}} (\bm{n_s}, \bm{n_i}, \omega) \approx \frac{1}{4 \pi}
	\big(\bm{n_s} \times \mathcal{I} \big) \frac{k(\omega)}{\omega}
	\iint\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s} - \bm{r''} \cdot \bm{n_i})} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) dV'' dV'.
\end{aligned}
$$

Expressions given above are independent of the choice of a coordinate system. In practice, it is rather convenient to establish a certain coordinate convention.

{{< figure src="/img/scat_coord.svg" caption="*Figure N: the scattering coordinate system.*" >}}

First, recall that \\(r\\) in Equation 15.6 stands for the distance from the center of the scattering object to the observation point. This makes the center of the object a suitable choice for the origin of the coordinate system.

Next, assume that the direction of incidence \\(\bm{n_i}\\) is fixed; conventionally, it is aligned with the \\(z\\)-axis. That is convenient, because it allows us to describe the incident electric field vector (or phasor) using only two Cartesian components, \\(x\\) and \\(y\\), since, according to Equations 7.18-7.19, the component along \\(\bm{n_i}\\) is zero.

We must also choose the *plane of reference* that fixes the orientation of the coordinate frame \[[4](#references) (ch. 5.11)\]. The decision is somewhat arbitrary (except for the fact that the plane should contain \\(\bm{n_i}\\) for the reason outlined above). A simple choice that reduces the number of degrees of freedom is to use the *plane of observation* spanned by \\(\bm{n_i}\\) and \\(\bm{n_s}\\) \[[4](#references) (ch. 5.22)\]. In summary, the coordinate convention for the incident field is

$$ \tag{15.8}
	\bm{z} = \bm{n_i}, \quad
	\bm{y} = \frac{\bm{n_i} \times \bm{n_s}}{\vert \bm{n_i} \times \bm{n_s} \vert}, \quad
	\bm{x} = \bm{y} \times \bm{z}.
$$

Imagine that the *scattering coordinate system* \\(xyz\\) is initially aligned with the *global coordinate system* \\(XYZ\\), so that

$$ \tag{15.9}
	\bm{Z} = \bm{n_i}, \quad
	\bm{Y} = \bm{n_i} \times \bm{n_p}, \quad
	\bm{X} = \bm{n_p}.
$$

 The latter could, for instance, represent the coordinate frame of the scatterer. We can now rotate \\(xyz\\) and define its orientation relative to \\(XYZ\\) in terms of the [Euler angles](https://en.wikipedia.org/wiki/Euler_angles). We are particularly interested in rotating the plane of observation \\(xz\\) about the \\(Z\\)-axis, as that allows the set of possible direction of observation to cover the entire the unit sphere. If the azimuthal (plane rotation) angle is \\(\phi\\) and the polar angle is \\(\theta\\), the spherical coordinates of \\(\bm{n_s}\\) with respect to the \\(XYZ\\) frame are

$$ \tag{15.10}
\bm{n_s} =
\begin{bmatrix}
	\theta \cr
	\phi   \cr
\end{bmatrix} =
\begin{bmatrix}
	\mathrm{atan2}(X, Z) \cr
	\mathrm{atan2}(Y, X) \cr
\end{bmatrix} =
\begin{bmatrix}
	\mathrm{atan2}(\bm{n_s} \cdot \bm{n_p}, \bm{n_s} \cdot \bm{n_i}) \cr
	\mathrm{atan2}(\bm{n_s} \cdot (\bm{n_i} \times \bm{n_p}), \bm{n_s} \cdot \bm{n_p}) \cr
\end{bmatrix}. $$

Note that we must use the [atan2](https://en.wikipedia.org/wiki/Atan2) function in order to obtain the full range of values of \\(\theta\\) and \\(\phi\\).

It can be seen that \\(\theta = 0\\) corresponds to *forward scattering*, and \\(\theta = \pi\\) -- to *back-scattering*.

Unfortunately, the rotation of the coordinate frame causes the values of the components of the incident electric field to change \[[4](#references) (ch. 5.31)\]. If the angle of rotation of the plane is \\(\phi\\), the field vectors (and phasors) are [correspondingly](https://en.wikipedia.org/wiki/Active_and_passive_transformation#Passive_transformation) rotated by \\(-\phi\\) radians:

$$ \tag{15.11}
\begin{bmatrix}
	E\_{x}(\phi) \cr
	E\_{y}(\phi) \cr
	E\_{z}(\phi) \cr
\end{bmatrix} =
R_z(-\phi)
\begin{bmatrix}
	E\_{X} \cr
	E\_{Y} \cr
	E\_{Z} \cr
\end{bmatrix} =
\begin{bmatrix}
	\phantom{-}\cos{\phi} & \sin{\phi} & 0 \cr
	-\sin{\phi}           & \cos{\phi} & 0 \cr
	0                     & 0          & 1 \cr
\end{bmatrix}
\begin{bmatrix}
	E\_{X} \cr
	E\_{Y} \cr
	E\_{Z} \cr
\end{bmatrix}.
$$

On the other hand, this convention makes computing the coordinates of the scattered field vectors (and phasors) easy.  Since \\(\bm{n_s}\\) is orthogonal to \\(\bm{y}\\), we can define

$$ \tag{15.12}
	\bm{z'} = \bm{n_s}, \quad
	\bm{y'} = \bm{y}, \quad
	\bm{x'} = \bm{y} \times \bm{n_s}.
$$

As Equation 13.14 shows, the scattered field vectors (and phasors) are orthogonal to \\(\bm{n_s}\\). Thus, in the \\(x' y' z'\\) coordinate system, only the \\(x'\\) and \\(y'\\) components of \\(\bm{E_s}\\) and \\(\bm{B_s}\\) are non-zero.

Equation 15.12 represents a rotation of the \\(x' y'\\) plane about the \\(y\\)-axis:

$$ \tag{15.13}
\begin{bmatrix}
	E\_{x'}(\theta, \phi) \cr
	E\_{y'}(\theta, \phi) \cr
	E\_{z'}(\theta, \phi) \cr
\end{bmatrix} =
R_y(-\theta)
\begin{bmatrix}
	E\_{x}(\phi) \cr
	E\_{y}(\phi) \cr
	E\_{z}(\phi) \cr
\end{bmatrix} =
\begin{bmatrix}
	\cos{\theta} & 0 & -\sin{\theta} \cr
	0            & 1 & 0             \cr
	\sin{\theta} & 0 & \phantom{-}\cos{\theta} \cr
\end{bmatrix}
\begin{bmatrix}
	E\_{x}(\phi) \cr
	E\_{y}(\phi) \cr
	E\_{z}(\phi) \cr
\end{bmatrix}.
$$

Suppose that the amplitude and the phase of the incident field is known at the plane with the \\(z\\)-coordinate (where \\(z\\) is a negative number). Then the value of the field at the origin is

$$ \tag{15.14}
\begin{bmatrix}
	E\_{i,x}(0, 0, \phi, \omega) \cr
	E\_{i,y}(0, 0, \phi, \omega) \cr
\end{bmatrix} =
e^{-i k(\omega) z}
\begin{bmatrix}
	E\_{i,x}(z, 0, \phi, \omega) \cr
	E\_{i,y}(z, 0, \phi, \omega) \cr
\end{bmatrix}.
$$

Since we can represent both the incident and the scattering fields using only two components (each), Equation 15.6.1 can be expressed[^14] in terms of the complex *scattering matrix*, or the [S-matrix](https://en.wikipedia.org/wiki/S-matrix) for short.

[^14]: The numbering convention and the division by \\(i k\\) are retained to match the results in the book by van de Hulst \[[4](#references) (ch. 4.41)\]. However, we do not make any changes to the tensor form of Equations 15.6-15.7 that follow the book by Mishchenko \[[8](#references) (ch. 5.3)\], since we find them entirely appropriate.

$$ \tag{15.15}
S(\theta, \phi, \omega) =
\begin{bmatrix}
	s_2(\theta, \phi, \omega) & s_3(\theta, \phi, \omega) \cr
	s_4(\theta, \phi, \omega) & s_1(\theta, \phi, \omega) \cr
\end{bmatrix},
$$

such that

$$ \tag{15.16}
\begin{bmatrix}
	E\_{s,x'}(r, \theta, \phi, \omega) \cr
	E\_{s,y }(r, \theta, \phi, \omega) \cr
\end{bmatrix} \approx
\frac{e^{i k(\omega) r - i k(\omega) z}}{i k(\omega) r} S(\theta, \phi, \omega)
\begin{bmatrix}
	E\_{i,x}(z, 0, \phi, \omega) \cr
	E\_{i,y}(z, 0, \phi, \omega) \cr
\end{bmatrix}.
$$

Comparison with Equation 15.6 (after after taking Equation 15.13 into account) shows that

$$ \tag{15.17}
	S(\theta, \phi, \omega)
	= i k(\omega) R_y(-\theta) \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega),
$$

with the angles \\(\theta\\) and \\(\phi\\) given by Equation 15.10.

The reason for introducing the \\(i k\\) constant in the expression above is twofold. Multiplication by the \\(k\\) factor makes the scattering matrix a dimensionless quantity, which is evident from Equations 13.4 and 15.16. As for the \\(i\\) factor, we shall see (in the next section) that it allows us to focus on the real part of the scattering matrix for radiometric computations, which is consistent with the way we handle the complex representation of electromagnetic waves.

In general, the elements of the scattering matrix depend on the azimuthal angle \\(\phi\\), since, as we rotate the plane of observation (\\(x z\\) or \\(x' z'\\)) around the \\(Z\\)-axis, the scattering object appears to rotate (in the opposite direction) in both the \\(x y z\\) and \\(x' y' z'\\) coordinate systems. However, if the object is symmetric (both geometrically and structurally) with respect to the \\(Z\\)-axis, which is the case for spherical and an axis-aligned [spheroidal](https://en.wikipedia.org/wiki/Spheroid) particles, the matrix only depends on the polar angle \\(\theta\\). This causes the scattering matrix to become diagonal, with \\(s_3 = s_4 = 0\\) \[[4](#references) (ch. 4.42)\].

It's easy to see why that is the case. Consider changing the handedness of the \\(x y z\\) coordinate frame, so that the basis vector of the \\(x\\)-axis (or \\(y\\)-axis) becomes \\(-\bm{x}\\) (or \\(-\bm{y}\\)), and vice versa. In the reflected coordinate system, both \\(E\_{s,x'}\\) and \\(E\_{i,x}\\) (or \\(E\_{s,y}\\) and \\(E\_{i,y}\\)) change the sign, but, since the particle possesses reflection symmetry, the scattering matrix remains unchanged. In order for the magnitudes of the scattered field components to remain unchanged, the off-diagonal elements \\(s_3\\) and \\(s_4\\) must be equal to \\(0\\). This decouples the \\(x\\) and \\(y\\) components of the field, which allows us to represent the vector scattering process in terms of two independent scalar waves.

In addition, a very similar proof shows that axial symmetry with respect to the direction of incidence leads to another important property of forward scattering: \\(s_1(0, \phi) = s_2(0, \phi) = s_0\\).

Finally, we would like to highlight the *scale invariance* property of electromagnetic scattering (also known as the principle of electrodynamic similitude) \[[8](#references) (ch. 5.5), [9](#references) (ch. 3.5)\]. The gist of it is that the dimensionless scattering and absorption characteristics of the scattering object do not depend on its linear dimension \\(a\\) and the wavenumber \\(k\\) separately, but rather the so-called relative *size parameter* \\(x = k a = 2 \pi a / \lambda\\). Thus, increasing both the linear dimension of the object and the wavelength by the same factor \\(f\\) leaves the formula unchanged: \\(x' = 2 \pi (a f) / (f \lambda) = x \\). This [reduces the dimensionality](https://en.wikipedia.org/wiki/Dimensionality_reduction) of the problem, since two independent parameters can be replaced with just one.

In order to prove that it is true, we must show that the expressions used to calculate the electromagnetic fields can be written in terms of the dimensionless quantity

$$ \tag{15.18}
	\bm{\hat{r}} = k \bm{r}.
$$

As a simple example, consider the scalar Green function \\(g\\) introduced in Equation 9.10. If we divide it by \\(k\\), we obtain its dimensionless counterpart \\(\hat{g}\\):

$$ \tag{15.19}
	\frac{1}{k} g(\bm{r}, k)
	= \hat{g}(\bm{\hat{r}})
	= \frac{e^{i |\bm{\hat{r}}|}}{4 \pi |\bm{\hat{r}}|}.
$$

The expression of the total field is the sum of the incident and the scattered fields. Equation 15.1 of the incident field can be transformed into the dimensionless form by a trivial substitution of Equation 15.18. On the other hand, Equation 15.3 of the scattered field is more complicated, as it contains the total electric and transition tensors. According to Equation 14.22, the transition tensor is itself defined in terms of the total electric tensor. Thus, we only need to transform the expression of the former. For convenience, we elect to use the expanded definition given by Equation 14.23, which we restate below:

$$ \tag{15.20}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I} + \int_V \mathcal{G} (\bm{r}, \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg)
	\cr
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&+ \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) \int\_{V}
	g( \bm{r} - \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

In order to replace \\(\bm{r}\\) with \\(\bm{\hat{r}}\\) inside the integral, we must perform a change of variables

$$ \tag{15.21}
	dV = dx \thinspace dy \thinspace dz
	\quad \to \quad
	d \hat{V} = dkx \thinspace dky \thinspace dkz = k^3 dV.
$$

Furthermore, the [scaling property](https://en.wikipedia.org/wiki/Dirac_delta_function#Scaling_and_symmetry) of the delta function tells us that

$$ \tag{15.22}
	\delta(\bm{r})
	= \delta \bigg(\frac{\bm{\hat{r}}}{k} \bigg)
	= k^3 \delta(\bm{\hat{r}})
$$

To handle the total electric tensor, we introduce

$$ \tag{15.23}
	\hat{\nabla} = k^{-1} \nabla,
$$

such that

$$ \tag{15.25}
	\hat{\nabla} \bm{\hat{r}} = \nabla \bm{r},
$$

which, coupled with Equation 15.19, directly leads to

$$ \tag{15.26}
	\mathcal{\hat{G}}(\bm{\hat{r}}, \bm{\hat{r}'})
	= \frac{1}{k} \mathcal{G}(\bm{r}, \bm{r'}, k).
$$

The only expression left to transform is the relative refractive index \\(m\\) given by Equation 11.8. It is already a dimensionless quantity; if we further assume that it has been defined as scale-invariant (both the index and its spatial distribution do not change as the object is rescaled), then we may reparametrize \\(m\\) in the following way:

$$ \tag{15.27}
	\hat{m}(\bm{\hat{r}})
	= m(\bm{r})
	= \frac{k_2}{k_1}
	= \frac{(\omega / c) \big( \eta + i \kappa \big)}{2 \pi / \lambda_1}
	= \frac{\lambda_1}{\lambda_2} + i \frac{\lambda_1 \alpha}{4 \pi}.
$$

The expanded expression clarifies why it remains invariant: the wavelength in the interior of the object is directly proportional to the wavelength outside, and as we increase the size of the object, the absorption coefficient linearly decreases in order to maintain the absorption characteristics of the scatterer.

Using the new definitions, Equation 15.20 becomes

$$ \tag{15.28}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= \big( \hat{m}^2(\bm{\hat{r}}) - 1 \big) \bigg(
	k^5 \delta(\bm{\hat{r}} - \bm{\hat{r}'}) \mathcal{I}
	+ \int\_{V} \mathcal{\hat{G}}(\bm{\hat{r}}, \bm{\hat{r}''}) \mathcal{T} (\bm{r''}, \bm{r'}, k) d \hat{V}'' \bigg)
	\cr
	&= \big( \hat{m}^2(\bm{\hat{r}}) - 1 \big) \bigg(
	k^5 \delta(\bm{\hat{r}} - \bm{\hat{r}'}) \mathcal{I}
	\cr
	&+ \Big( \mathcal{I} + \hat{\nabla} \otimes \hat{\nabla} \Big) \int\_{V}
	\hat{g}( \bm{\hat{r}} - \bm{\hat{r}''}) \mathcal{T} (\bm{r''}, \bm{r'}, k) d \hat{V}'' \bigg),
\end{aligned}
$$

from which it immediately follows that defining

$$ \tag{15.29}
	\mathcal{\hat{T}} (\bm{\hat{r}}, \bm{\hat{r}'}) = \frac{1}{k^5}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
$$

makes Equation 15.28 fully dimensionless. Additionally, in the operator notation, Equations 15.20 and 15.28 are formally identical. As a result, both formulations can be written as the Born series of Equation 14.20, and thus produce the same fields. This completes the proof of the scale invariance property of electromagnetic scattering.

(Consider Stratton's proof, p. 488)


### Optical Cross-Section Theorem

For radiative transfer applications, we may introduce a simpler description of the scattering and absorption properties of a particle by describing the outcome of the scattering process in radometric (rather than optical) terms \[[6](#references) (ch. 13.6), [8](#references) (ch. 13.1, 13.4)\].

Consider the incident time-harmonic[^15] electromagnetic field \\(\bm{E_i}\\) and \\(\bm{B_i}\\). In a linear, isotropic, homogeneous, source-free medium, the field can be decomposed into individual plane waves (cf. Equations 7.1-7.2) of the form

[^15]: We used the the monochromatic forms of the fields given by Equation 4.11. Extension to a polychromatic field is trivial, and amounts to summing up the individual harmonic terms per Equation 4.8.

$$ \tag{16.1}
\begin{aligned}
	& \bm{E_i}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{E_0}(\bm{n_i}) e^{i k (\bm{r} \cdot \bm{n_i}) - i \omega t} \big\rbrace, \cr
	& \bm{B_i}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{B_0}(\bm{n_i}) e^{i k (\bm{r} \cdot \bm{n_i}) - i \omega t} \big\rbrace,
\end{aligned}
$$

where \\(\bm{E_0}\\) and \\(\bm{B_0}\\) are the field phasors at the origin, and \\(\bm{n_i}\\) is the direction of propagation.

According to Equations 7.18, 7.23 and 7.25, if the medium is non-absorptive, they satisfy the relations

$$ \tag{16.2}
	\bm{n_i} \cdot  \bm{E_0} = 0, \quad
	\bm{n_i} \cdot  \bm{B_0} = 0, \quad
	\bm{n_i} \times \bm{E_0} = \frac{c}{\eta} \bm{B_0}, \quad
	\bm{B_0} \times \bm{n_i} = \frac{\eta}{c} \bm{E_0}.
$$

With each plane wave, we may associate the Poynting vector \\(\bm{S_i}\\) (cf. Equation 7.28) pointing along the direction of propagation. If the medium is non-absorptive and non-magnetic, the expression of the time-averaged Poynting vector (cf. Equations 7.31-7.32) is particularly simple:

$$ \tag{16.3}
	\braket{\bm{S_i}}
	= \frac{1}{2} \mu\_0^{-1} \mathcal{Re}
	\big\lbrace \bm{E_0} \times \bm{B_0^{\*}} \big\rbrace
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} |\bm{E_0}|^2 \bm{n_i}.
$$

As a result, the amount of power per unit area (cf. Equation 7.32) flowing through an arbitrary virtual surface element is

$$ \tag{16.4}
	\Epsilon_i
	= \braket{\bm{S_i}} \cdot \bm{n}
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} |\bm{E_0}|^2 (\bm{n_i} \cdot \bm{n}),
$$

where \\(\bm{n}\\) is the surface normal.

The total amount of power flowing through the entire virtual surface is then

$$ \tag{16.5}
	\Delta \Phi
	= \int_{A} \braket{\bm{S_i}} \cdot \bm{n} \thinspace dA
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} |\bm{E_0}|^2 \int_{A} \bm{n_i} \cdot \bm{n} \thinspace dA.
$$

The integral given above simply computes the signed projected area of the surface onto the plane of the incident wave. For a closed surface, the value of the integral is zero.

[Insert picture here]

Equation 16.5 expresses the energy conservation law:

$$ \tag{16.6}
	\Delta \Phi
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} |\bm{E_0}|^2 \bigg(
	  \oint_{A} \mathrm{max}(0, -\bm{n} \cdot \bm{n_i}) dA
	- \oint_{A} \mathrm{max}(0,  \bm{n} \cdot \bm{n_i}) dA \bigg) = 0.
$$

It shows that the rate at which energy enters the volume (bounded by the virtual surface) is perfectly balanced by the rate of energy leaving the volume.

Clearly, under more general conditions, the equality

$$ \tag{16.7}
	\Delta \Phi = 0
$$

does not hold. If the medium is absorptive,

$$ \tag{16.8}
	\Delta \Phi < 0.
$$

Otherwise, if sources are present,

$$ \tag{16.9}
	\Delta \Phi > 0.
$$

Our goal is to find a similar expression for the case when the volume contains particles.

We begin by constructing a virtual surface that is both centered at the particle and is sufficiently large to be located in its far zone. The scattered fields are then given by Equation 15.6:

$$ \tag{16.10}
\begin{aligned}
	& \bm{E_s}(\bm{r}, t)
	\approx \mathcal{Re} \bigg\lbrace \frac{e^{i k r}}{r} \mathcal{S_{ef}} \bm{E_0} e^{-i \omega t} \bigg\rbrace
	= \frac{1}{r} \mathcal{Re} \big\lbrace \bm{E_1}(\bm{n_s}) e^{i k r - i \omega t} \big\rbrace, \cr
	& \bm{B_s}(\bm{r}, t)
	\approx \mathcal{Re} \bigg\lbrace \frac{e^{i k r}}{r} \mathcal{S_{mf}} \bm{E_0} e^{-i \omega t} \bigg\rbrace
	= \frac{1}{r} \mathcal{Re} \big\lbrace \bm{B_1}(\bm{n_s}) e^{i k r - i \omega t} \big\rbrace,
\end{aligned}
$$

where \\(\bm{E_1}\\) and \\(\bm{B_1}\\) are the *polarization phasors* defined at the unit distance from the origin.

Consider a fixed direction of observation \\(\bm{n_s} = \bm{r}/r\\). According to Equations 13.14-13.17 and 13.21, if the host medium is non-absorptive, the following relations exist:

$$ \tag{16.11}
	\bm{n_s} \cdot  \bm{E_1} = 0, \quad
	\bm{n_s} \cdot  \bm{B_1} = 0, \quad
	\bm{n_s} \times \bm{E_1} = \frac{c}{\eta} \bm{B_1}, \quad
	\bm{B_1} \times \bm{n_s} = \frac{\eta}{c} \bm{E_1}.
$$

The Poynting vector of the scattered far field points radially outward. If the host medium is is non-absorptive and non-magnetic, the corresponding time-averaged Poynting vector (cf. Equations 13.21-13.23) is

$$ \tag{16.12}
	\braket{\bm{S_s}}
	= \frac{1}{2} \mu\_0^{-1} \frac{1}{r^2} \mathcal{Re}
	\big\lbrace \bm{E_1} \times \bm{B_1^{\*}} \big\rbrace
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} \frac{\big\vert \bm{E_1}(\bm{n_s}) \big\vert^2}{r^2} \bm{n_s}.
$$

In order to calculate the amount of scattered power, it is convenient to use a spherical surface \\(\mathbb{S}^2\\). Since the differential solid angle is

$$ \tag{16.13}
	d\Omega = \frac{\bm{n} \cdot \bm{n_s}}{r^2} dA
$$

setting \\(\bm{n} = \bm{r}/r = \bm{n_s}\\) results in an integral that is formally independent of the radius of the sphere:

$$ \tag{16.14}
	\Phi_s
	= \oint_{A} \braket{\bm{S_s}} \cdot \bm{n_s} \thinspace dA
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} \oint\_{\mathbb{S}^2} \big\vert \bm{E_1}(\bm{n_s}) \big\vert^2 d\Omega.
$$

From the radiometric point of view, the squared magnitude of the scattered field corresponds to [radiant intensity](https://en.wikipedia.org/wiki/Radiant_intensity)

$$ \tag{16.15}
	\Iota_s
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} \big\vert \bm{E_1}(\bm{n_s}) \big\vert^2.
$$

The scattered field does not exist in isolation. Since it is generated by the incident field, the total field is formed by the superposition of the two waves (cf. Equation 11.12):

$$ \tag{16.16}
\begin{aligned}
	& \bm{E}(\bm{r}, t) =
	\bm{E_i}(\bm{r}, t) +
	\bm{E_s}(\bm{r}, t), \cr
	& \bm{B}(\bm{r}, t) =
	\bm{B_i}(\bm{r}, t) +
	\bm{B_s}(\bm{r}, t).
\end{aligned}
$$

The Poynting vector of the combined wave is then given by the cross product (cf. Equation 2.17)

$$ \tag{16.17}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \cr
	&= \mu\_0^{-1} \big( \bm{E_i} \times \bm{B_i} + \bm{E_s} \times \bm{B_s} + \bm{E_i} \times \bm{B_s} + \bm{E_s} \times \bm{B_i} \big) \cr
	&= \bm{S_i} + \bm{S_s} - \bm{S_e}.
\end{aligned}
$$

The \\(\bm{S_e}\\) term shows that the incident and the scattered waves interfere. In order to determine the extent it influences the flow of energy, we must once again integrate the time-averaged Poynting vector over a closed surface:

$$ \tag{16.18}
	\Delta \Phi
	= \oint_{A} \braket{\bm{S}} \cdot \bm{n} \thinspace dA
	= \oint_{A} \Big( \negthinspace \braket{\bm{S_i}} + \braket{\bm{S_s}} - \braket{\bm{S_e}} \negthinspace \Big) \cdot \bm{n} \thinspace dA.
$$

Since the formula is the same, the conclusions of Equations 16.7-16.9 remain valid. In addition, Equations 16.5-16.6 show that \\(\oint \braket{\bm{S_i}} \cdot \bm{n} \thinspace dA = 0\\). Thus, in the absence of primary sources within the region, Equations 16.8 and 16.18 tell us that

$$ \tag{16.19}
	-\Delta \Phi
	= \Phi_a
	= \oint_{A} \braket{\bm{S_e}} \cdot \bm{n} \thinspace dA - \Phi_s
	= \Phi_e - \Phi_s
$$

is the amount of power absorbed by the particle, where

$$ \tag{16.20}
	\braket{\bm{S_e}}
	= -\mu\_0^{-1} \braket{\bm{E_i} \times \bm{B_s} + \bm{E_s} \times \bm{B_i}}.
$$

In the context of Equation 16.19,

$$ \tag{16.21}
	\Phi_e
	= \Phi_a + \Phi_s
$$

represents the total amount of power dissipated (absorbed or scattered) by the particle.

Now, recall (cf. Equations 4.8, 4.15) that we have found, under quite general conditions, that the time average of the cross product of two vectors oscillating at exactly the same frequency is

$$ \tag{16.22}
	\Big\langle
		\mathcal{Re} \big\lbrace \bm{V} e^{i \omega t} \big\rbrace
		\times
		\mathcal{Re} \big\lbrace \bm{W} e^{i \omega t} \big\rbrace
	\Big\rangle
	= \frac{1}{2} \mathcal{Re} \big\lbrace \bm{V} \times \bm{W}^{\*} \big\rbrace.
$$

Thus, the time average of the mixed Poynting vector can be written as

$$ \tag{16.23}
	\braket{\bm{S_e}} =
	-\frac{\mu_0^{-1}}{2 r} \mathcal{Re} \bigg\lbrace
	\Big( \bm{E_0} e^{i k (\bm{r} \cdot \bm{n_i})} \Big) \times \Big[ \bm{B_1} e^{i k r} \Big]^{\*} +
	\Big( \bm{E_1} e^{i k r} \Big) \times \Big[ \bm{B_0} e^{i k (\bm{r} \cdot \bm{n_i})} \Big]^{\*}
	\bigg\rbrace.
$$

The next step is to project \\(\braket{\bm{S_e}}\\) onto the normal of the virtual sphere. The statement of the problem leads to two relations:

$$ \tag{16.24}
	\bm{n} = \bm{n_s},
	\qquad
	\bm{r} = r \bm{n_s}.
$$

This task can be carried out most efficiently with the help of the following [triple product](https://en.wikipedia.org/wiki/Triple_product) identities:

$$ \tag{16.25}
\begin{aligned}
&	\bm{a} \cdot (\bm{b} \times \bm{c}) =
	\bm{b} \cdot (\bm{c} \times \bm{a}) =
	\bm{c} \cdot (\bm{a} \times \bm{b}),
	\cr
&	\bm{a} \times (\bm{b} \times \bm{c}) =
	\bm{b} (\bm{a} \cdot \bm{c}) -
	\bm{c} (\bm{a} \cdot \bm{b}),
\end{aligned}
$$

which are valid for both real and complex vectors.

By combining Equations 16.2, 16.11, 16.24 and 16.25, all magnetic phasor factors can be eliminated:

$$ \tag{16.26}
\begin{aligned}
	\bm{n_s} \cdot \braket{\bm{E_i} \times \bm{B_s}}
	&= \frac{1}{2 r} \mathcal{Re} \Big\lbrace
	\bm{n_s} \cdot \big( \bm{E_0} \times \bm{B_1^{\*}} \big) e^{-i k r} e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 r} \mathcal{Re} \Big\lbrace
	\bm{E_0} \cdot \big( \bm{B_1^{\*}} \times \bm{n_s} \big) e^{-i k r} e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 r} \frac{\eta}{c} \mathcal{Re} \Big\lbrace
	\big( \bm{E_0} \cdot \bm{E_1^{\*}} \big) e^{-i k r} e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace,
	\cr
	\braket{\bm{E_i} \times \bm{B_s}}
	&= \frac{1}{2 r} \mathcal{Re} \Big\lbrace
	\big( \bm{E_1} \times \bm{B_0^{\*}} \big) e^{i k r} e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 r} \frac{\eta}{c} \mathcal{Re} \Big\lbrace
	\big( \bm{E_1} \times \bm{n_i} \times \bm{E_0^{\*}} \big) e^{i k r} e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 r} \frac{\eta}{c} \mathcal{Re} \Big\lbrace
	\Big(
	\bm{n_i} \big( \bm{E_0^{\*} \cdot \bm{E_1}} \big) -
	\bm{E_0^{\*}} \big( \bm{n_i} \cdot \bm{E_1} \big)
	\Big)
	e^{i k r} e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace.
\end{aligned}
$$

Based on Equation 16.26, it is convenient to define two functions independent of \\(k\\) and \\(r\\):

$$ \tag{16.27}
\begin{aligned}
	f_1(\bm{n})
	&= \bm{E_0} \cdot \big[ \bm{E_1}(\bm{n}) \big]^{\*},
	\cr
	f_2(\bm{n})
	&= \big( \bm{n} \cdot \bm{n_i} \big) \big( \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n}) \big)
	 - \big( \bm{n} \cdot \bm{E_0^{\*}} \big) \big( \bm{n_i} \cdot \bm{E_1}(\bm{n}) \big).
\end{aligned}
$$

Let us now integrate the normal component of \\(\braket{\bm{S_e}}\\) over a large sphere:

$$ \tag{16.28}
	\Phi_e
	= \oint_{\mathbb{S}^2} \braket{\bm{S_e}} \cdot \bm{n_s} \thinspace dA
	= -\mu\_0^{-1} \oint_{\mathbb{S}^2} \braket{\bm{E_i} \times \bm{B_s} + \bm{E_s} \times \bm{B_i}} \cdot \bm{n_s} \thinspace dA.
$$

Splitting Equation 16.23 into two parts are before, and omitting the leading constant terms, we obtain

$$ \tag{16.29}
\begin{aligned}
	\oint\_{\mathbb{S}^2} \braket{\bm{E_i} \times \bm{B_s}} \cdot \bm{n_s} \thinspace dA
	&= \frac{1}{2} \frac{\eta}{c} \mathcal{Re} \Big\lbrace e^{-i k r} \frac{1}{r}
	\oint\_{\mathbb{S}^2} f_1(\bm{n_s}) e^{-i k r (-\bm{n_i} \cdot \bm{n_s})} dA
	\Big\rbrace,
	\cr
	\oint\_{\mathbb{S}^2} \braket{\bm{E_i} \times \bm{B_s}} \cdot \bm{n_s} \thinspace dA
	&= \frac{1}{2} \frac{\eta}{c} \mathcal{Re} \Big\lbrace e^{i k r} \frac{1}{r}
	\oint\_{\mathbb{S}^2} f_2(\bm{n_s}) e^{-i k r (\bm{n_i} \cdot \bm{n_s})} dA
	\Big\rbrace.
\end{aligned}
$$

The two integrals found in Equation 16.29 have the same form. That allows us to apply Jones' lemma \[[6](#references) (ap. XII)\], which says that, for \\(k r \to \infty\\), the [asymptotic expansion](https://en.wikipedia.org/wiki/Asymptotic_expansion) of the surface integral over a sphere of radius \\(r\\) is

$$ \tag{16.30}
	\frac{1}{r} \oint\_{\mathbb{S}^2} f(\bm{n}) e^{-i k r (\bm{u} \cdot \bm{n})} dA
	\approx \frac{2 \pi i}{k} \Big( f(\bm{u}) e^{-i k r} - f(-\bm{u}) e^{i k r} \Big),
$$

where \\(\bm{n}\\) is the outward facing normal vector, and \\(\bm{u}\\) is a constant real unit vector.

Application of Equation 16.30 to 16.29 shows that

$$ \tag{16.31}
\begin{aligned}
	\oint\_{\mathbb{S}^2} \braket{\bm{E_i} \times \bm{B_s}} \cdot \bm{n_s} \thinspace dA
	&\approx \frac{\eta}{c} \frac{2 \pi}{k} \mathcal{Re} \bigg\lbrace \frac{i}{2}
	\Big( \bm{E_0} \cdot \big[ \bm{E_1}(-\bm{n_i}) \big]^{\*} e^{-2 i k r} - \bm{E_0} \cdot \big[ \bm{E_1}(\bm{n_i}) \big]^{\*} \Big)
	\bigg\rbrace,
	\cr
	\oint\_{\mathbb{S}^2} \braket{\bm{E_i} \times \bm{B_s}} \cdot \bm{n_s} \thinspace dA
	&\approx \frac{\eta}{c} \frac{2 \pi}{k} \mathcal{Re} \bigg\lbrace \frac{i}{2}
	\Big( \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) + \bm{E_0^{\*}} \cdot \bm{E_1}(-\bm{n_i}) e^{2 i k r} \Big)
	\bigg\rbrace.
\end{aligned}
$$

Notice that

$$ \tag{16.32}
	\frac{i}{2} \Big( \bm{E_0} \cdot \bm{E_1^{\*}} e^{-2 i k r} + \bm{E_0^{\*}} \cdot \bm{E_1} e^{2 i k r} \Big)
	= i \mathcal{Re} \Big\lbrace \bm{E_0} \cdot \bm{E_1^{\*}} e^{-2 i k r} \Big\rbrace
$$

is a purely imaginary number, so its real part is 0. Similarly,

$$ \tag{16.33}
	\frac{i}{2} \big( \bm{E_0^{\*}} \cdot \bm{E_1} - \bm{E_0} \cdot \bm{E_1^{\*}} \big)
	= -\mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1} \big\rbrace
$$

is a real number. Thus, the total amount of power dissipated by the particle

$$ \tag{16.34}
	\Phi_e
	= -\mu\_0^{-1} \oint_{\mathbb{S}^2} \braket{\bm{E_i} \times \bm{B_s} + \bm{E_s} \times \bm{B_i}} \cdot \bm{n_s} \thinspace dA
	\approx \mu\_0^{-1} \frac{\eta}{c} \frac{2 \pi}{k} \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace
$$

depends on the scattering amplitude in the forward direction only. In general, the same is not true for the amount of power absorbed:

$$ \tag{16.35}
	\Phi_a
	= \Phi_e - \Phi_s
	\approx \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} \bigg(
	\frac{4 \pi}{k} \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace
	- \oint\_{\mathbb{S}^2} \big\vert \bm{E_1}(\bm{n_s}) \big\vert^2 d\Omega \bigg).
$$

The amount of power dissipated by the particle obviously depends on the amount of incident power the particle intercepts. The latter can be characterized with the help of the projected area of the particle - its *geometric cross-section* \\(C_g\\). For a convex particle, it is given by the integral

$$ \tag{16.36}
	C_g
	= \oint_{A} \mathrm{max}(0, -\bm{n} \cdot \bm{n_i}) dA.
$$

The amount of incident power is then (cf. Equations 16.3-16.5)

$$ \tag{16.37}
	\Phi_i
	= \frac{1}{2} \mu\_0^{-1} \frac{\eta}{c} |\bm{E_0}|^2
	  \oint_{A} \mathrm{max}(0, -\bm{n} \cdot \bm{n_i}) dA
	= \Epsilon_i(\bm{n_i}) C_g
	= \vert \negthinspace \braket{\bm{S_i}} \negthinspace \vert C_g.
$$

This allows us to introduce dimensionless absorption, scattering, and extinction *efficiencies*

$$ \tag{16.38}
\begin{aligned}
	& Q_a
	= \frac{\Phi_a}{\Phi_i}
	= \frac{\Phi_s + \Phi_e}{\Phi_i},
	\cr
	& Q_s
	= \frac{\Phi_s}{\Phi_i}
	= \frac{\oint |\bm{E_1}(\bm{n_s})|^2 d\Omega}{|\bm{E_0}|^2 C_g},
	\cr
	& Q_e
	= \frac{\Phi_e}{\Phi_i}
	= \frac{4 \pi}{k} \frac{\mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace}{|\bm{E_0}|^2 C_g}.
\end{aligned}
$$

If normalization with respect to the projected area is not required, we may utilize the absorption, scattering, and extinction *cross-sections* instead:

$$ \tag{16.39}
\begin{aligned}
	& C_a
	= \frac{\Phi_a}{\Phi_i}
	= Q_a C_g
	= \frac{\Phi_s + \Phi_e}{\vert \negthinspace \braket{\bm{S_i}} \negthinspace \vert},
	\cr
	& C_s
	= \frac{\Phi_s}{\vert \negthinspace \braket{\bm{S_i}} \negthinspace \vert}
	= Q_s C_g
	= \frac{\oint |\bm{E_1}(\bm{n_s})|^2 d\Omega}{|\bm{E_0}|^2},
	\cr
	& C_e
	= \frac{\Phi_e}{\vert \negthinspace \braket{\bm{S_i}} \negthinspace \vert}
	= Q_e C_g
	= \frac{4 \pi}{k} \frac{\mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace}{|\bm{E_0}|^2}.
\end{aligned}
$$

Equations 16.39.1-16.39.3 are known as the *optical cross-section theorem*, or the [optical theorem](https://en.wikipedia.org/wiki/Optical_theorem) for short. In particular, Equation 16.39.3 is known as the *extinction theorem* \[[4](#references) (ch. 4.2)\].

The equations of the extinction theorem can be simplified if the particle possesses axial symmetry with respect to the direction of incidence.

To show this, express \\(\bm{E_1}\\) in terms of the scattering tensor \\(\mathcal{S_{ef}}\\) according to Equation 16.10:

$$ \tag{16.40}
	\Phi_e
	\approx \mu\_0^{-1} \frac{\eta}{c} \frac{2 \pi}{k} \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \mathcal{S_{ef}}(\bm{n_i}, \bm{n_i}) \bm{E_0} \big\rbrace,
$$

or, alternatively, in terms of the scattering matrix \\(S\\), by setting \\(\theta=0\\) in Equation 15.17:

$$ \tag{16.41}
	\Phi_e
	\approx \mu\_0^{-1} \frac{\eta}{c} \frac{2 \pi}{k^2} \mathcal{Re} \big\lbrace \bm{E_0^{\*}} \cdot S(0, \phi, \omega) \bm{E_0} \big\rbrace.
$$

Now, for a symmetric particle, we have previously demonstrated that

$$ \tag{16.42}
	s_1(0, \phi) = s_2(0, \phi) = s_0,
	\qquad
	s_3 = s_4 = 0.
$$

Thus, we obtain the expressions

$$ \tag{16.43}
\begin{aligned}
	& Q_e
	= \frac{4 \pi}{k^2} \frac{\mathcal{Re} \big\lbrace s_0 (\bm{E_0^{\*}} \cdot \bm{E_0}) \big\rbrace}{|\bm{E_0}|^2 C_g}
	= \frac{4 \pi}{k^2} \frac{\mathcal{Re} \lbrace s_0 \rbrace}{C_g},
	\cr
	& C_e
	= Q_e C_g
	= 4 \pi k^{-2} \mathcal{Re} \lbrace s_0 \rbrace,
\end{aligned}
$$

which matches the results found using the scalar wave theory \[[4](#references) (ch. 4.21, 4.42)\]. Intuitively, this makes sense, since the particle looks the same regardless of the choice of the plane of reference (assuming the latter contains the direction of incidence), and the sensor (that only measures power) is not sensitive to the orientation of incoming light.

### Lorenz-Mie-Debye Theory

The Lorenz-Mie-Debye theory presents a rigorous solution to the problem of [diffraction](https://en.wikipedia.org/wiki/Diffraction) by an isotropic conducting sphere, also known as [Mie scattering](https://en.wikipedia.org/wiki/Mie_scattering). The solution is exact in the sense that it does not employ any approximations, so it exhibits all classical and semi-classical (e.i. wave-mechanical) effects, and thus shows an excellent agreement with experimental data (see \[[16](#references) (ch. 5)\] for a list of references).

The original theory was independently formulated by Lorenz (1890), Mie (1908), and Debye (1909). The derivation of the relevant formulas can be found in many popular textbooks, such as \[[4](#references) (ch. 9), [6](#references) (ch. 14.5)\]. A more mathematically elegant treatment of the problem was given by Hansen (1935, 1936, 1937). It has also been presented in a recent open access paper \[[15](#references)\], which we use as the foundation of this section.

The general idea behind the solution is fairly simple. We treat electromagnetic scattering as a boundary value problem. If we expand the expression of the electromagnetic field in series of vector spherical harmonics, the boundary conditions take a particularly simple form, which can be harnessed to find the (initially unknown) coefficients of the series expansion.

Consider the Maxwell equations in a linear, isotropic, homogeneous region of space given by Equation 6.3. We have seen that, after a number of transformations, they are reduced to a *vector* Helmholtz equation

$$ \tag{17.1}
	\big( \nabla^2 + k^2(\omega) \big) \bm{E}(\bm{r}, \omega) = 0.
$$

We have previously solved this equation by explicitly writing it in Cartesian coordinates. That allowed us to separate a single vector equation into three scalar equations, which could then be solved individually. While this leads to a convenient (plane wave) representation of the incident field, it is a poor fit for the scattered field, since the latter takes the form of a spherical wave in the far zone. In addition, the geometry of the scatterer suggests that we should exploit the spherical symmetry of the problem in some way.

Equation 17.1 can be written in yet another way. Recall (c.f. Equation 9.19) that we can express the electric field \\(\bm{E}\\) in terms of the vector potential \\(\bm{A}\\) and the scalar potential \\(\phi\\)

$$ \tag{17.2}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

Note that we are free to choose the value of \\(\nabla \cdot \bm{A}\\) to suit our particular problem.

Let us find the formulation in terms of the electromagnetic potential by substituting Equation 17.2 and 5.2.3 into the Maxwell equations 6.2.3-6.2.4:

$$ \tag{17.3}
\begin{aligned}
	& \nabla \times \nabla \times \bm{A}(\bm{r}, \omega)
	+ i \omega \mu(\omega) \varepsilon(\omega) \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0,
	\cr
	& \nabla \cdot \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0.
\end{aligned}
$$

After simplification, using the definition of the Laplace operator, and applying the curl of curl identity given by Equation 6.6, we obtain

$$ \tag{17.4}
\begin{aligned}
	& \nabla \big( \nabla \cdot \bm{A}(\bm{r}, \omega) \big) - \nabla^2 \bm{A}(\bm{r}, \omega)
	+ i \omega \mu(\omega) \varepsilon(\omega) \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0,
	\cr
	& \nabla \cdot \bm{A}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla^2 \phi(\bm{r}, \omega).
\end{aligned}
$$

These two equations may be combined, and the Laplace operator and the gradient may be [interchanged](https://en.wikipedia.org/wiki/Vector_calculus_identities#Third_derivatives):

$$ \tag{17.5}
\begin{aligned}
	& \nabla^2 \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big)
	+ \omega^2 \mu(\omega) \varepsilon(\omega) \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0,
	\cr
	& \nabla \cdot \bm{A}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla^2 \phi(\bm{r}, \omega).
\end{aligned}
$$

Since the value of \\(\nabla \cdot \bm{A}\\) is arbitrary, we may take inspiration from the free-space condition of Equation 9.20, and assume that

$$ \tag{17.6}
	\nabla \cdot \bm{A}(\bm{r}, \omega) = i \omega \mu(\omega) \varepsilon(\omega) \phi(\bm{r}, \omega).
$$

From Equation 9.21, it immediately follows that

$$ \tag{17.7}
	\frac{1}{i \omega} \nabla^2 \phi(\bm{r}, \omega)
	= i \omega \mu(\omega) \varepsilon(\omega) \phi(\bm{r}, \omega),
$$

which implies that \\(\phi\\) satisfies the scalar Helmholtz equation

$$ \tag{17.8}
	\big( \nabla^2 + k^2(\omega) \big) \phi(\bm{r}, \omega) = 0,
$$

where \\(k\\) is the complex wavenumber:

$$ \tag{17.9}
	k(\omega) = \omega \sqrt{\varepsilon(\omega) \mu(\omega)}.
$$

Similarly, substitution into Equation 17.5.1 shows that \\(\bm{A}\\) satisfies the vector Helmholtz equation

$$ \tag{17.10}
	\big( \nabla^2 + k^2(\omega) \big) \bm{A}(\bm{r}, \omega) = 0.
$$

Since the Helmholtz equation is both homogeneous and linear, it has multiple special solutions that can be combined into a single general solution according to the superposition principle:

$$ \tag{17.11}
	\phi(\bm{r}, \omega) = -\sum_n c_n(\omega) \psi_n(\bm{r}, \omega),
$$

with each special solution \\(\psi_n\\) satisfying the same equation

$$ \tag{17.12}
	\big( \nabla^2 + k^2(\omega) \big) \psi_n(\bm{r}, \omega) = 0.
$$

Similarly, in 3-dimensional space, the vector potential \\(\bm{A_n}\\) can be represented by a sum of 3 linearly independent vectors \\(\bm{L_n}, \bm{M_n}, \bm{N_n}\\):

$$ \tag{17.13}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \frac{i}{\omega} \sum_n \bm{A_n}(\bm{r}, \omega)
	\cr
	&= \frac{i}{\omega} \sum_n \big( a_n(\omega) \bm{M_n}(\bm{r}, \omega) + b_n(\omega) \bm{N_n}(\bm{r}, \omega) + c_n(\omega) \bm{L_n}(\bm{r}, \omega) \big).
\end{aligned}
$$

How should we define these 3 vectors? According to the Helmholtz decomposition (Equation 8.8), any sufficiently smooth vector field \\(\bm{A}\\) can be represented by a sum

$$ \tag{17.14}
\begin{aligned}
	&\bm{A} = \bm{A'} + \bm{A''}, &
	&\nabla \cdot  \bm{A' } = 0, &
	&\nabla \times \bm{A''} = 0. &
\end{aligned}
$$

Let us choose \\(\bm{L_n}\\) as [irrotational](https://en.wikipedia.org/wiki/Conservative_vector_field#Irrotational_vector_fields), and \\(\bm{M_n}\\) and \\(\bm{N_n}\\) as [solenoidal](https://en.wikipedia.org/wiki/Solenoidal_vector_field):

$$ \tag{17.15}
	\bm{A_n'} = a_n \bm{M_n} + b_n \bm{N_n},
	\quad
	\bm{A_n''} = c_n \bm{L_n}.
$$

From Equations 17.5.2, 17.11, and 17.13, it follows that

$$ \tag{17.16}
	\nabla \cdot \bm{A_n} = \nabla \cdot (c_n \nabla \psi_n).
$$

In other words,

$$ \tag{17.17}
	\bm{L_n} = \nabla \psi_n,
	\quad
	\nabla \times \bm{L_n} = 0.
$$

Since divergence of curl is zero, Equations 17.14.2 and 17.15.1 suggests that both \\(\bm{M_n}\\) and \\(\bm{N_n}\\) should be a curl of something. In addition, both should depend on \\(\psi_n\\). Since \\(\psi_n\\) is a scalar, its curl is undefined; a simple way to fix that is to introduce an additional vector \\(\bm{a}\\):

$$ \tag{17.18}
	\bm{M_n} = \nabla \times (\psi_n \bm{a}),
	\quad
	\nabla \cdot \bm{M_n} = 0.
$$

Finally, the curl of a non-zero vector field is linearly independent from the source field. Thus, we may choose

$$ \tag{17.19}
	\bm{N_n} = \frac{1}{k} \nabla \times \bm{M_n},
	\quad
	\nabla \cdot \bm{N_n} = 0,
$$

where the \\(1/k\\) constant is chosen for further convenience.

The three series of vectors \\(\bm{L_n}, \bm{M_n}, \bm{N_n}\\) are called [vector spherical harmonics](https://en.wikipedia.org/wiki/Vector_spherical_harmonics). \\(\bm{a}\\) is called the *pilot vector*. It is convenient to assume that it belongs to an [irrotational](https://en.wikipedia.org/wiki/Conservative_vector_field#Irrotational_vector_fields) vector field, such that

$$ \tag{17.20}
	\nabla \times \bm{a} = 0.
$$

Coupled with the [product rule](https://en.wikipedia.org/wiki/Vector_calculus_identities#Product_rule_for_multiplication_by_a_scalar), Equation 17.20 leads to a simpler definition of \\(\bm{M_n}\\):

$$ \tag{17.21}
	\bm{M_n} = \psi (\nabla \times \bm{a}) + (\nabla \psi_n) \times \bm{a} = \bm{L_n} \times \bm{a}.
$$

If \\(\bm{a}\\) is a real vector, this leads to a simple geometrical interpretation in terms of orthogonal vectors:

$$ \tag{17.22}
	\bm{M_n} \cdot \bm{a} = 0,
	\quad \text{or} \quad
	\bm{L_n} \cdot \bm{M_n} = 0.
$$

Each of the three vectors, \\(\bm{L_n}\\), \\(\bm{M_n}\\), and \\(\bm{N_n}\\), can be used to form a vector Helmholtz equation. That is trivial to show this for \\(\bm{L_n}\\):

$$ \tag{17.23}
	\big( \nabla^2 + k^2 \big) \bm{L_n}
	= \big( \nabla^2 + k^2 \big) \nabla \psi_n
	= \nabla \big( \nabla^2 + k^2 \big) \psi_n = 0,
$$

For \\(\bm{M_n}\\), the corresponding expression is

$$ \tag{17.24}
	\big( \nabla^2 + k^2 \big) \bm{M_n}
	= \big( \nabla^2 + k^2 \big) \big( \nabla \times (\psi_n \bm{a}) \big)
	= \nabla \times \Big( \big( \nabla^2 + k^2 \big) \big( \psi_n \bm{a} \big) \Big).
$$

If \\(\bm{a}\\) is a *constant* vector, from Equation 17.12, it immediately follows that

$$ \tag{17.25}
	\big( \nabla^2 + k^2 \big) \bm{M_n}
	= \nabla \times \Big( \bm{a} \big( \nabla^2 + k^2 \big) \psi_n \Big) = 0.
$$

In general, for an arbitrary \\(\bm{a}\\), that is not the case. We may use the identity

$$ \tag{17.26}
	\nabla^2 (\psi_n \bm{a})
	= \bm{a} \nabla^2 \psi_n
	+ 2 (\nabla \psi_n \cdot \nabla) \bm{a}
	+ \psi_n \nabla^2 \bm{a}
$$

to expand Equation 17.24:

$$ \tag{17.27}
	\big( \nabla^2 + k^2 \big) \bm{M_n}
	= \nabla \times \big( \bm{a} \big( \nabla^2 + k^2 \big) \psi_n
	+ 2 (\nabla \psi_n \cdot \nabla) \bm{a}
	+ \psi_n \nabla^2 \bm{a} \big).
$$

Thus, \\(\bm{M_n}\\) satisfies a vector Helmholtz equation if

$$ \tag{17.28}
	\nabla \times \big(2 (\nabla \psi_n \cdot \nabla) \bm{a} + \psi_n \nabla^2 \bm{a} \big) = 0.
$$

If we make a simple, but non-obvious choice of \\(\bm{a} = \bm{r}\\), it is easy to show that

$$ \tag{17.29}
	(\nabla \psi_n \cdot \nabla) \bm{r}
	= \nabla \psi_n,
	\quad
	\nabla^2 \bm{r} = 0,
$$

which confirms that

$$ \tag{17.30}
	\big( \nabla^2 + k^2 \big) \bm{M_n}
	= \nabla \times \big( \bm{r} \big( \nabla^2 + k^2 \big) \psi_n
	+ \nabla \psi_n \big) = 0.
$$

Since \\(\bm{N_n}\\) is defined in terms of \\(\bm{M_n}\\), it directly follows that

$$ \tag{17.31}
	\big( \nabla^2 + k^2 \big) \bm{N_n}
	= \frac{1}{k} \nabla \times \Big( \big( \nabla^2 + k^2 \big) \bm{M_n} \Big) = 0.
$$

Now, consider what happens when we take the curl of \\(\bm{N_n}\\):

$$ \tag{17.32}
	\frac{1}{k} \nabla \times \bm{N_n}
	= \frac{1}{k^2} \nabla \times \nabla \times \nabla \times (\psi_n \bm{a})
	= -\frac{1}{k^2} \nabla \times \big( \nabla^2(\psi_n \bm{a}) \big).
$$

Since, according to Equation 17.12,

$$ \tag{17.33}
	\nabla^2 \psi_n = -k^2 \psi_n,
$$

for *constant* \\(\bm{a}\\), Equation 17.32 leads to yet another definition of \\(\bm{M_n}\\):

$$ \tag{17.34}
	\bm{M_n}
	= \nabla \times (\psi_n \bm{a})
	= \frac{1}{k} \nabla \times \bm{N_n}.
$$

In order to show that it is also the case for \\(\bm{a} = \bm{r}\\), we must substitute Equations 17.26, 17.29, and 17.33 into 17.32:

$$ \tag{17.35}
\begin{aligned}
	\frac{1}{k} \nabla \times \bm{N_n}
	&= -\frac{1}{k^2} \nabla \times \big( \nabla^2(\psi_n \bm{a}) \big)
	\cr
	&= -\frac{1}{k^2} \nabla \times \big(
		\bm{a} \nabla^2 \psi_n
		+ 2 (\nabla \psi_n \cdot \nabla) \bm{a}
		+ \psi_n \nabla^2 \bm{a} \big)
	\cr
	&= -\frac{1}{k^2} \nabla \times \big(
		\bm{r} \nabla^2 \psi_n
		+ 2 \nabla \psi_n \big)
	\cr
	&= \nabla \times (\psi_n \bm{a}) = \bm{M_n}.
\end{aligned}
$$

Now that we have proved some basics properties of vector spherical harmonics (what about full orthogonality?), we can start applying them to simple problems.

Suppose that we have obtained the coefficients of the expansion of the electromagnetic potential in vector spherical harmonics. How can we find the associated electric and magnetic fields?

According to Equations 17.2.1, 17.11, 17.13, and 17.17,

$$ \tag{17.36}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega)
	\cr
	&= -\sum_n \big( \bm{A_n}(\bm{r}, \omega) - c_n(\omega) \nabla \psi_n(\bm{r}, \omega) \big)
	\cr
	&= -\sum_n \big( a_n(\omega) \bm{M_n}(\bm{r}, \omega) + b_n(\omega) \bm{N_n}(\bm{r}, \omega) \big).
\end{aligned}
$$

The result is a solenoidal phasor field, which is consistent with the Maxwell equations in linear, isotropic, homogeneous, source-free media.

Similarly, the magnetic field can be determined using Equations 17.2.2, 17.13, 17.17, 17.19, and 17.35:

$$ \tag{17.37}
\begin{aligned}
	\bm{B}(\bm{r}, \omega)
	&= \nabla \times \bm{A}(\bm{r}, \omega)
	\cr
	&= \frac{i}{\omega} \sum_n \Big( a_n(\omega) \big( \nabla \times \bm{M_n}(\bm{r}, \omega) \big) + b_n(\omega) \big( \nabla \times \bm{N_n}(\bm{r}, \omega) \big) \Big)
	\cr
	&= - \frac{k}{i \omega} \sum_n \big( a_n(\omega) \bm{N_n}(\bm{r}, \omega) + b_n(\omega) \bm{M_n}(\bm{r}, \omega) \big).
\end{aligned}
$$

The resulting field is irrotational. Note that the relative order of the vectors in Equations 17.36 and 17.37 is reversed.

Expressions given above are valid for arbitrary electromagnetic fields. They have also been painstakingly crafted in a coordinate-independent manner. In practice, to obtain numerical results, once must choose a certain coordinate system. As it was hinted at the beginning of this section, the symmetry of the problem suggests that it is advantageous to approach the problem in spherical coordinates.

Consider again the scalar Helmholtz equation (17.8). In [spherical coordinates](https://en.wikipedia.org/wiki/Laplace_operator#Three_dimensions), it can be expressed as

$$ \tag{17.38}
	\frac{1}{r^2} \frac{\partial}{\partial r} \bigg( r^2 \frac{\partial f}{\partial r} \bigg)
	+ \frac{1}{r^2 \sin{\theta}} \frac{\partial}{\partial \theta} \bigg( \sin{\theta} \frac{\partial f}{\partial \theta} \bigg)
	+ \frac{1}{r^2 \sin^2{\theta}} \frac{\partial^2 f}{\partial \phi^2}
	+ k^2 f = 0.
$$

If we look for a solution of the form

$$ \tag{17.39}
	f(r, \theta, \phi) = f_r(r) f_{\theta}(\theta) f_{\phi}(\phi),
$$

substitution results in three separate ordinary differential equations:

$$ \tag{17.40}
\begin{aligned}
	& r^2 \frac{d^2 f_r}{d r^2}
	+ 2 r \frac{d f_r}{d r}
	+ \big( k^2 r^2 - p^2 \big) f_r = 0,
	\cr
	& \frac{1}{\sin{\theta}} \frac{d}{d \theta} \bigg( \sin{\theta} \frac{ d f_{\theta} }{d \theta} \bigg)
	+ \bigg( p^2 - \frac{q^2}{\sin^2{\theta}} \bigg) f_{\theta} = 0,
	\cr
	& \frac{d^2 f_{\phi}}{d \phi^2}
	+ q^2 f_{\phi} = 0,
\end{aligned}
$$

where \\(p\\) and \\(q\\) are separation constants that ensure the solutions are finite and single-valued.

Differential equations of this type are well known in the literature \[[Stratton](#references) (ch 7.3)\]. Without proof, their solutions are:

$$ \tag{17.41}
	f_r(r) = z_n(k r),
	\quad
	f_{\theta}(\theta) = P_n^m(\cos{\theta}),
	\quad
	f_{\phi}(\phi) = e^{i m \phi},
$$

and thus the general solution of Equation 17.38 is

$$ \tag{17.42}
	f(r, \theta, \phi)
	= \sum_{m,n} c_{mn} \psi_{mn}(r, \theta, \phi)
	= \sum_{m,n} c_{mn} z_n(k r) P_n^m(\cos{\theta}) e^{i m \phi},
$$

where \\(c_{mn}\\) is a complex constant, \\(z_n\\) is a [spherical Bessel function](https://en.wikipedia.org/wiki/Bessel_function#Spherical_Bessel_functions:_jn,_yn) of order \\(n\\), and \\(P_n^m\\) is an [associated Legendre polynomial](https://en.wikipedia.org/wiki/Associated_Legendre_polynomials) of degree \\(n\\) and order \\(m\\). Note that, as expected, the solution depends on the distance \\(r\\) only through the phase difference \\(k r.\\)

We shall first examine the radial functions \\(z_n(k r)\\). THey may be used to represent any of the four kinds of spherical Bessel functions \[[Stratton](#references) (ch 7.4)\]:

$$ \tag{17.43}
\begin{aligned}
	& j_n(x) = \sqrt{\frac{\pi}{2 x}} J_{n+1/2}(x), &
	& y_n(x) = \sqrt{\frac{\pi}{2 x}} Y_{n+1/2}(x),
	\cr
	& h_n^{(1)}(x) = \sqrt{\frac{\pi}{2 x}} H_{n+1/2}^{(1)}(x), &
	& h_n^{(2)}(x) = \sqrt{\frac{\pi}{2 x}} H_{n+1/2}^{(2)}(x),
\end{aligned}
$$

where \\(J_n\\) and \\(Y_n\\) are the Bessel functions of the [first](https://en.wikipedia.org/wiki/Bessel_function#Bessel_functions_of_the_first_kind:_J%CE%B1) and the [second](https://en.wikipedia.org/wiki/Bessel_function#Bessel_functions_of_the_second_kind:_Y%CE%B1) kind, respectively, while \\(H_n^{(1)}\\) and \\(H_n^{(1)}\\) are the [Hankel functions](https://en.wikipedia.org/wiki/Bessel_function#Hankel_functions:_H(1)%CE%B1,\_H(2)%CE%B1) of the first and the second kind,
which are related to the Bessel functions in a very simple manner:

$$ \tag{17.44}
	H_n^{(1)}(x) = J_n(x) + i Y_n(x),
	\quad
	H_n^{(2)}(x) = J_n(x) - i Y_n(x).
$$

\\(H_n^{(1)}\\) and \\(H_n^{(1)}\\) are also known as Bessel functions of the third and the fourth kind, respectively.

As expected from the general scattering theory, the behavior of these functions is rather different depending on whether the magnitude of the argument is very large or very small.

{{< figure src="/img/spherical_bessel_j.svg" caption="*Figure N: Spherical Bessel functions of the first kind of order n=0 (blue), n=1 (orange), n=2 (green), n=3 (red).*" >}}

{{< figure src="/img/spherical_bessel_y.svg" caption="*Figure N: Spherical Bessel functions of the second kind of order n=0 (blue), n=1 (orange), n=2 (green), n=3 (red).*" >}}

In particular, in the far zone, as \\(k r \to \infin\\), both functions behave as decaying sine waves, which can be clearly seen on the plot below.

{{< figure src="/img/spherical_hankel_1.svg" caption="*Figure N: Spherical Hankel functions of the first kind of order n=0 (blue), n=1 (orange), n=2 (green), n=3 (red). Solid lines correspond to the real part, while the imaginary part is drawn using dashed lines.*" >}}

In addition, observe that the real and the imaginary components (the Bessel functions of the first and the second kind, respectively) are out-of-phase by a constant factor. This leads to the asymptotic forms

$$ \tag{17.45}
\begin{aligned}
	& j_n(x) \approx \frac{1}{x} \cos\Big( x - \frac{n+1}{2} \pi \Big), &
	& y_n(x) \approx \frac{1}{x} \sin\Big( x - \frac{n+1}{2} \pi \Big),
	\cr
	& h_n^{(1)}(x) \approx \frac{1}{x} (-i)^{n+1} e^{i \rho}, &
	& h_n^{(2)}(x) \approx \frac{1}{x} (i)^{n+1} e^{+i \rho}.
\end{aligned}
$$

Let us now turn our attention to the angular component of the solution

$$ \tag{17.46}
	P_n^m(\cos{\theta}) e^{i m \phi}
	= P_n^m(\cos{\theta}) \cos(m \phi)
	+ i P_n^m(\cos{\theta}) \sin(m \phi).
$$

If we set \\(m=0\\), the dependence on the azimuthal angle \\(\phi\\) disappears, and we are left with the [Legendre polynomial](https://en.wikipedia.org/wiki/Legendre_polynomials) \\(P_n\\) plotted below.

{{< figure src="/img/legendre.svg" caption="*Figure N: Legendere polynomials of degree n=0 (blue), n=1 (orange), n=2 (green), n=3 (red).*" >}}

The degree of a polynomial is also the number of zeros of the function. Coupled with the fact that \\(P_n(\cos{\theta})\\) is independent of \\(\phi\\), we can observe that the Legendre polynomial of degree \\(n\\) divides the surface of a sphere into \\(n + 1\\) zones of alternating sign. For this reason, the family of functions \\(P_n(\cos{\theta})\\) is sometimes referred to as *zonal harmonics*.

The situation changes when \\(m \neq 0\\). As can be seen from the graph below, the value of the associated Legendre polynomial \\(P_n^m(\cos{\theta})\\) goes to zero at \\(|\cos{\theta}| = 1\\). In addition, the total number of zeros of the function is \\(n - m + 2\\).

{{< figure src="/img/assoc_legendre.svg" caption="*Figure N: Associated Legendere polynomials of degree 3 and order m=0 (blue), m=1 (orange), m=2 (green), m=3 (red).*" >}}

We are now ready to interpret Equation 17.46 in full. Since that function has harmonic dependence on \\(\phi\\), if we plot one of its parts (either real or imaginary), we can observe that it divides the surface of a sphere into \\(2 m \times (n - m + 1)\\) rectangular domains, or tesserae. Thus, \\(P_n^m(\cos{\theta}) \cos(m \phi)\\) and \\(P_n^m(\cos{\theta}) \sin(m \phi)\\) are called *tesseral harmonics*.

{{< figure src="/img/assoc_legendre_3.png" caption="*Figure N: Tesseral harmonics of degree 3 and order m=1 (left), m=2 (center), m=3 (right). Warm colors correspond to positive values of the function, and cold colors - to negative values.*" >}}

---

 Let us now consider a concrete example of a plane wave, which is often used to describe the incident radiation of a distant source.

The general plane wave form of the electric field is given by Equation 6.16:

$$ \tag{17.38}
	\bm{E}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega.
$$

If we only consider a single direction of incidence \\(\bm{n} = \bm{n_i}\\), the expression above reduces to

$$ \tag{17.39}
	\bm{E_i}(\bm{r}, \omega) =
	\bm{E_i}(0, \bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i})}.
$$

It is straightforward to verify that a suitable description in terms of the electromagnetic potential is

$$ \tag{17.40}
\begin{aligned}
	& \bm{A_i}(\bm{r}, \omega)
	= \bm{A_i}(0, \bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i})},
	\cr
	& \phi_i(\bm{r}, \omega)
	= \phi_i(0, \bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i})}.
\end{aligned}
$$

After substituting \\(\psi_n = \phi_i\\) in Equations 17.17-17.19, we obtain

$$ \tag{17.41}
	\bm{L_n} = i k \phi_i \bm{n_i},
	\quad
	\bm{M_n} = i k \phi_i \bm{n_i} \times \bm{a},
	\quad
	\bm{N_n} = \frac{1}{k} \nabla \times \bm{M_n} = ???.
$$

---

## III. Simple Formulas for Practical Use

[Rayleigh](https://en.wikipedia.org/wiki/Rayleigh_scattering) - [Tyndall](https://en.wikipedia.org/wiki/Tyndall_effect) - [Mie](https://en.wikipedia.org/wiki/Mie_scattering)

Inelastic [Raman scattering](https://en.wikipedia.org/wiki/Raman_scattering)

Field Magnitude <-> Wave Amplitude

Is a spherical cavity the right choice for the depolarization operator?

## Acknowledgments

Pharr & Jakob, Bohren & Huffman, Robin Green, Larry Travis @ NASA.

## References

<!-- Modified APA style -->

1. Golubev, E. [Sampling Analytic Participating Media](/post/analytic-media/) (2020).
2. Chandrasekhar, S. [Radiative Transfer](https://doi.org/10.1002/qj.49707633016) (1950).
3. Golubev, E. [From Particle to Volume Scattering of Light](/post/particle-volume/) (2020).
4. van de Hulst, H. C. [Light Scattering by Small Particles](https://doi.org/10.1002/qj.49708436025) (1957).
5. Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman Lectures on Physics](https://www.feynmanlectures.caltech.edu/) (1965).
6. Born, M., & Wolf, E. [Principles of Optics](https://doi.org/10.1017/CBO9781139644181) (7th ed., 1999).
7. Van Bladel, J. G. [Electromagnetic Fields](https://doi.org/10.1002/047012458X) (2nd ed., 2007).
8. Mishchenko, M. I. [Electromagnetic Scattering by Particles and Particle Groups](https://doi.org/10.1017/CBO9781139019064) (2014).
9. Mishchenko, M. I., Travis, L. D., & Lacis, A. A. [Multiple Scattering of Light by Particles](https://www.cambridge.org/us/academic/subjects/earth-and-environmental-science/atmospheric-science-and-meteorology/multiple-scattering-light-particles-radiative-transfer-and-coherent-backscattering?format=HB&isbn=9780521834902) (2006).
10. Pauli, W. [Pauli Lectures on Physics](https://www.worldcat.org/title/pauli-lectures-on-physics-vol-1-electrodynamics/oclc/439349741) (1973).
11. Kong, J. A. [Electromagnetic Wave Theory](https://www.worldcat.org/title/electromagnetic-wave-theory/oclc/248547001) (2008).
12. Tsang, L., & Kong, J. A. [Scattering of Electromagnetic Waves: Advanced Topics](https://doi.org/10.1002/0471224278) (2001).
13. Fleisch, D. [Student's Guide to Vectors and Tensors](https://doi.org/10.1017/CBO9781139031035) (2011).
14. Talalai, G. [Derivation of the Free-Space Green’s Function](https://www.researchgate.net/publication/324729639_Derivation_of_the_Free-Space_Green's_Function_and_Radiation_from_a_Hertzian_Dipole) (2016).
15. Frezza, F., Mangini, F., & Tedeschi, N. [Introduction to Electromagnetic Scattering: Tutorial](https://doi.org/10.1364/JOSAA.35.000163) (2018).
16. Nussenzveig, H. M. [Diffraction Effects in Semiclassical Scattering](https://www.cambridge.org/us/academic/subjects/physics/theoretical-physics-and-mathematical-physics/diffraction-effects-semiclassical-scattering?format=PB) (1992).
99. Hansen, J. E., & Travis, L. D. [Light Scattering in Planetary Atmospheres](https://doi.org/10.1007/BF00168069) (1974).

<!--
### Polarization of Light

We have just seen that transverse waves are composed of two independent scalar components. Can we separate them? The answer is yes.

In the framework of geometric optics, light is represented by oriented ray. When this ray encounters a birefringent crystal (such as calcite), double refraction occurs, and the original light ray splits into the *ordinary* and *extraordinary* rays.

The idea behind [polarization of light](http://hyperphysics.phy-astr.gsu.edu/hbase/phyopt/polarcon.html#c1) \[Chandra?, [4](#references) (ch. 5), [5](#references) (vol. I, ch. 33), [6](#references) (ch. 1.4), [7](#references), [8](#references) (p. 527-533), [8](#references)\] (not to be confused with [polarization of matter](http://hyperphysics.phy-astr.gsu.edu/hbase/electric/dielec.html#c1) \[[4](#references) (ch. 4.5), [5](#references) (vol. II, ch. 32), [6](#references) (ch. 2.3), [8](#references)\]) is simple. Consider a fixed point[^5] in space; at that point, imagine the plane[^6] containing the electric and the magnetic vectors. Over time, the tip of the electric (and the magnetic) vector traces a closed[^7] curve. That shape of that curve is called polarization.

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
		\pm           \bar{E}\_b(\bm{r}) \sin(\phi(\bm{r}) + \omega t)
	\end{bmatrix},
$$

where \\(\phi(\bm{r})\\) is isotropic, and the phase difference between the two axes is \\(\pi/2\\). The sign in front of the \\(\bar{E}\_b\\) is used to distinguish the orientation of the ellipse (clockwise vs counterclockwise).

The significance of Equation 19 can be better seen if we express it using complex numbers

$$ \tag{??}
	\bm{E}(\bm{r},t) = \mathcal{Re}
	\begin{Bmatrix}
		\phantom{\pm} \bar{E}\_a(\bm{r}) \phantom{e^{-i \pi/2}} e^{i(\phi(\bm{r}) + \omega t)} \cr
				 \pm  \bar{E}\_b(\bm{r})          e^{-i \pi/2}  e^{i(\phi(\bm{r}) + \omega t)}
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
