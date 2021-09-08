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

substitute them into Equations 1.12.1-1.12.2, and group the resulting expressions with Equations 1.1.1-1.1.2, we obtain the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) of the Maxwell equations \[[6](#references) (ch. 1.1.1), [7](#references) (ch. 2.1), [8](#references) (ch. 2.1), [9](#references) (ch. 2.1)\]:

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

Since the microscopic properties of matter are continuous, this leads to continuous fields. But, sometimes, it is convenient to introduce a macroscopic discontinuity to approximate a very rapid (yet continuous) variation of material properties. At the *optical interface*, the fields must satisfy the [boundary conditions](https://en.wikipedia.org/wiki/Interface_conditions_for_electromagnetic_fields) \[[6](#references) (ch. 1.1.3), [7](#references) (ch. 2.10), [8](#references) (ch. 2.4), [9](#references) (ch. 2.2)\]. If \\(\bm{n\_{12}}\\) is a unit normal vector pointing from region 1 to region 2, the normal components of the fields must be such that

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

We take the Maxwell equations as axioms, and use them as the foundation on which we base the theory presented in the following sections.

### Force and Energy

Given a mathematical description of electromagnetic radiation in terms of vector fields, we would like to physically characterize it as an energy transfer process. In order to do that, we have to determine how much energy there is in a given volume element of space, and also the rate of energy flow \[[5](#references) (vol. II, ch. 27), [6](#references) (ch. 1.1.4), [7](#references) (ch. 2.11), [8](#references) (ch. 2.5), [9](#references) (ch. 2.4)\].

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

A more pressing question is whether our derivation, which exclusively used the microscopic formulation of the Maxwell equations, is valid at the microscopic scale. The answer is: almost.

A typical derivation[^5] replaces \\(\bm{J}\\) with \\(\bm{J_f}\\) in Equation 2.6, which, after substitution of Equation 1.14.3, leads to the macroscopic counterpart of Equation 2.7 \[[6](#references) (ch. 1.1.4), [8](#references) (ch. 2.5), [9](#references) (ch. 2.4)\]:

[^5]: I do not have a good reason why ignoring the bound current is valid in this context.

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

Comparison of Equations 2.17 and 2.22 shows that both formulations of the Poynting vector are equivalent if the measurement is performed inside a *non-magnetic* material. That explains the \\(\mu\_0^{-1}\\) factor: according to the microscopic formulation of the Maxwell equations, an electron (or a proton) is simply a charged region of vacuum. In comparison, due to the spatially-varying index of refraction, propagation of electromagnetic fields at the macroscopic scale can be considerably more complex.

Since electromagnetic fields oscillate so rapidly, one typically measures the *time-averaged* Poynting vector

$$ \tag{2.24}
	\braket{\bm{S}}
	= \frac{1}{T} \int\_{-T/2}^{\thinspace T/2} \bm{S}(\bm{r}, t + t') dt'.
$$

It becomes particularly useful once you consider a fixed direction \\(\bm{n}\\). Then, according to Equation 2.14,

$$ \tag{2.25}
	\mathtt{E}
	= \braket{\bm{S}} \cdot \bm{n}
	= \braket{\bm{S}} \cos{\theta}
$$

is the amount of energy per second per unit area that flows through a surface with the normal \\(\bm{n}\\). That is the definition of [irradiance](https://en.wikipedia.org/wiki/Irradiance) \\(\mathtt{E}\\).

### Maxwell Equations in the Frequency Domain

The Maxwell equations can be simplified by transforming the fields from the time to the frequency domain.

Define[^1] the [Fourier transform](https://en.wikipedia.org/wiki/Fourier_transform) of the electric *vector* field \\(\bm{E}(\bm{r}, t)\\) as

[^1]: The choice of the sign of the complex exponential is arbitrary; it determines the sign of the imaginary components of both the complex permittivity and the complex refractive index.

$$ \tag{3.1}
	\bm{E}(\bm{r}, \omega)
	= \mathcal{F} \big\lbrace \bm{E}(\bm{r}, t) \big\rbrace
	= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{-i \omega t} dt,
$$

where \\(\omega\\) is the *angular frequency*. \\(\bm{E}(\bm{r}, \omega)\\) is the electric [phasor](https://en.wikipedia.org/wiki/Phasor) field - a field of complex (phase) vectors[^2].

[^2]: This implies that all factors comprising the expression of the complex field are also complex.

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
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega
	 + \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{0} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} \Big( \bm{E}(\bm{r}, \omega) e^{i \omega t} + \big[ \bm{E}(\bm{r}, \omega) e^{i \omega t} \big]^{\*} \Big) d\omega \cr
	&= \frac{1}{\sqrt{2 \pi}} \int\_{0}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{i \omega t} \big\rbrace d\omega,
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
	&\nabla \times \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, \omega) e^{i \omega t} d\omega +
	 \frac{\partial}{\partial t} \int\_{-\infin}^{\infin} \bm{B}(\bm{r}, \omega) e^{i \omega t} d\omega = 0, \cr
	&\nabla \cdot  \int\_{-\infin}^{\infin} \bm{B}(\bm{r}, \omega) e^{i \omega t} d\omega = 0, \cr
	&\nabla \times \int\_{-\infin}^{\infin} \bm{H}(\bm{r}, \omega) e^{i \omega t} d\omega -
	 \frac{\partial}{\partial t} \int\_{-\infin}^{\infin} \bm{D}(\bm{r}, \omega) e^{i \omega t} d\omega =
	 \int\_{-\infin}^{\infin} \bm{J_f}(\bm{r}, \omega) e^{i \omega t} d\omega, \cr
	&\nabla \cdot \int\_{-\infin}^{\infin} \bm{D}(\bm{r}, \omega) e^{i \omega t} d\omega =
	 \int\_{-\infin}^{\infin} \rho_f(\bm{r}, \omega) e^{i \omega t} d\omega.
\end{aligned}
$$

It can be further simplified by applying the [Leibniz rule](https://en.wikipedia.org/wiki/Leibniz_integral_rule), taking the time derivatives, and grouping the terms:

$$ \tag{3.7}
\begin{aligned}
	&\int\_{-\infin}^{\infin} \big( \nabla \times \bm{E}(\bm{r}, \omega) +
	 i \omega \bm{B}(\bm{r}, \omega) \big) e^{i \omega t} d\omega = 0, \cr
	&\int\_{-\infin}^{\infin} \big( \nabla \cdot \bm{B}(\bm{r}, \omega) \big) e^{i \omega t} d\omega = 0, \cr
	&\int\_{-\infin}^{\infin} \big( \nabla \times \bm{H}(\bm{r}, \omega) -
	 i \omega \bm{D}(\bm{r}, \omega) \big) e^{i \omega t} d\omega =
	 \int\_{-\infin}^{\infin} \bm{J_f}(\bm{r}, \omega) e^{i \omega t} d\omega, \cr
	&\int\_{-\infin}^{\infin} \big( \nabla \cdot \bm{D}(\bm{r}, \omega) \big) e^{i \omega t} d\omega =
	 \int\_{-\infin}^{\infin} \rho_f(\bm{r}, \omega) e^{i \omega t} d\omega.
\end{aligned}
$$

Equivalently, using the transform operator notation,

$$ \tag{3.8}
\begin{aligned}
	&\mathcal{F^{-1}} \big\lbrace \nabla \times \bm{E}(\bm{r}, \omega) +
	 i \omega \bm{B}(\bm{r}, \omega) \big\rbrace = 0, \cr
	&\mathcal{F^{-1}} \big\lbrace \nabla \cdot \bm{B}(\bm{r}, \omega) \big\rbrace = 0, \cr
	&\mathcal{F^{-1}} \big\lbrace \nabla \times \bm{H}(\bm{r}, \omega) -
	 i \omega \bm{D}(\bm{r}, \omega) \big\rbrace =
	 \mathcal{F^{-1}} \big\lbrace \bm{J_f}(\bm{r}, \omega) \big\rbrace, \cr
	&\mathcal{F^{-1}} \big\lbrace \nabla \cdot \bm{D}(\bm{r}, \omega) \big\rbrace =
	 \mathcal{F^{-1}} \big\lbrace \rho_f(\bm{r}, \omega) \big\rbrace.
\end{aligned}
$$

Generally speaking, the equality of integrands (or function arguments) does not follow from the equality of integrals (or function values). Luckily for us, the inverse Fourier transform is [uniquely defined](https://en.wikipedia.org/wiki/Fourier_inversion_theorem) for a large class of functions. So, under quite general [conditions](https://en.wikipedia.org/wiki/Fourier_inversion_theorem#Conditions_on_the_function), we may say that

$$ \tag{3.9}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \bm{D}(\bm{r}, \omega) = \bm{J_f}(\bm{r}, \omega), &
	&\nabla \cdot  \bm{D}(\bm{r}, \omega) = \rho_f(\bm{r}, \omega).
\end{aligned}
$$

These are the *macroscopic* Maxwell equations for electromagnetic fields with *arbitrary* time dependence.

For future reference, we must also mention the frequency-domain representation of the *microscopic* Maxwell equations. The first two equations are the same, so their Fourier transforms remain unchanged; the third and the fourth equations can be quickly obtained by replacing \\(\bm{J_f}\\) with \\(\bm{J}\\), \\(\bm{D}\\) with \\(\epsilon_0 \bm{E}\\), and \\(\bm{H}\\) with \\(\mu\_0^{-1} \bm{B}\\):

$$ \tag{3.10}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, \omega) \big) - i \omega \big( \epsilon_0 \bm{E}(\bm{r}, \omega) \big) = \bm{J}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \epsilon_0 \bm{E}(\bm{r}, \omega) \big) = \rho(\bm{r}, \omega).
\end{aligned}
$$

A useful property of the frequency-domain solution is the reduction in the number of independent Maxwell equations from four to two. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, taking the divergence of the two equations on the left produces the equations on the right. The latter is true due to the expression of [conservation of charge](https://en.wikipedia.org/wiki/Charge_conservation) in the frequency domain:

$$ \tag{3.11}
	\nabla \cdot \bm{J}(\bm{r}, \omega) = -i \omega \rho(\bm{r}, \omega).
$$

Finally, observe that we only need to find the expression of the electric field. From Equations 3.9.1 and 3.10.1, it follows that

$$ \tag{3.12}
	\bm{B}(\bm{r}, \omega) = \frac{i}{\omega} \nabla \times \bm{E}(\bm{r}, \omega).
$$

In certain situations, it may be advantageous to do it the other way around: find the expression of the magnetic field first, and then use Equation 3.9.3 or 3.10.3 to calculate

$$ \tag{3.13}
\begin{aligned}
	\bm{D}(\bm{r}, \omega)
	&= \frac{i}{\omega} \Big( \bm{J_f}(\bm{r}, \omega)
	- \nabla \times \bm{H}(\bm{r}, \omega) \Big), \cr
	\epsilon_0 \bm{E}(\bm{r}, \omega)
	&= \frac{i}{\omega} \Big( \bm{J}(\bm{r}, \omega)
	- \nabla \times \big( \mu\_0^{-1} \bm{B}(\bm{r}, \omega) \big) \Big).
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

[^3]: The fact that the electric and the magnetic fields cannot exist independently can be shown by a simple relativistic argument \[[12](#references) (ch 6.2)\].

One particular feature of periodic functions is the existence of the [Fourier series](https://en.wikipedia.org/wiki/Fourier_series#Complex-valued_functions) representation:

$$ \tag{4.4}
	\bm{E}(\bm{r}, t)
	= \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{i p \omega_1 t}
	= \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{i \omega_p t},
	= \sum\_{p = -\infin}^{\infin} \ket{u_p} \braket{u_p \vert \bm{E}}.
$$

where we defined \\(\omega_p = p \thinspace \omega_1\\), with the *Fourier coefficients* \\(\bm{E_p}\\) given by the integral

$$ \tag{4.5}
	\bm{E_p}(\bm{r})
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} \bm{E}(\bm{r}, t) e^{-i \omega_p t} dt
	= \braket{u_p \vert \bm{E}}.
$$

Equation 4.5 can be interpreted as the [projection](https://en.wikipedia.org/wiki/Hilbert_space#Fourier_analysis) onto the discrete [Fourier basis](https://en.wikipedia.org/wiki/Fourier_series#Hilbert_space_interpretation), with Equation 4.4 showing the reconstruction. The individual elements of Equation 4.4 (called *harmonics*) possess a key property encapsulated in the *orthonormalization relation*

$$ \tag{4.6}
	\braket{u_p \vert u_q}
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} e^{i (\omega_q - \omega_p) t} dt
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
	&= \bm{E_0}(\bm{r})
	 + \sum\_{p = 1}^{\infin} \bm{E_p}(\bm{r}) e^{i \omega_p t}
	 + \sum\_{p = -\infin}^{-1} \bm{E_p}(\bm{r}) e^{i \omega_p t} \cr
	&= \bm{E_0}(\bm{r}) + \sum\_{p = 1}^{\infin} \Big( \bm{E_p}(\bm{r}) e^{i \omega_p t} + \big[ \bm{E_p}(\bm{r}) e^{i \omega_p t} \big]^{\*} \Big) \cr
	&= \bm{E_0}(\bm{r}) + \sum\_{p = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{i \omega_p t} \big\rbrace.
\end{aligned}
$$

Intuitively, a harmonic produces an equivalent amount of vibration regardless of the sign of its frequency.

What happens when we Fourier transform a periodic function? Combining Equations 3.1 and 4.4,

$$ \tag{4.9}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \bm{E}(\bm{r}, t) e^{-i \omega t} dt \cr
	&= \frac{1}{\sqrt{2 \pi}} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) \int\_{-\infin}^{\infin} e^{i (\omega_p - \omega) t} dt \cr
	&= \sqrt{2 \pi} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) \delta(\omega_p - \omega),
\end{aligned}
$$

where we used the exponential form of the [Dirac delta](https://en.wikipedia.org/wiki/Dirac_delta_function) function 

$$ \tag{4.10}
	\delta(x)
	= \frac{1}{2 \pi} \int\_{-\infin}^{\infin} e^{i x y} dy.
$$

Since the individual harmonics are orthogonal (and, as a result, independent), analysis of a *polychromatic* field with \\(N\\) components can be simplified by considering \\(N\\) *monochromatic* (or *time-harmonic*) vector fields, such as

$$ \tag{4.11}
	\bm{E}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{i \omega_p t} \big\rbrace,
$$

in isolation, with the corresponding phasor fields

$$ \tag{4.12}
	\bm{E}(\bm{r}, \omega)
	= \sqrt{2 \pi} \bm{E_p}(\bm{r}) \delta(\omega_p - \omega).
$$

Let us put the math to work. Assuming that the electromagnetic field is time-harmonic, Equation 2.22 of the Poynting vector can be written as

$$ \tag{4.13}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&=  \mu\_0^{-1}
		\Bigg( \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{i \omega_p t} \Bigg) \times
		\Bigg( \sum\_{q = -\infin}^{\infin} \bm{H_q}(\bm{r}) e^{i \omega_q t} \Bigg) \cr
	&=  \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p}(\bm{r}) e^{i \omega_p t} \big) \times
		\big( \bm{H_q}(\bm{r}) e^{i \omega_q t} \big) \cr
	&=  \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\big( \bm{E_p}(\bm{r}) e^{i \omega_p t} \big) \times
		\big[ \bm{H_q}(\bm{r}) e^{i \omega_q t} \big]^{\*}.
\end{aligned}
$$

The corresponding expression of the time-averaged Poynting vector (c.f. Equation 2.24) is

$$ \tag{4.14}
\begin{aligned}
	\braket{\bm{S}}
	= \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
	\bm{E_p}(\bm{r}) \times \big[ \bm{H_q}(\bm{r}) \big]^{\*}
	\Bigg(
		\frac{1}{T} \int\_{-T/2}^{\thinspace T/2} e^{i (\omega_p - \omega_q) t'} dt'
	\Bigg).
\end{aligned}
$$

First, consider the case when \\(T = T_1\\). We can immediately apply the orthonormalization relation to obtain

$$ \tag{4.15}
\begin{aligned}
	\braket{\bm{S}}
	&=  \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
		\bm{E_p} \times [\bm{H_q}]^{\*} \thinspace \delta_{m,n} \cr
	&=  \sum\_{p = -\infin}^{\infin}
		\bm{E_p} \times [\bm{H_p}]^{\*} \cr
	&=  (\bm{E_0} \times \bm{H_0})
	  + \sum\_{p = 1}^{\infin} \bm{E_p} \times [\bm{H_p}]^{\*}
	  + \sum\_{p = -\infin}^{-1} \bm{E_p} \times [\bm{H_p}]^{\*} \cr
	&=  (\bm{E_0} \times \bm{H_0})
	  + \sum\_{p = 1}^{\infin}
		\Big( 
			\big( \bm{E_p} \times [\bm{H_p}]^{\*} \big)
		  + \big[ \bm{E_p} \times [\bm{H_p}]^{\*} \big]^{\*}
		\Big) \cr
	&=  (\bm{E_0} \times \bm{H_0})
	  + \sum\_{p = 1}^{\infin}
		2 \thinspace \mathcal{Re} \big\lbrace \bm{E_p} \times [\bm{H_p}]^{\*} \big\rbrace,
\end{aligned}
$$

which is a real vector, as expected.

In the case when \\(T > T_1\\), the total value of \\(\braket{\bm{S}}\\) is a sum of the contribution from a number of whole periods (given by Equation 4.15) and the contribution from a fraction of the period. If the period used for time-averaging is very large \\((T \gg T_1)\\), the *relative* contribution from a fraction of the period will be much smaller than the *relative* contribution from a large number of whole periods, so the formula of Equation 4.15 should serve as a good approximation.

Equation 4.15 allows us to define the *time-averaged* Poynting phasor (technically, it is a Fourier coefficient)

$$ \tag{4.16}
	\braket{\bm{S_p}} = \Big( \bm{E_p}(\bm{r}) \times \big[ \bm{H_p}(\bm{r}) \big]^{\*} \Big)
$$

such that (cf. Equation 4.8)

$$ \tag{4.17}
	\braket{\bm{S}}
	= \braket{\bm{S_0}}
	+ \sum\_{p = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace \negthinspace \braket{\bm{S_p}} \negthinspace \big\rbrace.
$$

It produces a more compact expression of irradiance

$$ \tag{4.18}
	\mathtt{E}
	= \braket{\bm{S}} \cdot \bm{n}
	= \braket{\bm{S_0}} \cdot \bm{n}
	+ \sum\_{p = 1}^{\infin} 2 \thinspace \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cdot \bm{n}
$$

that can be written in terms of [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance)

$$ \tag{4.19}
	\mathtt{E_p}
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

The simplest way is to assume that the material responds to weak electromagnetic fields in an approximately linear manner[^6]. In this case, we may express \\(\bm{D}\\) and \\(\bm{H}\\) as [bilinear](https://en.wikipedia.org/wiki/Bilinear_map) functions of \\(\bm{E}\\) and \\(\bm{B}\\) \[[10](#references) (ch. 3.2)\]:

[^6]: This assumes that omitting the quadratic (and higher order) terms in the Taylor series expansion results in a negligible error.

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

where \\(\sigma\\) is the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity), \\(\epsilon\\) is the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity), and \\(\mu\\) is the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \[[6](#references) (ch. 1.1.2), [7](#references) (ch. 2.3), [8](#references) (ch. 2.3), [9](#references) (ch. 2.1)\].

Care must taken when transforming Equations 5.2.1-5.2.3 to the time domain, since a multiplication in the frequency domain leads to a convolution in the time domain.

Conservation of charge implies that

$$ \tag{5.3}
	\rho\_i(\bm{r}, \omega) = \frac{i}{\omega} \nabla \cdot \bm{J\_i}(\bm{r}, \omega).
$$

Equations 5.2.1 and 5.3 assume that the *free* charges and currents may be decomposed into the *source* and the *induced* parts:

$$ \tag{5.4}
	\bm{J\_f} = \bm{J\_s} + \bm{J\_i}, \quad
	\rho\_f = \rho\_s + \rho\_i.
$$

If the constitutive relations hold, the Maxwell equations in the frequency domain take the following form:

$$ \tag{5.5}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega)
	= \bm{J\_s}(\bm{r}, \omega) + \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\nabla \cdot  \big( \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega) + \frac{i}{\omega} \nabla \cdot \big( \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big).
\end{aligned}
$$

Using the definition of the [complex permittivity](https://en.wikipedia.org/wiki/Permittivity#Complex_permittivity) \[[7](#references) (ch. 2.8), [8](#references) (ch. 2.3), [9](#references) (ch. 2.3)\]

$$ \tag{5.6}
	\varepsilon(\bm{r}, \omega) = \epsilon(\bm{r}, \omega) - \frac{i}{\omega} \sigma(\bm{r}, \omega),
$$

we obtain the Maxwell equations for *linear, isotropic* media:

$$ \tag{5.7}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = \bm{J\_s}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho\_s(\bm{r}, \omega).
\end{aligned}
$$

Alternatively, the same set of equations can be expressed in terms of the \\(\bm{B}\\) field:

$$ \tag{5.8}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \big( \mu^{-1}(\bm{r}, \omega) \bm{B}(\bm{r}, \omega) \big) - i \omega \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \bm{J_s}(\bm{r}, \omega), &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = \rho_s(\bm{r}, \omega).
\end{aligned}
$$

Writing the Maxwell equations this way allows us to compare the macroscopic Equations 5.8 with the microscopic Equations 3.10. Observe that, when \\(\varepsilon\\) and \\(\mu\\) are independent from \\(\bm{r}\\) (or if the spatially-varying component is attributed to the current term on the right-hand side), their solutions are formally the same.

### Helmholtz Equation

Consider a region of space without any source charges or currents. Physically, this means that the region is *source-free*; but that does not mean it contains no energy. To find an expression of the internal field, we shall explore all general solutions of the Maxwell equations (we can find a special solution once we specify the boundary conditions), and determine how these solutions evolve over time.

Setting \\(\bm{J\_s} = \rho\_s = 0\\) in Equations 5.7.3-5.7.4, we obtain a coupled system of linear homogeneous equations:

$$ \tag{6.1}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \mu(\bm{r}, \omega) \bm{H}(\bm{r}, \omega) \big) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \big( \varepsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega) \big) = 0.
\end{aligned}
$$

Next, assume that the medium is *homogeneous* (constant in space):

$$ \tag{6.2}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \mu(\omega) \bm{H}(\bm{r}, \omega) = 0, &
	&\nabla \cdot  \bm{H}(\bm{r}, \omega) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \varepsilon(\omega) \bm{E}(\bm{r}, \omega) = 0, &
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


By considering each vector component, we obtain a system of three *homogeneous* [Helmholtz equations](https://en.wikipedia.org/wiki/Helmholtz_equation):

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

is a valid solution for a certain value of \\(\bm{n}\\). To avoid clutter, we shall adhere to a common convention with the negative sign; the positive solution can be obtained by reversing the direction of \\(\bm{n}\\).

How should we choose the direction of \\(\bm{n}\\)? It depends on the location of sources. Unless they are explicitly specified, we can use *any* value of \\(\bm{n}\\). And because the Helmholtz equation is both linear and homogeneous, we can actually use *every* value of \\(\bm{n}\\) by invoking the superposition principle. Thus, the general solution is an integral taken over the surface of the unit sphere \\(\mathbb{S^2}\\) [measured](https://en.wikipedia.org/wiki/Lebesgue_integration#Construction) by the [solid angle](https://en.wikipedia.org/wiki/Solid_angle) \\(d\Omega_n\\):

$$ \tag{6.15}
	\psi(\bm{r}) =
	\oint\_{\mathbb{S}^2} \psi(\bm{r}, \bm{n}) d\Omega_n =
	\oint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{-i k (\bm{r} \cdot \bm{n})} d\Omega_n.
$$

Equation 6.15 can be used to solve Equation 6.10:

$$ \tag{6.16}
	\bm{E}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E}(\bm{r}, \bm{n}, \omega) d\Omega_n =
	\oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega_n.
$$

That is a general solution of the Maxwell equations for *linear, isotropic, homogeneous, source-free* media.

### Plane waves

We can find a solution of the Maxwell equations in the time domain by performing the inverse Fourier transform of Equation 6.16:

$$ \tag{7.1}
	\bm{E}(\bm{r}, t)
	= \sqrt{\frac{2}{\pi}} \int\_{0}^{\infin} \oint\_{\mathbb{S}^2} \mathcal{Re} \big\lbrace \bm{E}(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} e^{i \omega t} \big\rbrace d\Omega_n d\omega.
$$

This triple integral represents a [wave packet](https://en.wikipedia.org/wiki/Wave_packet). It is a collection of *vector* [plane waves](https://en.wikipedia.org/wiki/Plane_wave) \[[6](#references) (ch. 1.4.2), [8](#references) (ch. 3.2), [9](#references) (ch. 3.1)\] of the form

$$ \tag{7.2}
	\bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t}
	= \bm{E}(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} e^{i \omega t}
$$

each composed of three *scalar* plane waves such as

$$ \tag{7.3}
	E(\bm{r}, \bm{n}, \omega) e^{i \omega t}
	= E(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} e^{i \omega t},
$$

with the optical properies of the medium encapsulated in the [complex wave number](https://en.wikipedia.org/wiki/Wavenumber#Complex)

$$ \tag{7.4}
	k(\omega) = \omega \sqrt{\varepsilon(\omega) \mu(\omega)}.
$$

If we define the [relative complex permittivity](https://en.wikipedia.org/wiki/Relative_permittivity#Lossy_medium) \\(\varepsilon\_r\\) and the [relative permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)#Relative_permeability_and_magnetic_susceptibility) \\(\mu\_r\\) using the vacuum as reference,

$$ \tag{7.5}
	\varepsilon\_r(\omega) = \frac{\varepsilon(\omega)}{\epsilon_0}, \quad
	\mu\_r(\omega) = \frac{\mu(\omega)}{\mu\_0},
$$

the complex wave number can be redefined as

$$ \tag{7.6}
	k(\omega) =
	\omega \sqrt{\varepsilon(\omega) \mu(\omega)} =
	\omega \sqrt{\epsilon_0 \mu\_0} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	\frac{\omega}{c} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)}.
$$

It is convenient to use a parametrization that does not involve taking a square root. Thus, we define two positive real numbers, the [refractive index](https://en.wikipedia.org/wiki/Refractive_index) \\(\eta\\) and the [attenuation index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\kappa\\) \[[6](#references) (ch. 14.1), [8](#references) (ch. 2.3), [9](#references) (ch. 3.1)\], by

$$ \tag{7.7}
	\eta(\omega) - i \kappa(\omega) =
	\sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	c \sqrt{\Big( \epsilon(\omega) - \frac{i}{\omega} \sigma(\omega) \Big) \mu(\omega)}.
$$

For high frequencies, such as those encountered in optics, we may perform a [Laurent series](https://en.wikipedia.org/wiki/Laurent_series) [expansion](https://www.wolframalpha.com/input/?i=series+sqrt%28a-I%2Fw*b%29) at infinity:

$$ \tag{7.8}
	\eta(\omega) - i \kappa(\omega) \approx
	c \Big( \sqrt{\epsilon \mu} - i \frac{\sigma \mu}{2 \omega \sqrt{\epsilon \mu}} \Big),
$$

which gives an approximate mapping between the optical and the physical parameters[^10].

[^10]: Keep in mind that, in general, the permittivity, permeability, and conductivity are complex numbers.

The combination of Equations 7.6-7.7 produces the [dispersion relation](https://en.wikipedia.org/wiki/Dispersion_relation)

$$ \tag{7.9}
	k(\omega)
	= \frac{\omega}{c} \big( \eta(\omega) - i \kappa(\omega) \big)
	= \omega \bigg( \frac{1}{v_p(\omega)} - \frac{i}{v_a(\omega)} \bigg),
$$

where \\(v_p = c/\eta\\) is the [phase velocity](https://en.wikipedia.org/wiki/Phase_velocity) and \\(v_a=c/\kappa\\) is the *amplitude velocity*. It shows that, in a dispersive medium, waves of different frequencies propagate at different rates.

In order to develop some intuition about the role of the refractive index \[[5](#references) (vol. II, ch. 32.4)\], consider the spatial component of a scalar plane wave

$$ \tag{7.10}
\begin{aligned}
	E(\bm{r}, \bm{n}, \omega)
	&= E\_0 e^{-i k (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{i \delta} e^{-i \omega (1/v_p - i/v_a) (\bm{r} \cdot \bm{n})} \cr
	&= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} e^{i \delta - i \omega (\bm{r} \cdot \bm{n}) / v_p}.
\end{aligned}
$$

Multiplication by the temporal component \\(e^{i \omega t}\\) yields the full expression of a scalar plane wave:

$$ \tag{7.11}
\begin{aligned}
	E(\bm{r}, \bm{n}, \omega) e^{i \omega t}
	&= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} e^{i \delta - i \omega (\bm{r} \cdot \bm{n}) / v_p} e^{i \omega t}.
\end{aligned}
$$

As any complex number, it can be expressed in terms of the *amplitude* \\(|E|\\) and the *phase* \\(\theta\\), both of which are [real-valued functions](https://en.wikipedia.org/wiki/Real-valued_function).

Let us perform [dimensional analysis](https://en.wikipedia.org/wiki/Dimensional_analysis) of Equation 7.11.

First, notice that \\((\bm{r} \cdot \bm{n}) / v\\) has units of time. Then, take the [argument](https://en.wikipedia.org/wiki/Argument_(complex_analysis)) of the expression

$$ \tag{7.12}
	\theta(\bm{r}, t)
	= \mathcal{Arg} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	= \delta - \omega \big(\bm{r} \cdot \bm{n} - v_p t \big) / v_p.
$$

Notice that, for any \\(\Delta t\\),

$$ \tag{7.13}
	\theta(\bm{r}, t) = \theta(\bm{r} + (v_p \Delta t) \bm{n}, \thinspace t + \Delta t).
$$

This implies that \\(\theta\\) represents a plane propagating along its normal \\(\bm{n}\\) at the phase velocity \\(v_p\\). In general, surfaces of constant phase are called *cophasal*, or *wavefronts*.

Taking the real part of Equation 7.11 allows us to uncover the *wave amplitude*

$$ \tag{7.14}
	\mathcal{Re} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} \cos{\theta},
	= |E(\bm{r}, \bm{n}, \omega)| \cos{\theta},
$$

where \\(|E|\\) is the spatially-varying *peak amplitude*.

If the attenuation index \\(\kappa = 0\\), the amplitude velocity \\(v_a = \infty\\), and Equation 7.14 represents a regular sine wave.

[Insert Picture Here]

On the other hand, \\(\kappa > 0\\) produces an exponential decay characteristic of an [evanescent wave](https://en.wikipedia.org/wiki/Evanescent_field).

[Insert Picture Here]

Equation 7.1 can be seen as a decomposition of the electric field into plane waves interfering with each other. By [interference](https://en.wikipedia.org/wiki/Wave_interference), we simply mean that when two waves (often assumed to be of the same frequency) are added together, the peak amplitude of the resulting wave is, in general, different from the sum of the peak amplitudes unless the waves oscillate *in-phase* (in such a way that the phase difference is zero), in which case interference is said to be *constructive*. In the worst case scenario, the peaks align with the troughs, and *destructive* interference causes the waves to completely cancel each other. A typical *out-of-phase* case falls in between these two extremes \[[5](#references) (vol. I, ch. 29)\].

[Insert Picture Here]

It is easy to show that, under favorable conditions, the field vectors oscillate in the plane of the wave. In general, the Maxwell equations (c.f. Equations 3.9.2, 3.9.4) tell us that

$$ \tag{7.15}
	\nabla \cdot \bm{B}(\bm{r}, \omega) = 0, \quad
	\nabla \cdot \bm{D}(\bm{r}, \omega) = \rho_s(\bm{r}, \omega).
$$

More specifically, in a source-free region of a linear, isotropic, homogeneous medium (c.f. Equation 6.2.2, 6.2.4),

$$ \tag{7.16}
	\nabla \cdot \bm{H}(\bm{r}, \omega) = 0, \quad
	\nabla \cdot \bm{E}(\bm{r}, \omega) = 0.
$$

Take \\(\bm{E}\\) as an example. Substitute Equation 6.16 into 7.16.2:

$$ \tag{7.17}
	\nabla \cdot \oint\_{\mathbb{S}^2} \bm{E}(\bm{r}, \bm{n}, \omega) d\Omega_n =
	\nabla \cdot \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega_n = 0.
$$

Moving divergence under the integral sign leads to

$$ \tag{7.18}
	\nabla \cdot \bm{E}(\bm{r}, \bm{n}, \omega)
	= -i k(\omega) \bm{n} \cdot \bm{E}(\bm{r}, \bm{n}, \omega)
	= 0.
$$

After division by the constant \\(i k\\) and multiplication by \\(e^{i \omega t}\\), it is clear that the field vector is orthogonal to the plane normal at all times:

$$ \tag{7.19}
	\bm{n} \cdot \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	= 0.
$$

Similarly, we can relate the electric and the magnetic field vectors. According to Equation 3.9.1,

$$ \tag{7.20}
	\nabla \times \bm{E}(\bm{r}, \omega) = - i \omega \bm{B}(\bm{r}, \omega).
$$

Substitute Equation 6.16 again (into both sides) and expand the expression of the curl

$$ \tag{7.21}
	\nabla \times \bm{E}(\bm{r}, \bm{n}, \omega)
	= -i k(\omega) \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega).
$$

Equation 7.20 is thus equivalent to

$$ \tag{7.22}
	-i k(\omega) \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega) = - i \omega \bm{B}(\bm{r}, \bm{n}, \omega).
$$

Using the definition of the wave number given by Equation 7.9, we obtain the following equation:

$$ \tag{7.23}
	\frac{1}{c} \big(\eta(\omega) - i \kappa(\omega) \big) \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega)
	= \bm{B}(\bm{r}, \bm{n}, \omega).
$$

If we take the real part of this expression multiplied by \\(e^{i \omega t}\\), the result

$$ \tag{7.24}
	\frac{\eta(\omega)}{c} \bm{n} \times \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
	= \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace
$$

shows us that the electric and the magnetic field vectors of a plane wave are orthogonal at all times. Thus, \\(\lbrace \bm{E}, \bm{B}, \bm{n} \rbrace\\) is an orthogonal triad of vectors that defines the geometric configuration of a plane wave in a linear, isotropic, homogeneous, source-free medium:

$$ \tag{7.25}
	\bm{n} \times \frac{\mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace}{\big\vert \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace \big\vert}
	= \frac{\mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace}{\big\vert \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace \big\vert}.
$$

[Insert Picture Here]

We have seen the \\(\bm{E} \times \bm{B}\\) expression before (c.f. Equation 2.17). Assuming the fields are monochromatic, and the medium where we perform the measurements - non-magnetic, Equation 7.25 suggests that

$$ \tag{7.26}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
	= \mu\_0^{-1} |\bm{E}(\bm{r}, t)| |\bm{B}(\bm{r}, t)| \bm{n}
$$

is the expression of the Poynting vector of a plane wave.

Since the field vectors are orthogonal, Equations 7.24-7.25 define the ratio of wave amplitudes:

$$ \tag{7.27}
	\frac{\big\vert \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace \big\vert}{\big\vert \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{i \omega t} \big\rbrace \big\vert}
	= \frac{\eta(\omega)}{c}.
$$

This equality has two consequences. First, it says that the electric and the magnetic vectors of a plane wave oscillate in-phase, with the magnitude of both vectors rising and falling at the same point in space and time. Stated mathematically, if we align the triad \\(\lbrace \bm{E}, \bm{B}, \bm{n} \rbrace\\) with the \\(\lbrace x,y,z \rbrace\\) axes of a Cartesian coordinate system, then

$$ \tag{7.28}
	\mathcal{Arg} \big\lbrace E_x(0, \bm{n}, \omega) \big\rbrace =
	\mathcal{Arg} \big\lbrace B_y(0, \bm{n}, \omega) \big\rbrace =
	\delta.
$$

Secondly, it means we can compute the Poynting vector without explicit consideration of the magnetic field:

$$ \tag{7.29}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \frac{\eta(\omega)}{c} |\bm{E}(\bm{r}, t)|^2 \bm{n}
	= \Big(\epsilon_0 \eta c |E\_0|^2 e^{-2 \omega (\bm{r} \cdot \bm{n}) (\kappa / c)} \cos^2{\theta(\bm{r}, t)} \Big) \bm{n},
$$

where we expanded the squared amplitude as per Equation 7.14.

The average value of a squared cosine is \\(\frac{1}{2}\\), so the corresponding irradiance[^11] value (c.f. Equation 2.25) is

$$ \tag{7.30}
	\mathtt{E}
	= \braket{\bm{S}} \cdot \bm{n}
	= \frac{1}{2} \epsilon_0 \eta c |E\_0|^2 e^{-2 \omega (\bm{r} \cdot \bm{n}) (\kappa / c)}
	= \frac{1}{2} \epsilon_0 \eta c |E\_0|^2 e^{-\beta_a (\bm{r} \cdot \bm{n})},
$$

[^11]: Since the fields are monochromatic, the expressions of irradiance and spectral irradiance are identical.

with the grouped constants forming the [absorption coefficient](https://en.wikipedia.org/wiki/Attenuation_coefficient#Absorption_and_scattering_coefficients)

$$ \tag{7.31}
	\beta_a(\omega) = 2 \omega \frac{\kappa(\omega)}{c}.
$$

### Electromagnetic Potential

The solutions of the Maxwell equations we have found so far have a limited range of validity. The medium must be isotropic and either homogeneous (which prohibits scattering) or divided into several bounded homogeneous regions (which permits scattering only at the boundaries), and the material's response to the incident electromagnetic field may only be purely linear. Moreover, while the method of solving a system of differential equations augmented with boundary conditions (the so-called [boundary value problem](https://en.wikipedia.org/wiki/Boundary_value_problem)) is suitable for many simple cases (such as reflection and transmission of a plane wave at a planar interface, which leads to the [Fresnel equations](https://en.wikipedia.org/wiki/Fresnel_equations)), it quickly becomes unwieldy for more complex problems. Another approach tends to become more viable, where one reformulates the field in terms of the [electromagnetic potential](https://en.wikipedia.org/wiki/Electromagnetic_four-potential), as this leads to an integral (rather than a differential) solution of the Maxwell equations.

Take another look at the equation of the magnetic field. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, Equation 1.1.2 can be written as

$$ \tag{8.1}
	\nabla \cdot \bm{B}(\bm{r}, t) = \nabla \cdot \big( \nabla \times \bm{A}(\bm{r}, t) \big) = 0,
$$

where \\(\bm{A}\\) is called a *vector potential* \[[5](#references) (vol. II, ch. 18), [6](#references) (ch. 2.1), [8](#references) (ch. 2.6)\].

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

In general, a vector field \\(\bm{A}\\) can be represented as a sum of a divergence-free (*transverse*) component \\(\bm{A'}\\) and a curl-free (*longitudinal*) component \\(\bm{A''}\\):

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

Equation 8.12.1 can be expanded using the [curl of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_curl) identity:

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
	\int\_{-\infin}^{\infin} \nabla^2 \psi(\bm{r}, \omega) e^{i \omega t} d\omega
	- \frac{1}{c^2} \int\_{-\infin}^{\infin} \frac{\partial^2}{\partial t^2} \big( \psi(\bm{r}, \omega) e^{i \omega t} \big) d\omega
	= \int\_{-\infin}^{\infin} -\xi(\bm{r}, \omega) e^{i \omega t} d\omega.
$$

Apply the Leibniz rule, take the time derivative, and simplify. The result is

$$ \tag{9.3}
	\int\_{-\infin}^{\infin} \Big( \nabla^2 + \big( \omega / c \big)^2 \Big) \psi(\bm{r}, \omega) e^{i \omega t} d\omega
	= \int\_{-\infin}^{\infin} -\xi(\bm{r}, \omega) e^{i \omega t} d\omega.
$$

Assume that the Fourier transform is uniquely defined. This permits us to remove the integral sign:

$$ \tag{9.4}
	\big( \nabla^2 + \big( \omega / c \big)^2 \big) \psi(\bm{r}, \omega) e^{i \omega t}
	= -\xi(\bm{r}, \omega) e^{i \omega t}.
$$

Thus we are lead to consider an *inhomogeneous* [Helmholtz equation](https://en.wikipedia.org/wiki/Helmholtz_equation#Inhomogeneous_Helmholtz_equation)

$$ \tag{9.5}
	\big( \nabla^2 + k_0^2 \big) \psi(\bm{r}) = -\xi(\bm{r}),
$$

where \\(k_0 = \omega / c\\) is the *free-space* wave number.

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

Equation 9.9 is an inhomogeneous linear differential equation. Consequently, its solution depends on the linear operator in question, its domain of validity, as well as the associated (initial or boundary) conditions. According to Equation 9.7, our domain is the whole real space, and the only applicable restriction is the Sommerfeld radiation condition. Thus, it can be shown that the *free-space* scalar Green function \\(g_0\\) takes the form[^9] of an diverging *spherical wave* \[[7](#references) (ch. 2.12), [8](#references) (ch. 5.2), [9](#references) (ap. B), [13](#references)\]:

$$ \tag{9.10}
	g_0(R) = g(R, k_0) = \frac{e^{-i k_0 R}}{4 \pi R}.
$$

[^9]: Mathematicians often move the minus sign from Equation 9.9 to 9.10. This introduces a significant amount of bookkeeping and is, ultimately, undesirable.

In order to reconcile Equations 9.8 and 9.9, multiply both sides of the latter by \\(\xi(\bm{r'})\\):

$$ \tag{9.11}
	\big( \nabla^2 + k_0^2 \big) g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) = -\delta(\bm{r} - \bm{r'}) \xi(\bm{r'}).
$$

Note that the Laplacian depends on \\(\bm{r}\\) rather than \\(\bm{r'}\\). Keeping this in mind, take the volume integral

$$ \tag{9.12}
	\big( \nabla^2 + k_0^2 \big) \int\_{\mathbb{R^3}} g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV' = -\xi(\bm{r}),
$$

and compare the result to Equation 9.5. We can see that we have found our solution:

$$ \tag{9.13}
	\psi(\bm{r}) = \int\_{\mathbb{R^3}} g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV'.
$$

Note that, despite the \\(1/|\bm{r} - \bm{r'}|\\) factor in the scalar Green function making the integral [improper](https://en.wikipedia.org/wiki/Improper_integral), it is convergent provided that \\(\xi\\) is a piecewise-continuous function \[[7](#references) (ch. 3.2)\].

Equation 9.13 allows us to find the expressions of the scalar and the vector potentials in the frequency domain:

$$ \tag{9.14}
\begin{aligned}
	&\bm{A}(\bm{r}, \omega)
	= \int\_{V} g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'
	= \int\_{V} \frac{e^{-i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV', \cr
	&\phi(\bm{r}, \omega)
	= \int\_{V} g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\rho(\bm{r'}, \omega)}{\epsilon_0} dV'
	= \int\_{V} \frac{e^{-i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\rho(\bm{r'}, \omega)}{\epsilon_0} dV',
\end{aligned}
$$

where we have replaced \\(\mathbb{R^3}\\) by \\(V\\) since charges tend to occupy a finite region of space.

Note that Equation 9.13 represents a *special* solution of the *inhomogeneous* Helmholtz equation. Since, due to linearity, adding a solution of the *homogeneous* equation does not change the right-hand side, we can obtain the *general* solution of Equation 9.5 by summing Equations 6.15 and 9.13:

$$ \tag{9.15}
\begin{aligned}
	\psi(\bm{r})
	&= \psi_i(\bm{r}) + \psi_s(\bm{r}) \cr
	&= \oint\_{\mathbb{S}^2} \psi_i(0, \bm{n}) e^{-i k_0 (\bm{r} \cdot \bm{n})} d\Omega_n
	 + \int\_{\mathbb{R^3}} \frac{e^{-i k_0 |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \xi(\bm{r'}) dV'.
\end{aligned}
$$

Since Equation 9.14 integrates over all currents and charges, all sources of electromagnetic fields are already accounted for, and the homogeneous part of the solution is 0. However, it is often convenient to specify the primary sources separately. In order to do that, we must recall the current and charge decomposition given by Equations 1.9 and 5.4. After substitution, the solution may be alternatively written as

$$ \tag{9.16}
\begin{aligned}
	&\bm{A}(\bm{r}, \omega)
	= \bm{A_i}(\bm{r}, \omega)
	+ \int\_{V} \frac{e^{-i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J_b}(\bm{r'}, \omega) + \bm{J_i}(\bm{r'}, \omega)}{\mu_0^{-1}} dV', \cr
	&\phi(\bm{r}, \omega)
	= \phi_i(\bm{r}, \omega)
	+ \int\_{V} \frac{e^{-i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\rho_b(\bm{r'}, \omega) + \rho_i(\bm{r'}, \omega)}{\epsilon_0} dV'.
\end{aligned}
$$

These equations have a simple optical interpretation: the first term on the right-hand side can be seen as the *incident* field (generated by primary sources), and the integral corresponds to the *scattered* field (produced by secondary sources).

Let us complete the derivation by finding the expressions of the electric and the magnetic phasors. First, let us establish some context by deriving the differential equation we are planning to solve. We take Equations 3.10.1 and 3.10.3,

$$ \tag{9.17}
\begin{aligned}
	& \nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0, \cr
	& \nabla \times \bm{B}(\bm{r}, \omega) = i \omega \mu\_0 \epsilon_0 \bm{E}(\bm{r}, \omega) + \frac{\bm{J}(\bm{r}, \omega)}{\mu\_0^{-1}},
\end{aligned}
$$

and substitute \\(\nabla \times \bm{B}\\) from the second equation into the curl of the first one. The result is

$$ \tag{9.18}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - k_0^2(\omega) \bm{E}(\bm{r}, \omega)
	= -i \omega \frac{\bm{J}(\bm{r}, \omega)}{\mu\_0^{-1}}, \cr
	& \bm{B}(\bm{r}, \omega) = \frac{i}{\omega} \nabla \times \bm{E}(\bm{r}, \omega).
\end{aligned}
$$

The corresponding solution in terms of potentials is given by Equation 8.4. In the frequency domain, it takes the following form:

$$ \tag{9.19}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = -i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

You may recall that we have used the Lorenz condition to relate the potentials. After performing the Fourier transform, Equation 8.11 becomes

$$ \tag{9.20}
\begin{aligned}
	\nabla \cdot \bm{A}(\bm{r}, \omega) = -\frac{i \omega}{c^2} \phi(\bm{r}, \omega).
\end{aligned}
$$

Remarkably, this implies we do not need to consider \\(\phi\\) at all:

$$ \tag{9.21}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = -i \omega \bm{A}(\bm{r}, \omega) + \frac{c^2}{i \omega} \nabla \big( \nabla \cdot \bm{A}(\bm{r}, \omega) \big), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

As we group the terms, we encounter a curious operator acting on \\(\bm{A}\\):

$$ \tag{9.22}
	\bm{E}(\bm{r}, \omega)
	= -i \omega \Big( \mathcal{I} + \frac{c^2}{\omega^2} \nabla \nabla \cdot \Big) \bm{A}(\bm{r}, \omega),
$$

where \\(\mathcal{I}\\) is the *identity tensor*.

Note that the gradient is a column vector, and the divergence can be visualized as a row vector. In this specific order, they represent an [tensor product](https://en.wikipedia.org/wiki/Tensor_product):

$$ \tag{9.23}
	\bm{E}(\bm{r}, \omega) =
	-i \omega \Big( \mathcal{I} + \frac{c^2}{\omega^2} \nabla \otimes \nabla \Big) \bm{A}(\bm{r}, \omega).
$$

Let us now substitute the definition of \\(\bm{A}\\) given by Equation 9.14.1:

$$ \tag{9.24}
	\bm{E}(\bm{r}, \omega)
	= -i \omega \Big( \mathcal{I} + \frac{1}{k_0^2(\omega)} \nabla \otimes \nabla \Big) \int\_{V}
	g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

Unfortunately, a complication arises when we try to move the new operator under the integral sign - if \\(\bm{r}\\) is inside \\(V\\), this action introduces a *singularity* (a pole of order 3) at \\(\bm{r} = \bm{r'}\\). We can sidestep this issue by creating a tiny cavity \\(V_{\delta}\\) centered at \\(\bm{r}\\), and separately evaluating the field produced by the piece of matter excised from the cavity. The result is

$$ \tag{9.25}
	\bm{E}(\bm{r}, \omega)
	= -i \omega \bigg( \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'
	- \frac{\mathcal{L}}{k_0^2(\omega)} \frac{\bm{J}(\bm{r}, \omega)}{\mu_0^{-1}} \bigg),
$$

where \\(\mathcal{L}\\) is the *depolarization tensor*, the form of which depends on the shape of the cavity \[[7](#references) (ch. 3.9)\], and

$$ \tag{9.26}
	\mathcal{G_{e}}(\bm{r}, \bm{r'}, k)
	= \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) g(\bm{r} - \bm{r'}, k)
$$

is the *electric tensor* Green function \[[7](#references) (ch. 7.9)\]. As a special case, \\(\mathcal{G\_0}(\bm{r}, \bm{r'}) = \mathcal{G\_{e}}(\bm{r}, \bm{r'}, k\_0)\\) is called the *free-space tensor* Green function[^12].

[^12]: The tensor Green functions are also known as the dyadic Green functions. A [dyadic](https://en.wikipedia.org/wiki/Dyadics) is a second order tensor that uses a special notation that is considered to be relatively obsolete.

Equation 9.25 can be cast in a more compact form by using the [principle value](https://en.wikipedia.org/wiki/Cauchy_principal_value) of the electric tensor. If we define the *total electric tensor* Green function as

$$ \tag{9.27}
	\mathcal{G}(\bm{r}, \bm{r'}, k)
	= \text{p.v.} \big( \mathcal{G_{e}}(\bm{r}, \bm{r'}, k) \big) - \frac{\mathcal{L}}{k^2} \delta(\bm{r} - \bm{r'}),
$$

the expression of the electric field is reduced to

$$ \tag{9.28}
	\bm{E}(\bm{r}, \omega)
	= -i \omega \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

To find the integral form of the magnetic field, we must expand Equation 9.21.2:

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
$$

that may be expressed using the [matrix form of the cross product](https://en.wikipedia.org/wiki/Cross_product#Conversion_to_matrix_multiplication).

### Electric and Magnetic Polarization

The physical meaning of the electromagnetic potential is more apparent in the time domain. With this goal in mind, perform the inverse Fourier transform of Equation 9.14.2:

$$ \tag{10.1}
\begin{aligned}
	\phi(\bm{r}, t)
	= \frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin} \int\_{V}
		\frac{\rho(\bm{r'}, \omega)}{\epsilon_0} \frac{e^{-i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} e^{i \omega t}
	dV' d\omega.
\end{aligned}
$$

After interchanging the integrals and grouping the terms, we obtain the following expression:

$$ \tag{10.2}
\begin{aligned}
	\phi(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V} \Bigg(
		\frac{1}{\sqrt{2 \pi}} \int\_{-\infin}^{\infin}
			\frac{\rho(\bm{r'}, \omega)}{|\bm{r} - \bm{r'}|} e^{i \omega (t - |\bm{r} - \bm{r'}| / c)}
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
	\bm{p}(t)
	= \int\_{V} \rho_b(\bm{r}, t) (\bm{r} - \bm{r_0}) dV
	= - Q_b(t) \bm{r_0} + \int\_{V} \rho_b(\bm{r}, t) \bm{r} dV ,
$$

where \\(Q_b\\) is the total bound charge (also called the [electric monopole moment](https://en.wikipedia.org/wiki/Multipole_expansion)):

$$ \tag{10.17}
	Q_b(t) = \int\_{V} \rho_b(\bm{r}, t) dV,
$$

which is zero for electrically neutral objects.

On the other hand, the electric dipole moment per unit volume \\(\bm{P}\\) is position-dependent:

$$ \tag{10.18}
	\bm{p}(t) = \int\_{V} \bm{P}(\bm{r}, t) dV,
	\qquad
	\bm{p}(\omega) = \int\_{V} \bm{P}(\bm{r}, \omega) dV.
$$

This expression is useful, since we can directly relate it to the definition of the polarization current density \\(\bm{J_p}\\) given by Equation 1.9.1:

$$ \tag{10.19}
	\frac{\partial}{\partial t} \bm{p}(t) = \int\_{V} \bm{J_p}(\bm{r}, t) dV,
	\qquad
	i \omega \bm{p}(\omega) = \int\_{V} \bm{J_p}(\bm{r}, \omega) dV.
$$

Equation 10.19 implies that we can represent an arbitrary polarization current density by oscillating electric dipole moments:

$$ \tag{10.20}
	\bm{J_p}(\bm{r}, t) = \frac{\partial}{\partial t} \bm{p}(t) \delta(\bm{r} - \bm{r_0}),
	\qquad
	\bm{J_p}(\bm{r}, \omega) = i \omega \bm{p}(\omega) \delta(\bm{r} - \bm{r_0}).
$$

A molecule can become polarized for a variety of reasons \[[5](#references) (vol. II, ch. 11)\]. If the separation of charges (such as the displacement of the electron cloud relative to the nucleus) occurs due the influence of an electric field, one speaks of *induced polarization*. If we assume that the effect is linear, the response is characterized by the [molecular polarizability](https://en.wikipedia.org/wiki/Electric_susceptibility#Molecular_polarizability) tensor \\(\mathcal{\Alpha_m}\\):

$$ \tag{10.21}
	\bm{p}(\omega) \approx \mathcal{\Alpha_m}(\omega) \epsilon_0 \bm{E_{\mu}}(\bm{r_0}, \omega).
$$

If there are \\(N\\) identical electric dipoles per unit volume, we can define the electric polarization \\(\bm{P}\\) as

$$ \tag{10.22}
	\bm{P}(\bm{r}, \omega)
	= N(\bm{r}) \bm{p}(\omega)
	\approx N(\bm{r}) \mathcal{\Alpha_m}(\omega) \epsilon_0 \bm{E_{\mu}}(\bm{r_0}, \omega).
$$

Note that, in general, the microscopic field \\(\bm{E_{\mu}}\\) acting on the dipole is different from the macroscopic field \\(\bm{E}\\). The reason is that the microscopic field varies very rapidly inside the matter - it is very strong next to the nucleus, and relatively weak in the gaps between the molecules. Thus, the density of matter plays an important role. If the dipoles are randomly distributed, we can assume that the dipole under consideration is located within a spherical cavity of a uniformly polarized material. For an isotropic material, it can be shown that the two fields are related by the equation

$$ \tag{10.23}
	\epsilon_0 \bm{E_{\mu}}(\bm{r_0}, \omega) = \epsilon_0 \bm{E}(\bm{r}, \omega) + \frac{1}{3} \bm{P}(\bm{r}, \omega),
$$

where \\(\bm{P}\\) describes the electric polarization of matter surrounding the dipole \[[5](#references) (vol. II, ch. 11), [6](#references) (ch. 2.3-2.4)\].

By combining Equations 10.22 and 10.23, and setting \\(\mathcal{\Alpha_m} = \alpha_m\\),

$$ \tag{10.24}
	\bm{P}(\bm{r}, \omega)
	\approx \frac{N(\bm{r})\alpha_m(\omega)}{1 - \frac{1}{3} N(\bm{r})\alpha_m(\omega)} \epsilon_0 \bm{E}(\bm{r}, \omega),
$$

we can incorporate the correction for the *microscopic* field into the *macroscopic* theory.

In order to be able to use Equation 10.24, we need to determine both the density and the polarizability of the material. For a dielectric composed of a single type of molecules, their product is given by the [Clausius–Mossotti relation](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation):

$$ \tag{10.25}
	\frac{\epsilon\_r(\bm{r}, \omega) - 1}{\epsilon\_r(\bm{r}, \omega) + 2} = \frac{1}{3} N(\bm{r}) \alpha\_m(\omega).
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

In some sense, dipoles can be considered elementary field generators: an electric dipole produces the \\(\bm{E}\\) field, and a magnetic dipole is a source of the \\(\bm{B}\\) field. They can be used to describe any (bound) current and charge distributions, which makes them particularly useful for solving electromagnetic radiation and scattering problems.

[Insert pictures of field lines of electric and magnetic dipoles]

Finally, we would like to emphasize certain properties of the solution of the Maxwell equations in terms of potentials. Perhaps the most obvious one is that the scattered field is expressed as a volume integral. While the geometry of the volume containing the charges plays an important role, it is now apparent that the interior of the volume is the actual source of what we consider the surface reflection \[[5](#references) (vol. I, ch. 31)\]. Furthermore, Equation 10.3 says that the microscopic electromagnetic field always propagates at the speed of light. The dipole field interferes with the incident field in such a way that the total macroscopic field appears to have a lower phase velocity, which is used as the definition of the index of refraction. This mechanism is described by the [Ewald-Oseen extinction theorem](https://en.wikipedia.org/wiki/Ewald%E2%80%93Oseen_extinction_theorem) \[[6](#references) (ch. 2.4)\].

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

Participating media can be broadly divided into two categories - homogeneous and inhomogeneous. This suggests that we may split any medium into two regions: 1) homogeneous, and 2) the remaining space (that can be reinterpreted as a single particle or particle group) contained within the volume \\(V\\).

[Picture?]

According to the Maxwell equations in the frequency domain (Equation 3.9.1), the following relation holds in both regions:

$$ \tag{11.1}
	\nabla \times \bm{E}(\bm{r}, \omega) + i \omega \bm{B}(\bm{r}, \omega) = 0.
$$

On the other hand, the second Maxwell equation (3.9.3) depends on the region under consideration:

$$ \tag{11.2}
	\nabla \times \bm{H}(\bm{r}, \omega) - i \omega \bm{D}(\bm{r}, \omega) = \bm{J_f}(\bm{r}, \omega).
$$

Assuming that the media are linear, isotropic, and source-free, Equation 11.2 can be replaced by Equation 6.1.3:

$$ \tag{11.3}
\begin{aligned}
	& \nabla \times \big( \mu_1^{-1}(\omega) \bm{B}(\bm{r_1}, \omega) \big)
	= i \omega \varepsilon_1(\omega)  \bm{E}(\bm{r_1}, \omega), \cr
	& \nabla \times \big( \mu_2^{-1}(\bm{r_2}, \omega) \bm{B}(\bm{r_2}, \omega) \big)
	= i \omega \varepsilon_2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega),
\end{aligned}
$$

where the first equation only holds in the (homogeneous) region 1, and the second - in the (inhomogeneous) region 2.

Further, assume that the media are non-magnetic. This implies that

$$ \tag{11.4}
\begin{aligned}
	& \nabla \times \bm{B}(\bm{r_1}, \omega)
	= i \omega \mu_0 \varepsilon_1(\omega) \bm{E}(\bm{r_1}, \omega), \cr
	& \nabla \times \bm{B}(\bm{r_2}, \omega)
	= i \omega \mu_0 \varepsilon_2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega),
\end{aligned}
$$

These equations are ready for substitution into the curl of Equation 11.1. The result is

$$ \tag{11.5}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r_1}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_1}, \omega) = 0, \cr
	& \nabla \times \nabla \times \bm{E}(\bm{r_2}, \omega) - k_2^2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega) = 0,
\end{aligned}
$$

with the constants grouped according to the definition of the complex wave number \\(k\\) given by Equation 7.4.

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

such that

$$ \tag{11.8}
	m(\bm{r}, \omega)
	= \frac{k_2(\bm{r}, \omega)}{k_1(\omega)}
	= \sqrt{ \frac{\varepsilon_2(\bm{r}, \omega)}{\varepsilon_1(\omega)} },
$$

we can replace Equations 11.6.1 and 11.6.2 by a single equation

$$ \tag{11.9}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - k_1^2(\omega) \bm{E}(\bm{r}, \omega)
	= \bm{J'}(\bm{r}, \omega).
$$

We have already encountered a mathematically identical problem stated by Equation 9.18.1. After matching the constants, the solution is readily given by Equation 9.25:

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
	= \frac{i}{\omega} \int\_{V} \mathcal{G_{m}} \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \bm{J'}(\bm{r'}, \omega) dV'
	\qquad
	\text{if } \bm{r} \notin V,
$$

where you may recognize \\(\mathcal{G_{m}} = \nabla \times \mathcal{G_{e}}\\) from Equation 3.12.

Equation 11.10 is a special solution of the inhomogeneous Equation 11.9 (just like Equation 9.13 is a special solution of Equation 9.5). To obtain a general solution, we must combine Equation 11.10 with the solution of the homogeneous equation, which physically amounts to adding the primary sources. In our particular case, the homogeneous equation is mathematically equivalent to Equation 6.5, which allows us to directly use the solution given by Equation 6.16:

$$ \tag{11.12}
\begin{aligned}
    \bm{E}(\bm{r}, \omega)
    &= \bm{E_i}(\bm{r}, \omega) + \bm{E_s}(\bm{r}, \omega) \cr
	&= \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{-i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega_n \cr
	&+ \int\_{V} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \bm{J'}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In addition to the mathematical approach presented above, we can arrive at the same result in a more "physical" way.

Consider a particle group embedded in vacuum. The solution in terms of potentials is given by Equation 9.16:

$$ \tag{11.13}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{-i k_0(\omega) (\bm{r} \cdot \bm{n})} d\Omega_n \cr
	&+ \int\_{V} \frac{e^{-i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J_b}(\bm{r'}, \omega) + \bm{J_i}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
\end{aligned}
$$

If the medium is non-magnetic, the magnetization current \\(\bm{J_m}\\) is zero, and, according to Equation 1.9.1, the bound current is

$$ \tag{11.14}
	\bm{J\_b}(\bm{r}, t) = \bm{J\_p}(\bm{r}, t) = \frac{\partial}{\partial t} \bm{P}(\bm{r}, t).
$$

In the frequency domain, the corresponding equation is

$$ \tag{11.15}
	\bm{J\_p}(\bm{r}, \omega) = i \omega \bm{P}(\bm{r}, \omega).
$$

Using Equation 10.26 that relates polarization to the macroscopic optical properties, we may write

$$ \tag{11.16}
	\bm{J\_p}(\bm{r}, \omega) = i \omega \big( \epsilon_2(\bm{r}, \omega) - \epsilon_0 \big)  \bm{E}(\bm{r}, \omega).
$$

If the medium is conductive, Equation 5.2.1 says that

$$ \tag{11.17}
	\bm{J\_i}(\bm{r}, \omega) = \sigma_2(\bm{r}, \omega) \bm{E}(\bm{r}, \omega).
$$

Let us now introduce

$$ \tag{11.18}
	\bm{J'}(\bm{r}, \omega)
	= -i \omega \frac{\bm{J_p}(\bm{r}, \omega) + \bm{J_i}(\bm{r}, \omega)}{\mu_0^{-1}}
	= \omega^2 \big( \varepsilon_2(\bm{r}, \omega) \mu_0 - \epsilon_0 \mu_0 \big) \bm{E}(\bm{r}, \omega).
$$

We may write it in terms of wave velocities using Equations 1.4 and 7.9:

$$ \tag{11.19}
	\bm{J'}(\bm{r}, \omega)
	= \omega^2 \Bigg( \bigg( \frac{1}{v_p(\bm{r}, \omega)} - \frac{i}{v_a(\bm{r}, \omega)} \bigg)^2 - \frac{1}{c^2} \Bigg) \bm{E}(\bm{r}, \omega).
$$

Substitution into Equation 11.13 yields

$$ \tag{11.20}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{-i \omega (\bm{r} \cdot \bm{n}) / c} d\Omega_n \cr
	&+ i \omega \int\_{V} \frac{e^{-i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} \Bigg( \bigg( \frac{1}{v_p(\bm{r'}, \omega)} - \frac{i}{v_a(\bm{r'}, \omega)} \bigg)^2 - \frac{1}{c^2} \Bigg) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

If the volume integral vanishes, we are left with the incident wave propagating at the speed of light \\(c\\). If we wish to replace vacuum with another homogeneous medium, we must reduce the wave velocities in the exterior region by a factor of \\((\eta_1 - i \kappa_1)^{-1}\\). Specifically, in our case, this means we must substitute \\(\varepsilon_1\\) for \\(\epsilon_0\\) and, as a consequence, \\((\varepsilon_1 \mu_0)^{-1/2}\\) for \\(c\\). These modifications do not affect the wave velocities in the interior region. The result is

$$ \tag{11.21}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{-i \omega \sqrt{\varepsilon_1(\omega) \mu\_0} (\bm{r} \cdot \bm{n})} d\Omega_n \cr
	&+ i \omega \int\_{V} \frac{e^{-i \omega \sqrt{\varepsilon_1(\omega) \mu\_0} |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( \varepsilon_2(\bm{r'}, \omega) \mu\_0 - \varepsilon_1(\omega) \mu\_0 \big) \bm{E}(\bm{r'}, \omega) dV',
\end{aligned}
$$

or, using the wave number notation,

$$ \tag{11.22}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{-i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega_n \cr
	&+ \frac{i}{\omega} \int\_{V} \frac{e^{-i k_1(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( k_2^2(\bm{r'}, \omega) - k_1^2(\omega) \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

Since the definition of the Lorenz condition is based on the Maxwell equations in vacuum, it must also be modified. Replacing \\(c\\) in Equations 8.11 and 9.20 leads us to the modified version of Equation 9.23:

$$ \tag{11.23}
	\bm{E}(\bm{r}, \omega) =
	-i \omega \Big( \mathcal{I} + \frac{1}{k_1^2(\omega)} \nabla \otimes \nabla \Big) \bm{A}(\bm{r}, \omega).
$$

After performing a substitution of Equation 11.22, and assuming that \\(\bm{r}\\) lies outside the inhomogeneous region, we obtain an expanded version of Equation 11.12:

$$ \tag{11.24}
\begin{aligned}
    \bm{E}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{-i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega_n \cr
	&+ k_1^2(\omega) \int\_{V} \Big( \mathcal{I} + \frac{1}{k_1^2(\omega)} \nabla \otimes \nabla \Big) \frac{e^{-i k_1(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In the future, when no ambiguity arises, we may drop redundant indexing by writing \\(k = k_1\\) and \\(\varepsilon = \varepsilon_1\\):

$$ \tag{11.25}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	= k^2(\omega) \int\_{V} \mathcal{G_{e}} \big( \bm{r}, \bm{r'}, k(\omega) \big) \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega)
	= \frac{i}{\omega} k^2(\omega) \int\_{V} \mathcal{G_{m}} \big( \bm{r}, \bm{r'}, k(\omega) \big) \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In the literature, Equation 11.25 is often called the *volume integral equation* \[[9](#references) (ch. 4)\]

### Dipole Radiation

Typically, it is not possible to evaluate the integrals of Equations 11.25 in closed form, since the value of the electric field in the interior of the volume is not known. Thus, we must make certain assumptions and employ various approximations in order to make computations feasible. This leads to a number of special cases.

One of the simplest problems that can be solved using our mathematical framework (of classical physics) is that of a single atom or a small molecule embedded in a homogeneous medium. This case corresponds to a tiny particle in vacuum, or an [impurity](https://en.wikipedia.org/wiki/Impurity) in an otherwise pure material. We represent it as an electric dipole - an oriented point source.

The statement of the problem leads to three simplifications [^13]:

[^13]: These simplifications are stated as assumptions. Their range of validity with regards to the general case shall be considered in the next section.

1. we may neglect the effect of the fields generated by the particle on itself;

2. the magnitude, the orientation, and the phase of the field driving the charges (equal to the incident field) is the same throughout the particle;

3. the resulting source of radiation is sufficiently localized, which permits us to evaluate the fields at practically any distance from the particle.

Assume that the particle is non-conducting. Equation 11.18 can then be simplified to

$$ \tag{12.1}
	\bm{J'}(\bm{r}, \omega)
	= -i \omega \frac{\bm{J_p}(\bm{r}, \omega)}{\mu_0^{-1}}.
$$

According to Equation 10.20, polarization current may be attributed to an oscillating dipole:

$$ \tag{12.2}
	\bm{J_p}(\bm{r}, \omega) = i \omega \bm{p}(\omega) \delta(\bm{r} - \bm{r_0}),
$$

with the dipole moment defined by Equation 10.21:

$$ \tag{12.3}
	\bm{p}(\omega) \approx \mathcal{\Alpha_m}(\omega) \varepsilon(\omega) \bm{E_i}(\bm{r_0}, \omega).
$$

where we replaced \\(\epsilon_0\\) with \\(\varepsilon\\) (to account for the properties of the surrounding medium) and \\(\bm{E_{\mu}}\\) with \\(\bm{E_i}\\) (according to the simplification discussed above).

The combination of Equations 12.1-12.3 results in

$$ \tag{12.4}
	\bm{J'}(\bm{r}, \omega)
	= k^2(\omega) \mathcal{\Alpha_m}(\omega) \bm{E_i}(\bm{r_0}, \omega) \delta(\bm{r} - \bm{r_0}).
$$

The matrix representation of \\(\mathcal{\Alpha_m}\\) is a convenient way to model polar molecules (or asymmetric particles); it acts by rotating and non-uniformly scaling the electric field phasor. To make the resulting formulas easier to interpret, we shall assume that the particle is isotropic, so that \\(\mathcal{\Alpha_m} = \alpha_m\\).

Let us substitute Equation 12.4 into Equations 11.10-11.11:

$$ \tag{12.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	= k^2(\omega) \mathcal{G_{e}} \big( \bm{r} - \bm{r_0}, k(\omega) \big) \alpha_m(\omega) \bm{E_i}(\bm{r_0}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	= \frac{i}{\omega} k^2(\omega) \mathcal{G_{m}} \big( \bm{r} - \bm{r_0}, k(\omega) \big) \alpha_m(\omega) \bm{E_i}(\bm{r_0}, \omega).
\end{aligned}
$$

Comparison with Equation 11.25 shows that Equation 12.5 corresponds to the contribution of a volume element occupied by a single atom or a small molecule, with the internal field replaced by the incident field, and with the polarizability \\(\alpha_m\\) taking the role of the relative refractive index \\(m\\).

Note that the polarizability \\(\alpha_m\\) has units of volume \[[4](#references) (ch. 6.22)\]:

$$ \tag{12.6}
	\alpha_m(\omega)
	= \int_V \big( m^2(\bm{r}, \omega) - 1 \big) dV.
$$

This can be shown by combining Equations 10.18.2 and 10.26

$$ \tag{12.7}
	\bm{p}(\omega)
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
    \frac{\partial^2}{\partial \\_ \partial \\_} \Bigg( \frac{e^{-i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg),
$$

where we may substitute \\(x\\), \\(y\\), or \\(z\\) into the blanks.

Under the previous assumption that \\(\bm{r} \neq \bm{r_0}\\), the scalar Green function and its partial derivatives are continuous, which means the order of partial differentiation makes no difference. Consequently, the matrix of Equation 12.8 is [symmetric](https://en.wikipedia.org/wiki/Symmetric_matrix), such that \\(\mathcal{G_{e}} = \mathcal{G_{e}}^T\\).

The process of differentiation is straightforward: we use the identity

$$ \tag{12.10}
    \frac{\partial}{\partial x} |\bm{r} - \bm{r'}|^{n}
    = \frac{\partial}{\partial x} \big( (\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'}) \big)^{n/2}
    = n \frac{\bm{r} - \bm{r'}}{|\bm{r} - \bm{r'}|^{2-n}} \cdot \frac{\partial}{\partial x} (\bm{r} - \bm{r'})
$$

and repeatedly apply the [product rule](https://en.wikipedia.org/wiki/Product_rule). Skipping the details of the calculation, and using the the shorthand notation \\(\bm{R} = \bm{r} - \bm{r'}, R = |\bm{R}|\\), a typical first derivative is

$$ \tag{12.11}
\begin{aligned}
    \frac{\partial}{\partial x} \Bigg( \frac{e^{-i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg)
    = -e^{-i k R} \frac{R_x}{R}
    	\bigg(
		      \frac{1}{R^2}
		    + \frac{i k}{R}
      	\bigg),
\end{aligned}
$$

the off-diagonal elements of the matrix have the form

$$ \tag{12.12}
\begin{aligned}
    \frac{\partial^2}{\partial y \partial x} \Bigg( \frac{e^{-i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg)
    = e^{-i k R} \frac{R_x R_y}{R^2}
    	\bigg(
		      \frac{3}{R^3}
		    + \frac{3 i k}{R^2}
		    - \frac{k^2}{R}
      	\bigg),
\end{aligned}
$$

while the elements on the main diagonal are equivalent to

$$ \tag{12.13}
\begin{aligned}
    \frac{\partial^2}{\partial x^2} \Bigg( \frac{e^{-i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg)
	= e^{-i k R} \frac{R_x^2}{R^2}
    	\bigg(
		      \frac{3}{R^3}
		    + \frac{3 i k}{R^2}
		    - \frac{k^2}{R}
      	\bigg)
    - e^{-i k R}
    	\bigg(
		      \frac{1}{R^3}
		    + \frac{i k}{R^2}
      	\bigg).
\end{aligned}
$$

Take a look at the individual factors in Equations 12.11-12.13: those outside the brackets oscillate between 0 and 1, while the terms  inside greatly depend on the distance between the observation point \\(\bm{r}\\) and the source \\(\bm{r_0}\\). This suggests that we may divide the entire space into zones based on the proximity to the observation point. If we are interested in the value of the field located near the source, we say that the observation point belongs to the [near-field](https://en.wikipedia.org/wiki/Near_and_far_field) zone. Similarly, far-away points are said to be located in the [far-field](https://en.wikipedia.org/wiki/Near_and_far_field) zone. Between them is a region called the *transition* zone.

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
    = -\frac{i}{k R} \bigg(\mathcal{I} - 3 \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) g(\bm{R}, k), \cr
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
    \mathcal{G_{m}} = \mathcal{G_{mn}} + \mathcal{G_{mf}},
$$

such that

$$ \tag{12.19}
\begin{aligned}
    & \mathcal{G_{mn}}(\bm{R}, k)
    = -\frac{1}{R} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) g(\bm{R}, k), \cr
	& \mathcal{G_{mf}}(\bm{R}, k)
    = -i k \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) g(\bm{R}, k).
\end{aligned}
$$

Note that the constants of proportionality are different in comparison to the electric tensor:

$$ \tag{12.20}
    k^{-3} \mathcal{G_{mn}}
    \varpropto (k R)^{-3},
    \qquad
	k^{-3} \mathcal{G_{mf}}
    \varpropto (k R)^{-2}.
$$

This implies that, as \\(k R \to \infin\\), the magnitude of the magnetic field becomes vanishingly small in comparison to that of the electric field, which explains why the dipole is called *electric*.

### Radiation in the Far-Field Zone

Let us focus on the case when the observation point is far away from the source. It is important for two reasons: first, if we are dealing with small particles, a typical observation falls into this category, and second, because this assumption greatly simplifies the behavior of the scattered wave, as we shall now demonstrate.

The idea behind the far-field approximation is simple: we discard certain terms in Equations 12.14 and 12.18. In order to do that, we must consider their magnitudes relative to each other:

$$ \tag{13.1}
    \mathcal{G_{e}}
    = \bigg( 1 + \frac{\mathcal{G_{et}}}{\mathcal{G_{ef}}} + \frac{\mathcal{G_{en}}}{\mathcal{G_{ef}}} \bigg) \mathcal{G_{ef}}, \qquad
    \mathcal{G_{m}}
    = \bigg( 1 + \frac{\mathcal{G_{mn}}}{\mathcal{G_{mf}}} \bigg) \mathcal{G_{mf}}.
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
	\approx k^2(\omega) \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) \frac{e^{-i k(\omega) R}}{4 \pi R} \alpha_m(\omega) \bm{E_i}(\bm{r'}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k^3(\omega)}{\omega} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) \frac{e^{-i k(\omega) R}}{4 \pi R} \alpha_m(\omega) \bm{E_i}(\bm{r'}, \omega),
\end{aligned}
$$

where we renamed \\(\bm{r_0}\\) to \\(\bm{r'}\\) in order to avoid introducing auxiliary variables.

Equation 13.3 can be reduced to a simpler form by aligning the origin of the coordinate system with the location of the dipole (so that \\(\bm{r'} = 0\\) and \\(\bm{R} = \bm{r}\\)) and introducing the direction of observation \\(\bm{n} = \bm{r}/|\bm{r}| = \bm{r} / r\\):

$$ \tag{13.4}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\approx k^2(\omega) \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \frac{e^{-i k(\omega) r}}{4 \pi r} \alpha_m(\omega) \bm{E_i}(0, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k^3(\omega)}{\omega} \big(\bm{n} \times \mathcal{I} \big) \frac{e^{-i k(\omega) r}}{4 \pi r} \alpha_m(\omega) \bm{E_i}(0, \omega).
\end{aligned}
$$

Let us pause for a moment to consider what \\(k R\\) represents. According to Equation 13.4, it is the phase difference (in radians) between the observation point \\(\bm{r}\\) and the location of the dipole \\(\bm{r'}\\). We may confirm this by dimensional analysis of Equations 7.6-7.7:

$$ \tag{13.5}
    k R = (\eta - i \kappa) \omega c^{-1} R = [\text{1}] [\text{rad/s}] [\text{s/m}] [\text{m}] = [\text{rad}].
$$

Intuitively, \\(k R \gg 1\\) implies that a wavelet generated by the dipole will go through a great number of cycles before reaching the observation point.

Equation 13.4 also shows that each wavelet is spherical, since the phase factor does not depend on the direction of observation. Note that surfaces of constant phase do not coincide with surfaces of constant amplitude, since the initial amplitude depends on the direction of observation.

Another important property of the far-field solution is the fact that the scattered field vectors are transverse with respect to the direction of observation:

$$ \tag{13.6}
\begin{aligned}
    & \bm{n} \cdot \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)
    = \Big( \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)^{T} \bm{n} \Big)^{T}
    = \big( \bm{n} - \bm{n} (\bm{n} \cdot \bm{n}) \big)^{T}
    = 0, \cr
	& \bm{n} \cdot \big( \bm{n} \times \mathcal{I} \big)
    = \Big( \big( \bm{n} \times \mathcal{I} \big)^{T} \bm{n} \Big)^{T}
    = \big( {-\bm{n}} \times \bm{n} \big)^{T}
	 = 0,
\end{aligned}
$$

where we utilized the symmetry properties in addition to the alternative expression of the tensor product given by Equations 9.22-9.23.

We can also show that the electric and the magnetic fields are mutually orthogonal:

$$ \tag{13.7}
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \bm{E} \cdot
	\big( \bm{n} \times \mathcal{I} \big) \bm{E}
	= \bm{E} \cdot
		\big( (\bm{n} \times \bm{E}) - \bm{n} \big( \bm{n} \cdot (\bm{n} \times \bm{E}) \big)
	= 0.
$$

Thus, at a large distance, for a fixed direction of observation, such a spherical wavelet is an analog of a plane wave with the amplitude decreasing as \\(R^{-1}\\).

[Picture]

We can make the similarity (with Equation 7.2) more apparent by introducing

$$ \tag{13.8}
\begin{aligned}
	& \bm{E_s}(\bm{r'}, \bm{n}, \omega) =
	k^2(\omega) \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \alpha_m(\omega) \bm{E_i}(\bm{r'}, \omega) e^{i k(\omega) (\bm{n} \cdot \bm{r'})}, \cr
	& \bm{B_s}(\bm{r'}, \bm{n}, \omega)
	= \frac{k^3(\omega)}{\omega} \big( \bm{n} \times \mathcal{I} \big) \alpha_m(\omega) \bm{E_i}(\bm{r'}, \omega) e^{i k(\omega) (\bm{n} \cdot \bm{r'})},
\end{aligned}
$$

so that Equation 13.4 becomes

$$ \tag{13.9}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) e^{i \omega t}
	\approx \frac{1}{4 \pi r} \bm{E_s}(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{n} \cdot \bm{r})} e^{i \omega t}, \cr
	& \bm{B_s}(\bm{r}, \omega) e^{i \omega t}
	\approx \frac{1}{4 \pi r} \bm{B_s}(0, \bm{n}, \omega) e^{-i k(\omega) (\bm{n} \cdot \bm{r})} e^{i \omega t}.
\end{aligned}
$$

We are now ready to formulate the far-field solution of the general case of a particle group. Assume that the observation point is in the far-field zone with respect to each individual volume element, so that \\(k R \gg 1\\). Equation 11.25 then takes the form

$$ \tag{13.10}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\approx k^2(\omega) \int\_{V} \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) \frac{e^{-i k(\omega) R}}{4 \pi R} \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega)
	\approx \frac{k^3(\omega)}{\omega} \int\_{V} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) \frac{e^{-i k(\omega) R}}{4 \pi R} \big( m^2(\bm{r'}, \omega) - 1 \big) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

Consider a particle (or a particle group) bounded by a sphere of radius \\(a\\) centered at the origin of the coordinate system. If that is the case, the requirement \\(k R \gg 1\\) is equivalent to \\(k (r - a) \gg 1\\).

Let us consider the expression of the radial distance between the observation point and the volume element:

$$ \tag{13.11}
\begin{aligned}
	R
	&= \sqrt{(\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'})} \cr
	&= \sqrt{|\bm{r}|^2 - 2 (\bm{r} \cdot \bm{r'}) + |\bm{r'}|^2} \cr
	&= r \sqrt{1 - 2 (\bm{n} \cdot \bm{n'}) (r'/r) + (r'/r)^2},
\end{aligned}
$$

where \\(\bm{n'} = \bm{r'}/|\bm{r'}| = \bm{r'}/r\\).

We may expand the square root (and its reciprocal) in the Taylor series of \\(r'/r\\) around zero:

$$ \tag{13.12}
\begin{aligned}
	& k R
	= k r \bigg( 1 - (\bm{n} \cdot \bm{n'}) (r'/r) + \frac{1}{2} \Big( 1 - (\bm{n} \cdot \bm{n'})^2 \Big) (r'/r)^2 + \mathrm{O} \Big( (r'/r)^3 \Big) \bigg), \cr
	& \frac{1}{R}
	= \frac{1}{r} \bigg( 1 + (\bm{n} \cdot \bm{n'}) (r'/r) + \mathrm{O} \Big( (r'/r)^2 \Big) \bigg).
\end{aligned}
$$

In order for \\(r'/r \ll 1\\) for all \\(r'\\), we require that \\(a/r \ll 1\\) (or, alternatively, \\(kr \gg ka\\)). Further assuming that \\(\frac{1}{2} k a (a/r) \ll 1\\), which is formally equivalent to \\(kr \gg \frac{1}{2} (ka)^2\\), we arrive at the approximation

$$ \tag{13.13}
	k R \approx k r - k (\bm{n} \cdot \bm{r'}), \qquad
	\frac{1}{R} \approx \frac{1}{r}.
$$

Next, we shall examine the expression of the directions of observation of individual volume element:

$$ \tag{13.14}
\begin{aligned}
	\frac{\bm{R}}{R}
	&= \frac{\bm{r} - \bm{r'}}{r \sqrt{1 - 2 (\bm{n} \cdot \bm{n'}) (r'/r) + (r'/r)^2}} \cr
	&= \bigg( \bm{n} - (r'/r) \bm{n'} \bigg) \bigg( 1 + (\bm{n} \cdot \bm{n'}) (r'/r) + \mathrm{O} \Big( (r'/r)^2 \Big) \bigg).
\end{aligned}
$$

Assume that the sphere covers a small solid angle when viewed from the observation point, which implies that \\(a/r \ll 1\\) . As the bounding volume shrinks to a point, the directions of observation corresponding to individual volume elements align, and we may write

$$ \tag{13.15}
	\frac{\bm{R}}{R} \approx \bm{n}.
$$

Substitution of Equations 13.13 and 13.15 into Equation 13.10 produces the *far-field approximation* of the volume integral equation:

$$ \tag{13.16}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) \approx k^2(\omega)
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)
	\frac{e^{-i k(\omega) r}}{4 \pi r}
	\int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) e^{i k(\omega) (\bm{n} \cdot \bm{r'})} dV', \cr
	& \bm{B_s}(\bm{r}, \omega) \approx \frac{k^3(\omega)}{\omega}
	\big(\bm{n} \times \mathcal{I} \big)
	\frac{e^{-i k(\omega) r}}{4 \pi r}
	\int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) e^{i k(\omega) (\bm{n} \cdot \bm{r'})} dV', \cr
\end{aligned}
$$

subject to the conditions \[[9](#references) (ch. 5)\]

$$ \tag{13.17}
	k r - k a \gg 1, \qquad
	kr \gg ka, \qquad
	kr \gg \frac{1}{2} (ka)^2.
$$

We would like to caution that conditions stated in Equation 13.17 are, strictly speaking, only valid for Equation 13.6. If one considers a derived quantity (such as the time-averaged Poynting vector), the preceding analysis may no longer hold, and the assumptions may change.

### Transition Operator

The volume integral equation can be reduced to a relatively simple expression by assuming that the observation point is located in the far-field zone of the particle (or the particle group). However, the value of the electric field in the interior remains unknown. For a single atom or a small molecule, we can reasonably assume that it doesn't drive itself, which makes the field driving the molecule the same as the incident field, with the resulting (simplified) expression of Equation 13.16 given by Equation 13.4. Yet, in general, this assumption does not hold. It is easy to convince ourselves by considering a highly reflective or absorptive particle - it seems fairly obvious that the total field deep inside the particle has (on average) a lower magnitude in comparison to the incident field at the same location if the particle wasn't there.

Let us recall the mechanics of scattering. In a dielectric, the incident field drives the dipoles, which in turn act as sources of scattered wavelets interfering with the incident field according to the superposition principle. We may continue this line of thinking by considering the effect of a dipole as a secondary source on all other dipoles surrounding it, effectively treating the dipole field as the secondary incident field. Repeated application of this iterative approach leads to evaluation of successive orders of scattering one by one. This is the general idea behind the [Born series](https://en.wikipedia.org/wiki/Born_series) \[[6](#references) (ch. 13.1)\].

We can formalize this approach in the following way \[[9](#references) (ch. 4.5)\]. We start with Equation 11.12, where we substitute the total electric tensor (cf. Equations 9.27-9.28) to ensure that the integral remains valid even if the observation point is inside the volume \\(V\\):

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
u(\bm{r}, \omega) =
\begin{cases}
   k^2(\omega) \big( m^2(\bm{r}, \omega) - 1 \big) &\text{if } \bm{r} \in V, \cr
   0 &\text{otherwise},
\end{cases}
$$

that allows us to write Equation 14.2 in a more compact form:

$$ \tag{14.4}
    \bm{E}(\bm{r}, \omega)
    = \bm{E_i}(\bm{r}, \omega)
	+ \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}, k(\omega) \big) u(\bm{r'}, \omega) \bm{E}(\bm{r'}, \omega) dV'.
$$

The resulting equation is recursive. Substitution makes it fairly obvious that the total electric field can be expressed as volume integral of the transformed incident field. While this fact can be rigorously derived from the properties of the tensor Green functions \[[11](#references) (ch. 5.1)\], for simplicity, let us assume that the solution exists:

$$ \tag{14.5}
    u(\bm{r'}, \omega) \bm{E}(\bm{r'}, \omega)
	= \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, \omega \big) \bm{E_i}(\bm{r''}, \omega) dV'',
$$

where \\(\mathcal{T}\\) is the *transition tensor*, the expression of which is yet to be determined.

Recursive equations written in the integral form quickly become unwieldy. Instead, we shall convert them into the operator form by using the [bra-ket notation](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation). Additionally, dependence on the frequency shall be implicit.

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

Similarly, Equation 14.5 is transformed into

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
	+ \int\_{V} \mathcal{G} \big( \bm{r}, \bm{r'}) \int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}) \bm{E_i}(\bm{r''}) dV'' dV'.
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

It does, however, depend on the geometry and the composition of the volume containing the particle group.

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
    \braket{r' \vert T \vert r''}
    &= \braket{r' \vert U (I + G T) \vert r''} \cr
    &= u(\bm{r'}) \braket{r' \vert I + G T \vert r''} \cr
    &= u(\bm{r'}) \bigg( \braket{r' \vert r''} + \int dV \braket{r' \vert G \vert r} \braket{r \vert T \vert r''} \bigg).
\end{aligned}
$$

Translation of Equation 14.21 into the integral form yields

$$ \tag{14.22}
    \mathcal{T} \big( \bm{r'}, \bm{r''} \big)
    = u(\bm{r'}) \bigg( \delta(\bm{r'} - \bm{r''}) \mathcal{I} + \int_V \mathcal{G} \big( \bm{r'}, \bm{r} \big) \mathcal{T} \big( \bm{r}, \bm{r''} \big) dV \bigg).
$$

---

Scattering Matrix...
Relative magnitude... Poynting vector... Radiative Transfer Coefficients...
Analytic solution for a non-magnetic conducting sphere (L-M)...

## III. Simple Formulas for Practical Use

[Rayleigh](https://en.wikipedia.org/wiki/Rayleigh_scattering) - [Tyndall](https://en.wikipedia.org/wiki/Tyndall_effect) - [Mie](https://en.wikipedia.org/wiki/Mie_scattering)

Inelastic [Raman scattering](https://en.wikipedia.org/wiki/Raman_scattering)

Field Magnitude <-> Wave Amplitude

Is a spherical cavity the right choice for the depolarization operator?

## Acknowledgments

Pharr & Jakob, Bohren & Huffman, NASA

## References

<!-- Modified APA style -->

1. Golubev, E. [Sampling Analytic Participating Media](/post/analytic-media/) (2020).
2. Chandrasekhar, S. [Radiative Transfer](https://doi.org/10.1002/qj.49707633016) (1950).
3. Golubev, E. [From Particle to Volume Scattering of Light](/post/particle-volume/) (2020).
4. van de Hulst, H. C. [Light Scattering by Small Particles](https://doi.org/10.1002/qj.49708436025) (1957).
5. Feynman, R. P., Leighton, R. B., & Sands, M. [The Feynman Lectures on Physics](https://www.feynmanlectures.caltech.edu/) (1965).
6. Born, M., & Wolf, E. [Principles of Optics](https://doi.org/10.1017/CBO9781139644181) (7th ed., 1999).
7. Van Bladel, J. G. [Electromagnetic Fields](https://doi.org/10.1002/047012458X) (2nd ed., 2007).
8. Ishimaru, A. [Electromagnetic Wave Propagation, Radiation, and Scattering](https://doi.org/10.1002/9781119079699) (2nd ed., 2017).
9. Mishchenko, M. I. [Electromagnetic Scattering by Particles and Particle Groups](https://doi.org/10.1017/CBO9781139019064) (2014).
10. Kong, J. A. [Electromagnetic Wave Theory](https://www.piers.org/emwave/) (2008).
11. Tsang, L., & Kong, J. A. [Scattering of Electromagnetic Waves: Advanced Topics](https://10.1002/0471224278) (2001).
12. Fleisch, D. [Student's Guide to Vectors and Tensors](https://doi.org/doi:10.1017/CBO9781139031035) (2011).
13. Talalai, G. [Derivation of the Free-Space Green’s Function](https://www.researchgate.net/publication/324729639_Derivation_of_the_Free-Space_Green's_Function_and_Radiation_from_a_Hertzian_Dipole) (2016).
99. Hansen, J. E., & Travis, L. D. [Light Scattering in Planetary Atmospheres](https://doi.org/10.1007/BF00168069) (1974).

<!--
### Polarization of Light

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
