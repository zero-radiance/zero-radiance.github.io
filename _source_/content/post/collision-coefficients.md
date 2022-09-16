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

1. We describe the position, orientation, shape, size, and composition of an individual particle.
2. We describe radiation incident on the particle using Maxwell's electromagnetic wave theory.
3. The incident wave interacts with the particle, producing a scattered wave.
4. Given the description of the incident and the scattered waves, we find the scattering and extinction efficiencies of the particle.
5. Using the distribution of particles within the volume element, we compute the volume collision coefficients.

You may wonder why we have to use *wave optics*. One of the reasons is that *geometrical optics* fails to model light transport when the size of the geometric features is on the order of the wavelength of light. However, to obtain the correct solution, we must employ wave optics even when dealing with large particles. Below, I have reproduced the statement of Hendrik van de Hulst on the matter \[[4](#references) (ch. 12.1)\].

{{< figure src="/img/geom_vs_wave.png">}}

It is not expected that you understand the prior statement fully before reading the rest of the article. However, it should (hopefully) encourage you to learn a little bit about wave optics. A modest introduction is given below.

<!--

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

Since the electromagnetic field *interacts* only with charged particles \[[28](#references) (ch. 1.3)\], the matter is represented by a *continuous* distribution of the [charge density](https://en.wikipedia.org/wiki/Charge_density) \\(\rho\\) \[[28](#references) (ch. 1.4)\], such that the total amount of charge \\(Q\\) inside the volume \\(V\\) is

$$ \tag{1.5}
	Q(V, t) = \int\_{V} \rho(\bm{r}, t) dV.
$$

Moving charges constitute a current. If their mean velocity is \\(\bm{v}\\), the *volume* [current density](https://en.wikipedia.org/wiki/Current_density) \\(\bm{J}\\) is simply

$$ \tag{1.6}
	\bm{J}(\bm{r}, t) = \rho(\bm{r}, t) \bm{v}(\bm{r}, t).
$$

Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, the difference of the divergence of Eqn. 1.1.3 and the time derivative of Eqn. 1.1.4 expresses [conservation of charge](https://en.wikipedia.org/wiki/Charge_conservation):

$$ \tag{1.7}
	\nabla \cdot \bm{J}(\bm{r}, t) = - \frac{\partial}{\partial t} \rho(\bm{r}, t).
$$

Eqn. 1.7 is a [continuity equation](https://en.wikipedia.org/wiki/Continuity_equation) that tells us that a reduction of the charge density is observed if the charges are carried away by a current.

Sometimes, Eqn. 1.1.1-1.1.4 are referred to as the Maxwell equations *in vacuum*. This name highlights the fact that these equations are based on the atomic theory of matter as a collection of particles (electrons, protons, neutrons, etc.) embedded in vacuum. It also explains the presence of the "vacuum factors" \\(\epsilon_0\\) and \\(\mu\_0\\).

The fields generated by such *microscopic* objects have extremely high spatial frequency. In addition, certain effects are not present (or cannot be detected) when measurements are performed at the *macroscopic* scale. Effectively, due to the sheer number of charges in bulk matter, you sometimes cannot see the forest for the trees.

This leads us to the idea of *Lorentz averaging*: we determine the values of the microscopic fields using Eqn. 1.1.1-1.1.4, and compute their spatial average

$$ \tag{1.8}
	\bm{E}(\bm{r}, t) = \frac{1}{V} \int\_{V} \bm{E\_{\mu}}(\bm{r} + \bm{r'}, t) dV'
$$

over a region that is sufficiently large to contain a great number of atoms \[[28](#references) (ch. 3.1)\].

Application of this technique allows us to transform the *microscopic* Maxwell equations in vacuum into the *macroscopic* Maxwell equations in bulk matter. The former set of equations is often used in theoretical physics, while the latter is dominant in applied branches, such as [wave optics](https://en.wikipedia.org/wiki/Physical_optics).

Because Eqn. 1.1.1-1.1.2 are independent of the description of matter, and are also linear, a linear transformation (such as the one given by Eqn. 1.8) does not change their form, since we may change the order of operations (move the derivatives inside or outside the integral). On the other hand, Eqn. 1.1.3-1.1.4 are defined in terms of current and charge densities, and must be modified.

First, we identify two types of charges - bound and free. As the name suggests, *free* charges are able to move around the material, the classic example being the conduction current in a copper wire.

$$ \tag{1.9}
	\bm{J} = \bm{J\_b} + \bm{J\_f}, \quad
	\rho   = \rho\_b + \rho\_f.
$$

 The *bound* [current](https://en.wikipedia.org/wiki/Current_density#Polarization_and_magnetization_currents) and [charge densities](https://en.wikipedia.org/wiki/Charge_density#Bound_charge), \\(\bm{J\_b}\\) and \\(\rho\_b\\), are more difficult to reason about. A short, but unsatisfactory, explanation is that oscillating charges constitute (electric) *polarization* currents \\(\bm{J\_p}\\), and spinning or orbiting charges charges are attributed to *magnetization* (or magnetic polarization) currents \\(\bm{J\_m}\\). They can be characterized in terms of [polarization of matter](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization):

$$ \tag{1.10}
\begin{aligned}
	&\bm{J\_b}(\bm{r}, t) = \bm{J\_p}(\bm{r}, t) + \bm{J\_m}(\bm{r}, t) =
	\frac{\partial}{\partial t} \bm{P}(\bm{r}, t) + \nabla \times \bm{M}(\bm{r}, t), \cr
	&\rho\_b(\bm{r}, t) = -\nabla \cdot \bm{P}(\bm{r}, t),
\end{aligned}
$$

where \\(\bm{P}\\) is the [polarization](https://en.wikipedia.org/wiki/Polarization_density) (electric dipole moment per unit volume) and \\(\bm{M}\\) is the [magnetization](https://en.wikipedia.org/wiki/Magnetization) (magnetic dipole moment per unit volume). We shall learn more about them in due course. Note that, according to the [Gauss law](https://en.wikipedia.org/wiki/Gauss%27s_law_for_magnetism) given by Eqn. 1.1.2, no magnetic charges exist; thus, \\(\rho\_b\\) is often called the (electric) *polarization* charge density.

Substitution of Eqn. 1.9.1, 1.10.1 into 1.1.3 and Eqn. 1.9.2, 1.10.2 into 1.1.4 yields

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

substitute them into Eqn. 1.12.1-1.12.2, and group the resulting expressions with Eqn. 1.1.1-1.1.2, we obtain the [macroscopic formulation](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Macroscopic_formulation) of the Maxwell equations \[[6](#references) (ch. 1.1.1), [7](#references) (ch. 2.1), [8](#references) (ch. 2.1)\]:

$$ \tag{1.14}
\begin{aligned}
	&\nabla \times \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) = 0, &
	&\nabla \cdot  \bm{B}(\bm{r}, t) = 0, \cr
	&\nabla \times \bm{H}(\bm{r}, t) - \frac{\partial}{\partial t} \bm{D}(\bm{r}, t) = \bm{J\_f}(\bm{r}, t), &
	&\nabla \cdot  \bm{D}(\bm{r}, t) = \rho\_f(\bm{r}, t).
\end{aligned}
$$

As discussed in \[[5](#references) (vol. II, ch. 32.2), [6](#references) (ch. 1.1.1)\], \\(\bm{E}\\) and \\(\bm{B}\\) are the basic fields, and \\(\bm{D}\\) and \\(\bm{H}\\) are the [auxiliary fields](https://en.wikipedia.org/wiki/Maxwell%27s_equations#Auxiliary_fields,_polarization_and_magnetization) that arise due to the influence of matter. The most fundamental description of the electromagnetic field is given by the potentials \\(\bm{A}\\) and \\(\phi\\) \[[28](#references) (ch. 1.1)\]; we shall defer their formal introduction to Sec. 8.

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

- the *edge condition* says that the amount of electromagnetic energy stored near an edge must be finite even if the magnitude of the field (the energy density) at the edge becomes infinite;

- the *regularity condition* bounds the values of \\(r \bm{E}\\) and \\(r \bm{B}\\) as the distance from the sources \\(r \to \infin\\);

- the [radiation condition](https://en.wikipedia.org/wiki/Sommerfeld_radiation_condition) prohibits the existence of sinks collecting (an infinite amount of) energy arriving from infinity -- these "infinite" sinks must be "finite" sources instead.

Finally, it is worth pointing out that the Maxwell equations (both 1.1 and 1.14) are not independent \[[17](#references) (ch. 1.3)\]. For instance, take the divergence of Eqn. 1.14.1 and 1.14.3. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, the resulting expression may be simplified to

$$ \tag{1.18}
	\nabla \cdot \bigg( \frac{\partial}{\partial t} \bm{B}(\bm{r}, t) \bigg) = 0,
	\quad
	\nabla \cdot \bigg( \frac{\partial}{\partial t} \bm{D}(\bm{r}, t) \bigg)
	= -\nabla \cdot \bm{J\_f}(\bm{r}, t)
	= \frac{\partial}{\partial t} \rho_f(\bm{r}, t),
$$

where the last equality is a consequence of the conservation of charge (Eqn. 1.7).

At all ordinary points, all fields and their first derivatives are assumed to be continuous. That allows us to interchange the order of the space and the time derivatives to obtain

$$ \tag{1.19}
	\frac{\partial}{\partial t} \bigg( \nabla \cdot \bm{B}(\bm{r}, t) \bigg) = 0,
	\quad
	\frac{\partial}{\partial t} \bigg( \nabla \cdot \bm{D}(\bm{r}, t) \bigg)
	= \frac{\partial}{\partial t} \rho_f(\bm{r}, t).
$$

The first equation tells us that the divergence of the magnetic field is does not change over time. Assuming that the sources of the field have existed for a finite amount of time, this constant is zero, which is consistent with Eqn. 1.14.2. Using similar logic, the constant of integration of Eqn. 1.19 is also zero, thus reproducing Eqn. 1.14.4.

We take the Maxwell equations as axioms, and use them as the foundation on which we base the theory presented in the following sections.

### Lorentz Force and Poynting Theorem

Given a mathematical description of electromagnetic radiation in terms of vector fields, we would like to physically characterize it as an energy transfer process. In order to do that, we have to determine how much energy there is in a given volume element of space, and also the rate of energy flow \[[5](#references) (vol. II, ch. 27), [6](#references) (ch. 1.1.4), [7](#references) (ch. 2.11), [8](#references) (ch. 2.4)\].

[Conservation of energy](https://en.wikipedia.org/wiki/Conservation_of_energy) is one of the most important principles of physics. Here is one way to state it: the difference between the amount of external energy \\(\mathcal{E\_{ext}}\\) flowing into the volume \\(V\\) and the amount of internal energy \\(\mathcal{E\_{int}}\\) flowing out equals the amount of work \\(\mathcal{W}\\) done inside.

$$ \tag{2.1}
	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) -
	\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
	\frac{\partial}{\partial t} \mathcal{W}(V, t).
$$

In Eqn. 2.1, \\(\mathcal{E}\\) refers to a single type of energy: kinetic, electromagnetic, etc. Work transforms a portion of \\(\mathcal{E\_{ext}}\\) into a different type of energy: kinetic into potential, electromagnetic into thermal, and so on. If we account for all types of energy, the total amount of energy is conserved.

Given our focus on electromagnetic energy, we can partition the total amount of energy into the *field energy* and the *matter energy*. In this context, work done by the field on the matter refers to *absorption*, and has a positive sign. Similarly, *emission* is the work done by the matter on the field, and has a negative sign. We shall not consider emissive materials in the analysis presented below.

Mathematically, the [rate of doing work](https://en.wikipedia.org/wiki/Work_(physics)#Mathematical_calculation) is the inner product of the force and the velocity:

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

Since the current density is just the number of moving charges per unit volume (see Eqn. 1.6),

$$ \tag{2.6}
	\frac{\partial}{\partial t} \mathcal{W}(V, t) =
	\int\_{V} \bm{E}(\bm{r}, t) \cdot \bm{J}(\bm{r}, t) dV.
$$

The Maxwell equations allow us to relate a current to the generated fields. Substitution of Eqn. 1.1.3 yields

$$ \tag{2.7}
	\int\_{V} \bm{E} \cdot \bm{J} dV =
	\int\_{V} \bm{E} \cdot \Big( \nabla \times \big( \mu\_0^{-1} \bm{B} \big) -
	\frac{\partial}{\partial t} \big( \epsilon_0 \bm{E} \big) \Big) dV.
$$

Since [the divergence of the cross product](https://en.wikipedia.org/wiki/Vector_calculus_identities#Cross_product_rule) is

$$ \tag{2.8}
	\nabla \cdot (\bm{E} \times \bm{B}) = (\nabla \times \bm{E}) \cdot \bm{B} - \bm{E} \cdot (\nabla \times \bm{B}),
$$

we can reformulate the integrand of Eqn. 2.7 as

$$ \tag{2.9}
	\bm{E} \cdot \bm{J} =
	\mu\_0^{-1} \big( (\nabla \times \bm{E}) \cdot \bm{B} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
	\epsilon_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

The curl of \\(\bm{E}\\) is also given by the Maxwell equations (see Eqn. 1.1.1). Then it follows that

$$ \tag{2.10}
	\bm{E} \cdot \bm{J} =
	\mu\_0^{-1} \big( {-\bm{B}} \cdot \frac{\partial \bm{B}}{\partial t} - \nabla \cdot (\bm{E} \times \bm{B}) \big) -
	\epsilon_0 \bm{E} \cdot \frac{\partial \bm{E}}{\partial t}.
$$

Moving the inner products under the derivative sign and grouping the derivatives produces a simpler expression

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
	\frac{\partial}{\partial t} \int\_{V} \Big( \frac{\epsilon_0}{2} \left| \bm{E} \right|^2 + \frac{\mu\_0^{-1}}{2} \left| \bm{B} \right|^2 \Big) dV
$$

and use the [divergence theorem](https://en.wikipedia.org/wiki/Divergence_theorem) to replace the leftmost volume integral with an integral taken over the bounding surface \\(\partial V\\) of the volume \\(V\\):

$$ \tag{2.13}
	\frac{\partial \mathcal{W}}{\partial t}
	= \oint\_{\partial V} \Big( \mu\_0^{-1} (\bm{E} \times \bm{B}) \cdot (-\bm{n}) \Big) dA -
	\frac{\partial}{\partial t} \int\_{V} \Big( \frac{\epsilon_0}{2} \left| \bm{E} \right|^2 + \frac{\mu\_0^{-1}}{2} \left| \bm{B} \right|^2 \Big) dV,
$$

where \\(\bm{n}\\) is the outward-facing surface normal.

According to Eqn. 2.1, the right-hand side of Eqn. 2.13 represents the difference between the rates of inflow and outflow of energy. Thus, the first term gives the amount of external energy (per unit time) flowing through the bounding surface into the volume,

$$ \tag{2.14}
	\frac{\partial}{\partial t} \mathcal{E\_{ext}}(V, t) =
	\oint\_{\partial V} \Big( \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \cdot (-\bm{n}) \Big) dA,
$$

and the second term corresponds to the rate at which the amount of energy within the volume decreases:

$$ \tag{2.15}
	-\frac{\partial}{\partial t} \mathcal{E\_{int}}(V, t) =
	-\frac{\partial}{\partial t} \int\_{V} \Big( \frac{\epsilon_0}{2} \left| \bm{E}(\bm{r}, t) \right|^2 + \frac{\mu\_0^{-1}}{2} \left| \bm{B}(\bm{r}, t) \right|^2 \Big) dV.
$$

According to this interpretation, the *squared* magnitudes of the fields

$$ \tag{2.16}
	\frac{\partial}{\partial V} \mathcal{E\_e}(V, t) = \frac{\epsilon_0}{2} \left| \bm{E}(\bm{r}, t) \right|^2, \quad
	\frac{\partial}{\partial V} \mathcal{E\_m}(V, t) = \frac{\mu\_0^{-1}}{2} \left| \bm{B}(\bm{r}, t) \right|^2, \quad
$$

are the electric and the magnetic energy densities, and

$$ \tag{2.17}
	\bm{S}(\bm{r}, t) = \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
$$

is the *instantaneous* [Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector) that characterizes the direction, the density, and the rate of energy flow.

Definitions given in Eqn. 2.16-2.17 transform Eqn. 2.11 into a statement of the [Poynting theorem](https://en.wikipedia.org/wiki/Poynting%27s_theorem):

$$ \tag{2.18}
	\frac{\partial^2 \mathcal{W}}{\partial V \partial t} =
	-\nabla \cdot \bm{S} -
	\frac{\partial^2 \mathcal{E\_{em}}}{\partial V \partial t}.
$$

A close examination of 2.18 reveals the fact that our definitions of the energy density and the Poynting vector are not unique, since one may also express the latter as

$$ \tag{2.19}
	\bm{S'}(\bm{r}, t) = \bm{S}(\bm{r}, t) + \nabla \times \bm{X}(\bm{r}, t)
$$

without violating the Poynting theorem \[[5](#references) (vol. II, ch. 27.4)\]. However, in practice, it does not pose a problem, since it is not actually possible to measure the energy *density* (or its flow) directly, and if one measures the *amount* (or its time rate) of electromagnetic energy (using Eqn. 2.14-2.15), the spatial integration process eliminates the curl term and makes the measurable quantities invariant with respect to this transformation [\[8](#references) (ch. 2.4, 11)\]. Moreover, due to the [mass-energy equivalence](https://en.wikipedia.org/wiki/Mass%E2%80%93energy_equivalence) principle, modifications of the definition of the energy density alter the gravitational field, which has observable consequences; in particular, the expansion of the universe appears to be consistent with the definitions given by Eqn. 2.16-2.17 \[[28](#references) (ch. 1.2)\].

A more pressing concern is whether our formulae, which were found using the microscopic formulation of the Maxwell equations, are valid at the microscopic scale. The answer is: almost always.

A typical derivation[^5] replaces \\(\bm{J}\\) with \\(\bm{J_f}\\) in Eqn. 2.6, which, after substitution of Eqn. 1.14.3, leads to the macroscopic counterpart of Eqn. 2.7 \[[6](#references) (ch. 1.1.4), [8](#references) (ch. 2.4), [28](#references) (ch. 6.1)\]:

[^5]: Work done on the bound current is implicit, since the latter is used to define \\(\bm{D}\\) and \\(\bm{H}\\).

$$ \tag{2.20}
	\int\_{V} \bm{E} \cdot \bm{J_f} \thinspace dV =
	\int\_{V} \bm{E} \cdot \bigg( \nabla \times \bm{H} - \frac{\partial \bm{D}}{\partial t} \bigg) dV.
$$

After performing the same transformations as in Eqn. 2.8-2.11, we arrive at the expression

$$ \tag{2.21}
	\bm{E} \cdot \bm{J_f} =
	-\nabla \cdot (\bm{E} \times \bm{H}) -
	\frac{\partial}{\partial t} \bigg(
	\frac{1}{2} \bm{E} \cdot \bm{D} +
	\frac{1}{2} \bm{B} \cdot \bm{H} \bigg)
$$

that matches the form required by the Poynting theorem.

Thus, at the macroscopic level, we can define the Poynting vector as

$$ \tag{2.22}
	\bm{S}(\bm{r}, t) = \bm{E}(\bm{r}, t) \times \bm{H}(\bm{r}, t)
$$

with the corresponding electromagnetic energy density

$$ \tag{2.23}
	\frac{\partial}{\partial V} \mathcal{E\_e}(V, t) = \frac{1}{2} \bm{E}(\bm{r}, t) \cdot \bm{D}(\bm{r}, t), \quad
	\frac{\partial}{\partial V} \mathcal{E\_m}(V, t) = \frac{1}{2} \bm{B}(\bm{r}, t) \cdot \bm{H}(\bm{r}, t), \quad
$$

Comparison of Eqn. 2.17 and 2.22 shows that both formulations of the Poynting vector are equivalent if the measurement is performed inside a *non-magnetic* material. That explains the \\(\mu\_0^{-1}\\) factor: since classical electrodynamics does not explicitly model the intrinsic properties of the elementary particles responsible for magnetism, the microscopic formulation of the Maxwell equations assumes that the observer is located in a potentially charged, yet non-magnetic region of vacuum.

Since electromagnetic fields oscillate so rapidly, one typically measures the *time-averaged* Poynting vector

$$ \tag{2.24}
	\braket{\bm{S}}
	= \frac{1}{T} \int\_{-T/2}^{\thinspace T/2} \bm{S}(\bm{r}, t + t') dt'.
$$

where \\(T\\) is the duration of the measurement, or the [exposure time](https://en.wikipedia.org/wiki/Shutter_speed).

It becomes particularly useful once you consider a fixed direction \\(\bm{n}\\). Then, according to Eqn. 2.14,

$$ \tag{2.25}
	\Epsilon(\bm{n})
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

We can define the integral forms of the fields by replacing \\(\bm{E}\\) by \\(\bm{B}\\), \\(\bm{D}\\), \\(\bm{H}\\), \\(\bm{J\_f}\\) or \\(\rho\_f\\) in Eqn. 3.4. If we substitute these integrals into Eqn. 1.14.1-1.14.4, the result is a system of integro-differential equations

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

Finally, observe that we only need to find the expression of the electric field. From Eqn. 3.9.1 and 3.10.1, it follows that

$$ \tag{3.12}
	\bm{B}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla \times \bm{E}(\bm{r}, \omega).
$$

In certain situations, it may be advantageous to do it the other way around: find the expression of the magnetic field first, and then use Eqn. 3.9.3 or 3.10.3 to calculate

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

We can obtain a more practical result at the cost of some generality. Specifically, we must assume that the expression of the electromagnetic field is a [periodic function](https://en.wikipedia.org/wiki/Periodic_function) of time, such that the *fundamental period* \\(\thinspace T\_1 = 2 \pi / \omega_1\\) of both[^3] the electric and the magnetic fields is the same \[[5](#references) (vol. I, ch. 50) [6](#references) (ch. 1.3.3, 1.4.3)\]. In most cases, the limitations imposed by this condition are not particularly severe: usually, for visible light, the fundamental period is very short, and the fields can often be virtually repeated outside the time interval of interest.

[^3]: The fact that the electric and the magnetic fields cannot exist independently can be shown by a simple relativistic argument \[[13](#references) (ch. 6.2)\].

One particular feature of periodic functions is the existence of a [Fourier series](https://en.wikipedia.org/wiki/Fourier_series#Complex-valued_functions) representation:

$$ \tag{4.4}
	\bm{E}(\bm{r}, t)
	= \frac{1}{2} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{-i p \omega_1 t}
	= \frac{1}{2} \sum\_{p = -\infin}^{\infin} \bm{E_p}(\bm{r}) e^{-i \omega_p t}
	= \frac{1}{2} \sum\_{p = -\infin}^{\infin} \ket{u_p} \braket{u_p | \bm{E}}.
$$

where we defined \\(\omega_p = p \thinspace \omega_1\\), with the *Fourier coefficients* \\(\bm{E_p}\\) given by the integral[^8]

[^8]: In order to obtain the same results found in the physics literature, we had to multiply the series by 1/2, and the coefficients by 2.

$$ \tag{4.5}
	\bm{E_p}(\bm{r})
	= \frac{2}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} \bm{E}(\bm{r}, t) e^{i \omega_p t} dt
	= 2 \braket{u_p | \bm{E}}.
$$

Eqn. 4.5 can be interpreted as the [projection](https://en.wikipedia.org/wiki/Hilbert_space#Fourier_analysis) onto the discrete [Fourier basis](https://en.wikipedia.org/wiki/Fourier_series#Hilbert_space_interpretation), with Eqn. 4.4 showing the reconstruction. The individual elements of Eqn. 4.4 (called *harmonics*) possess a key property encapsulated in the *orthonormalization relation*

$$ \tag{4.6}
	\braket{u_p | u_q}
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} e^{i (\omega_p - \omega_q) t} dt
	= \delta_{p,q}
$$

which serves as an integral representation of the [Kronecker delta](https://en.wikipedia.org/wiki/Kronecker_delta#Integral_representations) function \\(\delta_{p,q}\\).

Similarly to Eqn. 3.2, if the time-domain field is real,

$$ \tag{4.7}
	\bm{E_p}(\bm{r}) = [\bm{E_{-p}}(\bm{r})]^{\*},
$$

which permits us to simplify Eqn. 4.4:

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

What happens when we Fourier transform a periodic function? Combining Eqn. 3.1 and 4.4,

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

[^13]: Most authors abuse the notation by writing Eqn. 4.11 as \\(\mathcal{Re} \big\lbrace \bm{E}(\bm{r}) e^{-i \omega t} \big\rbrace\\) or \\(\mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace\\).

$$ \tag{4.11}
	\bm{E}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}) e^{-i \omega_p t} \big\rbrace,
$$

in isolation, with the corresponding phasor fields

$$ \tag{4.12}
	\bm{E}(\bm{r}, \omega)
	= \sqrt{\frac{\pi}{2}} \bigg( \bm{E_p}(\bm{r}) \delta(\omega - \omega_p) + \big[ \bm{E_p}(\bm{r}) \big]^{\*} \delta(\omega + \omega_p) \bigg).
$$

In practice, it is not necessary to solve the Maxwell equations for the complex conjugate. We simply take \\(\bm{E_p}(\bm{r}) \delta(\omega - \omega_p)\\) and substitute it in place of \\(\bm{E}(\bm{r}, \omega)\\) into Eqn. 3.7. As a result, we obtain a system formally equivalent to Eqn. 3.9, with \\(\bm{E}(\bm{r}, \omega)\\) replaced by \\(\bm{E_p}(\bm{r})\\), and \\(\omega\\) by \\(\omega_p\\). After solving for \\(\bm{E_p}(\bm{r})\\), we use Eqn. 4.11 to obtain the expression of the real electric vector \\(\bm{E}(\bm{r}, t)\\).

Let us put the math to work. Assuming that the electromagnetic field is time-harmonic, Eqn. 2.22 of the Poynting vector can be written as

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

The corresponding expression of the time-averaged Poynting vector (cf. Eqn. 2.24) is

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

In the case when \\(T > T_1\\), the total value of \\(\braket{\bm{S}}\\) is a sum of the contribution from a number of whole periods (given by Eqn. 4.15) and the contribution from a fraction of the period. If the duration of a measurement is very large \\((T \gg T_1)\\), the *relative* contribution from a fraction of the period will be much smaller than the *relative* contribution from a large number of whole periods, so the formula of Eqn. 4.15 should serve as a good approximation.

Eqn. 4.15 allows us to define the *time-averaged* Poynting phasor \[[8](#references) (ch. 2.4)\]

$$ \tag{4.16}
	\braket{\bm{S_p}} = \frac{1}{2} \bm{E_p}(\bm{r}) \times \big[ \bm{H_p}(\bm{r}) \big]^{\*}
$$

such that (cf. Eqn. 4.8)

$$ \tag{4.17}
	\braket{\bm{S}}
	= \frac{1}{2} \braket{\bm{S_0}}
	+ \sum\_{p = 1}^{\infin} \mathcal{Re} \big\lbrace \negthinspace \braket{\bm{S_p}} \negthinspace \big\rbrace.
$$

It produces a more compact expression of irradiance

$$ \tag{4.18}
	\Epsilon(\bm{n})
	= \braket{\bm{S}} \cdot \bm{n}
	= \frac{1}{2} \braket{\bm{S_0}} \cdot \bm{n}
	+ \sum\_{p = 1}^{\infin} \mathcal{Re} \big\lbrace
		\negthinspace \braket{\bm{S_p}} \negthinspace
	  \big\rbrace \cdot \bm{n}
$$

that can be written in terms of [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance)

$$ \tag{4.19}
	\Epsilon_p(\bm{n})
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

[^7]: Eqn. 5.2.1-5.2.3 make a big assumption that is not appropriate for certain types of real materials. For instance, [crystals](https://en.wikipedia.org/wiki/Crystal) have a well-defined [internal structure](https://en.wikipedia.org/wiki/Crystal_structure) which makes them inherently anisotropic \[[5](#references) (vol. II, ch. 30)\]. Since our application is primarily concerned with simple fluids, a linear, isotropic material is reasonable assumption that offers significant theoretical advantages (such as simplicity). However, this assumption is potentially not suitable for a more general application \[[6](#references) (ch. 2)\].

$$ \tag{5.2}
\begin{aligned}
	&\bm{J\_i}(\bm{r}, \omega) \approx \sigma(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{D}   (\bm{r}, \omega) \approx \epsilon(\bm{r}, \omega) \bm{E}(\bm{r}, \omega), \cr
	&\bm{H}   (\bm{r}, \omega) \approx \mu^{-1}(\bm{r}, \omega) \bm{B}(\bm{r}, \omega),
\end{aligned}
$$

where \\(\sigma\\) is the [specific conductivity](https://en.wikipedia.org/wiki/Electrical_resistivity_and_conductivity), \\(\epsilon\\) is the [electric permittivity](https://en.wikipedia.org/wiki/Permittivity), and \\(\mu\\) is the [magnetic permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)) \[[6](#references) (ch. 1.1.2), [7](#references) (ch. 2.3), [8](#references) (ch. 2.1)\]. Note that the reciprocal of the permeability is used -- it is a historical accident caused by the confusion over whether \\(\bm{H}\\) or \\(\bm{B}\\) is the fundamental magnetic field.

Care must taken when transforming Eqn. 5.2.1-5.2.3 to the time domain, since a multiplication in the frequency domain leads to a convolution in the time domain. There, Eqn. 5.2.1-5.2.3 take the form

$$ \tag{5.?}
	\bm{D}(\bm{r}, t) = \frac{1}{\sqrt{2 \pi}} \int_{-\infin}^{\infin} \epsilon(\bm{r}, t - t') \bm{E}(\bm{r}, t') dt',
$$

which indicates that these materials exhibit a *delayed response* \[[28](#references) (ch. 6.2)\].

Conservation of charge implies that

$$ \tag{5.3}
	\rho_i(\bm{r}, \omega) = \frac{1}{i \omega} \nabla \cdot \bm{J_i}(\bm{r}, \omega).
$$

Eqn. 5.2.1 and 5.3 assume that the *free* charges and currents may be decomposed into the *source* and the *induced* parts[^23]:

[^23]: Sometimes, induced charges are called unbound, and source charges are called free. For a detailed discussion, see \[[28](#references) (ch. 6)\].

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

Writing the Maxwell equations this way allows us to compare the macroscopic Eqn. 5.8 with the microscopic Eqn. 3.10. Observe that, when \\(\varepsilon\\) and \\(\mu\\) are independent from \\(\bm{r}\\) (or if the spatially-varying component is attributed to the current term on the right-hand side), their solutions are formally the same.

### Helmholtz Equation

Consider a region of space without any source charges or currents. Physically, this means that the region is *source-free*; but that does not mean it contains no energy. To find an expression of the internal field, we shall explore all general solutions of the Maxwell equations (we can find a special solution once we specify the boundary conditions), and determine how these solutions evolve over time.

Setting \\(\bm{J\_s} = \rho\_s = 0\\) in Eqn. 5.7.3-5.7.4, we obtain a coupled system of linear homogeneous equations:

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

Let us focus on the electric field \\(\bm{E}\\). Take the curl of Eqn. 6.2.1 and substitute \\(\nabla \times \bm{H}\\) from Eqn. 6.2.3:

$$ \tag{6.3}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - \omega^2 \mu(\omega) \varepsilon(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

It is convenient to group the constants by defining

$$ \tag{6.4}
	k(\omega) = \omega \sqrt{\mu(\omega) \varepsilon(\omega)},
$$

so that the Eqn. 6.3 can be written as

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

Use Eqn. 6.6 to expand Eqn. 6.5:

$$ \tag{6.9}
	\nabla \big( \nabla \cdot \bm{E}(\bm{r}, \omega) \big) - \nabla^2 \bm{E}(\bm{r}, \omega) - k^2(\omega) \bm{E}(\bm{r}, \omega) = 0.
$$

Substitution of Eqn. 6.2.4 into 6.9 yields the vector equation of the electric field \\(\bm{E}\\):

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

How should we choose the direction of \\(\bm{n}\\)? It depends on the location of sources. Unless they are explicitly specified, we can use *any* value of \\(\bm{n}\\). And because the Helmholtz equation is both linear and homogeneous, we can actually use *every* value of \\(\bm{n}\\) by invoking the superposition principle. Thus, the general solution is an integral taken over the surface of the unit sphere \\(\mathbb{S}^2\\) [measured](https://en.wikipedia.org/wiki/Lebesgue_integration#Construction) by the [solid angle](https://en.wikipedia.org/wiki/Solid_angle) \\(d\Omega\\):

$$ \tag{6.15}
	\psi(\bm{r}) =
	\oint\_{\mathbb{S}^2} \psi(\bm{r}, \bm{n}) d\Omega =
	\oint\_{\mathbb{S}^2} \psi(0, \bm{n}) e^{i k (\bm{r} \cdot \bm{n})} d\Omega.
$$

Eqn. 6.15 can be used to solve Eqn. 6.10:

$$ \tag{6.16}
	\bm{E}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E}(\bm{r}, \bm{n}, \omega) d\Omega =
	\oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n})} d\Omega.
$$

That is a general solution of the Maxwell equations for *linear, isotropic, homogeneous, source-free* media.

### Plane waves

We can find a solution of the Maxwell equations in the time domain by performing the inverse Fourier transform of Eqn. 6.16:

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
	k(\omega)
	= \omega \sqrt{\varepsilon(\omega) \mu(\omega)}
	= \omega \sqrt{\left( \epsilon(\omega) + i \frac{\sigma(\omega)}{\omega} \right) \mu(\omega)}.
$$

\\(\bm{E_0}\\) is called the *polarization phasor* of the plane wave (for reasons that go beyond the scope of this article).

If we define the [relative complex permittivity](https://en.wikipedia.org/wiki/Relative_permittivity#Lossy_medium) \\(\varepsilon\_r\\) and the [relative permeability](https://en.wikipedia.org/wiki/Permeability_(electromagnetism)#Relative_permeability_and_magnetic_susceptibility) \\(\mu\_r\\) using the vacuum as reference,

$$ \tag{7.5}
	\varepsilon\_r(\omega) = \frac{\varepsilon(\omega)}{\epsilon_0}, \quad
	\mu\_r(\omega) = \frac{\mu(\omega)}{\mu\_0},
$$

the complex wavenumber can be redefined as

$$ \tag{7.6}
	k(\omega) =
	\omega \sqrt{\epsilon_0 \mu\_0} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)} =
	\frac{\omega}{c} \sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)}.
$$

It is convenient to use a parametrization that does not involve taking a square root. Thus, we define two positive real numbers, the [refractive index](https://en.wikipedia.org/wiki/Refractive_index) \\(\eta\\) and the [attenuation index](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\kappa\\) \[[6](#references) (ch. 14.1), [8](#references) (ch. 3.1)\], by

$$ \tag{7.7}
	\eta(\omega) + i \kappa(\omega) =
	\sqrt{\varepsilon\_r(\omega) \mu\_r(\omega)}.
$$

This expression is sometimes called the *complex refractive index*.

For high frequencies, such as those encountered in optics, we may perform a [Laurent series](https://en.wikipedia.org/wiki/Laurent_series) [expansion](https://www.wolframalpha.com/input/?i=series+sqrt%28a-I%2Fw*b%29) at infinity:

$$ \tag{7.8}
	\eta(\omega) + i \kappa(\omega) \simeq
	c \bigg( \sqrt{\epsilon \mu} + i \frac{\sigma \mu}{2 \omega \sqrt{\epsilon \mu}} \bigg),
$$

which gives an approximate mapping between the optical and the physical properties[^10].

[^10]: Keep in mind that, in general, the permittivity, permeability, and conductivity are complex numbers.

The combination of Eqn. 7.6-7.7 produces the [dispersion relation](https://en.wikipedia.org/wiki/Dispersion_relation)

$$ \tag{7.9}
	k(\omega)
	= \frac{\omega}{c} \big( \eta(\omega) + i \kappa(\omega) \big)
	= \omega \bigg( \frac{1}{v_p(\omega)} + \frac{i}{v_a(\omega)} \bigg),
$$

where \\(v_p = c/\eta\\) is the [phase velocity](https://en.wikipedia.org/wiki/Phase_velocity) and \\(v_a=c/\kappa\\) is the *amplitude velocity*. It shows that, in a dispersive medium, waves of different frequencies propagate at different rates. Higher velocities are accomplished by reducing the wavelength

$$ \tag{7.?}
	\lambda = \frac{2 \pi}{\mathcal{Re} \lbrace k \rbrace } = \frac{2 \pi c}{\omega \eta}.
$$

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

Let us perform [dimensional analysis](https://en.wikipedia.org/wiki/Dimensional_analysis) of Eqn. 7.11.

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

Taking the real part of Eqn. 7.11 allows us to uncover the *wave amplitude*

$$ \tag{7.14}
	\mathcal{Re} \big\lbrace E(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace
	= |E| \cos{\theta}
	= |E\_0| e^{-\omega (\bm{r} \cdot \bm{n}) / v_a} \cos{\theta},
$$

where \\(|E|\\) is the spatially-varying *peak amplitude*.

If the attenuation index \\(\kappa = 0\\), the amplitude velocity \\(v_a = \infin\\), and Eqn. 7.14 represents a regular sine wave.

[Insert Picture Here]

On the other hand, \\(\kappa > 0\\) produces an exponential decay characteristic of an [evanescent wave](https://en.wikipedia.org/wiki/Evanescent_field).

[Insert Picture Here]

Eqn. 7.1 can be seen as a decomposition of the electric field into plane waves interfering with each other. By [interference](https://en.wikipedia.org/wiki/Wave_interference), we simply mean that when two waves (often assumed to be of the same frequency) are added together, the peak amplitude of the resulting wave is, in general, different from the sum of the peak amplitudes unless the waves oscillate *in-phase* (in such a way that the phase difference is zero), in which case interference is said to be *constructive*. In the worst case scenario, the peaks align with the troughs, and *destructive* interference causes the waves to completely cancel each other. A typical *out-of-phase* case falls in between these two extremes \[[5](#references) (vol. I, ch. 29)\].

[Insert Picture Here]

It is easy to show that, under favorable conditions, the field vectors oscillate in the plane of the wave. In general, the Maxwell equations (cf. Eqn. 3.9.2, 3.9.4) tell us that

$$ \tag{7.15}
	\nabla \cdot \bm{B}(\bm{r}, \omega) = 0, \quad
	\nabla \cdot \bm{D}(\bm{r}, \omega) = \rho_s(\bm{r}, \omega).
$$

More specifically, in a source-free region of a linear, isotropic, homogeneous medium (cf. Eqn. 6.2.2, 6.2.4),

$$ \tag{7.16}
	\nabla \cdot \bm{H}(\bm{r}, \omega) = 0, \quad
	\nabla \cdot \bm{E}(\bm{r}, \omega) = 0.
$$

Take \\(\bm{E}\\) as an example. Substitute Eqn. 6.16 into 7.16.2:

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

We can prove that the magnetic vector possesses the same property in a similar manner. According to Eqn. 3.9.1,

$$ \tag{7.20}
	\nabla \times \bm{E}(\bm{r}, \omega) = i \omega \bm{B}(\bm{r}, \omega).
$$

Substitute Eqn. 6.16 into the left-hand side and expand the expression of the curl

$$ \tag{7.21}
	\nabla \times \bm{E}(\bm{r}, \bm{n}, \omega)
	= \Big( \nabla e^{i k(\omega) (\bm{r} \cdot \bm{n})} \Big) \times \bm{E}(0, \bm{n}, \omega)
	= i k(\omega) \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega).
$$

After division by \\(i \omega\\), Eqn. 7.20 becomes equivalent to

$$ \tag{7.22}
	\frac{k(\omega)}{\omega} \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega) = \bm{B}(\bm{r}, \bm{n}, \omega).
$$

By taking the inner product of the resulting expression with \\(\bm{n}\\), we obtain an analog of Eqn. 7.18:

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

With \\(\bm{E}\\) and \\(\bm{B}\\) both orthogonal to \\(\bm{n}\\), the only remaining question is regarding their mutual orientation. Using the definition of the wavenumber given by Eqn. 7.9, Eqn. 7.22 can be written as

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

shows us that the electric and the magnetic field vectors of a plane wave are orthogonal only if \\(\kappa = 0\\). If that is the case, \\(\lbrace \bm{E}, \bm{B}, \bm{n} \rbrace\\) is an orthogonal triad of vectors that defines the geometric configuration of a plane wave in a non-absorptive, linear, isotropic, homogeneous, source-free medium:

$$ \tag{7.27}
	\bm{n} \times \frac{\mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace}{\big| \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big|}
	= \frac{\mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace}{\big| \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big|}.
$$

[Insert Picture Here]

Recall that we have seen the \\(\bm{E} \times \bm{B}\\) expression before (cf. Eqn. 2.17). Assuming the fields are monochromatic, and the medium where we perform the measurements - non-magnetic, Eqn. 7.27 suggests that

$$ \tag{7.28}
	\bm{S}(\bm{r}, t)
	= \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big)
	= \mu\_0^{-1} |\bm{E}(\bm{r}, t)| |\bm{B}(\bm{r}, t)| \bm{n}
$$

is the expression of the Poynting vector of a plane wave.

If the field vectors are mutually orthogonal, Eqn. 7.26-7.27 define the ratio of wave amplitudes:

$$ \tag{7.29}
	\frac{\big| \mathcal{Re} \big\lbrace \bm{B}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big|}{\big| \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big|}
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
	\pm \frac{\pi}{2} \pm \pi n.
\end{aligned}
$$

Secondly, it means we can compute the Poynting vector without explicit consideration of the magnetic field:

$$ \tag{7.31}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \mu\_0^{-1} \frac{\eta(\omega)}{c} {\big| \mathcal{Re} \big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) e^{-i \omega t} \big\rbrace \big|}^2 \bm{n} \cr
	&= \mu\_0^{-1} \frac{\eta}{c} \Big( |E_{0,x}|^2 \cos^2{\theta_x} + |E_{0,y}|^2 \cos^2{\theta_y} \Big) \bm{n},
\end{aligned}
$$

where we have utilized the expression of the wave amplitude of Eqn. 7.14.

The average value of a squared cosine is \\(\frac{1}{2}\\), so the corresponding irradiance[^11] value (cf. Eqn. 2.25) is

[^11]: Since the electromagnetic fields is monochromatic, the expressions of irradiance and spectral irradiance are identical.

$$ \tag{7.32}
	\Epsilon(\bm{n'})
	= \braket{\bm{S}} \cdot \bm{n'}
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} |\bm{E_0}|^2 \big( \bm{n} \cdot \bm{n'} \big).
$$

Without proof, we shall state an interesting fact. The time-averaged energy density (cf. Eqn. 2.23-2.24) of the field of a plane wave is

$$ \tag{7.33}
	\bigg\langle \frac{\partial \mathcal{E\_{em}}}{\partial V} \bigg\rangle
	= \frac{\mu\_0^{-1}}{2} \frac{\eta^2}{c^2} |\bm{E_0}|^2.
$$

This implies that the amount of energy that goes through a unit area per unit time is the amount of energy per unit volume times the velocity at which the energy flows \[[5](#references) (vol. II, ch. 27)\]:

$$ \tag{7.34}
	\Epsilon(\bm{n'})
	= \frac{c}{\eta} \bigg\langle \frac{\partial \mathcal{E\_{em}}}{\partial V} \bigg\rangle \big( \bm{n} \cdot \bm{n'} \big).
$$

If the medium is absorptive or magnetic, the formula of the *instantaneous* Poynting vector becomes more complicated. Nevertheless, we can easily find the *time-averaged* Poynting vector using Eqn. 4.16-4.17:

$$ \tag{7.35}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \Big\lbrace \bm{E}(\bm{r}, \bm{n}, \omega) \times \big[ \bm{H}(\bm{r}, \bm{n}, \omega) \big]^{\*} \Big\rbrace.
$$

Now, according to Eqn. 5.2.3 and 7.22,

$$ \tag{7.36}
	\frac{k(\omega)}{\omega \mu(\omega)} \bm{n} \times \bm{E}(\bm{r}, \bm{n}, \omega) = \bm{H}(\bm{r}, \bm{n}, \omega).
$$

If we take the complex conjugate of Eqn. 7.36, substitute it into Eqn. 7.35, and apply the [vector triple product](https://en.wikipedia.org/wiki/Triple_product#Vector_triple_product) identity

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

From the definitions of \\(\bm{E}\\) and \\(k\\) given by Eqn. 7.2 and 7.9, respectively, it follows that

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

where \\(\lambda_0 = 2 \pi c / \omega\\) is the vacuum wavelength.

Furthermore, the original definition of the complex wavenumber given by Eqn. 7.4 suggests that

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

Finally, according to Eqn. 7.18, \\(\bm{E} \cdot \bm{n} = 0\\). Thus, if we combine Eqn. 7.38-7.41, we obtain the general expression of the time-averaged Poynting vector that accounts for absorption and magnetization:

$$ \tag{7.42}
	\braket{\bm{S}}
	= \frac{1}{2} \mathcal{Re} \bigg\lbrace
		\sqrt{ \frac{\varepsilon}{\mu} }
	\bigg\rbrace |\bm{E_0}|^2 e^{-\alpha (\bm{r} \cdot \bm{n})} \bm{n}.
$$

### Electromagnetic Potential

The solutions of the Maxwell equations we have found so far have a limited range of validity. The medium must be isotropic and either homogeneous (which prohibits scattering) or divided into several bounded homogeneous regions (which permits scattering only at the boundaries), and the material's response to the incident electromagnetic field may only be purely linear. Moreover, while the method of solving a system of differential equations augmented with boundary conditions (the so-called [boundary value problem](https://en.wikipedia.org/wiki/Boundary_value_problem)) is suitable for many simple cases (such as reflection and transmission of a plane wave at a planar interface, which leads to the [Fresnel equations](https://en.wikipedia.org/wiki/Fresnel_equations)), it quickly becomes unwieldy for more complex problems. Another approach tends to become more viable, where one reformulates the field in terms of the [electromagnetic potential](https://en.wikipedia.org/wiki/Electromagnetic_four-potential), as this leads to an integral (rather than a differential) solution of the Maxwell equations.

Take another look at the equation of the magnetic field. Since the [divergence of curl](https://en.wikipedia.org/wiki/Vector_calculus_identities#Divergence_of_curl_is_zero) is zero, Eqn. 1.1.2 can be written as

$$ \tag{8.1}
	\nabla \cdot \bm{B}(\bm{r}, t) = \nabla \cdot \big( \nabla \times \bm{A}(\bm{r}, t) \big) = 0,
$$

where \\(\bm{A}\\) is called a *vector potential* \[[5](#references) (vol. II, ch. 18), [6](#references) (ch. 2.1)\].

Next, substitute the definition of \\(\bm{A}\\) into Eqn. 1.1.1 to obtain

$$ \tag{8.2}
	\nabla \times \Big( \bm{E}(\bm{r}, t) + \frac{\partial}{\partial t} \bm{A}(\bm{r}, t) \Big) = 0.
$$

As the [curl of gradient](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_of_gradient_is_zero) is zero, Eqn. 8.2 can be used to define the *scalar potential* \\(\phi\\):

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

If we substitute Eqn. 8.5 into 8.3 and group the terms under the gradient sign, we obtain the expression of the transformed scalar potential:

$$ \tag{8.7}
	\phi'(\bm{r}, t) = \phi(\bm{r}, t) - \frac{\partial}{\partial t} \chi(\bm{r}, t).
$$

Since the electric and the magnetic field are independent of a particular choice of \\(\chi\\), they are said to be *invariant* under a [gauge transformation](https://en.wikipedia.org/wiki/Gauge_theory#Classical_electromagnetism) given by Eqn. 8.5 and 8.7.

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

The combination of Eqn. 8.9.2 and 8.10 yields the [Lorenz condition](https://en.wikipedia.org/wiki/Lorenz_gauge_condition)

$$ \tag{8.11}
\begin{aligned}
	\nabla \cdot \bm{A}(\bm{r}, t) = -\frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t).
\end{aligned}
$$

Let us justify our choice of the gauge transformation by expressing the microscopic Maxwell equations in terms of the electromagnetic potential. Begin by substituting Eqn. 8.4 into 1.1.3-1.1.4:

$$ \tag{8.12}
\begin{aligned}
	&\nabla \times \nabla \times \bm{A}(\bm{r}, t) + \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \bm{A}(\bm{r}, t)
	+ \nabla \frac{1}{c^2} \frac{\partial}{\partial t} \phi(\bm{r}, t)
	= \frac{\bm{J}(\bm{r}, t)}{\mu_0^{-1}}, \cr
	&-\frac{\partial}{\partial t} \nabla \cdot \bm{A}(\bm{r}, t) - \nabla^2 \phi(\bm{r}, t)
	= \frac{\rho(\bm{r}, t)}{\epsilon_0}.
\end{aligned}
$$

We can immediately use the Lorenz condition to eliminate \\(\bm{A}\\) from Eqn. 8.12.2. The result is an inhomogeneous scalar *wave equation*

$$ \tag{8.13}
	\nabla^2 \phi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \phi(\bm{r}, t)
	= -\frac{\rho(\bm{r}, t)}{\epsilon_0}.
$$

Eqn. 8.12.1 can be expanded using the curl of curl identity given by Eqn. 6.6:

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

Compare Eqn. 8.16.3 to a single component of Eqn. 8.16.1 in the Cartesian coordinate system. Both have the same form

$$ \tag{9.1}
	\nabla^2 \psi(\bm{r}, t) - \frac{1}{c^2} \frac{\partial^2}{\partial t^2} \psi(\bm{r}, t)
	= -\xi(\bm{r}, t).
$$

Formally, Eqn. 9.1 describes the behavior of electromagnetic waves generated by sources in the vacuum of [free space](https://en.wikipedia.org/wiki/Vacuum#Electromagnetism). In this context, \\(\xi\\) is a *source function*, \\(\psi\\) is a *wave function*, and \\(c\\) is the *velocity* of the waves.

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

Since the Fourier transform is uniquely defined, we may remove the integral sign:

$$ \tag{9.4}
	\big( \nabla^2 + \omega^2 / c^2 \big) \psi(\bm{r}, \omega)
	= -\xi(\bm{r}, \omega).
$$

Thus we are lead to consider the *inhomogeneous* [Helmholtz equation](https://en.wikipedia.org/wiki/Helmholtz_equation#Inhomogeneous_Helmholtz_equation)

$$ \tag{9.5}
	\big( \nabla^2 + k_0^2 \big) \psi(\bm{r}) = -\xi(\bm{r}),
$$

where \\(k_0 = \omega / c\\) is the vacuum wavenumber.

The Helmholtz operator \\(\mathcal{H} = \nabla^2 + k_0^2\\) is a linear operator in 3 dimensions. Therefore, Eqn. 9.5 represents a linear transformation

$$ \tag{9.6}
	\mathcal{H} \big\lbrace \psi(\bm{r}) \big\rbrace = -\xi(\bm{r}).
$$

Unfortunately, unlike its homogeneous counterpart (Eqn. 6.11), Eqn. 9.5 does not have a [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression) solution. Yet, we can still solve it analytically using a neat mathematical trick.

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

does not appear to be simpler, as it turns out, we can easily solve the Helmholtz equation for a *point source*

$$ \tag{9.9}
	  \big( \nabla^2 + k^2 \big) g(\bm{r} - \bm{r'}, k)
	= -\delta(\bm{r} - \bm{r'}).
$$

Its solution is the *scalar* [Green function](https://en.wikipedia.org/wiki/Green%27s_function) \\(g\\). It represents the [impulse response](https://en.wikipedia.org/wiki/Impulse_response) of the linear operator \\(\mathcal{H}\\).

Eqn. 9.9 is an inhomogeneous linear differential equation. Consequently, its solution depends on the linear operator in question, its domain of validity, as well as the associated (initial or boundary) conditions. According to Eqn. 9.7, our domain is the whole real space, and the only applicable restriction is the Sommerfeld radiation condition. Thus, it can be shown that the *free-space* scalar Green function \\(g_0\\) takes the form[^9] of an diverging *scalar* [spherical wave](https://en.wikipedia.org/wiki/Wave_equation#Spherical_waves) \[[7](#references) (ch. 2.12), [8](#references) (ap. B), [14](#references)\]:

$$ \tag{9.10}
	g_0(\bm{r}) = g(\bm{r}, k_0) = \frac{e^{i k_0 |\bm{r}|}}{4 \pi |\bm{r}|}.
$$

[^9]: Mathematicians often move the minus sign from Eqn. 9.9 to 9.10. This introduces a significant amount of bookkeeping and is, ultimately, undesirable.

In order to reconcile Eqn. 9.8 and 9.9, multiply both sides of the latter by \\(\xi(\bm{r'})\\):

$$ \tag{9.11}
	\big( \nabla^2 + k_0^2 \big) g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) = -\delta(\bm{r} - \bm{r'}) \xi(\bm{r'}).
$$

Note that the Laplace operator depends on \\(\bm{r}\\) rather than \\(\bm{r'}\\). Keeping this in mind, take the volume integral

$$ \tag{9.12}
	\big( \nabla^2 + k_0^2 \big) \int\_{\mathbb{R^3}} g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV' = -\xi(\bm{r}),
$$

and compare the result to Eqn. 9.5. We can see that we have found our solution:

$$ \tag{9.13}
	\psi(\bm{r}) = \int\_{\mathbb{R^3}} g_0(\bm{r} - \bm{r'}) \xi(\bm{r'}) dV'.
$$

Note that, despite the \\(1/|\bm{r} - \bm{r'}|\\) factor in the scalar Green function making the integral [improper](https://en.wikipedia.org/wiki/Improper_integral), it is convergent provided that \\(\xi\\) is a piecewise-continuous function \[[7](#references) (ch. 3.2)\].

Eqn. 9.13 allows us to find the expressions of the scalar and the vector potentials given by Eqn. 8.16.1 and 8.16.3 in the frequency domain:

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

Note that Eqn. 9.13 represents a *special* solution of the *inhomogeneous* Helmholtz equation. Since, due to linearity, adding a solution of the *homogeneous* equation does not change the right-hand side, we can obtain the *general* solution of Eqn. 9.5 by summing Eqn. 6.15 and 9.13:

$$ \tag{9.15}
\begin{aligned}
	\psi(\bm{r})
	&= \psi_i(\bm{r}) + \psi_s(\bm{r}) \cr
	&= \oint\_{\mathbb{S}^2} \psi_i(0, \bm{n}) e^{i k_0 (\bm{r} \cdot \bm{n})} d\Omega
	 + \int\_{\mathbb{R^3}} \frac{e^{i k_0 |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \xi(\bm{r'}) dV'.
\end{aligned}
$$

Since Eqn. 9.14 integrates over all currents and charges, all sources of electromagnetic fields are already accounted for, and the homogeneous part of the solution is zero. However, it is often convenient to specify the primary sources separately. In order to do that, we must recall the current and charge decomposition given by Eqn. 1.9 and 5.4. After substitution, the solution may be alternatively written as

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

Let us complete the derivation by finding the expressions of the electric and the magnetic phasors. First, let us establish some context by deriving the differential equation we are planning to solve. We start with Eqn. 3.10.1 and 3.10.3,

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

The corresponding solution in terms of potentials is given by Eqn. 8.4. In the frequency domain, it takes the following form:

$$ \tag{9.19}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

You may recall that we have used the Lorenz condition to relate the potentials. After performing the Fourier transform, Eqn. 8.11 becomes

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

where \\(\mathcal{I}\\) is the *identity operator* that performs multiplication by 1.

In the component representation of Eqn. 9.22, the gradient operator is a column vector, while the divergence operator is as a row vector. When combined in this particular order, they form an [outer product](https://en.wikipedia.org/wiki/Outer_product) that can be represented by a 3x3 matrix:

$$ \tag{9.23}
	\bm{E}(\bm{r}, \omega) =
	i \omega \Big( \mathcal{I} + \frac{c^2}{\omega^2} \nabla \otimes \nabla \Big) \cdot \bm{A}(\bm{r}, \omega).
$$

Let us now substitute the definition of \\(\bm{A}\\) given by Eqn. 9.14.1:

$$ \tag{9.24}
	\bm{E}(\bm{r}, \omega)
	= i \omega \Big( \mathcal{I} + \frac{1}{k_0^2(\omega)} \nabla \otimes \nabla \Big) \cdot \int\_{V}
	g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

Unfortunately, a complication arises when we try to move the new operator under the integral sign - if \\(\bm{r}\\) is inside \\(V\\), this action introduces a [singularity](https://en.wikipedia.org/wiki/Singularity_(mathematics)) (a [pole](https://en.wikipedia.org/wiki/Zeros_and_poles) of order 3) at \\(\bm{r} = \bm{r'}\\). We can sidestep this issue by creating a tiny cavity \\(V_{\delta}\\) centered at \\(\bm{r}\\), and separately evaluating the field produced by the piece of matter excised from the cavity. The result is

$$ \tag{9.25}
	\bm{E}(\bm{r}, \omega)
	= i \omega \bigg( \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G}\_0( \bm{r}, \bm{r'}) \cdot \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'
	- \frac{\mathcal{L}(\bm{r})}{k_0^2(\omega)} \cdot \frac{\bm{J}(\bm{r}, \omega)}{\mu_0^{-1}} \bigg),
$$

where

$$ \tag{9.26}
	\mathcal{G}\_0(\bm{r}, \bm{r'}) =
	\mathcal{G}(\bm{r}, \bm{r'}, k_0)
	= \Big( \mathcal{I} + \frac{1}{k_0^2} \nabla \otimes \nabla \Big) g(\bm{r} - \bm{r'}, k_0)
$$

is the *free-space dyadic* Green function \[[7](#references) (ch. 7.9)\], and \\(\mathcal{L}\\) is the dimensionless *depolarization dyadic*[^12], the expression of which depends on the shape (but not the size) of the cavity \[[7](#references) (ch. 3.9)\].

[^12]: A [dyadic](https://en.wikipedia.org/wiki/Dyadics) is a second rank tensor with a notation borrowed from linear algebra. A second rank [tensor](https://en.wikipedia.org/wiki/Tensor) is a scalar-valued [multilinear](multilinear) function with two slots that accept vector arguments; if only one argument is provided, it becomes a vector-valued linear function. For example, the dyadic expression \\(\bm{E} = \mathcal{L} \cdot \bm{J}\\) could be written in the standard tensor notation as \\(\bm{E} = \mathcal{L}(\text{\textunderscore},\bm{J})\\). We retain the dyadic notation due to its prevalence in the light scattering literarture. A brief introduction to the dyadic analysis is available in \[[7](#references) (ap. 4), [8](#references) (ap. A)\].

Eqn. 9.25 can be cast in a more compact form by using the [principal value](https://en.wikipedia.org/wiki/Cauchy_principal_value) of the free-space dyadic. If we define the *electric dyadic* Green function as

$$ \tag{9.27}
	\mathcal{G}\_e(\bm{r}, \bm{r'}, k)
	= \mathcal{P.V.} \lbrace \mathcal{G}(\bm{r}, \bm{r'}, k) \rbrace
	- \frac{\delta(\bm{r} - \bm{r'})}{k^2} \mathcal{L}(\bm{r}),
$$

the expression of the electric field is reduced to

$$ \tag{9.28}
	\bm{E}(\bm{r}, \omega)
	= i \omega \int\_{V} \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k_0(\omega) \big) \cdot \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

Comparison with Eqn. 9.18.1 shows that the electric dyadic is a solution of another differential equation for a point source:

$$ \tag{9.29}
	\left( \nabla \times \nabla \times \mathcal{I} - k^2 \right) \mathcal{G}\_e(\bm{r}, \bm{r'}, k)
	= \mathcal{I} \delta(\bm{r} - \bm{r'}).
$$

To find the integral form of the magnetic field, we must expand Eqn. 9.21.2 using 9.14.1:

$$ \tag{9.30}
	\bm{B}(\bm{r}, \omega)
	= \nabla \times \int\_{V}
	g \big( \bm{r} - \bm{r'}, k_0(\omega) \big) \frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
$$

Using the [curl identity](https://en.wikipedia.org/wiki/Vector_calculus_identities#Curl_2)

$$ \tag{9.31}
	\nabla \times (g \bm{J}) = g (\nabla \times \bm{J}) + \nabla g \times \bm{J},
$$

and noting that \\(\bm{J}\\) does not depend on \\(\bm{r}\\), we obtain a convergent integral (in the principal value sense)

$$ \tag{9.32}
	\bm{B}(\bm{r}, \omega)
	= \int_{V} \mathcal{G}\_m \big( \bm{r}, \bm{r'}, k_0(\omega) \big)  \cdot\frac{\bm{J}(\bm{r'}, \omega)}{\mu_0^{-1}} dV',
$$

that features the *magnetic dyadic* Green function \[[7](#references) (ch. 7.9)\]

$$ \tag{9.34}
	\mathcal{G}\_m(\bm{r}, \bm{r'}, k)
	= \mathcal{P.V.} \lbrace \nabla g(\bm{r} - \bm{r'}, k) \rbrace \times \mathcal{I}
$$

that can be expressed using the [matrix form of the cross product](https://en.wikipedia.org/wiki/Cross_product#Conversion_to_matrix_multiplication). You may recognize that

$$ \tag{9.35}
	\mathcal{G}\_m(\bm{r}, \bm{r'}, k)
	= \frac{1}{i \omega} \nabla \times \mathcal{G}\_e(\bm{r}, \bm{r'}, k).
$$

Therefore, our results are consistent with Eqn. 3.12.

Finally, note that, according to Eqn. 9.29,

$$ \tag{9.3y}
	i \omega \nabla \times \mathcal{G}\_m(\bm{r}, \bm{r'}, k)
	= \nabla \times \nabla \times \mathcal{G}\_e(\bm{r}, \bm{r'}, k)
	= k^2 \mathcal{G}\_e(\bm{r}, \bm{r'}, k) + \mathcal{I} \delta(\bm{r} - \bm{r'}).
$$

Green's dyadics take on a simple form when expressed in the Cartesian coordinate system. Let us first consider the electric dyadic defined by Eqn. 9.26-9.27:

$$ \tag{9.36}
\begin{aligned}
	\mathcal{G}\_e(\bm{r}, \bm{r'}, k)
	= \mathcal{P.V.} \left\lbrace \mathcal{I} g + \frac{1}{k^2}
	\begin{bmatrix}
		\frac{\partial^2 g}{\partial x^2} & \frac{\partial^2 g}{\partial x \partial y} & \frac{\partial^2 g}{\partial x \partial z} \cr
		\frac{\partial^2 g}{\partial y \partial x} & \frac{\partial^2 g}{\partial y^2} & \frac{\partial^2 g}{\partial y \partial z} \cr
		\frac{\partial^2 g}{\partial z \partial x} & \frac{\partial^2 g}{\partial z \partial y} & \frac{\partial^2 g}{\partial z^2} \cr
	\end{bmatrix}
	\right\rbrace
	- \frac{\delta}{k^2} \mathcal{L},
\end{aligned}
$$

where the scalar Green function \\(g\\) is given by Eqn. 9.10.

Because the integrals involving the scalar Green function and its partial derivatives are always evaluated in the principal value sense, the singularity at \\(\bm{r} = \bm{r'}\\) is avoided, and the order of partial differentiation makes no difference. Furthermore, the depolarization dyadic is symmetric: \\(\mathcal{L} = \mathcal{L}^T\\) \[[7](#references) (ch. 3.9)\]. Consequently, the entire electric dyadic is symmetric as well: \\(\mathcal{G}\_e = \mathcal{G}\_e^T\\).

Since the scalar Green function only depends on the magnitude of its argument, the function itself is symmetric in its arguments, while its first derivative is not:

$$ \tag{9.37}
\begin{aligned}
	g(\bm{r} - \bm{r'}, k) &= g(\bm{r'} - \bm{r}, k),
	\cr
	\nabla g(\bm{r} - \bm{r'}, k) &= -\nabla' g(\bm{r'} - \bm{r}, k),
\end{aligned}
$$

where \\(\nabla'\\) denotes the gradient with respect to the coordinates of \\(\bm{r'}\\).

The second-order derivatives of Eqn. 9.36 perform the sign exchange twice. If we also take into account the fact that the depolarization dyadic only contributes when \\(\bm{r} = \bm{r'}\\), it becomes clear that the electric dyadic is invariant under the exchange of its arguments \[[8](#references) (ap. B)\]:

$$ \tag{9.38}
\begin{aligned}
	\mathcal{G}\_e(\bm{r}, \bm{r'}, k)
	&= \mathcal{P.V.} \Big\lbrace \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) g(\bm{r} - \bm{r'}, k) \Big\rbrace - \delta(\bm{r} - \bm{r'}) \frac{\mathcal{L}(\bm{r})}{k^2}
	\cr
	&= \mathcal{P.V.} \Big\lbrace \Big( \mathcal{I} + \frac{1}{k^2} \nabla' \otimes \nabla' \Big) g(\bm{r'} - \bm{r}, k) \Big\rbrace - \delta(\bm{r'} - \bm{r}) \frac{\mathcal{L}(\bm{r'})}{k^2}
	= \mathcal{G}\_e(\bm{r'}, \bm{r}, k).
\end{aligned}
$$

In summary,

$$ \tag{9.3x}
	\mathcal{G}\_e(\bm{r}, \bm{r'}, k)
	= \left[ \mathcal{G}\_e(\bm{r}, \bm{r'}, k) \right]^T
	= \left[ \mathcal{G}\_e(\bm{r'}, \bm{r}, k) \right]^T
	= \mathcal{G}\_e(\bm{r'}, \bm{r}, k).
$$

Ignoring the multiplicative constant \\(1/(4 \pi k^2)\\), the matrix elements or Eqn. 9.36 have the form

$$ \tag{9.39}
	\frac{\partial^2}{\partial \\_ \partial \\_ } \Bigg( \frac{e^{i k |\bm{r} - \bm{r'}|}}{|\bm{r} - \bm{r'}|} \Bigg),
$$

where we may substitute \\(x\\), \\(y\\), or \\(z\\) into the blanks.

The process of differentiation is straightforward: we use the identity

$$ \tag{9.40}
	\frac{\partial}{\partial x} |\bm{r} - \bm{r'}|^{n}
	= \frac{\partial}{\partial x} \big( (\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'}) \big)^{n/2}
	= n \frac{\bm{r} - \bm{r'}}{|\bm{r} - \bm{r'}|^{2-n}} \cdot \frac{\partial}{\partial x} (\bm{r} - \bm{r'})
$$

and repeatedly apply the [product rule](https://en.wikipedia.org/wiki/Product_rule). Skipping the details of the calculation, and using the shorthand notation \\(\bm{R} = \bm{r} - \bm{r'}, R = |\bm{R}|\\), a typical first derivative is

$$ \tag{9.41}
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

$$ \tag{9.42}
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

$$ \tag{9.43}
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

Take a look at the individual factors in Eqn. 9.41-9.43: those outside the brackets oscillate between 0 and 1, while the terms  inside greatly depend on the distance between the observation point \\(\bm{r}\\) and the source \\(\bm{r'}\\). This suggests that we may divide the entire space into zones based on the proximity to the observation point. If we are interested in the value of the field located near the source (the so-called *near field*), we say that the observation point belongs to the [near zone](https://en.wikipedia.org/wiki/Near_and_far_field). Similarly, far-away points (and the *far field*) are said to be located in the [far zone](https://en.wikipedia.org/wiki/Near_and_far_field) (also known as the *radiation zone*). Between them is a region called the *transition zone*.

If we fix a value of \\(k\\), we may decompose the electric dyadic into the near-, transition-, and far-field terms:

$$ \tag{9.44}
	\mathcal{G}\_e
	= \mathcal{G_{en}}
	+ \mathcal{G_{et}}
	+ \mathcal{G_{ef}}
	- \frac{\delta}{k^2} \mathcal{L},
$$

such that

$$ \tag{9.45}
\begin{aligned}
	& \mathcal{G_{en}}(\bm{R}, k)
	= -\frac{g(\bm{R}, k) }{k^2 R^2} \bigg(\mathcal{I} - 3 \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg), \cr
	& \mathcal{G_{et}}(\bm{R}, k)
	= i \frac{g(\bm{R}, k) }{k R} \bigg(\mathcal{I} - 3 \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg), \cr
	& \mathcal{G_{ef}}(\bm{R}, k)
	= g(\bm{R}, k) \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg),
\end{aligned}
$$

with the scalar Green function containing an additional \\(R^{-1}\\) factor, so that

$$ \tag{9.46}
	k^{-1} \mathcal{G_{en}}
	\varpropto (k R)^{-3},
	\quad
	k^{-1} \mathcal{G_{et}}
	\varpropto (k R)^{-2},
	\quad
	k^{-1} \mathcal{G_{ef}}
	\varpropto (k R)^{-1}.
$$

Let us analyze the magnetic dyadic in the same way. Write Eqn. 9.34 in the Cartesian coordinate system:

$$ \tag{9.47}
\begin{aligned}
	\mathcal{G}\_m(\bm{r}, \bm{r'}, k)
	= \mathcal{P.V.} \begin{bmatrix}
		\partial g / \partial x \cr
		\partial g / \partial y \cr
		\partial g / \partial z \cr
	\end{bmatrix} \times \mathcal{I}
	= \mathcal{P.V.} \begin{bmatrix}
		0 & -\frac{\partial g}{\partial z} & \frac{\partial g}{\partial y} \cr
		\frac{\partial g}{\partial z} & 0 & -\frac{\partial g}{\partial x} \cr
		-\frac{\partial g}{\partial y} & \frac{\partial g}{\partial x} & 0 \cr
	\end{bmatrix}.
\end{aligned}
$$

Clearly, the matrix is anti-symmetric: \\(\mathcal{G}\_m = -\mathcal{G}\_m^T\\). Furthermore, because the derivatives are only of the first order, exchange of the arguments leads to

$$ \tag{9.48}
	\mathcal{G}\_m(\bm{r'}, \bm{r}, k)
	= \frac{1}{i \omega} \nabla' \times \mathcal{G}\_e(\bm{r'}, \bm{r}, k)
	= \mathcal{P.V.} \lbrace \nabla' g(\bm{r'} - \bm{r}, k) \rbrace \times \mathcal{I}
	= -\mathcal{G}\_m(\bm{r}, \bm{r'}, k).
$$

In summary,

$$ \tag{9.49}
	\mathcal{G}\_m(\bm{r}, \bm{r'}, k)
	= -\left[ \mathcal{G}\_m(\bm{r}, \bm{r'}, k) \right]^T
	= \left[ \mathcal{G}\_m(\bm{r'}, \bm{r}, k) \right]^T
	= -\mathcal{G}\_m(\bm{r'}, \bm{r}, k).
$$

According to the expression of the first derivative given by Eqn. 9.41, the dyadic is composed of two terms:

$$ \tag{9.50}
	\mathcal{G}\_m = \mathcal{G_{mn}} + \mathcal{G_{mf}},
$$

such that

$$ \tag{9.51}
\begin{aligned}
	& \mathcal{G_{mn}}(\bm{R}, k)
	= -\frac{g(\bm{R}, k)}{R} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg), \cr
	& \mathcal{G_{mf}}(\bm{R}, k)
	= i k g(\bm{R}, k) \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg).
\end{aligned}
$$

Note that, as expected from Eqn. 9.35, the constants of proportionality are different in comparison with the electric dyadic:

$$ \tag{9.52}
	k^{-2} \mathcal{G_{mn}}
	\varpropto (k R)^{-2},
	\quad
	k^{-2} \mathcal{G_{mf}}
	\varpropto (k R)^{-1}.
$$

-->

### Electric and Magnetic Polarization

The physical meaning of the electromagnetic potential is more apparent in the time domain. With this goal in mind, perform the inverse Fourier transform of Eqn. 9.14.2:

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

For this reason, \\(\bm{A}\\) and \\(\phi\\) are called the [retarded potentials](https://en.wikipedia.org/wiki/Retarded_potential) \[[5](#references) (vol. II, ch. 21), [6](#references) (ch. 2.1), [28](#references) (ch. 5.2)\]:

$$ \tag{10.3}
\begin{aligned}
	&\bm{A}(\bm{r}, t)
	= \frac{1}{4 \pi \mu_0^{-1}} \int\_{V} \frac{\bm{J}(\bm{r'}, t - |\bm{r} - \bm{r'}| / c)}{|\bm{r} - \bm{r'}|} dV', \cr
	&\phi(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V} \frac{\rho(\bm{r'}, t - |\bm{r} - \bm{r'}| / c)}{|\bm{r} - \bm{r'}|} dV'.
\end{aligned}
$$

The simplicity of Eqn. 10.3 is deceptive. It tells us nothing about what kind of charges there are, or how they behave. It is thus advantageous to return to the macroscopic picture of matter by describing the bound currents and charges in terms of polarization of matter as per Eqn. 1.9. For simplicity, we assume that the material is non-conducting, so that \\(\bm{J\_f} = \rho\_f = 0\\).

To keep the resulting expressions compact, let us introduce the notion of [retarded time](https://en.wikipedia.org/wiki/Retarded_time)

$$ \tag{10.4}
	t' = t - \frac{|\bm{r} - \bm{r'}|}{c},
$$

which is simply the point in time at which the wave was generated by the source located at \\(\bm{r'}\\).

This definition couples the space and time variables. For instance, consider the following expression:

$$ \tag{10.5}
	\frac{\partial}{\partial x} f \big(\bm{r}, t'(\bm{r}) \big)
	= \frac{\partial f(\bm{r}, t)}{\partial x} \Big|\_{t=t'}
	+ \frac{\partial f(\bm{r}, t)}{\partial t} \Big|\_{t=t'} \frac{\partial t'(\bm{r})}{\partial x},
$$

where

$$ \tag{10.6}
	\frac{\partial t'(\bm{r})}{\partial x}
	= -\frac{1}{c} \frac{\partial}{\partial x} \big( (\bm{r} - \bm{r'}) \cdot (\bm{r} - \bm{r'}) \big)^{1/2}
	= -\frac{\bm{r} - \bm{r'}}{c |\bm{r} - \bm{r'}|} \cdot \frac{\partial}{\partial x} (\bm{r} - \bm{r'}).
$$

Since \\(t'\\) depends on \\(\bm{r}\\), this "simple" \\(x\\)-derivative of Eqn. 10.6 requires application of the [chain rule](https://en.wikipedia.org/wiki/Chain_rule#Multivariable_case).

Eqn. 10.5 can be written in a more clear way by utilizing the *retarded value* notation:

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

In order to be able to write these equation directly in terms of \\(t'\\), we must move the space derivatives outside the brackets. We can achieve this goal by using the chain rule as demonstrated by Eqn. 10.5-10.6:

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

Assuming that the region containing charges is finite, we can increase the size of the bounding surface without affecting the value of the integrals on the left-hand side. This will cause the integrals (on the right-hand side) taken over the extended bounding surface to vanish. Consequently, the volume integrals of Eqn. 10.11.1-10.11.2 take the following form:

$$ \tag{10.13}
\begin{aligned}
	& \int\_{V} \frac{-1}{|\bm{r} - \bm{r'}|} \nabla' \cdot \bm{P} dV'
	= \int\_{V} \bm{P} \cdot \nabla' \frac{1}{|\bm{r} - \bm{r'}|} dV', \cr
	& \int\_{V} \frac{1}{|\bm{r} - \bm{r'}|} \nabla' \times \bm{M} dV'
	= \int\_{V} \bm{M} \times \nabla' \frac{1}{|\bm{r} - \bm{r'}|} dV'.
\end{aligned}
$$

Let us combine Eqn. 10.10 and 10.13. Using the shorthand notation \\(\bm{R} = \bm{r} - \bm{r'}, R = |\bm{R}|\\), the result is

$$ \tag{10.14}
\begin{aligned}
	&\bm{A_s}(\bm{r}, t)
	= \frac{1}{4 \pi \mu_0^{-1}} \int\_{V}
		  \frac{\lfloor \partial \bm{P} / \partial t \rfloor}{R}
		+ \lfloor \bm{M} \rfloor \times \nabla' \frac{1}{R}
		- \frac{\bm{R} \times \lfloor \partial \bm{M} / \partial t \rfloor}{c R^2} dV', \cr
	&\phi_s(\bm{r}, t)
	= \frac{1}{4 \pi \epsilon_0} \int\_{V}
		  \lfloor\bm{P} \rfloor \cdot \nabla' \frac{1}{R}
		+ \frac{\bm{R} \cdot \lfloor \partial \bm{P} / \partial t \rfloor}{c R^2} dV'.
\end{aligned}
$$

Eqn. 10.14.1-10.14.2 can be interpreted in terms of the atomic theory of matter \[[6](#references) (ch. 2.2)\]. The [nucleus](https://en.wikipedia.org/wiki/Atomic_nucleus) of an atom contains [protons](https://en.wikipedia.org/wiki/Proton) with the charge \\(+q_e\\), and is surrounded by one or several [electrons](https://en.wikipedia.org/wiki/Electron) with the charge \\(-q_e\\). A molecule is an electrically neutral group of several atoms. Thus, an atom or a molecule is a system of several charges, half being negative, and the other half -- positive, separated (on average) by a certain distance \\(d\\). If this distance is not zero, it is said that the molecule is polarized. We can use this property to model an atom or a small molecule as a pair of point charges of opposite sign called a [dipole](https://en.wikipedia.org/wiki/Dipole) with an associated [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment)

$$ \tag{10.15}
	\bm{p} = q \bm{d},
$$

where \\(\bm{p}\\) points along the line from the negative to the positive charge.

The moment of a dipole does not depend on its location, \\(\bm{r_0}\\). That is because it describes the displacement from the mean, which is relative by definition. That is easy to show by expressing the electric dipole moment in terms of the bound (or, equivalently, polarization) charge density:

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

On the other hand, the electric dipole moment per unit volume \\(\bm{P}\\) is position-dependent \[[28](#references) (ch. 3.1)\]:

$$ \tag{10.18}
\begin{aligned}
	\bm{P}(\bm{r}, t) &= \sum_n \bm{p_n}(V_n, t) \delta(\bm{r} - \bm{r_n}),
	&
	\bm{p}(V, t) &= \int\_{V} \bm{P}(\bm{r}, t) dV,
	\cr
	\bm{P}(\bm{r}, \omega) &= \sum_n \bm{p_n}(V_n, \omega) \delta(\bm{r} - \bm{r_n}),
	&
	\bm{p}(V, \omega) &= \int\_{V} \bm{P}(\bm{r}, \omega) dV.
\end{aligned}
$$

This expression is useful, since we can directly relate it to the definition of the polarization current density \\(\bm{J_p}\\) given by Eqn. 1.10.1:

$$ \tag{10.19}
	\frac{\partial}{\partial t} \bm{p}(V, t) = \int\_{V} \bm{J_p}(\bm{r}, t) dV,
	\quad
	-i \omega \bm{p}(V, \omega) = \int\_{V} \bm{J_p}(\bm{r}, \omega) dV.
$$

Eqn. 10.19 implies that we can represent an arbitrary polarization current density by oscillating electric dipole moments:

$$ \tag{10.20}
\begin{aligned}
	&\bm{J_p}(\bm{r}, t)
	= \frac{\partial}{\partial t} \bm{P}(\bm{r}, t)
	= \frac{\partial}{\partial t} \sum_n \bm{p_n}(V_n, t) \delta(\bm{r} - \bm{r_n}),
	\cr
	&\bm{J_p}(\bm{r}, \omega)
	= -i \omega \bm{P}(\bm{r}, \omega)
	= -i \omega \sum_n \bm{p_n}(V_n, \omega) \delta(\bm{r} - \bm{r_n}).
\end{aligned}
$$

A molecule can become polarized for a variety of reasons \[[5](#references) (vol. II, ch. 11)\]. If the separation of charges (such as the displacement of the electron cloud relative to the positively charged nucleus) occurs under the influence of the external electromagnetic field, one speaks of *induced polarization*. If we assume that the effect is linear, the response is characterized by the [molecular polarizability](https://en.wikipedia.org/wiki/Electric_susceptibility#Molecular_polarizability) dyadic \\(\mathcal{\Alpha_m}\\):

$$ \tag{10.21}
	\bm{p}(V_m, \omega) \approx \mathcal{\Alpha_m}(V_m, \omega) \cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega),
$$

where \\(\bm{E_m}\\) is the phasor of the microscopic field acting on the molecule.

The dyadic form of \\(\mathcal{\Alpha_m}\\) is necessary to model a molecule that lacks spherical symmetry; it acts by rotating and non-uniformly scaling the electric field phasor. If the molecule absorbs light, its polarizability is complex \[[4](#references) (ch. 6.13)\].

If there are \\(N\\) identical electric dipoles per unit volume, we can define polarization \\(\bm{P}\\) as

$$ \tag{10.22}
	\bm{P}(\bm{r}, \omega)
	= N(\bm{r}) \bm{p}(V_m, \omega)
	\approx N(\bm{r}) \mathcal{\Alpha_m}(V_m, \omega) \cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega)
$$

In general, the microscopic field \\(\bm{E_m}\\) acting on a dipole is different from the macroscopic field \\(\bm{E}\\). This difference can be attributed to the influence of the molecules located in close proximity of the dipole \[[5](#references) (vol. II, ch. 11), [6](#references) (ch. 2.3-2.4)\]. If the molecules are randomly distributed, we can assume that (on average) the dipole is located within a (microscopic) spherical cavity of a uniformly (macroscopically) polarized medium. The electric field inside a uniformly polarized ball is

$$ \tag{10.2?}
	\bm{E_b} = -\frac{1}{3 \epsilon_0} \bm{P},
$$

where the minus sign is present because the electric field vector diverges from positive charges, which is the exact opposite of the convention used for polarization.

The superposition principle allows us to excise the polarized ball that occupies the cavity, yielding

$$ \tag{10.23}
	\bm{E_m}
	= \bm{E} - \bm{E_b}
	= \bm{E} + \frac{1}{3 \epsilon_0} \bm{P}.
$$

By combining Eqn. 10.22 with 10.23, and assuming that the polarizability is isotropic, so that \\(\mathcal{\Alpha_m} = \alpha_m\\),

$$ \tag{10.24}
	\bm{P}(\bm{r}, \omega)
	\approx \frac{N(\bm{r}) \alpha_m(V_m, \omega)}{1 - \frac{1}{3} N(\bm{r}) \alpha_m(V_m, \omega)} \epsilon_0 \bm{E}(\bm{r}, \omega),
$$

we can incorporate the correction for the *microscopic* field into the *macroscopic* theory.

In order to be able to use Eqn. 10.24, we need to determine both the density and the polarizability of the material. For an isotropic dielectric composed of a single type of small molecules, their product is given by the [Clausius–Mossotti relation](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation):

$$ \tag{10.25}
	\frac{\epsilon\_r(\bm{r}, \omega) - 1}{\epsilon\_r(\bm{r}, \omega) + 2} = \frac{1}{3} N(\bm{r}) \alpha\_m(V_m, \omega).
$$

Thus, Eqn. 10.24 can be expressed very simply in terms of the [relative permittivity](https://en.wikipedia.org/wiki/Relative_permittivity) (dielectric constant) \\(\epsilon_r\\):

$$ \tag{10.26}
	\bm{P}(\bm{r}, \omega)
	= \bm{D}(\bm{r}, \omega) - \epsilon_0 \bm{E}(\bm{r}, \omega)
	\approx \big( \epsilon_r(\bm{r}, \omega) - 1 \big) \epsilon_0 \bm{E}(\bm{r}, \omega),
$$

with the "exact" Eqn. 1.13.1 shown for comparison.

While the electric dipole moment \\(\bm{p}\\) is produced by oscillating atomic charges, the [magnetic dipole moment](https://en.wikipedia.org/wiki/Magnetic_moment#Amperian_loop_model) \\(\bm{m}\\) is generated by loops of circulating atomic current (i.e. spinning or orbiting charges):

$$ \tag{10.27}
	\bm{m} = I \bm{s},
$$

where \\(I\\) is the electric current, and \\(\bm{s}\\) is the [oriented area](https://en.wikipedia.org/wiki/Vector_area) of the loop. The Maxwell equations show that the effect of this *electric loop* is equivalent to a *pair of vibrating magnetic charges*, which is the reason why this configuration is also considered a dipole.

Formally, the magnetic dipole moment can be expressed in terms of the electric current density \\(\bm{j}\\) by the integral

$$ \tag{10.28}
	\bm{m}(t)
	= \frac{1}{2} \int\_{V} \bm{r} \times \bm{j}(\bm{r}, t) dV,
$$

or, in terms of the magnetic polarization per unit volume \\(\bm{M}\\),

$$ \tag{10.29}
	\bm{m}(t)
	= \int\_{V} \bm{M}(\bm{r}, t) dV.
$$

The curl of the latter is related to the magnetization current density \\(\bm{J_m}\\) by Eqn 1.10.1.

Like in the case of polarization, magnetism comes in different forms \[[5](#references) (vol. II, ch. 34)\]. Unfortunately, none of them appear to have a satisfactory explanation in terms of classical physics. In particular, to the best of the author's knowledge, there is no simple analog of Eqn. 10.21 for induced magnetization.

For weak fields in [diamagnets](https://en.wikipedia.org/wiki/Diamagnet) and [paramagnets](https://en.wikipedia.org/wiki/Paramagnet), it is possible to combine Eqn. 1.13.2 and 5.2.3 to express the magnetic polarization \\(\bm{M}\\) in terms of the relative permeability \\(\mu\_r\\),

$$ \tag{10.30}
	\bm{M}(\bm{r}, \omega)
	= \mu\_0^{-1} \bm{B}(\bm{r}, \omega) - \bm{H}(\bm{r}, \omega)
	\approx \big( 1 - \mu_r^{-1}(\bm{r}, \omega) \big) \mu\_0^{-1} \bm{B}(\bm{r}, \omega),
$$

but for [ferromagnets](https://en.wikipedia.org/wiki/Ferromagnet), this expression is invalid due to the [magnetic hysteresis](https://en.wikipedia.org/wiki/Magnetic_hysteresis) effect \[[5](#references) (vol. II, ch. 36)\].

Fortunately, for optical applications, the situation is rather simple. Paramagnetism and diamagnetism are both very weak in general; the same is true for ferromagnetism at optical frequencies in particular. This leads to \\(\mu\_r \approx 1\\) \[[10](#references) (vol. I, ch. 27)\].

In summary, it can be shown (but we won't prove it here) that Eqn. 10.14.2 is the scalar potential of a distribution of a electric dipoles of moment \\(\bm{P}\\) per unit volume, and that the first term of Eqn. 10.14.1 is the corresponding vector potential. Similarly, the last two terms of Eqn. 10.14.1 can be attributed to a distribution of magnetic dipoles of moment \\(\bm{M}\\) per unit volume \[[6](#references) (ch. 2.2), [28](#references) (ch. 5.3)\].

In some sense, dipoles can be considered elementary sources of oscillating fields of the same type[^14]. They can be used to describe any (bound) current and charge distributions, which makes them particularly useful for solving electromagnetic radiation and scattering problems.

[^14]: Dipoles also generate circulating fields of the opposite type.

[Insert pictures of field lines of electric and magnetic dipoles]

Finally, we would like to emphasize certain properties of the solution of the Maxwell equations in terms of potentials. Perhaps the most obvious one is that the scattered field is expressed as a volume integral. While the geometry of the volume containing the charges plays an important role, it is now apparent that the interior of the volume is the actual source of what we consider to be surface reflection. Furthermore, Eqn. 10.3 says that *microscopic* electromagnetic fields always propagate at the speed of light. The reason for different (ordinarily, lower) phase velocity of *macroscopic* fields in matter is the remarkable fact that the dipole field interferes with the incident field in such a way that the latter is completely extinguished and replaced by the internal field that propagates in accordance with the refractive index of the medium \[[5](#references) (vol. I, ch. 31)\]. Formally, this mechanism is described by the [extinction theorem](https://en.wikipedia.org/wiki/Ewald%E2%80%93Oseen_extinction_theorem) \[[6](#references) (ch. 2.4)\].

## II. Electromagnetic Scattering

### Scattering Problem

In practice, it is very difficult to solve the Maxwell equations exactly, except under certain (idealized) conditions. Thus, one usually employs various approximations that are carefully chosen to minimize both the error and the complexity of the computation.

One of the most important considerations is the scale of the observation. Evidently, even something as large and complex as a star can be approximated by a point source if the observer is sufficiently far away. A similar line of reasoning justifies modeling an electron as a point charge at the microscopic scale.

Stepping farther away, we can represent the average atom (or a molecule) as a dipole aligned with the applied field, thereby greatly restricting the expected behavior of microscopic charges and currents. In this approximation, constructive interference reinforces the fields produced by the charges moving in sync, while the effects of motion that differs from the average are suppressed by both destructive interference and the lower probability of occurrence.

Mathematically, the field of a (macroscopic) piece of matter is the (vector) sum of the fields of its (microscopic) parts. Yet, at the macroscopic scale, the field of a single dipole often cannot be distinguished at all! The field of a tremendous number of identical dipoles acting together manifests itself by bending light rays (according to the refractive index) and reducing their intensity (proportionally to the attenuation index). In order for light scattering effects to appear, the medium must be inhomogeneous. Macroscopically, such a medium features spatially-varying *optical properties*; microscopically, this implies that the underlying distribution of dipoles is not the same throughout the medium. Clearly, a homogeneous medium is an idealization, yet it can serve as an effective approximation for tiny patches of uniformly dense matter (composed of scatterers smaller than the wavelength of light).

[Insert picture here?]

The macroscopic distribution of optical properties can (at least in theory) be completely arbitrary. In order to arrive at a concrete (and practical) solution, we shall consider the special case of sparse inhomogeneous particles embedded in a homogeneous medium. Notable examples of such *composite media* include [gases](https://en.wikipedia.org/wiki/Gas) and [colloids](https://en.wikipedia.org/wiki/Colloid).

[Insert picture here]

Given the source illuminating the medium, we can determine the *total field* that arises after propagation and scattering of electromagnetic waves. Clearly, if there are no particles, the medium is homogeneous everywhere, and the magnitude of the field at the destination is the same as its magnitude at the source (before accounting for attenuation with distance). We shall refer to it as the *incident field*. The *algebraic difference* between the total field (with particles) and the incident field (without particles) constitutes the *scattered field*. The task of determining the scattered field is at the core of the *scattering problem*.

[Insert picture here]

In order to rigorously solve the scattering problem, we must specify not only the incident field, but also the composite medium *in its entirety*, since, according to the Maxwell equations, the value of the electromagnetic field at a certain point is the sum of the fields produced by all charges, everywhere. Clearly, this is highly inconvenient and, often, unnecessary. Thus, we make a compromise: we "chop" the composite medium into little pieces (that effectively shrink to a point), each filled with small particles, analogously to a region of the macroscopic medium formed by a distribution of microscopic dipoles. Extending the analogy further, just as the combined action of dipoles leads to the definition of optical properties,
the *radiative properties* of the large-scale medium are determined by solving the Maxwell equations for the distribution of particles within each little piece. Finally, we use the *radiative transfer equation* to integrate the contribution of all little pieces, which is a process that is conceptually similar to combining the fields of all charges according to the Maxwell equations.

### Volume Integral Equation

Participating media can be broadly divided into two categories - homogeneous and inhomogeneous. This suggests that we may split any medium into two regions: 1) infinite homogeneous, and 2) the remaining space of finite volume \\(V\\). The latter can be interpreted as a single particle or a particle group, or, more generally, as a scattering object (or a *scatterer* for short).

[Picture?]

According to the Maxwell equations in the frequency domain (Eqn. 3.9.1), the following relation holds in both regions:

$$ \tag{11.1}
	\nabla \times \bm{E}(\bm{r}, \omega) - i \omega \bm{B}(\bm{r}, \omega) = 0.
$$

On the other hand, the second Maxwell equation (3.9.3) depends on the region under consideration:

$$ \tag{11.2}
	\nabla \times \bm{H}(\bm{r}, \omega) + i \omega \bm{D}(\bm{r}, \omega) = \bm{J_f}(\bm{r}, \omega).
$$

Assuming that the media are linear, isotropic, and source-free, Eqn. 11.2 can be replaced by Eqn. 6.1.3:

$$ \tag{11.3}
\begin{aligned}
	& \nabla \times \big( \mu_1^{-1}(\omega) \bm{B}(\bm{r_1}, \omega) \big)
	= -i \omega \varepsilon_1(\omega)  \bm{E}(\bm{r_1}, \omega), \cr
	& \nabla \times \big( \mu_2^{-1}(\bm{r_2}, \omega) \bm{B}(\bm{r_2}, \omega) \big)
	= -i \omega \varepsilon_2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega),
\end{aligned}
$$

where the first equation only holds in the (homogeneous) region 1, and the second - in the (inhomogeneous) region 2.

Further, assume that the magnetic permeability is constant within the region 2. This implies that

$$ \tag{11.4}
\begin{aligned}
	& \nabla \times \bm{B}(\bm{r_1}, \omega)
	= -i \omega \mu_1(\omega) \varepsilon_1(\omega) \bm{E}(\bm{r_1}, \omega), \cr
	& \nabla \times \bm{B}(\bm{r_2}, \omega)
	= -i \omega \mu_2(\omega) \varepsilon_2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega),
\end{aligned}
$$

These equations are ready for substitution into the curl of Eqn. 11.1. The result is

$$ \tag{11.5}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r_1}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_1}, \omega) = 0, \cr
	& \nabla \times \nabla \times \bm{E}(\bm{r_2}, \omega) - k_2^2(\bm{r_2}, \omega) \bm{E}(\bm{r_2}, \omega) = 0,
\end{aligned}
$$

with the constants grouped according to the definition of the complex wavenumber \\(k\\) given by Eqn. 7.4.

To make these two equations more alike, we can subtract \\(k_1^2 \bm{E}\\) from both sides of Eqn. 11.5.2:

$$ \tag{11.6}
\begin{aligned}
	& \nabla \times \nabla \times \bm{E}(\bm{r_1}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_1}, \omega) = 0, \cr
	& \nabla \times \nabla \times \bm{E}(\bm{r_2}, \omega) - k_1^2(\omega) \bm{E}(\bm{r_2}, \omega)
	= \big( k_2^2(\bm{r_2}, \omega) - k_1^2(\omega) \big) \bm{E}(\bm{r_2}, \omega).
\end{aligned}
$$

We are now in a good position to state a single equation that is valid across the entire space. By introducing the term

$$ \tag{11.7}
\bm{J'}(\bm{r}, \omega) =
\begin{cases}
   k_1^2(\omega) \big( m^2(\bm{r}, \omega) - 1 \big) \bm{E}(\bm{r}, \omega) &\text{if } \bm{r} \in V, \cr
   0 &\text{otherwise},
\end{cases}
$$

such that the *relative wavenumber* (sometimes called the *relative refractive index*[^28]) is

[^28]: The complex refractive index (given by Eqn. 7.7) is defined relative to the optical properties of the free space (vacuum). The relative wavenumber depends on the properties of the surrounding medium, which may have a refractive index that is different from \\(1\\).

$$ \tag{11.8}
	m(\bm{r}, \omega)
	= \frac{k_2(\bm{r}, \omega)}{k_1(\omega)}
	= \sqrt{ \frac{\varepsilon_2(\bm{r}, \omega) \mu_2(\omega)}{\varepsilon_1(\omega) \mu_1(\omega)} }
	= \frac{\eta_2(\bm{r}, \omega) + i \kappa_2(\bm{r}, \omega)}{\eta_1(\omega) + i \kappa_1(\omega)},
$$

we can replace Eqn. 11.6.1 and 11.6.2 by a single equation

$$ \tag{11.9}
	\nabla \times \nabla \times \bm{E}(\bm{r}, \omega) - k_1^2(\omega) \bm{E}(\bm{r}, \omega)
	= \bm{J'}(\bm{r}, \omega).
$$

We have already encountered a mathematically identical problem shown in Eqn. 9.18.1. After matching the constants \\( \big( \bm{J'} = i \omega \bm{J} / \mu_0^{-1} \big) \\), the solution is readily given by Eqn. 9.28:

$$ \tag{11.10}
	\bm{E_s}(\bm{r}, \omega)
	= \int\_{V} \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \cdot \bm{J'}(\bm{r'}, \omega) dV'.
$$

Similarly, the magnetic field is given by Eqn. 9.32:

$$ \tag{11.11}
	\bm{B_s}(\bm{r}, \omega)
	= \frac{1}{i \omega} \int\_{V} \mathcal{G}\_m \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \cdot \bm{J'}(\bm{r'}, \omega) dV'.
$$

Eqn. 11.10 is a special solution of the inhomogeneous Eqn. 11.9 (just like Eqn. 9.13 is a special solution of Eqn. 9.5). To obtain a general solution, we must combine Eqn. 11.10 with the solution of the homogeneous equation, which physically amounts to adding the primary sources. In our particular case, the homogeneous equation is mathematically equivalent to Eqn. 6.5, which allows us to directly use the solution given by Eqn. 6.16:

$$ \tag{11.12}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \bm{E_i}(\bm{r}, \omega) + \bm{E_s}(\bm{r}, \omega) \cr
	&= \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ \int\_{V} \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k_1(\omega) \big) \cdot \bm{J'}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In addition to the mathematical approach presented above, we can arrive at the same result in a more "physical" way.

Consider an object embedded in vacuum. The solution in terms of potentials is given by Eqn. 9.16:

$$ \tag{11.13}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i k_0(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ \int\_{V} \frac{e^{i k_0(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \frac{\bm{J_b}(\bm{r'}, \omega) + \bm{J_i}(\bm{r'}, \omega)}{\mu_0^{-1}} dV'.
\end{aligned}
$$

The expression of the bound current is given by Eqn. 1.10.1. In the frequency domain, the corresponding equation is

$$ \tag{11.14}
	\bm{J\_b}(\bm{r}, \omega)
	= \bm{J\_p}(\bm{r}, \omega) + \bm{J\_m}(\bm{r}, \omega)
	= -i \omega \bm{P}(\bm{r}, \omega) + \nabla \times \bm{M}(\bm{r}, \omega).
$$

Using Eqn. 10.26 that relates polarization to the macroscopic optical properties, we may write

$$ \tag{11.15}
	\bm{J\_p}(\bm{r}, \omega) = -i \omega \big( \epsilon_2(\bm{r}, \omega) - \epsilon_0 \big)  \bm{E}(\bm{r}, \omega).
$$

Similarly, for magnetization, we may adopt Eqn. 10.30:

$$ \tag{11.16}
\begin{aligned}
	\bm{J\_m}(\bm{r}, \omega)
	&= \mu\_0^{-1} \nabla \times \bm{B}(\bm{r}, \omega) - \nabla \times \bm{H}(\bm{r}, \omega) \cr
	&= \big( \mu\_2(\omega) / \mu\_0 - 1 \big) \nabla \times \bm{H}(\bm{r}, \omega) \cr
	&= \big( \mu\_2(\omega) / \mu\_0 - 1 \big) \big( \sigma_2(\bm{r}, \omega) - i \omega \epsilon_2(\bm{r}, \omega) \big) \bm{E}(\bm{r}, \omega),
\end{aligned}
$$

where we additionally utilized Eqn. 5.2.3 and 5.5.3 to expand the expression of the curl.

If the object is conductive, Eqn. 5.2.1 says that

$$ \tag{11.17}
	\bm{J\_i}(\bm{r}, \omega) = \sigma_2(\bm{r}, \omega) \bm{E}(\bm{r}, \omega).
$$

Let us now introduce

$$ \tag{11.18}
	\bm{J'}(\bm{r}, \omega)
	= i \omega \frac{\bm{J_b}(\bm{r}, \omega) + \bm{J_i}(\bm{r}, \omega)}{\mu_0^{-1}}
	= \omega^2 \big( \varepsilon_2(\bm{r}, \omega) \mu_2(\omega) - \epsilon_0 \mu_0 \big) \bm{E}(\bm{r}, \omega).
$$

We may write it in terms of wave velocities using Eqn. 1.4, 7.4, and 7.9:

$$ \tag{11.19}
	\bm{J'}(\bm{r}, \omega)
	= \omega^2 \Bigg( \bigg( \frac{1}{v_p(\bm{r}, \omega)} + \frac{i}{v_a(\bm{r}, \omega)} \bigg)^2 - \frac{1}{c^2} \Bigg) \bm{E}(\bm{r}, \omega).
$$

Substitution into Eqn. 11.13 yields

$$ \tag{11.20}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i \omega (\bm{r} \cdot \bm{n}) / c} d\Omega \cr
	&- i \omega \int\_{V} \frac{e^{i \omega |\bm{r} - \bm{r'}| / c}}{4 \pi |\bm{r} - \bm{r'}|} \Bigg( \bigg( \frac{1}{v_p(\bm{r'}, \omega)} + \frac{i}{v_a(\bm{r'}, \omega)} \bigg)^2 - \frac{1}{c^2} \Bigg) \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

If the volume integral vanishes, we are left with the incident wave propagating at the speed of light \\(c\\). If we wish to replace vacuum with another homogeneous medium, we must reduce the wave velocities in the exterior region by a factor of \\((\eta_1 + i \kappa_1)^{-1}\\). Specifically, in our case, this means we must substitute \\(\varepsilon_1 \mu_1\\) for \\(\epsilon_0 \mu_0\\) and, as a consequence, \\((\varepsilon_1 \mu_1)^{-1/2}\\) for \\(c\\). These modifications do not affect the wave velocities in the interior region. The result is

$$ \tag{11.21}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{A}(0, \bm{n}, \omega) e^{i \omega \sqrt{\varepsilon_1(\omega) \mu_1(\omega)} (\bm{r} \cdot \bm{n})} d\Omega \cr
	&- i \omega \int\_{V} \frac{e^{i \omega \sqrt{\varepsilon_1(\omega) \mu_1(\omega)} |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \big( \varepsilon_2(\bm{r'}, \omega) \mu\_2(\omega) - \varepsilon_1(\omega) \mu_1(\omega) \big) \bm{E}(\bm{r'}, \omega) dV',
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

Since the definition of the Lorenz condition is based on the Maxwell equations in vacuum, it must also be modified. Replacing \\(c\\) in Eqn. 8.11 and 9.20 leads us to the modified version of Eqn. 9.23:

$$ \tag{11.23}
	\bm{E}(\bm{r}, \omega) =
	i \omega \Big( \mathcal{I} + \frac{1}{k_1^2(\omega)} \nabla \otimes \nabla \Big) \cdot \bm{A}(\bm{r}, \omega).
$$

After performing the substitution of Eqn. 11.22, recalling the definition of \\(m\\) given by Eqn. 11.8, and assuming that \\(\bm{r}\\) lies outside the inhomogeneous region, we obtain an expanded version of Eqn. 11.12:

$$ \tag{11.24}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \oint\_{\mathbb{S}^2} \bm{E}(0, \bm{n}, \omega) e^{i k_1(\omega) (\bm{r} \cdot \bm{n})} d\Omega \cr
	&+ k_1^2(\omega) \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big) \Big( \mathcal{I} + \frac{1}{k_1^2(\omega)} \nabla \otimes \nabla \Big) \frac{e^{i k_1(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|} \cdot \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In the future, when no ambiguity arises, we may drop redundant indexing by writing \\(k = k_1\\) and \\(\varepsilon = \varepsilon_1\\):

$$ \tag{11.25}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	= k^2(\omega) \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot \bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega)
	= \frac{k^2(\omega)}{i \omega} \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\mathcal{G}\_m \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot \bm{E}(\bm{r'}, \omega) dV'.
\end{aligned}
$$

In the literature, Eqn. 11.25.1 and Eqn. 11.25.2 are often called the *volume integral equations* \[[8](#references) (ch. 4)\]. They can be used to evaluate the electromagnetic field inside or outside the scattering object.

### Huygens-Fresnel Principle

The volume integral equation expresses the scattered field using the integral taken over the volume \\(V\\) of the scattering object. If its dimensions are large, and the material -- absorptive, then it is likely that a significant portion of its interior will make a negligible contribution to the field outside. Furthermore, determination of the internal field across the entire volume may prove to be both arduous and challenging. Thus, it may be advantageous to convert the volume integral into an integral taken over the surface \\(\partial V\\) (with the outward-facing normal \\(\bm{n}\\)) by applying the second *vector Green theorem* defined in terms of two vector fields \\(\bm{P}\\) and \\(\bm{Q}\\) \[[17](#references) (ch. 4.14)\]:

$$ \tag{1x.1}
\small
	\int_V \left(
		\bm{P} \cdot \nabla \times \nabla \times \bm{Q} -
		\bm{Q} \cdot \nabla \times \nabla \times \bm{P}
	\right) dV
	= \oint_{\partial V} \left(
		\bm{Q} \times \nabla \times \bm{P} -
		\bm{P} \times \nabla \times \bm{Q}
	\right) \cdot \bm{n} dA.
$$

In order for the integrals to converge, the surface must be smooth ([regular](https://mathworld.wolfram.com/RegularSurface.html)), and the integrands -- continuous. In addition, the volume \\(V\\) is supposed to represent a closed region of space (a [compact manifold](https://mathworld.wolfram.com/CompactManifold.html)). However, since every such region can be split in two (and the contributions of the adjacent surfaces cancel out), we may also merge several closed regions into a single connected region bounded by multiple surfaces.

We would like the left-hand side of Eqn. 1x.1 to resemble Eqn. 11.25.1; comparison of these equations reveals that the roles of \\(\bm{r}\\) and \\(\bm{r'}\\) need to be reversed. In particular, this implies that we must keep the observation point \\(\bm{r}\\) fixed, and evaluate the integral with the differential volume element \\(dV' = dV(\bm{r'})\\). In addition, we must take derivatives with respect to \\(\bm{r'}\\); this can be achieved by replacing \\(\nabla\\) with \\(\nabla'\\).

A suitable choice of \\(\bm{P}\\) and \\(\bm{Q}\\) is not obvious. Motivated by Eqn. 9.29 and 11.9, we shall assign the total electric field to \\(\bm{P} = \bm{E}(\bm{r'}, \omega)\\), and pick an arbitrary constant vector \\(\bm{a}\\) for \\(\bm{Q} = \mathcal{G}\_e(\bm{r'}, \bm{r}, k(\omega)) \cdot \bm{a}\\). Please note that we have swapped \\(\bm{r}\\) with \\(\bm{r'}\\).

The continuity requirements of the vector Green theorem have certain practical implications. For instance, if the scattering object represents a particle group, with particle's hard boundaries creating discontinuities in the refractive index of the medium, then, in order to avoid the discontinuities of the electromagnetic field, we must take the integral over each particle separately. Moreover, the Green functions are discontinuous at \\(\bm{r} = \bm{r'}\\). Therefore, in order to make the vector Green theorem applicable, we shall restrict the observation point \\(\bm{r}\\) to the exterior of the scattering object, with \\(\bm{r'}\\) located inside:

$$ \tag{1x.2}
\begin{aligned}
	&\int_V \Big(
		\bm{E} \cdot \left( \nabla' \times \nabla' \times \mathcal{G}\_e \right) \cdot \bm{a} -
		\left( \mathcal{G}\_e \cdot \bm{a} \right) \cdot \left( \nabla' \times \nabla' \times \bm{E} \right)
	\Big) dV'
	\cr
	= &\oint_{\partial V} \Big(
		\left( \mathcal{G}\_e \cdot \bm{a} \right) \times \left( \nabla' \times \bm{E} \right) -
		\bm{E} \times \left( \nabla' \times \mathcal{G}\_e \right) \cdot \bm{a}
	\Big) \cdot \bm{n'} dA'.
\end{aligned}
$$

According to Eqn. 11.7 and 11.9, in the interior region,

$$ \tag{1x.3}
	\nabla' \times \nabla' \times \bm{E}(\bm{r'}, \omega)
	= k^2(\omega) \bm{E}(\bm{r'}, \omega) + k^2(\omega) \left( m^2(\bm{r'}, \omega) - 1 \right) \bm{E}(\bm{r'}, \omega).
$$

Similarly, after interchanging the roles of \\(\bm{r}\\) and \\(\bm{r'}\\) in Eqn. 9.29, we obtain

$$ \tag{1x.4}
	\nabla' \times \nabla' \times \mathcal{G}\_e(\bm{r'}, \bm{r}, k)
	= k^2 \mathcal{G}\_e(\bm{r'}, \bm{r}, k) + \mathcal{I} \delta(\bm{r'} - \bm{r}).
$$

Substitution into Eqn 1x.2 yields

$$ \tag{1x.5}
\begin{aligned}
	&\int_V \Big(
		\bm{E} \cdot \left( k^2 \mathcal{G}\_e + \mathcal{I} \delta \right) \cdot \bm{a} -
		\left( \mathcal{G}\_e \cdot \bm{a} \right) \cdot \left( k^2 \bm{E} + k^2 \left( m^2 - 1 \right) \bm{E} \right)
	\Big) dV'
	\cr
	= &\oint_{\partial V} \Big(
		\left( \mathcal{G}\_e \cdot \bm{a} \right) \times \left( \nabla' \times \bm{E} \right) -
		\bm{E} \times \left( \nabla' \times \mathcal{G}\_e \right) \cdot \bm{a}
	\Big) \cdot \bm{n'} dA'.
\end{aligned}
$$

Since \\(\bm{r} \neq \bm{r'}\\), the Dirac delta function does not contribute to the integral, and can therefore be neglected. Additionally, the inner product of two vectors commutes, so \\(k^2 \bm{E} \cdot \left( \mathcal{G}\_e \cdot \bm{a} \right)\\) cancels out, leading to

$$ \tag{1x.6}
\begin{aligned}
	-k^2 &\int_V
		 ( m^2 - 1 ) ( \mathcal{G}\_e \cdot \bm{a} ) \cdot \bm{E}
	dV'
	\cr
	= &\oint_{\partial V} \Big(
		\left( \mathcal{G}\_e \cdot \bm{a} \right) \times \left( \nabla' \times \bm{E} \right) -
		\bm{E} \times \left( \nabla' \times \mathcal{G}\_e \right) \cdot \bm{a}
	\Big) \cdot \bm{n'} dA'.
\end{aligned}
$$

\\(\left( \mathcal{G} \cdot \bm{a} \right) \cdot \bm{E}\\) can be understood as a matrix-vector product (yielding a column vector) that is transposed and then matrix-multiplied by another column vector. Since the electric dyadic is symmetric, \\(\mathcal{G}\_e = \mathcal{G}\_e^T\\), which allows us to transpose the matrix product and reorder the factors:

$$ \tag{1x.7}
	\left( \mathcal{G}^T \cdot \bm{a} \right) \cdot \bm{E}
	= \left( \mathcal{G}^T \cdot \bm{a} \right)^T \bm{E}
	= \bm{a} \cdot \mathcal{G} \cdot \bm{E}.
$$

Substitution of Eqn. 1x.7 into 1x.6 yields a variant of the volume integral equation

$$ \tag{1x.8}
\begin{aligned}
	-\bm{a} \cdot \bm{E_s}
	&= -\bm{a} \cdot k^2 \int_V
		 (m^2 - 1) (\mathcal{G}\_e \cdot \bm{E})
	dV'
	\cr
	&= \oint_{\partial V} \Big(
		\left( \mathcal{G}\_e \cdot \bm{a} \right) \times \left( \nabla' \times \bm{E} \right) -
		\bm{E} \times \left( \nabla' \times \mathcal{G}\_e \right) \cdot \bm{a}
	\Big) \cdot \bm{n'} dA'.
\end{aligned}
$$

The surface integral can also be simplified. According to Eqn. 3.12,

$$ \tag{1x.9}
	\nabla' \times \bm{E}(\bm{r'}, \omega) = i \omega \bm{B}(\bm{r'}, \omega).
$$

And, if we pay close attention to the order of the arguments, Eqn. 9.35 and 9.48 tell us that

$$ \tag{1x.10}
	\nabla' \times \mathcal{G}\_e(\bm{r'}, \bm{r}, k)
	= i \omega \mathcal{G}\_m(\bm{r'}, \bm{r}, k)
	= -i \omega \mathcal{G}\_m(\bm{r}, \bm{r'}, k).
$$

The surface integral of Eqn. 1x.8 is thus reduced to

$$ \tag{1x.11}
\begin{aligned}
	\bm{a} \cdot \bm{E_s}
	&= \bm{a} \cdot k^2 \int_V
		 (m^2 - 1) (\mathcal{G}\_e \cdot \bm{E})
	dV'
	\cr
	&= i \omega \oint_{\partial V} \Big(
		\bm{B} \times \left( \mathcal{G}\_e \cdot \bm{a} \right) -
		\bm{E} \times \left( \mathcal{G}\_m \cdot \bm{a} \right)
	\Big) \cdot \bm{n'} dA'.
\end{aligned}
$$

At this point, the dyadic Green functions of Eqn. 1x.11 list their arguments in the same (conventional) order: \\(\mathcal{G}\_e(\bm{r}, \bm{r'}, k)\\) due to its symmetry, and \\(\mathcal{G}\_m(\bm{r}, \bm{r'}, k)\\) because of Eqn. 1x.10.

The surface integral features two [scalar triple products](https://en.wikipedia.org/wiki/Triple_product#Scalar_triple_product) of the form \\(\bm{n} \cdot (\bm{E} \times (\mathcal{G} \cdot \bm{a}))\\). We can use its cyclical property to our advantage:

$$ \tag{1x.12}
	\bm{n'} \cdot (\bm{E} \times (\mathcal{G}^T \cdot \bm{a}))
	= (\mathcal{G}^T \cdot \bm{a}) \cdot (\bm{n'} \times \bm{E})
	= \left( \mathcal{G}^T \cdot \bm{a} \right)^T (\bm{n'} \times \bm{E})
	= \bm{a} \cdot \mathcal{G} \cdot \left( \bm{n'} \times \bm{E} \right).
$$

Before we can apply Eqn. 1x.12, both Green dyadics need to be transposed. This operation must be performed carefully; while the electric dyadic is symmetric, the magnetic dyadic is anti-symmetric, so its transpose alters the sign: \\(\mathcal{G}\_m = -\mathcal{G}\_m^T\\).

Application of these identities allows us to move \\(\bm{a}\\) outside the integral:

$$ \tag{1x.13}
\begin{aligned}
	\bm{a} \cdot \bm{E_s}
	&= \bm{a} \cdot k^2 \int_V
		 (m^2 - 1) (\mathcal{G}\_e \cdot \bm{E})
	dV'
	\cr
	&= \bm{a} \cdot i \omega \oint_{\partial V} \Big(
		\mathcal{G}\_e \cdot \left( \bm{n'} \times \bm{B} \right) +
		\mathcal{G}\_m \cdot \left( \bm{n'} \times \bm{E} \right)
	\Big) dA'.
\end{aligned}
$$

Since \\(\bm{a}\\) is an arbitrary constant vector, it can be safely neglected. Therefore,

$$ \tag{1x.14}
\small
	\bm{E_s}(\bm{r}, \omega)
	= i \omega \oint_{\partial V} \Big(
		\mathcal{G}\_e(\bm{r}, \bm{r'}, \omega) \cdot \big( \bm{n'} \times \bm{B}(\bm{r'}, \omega) \big) +
		\mathcal{G}\_m(\bm{r}, \bm{r'}, \omega) \cdot \big( \bm{n'} \times \bm{E}(\bm{r'}, \omega) \big)
	\Big) dA'.
$$

The expression of the magnetic field \\(\bm{B_s}\\) can be determined using Eqn. 3.12, 9.35, and 9.3y:

$$ \tag{1x.15}
\small
	\bm{B_s}(\bm{r}, \omega)
	= i \omega \oint_{\partial V} \Big(
		\mathcal{G}\_m(\bm{r}, \bm{r'}, \omega) \cdot \big( \bm{n'} \times \bm{B}(\bm{r'}, \omega) \big) - \frac{k^2}{\omega^2}
		\mathcal{G}\_e(\bm{r}, \bm{r'}, \omega) \cdot \big( \bm{n'} \times \bm{E}(\bm{r'}, \omega) \big)
	\Big) dA'.
$$

Eqn. 1x.14 and 1x.15 are called the *surface integral equations*. They give the expressions of the scattered field in the region outside the scattering object \\((\bm{r} \notin V)\\) in terms of the *tangential surface fields* \\(\bm{n'} \times \bm{E}\\) and \\(\bm{n'} \times \bm{H} = \mu^{-1}(\bm{n'} \times \bm{B})\\). According to Eqn. 1.17, the latter are continuous across the optical interface, provided the conductivity is not enormous \[[17](#references) (ch. 1.13)\]. More generally speaking, the idea that a smooth (but not necessarily physical) surface can be seen as a source of spherical *wavelets* (secondary waves) interfering with each other is known as the [Huygens-Fresnel principle](https://en.wikipedia.org/wiki/Huygens%E2%80%93Fresnel_principle) \[[4](#references) (ch. 3.1), [25](#references) (ch. 2)\].

### Dipole Radiation

Typically, it is not possible to evaluate the integrals of Eqn. 11.25 in closed form, since the value of the electric field in the interior of the volume is not known. Thus, we must make certain assumptions and employ various approximations in order to make computations feasible. This leads to a number of special cases.

One of the simplest problems that can be solved using our mathematical framework (of classical physics) is that of a single non-magnetic atom or a small molecule embedded in a homogeneous medium. This case corresponds to a tiny particle in vacuum, or an [impurity](https://en.wikipedia.org/wiki/Impurity) in an otherwise pure material. We represent it by an electric dipole -- an oriented point source.

The necessary and sufficient condition for electric dipole radiation is that the radius \\(a\\) of the bounding sphere of the particle must be small compared to the wavelength \\(\lambda\\) computed both inside and outside the particle \[[4](#references) (ch. 6.4)\]. Since \\(k = 2 \pi / \lambda\\), we require that

$$
	|x| = |k a| \ll 1, \quad |m x| \ll 1.
$$

These assumptions radically simplify the scattering problem:

1. the magnitude and the phase of the internal field are similar to those of the external (incident) field;
2. the value of the internal field is uniform across the interior of the particle;
3. the resulting source of radiation is perfectly localized at a single point.

Assume that the particle is non-magnetic and non-conducting. Eqn. 11.18 can then be simplified to

$$ \tag{12.1}
	\bm{J'}(\bm{r}, \omega)
	= i \omega \frac{\bm{J_p}(\bm{r}, \omega)}{\mu_0^{-1}}.
$$

According to Eqn. 10.20.2, polarization current may be attributed to an oscillating dipole:

$$ \tag{12.2}
	\bm{J_p}(\bm{r}, \omega) = -i \omega \bm{p}(V_m, \omega) \delta(\bm{r} - \bm{r_0}),
$$

with the dipole moment defined by Eqn. 10.21:

$$ \tag{12.3}
	\bm{p}(V_m, \omega) = \mathcal{\Alpha_m}(V_m, \omega) \cdot \varepsilon(\omega) \bm{E}(\bm{r_0}, \omega),
$$

where we replaced \\(\epsilon_0\\) with \\(\varepsilon\\) (to account for the properties of the surrounding medium) and \\(\bm{E_m}\\) with \\(\bm{E}\\) (which is only permissible assuming the dipole is sufficiently isolated from the rest of the matter).

The combination of Eqn. 12.1-12.3 results in

$$ \tag{12.4}
	\bm{J'}(\bm{r}, \omega)
	= k^2(\omega) \mathcal{\Alpha_m}(V_m, \omega) \cdot \bm{E}(\bm{r_0}, \omega) \delta(\bm{r} - \bm{r_0}).
$$

Let us substitute Eqn. 12.4 into Eqn. 11.10-11.11:

$$ \tag{12.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	= k^2(\omega) \mathcal{G}\_e \big( \bm{r}, \bm{r_0}, k(\omega) \big) \cdot \mathcal{\Alpha_m}(V_m, \omega) \cdot \bm{E}(\bm{r_0}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	= \frac{k^2(\omega)}{i \omega} \mathcal{G}\_m \big( \bm{r}, \bm{r_0}, k(\omega) \big) \cdot \mathcal{\Alpha_m}(V_m, \omega) \cdot \bm{E}(\bm{r_0}, \omega).
\end{aligned}
$$

Comparison with Eqn. 11.25 shows that Eqn. 12.5 corresponds to the contribution of a volume element occupied by a single atom or a small molecule, and with the polarizability \\(\mathcal{\Alpha_m}\\) taking the role of the volume integral of the relative wavenumber \\(m\\). In particular, if the polarizability is isotropic, so that \\(\mathcal{\Alpha_m} = \alpha_m\\),

$$ \tag{12.6}
	\alpha_m(V_m, \omega)
	= \int_{V_m} \big( m^2(\bm{r}, \omega) - 1 \big) dV_m.
$$

This relation also follows from Eqn. 10.18.4, 10.21, and 10.26, under the assumption that the polarization \\(\bm{P}\\) vanishes outside the volume occupied by the dipole.

Finally, note that, since \\(m\\) is dimensionless, Eqn. 12.6 implies that the polarizability has units of volume \[[4](#references) (ch. 6.22)\].

### Spherical Waves

Let us focus on the case when the observation point is far away from the source. It is important for two reasons: first, if we are dealing with small particles, a typical observation falls into this category, and second, because this assumption greatly simplifies the behavior of the scattered wave, as we shall soon demonstrate.

The idea behind the far-field approximation is simple: we discard certain terms in Eqn. 9.44 and 9.50. In order to do that, we must consider their magnitudes relative to each other:

$$ \tag{13.1}
	\mathcal{G}\_e
	= \bigg( 1 + \frac{\mathcal{G_{et}}}{\mathcal{G_{ef}}} + \frac{\mathcal{G_{en}}}{\mathcal{G_{ef}}} \bigg) \mathcal{G_{ef}}, \quad
	\mathcal{G}\_m
	= \bigg( 1 + \frac{\mathcal{G_{mn}}}{\mathcal{G_{mf}}} \bigg) \mathcal{G_{mf}}.
$$

According to Eqn. 9.45, for any fixed direction, the relative difference between the three electric dyadics primarily arises from the leading scalar terms. Moreover, the two magnetic dyadics given by Eqn. 9.51 exhibit identical angular dependence. Following this logic, we use Eqn. 9.46 and 9.52 to approximate

$$ \tag{13.2}
	\mathcal{G}\_e
	= \bigg( 1 + \mathrm{O} \Big( (k R)^{-1} \Big) + \mathrm{O} \Big( (k R)^{-2} \Big) \bigg) \mathcal{G_{ef}}, \quad
	\mathcal{G}\_m
	= \bigg( 1 + \mathrm{O} \Big( (k R)^{-1} \Big) \bigg)
	\mathcal{G_{mf}}.
$$

If \\(k R \gg 1\\), the values of the expressions in the brackets approach 1. This allows us to write the asymptotic form of Eqn. 12.5 as

$$ \tag{13.3}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\simeq k^2(\omega) \frac{e^{i k(\omega) R}}{4 \pi R}
	\bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{R^2} \bigg) \cdot \mathcal{\Alpha_m}(V_m, \omega) \cdot \bm{E}(\bm{r'}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\simeq \frac{k^3(\omega)}{\omega} \frac{e^{i k(\omega) R}}{4 \pi R}
	\bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) \cdot \mathcal{\Alpha_m}(V_m, \omega) \cdot \bm{E}(\bm{r'}, \omega),
\end{aligned}
$$

where we renamed \\(\bm{r_0}\\) to \\(\bm{r'}\\) in order to avoid introducing additional auxiliary variables. This allows us to reuse the previous definitions of \\(\bm{R} = \bm{r} - \bm{r'}, R = |\bm{R}|\\).

Let us stop for a moment to consider what \\(k R\\) represents. According to Eqn. 13.3, it is the [dimensionless](https://en.wikipedia.org/wiki/Dimensionless_quantity) phase difference (measured in radians) between the observation point \\(\bm{r}\\) and the location of the dipole \\(\bm{r'}\\). We may confirm this by dimensional analysis of Eqn. 7.9:

$$ \tag{13.4}
	k R = (\eta + i \kappa) \omega c^{-1} R = [\text{1}] [\text{rad/s}] [\text{s/m}] [\text{m}] = [\text{rad}].
$$

Intuitively, \\(k R \gg 1\\) implies that the wave generated by the dipole will go through a great number of cycles before reaching the observation point.

Eqn. 13.3 can be reduced to a simpler form by aligning the origin of the coordinate system with the location of the dipole (so that \\(\bm{r'} = 0\\) and \\(\bm{R} = \bm{r}\\)) and introducing the direction of observation \\(\bm{n} = \bm{r}/|\bm{r}| = \bm{r} / r\\):

$$ \tag{13.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\simeq k^2(\omega) \frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \cdot \mathcal{\Alpha_m}(V_m, \omega)
	\cdot \bm{E_0}(\bm{n_i}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\simeq \frac{k^3(\omega)}{\omega} \frac{e^{i k(\omega) r}}{4 \pi r}
	\big(\bm{n} \times \mathcal{I} \big) \cdot \mathcal{\Alpha_m}(V_m, \omega)
	\cdot \bm{E_0}(\bm{n_i}, \omega)
\end{aligned}
$$

Let us return to the general case of a scattering object. Assume that the observation point is in the radiation zone with respect to each individual volume element, so that \\(k R \gg 1\\). Eqn. 11.25 then takes the form

$$ \tag{13.6}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\simeq k^2(\omega) \int\_{V}
	\big( m^2(\bm{r'}, \omega) - 1 \big) \frac{e^{i k(\omega) R}}{4 \pi R} \bigg( \mathcal{I} - \frac{\bm{R} \otimes \bm{R}}{\bm{R} \cdot \bm{R}} \bigg) \cdot \bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega)
	\simeq \frac{k^3(\omega)}{\omega} \int\_{V}
	\big( m^2(\bm{r'}, \omega) - 1 \big) \frac{e^{i k(\omega) R}}{4 \pi R} \bigg(\frac{\bm{R} \times \mathcal{I}}{R} \bigg) \cdot \bm{E}(\bm{r'}, \omega) dV'.
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
	= k r \bigg( 1 - (\bm{n} \cdot \bm{n'}) (r'/r) + \frac{1}{2} \Big( 1 - ( \bm{n} \cdot \bm{n'})^2 \Big) (r'/r)^2 + \mathrm{O} \Big( (r'/r)^3 \Big) \bigg), \cr
	& \frac{1}{R}
	= \frac{1}{r} \bigg( 1 + (\bm{n} \cdot \bm{n'}) (r'/r) + \mathrm{O} \Big( (r'/r)^2 \Big) \bigg).
\end{aligned}
$$

The second expression varies relatively slowly, so its expansion can be truncated by assuming that \\(r'/r \ll 1\\) for all \\(r'\\), which implies that \\(a/r \ll 1\\). The first expression is utilized in a rapidly-varying exponential function that is sensitive to the absolute error, so we should retain an extra term by assuming that \\(k a^2 / r \ll 1\\). Thus, we arrive at the approximation

$$ \tag{13.9}
	k R \simeq k r - k (\bm{r'} \cdot \bm{n}), \quad
	\frac{1}{R} \simeq \frac{1}{r}.
$$

Next, we shall examine the expression of the directions of observation of individual volume element:

$$ \tag{13.10}
\begin{aligned}
	\frac{\bm{R}}{R}
	&= \frac{\bm{r} - \bm{r'}}{r \sqrt{1 - 2 \big( \bm{n} \cdot \bm{n'} \big) (r'/r) + (r'/r)^2}} \cr
	&= \bigg( \bm{n} - (r'/r) \bm{n'} \bigg) \bigg( 1 + \big( \bm{n} \cdot \bm{n'} \big) (r'/r) + \mathrm{O} \Big( (r'/r)^2 \Big) \bigg).
\end{aligned}
$$

Assume that the sphere covers a small solid angle when viewed from the observation point, which implies that \\(a/r \ll 1\\) . As the bounding surface shrinks to a point, the directions of observation corresponding to individual volume elements align, and we may write

$$ \tag{13.11}
	\frac{\bm{R}}{R} \simeq \bm{n}.
$$

Substitution of Eqn. 13.9 and 13.11 into Eqn. 9.45.3 and 9.51.2 results in the following approximate expressions of the far-field Green dyadics:

$$ \tag{13.1x}
\begin{aligned}
	\mathcal{G_{ef}}(\bm{R}, k)
	& \simeq \frac{e^{i k r - i k (\bm{r'} \cdot \bm{n})}}{4 \pi r} \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big),
	\cr
	\mathcal{G_{mf}}(\bm{R}, k)
	& \simeq i k \frac{e^{i k r - i k (\bm{r'} \cdot \bm{n})}}{4 \pi r} \big(\bm{n} \times \mathcal{I} \big),
\end{aligned}
$$

subject to the conditions \[[8](#references) (ch. 5)\]

$$ \tag{13.13}
	k r - k a \gg 1, \quad
	kr \gg ka, \quad
	\frac{kr}{k a} \gg k a.
$$

Eqn. 13.1x can be used to define the *far-field approximation* of the volume integral equation (cf. Eqn. 11.25):

$$ \tag{13.12}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) \simeq k^2(\omega)
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \cdot
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV', \cr
	& \bm{B_s}(\bm{r}, \omega) \simeq \frac{k^3(\omega)}{\omega}
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\big(\bm{n} \times \mathcal{I} \big) \cdot
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV', \cr
\end{aligned}
$$

The *far-field approximation* of the surface integral equation (cf. Eqn. 1x.14-1x.15) can be obtained the same way:

$$ \tag{13.1y}
\begin{aligned}
	\bm{E_s}(\bm{r}, \omega)
	&\simeq i \omega \frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \cdot
	\oint_{\partial V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})}
	\big( \bm{n'} \times \bm{B}(\bm{r'}, \omega) \big) dA'
	\cr
	&- \omega k(\omega) \frac{e^{i k(\omega) r}}{4 \pi r}
	\big(\bm{n} \times \mathcal{I} \big) \cdot
	\oint_{\partial V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})}
	\big( \bm{n'} \times \bm{E}(\bm{r'}, \omega) \big) dA',
	\cr
	\bm{B_s}(\bm{r}, \omega)
	&\simeq \frac{k^2(\omega)}{i \omega} \frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \cdot
	\oint_{\partial V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})}
	\big( \bm{n'} \times \bm{E}(\bm{r'}, \omega) \big) dA'
	\cr
	& -\omega k(\omega) \frac{e^{i k(\omega) r}}{4 \pi r}
	\big(\bm{n} \times \mathcal{I} \big) \cdot
	\oint_{\partial V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})}
	\big( \bm{n'} \times \bm{B}(\bm{r'}, \omega) \big) dA',
\end{aligned}
$$

where \\(\bm{n'}\\) is the outward-facing surface normal associated with \\(dA' = dA(\bm{r'})\\), which does, in general, differ from \\(\bm{r'}/|\bm{r}|\\), unless the particle is spherical.

We would like to caution that these conditions are, strictly speaking, only valid for the expressions of the fields. If one considers a derived quantity (such as the time-averaged Poynting vector), the results of the preceding error analysis are no longer accurate, and the final expression should be examined instead.

Eqn. 13.5, 13.12, and 13.1y show an example of *vector* spherical waves, since the global phase factor \\(\exp(i k r)\\) does not depend on the direction of observation, so the initial phase value propagates in all directions at the same (phase) velocity. Note that surfaces of constant phase do not coincide with surfaces of constant amplitude, since the latter typically depends on the direction of observation.

An important property of the far-field solution is the fact that the scattered field vectors are transverse with respect to the direction of observation:

$$ \tag{13.14}
\begin{aligned}
	& \bm{n} \cdot \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big)
	= \bm{n} - (\bm{n} \cdot \bm{n}) \bm{n}
	= 0, \cr
	& \bm{n} \cdot \big( \bm{n} \times \mathcal{I} \big)
	= -\bm{n} \cdot \big( \mathcal{I} \times \bm{n} \big)
	= -\bm{n} \times \bm{n}
	= 0.
\end{aligned}
$$

We can also show that, under certain conditions, the electric and the magnetic fields are mutually orthogonal. We begin by factoring out the common term

$$ \tag{13.15}
	\bm{K}(\bm{r}, \omega)
	= \frac{k^2(\omega)}{4 \pi} \frac{e^{i k(\omega) r}}{r} \int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV',
$$

which simplifies Eqn. 13.12 to

$$ \tag{13.16}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\simeq \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \cdot \bm{K}(\bm{r}, \omega),
	\cr
	& \bm{B_s}(\bm{r}, \omega)
	\simeq \frac{k(\omega)}{\omega} \big(\bm{n} \times \mathcal{I} \big) \cdot \bm{K}(\bm{r}, \omega).
\end{aligned}
$$

Now, take the cross product of the direction of observation with Eqn. 13.16.1, multiply the result by \\(k/\omega\\), and compare it to Eqn. 13.16.2. The expressions are clearly identical:

$$ \tag{13.17}
	\frac{k(\omega)}{\omega} \bm{n} \times \bm{E_s}(\bm{r}, \omega)
	\simeq \bm{B_s}(\bm{r}, \omega)
$$

Furthermore, notice the resemblance of Eqn. 13.17 to 7.22.

Next, multiply Eqn. 13.17 by \\(\exp(-i \omega t)\\), expand the expression of the complex wavenumber as per Eqn. 7.9, and take the real part of both sides of the equation:

$$ \tag{13.18}
	\bm{n} \times \bigg(
	\frac{\eta(\omega)}{c} \mathcal{Re} \big\lbrace \bm{E_s} e^{-i \omega t} \big\rbrace
	- \frac{\kappa(\omega)}{c} \mathcal{Im} \big\lbrace \bm{E_s} e^{-i \omega t} \big\rbrace \bigg)
	\simeq \mathcal{Re} \big\lbrace \bm{B_s} e^{-i \omega t} \big\rbrace.
$$

We encounter a situation similar to vector plane waves, where the absorptive character of the homogeneous medium poses a complication. If we assume that \\(\kappa = 0\\), the field vectors become orthogonal, and we obtain a counterpart of Eqn. 7.29:

$$ \tag{13.19}
	\frac{\big| \mathcal{Re} \big\lbrace \bm{B_s}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace \big|}{\big| \mathcal{Re} \big\lbrace \bm{E_s}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace \big|}
	\simeq \frac{\eta(\omega)}{c}.
$$

Thus, at a large distance, for a fixed direction of observation, a spherical wave is an analog of a plane wave with the amplitude decreasing proportionally to \\(k r\\):

$$ \tag{13.20}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) e^{-i \omega t}
	\simeq \frac{1}{k r} \bm{E_1}(\bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n}) - i \omega t},
	\cr
	& \bm{B_s}(\bm{r}, \omega) e^{-i \omega t}
	\simeq \frac{1}{k r} \bm{B_1}(\bm{n}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n}) - i \omega t}.
\end{aligned}
$$

Comparison with Eqn. 7.2 and 13.12 makes it clear that

$$ \tag{13.??}
\begin{aligned}
	& \bm{E_1}(\bm{n}, \omega)
	= \frac{k^3(\omega)}{4 \pi} \big( \mathcal{I} - \bm{n} \otimes \bm{n} \big) \cdot \int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV',
	\cr
	& \bm{B_1}(\bm{n}, \omega)
	= \frac{k^4(\omega)}{4 \pi \omega} \big(\bm{n} \times \mathcal{I} \big) \cdot \int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n})} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\bm{E}(\bm{r'}, \omega) dV',
\end{aligned}
$$

are the *polarization phasors* of the spherical wave. Note that we must divide by \\(k r\\) (rather than just \\(r\\)) in order for \\(\bm{E_1}\\) and \\(\bm{B_1}\\) to have the same units as the electric and the magnetic field, respectively. This may fail to match the convention used by other authors \[[6](#references) (ch. 13.6), [8](#references) (ch. 5.1)\].

[Picture]

If the field vectors are orthogonal, and the ratio of their magnitudes is known, the (monochromatic) Poynting vector has the form

$$ \tag{13.21}
	\bm{S}(\bm{r}, t)
	\simeq \mu\_0^{-1} \frac{\eta(\omega)}{c} {\big| \mathcal{Re} \big\lbrace \bm{E_s}(\bm{r}, \omega) e^{-i \omega t} \big\rbrace \big|}^2 \bm{n}.
$$

Similarly to the case of plane waves, we may form a Cartesian coordinate frame using the set of vectors \\(\lbrace \bm{E_1}, \bm{B_1}, \bm{n} \rbrace\\), we obtain the expressions of the Poynting vector (cf. Eqn. 7.31)

$$ \tag{13.22}
	\bm{S}(\bm{r}, t)
	\simeq \mu\_0^{-1} \frac{\eta}{c} \bigg( \frac{|E_{1,x}|^2}{k^2 r^2} \cos^2{\theta_x} + \frac{|E_{1,y}|^2}{k^2 r^2} \cos^2{\theta_y} \bigg) \bm{n}
$$

and irradiance (cf. Eqn. 7.32)

$$ \tag{13.23}
	\Epsilon(\bm{n'})
	= \braket{\bm{S}} \cdot \bm{n'}
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{|\bm{E_1}|^2}{k^2 r^2} \big( \bm{n} \cdot \bm{n'} \big)
$$

that follow the [inverse-square law](https://en.wikipedia.org/wiki/Inverse-square_law).

For completeness, we would like to point out that it is possible to support the case when the host medium is absorptive and magnetic. The absorption requirement makes \\(k\\) complex; in addition, we must replace \\(\mu\_0\\) by \\(\mu_1(\omega)\\). Since the vectors are no longer orthogonal, Eqn. 13.21-13.23 become invalid, and we must follow the Poynting phasor route given by Eqn. 4.16-4.17 instead:

$$ \tag{13.24}
	\braket{\bm{S}}
	\simeq \frac{1}{2} \mathcal{Re} \Big\lbrace \bm{E_s}(\bm{r}, \omega) \times \big[ \bm{H_s}(\bm{r}, \omega) \big]^{\*} \Big\rbrace
	= \frac{1}{2 |k r|^2} \mathcal{Re} \Big\lbrace \bm{E_1}(\bm{r}, \omega) \times \big[ \bm{H_1}(\bm{r}, \omega) \big]^{\*} \Big\rbrace.
$$

Compare Eqn. 13.24 to 7.35; except for the names of the fields, the equations are essentially the same. Thus, the analysis performed in Eqn. 7.36-7.41 still holds, and we can reuse it to arrive at the analog of Eqn. 7.42:

$$ \tag{13.25}
	\braket{\bm{S}}
	\simeq \frac{1}{2} \mathcal{Re} \bigg\lbrace
		\sqrt{ \frac{\varepsilon}{\mu} }
	\bigg\rbrace \frac{|\bm{E_1}|^2}{|k r|^2} e^{-\alpha (\bm{r} \cdot \bm{n})} \bm{n},
$$

where the absorption coefficient \\(\alpha\\) is defined according to Eqn. 7.40.

### Transition Operator

The volume integral equation can be reduced to a relatively simple expression by assuming that the observation point is located in the radiation zone. However, in order to be able to compute the scattered field, we must first relate the internal field to the one acting on the scattering object, i.e. the external (incident) field in the absence of the object.

Let us recall the mechanics of scattering. In a dielectric, the incident field acts (exerts force) on the dipoles, causing them to oscillate, and therefore radiate scattered wavelets interfering with each other. As a result, each dipole becomes a secondary source, exerting force on all other dipoles surrounding it. Repeated application of this iterative approach leads to evaluation of successive orders of scattering one by one. This is the physical intuition behind the [Born series](https://en.wikipedia.org/wiki/Born_series) expansion \[[6](#references) (ch. 13.1)\].

We can formalize this approach in the following way \[[8](#references) (ch. 4.5)\]. We start with Eqn. 11.12, and substitute the expression of the source term \\(\bm{J'}\\) given by Eqn. 11.7:

$$ \tag{14.2}
	\bm{E}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega)
	+ \int\_{V} k^2(\omega) \big( m^2(\bm{r'}, \omega) - 1 \big) \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot \bm{E}(\bm{r'}, \omega) dV'.
$$

For convenience, we define the *potential function*

$$ \tag{14.3}
	u(\bm{r}, k, \omega) =
	\begin{cases}
	   k^2 \big( m^2(\bm{r}, \omega) - 1 \big) &\text{if } \bm{r} \in V, \cr
	   0 &\text{otherwise},
	\end{cases}
$$

that allows us to write Eqn. 14.2 in a more compact form:

$$ \tag{14.4}
	\bm{E}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega)
	+ \int\_{V} u \big( \bm{r'}, k(\omega), \omega \big) \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot \bm{E}(\bm{r'}, \omega) dV'.
$$

The resulting equation is recursive. Substitution makes it fairly obvious that the total electric field can be expressed as volume integral of the transformed incident field. While this fact can be rigorously derived from the properties of the dyadic Green functions \[[12](#references) (ch. 5.1)\], for simplicity, let us assume that the solution exists:

$$ \tag{14.5}
	u \big( \bm{r'}, k(\omega), \omega \big) \bm{E}(\bm{r'}, \omega)
	= \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, k(\omega), \omega \big) \cdot \bm{E_i}(\bm{r''}, \omega) dV'',
$$

where \\(\mathcal{T}\\) is the *transition dyadic*, the expression of which is yet to be determined.

Recursive equations written in the integral form quickly become unwieldy. Instead, we shall convert them into the operator form by using the [bra-ket notation](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation). Additionally, any dependence on the frequency or the wavenumber shall be kept implicit.

Our building blocks are state vectors (or *kets*), such as \\(\ket{E}\\), and linear functionals (or *bras*), such as \\(\bra{r}\\). Put together, they represent the projection (the [inner product](https://en.wikipedia.org/wiki/Inner_product)) of the two vectors:

$$ \tag{14.6}
	  \braket{r | E}
	= \big( \ket{r}, \ket{E} \big)
	= \bm{E}(\bm{r}).
$$

In addition, we can define the *potential operator* \\(U\\), such that, in the position representation,

$$ \tag{14.7}
	\bra{r} U = u(\bm{r}) \bra{r}.
$$

Similarly, we define the *Green operator* \\(G\\) and the *transition operator* \\(T\\) as

$$ \tag{14.8}
	\braket{r | G | r'} = \mathcal{G}\_e(\bm{r}, \bm{r'}), \quad
	\braket{r | T | r'} = \mathcal{T}(\bm{r}, \bm{r'}).
$$

Finally, we must define the *orthonormalization* relation

$$ \tag{14.9}
	  \braket{r | r'} = \delta(\bm{r} - \bm{r'})
$$

and the *closure* relation

$$ \tag{14.10}
	\int dV \ket{r} \bra{r} = \mathcal{I},
$$

which, when taken together, say that the set of \\(\ket{r}\\) vectors constitutes a basis.

In the new notation, Eqn. 14.4 can be written as

$$ \tag{14.11}
	\braket{r | E}
	= \braket{r | E_i}
	+ \int dV' \braket{r | G | r'} \braket{r' | U | E},
$$

or, in the representation-independent form,

$$ \tag{14.12}
	\ket{E} = \ket{E_i} + G U \ket{E}.
$$

Eqn. 14.12 is formally identical to the [Lippmann–Schwinger equation](https://en.wikipedia.org/wiki/Lippmann%E2%80%93Schwinger_equation).

Similarly, Eqn. 14.5 can be transformed into

$$ \tag{14.13}
	U \ket{E} = T \ket{E_i},
$$

which shows a transition from the state \\(\ket{E_i}\\) to \\(\ket{E}\\).

Substitution of Eqn. 14.13 into 14.12 produces an alternative expression of the total field

$$ \tag{14.14}
	\ket{E} = \ket{E_i} + G T \ket{E_i}.
$$

In the position representation,

$$ \tag{14.15}
	\braket{r | E}
	= \braket{r | E_i}
	+ \int dV' \int dV'' \braket{r | G | r'}
	\braket{r' | T | r''} \braket{r'' | E},
$$

and the corresponding dyadic expression is

$$ \tag{14.16}
	\bm{E}(\bm{r})
	= \bm{E_i}(\bm{r})
	+ \int\_{V} \mathcal{G}\_e (\bm{r}, \bm{r'}) \cdot \int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}) \cdot \bm{E_i}(\bm{r''}) dV'' dV'.
$$

Note that Eqn. 14.16 is identical to the combination of Eqn. 14.4 and 14.5.

Now, let us multiply both sides of Eqn. 14.14 by \\(U\\):

$$ \tag{14.17}
	U \ket{E}
	= U \ket{E_i} + U G T \ket{E_i}
	= U (I + G T) \ket{E_i},
$$

and substitute Eqn. 14.13 once more:

$$ \tag{14.18}
	T \ket{E_i} = U (I + G T) \ket{E_i}.
$$

Since the ket \\(\ket{E_i}\\) is arbitrary, it means the transition operator does not depend on the incident field:

$$ \tag{14.19}
	T = U (I + G T)
$$

It does, however, depend on the geometry and the structure of the scattering object.

Notice that, when we combine Eqn. 14.13 and 14.19, the potential operator cancels out, and we obtain the Born series expansion

$$ \tag{14.20}
\begin{aligned}
	\ket{E}
	&= (I + G T) \ket{E_i} \cr
	&= \big( I + G U (I + G T) \big) \ket{E_i} \cr
	&= \ket{E_i} + G U \ket{E_i} + (G U)^2 \ket{E_i} + \dots
\end{aligned}
$$

that corresponds to the recursive substitution of Eqn. 14.4.

Let us now complete the derivation by writing Eqn. 14.19 in the position representation:

$$ \tag{14.21}
\begin{aligned}
	\braket{r | T | r'}
	&= \braket{r | U (I + G T) | r'} \cr
	&= u(\bm{r}) \braket{r | I + G T | r'} \cr
	&= u(\bm{r}) \bigg( \negmedspace \braket{r | r'} + \int dV \braket{r | G | r''} \braket{r'' | T | r'} \negmedspace \bigg).
\end{aligned}
$$

Translation of Eqn. 14.21 into the integral form yields the definition of the transition dyadic:

$$ \tag{14.22}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= u(\bm{r}, k) \bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I} + \int_V \mathcal{G}\_e (\bm{r}, \bm{r''}, k) \cdot \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg)
	\cr
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I} + \int_V \mathcal{G}\_e (\bm{r}, \bm{r''}, k) \cdot \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

The expression of the electric dyadic (defined in Eqn. 9.27) can be expanded in two different ways. We can express it as a product, and move the derivatives outside the integral, as shown by Eqn. 9.24:

$$ \tag{14.23}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&+ \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) \cdot \int\_{V}
	g( \bm{r} - \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

Alternatively, we can decompose it into a sum of the dyadic Green function \\(\mathcal{G}\\) and the depolarization dyadic \\(\mathcal{L}\\) as per by Eqn. 9.27:

$$ \tag{14.24}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&- \frac{1}{k^2} \mathcal{L}(\bm{r}) \cdot \mathcal{T} (\bm{r}, \bm{r'})
	+ \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G} (\bm{r}, \bm{r''}) \cdot \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

Notice that the \\(1/k^2\\) term in front of the depolarization dyadic cancels out. If we move it to the left-hand side of the equation and rearrange the terms, we obtain the full definition of the transition dyadic:

$$ \tag{14.25}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big)
	\Big( \mathcal{I} + \big( m^2(\bm{r}) - 1 \big) \mathcal{L}(\bm{r}) \Big)^{-1}
	\cr
	&\cdot
	\bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I}
	+ \lim_{\delta \to 0} \int\_{V - V_{\delta}} \mathcal{G} (\bm{r}, \bm{r''}, k) \cdot \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

### Scattering Matrix

An arbitrary stream of electromagnetic radiation can decomposed into a number of  plane waves. Specifically, in a source-free region of a linear, isotropic, homogeneous medium, the expression of the incident field is given by Eqn. 6.16:

$$ \tag{15.1}
	\bm{E_i}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E_i}(\bm{r}, \bm{n_i}, \omega) d\Omega_i =
	\oint\_{\mathbb{S}^2} \bm{E_0}(\bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i})} d\Omega_i.
$$

We have also learned that the scattered field is a linear function of the incident field. This allows us to utilize the superposition principle, according to which, the scattered field is a superposition of scattered waves generated by individual incident plane waves. Stated mathematically,

$$ \tag{15.2}
	\bm{E_s}(\bm{r}, \omega) =
	\oint\_{\mathbb{S}^2} \bm{E_s}(\bm{r}, \bm{n_i}, \omega) d\Omega_i.
$$

We may expand the integrand using Eqn. 14.16 and 15.1:

$$ \tag{15.3}
\begin{aligned}
	\bm{E_s}(\bm{r}, \bm{n_i}, \omega)
	&= \int\_{V} \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot
	\int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) \cdot \bm{E_i}(\bm{r''}, \bm{n_i}, \omega) dV'' dV'
	\cr
	&= \int\_{V} \mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot
	\int\_{V} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) \cdot \bm{E_0}(\bm{n_i}, \omega) e^{i k(\omega) (\bm{r''} \cdot \bm{n_i})} dV'' dV'.
\end{aligned}
$$

If we take the polarization phasor \\(\bm{E_0}\\) outside the integral, it becomes apparent that the scattered field may be related to the incident field by the *scattering dyadic* \\(\mathcal{S}\\) \[[8](#references) (ch. 5.3)\]:

$$ \tag{15.4}
	\bm{E_s}(\bm{r}, \bm{n_i}, \omega)
	= \frac{e^{i k(\omega) r}}{k(\omega) r} \mathcal{S_e} (\bm{r}, \bm{n_i}, \omega) \cdot \bm{E_0}(\bm{n_i}, \omega).
$$

In general, the scattering dyadic depends on the position of the observation point relative to the scatterer. However, if the observation point is very far away, we can utilize the far-field expression of the electric dyadic, and combine Eqn. 13.12, 14.3, and 14.5 to write

$$ \tag{15.5}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega) \simeq
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s})} \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, k(\omega), \omega \big) \cdot \bm{E_i}(\bm{r''}, \omega) dV'' dV', \cr
	& \bm{B_s}(\bm{r}, \omega) \simeq
	\frac{k(\omega)}{\omega} \frac{e^{i k(\omega) r}}{4 \pi r}
	\big(\bm{n_s} \times \mathcal{I} \big) \cdot
	\int\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s})} \int\_{V} \mathcal{T} \big( \bm{r'}, \bm{r''}, k(\omega), \omega \big) \cdot \bm{E_i}(\bm{r''}, \omega) dV'' dV',
\end{aligned}
$$

making the far-field scattering dyadics independent from the distance to the observation point:

$$ \tag{15.6}
\begin{aligned}
	& \bm{E_s}(\bm{r}, \omega)
	\simeq \frac{e^{i k(\omega) r}}{k(\omega) r} \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \cdot \bm{E_0}(\bm{n_i}, \omega), \cr
	& \bm{B_s}(\bm{r}, \omega)
	\simeq \frac{k(\omega)}{\omega} \bm{n_s} \times \bm{E_s}(\bm{r}, \omega).
\end{aligned}
$$

Comparison with Eqn. 13.20 directly leads to

$$ \tag{15.?}
\begin{aligned}
	& \bm{E_1}(\bm{n_s}, \omega)
	= \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \cdot \bm{E_0}(\bm{n_i}, \omega),
	\cr
	& \bm{B_1}(\bm{r}, \omega)
	= \frac{k(\omega)}{\omega} \bm{n_s} \times \bm{E_1}(\bm{r}, \omega).
\end{aligned}
$$

For completeness, the formula of the electric far-field scattering dyadic is given below:

$$ \tag{15.7}
\begin{aligned}
	& \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \simeq
	\frac{k(\omega)}{4 \pi}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot
	\iint\_{V} e^{-i k(\omega) (\bm{r'} \cdot \bm{n_s} - \bm{r''} \cdot \bm{n_i})} \mathcal{T} (\bm{r'}, \bm{r''}, k(\omega), \omega) dV'' dV'.
\end{aligned}
$$

At the end of this section [EG: reorder], we will demonstrate that \\(\mathcal{T} \sim k^5\\), which implies that both of these dyadics are dimensionless. In comparison, certain authors prefer to divide their dyadics by \\(k\\) \[[8](#references) (ch. 5.3)\].

All the expressions given above are independent of the choice of a coordinate system. In practice, in order to perform computations, it is necessary to use a specific reference frame.

{{< figure src="/img/scat_coord.svg" caption="*Figure N: the scattering coordinate system.*" >}}

First, recall that \\(r\\) in Eqn. 15.6 stands for the distance from the center of the scattering object to the observation point. This makes the center of the scatterer a suitable choice for the origin of the coordinate system.

Next, assume that the direction of incidence \\(\bm{n_i}\\) is fixed; conventionally, it is aligned with the \\(z\\)-axis. That is convenient, because it allows us to describe the incident electric field vector (or phasor) using only two Cartesian components, \\(x\\) and \\(y\\), since, according to Eqn. 7.18-7.19, the component along \\(\bm{n_i}\\) is zero.

We must also choose the *plane of reference* that fixes the orientation of the coordinate frame \[[4](#references) (ch. 5.11)\]. The decision is somewhat arbitrary (except for the fact that the plane should contain \\(\bm{n_i}\\) for the reason outlined above). A simple choice that reduces the number of degrees of freedom is to use the *plane of observation* spanned by \\(\bm{n_i}\\) and \\(\bm{n_s}\\) \[[4](#references) (ch. 5.22)\]. In summary, the coordinate convention for the incident field is

$$ \tag{15.8}
	\bm{e_z} = \bm{n_i}, \quad
	\bm{e_y} = \frac{\bm{n_i} \times \bm{n_s}}{| \bm{n_i} \times \bm{n_s} |}, \quad
	\bm{e_x} = \bm{e_y} \times \bm{e_z}.
$$

If \\(\bm{n_i}\\) and \\(\bm{n_s}\\) are collinear, a supporting vector must take the role of \\(\bm{n_s}\\) in order to fix the orientation of the coordinate frame.

Imagine that the *scattering coordinate system* \\(xyz\\) is initially aligned with the *laboratory reference frame* \\(XYZ\\), such that

$$ \tag{15.9}
	\bm{e_Z} = \bm{n_i}, \quad
	\bm{e_Y} = \bm{n_i} \times \bm{n_p}, \quad
	\bm{e_X} = \bm{n_p},
$$

where \\(\bm{n_p}\\) is the principal axis of the particle (specifically chosen to be orthogonal to \\(\bm{n_i}\\)).

The latter could, for instance, represent the coordinate frame of the scatterer. We can now rotate \\(xyz\\) and define its orientation relative to \\(XYZ\\) in terms of the [Euler angles](https://en.wikipedia.org/wiki/Euler_angles). We are particularly interested in rotating the plane of observation about the \\(Z\\)-axis, as that allows the set of possible direction of observation to cover the entire the unit sphere. If the azimuthal (plane rotation) angle is \\(\phi\\) and the zenith angle is \\(\theta\\), the spherical coordinates of \\(\bm{n_s}\\) with respect to the \\(XYZ\\) frame are

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
\end{bmatrix}.
$$

Note that we must use the [atan2](https://en.wikipedia.org/wiki/Atan2) function in order to realize the full range of values of \\(\theta\\) and \\(\phi\\).

It can be seen that \\(\theta = 0\\) corresponds to *forward scattering*, and \\(\theta = \pi\\) -- to *back-scattering*.

Unfortunately, the rotation of the coordinate frame causes the values of the components of the incident electric field to change \[[4](#references) (ch. 5.31)\]. If the angle of rotation of the plane is \\(\phi\\), the field vectors (and phasors) are [passively](https://en.wikipedia.org/wiki/Active_and_passive_transformation#Passive_transformation) [rotated](https://en.wikipedia.org/wiki/Rotation_matrix#Basic_rotations) by \\(-\phi\\) radians:

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

On the other hand, this convention makes computing the coordinates of the scattered field vectors (and phasors) easy. Since \\(\bm{n_s}\\) is orthogonal to \\(\bm{e_y}\\), we can define a spherical coordinate system centered at the particle:

$$ \tag{15.12}
	\bm{e_r} = \bm{n_s},
	\quad
	\bm{e_{\theta}} = \bm{e_y} \times \bm{n_s},
	\quad
	\bm{e_{\phi}} = \bm{e_y}.
$$

As Eqn. 13.14 shows, the scattered field vectors (and phasors) are orthogonal to \\(\bm{n_s}\\). Thus, in the spherical coordinate system, only the tangential (\\(\theta\\) and \\(\phi\\)) components of \\(\bm{E_s}\\) and \\(\bm{B_s}\\) are non-zero.

Eqn. 15.12 represents a [rotation](https://en.wikipedia.org/wiki/Rotation_matrix#Basic_rotations) of the \\(\theta r\\)-plane about the \\(\phi\\)-axis:

$$ \tag{15.13}
\begin{bmatrix}
	E\_{\theta}(\theta, \phi) \cr
	E\_{\phi}(\theta, \phi) \cr
	E\_{r}(\theta, \phi) \cr
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

The combination of Eqn. 15.11 and 15.13 is the inverse of the standard transformation matrix from [spherical to Cartesian](https://en.wikipedia.org/wiki/List_of_common_coordinate_transformations#From_spherical_coordinates) coordinates.

Suppose that the amplitude and the phase of the incident field is known at the plane with a certain \\(z\\)-coordinate (where \\(z\\) is a negative number). Then the value of the field at the origin is

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

Since we can represent both the incident and the scattering fields using only two components (each), Eqn. 15.6.1 can be expressed in terms of the complex *scattering matrix*, or the [S-matrix](https://en.wikipedia.org/wiki/S-matrix) for short.

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
	E\_{s,\theta}(\bm{r}, \omega) \cr
	E\_{s,\phi}(\bm{r}, \omega) \cr
\end{bmatrix} \simeq
\frac{e^{i k(\omega) r - i k(\omega) z}}{k(\omega) r} S(\theta, \phi, \omega)
\begin{bmatrix}
	E\_{i,x}(z, 0, \phi, \omega) \cr
	E\_{i,y}(z, 0, \phi, \omega) \cr
\end{bmatrix}.
$$

Eqn. 15.16 matches the one given in the book by van de Hulst, except for the sign of the complex exponential and the missing \\(i\\) factor in the denominator \[[4](#references) (ch. 4.41)\]. The author claims that this factor makes the formulae more convenient \[[4](#references) (ch. 4.1)\]. While that is indeed the case, the convention is prone to sign errors, and also introduces seemingly imaginary coefficients that are, in fact, real.

In general, the elements of the scattering matrix depend on the azimuthal angle \\(\phi\\), since, as we rotate the plane of observation (\\(x z\\) or \\(r \theta\\)) around the \\(z\\)-axis, the scattering object appears to rotate in the opposite direction. However, if the object is symmetric (both geometrically and structurally) with respect to the \\(z\\)-axis, which is the case for spherical and an axis-aligned [spheroidal](https://en.wikipedia.org/wiki/Spheroid) particles, the matrix only depends on the zenith angle \\(\theta\\)[^26]. This causes the scattering matrix to become diagonal, with \\(s_3 = s_4 = 0\\) \[[4](#references) (ch. 4.42)\].

[^26]: That is also the case for groups of statistically isotropic and mirror-symmetric random particles \[[4](#references) (ch. 5.2), [8](#references) (ch. 15)\].

It's easy to see why that is the case. Consider changing the handedness of the Cartesian coordinate frame, so that the basis vector of the \\(x\\)-axis (or the \\(y\\)-axis) becomes \\(-\bm{e_x}\\) (or \\(-\bm{e_y}\\)), and vice versa. In the reflected coordinate system, both \\(E\_{i,x}\\) and \\(E\_{s,\theta}\\) (or \\(E\_{i,y}\\) and \\(E\_{s,\phi}\\)) change the sign, but, since the particle possesses reflection symmetry, the scattering matrix remains unchanged. In order for the magnitudes of the scattered field components to remain unchanged, the off-diagonal elements \\(s_3\\) and \\(s_4\\) must be equal to \\(0\\). This decouples the \\(x\\) and \\(y\\) components of the field, which allows us to represent the vector scattering process in terms of two independent scalar waves.

In addition, a very similar proof shows that axial symmetry with respect to the direction of incidence leads to another important property of forward scattering: \\(s_1(0, \phi) = s_2(0, \phi) = s_0\\).

[EG: this piece should come before Sec. 15]

Finally, we would like to highlight the *scale invariance* property of electromagnetic scattering, also known as the principle of *electrodynamic similitude* \[[8](#references) (ch. 5.5), [9](#references) (ch. 3.5), [17](#references) (ch. 9.3)\]. The gist of it is that, while the (dimensionless) scattering and absorption characteristics of a scattering object depend on its linear dimension \\(a\\) and the wavenumbers \\(k_2 \text{ and } k_1\\) in the interior and the exterior regions, respectively, its properties can be alternatively described using the *relative wavenumber* \\(m = k_2 / k_1\\) and the so-called *size parameter* \\(x = k_1 a = 2 \pi a / \lambda\\), both of which are dimensionless. Thus, provided that the value of \\(m\\) stays fixed, increasing both the linear dimension of the object and the wavelength by the same factor \\(f\\) leaves the formula unchanged: \\(x' = 2 \pi (a f) / (f \lambda) = x \\). This [reduces the dimensionality](https://en.wikipedia.org/wiki/Dimensionality_reduction) of the problem, since three parameters can be replaced with just two.

Note that, unlike \\(x\\), \\(m\\) is not scale-invariant. Recall its definition given by Eqn. 11.8:

$$ \tag{15.17}
	m(\bm{r}, \omega)
	= \frac{k_2(\bm{r}, \omega)}{k_1(\omega)}
	= \sqrt{ \frac{\varepsilon_2(\bm{r}, \omega) \mu_2(\omega)}{\varepsilon_1(\omega) \mu_1(\omega)} }.
$$

Suppose that the scattering object is located in vacuum. Then the denominator \\(\varepsilon_1 \mu_1=\epsilon_0 \mu_0\\) does not depend on the frequency of the incident radiation. However, the numerator does, since no material has a flat response across the entire frequency range. In particular, if the scattering object is conductive \\((\sigma_2 \neq 0)\\), then, according to Eqn. 5.6, its complex permittivity

$$ \tag{15.??}
	\varepsilon_2(\bm{r}, \omega)
	= \epsilon_2(\bm{r}, \omega) + i \frac{\sigma_2(\bm{r}, \omega)}{\omega}
$$

directly depends on the frequency \\(\omega\\). Thus, in order for the scale invariance property to hold, if the linear dimension of the object and the wavelength are both increased by the same factor, conductivity must be correspondingly reduced.

[Insert picture here]

In order to prove the principle of electrodynamic similitude, we must show that the expressions used to calculate the electromagnetic fields can be written in terms of the dimensionless quantity (which we indicate by the hat symbol)

$$ \tag{15.18}
	\bm{\hat{r}} = k \bm{r}.
$$

As a simple example, consider the scalar Green function \\(g\\) introduced in Eqn. 9.10. If we divide it by \\(k\\), we obtain its dimensionless counterpart \\(\hat{G_e}\\):

$$ \tag{15.19}
	\frac{1}{k} g(\bm{r}, k)
	= \hat{G_e}(\bm{\hat{r}})
	= \frac{e^{i |\bm{\hat{r}}|}}{4 \pi |\bm{\hat{r}}|}.
$$

The expression of the total field is the sum of the incident and the scattered fields. Eqn. 15.1 of the incident field can be transformed into the dimensionless form by a trivial substitution of Eqn. 15.18. On the other hand, Eqn. 15.3 of the scattered field is more complicated, as it contains the electric and transition dyadics. According to Eqn. 14.22, the transition dyadic is itself defined in terms of the electric dyadic. Thus, we only need to transform the expression of the former. For convenience, we elect to use the expanded definition given by Eqn. 14.23, which we restate below:

$$ \tag{15.20}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg( \delta(\bm{r} - \bm{r'}) \mathcal{I} + \int_V \mathcal{G}\_e (\bm{r}, \bm{r''}, k) \cdot \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg)
	\cr
	&= k^2 \big( m^2(\bm{r}) - 1 \big) \bigg(
	\delta(\bm{r} - \bm{r'}) \mathcal{I}
	\cr
	&+ \Big( \mathcal{I} + \frac{1}{k^2} \nabla \otimes \nabla \Big) \cdot
	\int\_{V} g( \bm{r} - \bm{r''}, k) \mathcal{T} (\bm{r''}, \bm{r'}, k) dV'' \bigg).
\end{aligned}
$$

In order to replace \\(\bm{r}\\) with \\(\bm{\hat{r}}\\) inside the integral, we must perform a change of variables

$$ \tag{15.21}
	dV = dx \thinspace dy \thinspace dz
	\quad \to \quad
	d \hat{V} = d(kx) \thinspace d(ky) \thinspace d(kz) = k^3 dV.
$$

Furthermore, the [scaling property](https://en.wikipedia.org/wiki/Dirac_delta_function#Scaling_and_symmetry) of the delta function tells us that

$$ \tag{15.22}
	\delta(\bm{r})
	= \delta \bigg(\frac{\bm{\hat{r}}}{k} \bigg)
	= k^3 \delta(\bm{\hat{r}})
$$

To handle the electric dyadic, we introduce

$$ \tag{15.23}
	\hat{\nabla} = k^{-1} \nabla,
$$

such that

$$ \tag{15.25}
	\hat{\nabla} \bm{\hat{r}} = \nabla \bm{r},
$$

which, coupled with Eqn. 15.19, directly leads to

$$ \tag{15.26}
	\mathcal{\hat{G_e}}(\bm{\hat{r}}, \bm{\hat{r}'})
	= \frac{1}{k} \mathcal{G}\_e(\bm{r}, \bm{r'}, k).
$$

The only expression left to transform is the relative wavenumber \\(m\\) given by Eqn. 11.8. It is already a dimensionless quantity; thus, if we rescale the coordinate frame by a factor of \\(1/k\\), we can define the function

$$ \tag{15.27}
	\hat{m}(\bm{\hat{r}}) = m(\bm{r})
$$

that operates in dimensionless coordinates.

Using the new definitions, Eqn. 15.20 becomes

$$ \tag{15.28}
\begin{aligned}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
	&= \big( \hat{m}^2(\bm{\hat{r}}) - 1 \big) \bigg(
	k^5 \delta(\bm{\hat{r}} - \bm{\hat{r}'}) \mathcal{I}
	+ \int\_{V} \mathcal{\hat{G_e}}(\bm{\hat{r}}, \bm{\hat{r}''}) \cdot \mathcal{T} (\bm{r''}, \bm{r'}, k) d \hat{V}'' \bigg)
	\cr
	&= \big( \hat{m}^2(\bm{\hat{r}}) - 1 \big) \bigg(
	k^5 \delta(\bm{\hat{r}} - \bm{\hat{r}'}) \mathcal{I}
	\cr
	&+ \Big( \mathcal{I} + \hat{\nabla} \otimes \hat{\nabla} \Big) \cdot
	\int\_{V} \hat{G_e}( \bm{\hat{r}} - \bm{\hat{r}''}) \mathcal{T} (\bm{r''}, \bm{r'}, k) d \hat{V}'' \bigg),
\end{aligned}
$$

from which it immediately follows that defining

$$ \tag{15.29}
	\mathcal{\hat{T_e}} (\bm{\hat{r}}, \bm{\hat{r}'}) = \frac{1}{k^5}
	\mathcal{T} (\bm{r}, \bm{r'}, k)
$$

makes Eqn. 15.28 fully dimensionless. Additionally, in the operator notation, Eqn. 15.20 and 15.28 are formally identical. As a result, both formulations can be expressed in terms of the Born series given by Eqn. 14.20, and thus produce the same fields.

### Optical Theorem

For radiative transfer applications, we may introduce a simpler description of the scattering and absorption properties of a particle by describing the outcome of the scattering process in radometric (rather than optical) terms \[[6](#references) (ch. 13.6), [8](#references) (ch. 13.1, 13.4)\].

Consider the incident time-harmonic[^15] electromagnetic field \\(\bm{E_i}\\) and \\(\bm{B_i}\\). In a linear, isotropic, homogeneous, source-free medium, the field can be decomposed into a number of plane waves (cf. Eqn. 7.1-7.2) of the form

[^15]: We used the monochromatic forms of the fields given by Eqn. 4.11. Extension to a polychromatic field is trivial, and amounts to summing up the individual harmonic terms per Eqn. 4.8.

$$ \tag{16.1}
\begin{aligned}
	& \bm{E_i}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{E_0} e^{i k (\bm{r} \cdot \bm{n_i}) - i \omega t} \big\rbrace, \cr
	& \bm{B_i}(\bm{r}, t)
	= \mathcal{Re} \big\lbrace \bm{B_0} e^{i k (\bm{r} \cdot \bm{n_i}) - i \omega t} \big\rbrace,
\end{aligned}
$$

where \\(\bm{E_0}\\) and \\(\bm{B_0}\\) are the polarization phasors at the origin, and \\(\bm{n_i}\\) is the direction of incidence.

According to Eqn. 7.18, 7.23 and 7.25, if the medium is non-absorptive, they satisfy the relations

$$ \tag{16.2}
	\bm{n_i} \cdot  \bm{E_0} = 0, \quad
	\bm{n_i} \cdot  \bm{B_0} = 0, \quad
	\bm{n_i} \times \bm{E_0} = \frac{c}{\eta} \bm{B_0}, \quad
	\bm{B_0} \times \bm{n_i} = \frac{\eta}{c} \bm{E_0}.
$$

With each plane wave, we may associate the Poynting vector \\(\bm{S_i}\\) (cf. Eqn. 7.28) pointing along its direction of propagation. If the host medium is non-absorptive and non-magnetic, the expression of the time-averaged Poynting vector (cf. Eqn. 7.31-7.32) is particularly simple:

$$ \tag{16.3}
	\braket{\bm{S_i}}
	= \frac{\mu\_0^{-1}}{2} \mathcal{Re}
	\big\lbrace \bm{E_0} \times \bm{B_0^{\*}} \big\rbrace
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} |\bm{E_0}|^2 \bm{n_i}.
$$

As a result, the amount of power per unit area (cf. Eqn. 7.32) flowing through an arbitrary virtual surface element is

$$ \tag{16.4}
	\Epsilon_i(\bm{n})
	= \braket{\bm{S_i}} \cdot \bm{n}
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} |\bm{E_0}|^2 (\bm{n_i} \cdot \bm{n}),
$$

where \\(\bm{n}\\) is the surface normal.

The total amount of power flowing through the entire virtual surface is then

$$ \tag{16.5}
	\Delta \Phi
	= \int_{A} \braket{\bm{S_i}} \cdot \bm{n} \thinspace dA
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} |\bm{E_0}|^2 \int_{A} \bm{n_i} \cdot \bm{n} \thinspace dA.
$$

The integral given above simply computes the signed projected area of the surface onto the plane of the incident wave. For a closed surface, the value of the integral is zero.

[Insert picture here]

Eqn. 16.5 expresses the energy conservation law:

$$ \tag{16.6}
	\Delta \Phi
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} |\bm{E_0}|^2 \bigg(
	  \oint_{A} \mathrm{max}(0, -\bm{n} \cdot \bm{n_i}) dA
	- \oint_{A} \mathrm{max}(0,  \bm{n} \cdot \bm{n_i}) dA \bigg) = 0.
$$

It shows that the rate at which energy enters the volume (bounded by the virtual surface) is perfectly balanced by the rate of energy leaving the volume.

Clearly, under more general conditions, the equality

$$ \tag{16.7}
	\Delta \Phi = 0
$$

does not hold. If the host medium is absorptive,

$$ \tag{16.8}
	\Delta \Phi < 0.
$$

Otherwise, if sources are present,

$$ \tag{16.9}
	\Delta \Phi > 0.
$$

Our goal is to find a similar expression for the case when the volume contains particles.

We begin by constructing a virtual surface that is (for convenience, and without loss of generality) both centered at the particle and is sufficiently large to be located in its radiation zone. The scattered fields on the surface are then given by Eqn. 15.6-15.?:

$$ \tag{16.10}
\begin{aligned}
	& \bm{E_s}(\bm{r}, t)
	\simeq \mathcal{Re} \bigg\lbrace \frac{e^{i k r}}{k r} \mathcal{S_{ef}}(\bm{n_s}) \cdot \bm{E_0} e^{-i \omega t} \bigg\rbrace
	= \mathcal{Re} \bigg\lbrace \frac{1}{k r} \bm{E_1}(\bm{n_s}) e^{i k r - i \omega t} \bigg\rbrace, \cr
	& \bm{B_s}(\bm{r}, t)
	\simeq \mathcal{Re} \bigg\lbrace \frac{e^{i k r}}{\omega r} \bm{n_s} \times \mathcal{S_{ef}}(\bm{n_s}) \cdot \bm{E_0} e^{-i \omega t} \bigg\rbrace
	= \mathcal{Re} \bigg\lbrace \frac{1}{k r} \bm{B_1}(\bm{n_s}) e^{i k r - i \omega t} \bigg\rbrace,
\end{aligned}
$$

where \\(\bm{E_1}\\) and \\(\bm{B_1}\\) are the polarization phasors of the spherical wave, and \\(\bm{n_s} = \bm{r}/r\\) is the direction of observation.

According to Eqn. 13.14-13.17 and 13.20-13.??, if the host medium is non-absorptive, the following relations exist:

$$ \tag{16.11}
	\bm{n_s} \cdot  \bm{E_1} = 0, \quad
	\bm{n_s} \cdot  \bm{B_1} = 0, \quad
	\bm{n_s} \times \bm{E_1} = \frac{c}{\eta} \bm{B_1}, \quad
	\bm{B_1} \times \bm{n_s} = \frac{\eta}{c} \bm{E_1}.
$$

The Poynting vector of the scattered far field points radially outward. If the host medium is is non-absorptive and non-magnetic, the corresponding time-averaged Poynting vector (see Eqn. 13.23) is

$$ \tag{16.12}
	\braket{\bm{S_s}}
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{\big| \bm{E_1}(\bm{n_s}) \big|^2}{k^2 r^2} \bm{n_s}.
$$

In order to calculate the amount of power scattered by the particle, it is convenient to use a spherical surface \\(\mathbb{S}^2\\). Since the expression of the differential solid angle is

$$ \tag{16.13}
	d\Omega = \frac{\bm{n} \cdot \bm{n_s}}{r^2} dA
$$

setting \\(\bm{n} = \bm{r}/r = \bm{n_s}\\) results in an integral that is formally independent of the radius of the sphere:

$$ \tag{16.14}
	\Phi_s
	= \oint_{A} \braket{\bm{S_s}} \cdot \bm{n_s} \thinspace dA
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{1}{k^2} \oint\_{\mathbb{S}^2} \big| \bm{E_1}(\bm{n_s}) \big|^2 d\Omega.
$$

It is easy to see why that is the case: even though Eqn. 16.14 is evaluated in the radiation zone, the scattered energy has no other choice but to pass through the surrounding virtual surface, no matter the size.

From the radiometric point of view, the squared magnitude of the scattered field corresponds to [intensity](https://en.wikipedia.org/wiki/Radiant_intensity)

$$ \tag{16.15}
	\Iota_s(\bm{n_s}) = r^2 \braket{\bm{S_s}} \cdot \bm{n_s}
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{1}{k^2} \big| \bm{E_1}(\bm{n_s}) \big|^2.
$$

The scattered field cannot exist in isolation. Since it is generated by the incident field, the total field is formed by the superposition of the two waves (cf. Eqn. 11.12):

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

The Poynting vector of the combined wave is then given by the cross product (cf. Eqn. 2.17)

$$ \tag{16.17}
\begin{aligned}
	\bm{S}(\bm{r}, t)
	&= \mu\_0^{-1} \big( \bm{E}(\bm{r}, t) \times \bm{B}(\bm{r}, t) \big) \cr
	&= \mu\_0^{-1} \big( \bm{E_i} \times \bm{B_i} + \bm{E_s} \times \bm{B_s} + \bm{E_i} \times \bm{B_s} + \bm{E_s} \times \bm{B_i} \big) \cr
	&= \bm{S_i} + \bm{S_s} - \bm{S_e}.
\end{aligned}
$$

The \\(\bm{S_e}\\) term shows that *the incident and the scattered waves interfere*. In order to determine the extent it influences the flow of energy, we must once again integrate the time-averaged Poynting vector over a closed surface:

$$ \tag{16.18}
	\Delta \Phi
	= \oint_{A} \braket{\bm{S}} \cdot \bm{n} \thinspace dA
	= \oint_{A} \big( \negthinspace \braket{\bm{S_i}} + \braket{\bm{S_s}} - \braket{\bm{S_e}} \negthinspace \big) \cdot \bm{n} \thinspace dA.
$$

Since the formula is the same, the conclusions of Eqn. 16.7-16.9 remain valid. In addition, Eqn. 16.5-16.6 show that \\(\oint \braket{\bm{S_i}} \cdot \bm{n} \thinspace dA = 0\\). Thus, in the absence of primary sources within the region, Eqn. 16.8 and 16.18 tell us that

$$ \tag{16.19}
	-\Delta \Phi
	= \Phi_a
	= \oint_{A} \big( \negthinspace \braket{\bm{S_e}} - \braket{\bm{S_s}} \negthinspace \big) \cdot \bm{n} \thinspace dA
	= \Phi_e - \Phi_s
$$

is the amount of power absorbed by the particle, where

$$ \tag{16.20}
	\braket{\bm{S_e}}
	= -\mu\_0^{-1} \braket{\bm{E_i} \times \bm{B_s} + \bm{E_s} \times \bm{B_i}}.
$$

In the context of Eqn. 16.19,

$$ \tag{16.21}
	\Phi_e
	= \Phi_a + \Phi_s
$$

represents the amount of power dissipated (absorbed or scattered) by the particle. We must caution against taking this interpretation literally: Eqn. 16.17 is just a convenient *mathematical decomposition*, and, in reality, the incident and the scattered fields cannot be completely *physically separated*. In fact, we can only measure the incident power \\(\Phi_i\\) (if the particle is absent) and the total amount of power \\(\Phi = \Phi_i + \Phi_s - \Phi_e\\) (with the particle in place), while \\(\Phi_s\\) and \\(\Phi_e\\) are mathematical quantities that *are not directly measurable*, derived from the fields that *cannot individually exist*.

Now, recall (cf. Eqn. 4.8, 4.15) that we have found, under quite general conditions, that the time average of the cross product of two vectors oscillating at exactly the same frequency is

$$ \tag{16.22}
	\Big\langle
		\mathcal{Re} \big\lbrace \bm{V} e^{i \omega t} \big\rbrace
		\times
		\mathcal{Re} \big\lbrace \bm{W} e^{i \omega t} \big\rbrace
	\Big\rangle
	= \frac{1}{2} \mathcal{Re} \big\lbrace \bm{V} \times \bm{W^{\*}} \big\rbrace.
$$

Thus, the time average of the mixed Poynting vector can be written as

$$ \tag{16.23}
	\braket{\bm{S_e}} \simeq
	-\frac{\mu_0^{-1}}{2 k r} \mathcal{Re} \bigg\lbrace
	\Big( \bm{E_0} e^{i k (\bm{r} \cdot \bm{n_i})} \Big) \times \Big[ \bm{B_1} e^{i k r} \Big]^{\*} +
	\Big( \bm{E_1} e^{i k r} \Big) \times \Big[ \bm{B_0} e^{i k (\bm{r} \cdot \bm{n_i})} \Big]^{\*}
	\bigg\rbrace.
$$

The next step is to project \\(\braket{\bm{S_e}}\\) onto the normal of the virtual sphere. The statement of the problem leads to two relations:

$$ \tag{16.24}
	\bm{n} = \bm{n_s},
	\quad
	\bm{r} = r \bm{n_s}.
$$

This task can be carried out most efficiently with the help of the following [triple product](https://en.wikipedia.org/wiki/Triple_product) identities:

$$ \tag{16.25}
\begin{aligned}
&   \bm{a} \cdot (\bm{b} \times \bm{c}) =
	\bm{b} \cdot (\bm{c} \times \bm{a}) =
	\bm{c} \cdot (\bm{a} \times \bm{b}),
	\cr
&   \bm{a} \times (\bm{b} \times \bm{c}) =
	\bm{b} (\bm{a} \cdot \bm{c}) -
	\bm{c} (\bm{a} \cdot \bm{b}),
\end{aligned}
$$

which are valid for both real and complex vectors.

By combining Eqn. 16.2, 16.11, 16.24 and 16.25, all magnetic phasor factors can be eliminated:

$$ \tag{16.26}
\begin{aligned}
	\bm{n_s} \cdot \braket{\bm{E_i} \times \bm{B_s}}
	&\simeq \frac{1}{2 k r} \mathcal{Re} \Big\lbrace
	\bm{n_s} \cdot \big( \bm{E_0} \times \bm{B_1^{\*}} \big) e^{-i k r} e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 k r} \mathcal{Re} \Big\lbrace
	\bm{E_0} \cdot \big( \bm{B_1^{\*}} \times \bm{n_s} \big) e^{-i k r} e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 k r} \frac{\eta}{c} \mathcal{Re} \Big\lbrace
	\big( \bm{E_0} \cdot \bm{E_1^{\*}} \big) e^{-i k r} e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace,
	\cr
	\braket{\bm{E_s} \times \bm{B_i}}
	&\simeq \frac{1}{2 k r} \mathcal{Re} \Big\lbrace
	\big( \bm{E_1} \times \bm{B_0^{\*}} \big) e^{i k r} e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 k r} \frac{\eta}{c} \mathcal{Re} \Big\lbrace
	\big( \bm{E_1} \times \bm{n_i} \times \bm{E_0^{\*}} \big) e^{i k r} e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace
	\cr
	&= \frac{1}{2 k r} \frac{\eta}{c} \mathcal{Re} \Big\lbrace
	\Big(
	\bm{n_i} \big( \bm{E_0^{\*} \cdot \bm{E_1}} \big) -
	\bm{E_0^{\*}} \big( \bm{n_i} \cdot \bm{E_1} \big)
	\Big)
	e^{i k r} e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\Big\rbrace.
\end{aligned}
$$

Based on Eqn. 16.26, it is convenient to define two functions independent of \\(k\\) and \\(r\\):

$$ \tag{16.27}
\begin{aligned}
	f_1(\bm{n})
	&= \bm{E_0} \cdot \bm{E_1^{\*}}(\bm{n}),
	\cr
	f_2(\bm{n})
	&= \big( \bm{n} \cdot \bm{n_i} \big) \big( \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n}) \big)
	 - \big( \bm{n} \cdot \bm{E_0^{\*}} \big) \big( \bm{n_i} \cdot \bm{E_1}(\bm{n}) \big).
\end{aligned}
$$

In particular, due to orthogonality relations given by Eqn. 16.2,

$$ \tag{16.28}
	f_2(\pm \bm{n_i})
	= \pm \bm{E_0^{\*}} \cdot \bm{E_1}(\pm \bm{n_i})
	= \pm \big[ f_1(\pm \bm{n_i}) \big]^{\*}.
$$

Use of notation introduced in Eqn. 16.27 results in a compact expression of the interference term given by Eqn. 16.20:

$$ \tag{16.29}
	\braket{\bm{S_e}} \cdot \bm{n_s}
	\simeq -\frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \mathcal{Re} \left\lbrace
		\frac{e^{-i k r}}{k r} f_1(\bm{n_s}) e^{i k r (\bm{n_s} \cdot \bm{n_i})} +
		\frac{e^{i k r}}{k r} f_2(\bm{n_s}) e^{-i k r (\bm{n_s} \cdot \bm{n_i})}
	\right\rbrace.
$$

Note that we are using the asymptotic expression of a spherical wave (Eqn. 16.10). It is based on the fact that a small surface patch of a very large spherical wavefront appears effectively flat. We can also use this idea in reverse, by reinterpreting a plane wave as a very large spherical wave \\((k r \to \infin)\\) that is either seen as diverging from the inside, or converging when observed from the outside:

$$ \tag{16.30}
\begin{aligned}
	e^{i k r (\bm{n_s} \cdot \bm{n_i})}
	\simeq 2 \pi i \left(
	\frac{e^{-i k r}}{k r} \delta(\bm{n_i} + \bm{n_s}) -
	\frac{e^{i k r}}{k r} \delta(\bm{n_i} - \bm{n_s}) \right)
\end{aligned},
$$

where

$$ \tag{16.31}
	\delta(\bm{n_i} - \bm{n_s})
	= \delta(\cos{\theta_i} - \cos{\theta_s}) \delta(\phi_i - \phi_s)
$$

is the Dirac delta function defined on the surface of a unit sphere \[[8](#references) (ch. 13, ap. D)\].

The expression of the time-average of the mixed Poynting vector can be thus transformed into

$$ \tag{16.32}
\begin{aligned}
	\braket{\bm{S_e}} \cdot \bm{n_s}
	&\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{2 \pi}{k^2 r^2} \mathcal{Im} \left\lbrace
	f_1(\bm{n_s}) \left(
		\delta(\bm{n_i} + \bm{n_s}) e^{-2 i k r} -
		\delta(\bm{n_i} - \bm{n_s})
	\right) \right\rbrace
	\cr
	&- \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{2 \pi}{k^2 r^2} \mathcal{Im} \left\lbrace
	f_2(\bm{n_s}) \left(
		\delta(\bm{n_i} + \bm{n_s}) e^{2 i k r} -
		\delta(\bm{n_i} - \bm{n_s})
	\right) \right\rbrace.
\end{aligned}
$$

Eqn. 16.32 indicates that we may detect observable interference between the incident and the scattered fields if the optical axis of the measurement device is directly facing the source \\((\bm{n_s} = \bm{n_i})\\). Evidently, the opposite orientation \\((\bm{n_s} = -\bm{n_i})\\) will cause the light sensor to partially block the incident light. And even if it were not the case, the integral of the normal component of \\(\braket{\bm{S_e}}\\) over a small [spherical cap](https://mathworld.wolfram.com/SphericalCap.html) \\(C_i^{-}\\) centered at \\(-\bm{n_i}\\)

$$ \tag{16.33}
\begin{aligned}
	\int_{C_i^{-}} \braket{\bm{S_e}} \cdot \bm{n_s} \thinspace dA
	&\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{2 \pi}{k^2} \mathcal{Im} \left\lbrace
	f_1(-\bm{n_i}) e^{-2 i k r} - f_2(-\bm{n_i}) e^{2 i k r} \right\rbrace
	\cr
	&= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{2 \pi}{k^2} \mathcal{Im} \left\lbrace
		\bm{E_0} \cdot \bm{E_1^{\*}}(-\bm{n_i}) e^{-2 i k r} +
		\bm{E_0^{\*}} \cdot \bm{E_1}(-\bm{n_i}) e^{2 i k r}
	\right\rbrace = 0,
\end{aligned}
$$

since the expression inside the curly braces is a real number (cf. Eqn. 3.5).

Thus, assuming the observer is located in the radiation zone, *the incident and the scattered light only interfere in the direction of incidence*. The associated amount of power (dissipated by the particle) is

$$ \tag{16.34}
	\Phi_e
	= \oint_{\mathbb{S}^2} \braket{\bm{S_e}} \cdot \bm{n_s} \thinspace dA
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{4 \pi}{k^2} \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace.
$$

Suppose that a measurement device (a light sensor) with the entrance pupil of area \\(C_d\\) is placed in the direction of incidence at a great distance from the source. Then, according to Eqn. 16.14, 16.17, 16.21, and 16.34, if there is a particle near the center of the line segment connecting the source to the sensor, the resulting measurement

$$ \tag{16.3x}
\begin{aligned}
	\Phi_f
	&= C_d \braket{\bm{S_i}} \cdot \bm{n_i}
	 + C_d \braket{\bm{S_s}} \cdot \bm{n_i} - \Phi_e
	\cr
	&= C_d \braket{\bm{S_i}} \cdot \bm{n_i}
	 + C_d \braket{\bm{S_s}} \cdot \bm{n_i}
	 - \oint_{A} \braket{\bm{S_s}} \cdot \bm{n_s} \thinspace dA - \Phi_a
\end{aligned}
$$

will be smaller in comparison with the amount of power \\(C_d \braket{\bm{S_i}} \cdot \bm{n_i}\\) arriving at the sensor in the absence of the particle, since the scattered power \\(C_d \braket{\bm{S_s}} \cdot \bm{n_i}\\) corresponding to the direction of incidence is smaller than the total amount of power \\(\Phi_s = \oint_{A} \braket{\bm{S_s}} \cdot \bm{n_s} \thinspace dA\\) scattered in all directions. Thus, we can draw a conclusion that transmission of a parallel beam of light through a particle reduces its intensity.

Eqn. 16.3x separates radiation transmitted in the direction of incidence into three components: the incident, the scattered, and the dissipated. Scattering tends to be separated from propagation of light, leading to the idea that particles "remove" energy from the incident beam. While this approach is conceptually plausible, the interpretation is not correct, since 1) the incident wave is not affected by the presence of the scattering object, and, as we shall see, 2) the calculated amount of scattered power is typically overestimated. This can lead to apparent paradoxes and violations of the law of conservation of energy. These issues can be avoided by focusing solely on the total electromagnetic field, which, as Eqn. 16.3x shows, is crucial if the measurement device is facing the source.

The amount of power absorbed by the particle can be determined using Eqn. 16.21:

$$ \tag{16.35}
	\Phi_a
	= \Phi_e - \Phi_s
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{1}{k^2}
	\left(
		4 \pi \thinspace \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace -
		\oint\_{\mathbb{S}^2} \big| \bm{E_1}(\bm{n_s}) \big|^2 d\Omega
	\right).
$$

Alternatively, [Ohm's law](https://en.wikipedia.org/wiki/Ohm%27s_law) may be used instead \[[25](#references) (ch. 1.1)\]:

$$ \tag{16.3z}
	\Phi_a
	= \frac{1}{2} \omega \int\_{V}
	\mathcal{Im} \big\lbrace \varepsilon_2(\bm{r'}) \big\rbrace \big| \bm{E}(\bm{r'}) \big|^2 dV'
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} k \int\_{V}
	\mathcal{Im} \left\lbrace \frac{m^2(\bm{r'})}{\mu\_r(\bm{r'})} \right\rbrace \big| \bm{E}(\bm{r'}) \big|^2 dV',
$$

with the integral taken over the interior of the particle \[[25](#references) (ch. 1.1)\]. As before, \\(\varepsilon_2\\) is the complex permittivity of the particle, \\(\mu\_r\\) is its relative permeability, and \\(m\\) is the relative wavenumber given by Eqn. 11.8.

It is convenient to be able to relate the amount of power absorbed by the particle to the amount of incident power the particle *geometrically* intercepts. The latter can be characterized with the help of the projected area of the particle - its *geometric cross-section* \\(C_g\\). For a convex particle, it is given by the integral

$$ \tag{16.36}
	C_g
	= \oint_{A} \mathrm{max}(0, -\bm{n} \cdot \bm{n_i}) dA.
$$

The amount of incident power is then (cf. Eqn. 16.3-16.5)

$$ \tag{16.37}
	\Phi_i
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} |\bm{E_0}|^2
	  \oint_{A} \mathrm{max}(0, -\bm{n} \cdot \bm{n_i}) dA
	= | \negthinspace \braket{\bm{S_i}} \negthinspace | C_g
	= \Epsilon_i(\bm{n_i}) C_g.
$$

This allows us to introduce dimensionless absorption, scattering, and extinction *efficiency factors*

$$ \tag{16.38}
\begin{aligned}
	& Q_a
	= \frac{\Phi_a}{\Phi_i}
	= \frac{\Phi_e - \Phi_s}{\Phi_i},
	\cr
	& Q_s
	= \frac{\Phi_s}{\Phi_i}
	= \frac{\oint |\bm{E_1}(\bm{n_s})|^2 d\Omega}{k^2 C_g |\bm{E_0}|^2},
	\cr
	& Q_e
	= \frac{\Phi_e}{\Phi_i}
	= \frac{4 \pi \thinspace \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace}{k^2 C_g |\bm{E_0}|^2}.
\end{aligned}
$$

If normalization with respect to the projected area is not required, we may utilize the absorption, scattering, and extinction *cross-sections* instead:

$$ \tag{16.39}
\begin{aligned}
	& C_a
	= \frac{\Phi_a}{\Epsilon_i}
	= Q_a C_g
	= \frac{\Phi_e - \Phi_s}{| \negthinspace \braket{\bm{S_i}} \negthinspace |},
	\cr
	& C_s
	= \frac{\Phi_s}{\Epsilon_i}
	= Q_s C_g
	= \frac{\oint |\bm{E_1}(\bm{n_s})|^2 d\Omega}{k^2 |\bm{E_0}|^2},
	\cr
	& C_e
	= \frac{\Phi_e}{\Epsilon_i}
	= Q_e C_g
	= \frac{4 \pi \thinspace \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \bm{E_1}(\bm{n_i}) \big\rbrace}{k^2 |\bm{E_0}|^2}.
\end{aligned}
$$

Eqn. 16.39.1-16.39.3 are known as the [optical theorem](https://en.wikipedia.org/wiki/Optical_theorem). They are often used to describe electromagnetic energy transfer in radiometric terms. For instance, Eqn. 16.3x that expresses the amount of energy transmitted in the direction of incidence can be succinctly written as

$$ \tag{16.3y}
\begin{aligned}
	\Phi_f
	&= (C_d - C_e) \Epsilon_i(\bm{n_i})
	+ C_d \frac{\Iota_s(\bm{n_i})}{r^2},
\end{aligned}
$$

where, as before, \\(C_d\\) is the area of the entrance pupil of the measurement device, \\(C_e\\) is the extinction cross-section, \\(\Epsilon_i\\) is the incident irradiance, and \\(\Iota_s\\) is the scattered intensity. Since, physically, the amount of power cannot be negative, Eqn. 16.3y implies that \\(C_d\\) must be sufficiently large \\((C_d \gg C_g)\\) in order to fully capture the extinction effect \[[8](#references) (ch. 13.1)\].

Eqn. 16.38.3 and 16.39.3 can be simplified if the particle possesses axial symmetry with respect to the direction of incidence. To show this, we can express \\(\bm{E_1}\\) in terms of the scattering dyadic \\(\mathcal{S_{ef}}\\) according to Eqn. 15.?:

$$ \tag{16.40}
	\Phi_e
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{4 \pi}{k^2} \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \mathcal{S_{ef}}(\bm{n_i}) \cdot \bm{E_0} \big\rbrace.
$$

Once we choose a coordinate system, we can substitute the scattering matrix with \\(\theta=0\\):

$$ \tag{16.41}
	\Phi_e
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{4 \pi}{k^2} \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot S(0, \phi) \bm{E_0} \big\rbrace.
$$

Now, for a symmetric particle, we have previously demonstrated that

$$ \tag{16.42}
	s_1(0, \phi) = s_2(0, \phi) = s_0,
	\quad
	s_3 = s_4 = 0.
$$

Thus, we obtain the expressions

$$ \tag{16.43}
\begin{aligned}
	& Q_e
	= \frac{4 \pi \thinspace \mathcal{Im} \big\lbrace s_0 \bm{E_0^{\*}} \cdot \bm{E_0} \big\rbrace}{k^2 C_g |\bm{E_0}|^2}
	= \frac{4 \pi \thinspace \mathcal{Im} \lbrace s_0 \rbrace}{k^2 C_g},
	\cr
	& C_e
	= Q_e C_g
	= 4 \pi k^{-2} \mathcal{Im} \lbrace s_0 \rbrace,
\end{aligned}
$$

that match the results found using the scalar wave theory \[[4](#references) (ch. 4.42)\]. Intuitively, this makes sense, since the particle looks the same regardless of the choice of the plane of reference (assuming the latter contains the direction of incidence), and the sensor (that only measures power) is not sensitive to the orientation of incoming light.

### Dynamic Light Scattering by a Particle Group

Consider a group of \\(N\\) arbitrary particles of embedded in a source-free region of a linear, isotropic, homogeneous medium characterized by the wavenumber \\(k\\). Let \\(V\\) denote both the region of space that contains the particles and its volume. For convenience, assume that the center of \\(V\\) coincides with the origin of the coordinate system.

The field acting on each particle is the sum of the incident field and the scattered fields produced by its neighbors \[[8](#references) (ch. 6.1)\]. The strength of this field depends on the number of particles, the distances between them, as well as the orientation, shape, size, and composition of each particle. In most cases, estimation of the scattered field in the region containing the particles is a challenging task more suited for a numerical computation. However, it is possible to make the problem amenable to an analytical treatment at the cost of generality, by considering a group composed of a small number of weakly absorbing and weakly scattering, widely spaced particles. These properties make the *single-scattering approximation* applicable, which allows us to neglect the influence of the scattered field on the particles in the group \[[8](#references) (ch. 14.1)\].

The quantitative criteria for applicability of the single-scattering approximation are not well-established. Intuitively, the approximation fails when the electromagnetic field acting on the particles is significantly different from the incident field. Since this is caused by other particles scattering or absorbing incident photons, this extinction process is directly linked to the extinction cross-section of a single particle. The latter is typically on the order of the geometrical cross-section of a single particle (or, for the tiniest of particles, significantly smaller). As we shall see, the total extinction cross-section of a particle group is (under certain conditions) proportional to the sum of the extinction cross-sections \\(C_{e}\\) of individual particles. For large particles, the laws of geometrical optics are applicable; thus, if we separate the incident illumination into parallel rays of light, then single scattering simply means that every ray intercepts a single particle (with each particle assumed to be weakly absorbing and weakly scattering). Obviously, that can only be the case if the total number of particles \\(N\\) is not too great, regardless of their density.

Alternatively, we may project the particles onto a plane facing the light source, and check for overlaps. We can also approximately account for the absorption and scattering characteristics of the particles by replacing their geometric cross-sections with the extinction cross-sections. If the extinction density per unit area is sufficiently low,

$$ \tag{17.0}
	\frac{\sum_{j=1}^{N} C_{e,j}}{L^2} \ll 1,
$$

then neglecting multiple scattering should result in a reasonable approximation. Note that the total number of particles \\(N\\) is proportional to \\(V = L^3\\), so it grows much quicker than the geometrical cross-section \\(L^2\\), which makes Eqn. 17.0 a quite demanding criterion for groups of small particles, compensating for the formal failure of the laws of geometrical optics.

As usual, we shall utilize the superposition principle to decompose the incident field into a number of plane waves. If the host medium is non-absorptive and non-magnetic, then the expressions of the incident field (Eqn. 16.1) and the associated time-averaged Poynting vector (Eqn. 16.3) are identical to those shown in the previous section.

Let \\(\bm{r_j}\\) denote the location of the \\(j\\)-th particle, and \\(a_j\\) -- the radius of its bounding sphere. If the observation point \\(\bm{r}\\) is located in the radiation zone of each particle, then the distances \\(R_j = |\bm{R_j}| = |\bm{r} - \bm{r_j}|\\) satisfy the inequalities given by Eqn. 13.13:

$$ \tag{17.1}
	k R_j - k a_j \gg 1, \quad
	k R_j \gg k a_j, \quad
	k R_j \gg (k a_j)^2.
$$

The resulting scattered far-field of the \\(j\\)-th particle is given by Eqn. 15.6:

$$ \tag{17.2}
\begin{aligned}
	& \bm{E_j}(\bm{r}, \omega)
	\simeq \frac{e^{i k R_j}}{k R_j} \mathcal{S_j} (\bm{R_j} / R_j) \cdot \bm{E_0} e^{i k (\bm{r_j} \cdot \bm{n_i})},
	\cr
	& \bm{B_j}(\bm{r}, \omega)
	\simeq \frac{\eta}{c} (\bm{R_j} / R_j) \times \bm{E_j}(\bm{r}, \omega).
\end{aligned}
$$

where we must remember to account for the fact that, in general, the particle is not located at the origin.

The value of the total field \\(\bm{E}\\) is simply

$$ \tag{17.x}
	\bm{E}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega) + \bm{E_s}(\bm{r}, \omega)
	= \bm{E_i}(\bm{r}, \omega)
	+ \sum_{j=1}^{N} \bm{E_j}(\bm{r}, \omega).
$$

Let \\(L\\) and \\(L\\) denote the linear dimension of \\(V\\) (for simplicity, we shall assume that \\(V\\) is suitably symmetric). Since a typical volume is large compared to the wavelength of light, then it follows that

$$ \tag{17.3}
	k L \ge 1.
$$

On the other hand, if the dimensions of \\(V\\) are sufficiently small compared to the distance between every particle and the observation point, such that

$$ \tag{17.4}
	k R_j \gg k L,
$$

then we may use the series expansions of Eqn. 13.8 and 13.10 to asymptotically approximate

$$ \tag{17.5}
	\frac{\bm{R_j}}{R_j} \simeq \bm{n_s} = \frac{\bm{r}}{r},
	\quad
	\frac{1}{R_j} \simeq \frac{1}{r},
	\quad
	k R_j
	\simeq k r - k (\bm{r_j} \cdot \bm{n_s})
	+ \frac{k r_j^2}{2 r} \left( 1 - (\bm{n_j} \cdot \bm{n_s})^2 \right),
$$

where \\(\bm{n_j} = \bm{r_j} / |\bm{r_j}| = \bm{r_j} / r_j\\).

It is important to note that Eqn. 17.4 does not imply that the observation point is located in the radiation zone with respect to the entire volume \\(V\\), since the latter requires \\(k R_j \gg (k L)^2\\).

Substitution of Eqn 17.5 into 17.2.1 yields an expression of a nearly-spherical wave diverging from the origin of the coordinate system:

$$ \tag{17.6}
	\bm{E_j}(\bm{r}, \omega)
	\simeq \frac{e^{i k (R_j + \bm{r_j} \cdot \bm{n_i})}}{k r} \mathcal{S_j} (\bm{n_s}) \cdot \bm{E_0}
	\approx \frac{e^{i k r}}{k r} \mathcal{S_j} (\bm{n_s}) \cdot \bm{E_0} e^{i k \bm{r_j} \cdot (\bm{n_i} - \bm{n_s}) + i k r_j^2 \left( 1 - (\bm{n_j} \cdot \bm{n_s})^2 \right)/(2 r)}.
$$

Let us combine the expressions of the incident and the scattered fields, and determine whether their interaction produces any observable interference effects. We can utilize the same method employed in Sec. 16; specifically, let us repurpose Eqn. 16.17 and 16.22 of the time-averaged Poynting vector

$$ \tag{17.8}
\begin{aligned}
	\braket{\bm{S}}
	&= \frac{\mu\_0^{-1}}{2} \mathcal{Re} \Big\lbrace
	\Big( \bm{E_i} + \sum_{j=1}^{N} \bm{E_j} \Big) \times
	\Big( \bm{B_i^{\*}} + \sum_{j=1}^{N} \bm{B_j^{\*}} \Big) \Big\rbrace
	\cr
	&= \braket{\bm{S_i}} + \sum_{j=1}^{N} \braket{\bm{S_j}} - \sum_{j=1}^{N} \braket{\bm{S_{ij}}} - \sum_{j=1}^{N-1} \sum_{k=j+1}^{N}  \braket{\bm{S_{jk}}},
\end{aligned}
$$

where \\(\braket{\bm{S_i}}\\) is the time-averaged Poynting vector of the incident wave (given by Eqn. 16.3), \\(\braket{\bm{S_j}}\\) is the time-averaged Poynting vector of the scattered wave of the \\(j\\)-th particle (given by Eqn. 16.12), and

$$ \tag{17.9}
	\braket{\bm{S_{jk}}}
	= -\frac{\mu\_0^{-1}}{2} \mathcal{Re} \big\lbrace \bm{E_j} \times \bm{B_k^{\*}} + \bm{E_k} \times \bm{B_j^{\*}} \big\rbrace.
$$

is the term that models the interaction of the waves \\(j\\) and \\(k\\). In particular, \\(\braket{\bm{S_{ij}}}\\) expresses interference between the incident and the scattered waves that leads to the extinction effect (cf. Eqn. 16.20, 16.23).

Therefore, the only term we have not yet previously encountered, \\(\braket{\bm{S_{jk}}}\\), is the one that models the interaction of the scattered waves of two particles. Eqn. 16.11 shows that both \\(\bm{E_j}\\) and \\(\bm{B_k^{\*}}\\) (as well as \\(\bm{E_k}\\) and \\(\bm{B_j^{\*}}\\)) are transverse; however, we have no reason to believe that these field vectors are mutually orthogonal. In addition, the waves are not in-phase, since the spatial locations of the particles are not the same. As a result, it is not immediately clear whether the last term of Eqn. 17.9 converges to a simple and intuitive analytical expression.

In order to obtain the desired solution, it is necessary to appeal to the [dynamic nature](https://en.wikipedia.org/wiki/Dynamical_system) of the microphysical system under consideration. Typically, the distribution of particles is not static -- it [evolves over time](https://en.wikipedia.org/wiki/Time_evolution), governed by processes such as [turbulence](https://en.wikipedia.org/wiki/Turbulence) or [Brownian motion](https://en.wikipedia.org/wiki/Brownian_motion). Thus, most relationships between the particles are not persistent and, when averaged over time -- not significant.

Furthermore, while a system typically evolves fairly slowly in comparison with the period of oscillation of an electromagnetic wave, it may undergo a significant change during the time it takes to perform a measurement. We can take advantage of this fact by assuming that 1) dynamic light scattering is a [stationary process](https://en.wikipedia.org/wiki/Stationary_process),
such that the *mean value* of the Poynting vector

$$ \tag{17.10}
	\braket{\braket{\bm{S}}} \negmedspace (\bm{r})
	= \lim_{T \to \infin} \frac{1}{T} \int\_{0}^{T} \bm{S}(\bm{r}, t_0 + t) dt,
$$

is invariant with respect to the initial time \\(t_0\\), and that 2) the duration \\(T\\) of the measurement is sufficiently long for the *time-averaged value* of the Poynting vector

$$ \tag{17.11}
	\braket{\bm{S}} \negmedspace (\bm{r}, t)
	= \frac{1}{T} \int\_{-T/2}^{\thinspace T/2} \bm{S}(\bm{r}, t + t') dt'
$$

to be representative of its mean value at any point in time \\(t\\):

$$ \tag{17.12}
	\braket{\bm{S}} \negmedspace (\bm{r}, t)
	\approx \braket{\braket{\bm{S}}} \negmedspace (\bm{r}).
$$

In principle, one could solve a dynamic light scattering problem by choosing the initial configuration of the system (i.e. by assigning each particle a certain position, orientation, shape, size, and composition), describing the way the system evolves, and evaluating the integral given by Eqn 17.10. In practice, this (obvious, but arduous) solution is rarely used; nevertheless, it helps us build the mental model and understand its implications.

First of all, since the properties of the particles vary in time, the phasors of the scattered field must become time-dependent as well. Examination of the resulting expression of the total field

$$ \tag{17.13}
	\bm{E}(\bm{r}, t)
	= \frac{1}{2} \bm{E_0}(\bm{r}, t)
	+ \sum\_{p = 1}^{\infin} \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}, t) e^{-i \omega_p t} \big\rbrace,
$$

reveals that its components are no longer time-harmonic. This complicates the theory, since, unlike Eqn. 4.8, Eqn 17.13 can not be obtained by solving the Maxwell equations in the frequency domain.

Furthermore, substitution of Eqn. 17.13 into 17.10 results in an integral

$$ \tag{17.14}
\begin{aligned}
	\braket{\braket{\bm{S}}} \negmedspace (\bm{r})
	= \lim_{T \to \infin} \frac{1}{T} \int\_{0}^{T}
	\frac{\mu_0^{-1}}{4} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
	\bm{E_p}(\bm{r}, t) \times \big[ \bm{B_q}(\bm{r}, t) \big]^{\*}
	e^{i (\omega_q - \omega_p) t} dt.
\end{aligned}
$$

that, unlike Eqn 4.14, cannot be readily simplified any further. However, if we are only concerned with high-frequency electromagnetic radiation, and if the [temperature](https://en.wikipedia.org/wiki/Thermal_velocity) of the substance is not too high, then we may assume that the fundamental period \\(T_1\\) of the electromagnetic field is much shorter than the amount of time \\(T_e\\) it takes the system to evolve to a significant degree:

$$ \tag{17.15}
	T_e \gg T_1.
$$

This permits us to split the time averaging process in two steps

$$ \tag{17.16}
\begin{aligned}
	\braket{\braket{\bm{S}}} \negmedspace (\bm{r})
	&=\lim_{T \to \infin} \frac{1}{T} \int\_{0}^{T}
	\frac{\mu_0^{-1}}{4} \sum\_{p = -\infin}^{\infin} \sum\_{q = -\infin}^{\infin}
	\bm{E_p}(\bm{r}, t) \times \big[ \bm{B_q}(\bm{r}, t) \big]^{\*}
	\left( \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} e^{i (\omega_q - \omega_p) t'} dt' \right) dt
	\cr
	&= \lim_{T \to \infin} \frac{1}{T} \int\_{0}^{T}
		\frac{\mu_0^{-1}}{2} \sum\_{p = 1}^{\infin}
		\mathcal{Re} \left\lbrace \bm{E_p}(\bm{r}, t) \times \big[ \bm{B_p}(\bm{r}, t) \big]^{\*} \right\rbrace dt
\end{aligned}
$$

in such a way that, as the time-harmonic factor of the incident field is being eliminated, the microphysical system is kept static. This crucial simplification decouples time-dependence of the electromagnetic field from that of the microphysical system.

At this point, we may formally introduce the idea of a [microstate](https://en.wikipedia.org/wiki/Microstate_(statistical_mechanics)). It is a finite collection of parameters (that correspond to the internal degrees of freedom) of a microphysical system that can be used to describe its configuration at a certain point in time. Exposing the microstate \\(\psi\\) as a parameter of Eqn. 17.16 yields:

$$ \tag{17.17}
	\braket{\braket{\bm{S}}} \negmedspace (\bm{r})
	= \lim_{T \to \infin} \frac{1}{T} \int\_{0}^{T}
		\frac{\mu_0^{-1}}{2} \sum\_{p = 1}^{\infin}
		\mathcal{Re} \left\lbrace \bm{E_p} \big( \bm{r}, \psi(t) \big) \times \big[ \bm{B_p} \big( \bm{r}, \psi(t) \big) \big]^{\*} \right\rbrace dt.
$$

The form of Eqn. 17.17 makes it apparent that the mean value of the Poynting vector directly depends on the fraction of time the system spends in each microstate. In general, the latter is sensitive to the initial conditions. In fact, none of the requirements we have imposed so far prohibit the system from remaining in the same microstate indefinitely, which obviously defeats the purpose of time averaging in the first place! Therefore, let us impose a stronger condition, by requiring dynamic light scattering to be an [ergodic process](https://en.wikipedia.org/wiki/Ergodic_process), such that, at any point in time, computing the average in the time domain is equivalent to averaging over the space \\(\Psi\\) containing every possible microstate (ranging across all experiments and initial conditions) \[[8](#references) (ch. 10.4)\]:

$$ \tag{17.18}
	\braket{\braket{\bm{S}}} \negmedspace (\bm{r})
	= \int\_{\Psi} \frac{\mu_0^{-1}}{2} \sum\_{p = 1}^{\infin}
		\mathcal{Re} \left\lbrace \bm{E_p}(\bm{r}, \psi) \times \big[ \bm{B_p}(\bm{r}, \psi) \big]^{\*} \right\rbrace p(\psi) d\psi.
$$

Effectively, it implies that every sizable time sequence is statistically representative of the whole process.

The new formulation replaces the dynamic model of the microphysical system with a [stochastic process](https://en.wikipedia.org/wiki/Stochastic_process) characterized by the set of possible outcomes (i.e. microstates) called an [ensemble](https://en.wikipedia.org/wiki/Ensemble_(mathematical_physics)), and the associated [probability density function](https://en.wikipedia.org/wiki/Probability_density_function) \\(p(\psi)\\) that describes the likelihood of observing the system in each microstate. Furthermore, it restores the periodic form of the expression of the total field:

$$ \tag{17.19}
	\bm{E}(\bm{r}, \psi, t)
	= \sum\_{p = 1}^{\infin} \mathcal{Re} \big\lbrace \bm{E_p}(\bm{r}, \psi) e^{-i \omega_p t} \big\rbrace.
$$

This key aspect allows the previously-established theoretical methods to remain valid.

The integral found in Eqn. 17.18 represents a special kind of weighted average

$$ \tag{17.20}
	\braket{f}\_{\psi}
	= \int_{\Psi} f(\bm{r}, \psi, t) p(\psi) d\psi,
$$

called an [ensemble average](https://en.wikipedia.org/wiki/Ensemble_average). Formally, it is defined as the [expected value](https://en.wikipedia.org/wiki/Expected_value) of a quantity that depends on the microstate of the system. Let us also introduce a convenient short-hand notation

$$ \tag{17.21}
	\braket{f}\_t
	= \frac{1}{T_1} \int\_{-T_1/2}^{\thinspace T_1/2} f(\bm{r}, \psi, t + t') dt'
$$

for the average over the fundamental period of the electromagnetic field.

By combining Eqn. 17.12, 17.16, 17.20 and 17.21, our approximation can be summarized as

$$ \tag{17.22}
	\braket{\bm{S}} \negmedspace (\bm{r}, t)
	\approx \braket{\braket{\bm{S}}\_t}\_{\psi} \negmedspace (\bm{r}).
$$

Let us apply the ergodic assumption to Eqn. 17.8, and carefully consider its individual terms. First of all, by definition, the incident field is not affected by the presence of the scattering object. Therefore, no modifications to \\(\braket{\bm{S\_i}}\\) are necessary. Next, consider the Poynting vector \\(\braket{\bm{S\_j}}\\) associated with the scattered field of the \\(j\\)-th particle. Since, according to Eqn. 16.11, the scattered field vectors \\(\bm{E\_j}\\) and \\(\bm{B\_j}\\) are mutually orthogonal, we may express \\(\bm{S\_j}\\) using Eqn. 13.21:

$$ \tag{17.23}
\begin{aligned}
	\bm{S_j}(\bm{r}, \psi, t)
	&\simeq \mu\_0^{-1} \frac{\eta}{c} {\big| \mathcal{Re} \big\lbrace \bm{E_j}(\bm{r}, \psi) e^{-i \omega t} \big\rbrace \big|}^2 \bm{n_s}
	\cr
	&= \mu\_0^{-1} \frac{\eta}{c} \frac{\big| \mathcal{Re} \big\lbrace
	\mathcal{S_j}(\bm{n_s}, \psi) \cdot \bm{E_0}
	e^{i k (R_j + \bm{r_j} \cdot \bm{n_i}) -i \omega t}
	\big\rbrace \big|^2}{k^2 r^2} \bm{n_s}.
\end{aligned}
$$

Eqn. 13.22-13.23 demonstrate that time averaging Eqn. 17.23 is equivalent to replacing the phase factor with \\(1/2\\), which yields the standard expression of the time-averaged Poynting vector

$$ \tag{17.24}
	\braket{\bm{S_j}}\_t \negmedspace (\bm{r}, \psi)
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{\left|
	\mathcal{S_j}(\bm{n_s}, \psi) \cdot \bm{E_0} \right|^2}{k^2 r^2} \bm{n_s},
$$

as if the particle were located at the origin. Note that the value of \\(\braket{\bm{S_j}}\\) explicitly depends on the state of the incident light, and, implicitly, on the orientation, shape, size, and composition of the particle via the far-field scattering dyadic \\(\mathcal{S_j}\\). Because the latter is located inside the squared norm operator, ensemble averaging the Poynting vector

$$ \tag{17.25}
	\braket{\braket{\bm{S_j}}} \negmedspace (\bm{r})
	\simeq \int\_{\Psi}
	\braket{\bm{S_j}}\_t \negmedspace (\bm{r}, \psi)
	p(\psi) d\psi
$$

is, in general, different from replacing \\(\mathcal{S_j}\\) with the average scattering dyadic \\(\braket{\mathcal{S_j}}\\). This implies that the mean intensity \\(\braket{\Iota_s}\\) of scattered light is not the same as the intensity of light scattered by the average particle (cf. Eqn. 16.15) \[[8](#references) (ch. 14.4)\]:

$$ \tag{17.26}
	\braket{\Iota_s} \negmedspace (\bm{n_s})
	= r^2 \braket{\braket{\bm{S_j}}} \cdot \bm{n_s}
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{1}{k^2} \left\langle
	\left| \mathcal{S_j}(\bm{n_s}, \psi) \cdot \bm{E_0} \right|^2
	\right\rangle \_{\psi}.
$$

The Poynting vector \\(\braket{\bm{S_j}}\\) associated with the scattered field does not, by itself, completely determine the intensity of light. In addition, we must account for interference between the incident and the scattered waves contained within the \\(\braket{\bm{S_{ij}}}\\) terms found in Eqn 17.8.

Substitution of Eqn. 16.1 into 17.9 yields an expression

$$ \tag{17.27}
\begin{aligned}
	\braket{\bm{S_{ij}}}\_t \negmedspace (\bm{r}, \psi)
	\simeq &-\frac{\mu_0^{-1}}{2} \mathcal{Re} \bigg\lbrace
	\Big( \bm{E_0} e^{i k (\bm{r} \cdot \bm{n_i})} \Big) \times \bm{B_j^{\*}}
	+ \bm{E_j} \times \Big[ \bm{B_0} e^{i k (\bm{r} \cdot \bm{n_i})} \Big]^{\*}
	\bigg\rbrace.
\end{aligned}
$$

that is broadly similar to Eqn. 16.23. In particular, according to Eqn. 16.30, the complex exponential term \\(\exp(i k (\bm{r} \cdot \bm{n_i})) = \exp(i k r (\bm{n_s} \cdot \bm{n_i}))\\) indicates that interference only occurs when the optical axis of the measurement device is directly facing the source. While that is indeed the case, we must carefully account for the additional phase factors featured in the expressions of \\(\bm{E_j}\\) and \\(\bm{B_j^{\*}}\\) given by Eqn. 17.2:

$$ \tag{17.28}
\begin{aligned}
	\braket{\bm{S_{ij}}}\_t \negmedspace (\bm{r}, \psi)
	\simeq &-\frac{\mu_0^{-1}}{2 k R_j} \mathcal{Re} \bigg\lbrace
	\left( \bm{E_0} e^{i k (\bm{R_j} \cdot \bm{n_i})} \right) \times
	\frac{\eta}{c} (\bm{R_j} / R_j) \times
	\left[ \mathcal{S_j} (\bm{R_j} / R_j) \cdot \bm{E_0} e^{i k R_j} \right]^{\*}
	\bigg\rbrace
	\cr
	&-\frac{\mu_0^{-1}}{2 k R_j} \mathcal{Re} \bigg\lbrace
	\left( \mathcal{S_j} (\bm{R_j} / R_j) \cdot \bm{E_0} e^{i k R_j} \right) \times
	\left[ \bm{B_0} e^{i k (\bm{R_j} \cdot \bm{n_i})} \right]^{\*}
	\bigg\rbrace.
\end{aligned}
$$

The essential difference between Eqn. 16.23 and 17.28 stems from the fact that the particle is no longer fixed at the origin of the coordinate system, so interference is observed within a small beam-shaped region centered at \\(\bm{r_j}\\) and oriented along \\(\bm{R_j} = \bm{r} - \bm{r_j}\\), with the cross-sectional area on the order of the numerical value of \\(C_g\\). Naturally, if \\(\bm{r}\\) is not located within the beam, interference is not observed.

[Maybe add an illustration?]

If we place a measurement device in the radiation zone of the particle, orient it perpendicularly to the direction of observation \\(\bm{n_i}\\), and make sure that the area \\(C_d\\) of the entrance pupil is sufficiently large \\((C_d \gg C_g)\\) to fully capture the "extinction beam", then, after a couple of trivial substitutions, we may directly reuse the results from the previous section. In particular, substitution of Eqn. 16.30 into 17.28, followed by integration over a large sphere, a tiny region of which (centered at \\(\bm{r} = \bm{r_j} + R_j \bm{n_i}\\)) overlaps the entrance pupil of the detector, produces a direct analog of Eqn. 16.34:

$$ \tag{17.29}
	\Phi_{ij}(\psi)
	= \oint_{\mathbb{S}^2} \braket{\bm{S_{ij}}}\_t \negmedspace  \cdot \bm{n} \thinspace dA
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{4 \pi}{k^2} \mathcal{Im} \left\lbrace \bm{E_0^{\*}} \cdot \mathcal{S_j}(\bm{n_i}, \psi) \cdot \bm{E_0} \right\rbrace.
$$

Eqn. 17.29 gives the amount of power dissipated by the particle. The result is formally independent of the distance from the particle to the detector, but it does depend on its lateral position, since a movement of the particle results in translation of its beam. Assuming the entrance pupil of the measurement device is sufficiently large \\((C_d \gg L^2)\\) to image the entire particle group at once, performing ensemble averaging over the particle positions has no effect:

$$ \tag{17.30}
	\braket{\Phi_e}
	= \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{4 \pi}{k^2}
	\mathcal{Im} \left\lbrace
	\bm{E_0^{\*}} \cdot \braket{\mathcal{S_j}}\_{\psi} \negmedspace (\bm{n_i}) \cdot \bm{E_0}
	\right\rbrace.
$$

Thus, the mean amount of power \\(\braket{\Phi_e}\\) dissipated per particle is the same as that of the average particle \[[8](#references) (ch. 14.6)\]. Intuitively, if the number density of the particles is small, their "extinction beams" [do not overlap](https://ghostbusters.fandom.com/wiki/Cross_the_Streams).

It is also interesting to consider the impact of extinction on a small particle (as opposed to a large detector). The probability of lining up a uniformly-distributed particle with a fixed target is on the order of \\(1/L^2\\). If the particle group is sparse (with particles occupying a small fraction of the total volume), and the particles themselves are weakly absorbing and weakly scattering, then interference between the incident light and the light scattered by the particle group has practically no effect as far as another small particle is concerned (cf. Eqn. 17.0) \[[8](#references) (ch. 14.2)\]:

$$ \tag{17.31}
	\frac{\sum_{j=1}^{N} C_{e,j}}{L^2}
	= \frac{\sum_{j=1}^{N} \Phi_{ij} / \Epsilon_i}{L^2}
	= \frac{4 \pi \sum_{j=1}^{N}  \thinspace \mathcal{Im} \big\lbrace \bm{E_0^{\*}} \cdot \mathcal{S_j}(\bm{n_i}, \psi) \cdot \bm{E_0} \big\rbrace}{(k L)^2 |\bm{E_0}|^2} \ll 1.
$$

Let us now consider the final term of Eqn. 17.8 that accounts for interference of the scattered fields of two particles. Since the observation point \\(\bm{r}\\) is located in the radiation zone of each particle, we can obtain the formula of \\(\braket{\bm{S_{jk}}}\\) by substituting Eqn. 17.6 twice into 17.9.

The resulting expression is highly symmetrical. It can be readily simplified by taking advantage of the identities given by Eqn. 16.11 and 16.25, according to which,

$$ \tag{17.39}
\begin{aligned}
	\bm{n_s} \cdot \big( \bm{E_j} \times \bm{B_k^{\*}} \big)
	&\simeq \frac{\eta}{c}
	\big( \bm{E_j} \cdot \bm{E_k^{\*}} \big).
\end{aligned}
$$

Substitution of Eqn. 17.39 into 17.9 yields a compact expression

$$ \tag{17.40}
	\bm{n_s} \cdot \braket{\bm{S_{jk}}}\_t \negmedspace (\bm{r}, \psi)
	\simeq -\frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \big( \bm{E_j} \cdot \bm{E_k^{\*}} + \bm{E_k} \cdot \bm{E_j^{\*}} \big)
	= -\mu\_0^{-1} \frac{\eta}{c} \mathcal{Re} \big\lbrace \bm{E_j^{\*}} \cdot \bm{E_k} \big\rbrace.
$$

If we ignore all interactions between the particles (except through their fields), then \\(\bm{E_j}\\) and \\(\bm{E_k}\\) can be considered the outcomes of two independent random processes. This assumption allows us to perform ensemble averaging of the scattered fields separately:

$$ \tag{17.41}
\begin{aligned}
	\bm{n_s} \cdot \braket{\braket{\bm{S_{jk}}}} \negmedspace (\bm{r})
	&\simeq -\mu\_0^{-1} \frac{\eta}{c} \mathcal{Re} \big\lbrace \negmedspace
	\braket{\bm{E_j^{\*}}}\_{\psi} \cdot \braket{\bm{E_k}}\_{\psi}
	\negmedspace \big\rbrace
	\cr
	&= -\mu\_0^{-1} \frac{\eta}{c} \mathcal{Re} \big\lbrace \negmedspace
	\braket{\bm{E_j}}\_{\psi}^{\*} \cdot \braket{\bm{E_j}}\_{\psi}
	\negmedspace \big\rbrace
	= -\mu\_0^{-1} \frac{\eta}{c} \big| \negmedspace
	\braket{\bm{E_j}}\_{\psi} \negmedspace \big|^2.
\end{aligned}
$$

Unlike Eqn. 17.26, Eqn. 17.41 corresponds to (double) the intensity of light scattered by the average particle (cf. Eqn. 16.15).

Let us expand this expression by explicitly performing ensemble averaging. Suppose that every position is equally likely, with the corresponding probability density \\(p(\bm{r_j}) = 1/V\\). Then we can account for random motion of particles by evaluating the integral

$$ \tag{17.42}
	v(f, g) = \frac{1}{V} \int_V
	\exp \negmedspace \left(
	\frac{i}{2} \frac{k^2}{k r} r_j^2 \left( 1 - (\bm{n_j} \cdot \bm{n_s})^2 \right)
	\right)
	\exp \negmedspace \big(
	i k \bm{r_j} \cdot (\bm{n_i} - \bm{n_s})
	\big)
	dV_j.
$$

Note that Eqn. 17.41 only features the squared magnitude of \\(v(f, g)\\), so its phase is irrelevant.

We can gain some intuition about the behavior of this integral by analyzing its terms in isolation. First, consider

$$ \tag{17.31}
	v(f, 0) = \frac{1}{V} \int_V
	\exp \negmedspace \left(
	\frac{i}{2} \frac{k^2}{k r} r_j^2 \left( 1 - (\bm{n_j} \cdot \bm{n_i})^2 \right)
	\right) dV_j,
$$

which is a special case of Eqn. 17.42 with \\(\bm{n_i} = \bm{n_s}\\).

For a suitably symmetric region \\(V\\), the value of Eqn. 17.31 can be expressed in terms of the ratio

$$ \tag{17.32}
	f = \frac{(k L)^2}{k r}
$$

of the dimensionless area of the cross-section \\((k L)^2\\) to the dimensionless distance to the observation point \\(k r\\).

It is convenient to reparametrize Eqn. 17.31 by aligning \\(\bm{n_i}\\) with an axis of the coordinate system:

$$ \tag{17.33}
	r_j^2 \left( 1 - (\bm{n_j} \cdot \bm{n_i})^2 \right)
	= (r_j \sin{\theta_j})^2
	= x_j^2 + y_j^2.
$$

Then, for an axis-aligned cube of volume \\(L^3\\),

$$ \tag{17.34}
\begin{aligned}
	\big| v(f, 0) \big| &= \left| \frac{1}{V} \int_V
	\exp \negmedspace \left(
	\frac{i}{2} \frac{k^2}{k r} \left( x_j^2 + y_j^2 \right)
	\right) dx_j dy_j dz_j \right|
	\cr
	&= \left| \left( \int_{-1/2}^{1/2}
	\exp \negmedspace \left(
	i f u^2 / 2
	\right) du \right)^2 \right|
	\approx \frac{\sin(f/11)}{f/11}.
\end{aligned}
$$

Similarly, for a sphere of the same volume (with a slightly larger cross-sectional area),

$$ \tag{17.35}
\begin{aligned}
	\big| v(f, 0) \big| = \left| \frac{1}{V} \int_V
	\exp \negmedspace \left(
	\frac{i}{2} \frac{k^2}{k r} \left( r_j \sin{\theta_j} \right)^2
	\right) r_j^2 \sin{\theta_j} dr_j d\theta_j d\phi_j \right|
	\approx \frac{\sin(f/11.6)}{f/11.6}.
\end{aligned}
$$

The approximations of Eqn. 17.34 and 17.35 are reasonably accurate provided \\(f \leq 4\\).

{{< figure src="/img/vol_int_1.svg" caption="*Figure N: Plot of \\(|v(f, 0)|^2\\) defined by Eqn. 17.35.*" >}}

In general, \\(v(f, 0)\\) is a fairly complicated function that tends[^21] to \\(1\\) as \\(f \to 0\\). The latter indicates that the observer approaches the radiation zone of the entire particle group.

[^21]: Since \\(\exp(i f u^2) \to 1\\) as \\(f \to 0\\), the same holds for the value of \\(\int \exp(i f u^2) du\\) taken over a sufficiently small interval.

{{< figure src="/img/vol_int_2.svg" caption="*Figure N: Plot of \\(f^2 |v(f, 0)|^2\\) defined by Eqn. 17.35. We can observe that the moving average of \\(|v(f, 0)|^2\\) is inversely proportional to \\(f^2\\), and thus, \\((k L)^4\\).*" >}}

Another important case of interest is when both \\(k L \gg 1\\) and \\(k r \gg 1\\) in such a way that \\(f \gg 1\\). That causes the approximations of Eqn. 17.34-17.35 to break down. One may encounter this situation in practice while considering the force exerted by the electromagnetic field of one particle group onto a particle in an adjacent group. Fortunately, by taking the limit of Eqn. 17.31, one can show[^22] that \\(v \to 0\\) as \\(f \to \infin\\).

[^22]: Both the real and the imaginary parts of \\(\exp(i f u^2)\\) rapidly oscillate around \\(0\\), except for the central region where \\(f u^2 \approx 0\\). As \\(f \to \infin\\), the contribution of the central region to the value of \\(\int \exp(i f u^2) du\\) decreases, and thus \\(v \to 0\\).

Now, suppose that the observer is located in the radiation zone of the entire particle group. Then \\(f \to 0\\), which reduces Eqn. 17.42 to

$$ \tag{17.44}
	v(0, g) = \frac{1}{V} \int_V \exp \negmedspace \left(
	i k \bm{r_j} \cdot (\bm{n_i} - \bm{n_s})
	\right) dV_j.
$$

For a suitably symmetric region \\(V\\), Eqn. 17.44 can be [reparametrized](https://en.wikipedia.org/wiki/List_of_trigonometric_identities#Half-angle_formulae) in terms of the real number

$$ \tag{17.43}
	g
	= k L |\bm{n_i} - \bm{n_s}|
	= 2 k L \sqrt{\frac{1 - \bm{n_i} \cdot \bm{n_s}}{2}}
	= 2 k L \sin(\theta/2)
$$

that depends on both the dimensions of the particle group and the degree of alignment of the wave vectors.

It is convenient to reparametrize[^20] Eqn. 17.44 by aligning \\(\bm{n_i} - \bm{n_s}\\) with an axis of the coordinate system:

[^20]: This parameterization differs from the one used in Eqn. 17.33. As a result, the orientation of the cubical volume is not the same.

$$ \tag{17.45}
	\bm{r_j} \cdot \frac{\bm{n_i} - \bm{n_s}}{|\bm{n_i} - \bm{n_s}|}
	= r_j \cos{\theta_j}
	= z_j
$$

Then, for an axis-aligned cube of volume \\(L^3\\),

$$ \tag{17.46}
	v(0, g)
	= \frac{1}{V} \int_V
	\exp \negmedspace \left(
	i k |\bm{n_i} - \bm{n_s}| z_j
	\right) dx_j dy_j dz_j
	= \int_{-1/2}^{1/2}
	\exp \negmedspace \left(
	i g u
	\right) du
	= \frac{\sin(g/2)}{g/2}.
$$

Similarly, for a sphere of the same volume (with a slightly larger cross-sectional area),

$$ \tag{17.47}
\begin{aligned}
	\big| v(0, g) \big|
	= \left| \frac{1}{V} \int_V
	\exp \negmedspace \left(
	i k |\bm{n_i} - \bm{n_s}| r_j \cos{\theta_j}
	\right) r_j^2 \sin{\theta_j} dr_j d\theta_j d\phi_j \right|
	\approx \frac{\sin(g / 2.1)}{g / 2.1}.
\end{aligned}
$$

The approximation of Eqn. 17.47 is reasonably accurate provided \\(g \leq 4\\).

{{< figure src="/img/vol_int_3.svg" caption="*Figure N: Plot of \\(|v(0, g)|^2\\) defined by Eqn. 17.47.*" >}}

Figure N demonstrates that the scattered waves always interfere in the direction of incidence \\((g = 0)\\). If, in addition, the dimensions of the particle group are small in comparison with the wavelength of light \\((g \leq k L \ll 1)\\), then the scattered waves strongly interfere in every direction. That is not surprising, since the particles simply do not have the opportunity to move out-of-phase.

{{< figure src="/img/vol_int_4.svg" caption="*Figure N: Plot of \\(g^4 |v(0, g)|^2\\) defined by Eqn. 17.47. We can observe that the moving average of \\(|v(0, g)|^2\\) is inversely proportional to \\(g^4\\), and thus, \\((k L)^4\\).*" >}}

In general, the strength of the interference effect wanes as the scattering angle \\(\theta = \arccos(\bm{n_i} \cdot \bm{n_s})\\) or the linear dimension \\(kL\\) increase. As \\(kL \to \infin\\), the graph of Figure N (parameterized by \\(\theta\\) in this case) is horizontally compressed while maintaining the peak value of \\(1\\). As a result, its area decreases, and the limit of the integral taken over all scattering directions is

$$ \tag{17.48}
	\lim_{kL \to \infin} \int_{-\pi}^{\pi} \int_{0}^{\pi} \big| v \big(0, 2 k L \sin(\theta/2) \big) \big|^2 \sin{\theta} d\theta d\phi = 0.
$$

Therefore, if the observer is located in the radiation zone of an extensive particle group, the scattered fields of two particles do not cause significant interference:

$$ \tag{17.49}
	\braket{\Phi_{jk}}
	= \oint\_{\mathbb{S}^2} r^2 \braket{\braket{\bm{S_{jk}}}} \cdot \bm{n_s} d\Omega \approx 0.
$$

Finally, let us show that Eqn. 17.49 is true at any distance (subject to the conditions imposed by Eqn. 17.1, 17.3 and 17.4). It is fairly difficult to prove this formally, so we will have to resort to an intuitive argument. Return to Eqn. 17.42 and 17.44, and perform a change of variables \\(u = r_j / L\\). Comparison of the integrands reveals that the first complex exponential with \\((k L u)^2\\) performs amplitude modulation of the second one containing \\(k L u\\). These expressions guarantee that oscillation happens at different rates. The combined exponential reaches it peak (unit) amplitude at \\(u = \theta = 0\\) , and then (as the value of \\(u\\) increases) begins to rapidly oscillate, owing to the fact that \\(k L \gg 1\\). Similarly to Eqn. 17.44, the primary contribution to the absolute value of \\(v(f,g)\\) comes from the central peak, the width of which is inversely proportional to \\((k L)^2\\). As it shrinks, the amount of power in the \\(\braket{\Phi_{jk}}\\) decreases accordingly.

---

Let us summarize the results of this section. If the ergodic assumption and the single scattering approximation are applicable, and the conditions imposed by Eqn. 17.1, 17.3, and 17.4 hold, then the mean value of the Poynting vector (cf. Eqn. 17.8) in the presence a group of \\(N\\) particles is

$$ \tag{17.51}
\begin{aligned}
	\braket{\braket{\bm{S}}}
	= \braket{\bm{S_i}}
	&+ N \left(\frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{1}{k^2 r^2}
	\left\langle \left| \mathcal{S_j} \cdot \bm{E_0} \right|^2 \right\rangle\_{\psi}
	\right) \bm{n_s}
	\cr
	&- \frac{N}{L^2} \left( \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{4 \pi}{k^2 r^2}
	\mathcal{Im} \left\lbrace
	\bm{E_0^{\*}} \cdot \braket{\mathcal{S_j}}\_{\psi} \cdot \bm{E_0}
	\right\rbrace \right) \delta(\bm{n_i} - \bm{n_s}) \bm{n_s}
	\cr
	&+ (N - 1)^2 \left(\frac{\mu\_0^{-1}}{2} \frac{\eta}{c} \frac{1}{k^2 r^2}
	\left| v(f,g) \braket{\mathcal{S_j}}\_{\psi} \cdot \bm{E_0} \right|^2
	\right) \bm{n_s}.
\end{aligned}
$$

The third term may be neglected if we are concerned with the force exerted on a small particle, but must be taken into account if we perform a light measurement by a large detector.

Finally, the impact of the last term is insignificant (relative to the first two) if

$$ \tag{17.54}
	\frac{(N - 1)^2}{N} \left| v(f,g) \right|^2 \ll 1
	\quad \iff \quad
	\frac{N}{(k L)^4} \ll 1.
$$

Eqn. 17.54 shows that, if the [number density](https://en.wikipedia.org/wiki/Number_density) of particles

$$ \tag{17.55}
	n = \frac{N}{V} = \frac{N}{L^3}
$$

is kept constant, then, as the size of the particle group increases, the fraction of power attributed to interference between the scattered waves of individual particles proportionally decreases. This process is limited by the requirement on the total number of particles \\(N\\) that must remain sufficiently low in order for the single-scattering approximation to remain applicable.

Elimination of the last term of Eqn. 17.51 allows the *light intensities of the individual particles to be summed* into the total amount of light scattered (or dissipated) by the particle group \[[4](#references) (ch. 4.22)\]:

$$ \tag{17.56}
\begin{aligned}
	\braket{\Iota_s^{tot}}
	&= \sum_j r^2 \braket{\braket{\bm{S_j}}} \cdot \bm{n_s}
	= \frac{N}{k^2} \frac{\mu\_0^{-1}}{2} \frac{\eta}{c}
	\int_{\Psi} p(\psi)
	\left| \mathcal{S_j}(\bm{n_s}, \psi) \cdot \bm{E_0} \right|^2
	d\psi,
	\cr
	\braket{\Phi_s^{tot}}
	&= \oint\_{\mathbb{S}^2} \braket{\Iota_s^{tot}} \negmedspace (\bm{n_s}) d\Omega
	= \frac{N}{k^2} \frac{\mu\_0^{-1}}{2} \frac{\eta}{c}
	\int_{\Psi} p(\psi) \oint\_{\mathbb{S}^2}
	\left| \mathcal{S_j}(\bm{n_s}, \psi) \cdot \bm{E_0} \right|^2
	d\Omega d\psi,
	\cr
	\braket{\Phi_e^{tot}}
	&= L^2 \sum_j \oint\_{\mathbb{S}^2}
	\left[ r^2 \braket{\braket{\bm{S_{ij}}}} \cdot \bm{n_s} \right] d\Omega
	= \frac{N}{k^2} \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} 4 \pi
	\int_{\Psi} p(\psi)
	\mathcal{Im} \left\lbrace
	\bm{E_0^{\*}} \cdot \mathcal{S_j}(\bm{n_i}, \psi) \cdot \bm{E_0}
	\right\rbrace
	d\psi.
\end{aligned}
$$

Furthermore, if the incident irradiance \\(\Epsilon_i\\) is uniform across the particle group, then the optical cross-sections of the particle group are directly proportional to the mean optical cross-sections of a single particle:

$$ \tag{17.57}
	\braket{C_x^{tot}}
	= \frac{\braket{\Phi_x^{tot}}}{\Epsilon_i}
	= \frac{N \braket{\Phi_x}}{\Epsilon_i}
	= N \braket{C_x}
$$

where \\(x\\) can stand for absorption \\((a)\\), scattering \\((s)\\), or extinction \\((e)\\). Thus, we find that the *optical cross-sections of the individual particles can be summed* as well.

Often, one has to consider a large cloud of particles. Its interior may be conceptually subdivided into small, non-overlapping regions, each containing a particle group. In order to avoid keeping track of both the volume \\(V\\) and the number of particles \\(N\\) in each region, it is highly convenient to combine them into the spatially-varying number density \\(n(\bm{r})\\) defined by Eqn. 17.55. If, in addition, the distribution of particles \\((\\)governed by the probability density function \\(p(\psi))\\) is the same across the entire cloud, we can parametrize its interior using the spatially-varying *volume[^27] collision coefficients*

[^27]: Sometimes, it is more convenient to specify the mass density instead of the number density of particles, which leads to the definition of the mass collision coefficients \[[2](#references) (ch. 3)\].

$$ \tag{17.58}
	\varSigma_a(\bm{r}) = n(\bm{r}) \braket{C_a},
	\quad
	\varSigma_s(\bm{r}) = n(\bm{r}) \braket{C_s},
	\quad
	\varSigma_e(\bm{r}) = n(\bm{r}) \braket{C_e}.
$$

These are simply the weighted averages of the optical cross-sections of the individual particles.

<!--
### Lorenz-Mie-Debye Theory

The Lorenz-Mie-Debye theory presents a rigorous solution to the problem of [diffraction](https://en.wikipedia.org/wiki/Diffraction) by a conducting sphere, also known as [Mie scattering](https://en.wikipedia.org/wiki/Mie_scattering). The solution is exact in the sense that it does not employ any approximations, so it exhibits all classical and semi-classical (i.e. wave-mechanical) effects, and thus shows an excellent agreement with experimental data (see \[[16](#references) (ch. 5)\] for a list of references).

The original theory was independently formulated by Lorenz (1890), Mie (1908), and Debye (1909). The derivation of the relevant formulae can be found in many popular textbooks, such as \[[4](#references) (ch. 9), [6](#references) (ch. 14.5)\]. A more mathematically elegant treatment of the problem was given by Hansen in a series of papers (1935, 1936, 1937). It has also been presented in a recent open access paper \[[15](#references)\], which we use as the foundation of this section.

The general idea behind the solution is fairly simple. We treat electromagnetic scattering as a boundary value problem. If we expand the expression of the electromagnetic field in series of vector spherical harmonics, the boundary conditions take a particularly simple form, which can be harnessed to find the (initially unknown) coefficients of the series expansion.

Consider the Maxwell equations in a source-free region of a linear, isotropic, homogeneous medium given by Eqn. 6.3. We have seen that, after a number of transformations, they are reduced to a *vector* Helmholtz equation

$$ \tag{18.1}
	\big( \nabla^2 + k^2(\omega) \big) \bm{E}(\bm{r}, \omega) = 0.
$$

We have previously solved this equation by expanding it in Cartesian coordinates. That allowed us to separate a single vector equation into three scalar equations, which could then be solved individually. While this leads to a convenient (plane wave) representation of the incident field, it is a poor fit for the scattered field, since the latter takes the form of a spherical wave in the radiation zone. In addition, the geometry of the scatterer suggests that we should exploit the spherical symmetry of the problem in some way.

Eqn. 18.1 can be written in yet another way. Recall (cf. Eqn. 9.19) that we can express the electric field \\(\bm{E}\\) in terms of the vector potential \\(\bm{A}\\) and the scalar potential \\(\phi\\)

$$ \tag{18.2}
\begin{aligned}
	&\bm{E}(\bm{r}, \omega) = i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega), &
	&\bm{B}(\bm{r}, \omega) = \nabla \times \bm{A}(\bm{r}, \omega).
\end{aligned}
$$

Note that we are free to choose the value of \\(\nabla \cdot \bm{A}\\) to suit our particular problem.

Let us find the formulation in terms of the electromagnetic potential by substituting Eqn. 18.2 and 5.2.3 into the Maxwell equations 6.2.3-6.2.4:

$$ \tag{18.3}
\begin{aligned}
	& \nabla \times \nabla \times \bm{A}(\bm{r}, \omega)
	+ i \omega \mu(\omega) \varepsilon(\omega) \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0,
	\cr
	& \nabla \cdot \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0.
\end{aligned}
$$

After using the definition of the Laplace operator and applying the curl of curl identity given by Eqn. 6.6, we obtain the following expressions:

$$ \tag{18.4}
\begin{aligned}
	& \nabla \big( \nabla \cdot \bm{A}(\bm{r}, \omega) \big) - \nabla^2 \bm{A}(\bm{r}, \omega)
	+ i \omega \mu(\omega) \varepsilon(\omega) \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0,
	\cr
	& \nabla \cdot \bm{A}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla^2 \phi(\bm{r}, \omega).
\end{aligned}
$$

These two equations may be combined, and the Laplace operator and the gradient -- [interchanged](https://en.wikipedia.org/wiki/Vector_calculus_identities#Third_derivatives):

$$ \tag{18.5}
\begin{aligned}
	& \nabla^2 \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big)
	+ \omega^2 \mu(\omega) \varepsilon(\omega) \big( i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega) \big) = 0,
	\cr
	& \nabla \cdot \bm{A}(\bm{r}, \omega) = \frac{1}{i \omega} \nabla^2 \phi(\bm{r}, \omega).
\end{aligned}
$$

Since the value of \\(\nabla \cdot \bm{A}\\) is arbitrary, we may take inspiration from the Lorenz condition of Eqn. 9.20, and assume that

$$ \tag{18.6}
	\nabla \cdot \bm{A}(\bm{r}, \omega) = i \omega \mu(\omega) \varepsilon(\omega) \phi(\bm{r}, \omega).
$$

From Eqn. 9.21, it immediately follows that

$$ \tag{18.7}
	\frac{1}{i \omega} \nabla^2 \phi(\bm{r}, \omega)
	= i \omega \mu(\omega) \varepsilon(\omega) \phi(\bm{r}, \omega),
$$

which implies that \\(\phi\\) satisfies the scalar Helmholtz equation

$$ \tag{18.8}
	\big( \nabla^2 + k^2(\omega) \big) \phi(\bm{r}, \omega) = 0,
$$

where \\(k\\) is the complex wavenumber:

$$ \tag{18.9}
	k(\omega) = \omega \sqrt{\varepsilon(\omega) \mu(\omega)}.
$$

Similarly, substitution into Eqn. 18.5.1 shows that \\(\bm{A}\\) satisfies the vector Helmholtz equation

$$ \tag{18.10}
	\big( \nabla^2 + k^2(\omega) \big) \bm{A}(\bm{r}, \omega) = 0.
$$

Since the Helmholtz equation is both homogeneous and linear, it has multiple special solutions that can be combined into a single general solution according to the superposition principle:

$$ \tag{18.11}
	\phi(\bm{r}, \omega) = \sum_{m,n} c_{m,n}(\omega) \psi_{m,n}(\bm{r}, \omega),
$$

with each special solution \\(\psi_{m,n}\\) satisfying the same equation

$$ \tag{18.12}
	\big( \nabla^2 + k^2(\omega) \big) \psi_{m,n}(\bm{r}, \omega) = 0.
$$

Similarly, in 3-dimensional space, the vector potential \\(\bm{A_{m,n}}\\) can be represented by a sum of 3 linearly independent vectors \\(\bm{L_{m,n}}, \bm{M_{m,n}}, \bm{N_{m,n}}\\):

$$ \tag{18.13}
\begin{aligned}
	\bm{A}(\bm{r}, \omega)
	&= \frac{1}{i \omega} \sum_{m,n} \bm{A_{m,n}}(\bm{r}, \omega)
	\cr
	&= \frac{1}{i \omega} \sum_{m,n} \big( a_{m,n}(\omega) \bm{M_{m,n}}(\bm{r}, \omega) + b_{m,n}(\omega) \bm{N_{m,n}}(\bm{r}, \omega) + c_{m,n}(\omega) \bm{L_{m,n}}(\bm{r}, \omega) \big).
\end{aligned}
$$

How should we define these 3 vectors? According to the [Helmholtz decomposition](https://en.wikipedia.org/wiki/Helmholtz_decomposition) (Eqn. 8.8), any sufficiently smooth vector field \\(\bm{A}\\) can be represented by a sum

$$ \tag{18.14}
\begin{aligned}
	&\bm{A} = \bm{A'} + \bm{A''}, &
	&\nabla \cdot  \bm{A' } = 0, &
	&\nabla \times \bm{A''} = 0. &
\end{aligned}
$$

Let us choose \\(\bm{L_{m,n}}\\) as [irrotational](https://en.wikipedia.org/wiki/Conservative_vector_field#Irrotational_vector_fields), and \\(\bm{M_{m,n}}\\) and \\(\bm{N_{m,n}}\\) as [solenoidal](https://en.wikipedia.org/wiki/Solenoidal_vector_field):

$$ \tag{18.15}
	\bm{A_{m,n}'} = a_{m,n} \bm{M_{m,n}} + b_{m,n} \bm{N_{m,n}},
	\quad
	\bm{A_{m,n}''} = c_{m,n} \bm{L_{m,n}}.
$$

From Eqn. 18.5.2 and 18.11, and 18.13, it follows that

$$ \tag{18.16}
	\nabla \cdot \bm{A_{m,n}} = \nabla \cdot (c_{m,n} \nabla \psi_{m,n}).
$$

In other words, Eqn. 18.13 and 18.16 suggest that we may define

$$ \tag{18.17}
	\bm{L_{m,n}} = \frac{1}{k} \nabla \psi_{m,n},
	\quad
	\nabla \times \bm{L_{m,n}} = 0,
$$

where the \\(1/k\\) constant is chosen in order to make the resulting expression dimensionless \[[25](#references) (ch. 4.3)\].

Since divergence of curl is zero, Eqn. 18.14.2 and 18.15.1 suggests that both \\(\bm{M\_{m,n}}\\) and \\(\bm{N\_{m,n}}\\) should be the curl of something. In addition, both should depend on \\(\psi\_{m,n}\\). Since \\(\psi\_{m,n}\\) is a scalar, its curl is undefined; a simple way to fix that is to introduce a vector \\(\bm{a}\\):

$$ \tag{18.18}
	\bm{M_{m,n}} = \nabla \times (\psi_{m,n} \bm{a}),
	\quad
	\nabla \cdot \bm{M_{m,n}} = 0.
$$

Finally, the curl generates a linearly independent vector field. Thus, we may choose

$$ \tag{18.19}
	\bm{N_{m,n}} = \frac{1}{k} \nabla \times \bm{M_{m,n}},
	\quad
	\nabla \cdot \bm{N_{m,n}} = 0.
$$

The set of vectors \\(\lbrace \bm{L\_{m,n}}, \bm{M\_{m,n}}, \bm{N\_{m,n}} \rbrace\\) is called the *vector spherical wave functions*. They are defined in terms of the *scalar spherical wave functions* \\(\psi\_{m,n}\\) and the *guiding vector* \\(\bm{a}\\).

It is convenient to assume that the guiding vector belongs to an [irrotational](https://en.wikipedia.org/wiki/Conservative_vector_field#Irrotational_vector_fields) vector field, such that

$$ \tag{18.20}
	\nabla \times \bm{a} = 0.
$$

Coupled with the [product rule](https://en.wikipedia.org/wiki/Vector_calculus_identities#Product_rule_for_multiplication_by_a_scalar), Eqn. 18.20 leads to a simpler definition of \\(\bm{M_{m,n}}\\):

$$ \tag{18.21}
	\bm{M_{m,n}} = \psi (\nabla \times \bm{a}) + (\nabla \psi_{m,n}) \times \bm{a} = \bm{L_{m,n}} \times (k \bm{a}).
$$

If \\(\bm{a}\\) is a real vector, the new expression has a simple geometrical interpretation in terms of orthogonal vectors:

$$ \tag{18.22}
	\bm{M_{m,n}} \cdot \bm{a} = 0,
	\quad \text{or} \quad
	\bm{L_{m,n}} \cdot \bm{M_{m,n}} = 0.
$$

Each of the three vectors, \\(\bm{L_{m,n}}\\), \\(\bm{M_{m,n}}\\), and \\(\bm{N_{m,n}}\\), can be used to form (and solve) a vector Helmholtz equation. That is trivial to show this for \\(\bm{L_{m,n}}\\):

$$ \tag{18.23}
	\big( \nabla^2 + k^2 \big) \bm{L_{m,n}}
	= \frac{1}{k} \big( \nabla^2 + k^2 \big) \nabla \psi_{m,n}
	= \frac{1}{k} \nabla \big( \nabla^2 + k^2 \big) \psi_{m,n} = 0.
$$

For \\(\bm{M_{m,n}}\\), the corresponding expression is

$$ \tag{18.24}
	\big( \nabla^2 + k^2 \big) \bm{M_{m,n}}
	= \big( \nabla^2 + k^2 \big) \big( \nabla \times (\psi_{m,n} \bm{a}) \big)
	= \nabla \times \Big( \big( \nabla^2 + k^2 \big) \big( \psi_{m,n} \bm{a} \big) \Big).
$$

If \\(\bm{a}\\) is a *constant* vector, from Eqn. 18.12, it immediately follows that

$$ \tag{18.25}
	\big( \nabla^2 + k^2 \big) \bm{M_{m,n}}
	= \nabla \times \Big( \bm{a} \big( \nabla^2 + k^2 \big) \psi_{m,n} \Big) = 0.
$$

In general, for an arbitrary \\(\bm{a}\\), that is not the case. We may use the identity

$$ \tag{18.26}
	\nabla^2 (\psi_{m,n} \bm{a})
	= \bm{a} \nabla^2 \psi_{m,n}
	+ 2 (\nabla \psi_{m,n} \cdot \nabla) \bm{a}
	+ \psi_{m,n} \nabla^2 \bm{a}
$$

to expand Eqn. 18.24:

$$ \tag{18.27}
	\big( \nabla^2 + k^2 \big) \bm{M_{m,n}}
	= \nabla \times \big( \bm{a} \big( \nabla^2 + k^2 \big) \psi_{m,n}
	+ 2 (\nabla \psi_{m,n} \cdot \nabla) \bm{a}
	+ \psi_{m,n} \nabla^2 \bm{a} \big).
$$

Thus, \\(\bm{M_{m,n}}\\) satisfies a vector Helmholtz equation if

$$ \tag{18.28}
	\nabla \times \big(2 (\nabla \psi_{m,n} \cdot \nabla) \bm{a} + \psi_{m,n} \nabla^2 \bm{a} \big) = 0.
$$

If we make a simple, but non-obvious choice of \\(\bm{a} = \bm{r}\\), it is easy to show that

$$ \tag{18.29}
	(\nabla \psi_{m,n} \cdot \nabla) \bm{r}
	= \nabla \psi_{m,n},
	\quad
	\nabla^2 \bm{r} = 0,
$$

which confirms that

$$ \tag{18.30}
	\big( \nabla^2 + k^2 \big) \bm{M_{m,n}}
	= \nabla \times \big( \bm{r} \big( \nabla^2 + k^2 \big) \psi_{m,n}
	+ 2 \nabla \psi_{m,n} \big) = 0.
$$

Since \\(\bm{N_{m,n}}\\) is defined in terms of \\(\bm{M_{m,n}}\\), it directly follows that

$$ \tag{18.31}
	\big( \nabla^2 + k^2 \big) \bm{N_{m,n}}
	= \frac{1}{k} \nabla \times \Big( \big( \nabla^2 + k^2 \big) \bm{M_{m,n}} \Big) = 0.
$$

Now, consider what happens when we take the curl of \\(\bm{N_{m,n}}\\):

$$ \tag{18.32}
	\frac{1}{k} \nabla \times \bm{N_{m,n}}
	= \frac{1}{k^2} \nabla \times \nabla \times \nabla \times (\psi_{m,n} \bm{a})
	= -\frac{1}{k^2} \nabla \times \big( \nabla^2(\psi_{m,n} \bm{a}) \big).
$$

Since, according to Eqn. 18.12,

$$ \tag{18.33}
	\nabla^2 \psi_{m,n} = -k^2 \psi_{m,n},
$$

for *constant* \\(\bm{a}\\), Eqn. 18.32 leads to yet another definition of \\(\bm{M_{m,n}}\\):

$$ \tag{18.34}
	\bm{M_{m,n}}
	= \nabla \times (\psi_{m,n} \bm{a})
	= \frac{1}{k} \nabla \times \bm{N_{m,n}}.
$$

In order to show that it is also the case for \\(\bm{a} = \bm{r}\\), we must substitute Eqn. 18.26, 18.28, and 18.33 into 18.32:

$$ \tag{18.35}
\begin{aligned}
	\frac{1}{k} \nabla \times \bm{N_{m,n}}
	&= -\frac{1}{k^2} \nabla \times \big( \nabla^2(\psi_{m,n} \bm{a}) \big)
	\cr
	&= -\frac{1}{k^2} \nabla \times \big(
		\bm{a} \nabla^2 \psi_{m,n}
		+ 2 (\nabla \psi_{m,n} \cdot \nabla) \bm{a}
		+ \psi_{m,n} \nabla^2 \bm{a} \big)
	\cr
	&= -\frac{1}{k^2} \nabla \times \big(
		\bm{a} \nabla^2 \psi_{m,n} \big)
	 = \nabla \times (\psi_{m,n} \bm{a}) = \bm{M_{m,n}}.
\end{aligned}
$$

Going forward, we shall focus on the set of vectors \\(\lbrace \bm{L\_{m,n}}, \bm{M\_{m,n}}, \bm{N\_{m,n}} \rbrace\\) produced with the choice of \\(\bm{a} = \bm{r}\\). We have just demonstrated some of their basic properties, and we now can begin to apply them to simple problems.

Suppose that we have obtained the coefficients of the series expansion of the electromagnetic potential given by Eqn. 18.13. How can we find the associated electric and magnetic fields?

According to Eqn. 18.2.1, 18.11, 18.13, and 18.17,

$$ \tag{18.36}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= i \omega \bm{A}(\bm{r}, \omega) - \nabla \phi(\bm{r}, \omega)
	\cr
	&= \sum_{m,n} \big( \bm{A_{m,n}}(\bm{r}, \omega) - c_{m,n}(\omega) \nabla \psi_{m,n}(\bm{r}, \omega) \big)
	\cr
	&= \sum_{m,n} \big( a_{m,n}(\omega) \bm{M_{m,n}}(\bm{r}, \omega) + b_{m,n}(\omega) \bm{N_{m,n}}(\bm{r}, \omega) \big).
\end{aligned}
$$

The result is a solenoidal phasor field, which is consistent with the Maxwell equations in linear, isotropic, homogeneous, source-free media.

Similarly, the magnetic field can be determined using Eqn. 18.2.2, 18.13, 18.17, 18.19, and 18.35:

$$ \tag{18.37}
\begin{aligned}
	\bm{B}(\bm{r}, \omega)
	&= \nabla \times \bm{A}(\bm{r}, \omega)
	\cr
	&= \frac{1}{i \omega} \sum_{m,n} \Big( a_{m,n}(\omega) \big( \nabla \times \bm{M_{m,n}}(\bm{r}, \omega) \big) + b_{m,n}(\omega) \big( \nabla \times \bm{N_{m,n}}(\bm{r}, \omega) \big) \Big)
	\cr
	&= \frac{k}{i \omega} \sum_{m,n} \big( a_{m,n}(\omega) \bm{N_{m,n}}(\bm{r}, \omega) + b_{m,n}(\omega) \bm{M_{m,n}}(\bm{r}, \omega) \big).
\end{aligned}
$$

The resulting field is irrotational. Note that the relative order of the vectors in Eqn. 18.36 and 18.37 is reversed.

Expressions given above are valid for arbitrary electromagnetic fields. They have also been painstakingly crafted in a coordinate-independent manner. In practice, to obtain numerical results, once must choose a certain coordinate system. As it was hinted at the beginning of this section, the symmetry of the problem suggests that it is advantageous to approach the problem in a spherical coordinate system.

Consider again the scalar Helmholtz equation (17.8). In [spherical coordinates](https://en.wikipedia.org/wiki/Laplace_operator#Three_dimensions), it can be expressed as[^16]

[^16]: We use \\(f\\) in place of \\(\phi\\), since the letter used for the scalar potential clashes with the notation used for spherical coordinates.

$$ \tag{18.38}
	\frac{1}{r^2} \frac{\partial}{\partial r} \bigg( r^2 \frac{\partial f}{\partial r} \bigg)
	+ \frac{1}{r^2 \sin{\theta}} \frac{\partial}{\partial \theta} \bigg( \sin{\theta} \frac{\partial f}{\partial \theta} \bigg)
	+ \frac{1}{r^2 \sin^2{\theta}} \frac{\partial^2 f}{\partial \phi^2}
	+ k^2 f = 0,
$$

or, in its fully expanded form, as

$$ \tag{18.39}
	\frac{1}{r^2} \bigg(
	  \frac{\partial^2 f}{\partial \theta^2}
	+ \frac{\cos{\theta}}{\sin{\theta}} \frac{\partial f}{\partial \theta}
	+ \frac{1}{\sin^2{\theta}} \frac{\partial^2 f}{\partial \phi^2} \bigg)
	+ \frac{\partial^2 f}{\partial r^2}
	+ \frac{2}{r} \frac{\partial f}{\partial r}
	+ k^2 f = 0.
$$

If we look for a solution of the form

$$ \tag{18.40}
	f(\bm{r}) = f_r(r) f_{\theta}(\theta) f_{\phi}(\phi),
$$

substitution results in three separate ordinary differential equations:

$$ \tag{18.41}
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

Differential equations of this type are well known in the literature \[[17](#references) (ch. 7.3)\]. If we make a particular choice of

$$ \tag{18.42}
	p^2 = n (n+1),
	\quad
	q = m,
$$

without proof, the particular solutions of Eqn. 18.41.1-18.41.3 are

$$ \tag{18.43}
	f_r(r) = z_n(k r),
	\quad
	f_{\theta}(\theta) = P_n^m(\cos{\theta}),
	\quad
	f_{\phi}(\phi) = e^{i m \phi}.
$$

Since the solutions of the Helmholtz equation that are continuous (within a certain domain), finite, and single-valued, form a *discrete set*, the general solution of Eqn. 18.38 is

$$ \tag{18.44}
\begin{aligned}
	& f(\bm{r})
	= \sum_{m,n} c_{m,n} \psi_{m,n}(\bm{r}),
	\cr
	& \psi_{m,n}(\bm{r})
	= \gamma_{m,n} z_n(k r) P_n^m(\cos{\theta}) e^{i m \phi},
\end{aligned}
$$

where \\(m\\) and \\(n\\) are integers \\((n \ge 0)\\), \\(c_{m,n}\\) is a complex expansion coefficient, \\(\gamma_{m,n}\\) is a certain normalization constant, \\(z_n\\) is a [spherical Bessel function](https://en.wikipedia.org/wiki/Bessel_function#Spherical_Bessel_functions:_jn,_yn) of order \\(n\\), and \\(P_n^m\\) is an [associated Legendre polynomial](https://en.wikipedia.org/wiki/Associated_Legendre_polynomials) of degree \\(n\\) and order \\(m\\). Note that, as expected, the solution depends on the distance \\(r\\) only through the phase difference \\(k r\\).

Let us begin by examining the radial functions \\(z_n(k r)\\). They may be used to represent any of the three kinds of *spherical Bessel functions* \[[17](#references) (ch. 7.4)\]:

$$ \tag{18.48}
\begin{aligned}
	& j_n(x) = \sqrt{\frac{\pi}{2 x}} J_{n+1/2}(x),
	&
	& y_n(x) = \sqrt{\frac{\pi}{2 x}} Y_{n+1/2}(x),
	&
	& h_n^{(k)}(x) = \sqrt{\frac{\pi}{2 x}} H_{n+1/2}^{(k)}(x),
\end{aligned}
$$

where \\(J_n\\), \\(Y_n\\), and \\(H_n^{(k)}\\) are the [Bessel functions](https://en.wikipedia.org/wiki/Bessel_function) of the first, the second, and the third kind, respectively. More specifically, \\(H_n^{(1)}\\) and \\(H_n^{(2)}\\) are called the [Hankel functions](https://en.wikipedia.org/wiki/Bessel_function#Hankel_functions:_H(1)%CE%B1,\_H(2)%CE%B1) of the first and the second kind, and they are related to the ordinary Bessel functions in a very simple manner:

$$ \tag{18.49}
	H_n^{(1)}(x) = J_n(x) + i Y_n(x),
	\quad
	H_n^{(2)}(x) = J_n(x) - i Y_n(x).
$$

All spherical Bessel functions satisfy the [recurrence relations](https://dlmf.nist.gov/10.51) \[[17](#references) (ch. 7.4)\]

$$ \tag{18.50}
	\frac{z_n(x)}{x} = \frac{z_{n-1}(x) + z_{n+1}(x)}{2 n + 1},
	\quad
	\frac{\partial z_n(x)}{\partial x} = \frac{n z_{n-1}(x) - (n + 1) z_{n+1}(x)}{2 n + 1}.
$$

This implies that we can compute the value of the spherical Bessel function (or its derivative) of any order \\(n\\) if we utilize the [closed-form expressions](https://dlmf.nist.gov/10.49) of the functions of the orders 0 and 1:

$$ \tag{18.51}
\begin{aligned}
	& j_0(x) = \frac{\sin{x}}{x},
	&
	& j_1(x) = \frac{j_0(x)}{x} + y_0(x),
	&
	& j_n(x) = (2 n - 1) \frac{j_{n-1}(x)}{x} - j_{n-2}(x),
	\cr
	& y_0(x) = -\frac{\cos{x}}{x},
	&
	& y_1(x) = \frac{y_0(x)}{x} - j_0(x),
	&
	& y_n(x) = (2 n - 1) \frac{y_{n-1}(x)}{x} - y_{n-2}(x).
\end{aligned}
$$

As expected from the general scattering theory, the behavior of these functions is rather different depending on whether the magnitude of the argument is very large or very small.

{{< figure src="/img/spherical_bessel_j.svg" caption="*Figure N: Spherical Bessel functions of the first kind of order n=0 (blue), n=1 (orange), n=2 (green), n=3 (red).*" >}}

{{< figure src="/img/spherical_bessel_y.svg" caption="*Figure N: Spherical Bessel functions of the second kind of order n=0 (blue), n=1 (orange), n=2 (green), n=3 (red).*" >}}

For small values of the argument, the behavior is both varied and relatively complicated. In particular, the functions of the first kind take on finite values, while the functions of the second kind diverge at the origin. In the radiation zone, as \\(k r \to \infin\\), both types of functions behave as decaying sine waves.

{{< figure src="/img/spherical_hankel_1.svg" caption="*Figure N: Spherical Hankel functions of the first kind of order n=0 (blue), n=1 (orange), n=2 (green), n=3 (red). Solid lines correspond to the real part, while the imaginary part is drawn using dashed lines.*" >}}

For large values of the argument, the spherical Bessel functions of the first and the second kinds differ in phase by a factor of \\(\pi\\). The latter is prominently featured in the [asymptotic forms](https://dlmf.nist.gov/10.52) valid for \\(| x | \gg n^2/2\\):

$$ \tag{18.52}
\begin{aligned}
	& j_n(x) = \frac{1}{x} \sin\Big( x - \frac{\pi n}{2} \Big)
	+ \mathrm{O}\big( x^{-2} \big),
	&
	& y_n(x) = - \frac{1}{x} \cos\Big( x - \frac{\pi n}{2} \Big)
	+ \mathrm{O}\big( x^{-2} \big),
	\cr
	& h_n^{(1)}(x) = i^{-n-1} \frac{e^{ i x}}{x}
	+ \mathrm{O}\big( x^{-2} \big),
	&
	& h_n^{(2)}(x) = i^{n+1}  \frac{e^{-i x}}{x}
	+ \mathrm{O}\big( x^{-2} \big).
\end{aligned}
$$

Let us now turn our attention to the angular component of the solution

$$ \tag{18.53}
	P_n^m(\cos{\theta}) e^{i m \phi}
	= P_n^m(\cos{\theta}) \cos(m \phi)
	+ i P_n^m(\cos{\theta}) \sin(m \phi).
$$

If we set \\(m=0\\), the dependence on the azimuthal angle \\(\phi\\) disappears, and we are left with the [Legendre polynomial](https://en.wikipedia.org/wiki/Legendre_polynomials) \\(P_n\\)

$$ \tag{18.54}
	P_n(x)
	= \frac{1}{2^n n!} \frac{\partial^n}{\partial x^n} (x^2 - 1)^n
$$

plotted below:

{{< figure src="/img/legendre.svg" caption="*Figure N: Legendere polynomials of degree \\(n=0\\) (blue), \\(n=1\\) (orange), \\(n=2\\) (green), \\(n=3\\) (red).*" >}}

The degree of a polynomial is also the number of zeros of the function. Coupled with the fact that \\(P_n(\cos{\theta})\\) is independent of \\(\phi\\), we can observe that the Legendre polynomial of degree \\(n\\) divides the surface of a sphere into \\(n + 1\\) zones of alternating sign. For this reason, the family of functions \\(P_n(\cos{\theta})\\) is sometimes referred to as [zonal harmonics](https://mathworld.wolfram.com/ZonalHarmonic.html).

If \\(0 < m \leq n\\), we can define the associated Legendre polynomials in terms of the unassociated ones:

$$ \tag{18.55}
	P_n^m(x)
	= (-1)^m (1 - x^2)^{m/2} \frac{\partial^m}{\partial x^m} P_n(x).
$$

The polynomials of negative orders differ from their positive counterparts by a [constant](https://en.wikipedia.org/wiki/Associated_Legendre_polynomials#Negative_m_and/or_negative_%E2%84%93):

$$ \tag{18.47}
	P_n^{-m}
	= (-1)^m \frac{(n - m)!}{(n + m)!} P_n^m.
$$

Additionally, by careful manipulating the derivatives of Eqn. 18.54 and 18.55, one may show that the associated Legendre polynomials satisfy the [recurrence relation](https://en.wikipedia.org/wiki/Associated_Legendre_polynomials#Recurrence_formula)

$$ \tag{18.5?}
	x P_n^m(x) = \frac{n - m + 1}{2 n + 1} P_{n+1}^m(x) + \frac{n + m}{2 n + 1} P_{n-1}^m(x).
$$

As can be seen from the graph below, the value of the associated Legendre polynomial \\(P_n^m(\cos{\theta})\\) goes to zero at \\(\cos{\theta} = \pm 1\\). In addition, the total number of zeros of the function is \\(n - m + 2\\).

{{< figure src="/img/assoc_legendre.svg" caption="*Figure N: Associated Legendere polynomials of degree 3 and order \\(m=1\\) (blue), \\(m=2\\) (orange), \\(m=3\\) (green).*" >}}

We are now ready to interpret the entire Eqn. 18.53. Since that function has harmonic dependence on \\(\phi\\), if we plot one of its parts (either real or imaginary), we can observe that it divides the surface of a sphere into \\(2 m \times (n - m + 1)\\) rectangular domains, or tesserae. Thus, \\(P_n^m(\cos{\theta}) \cos(m \phi)\\) and \\(P_n^m(\cos{\theta}) \sin(m \phi)\\) are called the even and odd [tesseral harmonics](https://mathworld.wolfram.com/TesseralHarmonic.html) (or [sectorial harmonics](https://mathworld.wolfram.com/SectorialHarmonic.html) if \\(m = n\\)).

{{< figure src="/img/assoc_legendre_3.png" caption="*Figure N: Tesseral harmonics of degree 3 and order \\(m=1\\) (left), \\(m=2\\) (center), \\(m=3\\) (right). Warm colors correspond to positive values of the function, and cold colors - to negative values.*" >}}

Associated Legendre polynomials have a powerful property - they are [orthogonal](https://en.wikipedia.org/wiki/Associated_Legendre_polynomials#Reparameterization_in_terms_of_angles) \[[17](#references) (ch. 7.3)\]:

$$ \tag{18.56}
	\int_{-1}^{1} P_l^m(x) P_n^m(x) dx
	= \frac{2}{2n + 1} \frac{(n+m)!}{(n-m)!} \delta_{l,n}.
$$

In addition, we know that any sufficiently smooth univariate function can be expanded into a [Fourier series](https://en.wikipedia.org/wiki/Fourier_series), since

$$ \tag{18.57}
\begin{aligned}
	& \int_{-\pi}^{\pi} \sin(m x) \cos(n x) dx = 0,
	& (m \neq 0 \text{ or } n \neq 0),
	\cr
	& \int_{-\pi}^{\pi} \sin(m x) \sin(n x) dx = \pi \delta_{m,n}
	& (m \neq 0 \text{ or } n \neq 0),
	\cr
	& \int_{-\pi}^{\pi} \cos(m x) \cos(n x) dx = \pi \delta_{m,n}
	& (m \neq 0 \text{ or } n \neq 0),
\end{aligned}
$$

where \\(\delta_{m,n}\\) is the Kronecker delta function given by Eqn. 4.6.

Remarkably, by means of double integration over \\(\theta\\) and \\(\phi\\), these two facts can be combined to show that the tesseral harmonics \\(P_n^m(\cos{\theta}) \cos(m \phi)\\) and \\(P_n^m(\cos{\theta}) \sin(m \phi)\\) form a *complete orthogonal system* of basis functions on the surface of a sphere \[[18](#references) (ch. 7.5), [28](#references) (ch. 2.4)\].

Tesseral harmonics are [real-valued basis functions](https://en.wikipedia.org/wiki/Spherical_harmonics#Real_form), which is sufficient for most problems. However, the theory can be trivially extended to complex values by combining the tesseral harmonics according to Eqn. 18.53. This directly leads to the definition of [scalar spherical harmonics](https://mathworld.wolfram.com/SphericalHarmonic.html)

$$ \tag{18.58}
	Y_n^m(\theta, \phi)
	= \gamma_{m,n} P_n^m(\cos{\theta}) e^{i m \phi}
	= \sqrt{\frac{(2 n + 1)}{4 \pi} \frac{(n - m)!}{(n + m)!}} P_n^m(\cos{\theta}) e^{i m \phi},
$$

where \\(\gamma_{m,n}\\) is the normalization constant chosen in order to satisfy the normalization and the completeness relations

$$ \tag{18.59}
\begin{aligned}
	& \int_{-\pi}^{\pi} \int_{-1}^{1} \big[ Y_t^s(\theta, \phi) \big]^{\*} Y_n^m(\theta, \phi) \thinspace d(\cos{\theta}) d\phi = \delta_{s,m} \delta_{t,n},
	\cr
	& \sum_{n=0}^{\infin} \sum_{m=-n}^{n} \big[ Y_n^m(\theta', \phi') \big]^{\*} Y_n^m(\theta, \phi)
	= \delta(\cos{\theta} - \cos{\theta'}) \delta(\phi - \phi').
\end{aligned}
$$

In addition, Eqn. 18.47 shows that normalization leads to a simple relation

$$ \tag{18.60}
	Y_n^{-m}
	= (-1)^m \big[ Y_n^m \big]^{\*}.
$$

Thus, the spherical wave solution of Eqn. 18.38 (given by Eqn. 18.44) can be more compactly written as

$$ \tag{18.61}
\begin{aligned}
	& f(\bm{r})
	= \sum_{n=0}^{\infin} \sum_{m=-n}^{n} c_{m,n} \psi_{m,n}(\bm{r}),
	\cr
	& \psi_{m,n}(\bm{r})
	= z_n(k r) Y_n^m(\theta, \phi).
\end{aligned}
$$

We may now substitute Eqn. 18.61 in place of Eqn. 18.11, and carry out the subsequent derivation to find the explicit form of the solution of the Helmholtz equation of the *vector* potential (in terms of the vector spherical wave functions) that complements the solution of the Helmholtz equation of the *scalar* potential (in terms of the scalar spherical wave functions).

The expression of the first vector \\(\bm{L_{m,n}}\\) is given by Eqn. 18.17.1:

$$ \tag{18.62}
\begin{aligned}
	\frac{1}{\gamma_{m,n}} \bm{L_{m,n}}(\bm{r})
	&= \frac{\partial z_n(k r)}{\partial (k r)} P_n^m(\cos{\theta}) e^{i m \phi} \bm{e_r}
	\cr
	&+ \frac{z_n(k r)}{k r} \frac{\partial P_n^m(\cos{\theta})}{\partial \theta} e^{i m \phi} \bm{e_{\theta}}
	\cr
	&+ i m \frac{z_n(k r)}{k r} \frac{P_n^m(\cos{\theta})}{\sin{\theta}} e^{i m \phi} \bm{e_{\phi}},
\end{aligned}
$$

where \\(\lbrace \bm{e_r}, \bm{e_{\theta}}, \bm{e_{\phi}} \rbrace\\) is the set of unit vectors pointing along the spherical coordinate axes.

Similarly, we can determine the expression of \\(\bm{M_{m,n}}\\) by expanding Eqn. 18.18.1 with \\(\bm{a} = \bm{r}\\):

$$ \tag{18.6x}
\begin{aligned}
	\bm{M_{m,n}}(\bm{r})
	&= \frac{1}{\sin{\theta}} \frac{\partial \psi_{m,n}}{\partial \phi} \bm{e_{\theta}}
	- \frac{\partial \psi_{m,n}}{\partial \theta} \bm{e_{\phi}}.
\end{aligned}
$$

Substitution of Eqn. 18.61 yields

$$ \tag{18.63}
\begin{aligned}
	\frac{1}{\gamma_{m,n}} \bm{M_{m,n}}(\bm{r})
	&= i m z_n(k r) \frac{P_n^m(\cos{\theta})}{\sin{\theta}} e^{i m \phi} \bm{e_{\theta}}
	 - z_n(k r) \frac{\partial P_n^m(\cos{\theta})}{\partial \theta} e^{i m \phi} \bm{e_{\phi}}.
\end{aligned}
$$

Note that the radial component is zero, as expected from Eqn. 18.21 that contains a cross product with \\(\bm{r}\\).

Finally, we may find the representation of \\(\bm{N_{m,n}}\\) by substituting Eqn. 18.63 into 18.19.1:

$$ \tag{18.64}
\begin{aligned}
	\bm{N_{m,n}}(\bm{r})
	&= \frac{-1}{kr} \bigg(
	   \frac{\partial^2 \psi_{m,n}}{\partial \theta^2}
	 + \frac{\cos{\theta}}{\sin{\theta}} \frac{\partial \psi_{m,n}}{\partial \theta}
	 + \frac{1}{\sin^2{\theta}} \frac{\partial^2 \psi_{m,n}}{\partial \phi^2} \bigg) \bm{e_r}
	\cr
	&+ \frac{1}{kr} \bigg(
	   \frac{\partial \psi_{m,n}}{\partial \theta}
	 + r \frac{\partial^2 \psi_{m,n}}{\partial r \partial \theta} \bigg) \bm{e_{\theta}}
	\cr
	&+ \frac{1}{kr \sin{\theta}} \bigg(
	   \frac{\partial \psi_{m,n}}{\partial \phi}
	 + r \frac{\partial^2 \psi_{m,n}}{\partial r \partial \phi} \bigg) \bm{e_{\phi}}.
\end{aligned}
$$

Careful comparison of the expression of the radial component to Eqn. 18.39 yields

$$ \tag{18.65}
	(\bm{N_{m,n}})\_r
	= \frac{1}{kr} \bigg(
		 r^2 \frac{\partial^2 \psi_{m,n}}{\partial r^2}
	   + 2 r \frac{\partial \psi_{m,n}}{\partial r}
	   + k^2 r^2 \psi_{m,n} \bigg).
$$

Since \\(\psi_{m,n}\\) is separable, it satisfies Bessel's differential Eqn. 18.41.1. Coupled with the definition of \\(p^2\\) given by Eqn. 18.42.1, we obtain a simplified expression

$$ \tag{18.66}
	(\bm{N_{m,n}})\_r
	= \frac{n (n + 1)}{kr} \psi_{m,n}.
$$

The explicit form of the components can be obtained by substitution of Eqn. 18.44:

$$ \tag{18.67}
\begin{aligned}
	\frac{1}{\gamma_{m,n}} \bm{N_{m,n}}(\bm{r})
	&= n (n + 1) \frac{z_n(k r)}{k r} P_n^m(\cos{\theta}) e^{i m \phi} \bm{e_r}
	\cr
	&+ \bigg(\frac{z_n(k r)}{k r} + \frac{\partial z_n(k r)}{\partial (k r)} \bigg)
	   \frac{\partial P_n^m(\cos{\theta})}{\partial \theta} e^{i m \phi} \bm{e_{\theta}}
	\cr
	&+ i m \bigg(\frac{z_n(k r)}{k r} + \frac{\partial z_n(k r)}{\partial (k r)} \bigg)
	   \frac{P_n^m(\cos{\theta})}{\sin{\theta}} e^{i m \phi} \bm{e_{\phi}}.
\end{aligned}
$$

The expressions given above may be shortened by defining two new polar functions

$$ \tag{18.68}
	\pi_{m,n}(\theta)
	= m \frac{P_n^m(\cos{\theta})}{\sin{\theta}},
	\quad
	\tau_{m,n}(\theta)
	= \frac{\partial P_n^m(\cos{\theta})}{\partial \theta}
	= -\sin{\theta} \frac{\partial P_n^m(\cos{\theta})}{\partial (\cos{\theta})},
$$

that satisfy the modified Legendre's differential equation (cf. Eqn. 18.41.2 and 18.42)

$$ \tag{18.??}
	m \pi_{m,n}(\theta)
	= \frac{\partial \big[ \tau_{m,n}(\theta) \sin{\theta} \big] }{\partial \theta}
	+ n ( n + 1) P_n^m(\cos{\theta}) \sin{\theta},
$$

and possess the following orthogonality properties:

$$ \tag{18.69}
\begin{aligned}
	& \int_{0}^{\pi} \big( \pi_{m,n}(\theta) \tau_{m,l}(\theta)
	+ \tau_{m,n}(\theta) \pi_{m,l}(\theta) \big) \sin{\theta} \thinspace d\theta
	\cr
	&= m \int_{0}^{\pi} \frac{\partial \big[ P_n^m(\cos{\theta}) P_l^m(\cos{\theta}) \big]}{\partial \theta} \thinspace d\theta
	= 0,
\end{aligned}
$$

plus

$$ \tag{18.6?}
\begin{aligned}
	& \int_{0}^{\pi} \big( \pi_{m,n}(\theta) \tau_{m,l}(\theta)
	+ \tau_{m,n}(\theta) \pi_{m,l}(\theta) \big) \cos{\theta} \sin{\theta} \thinspace d\theta
	\cr
	&= m \int_{-1}^{1} P_n^m(x) P_l^m(x) \thinspace dx
	- m \int_{-1}^{1} \frac{\partial \big[x P_n^m(x) P_l^m(x) \big]}{\partial x} dx
	= \frac{2 m}{2n + 1} \frac{(n+m)!}{(n-m)!} \delta_{l,n},
\end{aligned}
$$

as well as

$$ \tag{18.70}
\begin{aligned}
	& \int_{0}^{\pi} \big( \pi_{m,n}(\theta) \pi_{m,l}(\theta)
	+ \tau_{m,n}(\theta) \tau_{m,l}(\theta) \big) \sin{\theta} \thinspace d\theta
	\cr
	&= \frac{1}{2} \int_{0}^{\pi} \big( m \pi_{m,n}(\theta) P^m_l(\cos{\theta}) + m P^m_n(\cos{\theta}) \pi_{m,l}(\theta) + 2 \tau_{m,n}(\theta) \tau_{m,l}(\theta) \sin{\theta} \big) \thinspace d\theta
	\cr
	&= \frac{1}{2} \int_{0}^{\pi} \frac{\partial}{\partial \theta} \left( \sin{\theta} \frac{\partial \big[ P_n^m(\cos{\theta}) P_l^m(\cos{\theta}) \big]}{\partial \theta} \right) d\theta
	\cr
	&+ \frac{n (n+1) + l (l+1)}{2} \int_{-1}^{1} P_n^m(x) P_l^m(x) \thinspace dx
	= \frac{2 n (n+1)}{2n + 1} \frac{(n+m)!}{(n-m)!} \delta_{l,n},
\end{aligned}
$$

and, finally,

$$ \tag{18.7?}
\begin{aligned}
	& \int_{0}^{\pi} \big( \pi_{m,n}(\theta) \pi_{m,l}(\theta)
	+ \tau_{m,n}(\theta) \tau_{m,l}(\theta) \big) \cos{\theta} \sin{\theta} \thinspace d\theta
	\cr
	&= \frac{1}{2} \int_{0}^{\pi} \big( m \pi_{m,n}(\theta) P^m_l(\cos{\theta}) + m \pi_{m,l}(\theta) P^m_n(\cos{\theta}) + 2 \tau_{m,n}(\theta) \tau_{m,l}(\theta) \sin{\theta} \big) \cos{\theta} \thinspace d\theta
	\cr
	&= \frac{1}{2} \int_{0}^{\pi}
		\left(
			\frac{\partial \big[ \tau_{m,n}(\theta) \sin{\theta} \big] }{\partial \theta} P^m_l(\cos{\theta}) \cos{\theta} +
			P^m_n(\cos{\theta}) \frac{\partial \big[ \tau_{m,l}(\theta) \sin{\theta} \big] }{\partial \theta} \cos{\theta}
		\right) d\theta
	\cr
	&+ \frac{1}{2} \int_{0}^{\pi} 2 \tau_{m,n}(\theta) \tau_{m,l}(\theta) \cos{\theta} \sin{\theta} \thinspace d\theta
	+ \frac{n (n+1) + l (l+1)}{2} \int_{-1}^{1} P_n^m(x) x P_l^m(x) \thinspace dx
	\cr
	&= \frac{1}{2} \frac{(l - m + 1)}{(2 l + 1)} \int_{0}^{\pi} \frac{\partial}{\partial \theta} \left( \sin{\theta} \frac{\partial \big[ P_n^m(\cos{\theta}) P_{l+1}^m(\cos{\theta}) \big]}{\partial \theta} \right) d\theta
	\cr
	&+ \frac{1}{2} \frac{(l + m)}{(2 l + 1)} \int_{0}^{\pi} \frac{\partial}{\partial \theta} \left( \sin{\theta} \frac{\partial \big[ P_n^m(\cos{\theta}) P_{l-1}^m(\cos{\theta}) \big]}{\partial \theta} \right) d\theta
	\cr
	&+ \int_{0}^{\pi}
		\left( P^m_n(\cos{\theta}) \tau_{m,l}(\theta)
		+ \tau_{m,n}(\theta) P_l^m(\cos{\theta}) \right)
		\left( 1 - \cos^2{\theta} \right) d\theta
	\cr
	&+ \frac{n (n+1) + l (l+1) + 2}{2} \int_{-1}^{1} P_n^m(x) x P_l^m(x) \thinspace dx
	\cr
	&= \frac{n (n+1) + l (l+1)}{2} \int_{-1}^{1} P_n^m(x)
		\left(
			\frac{(l - m + 1)}{(2 l + 1)} P_{l+1}^m(x) +
			\frac{(l + m)}{(2 l + 1)} P_{l-1}^m(x)
		\right) dx
	\cr
	&- \frac{(l - m + 1)}{(2 l + 1)} \frac{1}{m} \int_{0}^{\pi}
		\left(
			\pi_{m,n}(\theta) \tau_{m,l+1}(\theta)
			+ \tau_{m,n}(\theta) \pi_{m,l+1}(\theta)
		\right)
		\cos{\theta} \sin{\theta} \thinspace d\theta
	\cr
	&- \frac{(l + m)}{(2 l + 1)} \frac{1}{m} \int_{0}^{\pi}
		\left(
			\pi_{m,n}(\theta) \tau_{m,l-1}(\theta)
			+ \tau_{m,n}(\theta) \pi_{m,l-1}(\theta)
		\right)
		\cos{\theta} \sin{\theta} \thinspace d\theta
	\cr
	&= \frac{2}{2n + 1} \frac{(n+m)!}{(n-m)!} \left(
	\frac{(n^2-1) (n-m)}{2 n-1} \delta_{l+1,n}
	+ \frac{n (n+2) (m+n+1)}{2 n+3} \delta_{l-1,n} \right),
\end{aligned}
$$

where, in the addition to Legendre's differential equation (Eqn. 18.??), we utilized the orthogonality property of Legendre's polynomials (Eqn. 18.56), the recurrence relation (Eqn. 18.5?), and the fact that the value of the associated Legendre polynomials is zero for \\(\cos{\theta} = \pm 1\\) unless \\(m = 0\\).

To save some space, let us introduce a compact notation for the product derivative of the spherical Bessel function:

$$ \tag{18.??}
\begin{aligned}
	  \frac{z_n(x)}{x} + \frac{\partial z_n(x)}{\partial x}
	= \frac{1}{x} \frac{\partial \big[ x z_n(x) \big]}{\partial x}
	= \frac{\big[ x z_n(x) \big]'}{x}.
\end{aligned}
$$

After substitution into Eqn. 18.62, 18.63, and 18.67, we immediately obtain the reduced expressions of the vector spherical wave functions:

$$ \tag{18.71}
\begin{aligned}
	\bm{L_{m,n}}(\bm{r})
	&= \gamma_{m,n} e^{i m \phi} \bigg( \frac{\partial z_n(k r)}{\partial (k r)} P_n^m(\cos{\theta}) \bm{e_r}
	 + \frac{z_n(k r)}{k r}
	 \big( \tau_{m,n}(\theta) \bm{e_{\theta}} + i \pi_{m,n}(\theta) \bm{e_{\phi}} \big) \bigg),
	\cr
	\bm{M_{m,n}}(\bm{r})
	&= \gamma_{m,n} e^{i m \phi}  z_n(k r)
	\big( i \pi_{m,n}(\theta) \bm{e_{\theta}} - \tau_{m,n}(\theta) \bm{e_{\phi}} \big),
	\cr
	\bm{N_{m,n}}(\bm{r})
	&= \gamma_{m,n} e^{i m \phi} \bigg(
	n (n + 1) \frac{z_n(k r)}{k r} P_n^m(\cos{\theta}) \bm{e_r}
	+ \frac{\big[ k r z_n(k r) \big]'}{k r}
	\big( \tau_{m,n}(\theta) \bm{e_{\theta}} + i \pi_{m,n}(\theta) \bm{e_{\phi}} \big) \bigg).
\end{aligned}
$$

You may notice that the expressions given above exhibit a certain pattern. That is not a coincidence. Indeed, the vector spherical wave functions are directly related to the set of vectors

$$ \tag{18.72}
\begin{aligned}
	& \bm{\Upsilon_{m,n}}(\theta, \phi)
	= Y^m_n \bm{e_r} = \gamma_{m,n} e^{i m \phi} P_n^m(\cos{\theta}) \bm{e_r},
	\cr
	& \bm{\Phi_{m,n}}(\theta, \phi)
	= \nabla Y^m_n \times \bm{r}
	= \gamma_{m,n} e^{i m \phi} \big( i \pi_{m,n}(\theta) \bm{e_{\theta}} - \tau_{m,n}(\theta) \bm{e_{\phi}} \big)
	= \bm{\Psi_{m,n}} \times \bm{e_r},
	\cr
	& \bm{\Psi_{m,n}}(\theta, \phi)
	= r \nabla Y^m_n
	= \gamma_{m,n} e^{i m \phi} \big( \tau_{m,n}(\theta) \bm{e_{\theta}} + i \pi_{m,n}(\theta) \bm{e_{\phi}} \big)
	= \bm{e_r} \times \bm{\Phi_{m,n}},
\end{aligned}
$$

called [vector spherical harmonics](https://en.wikipedia.org/wiki/Vector_spherical_harmonics#Alternative_definition). They are an extension of the scalar spherical harmonics (Eqn. 18.58) to vectors fields on the surface of a sphere. Like the scalar spherical harmonics, they form a complete set of orthogonal basis functions \[[19](#references) (ch. 13.3), [28](#references) (ch. 4.2)\]. Unlike the vector spherical wave functions, they do not depend on the radial distance \\(r\\).

Most properties of the vector spherical harmonics are a direct consequence of the fact that they are built on the scalar spherical harmonics combined with the radial basis vector \\(\bm{e_r}\\). For instance, the symmetry property of Eqn. 18.60 leads to

$$ \tag{18.73}
	\bm{\Upsilon_{-m,n}}
	= (-1)^m \bm{\Upsilon_{m,n}^{\*}},
	\quad
	\bm{\Phi_{-m,n}}
	= (-1)^m \bm{\Phi_{m,n}^{\*}},
	\quad
	\bm{\Psi_{-m,n}}
	= (-1)^m \bm{\Psi_{m,n}^{\*}}.
$$

The particular manner of dependence on a single vector \\(\bm{e_r}\\) leads to orthogonality in the 3-dimensional space

$$ \tag{18.74}
\begin{aligned}
	\bm{\Upsilon_{s,t}}      \cdot \bm{\Phi_{m,n}} =
	\bm{\Upsilon_{s,t}^{\*}} \cdot \bm{\Phi_{m,n}} = 0,
	\cr
	\bm{\Upsilon_{s,t}}      \cdot \bm{\Psi_{m,n}} =
	\bm{\Upsilon_{s,t}^{\*}} \cdot \bm{\Psi_{m,n}} = 0,
	\cr
	\bm{\Phi_{m,n}} \cdot \bm{\Psi_{m,n}} = 0,
\end{aligned}
$$

for arbitrary values of \\(m,n,s,t\\).

Furthermore, let us demonstrate that the vector spherical harmonics are also orthogonal in the [Hilbert space](https://en.wikipedia.org/wiki/Hilbert_space), which (very roughly) means that the vectors with different indices are orthogonal on the surface of a sphere.

Let us begin by considering the double integral

$$ \tag{18.75}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Upsilon_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Upsilon_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \gamma_{s,t} \gamma_{m,n}
		\int_{0}^{\pi} P_t^s(\cos{\theta}) P_n^m(\cos{\theta}) \sin{\theta} \thinspace d\theta
		\int_{-\pi}^{\pi} e^{i (m - s) \phi} \thinspace d\phi.
\end{aligned}
$$

Analysis shows that the initial expression can be separated into a product of several terms. We may recognize from Eqn. 4.6 that the integral over \\(\phi\\) is nothing other than the rescaled Kronecker delta function \\(\delta_{m,s}\\). Thus, the value of the double integral is zero unless \\(m = s\\). Similarly, according to Eqn. 18.56, the integral over \\(\theta\\) is proportional to another Kronecker delta function \\(\delta_{t,n}\\). A simple calculation shows that the various constants cancel out, and we are left with

$$ \tag{18.76}
\begin{aligned}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Upsilon_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Upsilon_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	= \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

Similarly, from Eqn. 4.6 and 18.70, it follows that

$$ \tag{18.77}
\begin{aligned}
	& \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Phi_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Phi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Psi_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Psi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \gamma_{s,t} \gamma_{m,n}
		\int_{0}^{\pi}
		\big( \pi_{s,t}(\theta) \pi_{m,n}(\theta) + \tau_{s,t}(\theta) \tau_{m,n}(\theta) \big) \sin{\theta} \thinspace d\theta
		\int_{-\pi}^{\pi} e^{i (m - s) \phi} \thinspace d\phi
	\cr
	&= n (n + 1) \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

We can also show that the all mixed products are zero. According to Eqn. 18.74,

$$ \tag{18.78}
\begin{aligned}
	& \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Upsilon_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Phi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Upsilon_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Psi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	= 0.
\end{aligned}
$$

For the last pair of vectors, the proof involves Eqn. 18.69 instead:

$$ \tag{18.79}
\begin{aligned}
	& \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Phi_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Psi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= -i \gamma_{s,t} \gamma_{m,n}
		\int_{0}^{\pi}
		\big( \pi_{s,t}(\theta) \tau_{m,n}(\theta) + \tau_{s,t}(\theta) \pi_{m,n}(\theta) \big) \sin{\theta} \thinspace d\theta
		\int_{-\pi}^{\pi} e^{i (m - s) \phi} \thinspace d\phi
	= 0.
\end{aligned}
$$

Let us now return to the expression of the vector spherical wave functions given in Eqn. 18.71. They can be expressed as a weighted combination of the vector spherical harmonics, effectively extending them to the entire 3-dimensional space:

$$ \tag{18.80}
\begin{aligned}
	\bm{L_{m,n}}(\bm{r})
	&= \frac{\partial z_n(k r)}{\partial (k r)} \bm{\Upsilon_{m,n}}(\theta, \phi)
	 + \frac{z_n(k r)}{k r} \bm{\Psi_{m,n}}(\theta, \phi),
	\cr
	\bm{M_{m,n}}(\bm{r})
	&= z_n(k r) \bm{\Phi_{m,n}}(\theta, \phi),
	\cr
	\bm{N_{m,n}}(\bm{r})
	&= n (n + 1) \frac{z_n(k r)}{k r} \bm{\Upsilon_{m,n}}(\theta, \phi)
	 + \frac{\big[ k r z_n(k r) \big]'}{k r} \bm{\Psi_{m,n}}(\theta, \phi).
\end{aligned}
$$

By simplifying Eqn. 18.72, we may show that, in general, \\(\bm{\Upsilon_{0,0}} \neq 0\\), while \\(\bm{\Phi_{0,0}} = \bm{\Psi_{0,0}} = 0\\). Substitution into Eqn. 18.80 leads to \\(\bm{L_{0,0}} \neq 0\\) and \\(\bm{M_{0,0}} = \bm{N_{0,0}} = 0\\). Consequently, the series expansions that exclude the \\(\bm{L_{m,n}}\\) vectors may begin at \\(n = 1\\).

Most properties of the vector spherical harmonics are, to some degree, shared by the vector spherical wave functions. For instance, they exhibit the symmetry property that follows from Eqn. 18.73:

$$ \tag{18.81}
\begin{aligned}
	\bm{L_{-m,n}}(\bm{r})
	&= (-1)^m \bigg( \frac{\partial z_n(k r)}{\partial (k r)} \big[ \bm{\Upsilon_{m,n}}(\theta, \phi) \big]^{\*}
	 + \frac{z_n(k r)}{k r} \big[ \bm{\Psi_{m,n}}(\theta, \phi) \big]^{\*} \bigg),
	\cr
	\bm{M_{-m,n}}(\bm{r})
	&= (-1)^m \bigg( z_n(k r) \big[ \bm{\Phi_{m,n}}(\theta, \phi) \big]^{\*} \bigg),
	\cr
	\bm{N_{-m,n}}(\bm{r})
	&= (-1)^m \bigg( n (n + 1) \frac{z_n(k r)}{k r} \big[ \bm{\Upsilon_{m,n}}(\theta, \phi) \big]^{\*}
	 + \frac{\big[ k r z_n(k r) \big]'}{k r} \big[ \bm{\Psi_{m,n}}(\theta, \phi) \big]^{\*} \bigg).
\end{aligned}
$$

For real values of \\(k\\), and if the spherical Bessel function is real (which excludes the Hankel functions), the expressions given above can be simplified to

$$ \tag{18.8x}
	\bm{L_{-m,n}}
	= (-1)^m \bm{L_{m,n}^{\*}},
	\enspace
	\bm{M_{-m,n}}
	= (-1)^m \bm{M_{m,n}^{\*}},
	\enspace
	\bm{N_{-m,n}}
	= (-1)^m \bm{N_{m,n}^{\*}}.
	\enspace
	(z_n \in \mathcal{R})
$$

According to Eqn. 18.22, \\(\bm{L_{m,n}}\\) and \\(\bm{M_{m,n}}\\) are mutually orthogonal in the  3-dimensional space. Eqn. 18.80 allows us to determine the state of affairs for the remaining two pairs of vectors. Since, according to Eqn. 18.74, the spherical harmonic vectors are orthogonal,

$$ \tag{18.82}
	\bm{M_{m,n}} \cdot \bm{N_{m,n}} = 0.
$$

The relationship between \\(\bm{L_{m,n}}\\) and \\(\bm{N_{m,n}}\\) is more complicated:

$$ \tag{18.83}
\small
\begin{aligned}
	\bm{L_{m,n}} \cdot \bm{N_{m,n}}
	&= \frac{z_n(k r)}{k r} \bigg( n (n + 1) \frac{\partial z_n(k r)}{\partial (k r)} \big[ \bm{\Upsilon_{m,n}}(\theta, \phi) \big]^2
	+ \frac{\big[ k r z_n(k r) \big]'}{k r} \big[ \bm{\Psi_{m,n}}(\theta, \phi) \big]^2 \bigg).
\end{aligned}
$$

Since \\(\bm{\Psi_{0,0}} = 0\\), it trivially follows that \\(\bm{L_{0,0}} \cdot \bm{N_{0,0}} = 0\\). For arbitrary \\(m\\) and \\(n\\), all that can be said is that \\(|\bm{L_{m,n}} \cdot \bm{N_{m,n}}|/(|\bm{L_{m,n}}||\bm{N_{m,n}}|) \leq 1\\), and the two vectors periodically become aligned for certain isolated values of \\(\theta\\).

{{< figure src="/img/l_dot_n.svg" caption="*Figure N: \\((\bm{L\_{0,n}^{(1)}} \cdot \bm{N\_{0,n}^{(1)}})/(|\bm{L\_{0,n}^{(1)}}| |\bm{N\_{0,n}^{(1)}}|)\\) of order \\(m=0\\) and degree \\(n=1\\) (blue), \\(n=2\\) (orange), \\(n=3\\) (green), defined in terms of the spherical Bessel function of the first kind, as a function of the zenith angle \\(\theta\\), with fixed values of \\(k = r = 1, \thinspace \thinspace \phi = 0\\).*" >}}

In addition to partial orthogonality in the real 3-dimensional space, we can show that the vector spherical wave functions are also partially orthogonal in the Hilbert space. In order to do that, we shall utilize the definition given by Eqn. 18.80 and the orthogonality relations of Eqn. 18.74:

Noting that, according to Eqn. 18.74, Let us evaluate the integral taken over the surface of a sphere.

$$ \tag{18.84}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{L_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&=  \bigg[ \frac{\partial z_t(k r)}{\partial (k r)} \bigg]^{\*} \frac{\partial z_n(k r)}{\partial (k r)}
		\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Upsilon_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Upsilon_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&+  \frac{\big[ z_t(k r) \big]^{\*} z_n(k r)}{|k r|^2}
		\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Psi_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Psi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi.
\end{aligned}
$$

Application of the orthogonality properties given by Eqn. 18.76 and 18.77 immediately yields

$$ \tag{18.85}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{L_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \Bigg( \bigg| \frac{\partial z_n(k r)}{\partial (k r)} \bigg|^2
		+ n (n+1) \bigg| \frac{z_n(k r)}{k r} \bigg|^2 \Bigg) \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

The derivative can be eliminated by considering the recurrence relations of Eqn. 18.50:

$$ \tag{18.86}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{L_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \frac{1}{(2 n + 1)^2} \Big(
		\big| n z_{n-1}(k r) - (n + 1) z_{n+1}(k r) \big|^2
		+ n (n+1) \big| z_{n-1}(k r) + z_{n+1}(k r) \big|^2
	\Big) \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

Further simplification can be only achieved by assuming that no complex numbers are involved \\((z_n \in \mathcal{R})\\):

$$ \tag{18.87}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{L_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \frac{1}{2 n + 1} \Big( n \big[ z_{n-1}(k r) \big]^2 + (n + 1) \big[ z_{n+1}(k r) \big]^2 \Big) \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

The same formulae lead to

$$ \tag{18.88}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{M_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	= n (n+1) \big| z_n(k r) \big|^2 \delta_{m,s} \delta_{t,n}
$$

and

$$ \tag{18.89}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{N_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= n (n+1) \Bigg(
		\Bigg| \frac{\big[ k r z_n(k r) \big]'}{k r} \Bigg|^2
		+ n (n+1) \bigg| \frac{z_n(k r)}{k r} \bigg|^2
		\Bigg) \delta_{m,s} \delta_{t,n}
	\cr
	&= \frac{n (n+1)}{(2 n + 1)^2} \Big(
		\big| (n + 1) z_{n-1}(k r) - n z_{n+1}(k r) \big|^2
		+ n (n+1) \big| z_{n-1}(k r) + z_{n+1}(k r) \big|^2
		\Big) \delta_{m,s} \delta_{t,n},
\end{aligned}
$$

which, for real numbers \\((z_n \in \mathcal{R})\\), can be reduced to

$$ \tag{18.90}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{N_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \frac{n (n+1)}{(2 n + 1)} \Big(
		(n + 1) \big[ z_{n-1}(k r) \big]^2 + n \big[ z_{n+1}(k r) \big]^2
		\Big) \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

We have just shown that the vectors of the same type (but of different orders) are orthogonal in the Hilbert space. What remains is to determine whether that holds for combinations of different types of vectors as well.

We begin by considering the pairs of \\(\bm{L_{m,n}}\\) and \\(\bm{M_{m,n}}\\):

$$ \tag{18.91}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{M_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \bigg[ \frac{\partial z_t(k r)}{\partial (k r)} \bigg]^{\*} z_n(k r) \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Upsilon_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Phi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&+ \bigg[ \frac{z_t(k r)}{k r} \bigg]^{\*} z_n(k r) \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{\Psi_{s,t}}(\theta, \phi) \big]^{\*} \cdot \bm{\Phi_{m,n}}(\theta, \phi) \sin{\theta} \thinspace d\theta d\phi = 0,
\end{aligned}
$$

which directly follows from orthogonality of the vector spherical harmonics demonstrated by Eqn. 18.78 and 18.79.

An identical calculation leads to

$$ \tag{18.92}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{N_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi = 0.
$$

Since, according to Eqn. 18.83, \\(\bm{L_{m,n}}\\) and \\(\bm{N_{m,n}}\\) are not fully orthogonal in the 3-dimensional space, we should also expect some complications in the Hilbert space. From Eqn. 18.74, 18.76 and 18.77 it follows that

$$ \tag{18.93}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{N_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= n (n + 1) \Bigg( \bigg[ \frac{\partial z_n(k r)}{\partial (k r)} \bigg]^{\*} \frac{z_n(k r)}{k r}
	+ \bigg[ \frac{z_n(k r)}{k r} \bigg]^{\*} \frac{\big[ k r z_n(k r) \big]'}{k r} \Bigg) \delta_{m,s} \delta_{t,n}.
\end{aligned}
$$

In the absence of complex numbers \\((z_n \in \mathcal{R})\\), Eqn. 18.93 has a simpler expression

$$ \tag{18.94}
\begin{aligned}
	&\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{N_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= n (n + 1) \frac{z_n(k r)}{k r}
	\bigg( \frac{z_n(k r)}{k r} + 2 \frac{\partial z_n(k r)}{\partial (k r)} \bigg) \delta_{m,s} \delta_{t,n}
	\cr
	&= \frac{n (n + 1)}{2 n + 1}
	\Big( \big[ z_{n-1}(k r) \big]^2 - \big[ z_{n+1}(k r) \big]^2 \Big) \delta_{m,s} \delta_{t,n},
\end{aligned}
$$

where we once again utilized the identities of Eqn. 18.50.

Given the asymptotic behavior of the spherical Bessel functions given by Eqn. 18.52, it follows that

$$ \tag{18.95}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{L_{s,t}}(\bm{r}) \big]^{\*} \cdot \bm{N_{m,n}}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\simeq 0.
$$

For our application, the partial orthogonality between \\(\bm{L_{m,n}}\\) and \\(\bm{N_{m,n}}\\) is not a real concern. According to Eqn. 18.36 and 18.37, the electric and the magnetic fields can be expressed solely in terms of \\(\bm{M_{m,n}}\\) and \\(\bm{N_{m,n}}\\), which are mutually orthogonal, as has been shown by Eqn. 18.82 and 18.92.

Now, let us return to the scattering problem. In general, we may arrive at the solution by determining the values of the set of coefficients of the vector spherical wave series expansion given by Eqn. 18.36. In order to obtain these coefficients, we must consider three distinct types of electromagnetic fields: the incident field produced by an external source, the scattered field in the radiation zone of the particle, and the total field induced inside the sphere. The reason for this kind of decomposition will become apparent shortly.

Let us begin by considering the expansion of the incident field:

$$ \tag{18.96}
	\bm{E_i}(\bm{r}, \omega)
	= \sum_{n=1}^{\infin} \sum_{m=-n}^{n} \big( c_{m,n}(\omega) \bm{M_{m,n}^{(1)}}(\bm{r}, \omega) + d_{m,n}(\omega) \bm{N_{m,n}^{(1)}}(\bm{r}, \omega) \big).
$$

The superscript \\((1)\\) indicates that we have made the choice of a spherical Bessel function of the first kind

$$ \tag{18.97}
	z_n^{(1)}(k r) = j_n(k r)
$$

motivated by its non-singular behavior at the origin (cf. Fig. N). This property makes it suitable for expressing a smooth, finite-valued electromagnetic field.

Evidently, the analytic expression of the incident field must be known a priori. This fact permits us to determine the values of the coefficients \\(c_{m,n}\\) and \\(d_{m,n}\\) by combining Eqn. 18.96 with 18.88 and 18.89, respectively:

$$ \tag{18.98}
\small
\begin{aligned}
	c_{m,n}
	&= \frac{ \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi }
	{ \int_{-\pi}^{\pi} \int_{0}^{\pi} \big| \bm{M_{m,n}^{(1)}}(\bm{r}) \big|^2 \sin{\theta} \thinspace d\theta d\phi }
	= \frac{ \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi }
	{ n (n+1) \big| j_n(k r) \big|^2 },
	\cr
	d_{m,n}
	&= \frac{ \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi }
	{ \int_{-\pi}^{\pi} \int_{0}^{\pi} \big| \bm{N_{m,n}^{(1)}}(\bm{r}) \big|^2 \sin{\theta} \thinspace d\theta d\phi }
	= \frac{ \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi }
	{ n (n+1) \Big(
		\big| [k r j_n(k r)]' / (k r) \big|^2
		+ n (n+1) \big| j_n(k r) / (k r) \big|^2
		\Big) }.
\end{aligned}
$$

Eqn. 18.96 and 18.98 have a simple geometric interpretation. If we define the *Hilbert space norm*

$$ \tag{18.99}
	\mathcal{N_h}(\bm{V})
	= \sqrt{\mathcal{P_h}(\bm{V}, \bm{V})},
$$

in terms of the *Hilbert space projection operator*

$$ \tag{18.100}
	\mathcal{P_h}(\bm{V}, \bm{W})
	= \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{W}(\bm{r}) \big]^{\*} \cdot \bm{V}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi,
$$

then Eqn. 18.98 can be interpreted the projection into the Hilbert space,

$$ \tag{18.101}
	c_{m,n} = \frac{ \mathcal{P_h} \big( \bm{E_i}, \bm{M_{m,n}^{(1)}} \big) }{ \mathcal{N_h}^2 \big( \bm{M_{m,n}^{(1)}} \big) },
	\quad
	d_{m,n} = \frac{ \mathcal{P_h} \big( \bm{E_i}, \bm{N_{m,n}^{(1)}} \big) }{ \mathcal{N_h}^2 \big( \bm{N_{m,n}^{(1)}} \big) },
$$

with Eqn. 18.96 performing the reconstruction:

$$ \tag{18.102}
\small
\bm{E_i}
= \sum_{n=1}^{\infin} \sum_{m=-n}^{n} \Bigg(
	\mathcal{P_h} \Bigg( \bm{E_i}, \frac{ \bm{M_{m,n}^{(1)}} }{ \mathcal{N_h} \big( \bm{M_{m,n}^{(1)}} \big) } \Bigg) \frac{ \bm{M_{m,n}^{(1)}} }{ \mathcal{N_h} \big( \bm{M_{m,n}^{(1)}} \big) } +
	\mathcal{P_h} \Bigg( \bm{E_i}, \frac{ \bm{N_{m,n}^{(1)}} }{ \mathcal{N_h} \big( \bm{N_{m,n}^{(1)}} \big) } \Bigg) \frac{ \bm{N_{m,n}^{(1)}} }{ \mathcal{N_h} \big( \bm{N_{m,n}^{(1)}} \big) } \Bigg).
$$

Let us further assume that the incident field can be represented by a plane wave. This is a valid description of an extended source located in the radiation zone of a small particle; a more general source could be modeled as a superposition of plane waves.

If the region of space is filled with a linear, isotropic, homogeneous, source-free medium, the expression of the incident vector plane wave is given by Eqn. 7.2:

$$ \tag{18.103}
	\bm{E_i}(\bm{r}, \omega) e^{- i \omega t}
	= \bm{E_0}(\bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i}) - i \omega t}.
$$

In spherical coordinates, the explicit representation of \\(\bm{r}\\) is

$$ \tag{18.104}
	\bm{r} = \lbrace r, \theta, \phi \rbrace.
$$

We may take advantage of the symmetry of the problem by aligning \\(\bm{n_i}\\) with the \\(z\\)-axis (cf. Fig. N). Then,

$$ \tag{18.105}
	\bm{r} \cdot \bm{e_z} = r \cos{\theta}.
$$

Further simplification can be achieved by recalling that an electromagnetic plane wave is transverse with respect to its direction of propagation (as shown by Eqn. 7.18 and 7.23). Therefore, in Cartesian coordinates, the polarization phasor \\(\bm{E_0}\\) of a plane wave can be completely described by two complex numbers \\(E_X\\) and \\(E_Y\\):

$$ \tag{18.106}
	\bm{E_0}(\bm{e_z}, \omega)
	= E_X(\omega) \bm{e_x} + E_Y(\omega) \bm{e_y}.
$$

Eqn. 18.106 does not explicitly depend on the point of reference \\(\bm{r}\\). However, the spherical coordinate basis vectors [vary in space](https://en.wikipedia.org/wiki/Vector_fields_in_cylindrical_and_spherical_coordinates#Spherical_coordinate_system):

$$ \tag{18.107}
\begin{aligned}
	\bm{e_x} &= \sin{\theta} \cos{\phi} \thinspace \bm{e_{r}}
			  + \cos{\theta} \cos{\phi} \thinspace \bm{e_{\theta}}
			  - \sin{\phi} \thinspace \bm{e_{\phi}},
	\cr
	\bm{e_y} &= \sin{\theta} \sin{\phi} \thinspace \bm{e_{r}}
			  + \cos{\theta} \sin{\phi} \thinspace \bm{e_{\theta}}
			  + \cos{\phi} \thinspace \bm{e_{\phi}},
	\cr
	\bm{e_z} &= \cos{\theta} \thinspace \bm{e_{r}}
			  - \sin{\theta} \thinspace \bm{e_{\theta}}.
	\cr
\end{aligned}
$$

Eqn. 18.103-18.107 give a complete description of an electromagnetic plane wave in the spherical coordinates. We may now determine the values of the series coefficients (using Eqn. 18.98) by projecting the vector plane wave onto the vector spherical wave functions.

Let us first evaluate

$$ \tag{18.114}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi.
$$

Since, according to Eqn. 18.107, the expression of the incident wave only contains first-order harmonic functions of \\(\phi\\), orthogonality of the Fourier basis makes the integral of Eqn. 18.114 vanish unless \\(m = \pm 1\\). Furthermore, the definition of \\(\bm{M_{m,n}}\\) (given by Eqn. 18.63) omits the radial basis vector, so the latter does not require further consideration. Therefore,

$$ \tag{18.115}
\begin{aligned}
	& \frac{-1}{ \gamma_{m,n} \big[ j_n(k r) \big]^{\*} }\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= E_X \int_{-\pi}^{\pi} \int_{0}^{\pi} e^{-i m \phi + i k r \cos{\theta}} \bigg(
		i m \frac{P_n^m(\cos{\theta})}{\sin{\theta}} \cos{\theta} \cos{\phi}
		- \frac{\partial P_n^m(\cos{\theta})}{\partial \theta} \sin{\phi}
	\bigg) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&+ E_Y \int_{-\pi}^{\pi} \int_{0}^{\pi} e^{-i m \phi + i k r \cos{\theta}} \bigg(
		i m \frac{P_n^m(\cos{\theta})}{\sin{\theta}} \cos{\theta} \sin{\phi}
		+ \frac{\partial P_n^m(\cos{\theta})}{\partial \theta} \cos{\phi}
	\bigg) \sin{\theta} \thinspace d\theta d\phi.
\end{aligned}
$$

Each term of the sum given above can be factored into a product of the \\(r\\), \\(\theta\\), \\(\phi\\) components. Using the identities

$$ \tag{18.116}
	\int_{-\pi}^{\pi} \sin{\phi} \thinspace e^{\pm i \phi} \thinspace d\phi = \pm i \pi,
	\quad
	\int_{-\pi}^{\pi} \cos{\phi} \thinspace e^{\pm i \phi} \thinspace d\phi = \pi,
$$

the integrals taken over the \\(\phi\\) angle can be evaluated analytically. Eqn. 18.115 is thus reduced to

$$ \tag{18.117}
\begin{aligned}
	& \frac{-1}{ \gamma_{m,n} \big[ j_n(k r) \big]^{\*} }\int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \delta_{m,\pm 1} (i m E_X + E_Y) \pi \int_{0}^{\pi} e^{i k r \cos{\theta}} \bigg(
		  \frac{P_n^m(\cos{\theta})}{\sin{\theta}} \cos{\theta}
		+ \frac{\partial P_n^m(\cos{\theta})}{\partial \theta}
	\bigg) \sin{\theta} \thinspace d\theta.
\end{aligned}
$$

If \\(m = \pm 1\\), then, according to Eqn. 18.47 and 18.55, the associated Legendre polynomials are related to the unassociated ones in a very simple manner:

$$ \tag{18.118}
	P_n^{1}(\cos{\theta}) = \frac{\partial}{\partial \theta} P_n(\cos{\theta}),
	\quad
	P_n^{-1}(\cos{\theta}) = \frac{-1}{n (n + 1)} \frac{\partial}{\partial \theta} P_n(\cos{\theta}).
$$

Once we transform the expression found in Eqn. 18.117,

$$ \tag{18.119}
\begin{aligned}
	  \bigg( \frac{P_n^1(\cos{\theta})}{\sin{\theta}} \cos{\theta}
	+ \frac{\partial P_n^1(\cos{\theta})}{\partial \theta} \bigg) \sin{\theta}
	= \bigg( \cos{\theta} \frac{\partial}{\partial \theta}
	+ \sin{\theta} \frac{\partial^2}{\partial \theta^2} \bigg) P_n(\cos{\theta}).
\end{aligned}
$$

we discover that it directly maps onto Legendre's differential Eqn. 18.41.2 (with the constants given by Eqn. 18.42):

$$ \tag{18.120}
\begin{aligned}
	  \bigg( \cos{\theta} \frac{\partial}{\partial \theta}
	+ \sin{\theta} \frac{\partial^2}{\partial \theta^2} \bigg) P_n(\cos{\theta})
	= -n (n+1) P_n(\cos{\theta}) \sin{\theta}.
\end{aligned}
$$

Eqn. 18.119 and 18.120, coupled with [Gegenbauer's representation](https://dlmf.nist.gov/10.54) of the spherical Bessel function of the first kind \[[Watson](#references) (ch. 3.32)\]

$$ \tag{18.1??}
	j_n(x) = \frac{i^{-n}}{2}
	\int_{0}^{\pi} e^{i x \cos{\theta}} P_n(\cos{\theta}) \sin{\theta} \thinspace d\theta,
$$

allow us to analytically evaluate the integral found in Eqn. 18.117:

$$ \tag{18.121}
\begin{aligned}
	& \int_{0}^{\pi} e^{i k r \cos{\theta}} \bigg(
		  \frac{P_n^1(\cos{\theta})}{\sin{\theta}} \cos{\theta}
		+ \frac{\partial P_n^1(\cos{\theta})}{\partial \theta}
	\bigg) \sin{\theta} \thinspace d\theta
	\cr
	&= -n (n+1) \int_{0}^{\pi} e^{i k r \cos{\theta}} P_n(\cos{\theta}) \sin{\theta} \thinspace d\theta = -2 i^n n (n+1) j_n(k r).
\end{aligned}
$$

We may extend our result to the \\(m = -1\\) case by examining Eqn. 18.58 and 18.118.2 and noting that

$$ \tag{18.122}
\begin{aligned}
	\gamma_{1,n} = \frac{\gamma_{-1,n}}{n (n + 1)}.
\end{aligned}
$$

Furthermore, the minus sign found in Eqn. 18.118.2 can be dealt with by multiplying both the positive and the negative solutions by \\(m\\). Thus, with the help of Eqn. 18.121-18.122, the unified value of Eqn. 18.117 is

$$ \tag{18.123}
\begin{aligned}
	& \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{M_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \delta_{m,\pm 1} (i E_X + m E_Y) 2 \pi i^n \gamma_{1,n} n (n+1) \big| j_n(k r) \big|^2.
\end{aligned}
$$

Comparison with Eqn. 18.98.1 yields the value of the first expansion coefficient

$$ \tag{18.124}
	c_{m,n}
	= \delta_{m,\pm 1} (i E_X + m E_Y) 2 \pi i^n \gamma_{1,n}.
$$

The value of the second coefficient can be obtained in a similar manner (except, this time, the radial component can no longer be ignored). Substitution of Eqn. 18.67 and 18.103-18.107 into 18.98.2 produces the following expression:

$$ \tag{18.125}
\small
\begin{aligned}
	& \frac{1}{\gamma_{m,n}} \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= E_X \Bigg[ \frac{\big[ k r j_n(k r) \big]'}{k r} \Bigg]^{\*}
		\int_{-\pi}^{\pi} \int_{0}^{\pi} e^{-i m \phi + i k r \cos{\theta}} \bigg(
		\frac{\partial P_n^m(\cos{\theta})}{\partial \theta} \cos{\theta} \cos{\phi}
		+ i m \frac{P_n^m(\cos{\theta})}{\sin{\theta}} \sin{\phi}
	\bigg) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&+ E_X \bigg[ \frac{j_n(k r)}{k r} \bigg]^{\*} n (n + 1)
		\int_{-\pi}^{\pi} \int_{0}^{\pi} e^{-i m \phi + i k r \cos{\theta}}
		P_n^m(\cos{\theta}) \sin^2{\theta} \cos{\phi}
	\thinspace d\theta d\phi
	\cr
	&+ E_Y \bigg[ \frac{j_n(k r)}{k r} \bigg]^{\*} n (n + 1)
		\int_{-\pi}^{\pi} \int_{0}^{\pi} e^{-i m \phi + i k r \cos{\theta}}
		P_n^m(\cos{\theta}) \sin^2{\theta} \sin{\phi}
	\thinspace d\theta d\phi
	\cr
	&+ E_Y \Bigg[ \frac{\big[ k r j_n(k r) \big]'}{k r} \Bigg]^{\*}       \int_{-\pi}^{\pi} \int_{0}^{\pi} e^{-i m \phi + i k r \cos{\theta}} \bigg(
		\frac{\partial P_n^m(\cos{\theta})}{\partial \theta} \cos{\theta} \sin{\phi}
		- i m \frac{P_n^m(\cos{\theta})}{\sin{\theta}} \cos{\phi}
	\bigg)  \sin{\theta} \thinspace d\theta d\phi.
\end{aligned}
$$

Since, once again, \\(m = \pm 1\\), we may utilize Eqn. 18.116 to perform integration over the \\(\phi\\) angle:

$$ \tag{18.126}
\small
\begin{aligned}
	& \frac{1}{\gamma_{m,n}} \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \delta_{m,\pm 1} (E_X - i m E_Y)
	\bigg[ \frac{j_n(k r)}{k r} \bigg]^{\*}
	\pi n (n + 1) \int_{0}^{\pi} e^{i k r \cos{\theta}}
		P_n^m(\cos{\theta}) \sin^2{\theta}
	\thinspace d\theta
	\cr
	&+ \delta_{m,\pm 1} (E_X - i m E_Y)
	\Bigg[ \frac{\big[ k r j_n(k r) \big]'}{k r} \Bigg]^{\*}
	\pi \int_{0}^{\pi} e^{i k r \cos{\theta}} \bigg(
		\frac{\partial P_n^m(\cos{\theta})}{\partial \theta} \cos{\theta}
		+ \frac{P_n^m(\cos{\theta})}{\sin{\theta}}
	\bigg) \sin{\theta} \thinspace d\theta.
\end{aligned}
$$

The first term can be [integrated by parts](https://en.wikipedia.org/wiki/Integration_by_parts). If we use Eqn. 18.118 and 18.120 to define

$$ \tag{18.127}
\begin{aligned}
	u(n, \theta) &= P_n^{1}(\cos{\theta}) \sin{\theta} = \sin{\theta} \frac{\partial}{\partial \theta} P_n(\cos{\theta}),
	\cr
	\frac{\partial u(n, \theta)}{\partial \theta} &= \bigg( \cos{\theta} \frac{\partial}{\partial \theta}
	+ \sin{\theta} \frac{\partial^2}{\partial \theta^2} \bigg) P_n(\cos{\theta})
	= -n (n+1) P_n(\cos{\theta}) \sin{\theta},
	\cr
	\frac{\partial v(kr, \theta)}{\partial \theta} &= e^{i k r \cos{\theta}} \sin{\theta},
	\cr
	v(kr, \theta) &= \frac{i}{k r} e^{i k r \cos{\theta}},
\end{aligned}
$$

and apply Eqn. 18.1??, we readily obtain

$$ \tag{18.128}
\begin{aligned}
	&\int_{0}^{\pi} e^{i k r \cos{\theta}}
		P_n^1(\cos{\theta}) \sin^2{\theta}
	\thinspace d\theta
	\cr
	&= u(n, \pi) v(kr, \pi) - u(n, 0) v(kr, 0) - \int_{0}^{\pi} \frac{\partial u(n, \theta)}{\partial \theta} v(kr, \theta) \thinspace d\theta
	\cr
	&= n (n+1) \frac{i}{k r} \int_{0}^{\pi} e^{i k r \cos{\theta}} P_n(\cos{\theta}) \sin{\theta} \thinspace d\theta
	 = 2 i^{n+1} n (n+1) \frac{j_n(k r)}{k r}.
\end{aligned}
$$

The second integral of Eqn. 18.126 must not be confused with the one in Eqn. 18.121. Here, we use a different approach: if we differentiate Eqn. 18.1??,

$$ \tag{18.129}
	\frac{\partial j_n(x)}{\partial x}
	= \frac{i^{-n + 1}}{2}
	\int_{0}^{\pi} e^{i x \cos{\theta}} P_n(\cos{\theta}) \sin{\theta} \cos{\theta} \thinspace d\theta,
$$

and utilize Eqn. 18.118, 18.120, and 18.128 once more, we may show that

$$ \tag{18.130}
\begin{aligned}
	& \int_{0}^{\pi} e^{i k r \cos{\theta}} \bigg(
		\frac{\partial P_n^1(\cos{\theta})}{\partial \theta} \cos{\theta}
		+ \frac{P_n^1(\cos{\theta})}{\sin{\theta}}
	\bigg) \sin{\theta} \thinspace d\theta
	\cr
	&= \int_{0}^{\pi} e^{i k r \cos{\theta}} \bigg(
		\cos{\theta} \sin{\theta} \frac{\partial^2 P_n(\cos{\theta})}{\partial \theta^2}
		+ P_n^1(\cos{\theta})
	\bigg) \thinspace d\theta
	\cr
	&= -n (n+1) \int_{0}^{\pi} e^{i k r \cos{\theta}} P_n(\cos{\theta}) \sin{\theta} \cos{\theta} \thinspace d\theta
		+ \int_{0}^{\pi} e^{i k r \cos{\theta}} P_n^1(\cos{\theta}) \sin^2{\theta} \thinspace d\theta
	\cr
	&= 2 i^{n+1} n (n+1) \frac{\big[ k r j_n(k r) \big]'}{k r}.
\end{aligned}
$$

Substitution of Eqn. 18.128 and 18.130 finally allows us to evaluate Eqn. 18.126:

$$ \tag{18.131}
\begin{aligned}
	& \int_{-\pi}^{\pi} \int_{0}^{\pi} \big[ \bm{N_{m,n}^{(1)}}(\bm{r}) \big]^{\*} \cdot \bm{E_i}(\bm{r}) \sin{\theta} \thinspace d\theta d\phi
	\cr
	&= \delta_{m,\pm 1} (i m E_X + E_Y) 2 \pi i^n \gamma_{1,n} n (n+1)
	\Bigg( \Bigg| \frac{\big[ k r j_n(k r) \big]'}{k r} \Bigg|^2 + n (n + 1) \bigg| \frac{j_n(k r)}{k r} \bigg|^2
	\Bigg).
\end{aligned}
$$

As before, we have unified the expressions of both orders by multiplying each term by \\(m\\).

Once we substitute the value of the integral into Eqn. 18.98.2, the factor containing the Bessel function cancels out, making the expression of the second coefficient is particularly simple:

$$ \tag{18.132}
\begin{aligned}
	d_{m,n} &= \delta_{m,\pm 1} (i m E_X + E_Y) 2 \pi i^n \gamma_{1,n}.
\end{aligned}
$$

Derivation of the coefficients given by Eqn. 18.124 and 18.132 completes the vector spherical wave series expansion of an electromagnetic plane wave:

$$ \tag{18.133}
\begin{aligned}
	\bm{E_0}(\bm{e_z}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{e_z})}
	&= \sum_{n=1}^{\infin} \sum_{m=-n}^{n} \big( c_{m,n}(\omega) \bm{M_{m,n}^{(1)}}(\bm{r}, \omega) + d_{m,n}(\omega) \bm{N_{m,n}^{(1)}}(\bm{r}, \omega) \big)
	\cr
	&= i E_X(\omega) \sum_{n=1}^{\infin} 2 \pi i^n \gamma_{1,n} \sum_{m= \pm 1} \big( \bm{M_{m,n}^{(1)}}(\bm{r}, \omega) + m \bm{N_{m,n}^{(1)}}(\bm{r}, \omega) \big)
	\cr
	&+ E_Y(\omega) \sum_{n=1}^{\infin} 2 \pi i^n \gamma_{1,n} \sum_{m= \pm 1} \big( m \bm{M_{m,n}^{(1)}}(\bm{r}, \omega) + \bm{N_{m,n}^{(1)}}(\bm{r}, \omega) \big).
\end{aligned}
$$

The \\(i\\) factor preceding \\(E_X\\) should not come as a surprise. Since, according to [Euler's identity](https://en.wikipedia.org/wiki/Euler%27s_identity),

$$ \tag{18.134}
	e^{\pm i \pi/2} = \pm i,
$$

it simply expresses the fact that the angle between the \\(x\\) and \\(y\\) axes is 90 degrees. This introduces a phase shift: if the \\(y\\)-wave behaves as \\(\sin{\theta}\\) (or \\(\cos{\theta}\\)), then the \\(x\\)-wave is proportional to \\(\cos{\theta}\\) (or \\(-\sin{\theta}\\)) instead.

Another way to show this is by introducing the even and odd vector tesseral wave functions

$$ \tag{18.135}
\begin{aligned}
	\bm{L_{^e_o,m,n}}(\bm{r})
	&= \negthickspace \begin{array}{cc}
	   1 \cr
	   i^{-1}
	\end{array} \negthickspace
	\frac{\bm{L_{m,n}}(\bm{r}) \pm (-1)^m \bm{L_{-m,n}}(\bm{r})}{2 \gamma_{m,n}},
	\cr
	\bm{M_{^e_o,m,n}}(\bm{r})
	&= \negthickspace \begin{array}{cc}
	   1 \cr
	   i^{-1}
	\end{array} \negthickspace
	\frac{\bm{M_{m,n}}(\bm{r}) \pm (-1)^m \bm{M_{-m,n}}(\bm{r})}{2 \gamma_{m,n}},
	\cr
	\bm{N_{^e_o,m,n}}(\bm{r})
	&= \negthickspace \begin{array}{cc}
	   1 \cr
	   i^{-1}
	\end{array} \negthickspace
	\frac{\bm{N_{m,n}}(\bm{r}) \pm (-1)^m \bm{N_{-m,n}}(\bm{r})}{2 \gamma_{m,n}},
\end{aligned}
$$

by mirroring [Euler's formulae](https://en.wikipedia.org/wiki/Euler%27s_formula#Relationship_to_trigonometry)

$$
	\cos(m \phi) = \frac{1}{2} \Big( e^{i m \phi} + e^{-i m \phi} \Big),
	\quad
	\sin(m \phi) = \frac{1}{2 i} \Big( e^{i m \phi} - e^{-i m \phi} \Big),
$$

and accounting for the symmetry property of the vector spherical harmonics given by Eqn. 18.73.

Using the definitions of the vector spherical harmonics (Eqn. 18.72-18.73) and the vector spherical wave functions (Eqn. 18.80-18.81), it may be shown that the functions of Eqn. 18.135 are weighted combinations of the (real) vector tesseral harmonics \\(\bm{\Upsilon_{^e_o,m,n}}, \bm{\Phi_{^e_o,m,n}}, \bm{\Psi_{^e_o,m,n}}\\). In order to save some space, we shall simply state the resulting expressions below:

$$ \tag{18.136}
\begin{aligned}
	\bm{L_{^e_o,m,n}}(\bm{r})
	&= \frac{\partial z_n(k r)}{\partial (k r)} P_n^m(\cos{\theta})
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_r}
	\cr
	&+ \frac{z_n(k r)}{k r} \Big( \tau_{m,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_{\theta}}
	\mp \pi_{m,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \sin \cr
	   \cos
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_{\phi}} \Big)
	\cr
	\bm{M_{^e_o,m,n}}(\bm{r})
	&= z_n(k r)
	\Big( \mp \pi_{m,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \sin \cr
	   \cos
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_{\theta}} - \tau_{m,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_{\phi}} \Big),
	\cr
	\bm{N_{^e_o,m,n}}(\bm{r})
	&= n (n + 1) \frac{z_n(k r)}{k r} P_n^m(\cos{\theta})
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_r}
	\cr
	&+ \frac{\big[ k r z_n(k r) \big]'}{k r}
	\Big( \tau_{m,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_{\theta}}
	\mp \pi_{m,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \sin \cr
	   \cos
	\end{array} \negthickspace (m \phi) \thinspace \bm{e_{\phi}} \Big).
\end{aligned}
$$

Eqn. 18.135 make it easy to show that the plane wave expansion of Eqn. 18.133 can be reduced to

$$ \tag{18.137}
\begin{aligned}
	\bm{E_i}(\bm{r}, \omega)
	&= E_X(\omega) \sum_{n=1}^{\infin} i^n \frac{(2 n + 1)}{n (n + 1)} \big( {-\bm{M_{o,1,n}^{(1)}}}(\bm{r}, \omega) + i \bm{N_{e,1,n}^{(1)}}(\bm{r}, \omega) \big)
	\cr
	&+ E_Y(\omega) \sum_{n=1}^{\infin} i^n \frac{(2 n + 1)}{n (n + 1)} \big( {\bm{M_{e,1,n}^{(1)}}}(\bm{r}, \omega) + i \bm{N_{o,1,n}^{(1)}}(\bm{r}, \omega) \big).
\end{aligned}
$$

Eqn. 18.137 can be found in \[[17](#references) (ch. 7.11)\]. The author does not show the entire derivation, and, unfortunately, arrives at the result with the opposite sign.

Note that, since the vector tesseral wave functions are used to represent the \\(x\\) and \\(y\\) components of the fields, we can transform the odd functions into the even ones by performing a 90 degree rotation:

$$ \tag{18.???}
\begin{aligned}
	\bm{L_{e,m,n}}(r, \theta, \phi) &= \bm{L_{o,m,n}} \bigg( r, \theta, \phi + \frac{\pi}{2 m} \bigg),
	\cr
	\bm{M_{e,m,n}}(r, \theta, \phi) &= \bm{M_{o,m,n}} \bigg( r, \theta, \phi + \frac{\pi}{2 m} \bigg),
	\cr
	\bm{N_{e,m,n}}(r, \theta, \phi) &= \bm{N_{o,m,n}} \bigg( r, \theta, \phi + \frac{\pi}{2 m} \bigg).
\end{aligned}
$$

For many purposes, the most useful expression of the incident field given by Eqn. 18.133 is in terms of the vector spherical harmonics (as shown by Eqn. 18.80-18.81):

$$ \tag{18.138}
\begin{aligned}
	\bm{E_i}(\bm{r}, \omega)
	&= \sum_{m,n} c_{m,n}(\omega) j_n(k r) \bm{\Phi_{m,n}}(\theta, \phi)
	\cr
	&+ \sum_{m,n} d_{m,n}(\omega) \bigg( n (n + 1) \frac{j_n(k r)}{k r} \bm{\Upsilon_{m,n}}(\theta, \phi)
	 + \frac{\big[ k r j_n(k r) \big]'}{k r} \bm{\Psi_{m,n}}(\theta, \phi) \bigg).
\end{aligned}
$$

The expression of the scattered field remains unknown. Since it is a component of the total field

$$ \tag{18.139}
	\bm{E_{ext}} = \bm{E_i} + \bm{E_s},
$$

we know that its vector and scalar potentials satisfy the Helmholtz equations; thus, by linearity, Eqn. 18.36 remains applicable. In addition, thanks to Eqn. 15.6, we know the asymptotic behavior of the scattered field as \\(k r \to \infin\\). As we compare it to Eqn. 18.52, it becomes clear that we must choose the spherical Bessel function of the third kind

$$ \tag{18.140}
	z_n^{(3)}(k r) = h_n^{(1)}(k r)
$$

for the vector spherical wave function series expansion

$$ \tag{18.141}
\begin{aligned}
	\bm{E_s}(\bm{r}, \omega)
	&= \sum_{m,n} \big( a_{m,n}(\omega) \bm{M_{m,n}^{(3)}}(\bm{r}, \omega) + b_{m,n}(\omega) \bm{N_{m,n}^{(3)}}(\bm{r}, \omega) \big)
	\cr
	&= \sum_{m,n} a_{m,n}(\omega) h_n^{(1)}(k r) \bm{\Phi_{m,n}}(\theta, \phi)
	\cr
	&+ \sum_{m,n} b_{m,n}(\omega) \bigg( n (n + 1) \frac{h_n^{(1)}(k r)}{k r} \bm{\Upsilon_{m,n}}(\theta, \phi)
	 + \frac{\big[ k r h_n^{(1)}(k r) \big]'}{k r} \bm{\Psi_{m,n}}(\theta, \phi) \bigg).
\end{aligned}
$$

In addition to the total field \\(\bm{E_{ext}}\\) in the exterior region of the particle, we must also determine the expression of the internal field \\(\bm{E_{int}}\\) induced inside the sphere. We can deduce two of its features: first, in order for the solution to be physically plausible, the field must take on finite values at the origin, which means we must employ the spherical Bessel function of the first kind; and second, the wavenumber \\(k_2\\) inside the sphere depends on its permittivity and permeability (cf. Eqn. 11.8), and is thus necessarily different from the wavenumber \\(k = k_1\\) in the surrounding (host) medium.

The combination of these two facts motivates the following series expansion of the internal field:

$$ \tag{18.142}
\begin{aligned}
	\bm{E_{int}}(\bm{r}, \omega)
	&= \sum_{m,n} \big( a_{m,n}^{\circ}(\omega) \bm{M_{m,n}^{(1)}}(\bm{r}, \omega) + b_{m,n}^{\circ}(\omega) \bm{N_{m,n}^{(1)}}(\bm{r}, \omega) \big)
	\cr
	&= \sum_{m,n} a_{m,n}^{\circ}(\omega) j_n(k_2 r) \bm{\Phi_{m,n}}(\theta, \phi)
	\cr
	&+ \sum_{m,n} b_{m,n}^{\circ}(\omega) \bigg( n (n + 1) \frac{j_n(k_2 r)}{k_2 r} \bm{\Upsilon_{m,n}}(\theta, \phi)
	 + \frac{\big[ k_2 r j_n(k_2 r) \big]'}{k_2 r} \bm{\Psi_{m,n}}(\theta, \phi) \bigg).
\end{aligned}
$$

How are the internal and the external fields related? According to Eqn. 1.17, the tangential component of the electric field must be continuous at the optical interface, i.e. along the surface of the sphere:

$$ \tag{18.143}
	\bm{e_r} \times (\bm{E_{int}} - \bm{E_{ext}}) = 0.
$$

This is where the form of the vector spherical harmonics given by Eqn. 18.72 becomes particularly useful. From the definitions, it immediately follows that

$$ \tag{18.144}
	\bm{e_r} \times \bm{\Upsilon_{m,n}}
	= 0,
	\quad
	\bm{e_r} \times \bm{\Phi_{m,n}}
	= \bm{\Psi_{m,n}},
	\quad
	\bm{e_r} \times \bm{\Psi_{m,n}}
	= - \bm{\Phi_{m,n}}.
$$

Thus, in order to take the cross product, we must either eliminate, swap, or negate and swap the spherical harmonic vectors:

$$ \tag{18.145}
\begin{aligned}
	\bm{e_r} \times \bm{E_{int}}
	&= \sum_{m,n} \bigg( a_{m,n}^{\circ} j_n(k_2 r) \bm{\Psi_{m,n}}(\theta, \phi)
	- b_{m,n}^{\circ} \frac{\big[ k_2 r j_n(k_2 r) \big]'}{k_2 r} \bm{\Phi_{m,n}}(\theta, \phi) \bigg),
	\cr
	\bm{e_r} \times \bm{E_{ext}}
	&= \sum_{m,n}
		\big( c_{m,n} j_n(k_1 r) + a_{m,n} h_n^{(1)}(k_1 r) \big)
	\bm{\Psi_{m,n}}(\theta, \phi)
	\cr
	&- \sum_{m,n} \Bigg(
		d_{m,n} \frac{\big[ k_1 r j_n(k_1 r) \big]'}{k_1 r}
		+ b_{m,n} \frac{\big[ k_1 r h_n^{(1)}(k_1 r) \big]'}{k_1 r}
	\Bigg) \bm{\Phi_{m,n}}(\theta, \phi).
\end{aligned}
$$

Since the vector spherical harmonics are orthogonal, we may conceptually treat the sets of unknown coefficients \\(\lbrace a_{m,n} \rbrace, \lbrace b_{m,n} \rbrace, \lbrace a_{m,n}^{\circ} \rbrace, \lbrace b_{m,n}^{\circ} \rbrace\\) as vectors. Yet, even in this interpretation, the unknowns outnumber the equations 2-to-1. Therefore, we must also apply the second boundary condition of Eqn. 1.17:

$$ \tag{18.146}
	\bm{e_r} \times (\bm{H_{int}} - \bm{H_{ext}}) = \bm{J_n},
$$

Assuming the conductivity of the sphere is not enormous, the surface current term \\(\bm{J_n}\\) can be neglected \[[17](#references) (ch. 1.13)\], and we may combine Eqn. 3.12, 5.2.3 and 18.146 into

$$ \tag{18.147}
	\bm{e_r} \times \bigg( \frac{1}{\mu_2} \nabla \times \bm{E_{int}} - \frac{1}{\mu_1} \nabla \times \bm{E_{ext}} \bigg) = 0.
$$

The simplest way to take the curl of the electric field is by utilizing its vector spherical wave function series representation. According to Eqn. 18.17, 18.19, and 18.34,

$$ \tag{18.148}
	\nabla \times \bm{L_{m,n}} = 0,
	\quad
	\nabla \times \bm{M_{m,n}} = k \bm{N_{m,n}},
	\quad
	\nabla \times \bm{N_{m,n}} = k \bm{M_{m,n}},
$$

which, in our case, simply means that the corresponding series coefficients must be rescaled and interchanged:

$$ \tag{18.149}
\begin{aligned}
	\bm{e_r} \times \nabla \times \bm{E_{int}}
	&= \sum_{m,n} k_2 \bigg( b_{m,n}^{\circ} j_n(k_2 r) \bm{\Psi_{m,n}}(\theta, \phi)
	- a_{m,n}^{\circ} \frac{\big[ k_2 r j_n(k_2 r) \big]'}{k_2 r} \bm{\Phi_{m,n}}(\theta, \phi) \bigg),
	\cr
	\bm{e_r} \times \nabla \times \bm{E_{ext}}
	&= \sum_{m,n}
		k_1 \big( d_{m,n} j_n(k_1 r) + b_{m,n} h_n^{(1)}(k_1 r) \big)
	\bm{\Psi_{m,n}}(\theta, \phi)
	\cr
	&- \sum_{m,n} k_1 \Bigg(
		c_{m,n}  \frac{\big[ k_1 r j_n(k_1 r) \big]'}{k_1 r} +
		a_{m,n} \frac{\big[ k_1 r h_n^{(1)}(k_1 r) \big]'}{k_1 r} \Bigg)
	\bm{\Phi_{m,n}}(\theta, \phi).
\end{aligned}
$$

Let us first consider Eqn. 18.143. It tells us that the expressions given by Eqn. 18.145.1 and 18.145.2 are equal. If perform Hilbert space projection onto \\(\Psi_{s,t}\\) or \\(\Phi_{s,t}\\) using Eqn. 18.77 and 18.79, we obtain the relations that connect the expansion coefficients of the external field to those of the internal field:

$$ \tag{18.150}
\begin{aligned}
	a_{m,n}^{\circ}
	&= \frac{ c_{m,n} j_n(k_1 r) + a_{m,n} h_n^{(1)}(k_1 r) }{ j_n(k_2 r) },
	\cr
	b_{m,n}^{\circ}
	&=
	\frac{ d_{m,n} \big[ k_1 r j_n(k_1 r) \big]' + b_{m,n} \big[ k_1 r h_n^{(1)}(k_1 r) \big]'
	}{ \frac{k_1}{k_2} \big[ k_2 r j_n(k_2 r) \big]' }.
\end{aligned}
$$

Performing the same steps for Eqn. 18.147 and 18.149 leads to

$$ \tag{18.151}
\begin{aligned}
	a_{m,n}^{\circ}
	&= \frac{ c_{m,n} \big[ k_1 r j_n(k_1 r) \big]' + a_{m,n} \big[ k_1 r h_n^{(1)}(k_1 r) \big]'
	}{ \frac{\mu_1}{\mu_2} \big[ k_2 r j_n(k_2 r) \big]' },
	\cr
	b_{m,n}^{\circ}
	&= \frac{ d_{m,n} j_n(k_1 r) + b_{m,n} h_n^{(1)}(k_1 r) }{ \frac{\mu_1 k_2}{\mu_2 k_1} j_n(k_2 r) }.
\end{aligned}
$$

The value of the coefficients \\(c_{m,n}\\) and \\(d_{m,n}\\) of the incident field is known. By combining Eqn. 18.150 and 18.151, we can finally evaluate the coefficients of the scattered field \[[15](#references)\]:

$$ \tag{18.152}
\begin{aligned}
	a_{m,n}
	&= -c_{m,n} \frac{
		j_n(k_2 r) \big[ k_1 r j_n(k_1 r) \big]' - \frac{\mu_1}{\mu_2} j_n(k_1 r) \big[ k_2 r j_n(k_2 r) \big]'
	}{
		j_n(k_2 r) \big[ k_1 r h_n^{(1)}(k_1 r) \big]' - \frac{\mu_1}{\mu_2} h_n^{(1)}(k_1 r) \big[ k_2 r j_n(k_2 r) \big]'
	},
	\cr
	b_{m,n}
	&= -d_{m,n} \frac
	{
		\frac{\mu_1 k_2^2}{\mu_2 k_1^2} j_n(k_2 r) \big[ k_1 r j_n(k_1 r) \big]' - j_n(k_1 r) \big[ k_2 r j_n(k_2 r) \big]'
	}{
		\frac{\mu_1 k_2^2}{\mu_2 k_1^2} j_n(k_2 r) \big[ k_1 r h_n^{(1)}(k_1 r) \big]' - h_n^{(1)}(k_1 r) \big[ k_2 r j_n(k_2 r) \big]'
	}.
\end{aligned}
$$

We may observe that both the scattered and the internal fields are directly proportional to the incident field. Furthermore, the coefficients of proportionality are independent of \\(m\\). They may be written down neatly if we re-introduce the traditional notation that calls the radius of the spherical particle \\(a\\), the size parameter \\(x = k_1 a\\), the relative wavenumber \\(m = k_2 / k_1\\), and the reciprocal[^17] of the relative permeability \\(u = \mu_1 / \mu_2\\). The order-independent multiplicative factors of Eqn. 18.152 can then be expressed as \[[17](#references) (ch. 9.25)\]

[^17]: Recall Eqn. 5.2.3 and the subsequent discussion.

$$ \tag{18.154}
\begin{aligned}
	a_n
	&= -\frac{
		j_n(m x) \big[ x j_n(x) \big]' - u j_n(x) \big[ m x j_n(m x) \big]'
	}{
		j_n(m x) \big[ x h_n^{(1)}(x) \big]' - u h_n^{(1)}(x) \big[ m x j_n(m x) \big]'
	},
	\cr
	b_n
	&= -\frac
	{
		u m^2 j_n(m x) \big[ x j_n(x) \big]' - j_n(x) \big[ m x j_n(m x) \big]'
	}{
		u m^2 j_n(m x) \big[ x h_n^{(1)}(x) \big]' - h_n^{(1)}(x) \big[ m x j_n(m x) \big]'
	}.
\end{aligned}
$$

Many authors \[[4](#references) (ch. 9.22), [6](#references) (ch. 14.5)\] prefer to give this formula in terms of the [Riccati-Bessel functions](https://en.wikipedia.org/wiki/Bessel_function#Riccati%E2%80%93Bessel_functions:_Sn,_Cn,_%CE%BEn,_%CE%B6n)

$$ \tag{18.155}
\begin{aligned}
	\psi_n(x) = x j_{n}(x),
	\quad
	\chi_n(x) = x y_{n}(x),
	\quad
	\xi_n(x) = x h_n^{(1)}(x) = \psi_n(x) + i \chi_n(x),
\end{aligned}
$$

instead:

$$ \tag{18.156}
\begin{aligned}
	a_n
	&= -\frac{
		\psi_n(m x) \psi_n'(x) - u m  \psi_n(x) \psi_n'(m x)
	}{
		\psi_n(m x) \xi_n'(x) - u m \xi_n(x) \psi_n'(m x)
	},
	\cr
	b_n
	&= -\frac
	{
		u m \psi_n(m x) \psi_n'(x) - \psi_n(x) \psi_n'(m x)
	}{
		u m \psi_n(m x) \xi_n'(x) - \xi_n(x) \psi_n'(m x)
	}.
\end{aligned}
$$

The coefficients of proportionality of the internal field can be taken from Eqn. 18.151.1 and 18.150.2:

$$ \tag{18.157}
\begin{aligned}
	a_n^{\circ}
	&= \frac{1}{u} \frac{ \psi_n'(x) + a_n \xi_n'(x) }{ \psi_n'(m x) }
	= m \frac{\psi_n(x) \xi_n'(x) - \xi_n(x) \psi_n'(x)}{\psi_n(m x) \xi_n'(x) - u m \xi_n(x) \psi_n'(m x)},
	\cr
	b_n^{\circ}
	&= m \frac{ \psi_n'(x) + b_n \xi_n'(x) }{ \psi_n'(m x) }
	= m \frac{\psi_n(x) \xi_n'(x) - \xi_n(x) \psi_n'(x)}{u m \psi_n(m x) \xi_n'(x) - \xi_n(x) \psi_n'(m x)}.
\end{aligned}
$$

Note that the numerators of Eqn. 18.157.1 and 18.157.2 are the same. Thus, if their denominators vary slowly, \\(a_n^{\circ}\\) and \\(b_n^{\circ}\\) change roughly at the same rate. In addition, the denominators of Eqn. 18.156.1 and 18.156.2 match those of Eqn. 18.157.1 and 18.157.2, respectively. Therefore, we may expect some of the peaks of \\(a_n\\) and \\(a_n^{\circ}\\) (or \\(b_n\\) and \\(b_n^{\circ}\\)) to be co-located in the same region of the \\(m\text{-}x\\) domain.

{{< figure src="/img/riccati_psi.svg" caption="*Figure N: Riccati-Bessel functions of the first kind of order n=1 (blue), n=2 (orange), n=3 (green).*" >}}

{{< figure src="/img/riccati_chi.svg" caption="*Figure N: Riccati-Bessel functions of the second kind of order n=1 (blue), n=2 (orange), n=3 (green).*" >}}

Now, recall the transformation of the expression of the incident field (Eqn. 18.133-18.137), where we introduced the vector tesseral wave functions. Since the expansion coefficients of the scattered and the internal fields differ from those of the incident field only by a multiplicative factor, it is easy to see that the overall structure of the expression remains the same; novel expressions, such as that of the scattered field, can be obtained by attaching the corresponding factors to the tesseral vectors:

$$ \tag{18.158}
\begin{aligned}
	\bm{E_s}(\bm{r}, \omega)
	&= E_X(\omega) \sum_{n=1}^{\infin} i^n \frac{(2 n + 1)}{n (n + 1)} \big( {-a_n(\omega) \bm{M_{o,1,n}^{(3)}}}(\bm{r}, \omega) + i b_n(\omega) \bm{N_{e,1,n}^{(3)}}(\bm{r}, \omega) \big)
	\cr
	&+ E_Y(\omega) \sum_{n=1}^{\infin} i^n \frac{(2 n + 1)}{n (n + 1)} \big( a_n(\omega) {\bm{M_{e,1,n}^{(3)}}}(\bm{r}, \omega) + i b_n(\omega) \bm{N_{o,1,n}^{(3)}}(\bm{r}, \omega) \big).
\end{aligned}
$$

Similarly, the expression of the internal field can be written as

$$ \tag{18.159}
\begin{aligned}
	\bm{E_{int}}(\bm{r}, \omega)
	&= E_X(\omega) \sum_{n=1}^{\infin} i^n \frac{(2 n + 1)}{n (n + 1)} \big( {-a_n^{\circ}(\omega) \bm{M_{o,1,n}^{(1)}}}(\bm{r}, \omega) + i b_n^{\circ}(\omega) \bm{N_{e,1,n}^{(1)}}(\bm{r}, \omega) \big)
	\cr
	&+ E_Y(\omega) \sum_{n=1}^{\infin} i^n \frac{(2 n + 1)}{n (n + 1)} \big( a_n^{\circ}(\omega) {\bm{M_{e,1,n}^{(1)}}}(\bm{r}, \omega) + i b_n^{\circ}(\omega) \bm{N_{o,1,n}^{(1)}}(\bm{r}, \omega) \big).
\end{aligned}
$$

Eqn. 18.137, 18.158 and 18.159 allow us to determine the value of the electromagnetic field at any point in space, both inside and outside the spherical particle. Thus, the scattering problem is solved. This completes the derivation of the Lorenz-Mie-Debye formula.

### Analysis and Interpretation of the Lorenz-Mie-Debye Formulae

The solution of the Lorenz-Mie-Debye theory is rigorous and exact (to the extent permitted by the assumptions inherent to the statement of the problem). Unfortunately, the form of the solution - a series expansion - has significant drawbacks.

One of the biggest issues is the difficulty of finding a physical interpretation of the results. To see why that is the case, consider the Taylor series expansions of two elementary functions:

$$ \tag{19.1}
\begin{aligned}
	\cos{x} - \sin{x} &= 1-x-\frac{x^2}{2}+\frac{x^3}{6}+\frac{x^4}{24}+O\left(x^5\right),
	\cr
	e^{-x} &= 1-x+\frac{x^2}{2}-\frac{x^3}{6}+\frac{x^4}{24}+O\left(x^5\right).
\end{aligned}
$$

These functions have radically different behavior, yet correctly guessing the function given the first few terms of its series expansion is a nontrivial task. And even if the function were known, the method of derivation still obscures the mechanism of the underlying physical process.

The problem is exacerbated by the convergence properties of the series. It was shown by Debye (1909) that, for points in the radiation zone, the asymptotic expression (for large \\(x\\)) of the scattered field  requires on the order of \\(n \sim (x + 1/2)\\) terms; this number can reach \\(x = 2 \pi (1 \text{ mm} / 380 \text{ nm}) = 16535 \\) for large [raindrops](https://glossary.ametsoc.org/wiki/Raindrop) illuminated by visible light, making computation impractical.

#### Partial Waves

The most straightforward interpretation of Eqn. 18.158 and 18.159 treats the individual terms as *partial waves*. If we examine the definition of the vector tesseral harmonics given by Eqn. 18.136, we may note that \\(\bm{N\_{m,n}}\\) has a radial component, while \\(\bm{M\_{m,n}}\\) does not. Since an electric charge is a source of a radially-symmetric electric field, we may interpret the existence of the radial component of \\(\bm{N\_{m,n}}\\) as a sign of the fact that the sphere contains a distribution of electric charges; therefore, the partial waves prefixed with \\(b\_n\\) are said to be of the *electric type*. At the same time, in comparison with Eqn. 18.36 of the electric field, Eqn. 18.37 of the magnetic field has the coefficients \\(a\_n\\) and \\(b\_n\\) interchanged; similar logical steps lead to the conclusion that the partial waves prefixed with \\(a\_n\\) are of the *magnetic type* \[[17](#references) (ch. 9.22)\].

We may also show that the field becomes transverse in the radiation zone (see Eqn. 13.14 for the case of a scattering object of an arbitrary shape). Using the asymptotic expressions of the spherical Bessel functions given by Eqn. 18.52, the corresponding Riccati-Bessel functions (and their derivatives) take the form

$$ \tag{19.2}
\begin{aligned}
	& \psi_n(x) = \sin\negmedspace\Big( x - \frac{\pi n}{2} \Big)
	+ \mathrm{O}\big( x^{-1} \big),
	&
	& \xi_n(x) = i^{-n-1} e^{i x}
	+ \mathrm{O}\big( x^{-1} \big),
	\cr
	& \psi_n'(x) = \cos\negmedspace\Big( x - \frac{\pi n}{2} \Big)
	+ \mathrm{O}\big( x^{-2} \big),
	&
	& \xi_n'(x) = i^{-n} e^{i x}
	+ \mathrm{O}\big( x^{-2} \big).
\end{aligned}
$$

If we substitute Eqn. 19.2 into 18.136 and neglect the inverse square term, we can observe that neither vector has a radial component:

$$ \tag{19.3}
\begin{aligned}
	\bm{M_{^e_o,1,n}^{(3)}}(\bm{r})
	&\simeq i^{-n-1} \frac{e^{i k r}}{k r}
	\Big( \mp \pi_{1,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \sin \cr
	   \cos
	\end{array} \negthickspace (\phi) \thinspace \bm{e_{\theta}} - \tau_{1,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (\phi) \thinspace \bm{e_{\phi}} \Big),
	\cr
	\bm{N_{^e_o,1,n}^{(3)}}(\bm{r})
	&\simeq i^{-n} \frac{e^{i k r}}{k r}
	\Big( \tau_{1,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \cos \cr
	   \sin
	\end{array} \negthickspace (\phi) \thinspace \bm{e_{\theta}}
	\mp \pi_{1,n}(\theta)
	\negthickspace \begin{array}{cc}
	   \sin \cr
	   \cos
	\end{array} \negthickspace (\phi) \thinspace \bm{e_{\phi}} \Big).
\end{aligned}
$$

Furthermore, these vectors are mutually orthogonal to each other. Hence, partial waves of the electric type are sometimes called *transverse magnetic modes* (their magnetic field is always transverse, while the electric isn't), and those of the magnetic type are referred to as *transverse electric modes* (their electric field is always transverse, while the magnetic isn't).

Substitution into Eqn. 18.158 produces a convenient asymptotic expansion of the scattered field:

$$ \tag{19.4}
\begin{aligned}
	\bm{E_s}(\bm{r}, \omega)
	&\simeq \frac{e^{i k r}}{k r} \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)}
	i \big( a_n \pi_{1,n}(\theta) + b_n \tau_{1,n}(\theta) \big)
	\big( E_X \cos(\phi) + E_Y \sin(\phi) \big) \bm{e_{\theta}}
	\cr
	&+ \frac{e^{i k r}}{k r} \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)}
	i \big( a_n \tau_{1,n}(\theta) + b_n \pi_{1,n}(\theta) \big)
	\big( {-E_X} \sin(\phi) + E_Y \cos(\phi) \big) \bm{e_{\phi}}.
\end{aligned}
$$

As we shall soon see, the standard definitions of \\(a\_n\\) and \\(b\_n\\) often produce imaginary coefficients, while the expressions of \\(\pi\_{1,n}\\) and \\(\tau\_{1,n}\\) composed of elementary functions are negative. Therefore, for clarity, it makes sense to structure Eqn. 19.4 in the following way:

$$ \tag{19.5}
\begin{aligned}
	\begin{bmatrix}
		E_{s,\theta}(\bm{r}, \omega) \cr
		E_{s,\phi}(\bm{r}, \omega) \cr
	\end{bmatrix}
	&\simeq \frac{e^{i k r}}{k r}
	\Bigg( \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)}
	\frac{a_n}{i}
	\begin{bmatrix}
		-\pi_{1,n}(\theta) & 0 \cr
		0 & -\tau_{1,n}(\theta) \cr
	\end{bmatrix}
	\Bigg)
	\begin{bmatrix}
		\phantom{-}\cos(\phi) & \sin(\phi) \cr
		-\sin(\phi) & \cos(\phi) \cr
	\end{bmatrix}
	\begin{bmatrix}
		E_X \cr
		E_Y \cr
	\end{bmatrix}
	\cr
	&+ \frac{e^{i k r}}{k r}
	\Bigg( \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)}
	\frac{b_n}{i}
	\begin{bmatrix}
		-\tau_{1,n}(\theta) & 0 \cr
		0 & -\pi_{1,n}(\theta) \cr
	\end{bmatrix}
	\Bigg)
	\begin{bmatrix}
		\phantom{-}\cos(\phi) & \sin(\phi) \cr
		-\sin(\phi) & \cos(\phi) \cr
	\end{bmatrix}
	\begin{bmatrix}
		E_X \cr
		E_Y \cr
	\end{bmatrix}.
\end{aligned}
$$

In addition to the benefits described above, it allows us identify a rotation of the incident electric field \\([E_X, E_Y]^T\\) around the \\(z\\)-axis. If we refer to Eqn. 15.11 and Fig. N, we may recognize the components of the phasor \\([E\_x(\phi), E\_y(\phi)]^T\\) defined relative to the plane of observation.

If we specify the incident field at a certain coordinate \\(z < 0\\) and substitute the resulting phasor into Eqn. 19.5, the resulting expression is a perfect match for the template of Eqn. 15.16. This allows us to isolate the expression of the scattering matrix

$$ \tag{19.6}
	S(\theta, \omega)
	= \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)} i
	\begin{bmatrix}
		a_n \pi_{1,n}(\theta) + b_n\tau_{1,n}(\theta) & 0 \cr
		0 & a_n\tau_{1,n}(\theta) + b_n \pi_{1,n}(\theta) \cr
	\end{bmatrix}.
$$

It is both diagonal and independent of the azimuthal angle \\(\phi\\). This is consistent with the predictions of the general scattering theory of spherical particles of Sec. 15.

We can study the angular dependence of the scattering matrix by analyzing the polar functions \\(\pi\_{1,n}\\) and \\(\tau\_{1,n}\\) given by Eqn. 18.68. If we substitute the recurrence relation of the associated Legendre polynomials (Eqn. 18.5?) into the formulae of the polar functions, it is straightforward to show that the latter satisfy the recurrence relations

$$ \tag{19.7}
\begin{aligned}
	-\pi_{1,0}(\theta) &= 0,
	&
	\pi_{1,n}(\theta)
	&= \frac{2 n - 1}{n - 1}\cos(\theta) \pi_{1,n-1}(\theta) - \frac{n}{n - 1} \pi_{1,n-2}(\theta),
	\cr
	-\pi_{1,1}(\theta) &= \mathrm{sign}(\theta),
	&
	\tau_{1,n}(\theta)
	&= n \cos(\theta) \pi_{1,n}(\theta) - (n + 1) \pi_{1,n-1}(\theta),
\end{aligned}
$$

Coupled with the recurrence relations of the spherical Bessel functions given by Eqn. 18.51, it gives rise to a powerful [memoization](https://en.wikipedia.org/wiki/Memoization)-based method of computation, which is the key to rapid evaluation of the scattering matrix.

On the \\([0, \pi]\\) interval, the polar functions can be expressed in terms of \\(\cos{\theta}\\). In this particular case, the explicit formulae for the first couple of orders are

$$ \tag{19.8}
\begin{aligned}
	-\tau_{1,1}(\theta) &= \cos(\theta), &
	-\pi_{1,1}(\theta)  &= 1,
	\cr
	-\tau_{1,2}(\theta) &= 3 \cos(2 \theta), &
	-\pi_{1,2}(\theta)  &= 3 \cos(\theta).
\end{aligned}
$$

The values of these functions at the endpoints of the \\([0, \pi]\\) interval must be determined by a careful limiting process. The final result is always an integer:

$$ \tag{19.?}
\begin{aligned}
	-\tau_{1,n}(0) &= -\pi_{1,n}(0) = \frac{n (n+1)}{2},
	\cr
	-\tau_{1,n}(\pi) &= +\pi_{1,n}(\pi) = (-1)^n \frac{n (n+1)}{2}.
\end{aligned}
$$

Since \\(\phi \in [0, 2 \pi)\\) in a spherical coordinate system, the values of the argument of the polar functions are formally limited to the range of \\(\theta \in [0, \pi]\\), which corresponds to the upper half of a circle. Nevertheless, we are interested in the angular behavior of the scattered field for all possible directions of observation which, when taken together, form the surface of a unit sphere. We could approach this problem by plotting the 2-dimensional spherical distributions found in Eqn. 19.5; however, the resulting plots are fairly complicated, and it is much simpler to consider a slice of a unit sphere formed by two semicircles with \\(\phi = 0\\) and \\(\phi = \pi\\) instead.

In general, Eqn. 19.7 shows that \\(\pi\_{1,n}\\) and \\(\tau\_{1,n}\\) are odd functions of \\(\theta\\):

$$ \tag{19.9}
	\pi_{1,n}(-\theta) = -\pi_{1,n}(\theta),
	\quad
	\tau_{1,n}(-\theta) = -\tau_{1,n}(\theta).
$$

And because the scattered electric field is also an odd function of \\(\theta\\),

$$ \tag{19.10}
\begin{aligned}
	\bm{E_s}(r, \theta, \phi + \pi, \omega)
	= -\bm{E_s}(r, \theta, \phi, \omega)
	= \bm{E_s}(r, -\theta, \phi, \omega),
\end{aligned}
$$

we may use the polar plots of \\(-\tau\_{1,n}\\) and \\(-\pi\_{1,n}\\) on the interval \\(\theta \in [-\pi, \pi]\\) to illustrate the angular behavior of Eqn. 19.5.

{{< figure src="/img/polar_tau.svg" caption="*Figure N: Polar functions \\(-\tau\_{1,n}\\) of order n=1 (blue), n=2 (orange), n=3 (green). Dashed lines indicate negative values.*" >}}

{{< figure src="/img/polar_pi.svg" caption="*Figure N: Polar functions \\(-\pi\_{1,n}\\) of order n=1 (blue), n=2 (orange), n=3 (green). Dashed lines indicate negative values.*" >}}

Care must be taken when interpreting these plots. Typically, if the value of a function is negative, it is plotted with a positive magnitude in the opposite direction. Such visualizations can be misleading. Instead, we utilize dashed lines for negative values and plot them in the original direction. Also, note the sign reversal at the horizontal axis. It does not imply a discontinuity of the field; rather, during the transition from \\(\phi = 0\\) to \\(\phi = \pi,\\) it is the direction of the spherical coordinate basis vectors that is reversed.

Now, recall that Eqn. 19.5 contains a sum of the (negated) polar functions. Evidently, their plots show the largest amount of overlap along the horizontal axis. Yet, we must also account for the sign of the functions involved. In this sense, the forward direction is unique, because only then do the values of all functions share the same sign. Thus, as the particle size increases and we accumulate more terms, a larger percentage of total energy is scattered in the forward direction. This phenomenon is called the *Mie effect*.

The explicit expressions of the polar functions (and their plots) also allow us to determine the *apparent* charge configurations responsible for the first few partial waves.

First, let us consider the *electric* partial waves by setting \\(a\_n = 0\\) in Eqn. 19.5. Then, according to Eqn. 15.13, the scattering matrix of the first electric partial wave (given by the first line of Eqn. 19.8) performs a *rotation* of the coordinate axes -- one that corresponds to *reinterpretation* of the components of the incident electric phasor \\([E\_x(\phi), E\_y(\phi), 0]^T\\) in spherical coordinates -- followed by a *projection* onto the tangent plane of a sphere; this results in the radial component of the phasor being discarded.

Another way of saying this is that the *tangential component* of the incident electric field is parallel to the electric field of the first electric partial wave. Furthermore, Eqn. 19.5 indicates that the components of the scattered field propagate as spherical waves. These are key features of the far field of an *electric dipole*, a concept we have previously encountered in Sec. 10 and 13.

Once the size of the particle begins to approach the wavelength of light (as measured in the interior of the sphere), the atoms in its interior no longer oscillate in-phase because they are too far apart \[[5](#references) (vol. I, ch. 32.4)\]. This renders the dipole approximation insufficient, and therefore requires a more general description of the scatterer. One approach, something that we have already explored in Eqn. 13.12, consists of taking an integral over the volume of the particle filled with dipoles. However, that is not how the Lorenz-Mie-Debye formula works; we may interpret it as a [multipole series](https://en.wikipedia.org/wiki/Multipole_expansion) expansion instead.

Let us see how it works. Imagine a particle in the radiation zone. Since the particle is so small (or so far away), it appears point-like to us. However, a single point charge (a monopole), due to its symmetry, is not able to produce radiation that is transverse. Thus, we should reimagine the small particle in question as a tiny disk instead.

How should we orient this disk with respect to the incident wave? Since the axis of a dipole is aligned with the vector of the incident field, the disk must contain the latter. Then, by symmetry, we have two remaining options for the orientation: either across or along the direction of propagation of the incident wave. While the first option allows us to rotate the dipole around the axis of the incident beam, this action can also be accomplished by using a complex coefficient in front of the dipole term. The second option is much more versatile, since it allows us to imagine sources of scattered radiation at arbitrary (polar) angles with respect to the direction of incidence.

The multipole series expansion process consists of splitting the perimeter of the disk into segments terminated by equidistant pairs of charges of opposite sign. A single pair rotated by \\(\pi/2\\) radians represents the dipole term. The \\(\theta\\)-component of its far field is split into two lobes separated by the plane oriented transversely with respect to the direction of incidence. In this plane, due to the projection angle, the \\(\theta\\)-component of the far field vanishes. This can be clearly seen as two vertical dips in the blue graph of Fig. N.

For a larger particle, we may require a greater number of electric charges to model its scattering behavior. Thus, we may add the second partial wave -- the [quadrupole](https://en.wikipedia.org/wiki/Quadrupole) term -- by subdividing the perimeter of the disk into four segments. In the standard orientation, the pattern formed by the charges resembles the vertices of an axis-aligned square; this explains why the orange graph of Fig. 4 is rotated by \\(\pi/4\\) radians.

We can continue the subdivision process by using three pairs of charges of opposite sign rotated by \\(\pi/6\\) radians, forming a *sextupole* that generates the third partial wave. Naturally, its graph (in green) has six zeros and six lobes. Higher order terms can be obtained in a similar manner. In summary, an *electric* partial wave is the electric field of an *electric* multipole.

{{< figure src="/img/partial_waves.png" caption="*Figure N: Partial waves. Top row: electric type, bottom row: magnetic type. Columns, left to right: dipole, quadrupole, sextupole. (Mie, 1908)*" >}}

So far, we have specifically focused on *electric* partial waves of the form \\( \sum\_{m} b\_{m,n} \bm{N\_{m,n}^{(3)}} \\) that are generated by oscillating *electric* charges. However, examination of Eqn. 18.37 shows that \\( \sum\_{m} a\_{m,n} \bm{N\_{m,n}^{(3)}} \\) is a *magnetic* field that exhibits a pattern similar to the corresponding electric partial wave (up to rotation and scale). Thus, we may propose a similar explanation in terms of *magnetic* charges of opposite polarity (or, equivalently, loops of circulating current). This makes \\( \sum\_{m} a\_{m,n} \bm{N\_{m,n}^{(3)}} \\) the magnetic field of a *magnetic* multipole.

In order to show its connection to the *magnetic* partial wave \\( \sum\_{m} a\_{m,n} \bm{M\_{m,n}^{(3)}} \\), return to Eqn. 18.36, set \\(b\_n' = 0\\), then take its curl (as shown by Eqn. 3.12) and examine the result:

$$ \tag{19.11}
\begin{aligned}
	\frac{1}{i \omega} \nabla \times \bm{E'}
	= \frac{1}{i \omega} \nabla \times \sum\_{m} a_{m,n} \bm{M_{m,n}^{(3)}} = \frac{k}{i \omega} \sum\_{m} a_{m,n} \bm{N_{m,n}^{(3)}}
	= \bm{B'}.
\end{aligned}
$$

Eqn. 19.11 shows that a *magnetic* partial wave is the electric field of a *magnetic* multipole. Since the curl of this electric field is the magnetic field of a magnetic multipole, the electric field must circulate. This is nicely illustrated by Mie in his original paper \[Mie 1908\].

For large particles, the partial waves can be classified according to the *localization principle*, according to which the terms of the order \\(n\\) represent parallel rays of incident light traced at the distance \\((n-1/2)/x\\) from the center of the particle \[[4](#references) (ch. 12.31)\]. It implies that the rays of the order \\(n \le (x + 1/2)\\) intersect the sphere, while the higher order waves pass above its surface. The localization principle can be also used to explain the \\(n \sim (x + 1/2)\\) terms rule often used for numerical computations.

#### Resonance

Since a sphere is a body bounded by a closed surface, its interior can sustain a certain number of standing waves, also known as [normal modes](https://en.wikipedia.org/wiki/Normal_mode) \[[5](#references) (vol. I, ch. 49), [17](#references) (ch. 9.22)\]. These waves oscillate at [natural frequencies](https://en.wikipedia.org/wiki/Natural_frequency) that depend on the size and the composition of the particle.

The natural frequencies \\(\omega_n\\) can be determined by setting the denominators of Eqn. 18.157 of the internal field to zero, solving the resulting transcendental equations for \\(x_n = x\\), and substituting the roots into Eqn. 7.4:

$$ \tag{19.12}
	x_n^2
	= \left(k_1 a \right)^2
	= \Big( \omega_n^2 \epsilon_1 + i \omega_n \sigma_1 \Big) \mu_1 a^2.
$$

Typically, the host medium is non-conductive and non-magnetic, which leads to

$$ \tag{19.1?}
	\omega_n = \frac{x_n}{ a \sqrt{\epsilon_1 \mu_0}}.
$$

The natural sizes \\(x_n\\) are always complex, which makes the natural frequencies \\(\omega_n\\) complex as well. This implies that, no matter what the frequency of the incident wave is, the amplitudes of the scattered and the internal fields remain finite \[[17](#references) (ch. 9.25)\]. Nevertheless, a certain combination of parameters that reduces the magnitude of the denominator to a small value may simultaneously make the numerator large, producing a spike in the plot of the expansion coefficient; this phenomenon is called [resonance](https://en.wikipedia.org/wiki/Resonance) \[[5](#references) (vol. I, ch. 23)\].

-->

#### Limiting Cases

Let us take a closer look at the expressions of the expansion coefficients of the scattered field given by Eqn. 18.156.

First, let us consider a very large spherical particle. If we ignore the possibility that it actually represents a cavity (e.g. an air bubble), then \\(| m | > 1\\), which, once combined with \\(| x | \gg 1\\), leads to \\(| m x | \gg 1\\). This allows us to evaluate the expansion coefficients of the first few orders using the asymptotic expressions given by Eqn. 19.2:

$$ \tag{19.13}
\small
\begin{aligned}
	a_n
	&\simeq -i^n e^{-i x} \frac
	{
		\sin(m x - \pi n / 2) \cos(x - \pi n / 2) - u m \cos(m x - \pi n / 2) \sin(x - \pi n / 2)
	}{
		\sin(m x - \pi n / 2) + i u m \cos(m x - \pi n / 2)
	},
	\cr
	b_n
	&\simeq -i^{n + 1} e^{-i x} \frac
	{
		-\cos(m x - \pi n / 2) \sin(x - \pi n / 2) + u m \sin(m x - \pi n / 2) \cos(x - \pi n / 2)
	}{
		-\cos(m x - \pi n / 2) + i u m \sin(m x - \pi n / 2)
	}.
\end{aligned}
$$

Strictly speaking, these expressions are only valid if both \\(| x | \gg n^2/2\\) and \\(| m x | \gg n^2/2\\); in practice, the approximation error remains acceptable if \\(| x | \sim n/2\\). The asymptotic expressions valid for all orders \\(n\\) have been found by Debye (1909); they are considerably more complicated, and we shall not discuss them here.

We may observe that the resulting coefficients are highly cyclical: \\(a_{n+2} \simeq a\_n\\) and \\(a_{n+1} \simeq b\_n\\). Thus, for any given pair of \\(m\\) and \\(x\\), only 2 unique coefficients exist. In addition, their sum is independent of the order \\(n\\).

These formulae can be simplified by introducing the exponential notation. Since

$$ \tag{19.14}
\begin{aligned}
	-i^{n+1} \sin\negmedspace\Big( x - \frac{\pi n}{2} \Big) e^{-i x}
	&= \frac{1}{2} \left( (-1)^n e^{-i 2 x} - 1 \right),
	\cr
	-i^n \cos\negmedspace\Big( x - \frac{\pi n}{2} \Big) e^{-i x}
	&= \frac{1}{2} \left( (-1)^{n+1} e^{-i 2 x} - 1 \right),
\end{aligned}
$$

Eqn. 19.13 can be equivalently expressed as

$$ \tag{19.15}
\begin{aligned}
	a_n
	&\simeq \frac{1}{2} \left( (-1)^n \frac
	{
		-\sin(m x - \pi n / 2) + i u m \cos(m x - \pi n / 2)
	}{
		\sin(m x - \pi n / 2) + i u m \cos(m x - \pi n / 2)
	} e^{-i 2 x} - 1 \right),
	\cr
	b_n
	&\simeq \frac{1}{2} \left( (-1)^{n+1} \frac
	{
		\cos(m x - \pi n / 2) + i u m \sin(m x - \pi n / 2)
	}{
		-\cos(m x - \pi n / 2) + i u m \sin(m x - \pi n / 2)
	} e^{-i 2 x} - 1 \right).
\end{aligned}
$$

Further reduction can be achieved by rewriting Eqn. 19.15 in terms of \\(\tan(m x - \pi n / 2)\\):

$$ \tag{19.16}
\begin{aligned}
	a_n
	&\simeq (-1)^n
	\left( \frac{1}{1 - i (u m)^{-1} \tan(m x - \pi n / 2)} - \frac{1}{2} \right)
	e^{-i 2 x} - \frac{1}{2},
	\cr
	b_n
	&\simeq (-1)^n
		\left( \frac{1}{1 - i u m \tan(m x - \pi n / 2)} - \frac{1}{2} \right)
	e^{-i 2 x} - \frac{1}{2}.
\end{aligned}
$$

These expressions demonstrate that the magnitudes of the coefficients rapidly oscillate between 0 and 1.

{{< figure src="/img/coef_a_n.svg" caption="*Figure N: The plots of the real (solid line) and the imaginary (dashed line) parts of the expansion coefficient \\(a_1/i\\) for \\(m = 4/3\\). The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

{{< figure src="/img/coef_b_n.svg" caption="*Figure N: The plots of the real (solid line) and the imaginary (dashed line) parts of the expansion coefficient \\(b_1/i\\) for \\(m = 4/3\\). The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

In addition to the cyclicality, by transforming Eqn. 19.15 into a function of \\((m - 1) x\\), it can be shown that the expansion coefficients are periodic functions of \\(x\\), with the period

$$ \tag{19.1??}
\begin{aligned}
	\Delta x = \frac{\pi}{m - 1},
\end{aligned}
$$

provided that \\(0.5 < m < 2.5\\), and \\(x\\) is sufficiently large \[[23](#references)\].

Next, let us turn our attention to particles on the opposite end of the size spectrum, with \\(| m x | \ll 1\\). If we assume that \\(| m | > 1\\), then it follows that \\(| x | \ll 1\\) as well. The Taylor series expansions of the Riccati-Bessel functions \[[17](#references) (ch. 7.4)\] are

$$ \tag{19.17}
\begin{aligned}
	\psi_n(x)
	&= 2^n x^{n+1} \sum_{k=0}^{\infin} \frac{(-1)^k (n + k)!}{k! (2 n + 2 k + 1)!} x^{2 k},
	\cr
	\chi_n(x)
	&= -\frac{1}{2^n x^n} \sum_{k=0}^{\infin} \frac{\Gamma(2 n - 2 k + 1)}{k! \Gamma(n - k + 1)} x^{2 k},
\end{aligned}
$$

where

$$ \tag{19.18}
\begin{aligned}
	\Gamma(n + 1) = n! \qquad (n \in \mathcal{Z}, n>0)
\end{aligned}
$$

is the [Gamma function](https://en.wikipedia.org/wiki/Gamma_function). The Taylor series expansion of \\(\xi_n(x)\\) can be obtained by combining the expressions listed above according to Eqn. 18.155.3.

The two functions of Eqn. 19.17 exhibit the opposite behavior near the origin. On the one hand, the function of the first kind is positive, and quickly approaches 0 as \\(n\\) increases. On the other hand, the function of the second kind is negative, and rapidly diverges from 0. Both power series are dominated by the leading \\(k=0\\) term: this is clearly demonstrated by the almost perfectly straight [log-log plots](https://en.wikipedia.org/wiki/Log%E2%80%93log_plot) shown below.

{{< figure src="/img/small_psi.svg" caption="*Figure N: Riccati-Bessel functions of the first kind of order n=1 (blue), n=2 (orange), n=3 (green).*" >}}

{{< figure src="/img/small_chi.svg" caption="*Figure N: Negated Riccati-Bessel functions of the second kind of order n=1 (blue), n=2 (orange), n=3 (green).*" >}}

Thus, we can estimate the asymptotic behavior of the expansion coefficients of the scattered field by taking a single term from each of Eqn. 19.17.1 and 19.17.2:

$$ \tag{19.19}
\begin{aligned}
	\frac{a_n}{i}
	&\simeq \frac{ 1 - u }
	{ N x^{-2 n-1} (n (u + 1) + u) - i (1 - u) },
	\cr
	\frac{b_n}{i}
	&\simeq \frac{u m^2 - 1}
	{ N x^{-2 n-1} (n (u m^2 + 1) + 1) - i (u m^2 - 1) },
\end{aligned}
$$

where

$$ \tag{19.20}
	N(n) = \frac{ (2 n)! (2 n+1)! }{ 4^n (n!)^2 (n+1) }
$$

is a rapidly growing function of \\(n\\), with \\(N(1)=3/2.\\)

In this crude approximation, the magnetic coefficients \\(a_n\\) vanish for non-magnetic materials with \\(u=1\\). In addition, the magnetic coefficients do not depend on the relative wavenumber \\(m\\) at all. However, this approximation serves it purpose, since it clearly demonstrates that the magnitudes of both coefficients are roughly proportional \\(N^{-1} x^{2 n + 1}\\). Since, for \\(| x | \ll 1\\), the latter plummets with increasing \\(n\\), we may restrict ourselves to just the fist couple of terms.

In general, for arbitrary argument values, the expansion coefficients of the first couple of orders can be easily evaluated *exactly* using the explicit formulae of the Riccati-Bessel functions derived from Eqn. 18.51 and 18.155:

$$ \tag{19.21}
\begin{aligned}
	& \psi_1(x) = \frac{\sin{x}}{x}-\cos{x},
	&
	& \psi_2(x) = \frac{\left( 3-x^2 \right) \sin{x} - 3 x \cos{x}}{x^2},
	\cr
	& \xi_1(x) = -\frac{e^{i x} (x+i)}{x},
	&
	& \xi_2(x) = \frac{i e^{i x} \left(x^2+3 i x-3\right)}{x^2}.
\end{aligned}
$$

However, for very small particles with \\(| m x | \ll 1\\), it is somewhat more efficient to utilize Taylor series expansions. We may expand Eqn. 19.21 using the following expression of the complex exponential

$$ \tag{19.2z}
	e^{i x} = 1 + i x - \frac{x^2}{2} - i \frac{x^3}{6} + \frac{x^4}{24} + i \frac{x^5}{120} - \frac{x^6}{720} + \mathrm{O}\big( x^7 \big),
$$

and then substitute the resulting series into Eqn. 18.156. Restricting ourselves to polynomials of degree 6, we obtain

$$ \tag{19.22}
\begin{aligned}
	\frac{a_{1}}{i}
	&= \frac{2}{3} \frac{1-u}{1 + 2 u} x^3
	+ \frac{1}{5} \frac{u m^2+4 u^2-6 u+1}{(1 + 2 u)^2} x^5
	- i \left( \frac{2}{3} \frac{1-u}{1 + 2 u} x^3 \right)^2
	+ \mathrm{O}\big( x^7 \big),
	\cr
	\frac{b_{1}}{i}
	&= \frac{2}{3} \frac{u m^2-1}{u m^2+2} x^3
	+ \frac{1}{5} \frac{u^2 m^4 +u m^4 -6 u m^2 +4}{\left(u m^2+2\right)^2} x^5
	- i \left( \frac{2}{3} \frac{u m^2-1}{u m^2+2} x^3 \right)^2
	+ \mathrm{O}\big( x^7 \big),
	\cr
	\frac{a_{2}}{i}
	&= \frac{1}{15} \frac{1-u}{2 + 3 u} x^5
	+ \mathrm{O}\big( x^7 \big),
	\cr
	\frac{b_{2}}{i}
	&= \frac{1}{15} \frac{u m^2-1}{2 u m^2+3} x^5
	+ \mathrm{O}\big( x^7 \big).
\end{aligned}
$$

If the material is non-magnetic, we may set \\(u=1\\):

$$ \tag{19.23}
\begin{aligned}
	\frac{a_{1}}{i}
	&= \frac{1}{45} \left(m^2-1\right) x^5
	+ \mathrm{O}\big( x^7 \big),
	\cr
	\frac{b_{1}}{i}
	&= \frac{2}{3} \frac{m^2-1}{m^2+2} x^3
	+ \frac{2}{5} \frac{(m^2 - 1)(m^2 - 2)}{\left(m^2+2\right)^2} x^5
	- i \left( \frac{2}{3} \frac{m^2-1}{m^2+2} x^3 \right)^2
	+ \mathrm{O}\big( x^7 \big),
	\cr
	\frac{a_{2}}{i}
	&= \mathrm{O}\big( x^7 \big),
	\cr
	\frac{b_{2}}{i}
	&= \frac{1}{15} \frac{m^2-1}{2 m^2+3} x^5
	+ \mathrm{O}\big( x^7 \big).
\end{aligned}
$$

Unexpectedly, for very small particles, the relationship between the electric and the magnetic coefficients is reversed: \\(a\_n \sim b_{n+1}\\) as opposed to \\(a_{n+1} \sim b\_n\\) when particles are very large.

For small molecules, even the \\(x^5\\) term is often neglected. In this case, all coefficients of \\(b_1/i\\) except for

$$ \tag{19.24}
	\frac{2}{3} \frac{m^2-1}{m^2+2} x^3
$$

vanish. Eqn. 19.24 is the basis of the famous law of [Rayleigh scattering](https://en.wikipedia.org/wiki/Rayleigh_scattering) that explains the color of the sky: when molecules of the atmosphere scatter sunlight, the intensity of blue light, which has a shorter wavelength, is much greater than of the rest of the [visible spectrum](https://en.wikipedia.org/wiki/Visible_spectrum), since the size parameter \\(x\\) is inversely proportional to the wavelength \\(\lambda\\).

Occasionally, additional terms of the series expansion given by Eqn. 19.23.2 are retained. The second term

$$ \tag{19.2x}
	\frac{2}{5} \frac{(m^2 - 1)(m^2 - 2)}{\left(m^2+2\right)^2} x^5,
$$

was called "residual blue" by Tyndall. It corresponds to a much deeper blue color, since light of longer wavelengths is attenuated even more rapidly than in the case of Rayleigh scattering \[[4](#references) (ch. 10.3)\].

The third term

$$ \tag{19.2y}
	-i \left( \frac{2}{3} \frac{m^2-1}{m^2+2} x^3 \right)^2
$$

is both negative and imaginary. We shall later see that it models *radiation reaction* (or *radiation damping*) -- the effect of the "recoil" force (implied by the [conservation of momentum](https://en.wikipedia.org/wiki/Momentum#Conservation)) experienced by the particle when it "shoots" a scattered photon [[4](#references) (ch. 6.13)\].

#### Light Intensity and Efficiency Factors

Since the frequencies of the electromagnetic radiation associated with visible light are so high, it is practically impossible to measure the instantaneous values of the electromagnetic field \[[8](#references) (ch. 7)\]. For this reason, typical applications of light scattering are concerned with the rate of the flow of electromagnetic energy, which is connected to light intensity via the optical theorem.

Suppose that the host medium is non-absorptive and non-magnetic. Then \\(\kappa = 0, \mu = \mu_0, k\\) is real, and the intensity of the scattered light is given by Eqn. 16.15:

$$ \tag{19.25}
	\Iota_s(\bm{n_s}, \omega)
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta(\omega)}{c} \frac{1}{k^2(\omega)} \left| \bm{E_1}(\bm{n_s}, \omega) \right|^2.
$$

Eqn. 15.? allows us to replace the polarization phasor \\(\bm{E_1}\\) of the scattered field with the product the of the electric far-field scattering dyadic \\(\mathcal{S_{ef}}\\) and the polarization phasor \\(\bm{E_0}\\) of the incident field:

$$ \tag{19.26}
	\Iota_s(\bm{n_s}, \omega)
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta(\omega)}{c} \frac{1}{k^2(\omega)} \left| \mathcal{S_{ef}}(\bm{n_s}, \bm{n_i}, \omega) \cdot \bm{E_0}(\bm{n_i}, \omega) \right|^2.
$$

Both \\(\mathcal{S_{ef}}\\) and \\(\bm{E_0}\\) are coordinate-independent entities. In practice, we shall utilize the convention established in Sec. 15, according to which both the incident and the scattered fields can be described using only two components each. After taking Eqn. 15.14-15.16 into account, Eqn. 19.26 takes the form

$$ \tag{19.27}
	\Iota_s(\theta, \phi, \omega)
	\simeq \frac{\mu\_0^{-1}}{2} \frac{\eta(\omega)}{c} \frac{1}{k^2(\omega)} \left| S(\theta, \phi, \omega)
	\begin{bmatrix}
		E_{x}(\phi, \omega) \cr
		E_{y}(\phi, \omega) \cr
	\end{bmatrix}
	\right|^2,
$$

where \\(S\\) is the scattering matrix.

Since the scattering matrix of a spherical particle (given by Eqn. 19.6) is diagonal, our task is reduced to determination of the squared magnitude of the tangent vector

$$ \tag{19.28}
	\begin{bmatrix}
		E_{\theta} \cr
		E_{\phi} \cr
	\end{bmatrix}
	=
	\begin{bmatrix}
		s_2 & 0 \cr
		0 & s_1 \cr
	\end{bmatrix}
	\begin{bmatrix}
		E_x \cr
		E_y \cr
	\end{bmatrix},
$$

with the transformation of the components of the phasor of the incident wave from the laboratory reference frame to the scattering coordinate system given by Eqn. 15.11:

$$ \tag{19.29}
	\begin{bmatrix}
		E_x(\phi) \cr
		E_y(\phi) \cr
	\end{bmatrix} = R(-\phi)
	\begin{bmatrix}
		E\_{X} \cr
		E\_{Y} \cr
	\end{bmatrix}
	=
	\begin{bmatrix}
		\phantom{-}\cos{\phi} & \sin{\phi} \cr
		-\sin{\phi}           & \cos{\phi} \cr
	\end{bmatrix}
	\begin{bmatrix}
		a\_{X} e^{i \delta_X} \cr
		a\_{Y} e^{i \delta_Y} \cr
	\end{bmatrix}.
$$

As a consequence, the squared magnitude of the tangent vector *depends on the state of the incident light*:

$$ \tag{19.30}
\begin{aligned}
	\begin{vmatrix}
		E_{\theta} \cr
		E_{\phi} \cr
	\end{vmatrix}^2
	&=
	\begin{bmatrix}
		E_x^{\*} &
		E_y^{\*} \cr
	\end{bmatrix}
	\begin{bmatrix}
		s_2^{\*} & 0 \cr
		0 & s_1^{\*} \cr
	\end{bmatrix}
	\begin{bmatrix}
		s_2 & 0 \cr
		0 & s_1 \cr
	\end{bmatrix}
	\begin{bmatrix}
		E_x \cr
		E_y \cr
	\end{bmatrix}
	= |E_x|^2 |s_2|^2 + |E_y|^2 |s_1|^2.
\end{aligned}
$$

Let us consider the special case of *natural light* that has neither a preferred orientation nor a well-defined phase relationship \[[6](#references) (ch. 10.9)\]. The latter assumption makes adding the intensities equivalent to summing up the fields. And, because the elements of the scattering matrix of a spherical particle are independent of the azimuthal angle \\(\phi\\), we can express the random orientation of the electric field vector by averaging the light intensities over all possible orientations of the plane of reference. A straightforward calculation yields

$$ \tag{19.31}
	\frac{1}{2 \pi} \int_{-\pi}^{\pi} \big| E_x(\phi) \big|^2 d\phi =
	\frac{1}{2 \pi} \int_{-\pi}^{\pi} \big| E_y(\phi) \big|^2 d\phi =
	\frac{1}{2} \left( a\_{X}^2 + a\_{Y}^2 \right) =
	\frac{1}{2} |\bm{E_0}|^2.
$$

As a corollary, any expression that depends on the components of the incident field only by means of the integrals of Eqn. 19.31 is valid for an *arbitrary state of the incident light*.

The natural light assumption allows us to directly relate the scattered intensity \\(\Iota_s\\) to the incident irradiance \\(\Epsilon_i\\) (the latter is given by Eqn 16.4) by averaging Eqn. 19.27:

$$ \tag{19.32}
	\Iota_s(\theta, \phi, \omega)
	\simeq \frac{\Epsilon_i(\omega)}{k^2(\omega)}
	\frac{ \left| s_1(\theta, \omega) \right|^2 + \left| s_2(\theta, \omega) \right|^2 }{2},
$$

where the non-zero elements of the scattering matrix are

$$ \tag{19.33}
\begin{aligned}
	s_1(\theta, \omega)
	&= \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)} i
	\big( a_n(\omega) \tau_{1,n}(\theta) + b_n(\omega) \pi_{1,n}(\theta) \big),
	\cr
	s_2(\theta, \omega)
	&= \sum_{n=1}^{\infin} \frac{(2 n + 1)}{n (n + 1)} i
	\big( a_n(\omega) \pi_{1,n}(\theta) + b_n(\omega) \tau_{1,n}(\theta) \big).
\end{aligned}
$$

For illustrative purposes, we may split the intensity into the average of the vertical (or parallel) component \\(\Iota_{\parallel}\\) that corresponds to the electric field oriented along the \\(\theta\\)-axis, and the horizontal (or perpendicular) component \\(\Iota_{\perp}\\) derived from the electric field oriented along the \\(\phi\\)-axis:

$$ \tag{19.34}
\begin{aligned}
	\Iota_{\perp}(\theta, \phi, \omega)
	&\simeq \frac{\Epsilon_i(\omega)}{k^2(\omega)}
	\left| s_1(\theta, \omega) \right|^2,
	\cr
	\Iota_{\parallel}(\theta, \phi, \omega)
	&\simeq \frac{\Epsilon_i(\omega)}{k^2(\omega)}
	\left| s_2(\theta, \omega) \right|^2,
	\cr
	\Iota_s(\theta, \phi, \omega)
	&= \frac{ \Iota_{\perp}(\theta, \phi, \omega) + \Iota_{\parallel}(\theta, \phi, \omega) }{2}.
\end{aligned}
$$

We may observe that the intensity directly depends on the wavenumber \\(k\\) in the host medium, and indirectly on the size parameter \\(x\\), the relative wavenumber \\(m\\), and the reciprocal of the relative permeability \\(u\\), through Eqn. 18.156 of the expansion coefficients \\(a_n\\) and \\(b_n\\). Not all of these parameters are independent; for instance, both \\(m\\) and \\(x\\) are proportional to \\(k\\). In order to make the analysis simple, we shall fix the values of \\(k\\), \\(m\\) and \\(u\\), and only vary the size of the particle \\(a\\), thereby determining the value of the size parameter \\(x\\).

Consider a water droplet suspended in vacuum illuminated by orange light. Assuming the vacuum wavelength \\(\lambda_0 = 600 \text{ nm}\\), the corresponding wavenumber is \\(k = k_0 = 2 \pi / \lambda_0 \approx 0.01 \text{ nm}^{-1}\\). The refractive index of [water](https://refractiveindex.info/?shelf=main&book=H2O&page=Hale) under visible light is \\(\eta + i \kappa \approx 4/3\\), with \\(\mu \approx 1\\). And since the particle is assumed to be located in vacuum, \\(m \approx 4/3\\) and \\(u \approx 1\\).

The smallest possible water droplet is a [single molecule](https://water.lsbu.ac.uk/water/water_molecule.html) of radius \\(a \approx 0.1375 \text{ nm}\\), which corresponds to \\(x = k a \approx 0.00144\\). If we ignore the anisotropy of the molecule and imagine it to be a sphere, we can apply the Lorenz-Mie-Debye theory to determine the intensity of the scattered light.

{{< figure src="/img/water_molecule.svg" caption="*Figure N: Linear plot of the intensity of light scattered by a water droplet of size \\(x = 0.00144\\) (solid line). The vertical and the horizontal intensities are plotted using the dashed and the dotted lines, respectfully.*" >}}

The resulting distribution of scattered light is symmetric with respect to the vertical plane containing the incident electric field vector; and it is, of course, also symmetric around the direction of propagation of the incident light (pointing from left to right). At the same time, while the horizontal component is isotropic, the value of the vertical component is zero at the angle of 90 degrees. Comparison with the plots of the polar functions (Fig. N and M) shows that only the functions of the first order are present, indicating that the intensity of molecular scattering is dominated by the contribution of first electric partial wave, with the molecule acting as an electric dipole.

Once the size of the droplet approaches the wavelength of the incident light \\((x \to 1)\\), the Mie effect becomes apparent: both the horizontal and the vertical components begin to compress and shift towards the forward direction.

{{< figure src="/img/water_x1.svg" caption="*Figure N: Linear plot of the intensity of light scattered by a water droplet of size \\(x = 1\\) (solid line). The vertical and the horizontal intensities are plotted using the dashed and the dotted lines, respectfully.*" >}}

As the size of the particle increases, the two components continue to converge towards their average.

{{< figure src="/img/water_x2.svg" caption="*Figure N: Linear plot of the intensity of light scattered by a water droplet of size \\(x = 2\\) (solid line). The vertical and the horizontal intensities are plotted using the dashed and the dotted lines, respectfully.*" >}}

For particles much larger than the wavelength \\((x \gg 1)\\), we can observe formation of the off-center peak structure around the forward direction. The shape and the complexity of this structure rapidly changes with both \\(m\\) and \\(x\\), leading to a colorful [dispersive](https://en.wikipedia.org/wiki/Dispersion_(optics)) effect called *polychroism*.

{{< figure src="/img/water_x10.svg" caption="*Figure N: Linear plot of the intensity of light scattered by a water droplet of size \\(x = 10\\).*" >}}

If the refractive index of the particle is large \\((|m| \gg 1)\\), for small scattering angles, the distribution of peaks can be predicted using the [Huygens–Fresnel principle](https://en.wikipedia.org/wiki/Huygens%E2%80%93Fresnel_principle), according to which, given an opaque screen with a hole, the total field in the far zone is given by the integral of spherical wavelets formed in the plane of the aperture \[[4](#references) (ch. 3.1), [5](#references) (vol. I, ch. 30), [6](#references) (ch. 8.2, 14.5.2.c)\]. Connection with the present case of a spherical obstacle can be made by application of [Babinet's principle](https://en.wikipedia.org/wiki/Babinet%27s_principle), which states that, except for the overall intensity, the interference patterns of an aperture and an opaque body (a complementary screen) of the same size and shape are identical.

[Insert picture here]

The Huygens–Fresnel principle indicates an apparent failure of the obstacle to block the incident light; it is then said that the wave nature of light causes it to be [diffracted](https://en.wikipedia.org/wiki/Diffraction) (or bent) around the object.

For a typical [cloud droplet](https://ntrs.nasa.gov/citations/20160014659) of radius \\(a \approx 10 \text{ μm}\\) (which corresponds to \\(x \approx 104.7\\)), the intensity of forward scattering is so high that we must use a logarithmic plot in order to discern the details of the angular distribution of scattered light.

{{< figure src="/img/water_cloud.svg" caption="*Figure N: Logarithmic plot of the intensity of light scattered by a water droplet of size \\(x = 104.7\\).*" >}}

A polar plot can hardly convey how large the forward peak is. A rectangular plot shows that there is a 5 order-of-magnitude difference between the highest peak and the lowest trough.

{{< figure src="/img/water_cloud_intensity.svg" caption="*Figure N: Plot of the intensity of light scattered by a water droplet of size \\(x = 104.7\\). The horizontal axis represents the zenith angle of the direction of the sensor.*" >}}

How large is the contribution of the secondary components of the scattering function? We can answer this question by plotting the total amount of power scattered by the particle as a function of the solid angle, where a small solid angle corresponds to a tiny sensor located directly in front of the droplet, and \\(4 \pi\\) represents a hypothetical spherical sensor that completely surrounds the particle.

{{< figure src="/img/water_cloud_power.svg" caption="*Figure N: Plot of the fraction of the scattered power for a water droplet of size \\(x = 104.7\\). The horizontal axis represents the solid angle subtended by the sensor.*" >}}

It is convenient to normalize the graph by the total amount of power scattered by the droplet. This makes it easy to see that, while around 50% of the scattered power is concentrated in the forward direction, the remaining 50% is channeled elsewhere.

Asymmetry of the angular distribution of the scattered light can be characterized in several different ways. For instance, we could take the intensity of light scattered in the forward direction and divide it by the corresponding intensity in the backward direction.

{{< figure src="/img/water_intensity_ratio.svg" caption="*Figure N: Plot of the ratio of the intensities of light scattered by a water droplet in the forward versus the backward direction. The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

As it turns out, the result is a strongly varying function of \\(x\\). This can be explained by the presence of the off-center peak structure for \\(x \gg 1\\), which causes the scattered intensity in the backward direction to rapidly oscillate.

There is a more robust parameter used to characterize the asymmetry of the angular distribution of the scattered light called the *mean cosine*. In order to compute it, we must first define the corresponding [probability density function](https://en.wikipedia.org/wiki/Probability_density_function)

$$ \tag{19.35}
	p_s(\theta, \phi, \omega)
	= \frac{\Iota_s(\theta, \phi, \omega)}{\Phi_s(\omega)}
$$

on the surface of a unit sphere. The former is directly related to the [phase function](https://glossary.ametsoc.org/wiki/Phase_function)

$$ \tag{19.36}
	f_p(\theta, \phi, \omega)
	= 4 \pi p_s(\theta, \phi, \omega).
$$

Given the probability density function, the expected value of \\(\cos{\theta}\\) can be calculated as

$$ \tag{19.37}
	g(\omega)
	= \braket{\cos{\theta}}
	= \int_{-\pi}^{\pi} \int_{0}^{\pi} p_s(\theta, \phi, \omega) \cos{\theta} \sin{\theta} d\theta d\phi.
$$

If the angular distribution is *isotropic* (that is, invariant with respect to direction), then \\(f_p = 1\\) and \\(g = 0\\). For spherical particles, due to the Mie effect, we should expect \\(g \to 1\\) as \\(x \to \infty\\).

In order to compute the probability density function, we need to evaluate both the scattered intensity and the scattered power. The latter can be obtained by combining Eqn. 16.14 and 16.15:

$$ \tag{19.38}
	\Phi_s(\omega)
	= \int_{-\pi}^{\pi} \int_{0}^{\pi} \Iota_s(\theta, \phi, \omega) \sin{\theta} d\theta d\phi.
$$

In the particular case of spherical particles, we may utilize Eqn. 19.27-19.31 to obtain

$$ \tag{19.39}
\begin{aligned}
	\Phi_s(\omega)
	&= \frac{\mu\_0^{-1}}{2} \frac{\eta(\omega)}{c} \frac{1}{k^2(\omega)}
	\int_{-\pi}^{\pi} \int_{0}^{\pi}
	\left(
	\big| s_1(\theta, \omega) \big|^2 \big| E_y(\phi, \omega) \big|^2 + \big| s_2(\theta, \omega) \big|^2 \big| E_x(\phi, \omega) \big|^2
	\right)
	\sin{\theta} d\theta d\phi
	\cr
	&= \frac{\mu\_0^{-1}}{2} \frac{\eta(\omega)}{c} \frac{2 \pi}{k^2(\omega)} \big| \bm{E_0}(\omega) \big|^2
	\int_{0}^{\pi} \frac{ \left| s_1(\theta, \omega) \right|^2 + \left| s_2(\theta, \omega) \right|^2 }{2} \sin{\theta} d\theta
	\cr
	&= \frac{2 \pi}{k^2(\omega)} \Epsilon_i(\omega)
	\int_{0}^{\pi} \frac{ \left| s_1(\theta, \omega) \right|^2 + \left| s_2(\theta, \omega) \right|^2 }{2} \sin{\theta} d\theta,
\end{aligned}
$$

where we choose to express the squared magnitudes of the elements of the scattering matrix by a double series:

$$ \tag{19.40}
\begin{aligned}
	\left| s_1(\theta, \omega) \right|^2
	&= \sum_{l=1}^{\infin} \sum_{n=1}^{\infin} \frac{(2 l + 1)}{l (l + 1)} \frac{(2 n + 1)}{n (n + 1)}
	\big( a_l^{\*} \tau_{1,l}(\theta) + b_l^{\*} \pi_{1,l}(\theta) \big) \big( a_n \tau_{1,n}(\theta) + b_n \pi_{1,n}(\theta) \big),
	\cr
	\left| s_2(\theta, \omega) \right|^2
	&= \sum_{l=1}^{\infin} \sum_{n=1}^{\infin} \frac{(2 l + 1)}{l (l + 1)} \frac{(2 n + 1)}{n (n + 1)}
	\big( a_l^{\*} \pi_{1,l}(\theta) + b_l^{\*} \tau_{1,l}(\theta) \big) \big( a_n \pi_{1,n}(\theta) + b_n \tau_{1,n}(\theta) \big).
\end{aligned}
$$

Normally, it is simpler to evaluate a single series and then compute the magnitude of the resulting complex number. However, the somewhat complicated expression given above allows us to efficiently eliminate the outer integral by considering the identities given by Eqn. 18.69 and 18.70, yielding

$$ \tag{19.41}
	\int_{0}^{\pi} \frac{ \left| s_1(\theta, \omega) \right|^2 + \left| s_2(\theta, \omega) \right|^2 }{2} \sin{\theta} d\theta
	= \sum_{n=1}^{\infin} (2 n + 1) \left( |a_n|^2 + |b_n|^2 \right).
$$

Thus, the amount of power scattered by a spherical particle is

$$ \tag{19.42}
	\Phi_s(\omega)
	= \frac{2 \pi}{k^2(\omega)} \Epsilon_i(\omega)
	\sum_{n=1}^{\infin} (2 n + 1) \left( |a_n|^2 + |b_n|^2 \right).
$$

As we return to Eqn. 19.37, we face a slightly different integral with an additional \\(\cos{\theta}\\) factor:

$$ \tag{19.43}
\begin{aligned}
	g(\omega)
	&= \int_{-\pi}^{\pi} \int_{0}^{\pi} \frac{I_s(\theta, \phi, \omega)}{\Phi_s(\omega)} \cos{\theta} \sin{\theta} d\theta d\phi
	\cr
	&= \frac{2}{\Phi_s(\omega)}
		\int_{0}^{\pi} \frac{ \left| s_1(\theta, \omega) \right|^2 + \left| s_2(\theta, \omega) \right|^2 }{2} \cos{\theta} \sin{\theta} d\theta.
\end{aligned}
$$

We may substitute the double series of Eqn. 19.40 once again; however, this time, the additional cosine term in the resulting integrals requires application of a different set of orthogonality properties, namely, Eqn. 18.6? and 18.7?, yielding

$$ \tag{19.44}
\begin{aligned}
	& \int_{0}^{\pi} \frac{ \left| s_1(\theta, \omega) \right|^2 + \left| s_2(\theta, \omega) \right|^2 }{2} \cos{\theta} \sin{\theta} d\theta
	\cr
	&= \sum_{n=1}^{\infin} \frac{2 (2 n + 1)}{n (n + 1)} \mathcal{Re} \lbrace a_n b_n^{\*} \rbrace
	+ \sum_{n=1}^{\infin} \frac{2 n (n+2)}{n+1}
	\mathcal{Re} \lbrace a_n a_{n+1}^{\*} + b_n b_{n+1}^{\*} \rbrace.
\end{aligned}
$$

The combination of Eqn. 19.41-19.44 produces the analytic expression of the mean cosine:

$$ \tag{19.45}
\begin{aligned}
	g(\omega)
	&= \frac{\sum_{n=1}^{\infin} \frac{2 (2 n + 1)}{n (n + 1)} \mathcal{Re} \lbrace a_n b_n^{\*} \rbrace
	+ \sum_{n=1}^{\infin} \frac{2 n (n+2)}{n+1}
	\mathcal{Re} \lbrace a_n a_{n+1}^{\*} + b_n b_{n+1}^{\*} \rbrace}{\sum_{n=1}^{\infin} (2 n + 1) \left( |a_n|^2 + |b_n|^2 \right)}.
\end{aligned}
$$

Eqn. 19.45 allows us to plot the mean cosine of a water droplet as a function of its size.

{{< figure src="/img/water_mean_cos.svg" caption="*Figure N: Plot of the mean cosine of a water droplet. The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

Up to \\(x \approx 5\\), the value of the mean cosine increases in line with the prediction based on the Mie effect. For larger particles, the size parameter \\(x \gg 1\\), and, as we have already seen, this condition leads to formation of the off-center peak structure around the forward direction. Initially, its magnitude is fairly significant in comparison with the size of the forward peak, which explains the dip in the plot at \\(x \approx 10\\). For particles larger still, the intensity of light scattered in the forward direction grows exponentially (see Fig. N), while most of the secondary components grow at a slower pace, which leads to the (asymptotically) rising value of the mean cosine.

{{< figure src="/img/water_intensity.svg" caption="*Figure N: Plot of the intensity of light scattered by a water droplet in the forward direction. The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

The prominent wave pattern featured in the plot of Fig. N can be explained by the fact that the mean cosine is inversely proportional to the scattered power. The latter is most intuitively visualized by plotting the dimensionless scattering efficiency factor

$$ \tag{19.46}
\begin{aligned}
	& Q_s(\omega)
	= \frac{\Phi_s(\omega)}{\Phi_i(\omega)}
	= \frac{2}{x^2}
	\sum_{n=1}^{\infin} (2 n + 1) \left( |a_n|^2 + |b_n|^2 \right).
\end{aligned}
$$

defined in Eqn. 16.38.2.

{{< figure src="/img/water_sca_eff.svg" caption="*Figure N: Plot of the scattering efficiency factor of a water droplet. The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

We can observe that the scattering efficiency is an oscillating function of the particle size. In addition to the large quasi-periodic *interference structure* that is generated by *refracted* light rays \\((n < x)\\), the curve exhibits a fine *ripple structure* that is typically attributed to *resonating* [surface waves](https://en.wikipedia.org/wiki/Surface_wave) \\((n > x)\\) traveling along the sphere's boundary \[[4](#references) (ch. 17.3), [23](#references), [24](#references)\]. Ripple is not present in plots of absorptive particles, since the amplitude of a surface wave is easily attenuated, primarily because the length of its path (its *orbit*) can easily exceed the diameter of the sphere. The partial waves presently not accounted for \\((n \approx x)\\) are generated by rays hitting the edges of (or *tunneling* through) the sphere; they constitute diffracted light \[[16](#references) (ch. 8)\].

Now, suppose that we hold the radius \\(a\\) of the particle and its refractive index \\(m\\) fixed, and vary the angular frequency \\(\omega \propto x\\) of the incident light instead. This allows us to reinterpret the same plot of the scattering efficiency factor in a new way. We can divide it into three consecutive regions based on the combination of the size parameter and the frequency response. Molecules and small particles belong to the first region (\\(x < 6\\) on Fig. N), where the total amount of the scattered light grows with the frequency, so a larger portion of the incident blue light is scattered (in all directions) relative to red light. Medium-sized particles in the adjacent region (\\(6 \le x < 12\\) on Fig. N) exhibit the opposite behavior. Finally, large particles (\\(x \gg 12\\) on Fig. N) are not particularly selective: if they are illuminated by white light, the scattered light, *on average*, remains mostly white.

If the particle is non-absorptive, then, from Eqn. 16.38.1, it follows that the scattering and the extinction efficiencies are identical:

$$ \tag{19.47}
	Q_a = 0
	\quad
	\iff
	\quad
	Q_e = Q_s.
$$

The general expression of the latter is given by Eqn. 16.38.3 and, for the particular case of a spherical particle, by the combination of Eqn. 16.43.1, 19.?.1, and 19.33:

$$ \tag{19.48}
\begin{aligned}
	Q_e(\omega)
	= \frac{\Phi_a(\omega) + \Phi_s(\omega)}{\Phi_i(\omega)}
	&= -\frac{2}{x^2} \sum_{n=1}^{\infin} (2 n + 1) \mathcal{Re} \lbrace a_n + b_n \rbrace
	\cr
	&= \frac{2}{x^2} \sum_{n=1}^{\infin} (2 n + 1) \mathcal{Im} \left\lbrace \frac{a_n}{i} + \frac{b_n}{i} \right\rbrace.
\end{aligned}
$$

If you are not very familiar with the subject, then comparison of Eqn. 19.46 with Fig. N may suggest that something is wrong, since the amount of power scattered by a particle may significantly exceed the amount of incident power. Since the scattered power is also featured in the expression of the extinction efficiency factor, the latter exhibits the same problem, which indicates that the amount of power "removed" from the incident beam is larger than the amount of power geometrically intercepted by the particle. This surprising behavior is known as the [extinction paradox](https://en.wikipedia.org/wiki/Extinction_paradox).

This phenomenon is not limited to a particular composition, or a certain shape of a particle. However, it does strongly depend on the size. Why is that the case?

First, let us examine the expression of the incident power \\(\Phi_i\\) given by Eqn. 16.37. It is computed as the product of the geometrical cross-section \\(C_g\\) of the particle and the irradiance \\(\Epsilon_i\\) of the incident plane wave. This formulation is necessitated by the fact that a plane wave has an infinite spatial extent, so its total power and energy are also infinite.

While this definition of the incident power is intuitive, it assumes that the incident field not geometrically intercepted by the particle does not influence its scattered field in any way. Strictly speaking, that is not true; electromagnetic scattering is a global process, and the value of the electromagnetic field at any given point depends on all values of the electric and the magnetic fields everywhere else. "Cutting it too short" around the particle may reduce or eliminate certain edge effects, while at the same time introducing new unexpected behavior caused by the irregular shape of the wavefront.

It is reasonable to believe that, for a fixed wavelength, the relative contribution of edge effects towards the total amount of scattered power should diminish as the size of the particle increases, since the cross-sectional area grows faster than the circumference. Fig. N shows that, while that is true to some degree, \\(Q_s \to 2\\) as \\(|m x| \to \infin\\), so it does not provide a complete explanation.

Now, suppose that \\(x\\) is very large, so that \\(\Phi_i\\) provides a reasonably accurate estimate. Since the paradox manifests itself regardless of the geometry and the optical properties of the particle, let us imagine the particle to be a flat mirror. It perfectly reflects the incident light, contributing a factor of \\(1\\) to \\(Q_s\\). As the mirror is large, it also casts a shadow, which indicates that the value of the electric field behind the mirror is \\(0\\). If we recall that the incident field is not modified by the addition of the scattering object in any way, this leads to the conclusion that the scattered field behind the mirror must have the same magnitude and the opposite phase in order to destructively interfere with and completely cancel the incident field. Thus, \\(Q_s = 2\\), which explains the paradox for a flat mirror \[[21](#references), [22](#references)\].

Since, in general, a particle with \\(|m x| \to \infin\\) does not necessarily cast a well-defined shadow, we require an alternative explanation. Suppose that \\(|m| \to \infin\\), and \\(x\\) is not too small. This implies that the particle is either a *perfect dielectric reflector* \\((\epsilon, \eta \to \infin)\\) or a *perfect electric conductor* \\((\sigma, \kappa \to \infin)\\). According to Eqn. 18.157, a particle of the first kind is able to sustain a fairly strong internal field, while a particle of the second kind makes it vanish. In both cases, the particle appears mirror-like, and, in order for the value of the internal field to be maintained, the *extinction theorem* (see Sec. 10) requires the scattered field to continually extinguish the incident field inside the particle. Accounting for these two facts once again leads to \\(Q_s = 2\\) \[[20](#references), [21](#references)\].

Note that, unlike the scattered field, the total field has no apparent issues with energy conservation, since its power (change in the amount of energy over time) is zero in inaccessible areas. This underscores the importance of understanding the fact that only the total field is an actual physical entity, while the incident and the scattered fields are just mathematical constructs.

## III. Approximate Methods for Practical Use

### Basics of Molecular Light Scattering

All matter is made of [atoms](https://en.wikipedia.org/wiki/Atom). When certain types of atoms end up in close proximity to one another, [attractive electrical forces](https://en.wikipedia.org/wiki/Chemical_bond) cause them to aggregate into small clusters called [molecules](https://en.wikipedia.org/wiki/Molecule). Molecules are the smallest building blocks of a [chemical substance](https://en.wikipedia.org/wiki/Chemical_substance), which is a form of matter that has a definite chemical composition and characteristic properties. Theoretically, if the state and the structure of matter were known in sufficient detail, molecular light scattering could be used to explain the appearance of almost everything we see in nature.

All scattering processes can be broadly classified in two categories: [elastic](https://en.wikipedia.org/wiki/Elastic_scattering) and [inelastic](https://en.wikipedia.org/wiki/Inelastic_scattering). A typical example of *elastic scattering* is an idealized rubber ball perfectly bouncing off a hard surface; we say that, in the center-of-mass frame, the kinetic energy of the ball is conserved. The description of *elastic light scattering* is even simpler: since, as far as we know, [photons](https://en.wikipedia.org/wiki/Photon) are massless, the photon's energy \\(\mathcal{E_p}\\) must be conserved. If, using the [uncertainty principle](https://en.wikipedia.org/wiki/Uncertainty_principle), we associate the photon with a plane wave, then the [Planck-Einstein relation](https://en.wikipedia.org/wiki/Planck_relation)

$$ \tag{20.1}
	\mathcal{E_p} = \hbar \omega
$$

tells us that the angular frequency \\(\omega\\) must remain unchanged.

That is not the case for *inelastic light scattering*, which allows an atom (or a molecule) to absorb a photon \\(\mathcal{E_p}\\), only to emit a different photon \\(\mathcal{E_p'}\\) at a later point in time. The law of conservation of energy requires the total amount of energy \\(\mathcal{E}\\) in the atom-photon system to be the same before and after the scattering event:

$$ \tag{20.2}
	\mathcal{E_a} + \mathcal{E_p} = \mathcal{E} = \mathcal{E_a'} + \mathcal{E_p'}.
$$

Therefore, if scattering alters the energy of the atom \\((\mathcal{E_a} \neq \mathcal{E_a'})\\), the radiation undergoes a frequency shift

$$ \tag{20.3}
\begin{aligned}
	\Omega
	= \frac{\mathcal{E_p'} - \mathcal{E_p}}{\hbar}
	= \frac{\mathcal{E_a} - \mathcal{E_a'}}{\hbar}
\end{aligned}
$$

that can be either positive or negative, depending on the initial and final states of the system.

Classical electrodynamics represents matter by a charge distribution that behaves a certain way. In particular, if we consider an atom or a small molecule, to a good approximation, light-matter interaction is limited to the displacement of the electron cloud relative to the center of mass of the molecule, which is characterized by the induced dipole moment

$$ \tag{20.4}
	\bm{p}(V_m, \omega) =
	\mathcal{\Alpha_m}(V_m, \omega) \cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega),
$$

where \\(\mathcal{\Alpha_m}\\) is the molecular polarizability dyadic (see Eqn. 10.21).

In addition to forced motion of the electron cloud, a diatomic molecule may experience vibration of the nuclei relative to the center of mass of the molecule \[[30](#references), ch. \\(\mathrm{A\_V}\\)\]. Since the nuclei are much heavier than the electrons, they oscillate at a lower frequency, effectively [modulating the amplitude](https://en.wikipedia.org/wiki/Amplitude_modulation) of the oscillation of the dipole moment by a factor of

$$ \tag{20.5}
	\cos(\Omega t)
	= \frac{e^{i \Omega t} + e^{-i \Omega t}}{2}.
$$

If the applied field \\(\bm{E_m}\\) is time-harmonic,

$$ \tag{20.6}
\begin{aligned}
	\bm{p}(V_m, \omega) e^{i \omega t}
	&= \big( \mathcal{\Alpha_m}(V_m, \omega) + 2 \mathcal{\Beta_m}(V_m, \omega) \cos(\Omega t) \big)
	\cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega) e^{i \omega t}
	\cr
	&= \mathcal{\Alpha_m}(V_m, \omega) \cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega) e^{i \omega t}
	\cr
	&+ \mathcal{\Beta_m}(V_m, \omega)  \cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega) e^{i (\omega - \Omega) t}
	\cr
	&+ \mathcal{\Beta_m}(V_m, \omega)  \cdot \epsilon_0 \bm{E_m}(\bm{r_0}, \omega) e^{i (\omega + \Omega) t}
	\cdot
\end{aligned}
$$

Eqn. 20.6 constitutes the classical description of molecular light scattering, where the first term corresponds to *elastic* [Rayleigh scattering](https://en.wikipedia.org/wiki/Rayleigh_scattering), the second -- to *inelastic Stokes-Raman scattering*, and the third -- to *inelastic anti-Stokes-Raman scattering*. [Raman scattering](https://en.wikipedia.org/wiki/Raman_scattering) is quantum-mechanical in nature, so its derivation is beyond the scope of this article. However, if the reader were to obtain the values of \\(\mathcal{\Beta_m}\\) and \\(\Omega\\) elsewhere \[[29](#references)\], extending the theory presented below should be fairly straightforward.

### Light Scattering by Gas Molecules

We shall begin our investigation of light-matter interactions with the simplest state of matter -- [gas](https://en.wikipedia.org/wiki/Gas). Specifically, let us consider a *group of gas molecules* with the following (fairly typical) properties:

1. The molecules are small compared to the wavelength of light. If \\(a\\) is the largest linear dimension of the molecule (e.g. its radius), and \\(k = k_0 = \omega / c\\) is the vacuum wavenumber, then \\(x = k a \ll 1\\).
2. The molecules are non-magnetic, and not too strongly absorbing or scattering. If \\(m\\) is the refractive index of the molecule (not to be confused with the refractive index of the gas as a medium), then \\(|m x| \ll 1\\).
3. The average distance between the molecules is large.
4. The number \\(N\\) of molecules within the group is not too great.
5. The volume \\(V\\) containing the group of molecules is large compared to the wavelength of light. If \\(L\\) is the smallest linear dimension of the volume (e.g. its length), then \\(k L \gg 1\\).
6. The molecules are randomly distributed. This is usually true due to [Brownian motion](https://en.wikipedia.org/wiki/Brownian_motion).

The properties #1 and #2 suggest that we may represent a gas molecule by an electric dipole (cf. Sec. 12).

The properties #2-4, taken together, imply that an electromagnetic wave incident on the volume \\(V\\) is not significantly impeded (or otherwise altered) by the presence of the group of molecules located within. This makes makes the single scattering approximation (cf. Eqn. 17.0) applicable, which allows us to assume that each dipole is driven exclusively by the incident field (or, in other words, the polarization \\(\bm{P}\\) is vanishingly small outside the volume occupied by any given dipole, cf. Sec. 10). That also allows us to replace the microscopic field \\(\bm{E_m}\\) acting on the molecule in Eqn 10.21 with the incident field \\(\bm{E_0}\\) \[[4](#references) (ch. 6.22)\]:

$$ \tag{21.1}
	\bm{p}(V_m, \omega) \approx \mathcal{\Alpha_m}(V_m, \omega) \cdot \epsilon_0 \bm{E_0}(\bm{n_i}, \omega).
$$

The property #6 allows us to invoke the ergodic assumption, making the results of Sec. 17 applicable. Once we also take the property #5 into account, the amount of light scattered or absorbed by the particle group (as well as the associated radiative transfer coefficients) can be determined by computing simple weighted averages according to Eqn. 17.56.

In order to apply the results of Sec. 17, we must tailor them to our particular use case. The expression of the scattered far-field of an arbitrary particle is given by Eqn. 15.6.1:

$$ \tag{21.2}
	\bm{E_s}(\bm{r}, \omega)
	\simeq \frac{e^{i k_0(\omega) r}}{k_0(\omega) r} \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \cdot \bm{E_0}(\bm{n_i}, \omega).
$$

The corresponding formula for a dipole is found in Eqn. 13.5:

$$ \tag{21.3}
	\bm{E_s}(\bm{r}, \omega)
	\simeq \frac{e^{i k_0(\omega) r}}{k_0(\omega) r} \frac{k_0^3(\omega)}{4 \pi}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot \mathcal{\Alpha_m}(V_m, \omega)
	\cdot \bm{E_0}(\bm{n_i}, \omega).
$$

Thus, the scattering dyadic \\(\mathcal{S\_{ef}}\\) and the molecular polarizability dyadic \\(\mathcal{\Alpha\_m}\\) are related as follows:

$$ \tag{21.4}
	\mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega)
	\simeq \frac{k_0^3(\omega)}{4 \pi}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot \mathcal{\Alpha_m}(V_m, \omega).
$$

The first term is the power law of Rayleigh scattering; the second term ensures that the electromagnetic field is transverse; the last term encodes the orientation and the "responsiveness" of the molecule with respect to the applied electromagnetic field.

Since a typical molecule is aspherical, \\(\mathcal{\Alpha\_m}\\) is a dyadic rather than a scalar. In a Cartesian coordinate system, the polarizability dyadic can be represented by a matrix

$$ \tag{21.5}
	\mathcal{\Alpha_m}(V_m, \omega) =
	\begin{bmatrix}
		\alpha_{11} & \alpha_{12} & \alpha_{13} \cr
		\alpha_{21} & \alpha_{22} & \alpha_{23} \cr
		\alpha_{31} & \alpha_{32} & \alpha_{33} \cr
	\end{bmatrix}.
$$

Let us assume that the matrix is (at least approximately) [diagonalizable](https://en.wikipedia.org/wiki/Diagonalizable_matrix), so that it can be written in the form

$$ \tag{21.6}
	\mathcal{\Alpha_m}(V_m, \omega) =
	X \Lambda X^{-1}
	=
	\begin{bmatrix}
		\bm{v_1} & \bm{v_2} & \bm{v_3} \cr
	\end{bmatrix}
	\begin{bmatrix}
		\alpha_1 & 0          & 0         \cr
		0         & \alpha_2 & 0          \cr
		0         & 0         & \alpha_3 \cr
	\end{bmatrix}
	\begin{bmatrix}
		\bm{v_1} & \bm{v_2} & \bm{v_3} \cr
	\end{bmatrix}^{-1},
$$

where \\(\bm{v_1}, \bm{v_2}, \bm{v_3}\\) are the eigenvectors, and \\(\alpha_1, \alpha_2, \alpha_3\\) are the corresponding eigenvalues.

Let us further assume that the eigenvectors are real and orthogonal (the eigenvalues remain complex). Then

$$ \tag{21.7}
	\mathcal{\Alpha_m}(V_m, \omega) =
	X \Lambda X^{T}
	=
	\begin{bmatrix}
		\bm{v_1} & \bm{v_2} & \bm{v_3} \cr
	\end{bmatrix}
	\begin{bmatrix}
		\alpha_1 & 0          & 0         \cr
		0         & \alpha_2 & 0          \cr
		0         & 0         & \alpha_3 \cr
	\end{bmatrix}
	\begin{bmatrix}
		\bm{v_1} \cr \bm{v_2} \cr \bm{v_3} \cr
	\end{bmatrix}.
$$

This geometric configuration[^24] corresponds to an [ellipsoid](https://en.wikipedia.org/wiki/Ellipsoid) of length \\(2 \alpha_1\\), height \\(2 \alpha_2\\), and width \\(2 \alpha_3\\).

[^24]: In general, a single molecule is neither spherical nor ellipsoidal. However, if we consider a measurement of light scattered by a large group of randomly oriented molecules, due to the  averaging process described in Sec. 17, it is fairly reasonable to assume that the same measurement can be reproduced by an appropriately chosen distribution of randomly oriented ellipsoids.

According to Eqn. 21.1, whenever the electric field vector \\(\bm{E_0}\\) points along one the principal axes \\(\bm{v_n}\\) of the molecule \\((n=1,2,3)\\), then so does the induced dipole moment \\(\bm{p_n}\\):

$$ \tag{21.8}
	\bm{p_n}
	= \alpha_n (\epsilon_0 \bm{E_0} \cdot \bm{v_n}) \bm{v_n}.
$$

However, in general, the dipole moment \\(\bm{p}\\) is not be parallel to the electric field unless \\(\alpha_1 = \alpha_2 = \alpha_3\\):

$$ \tag{21.9}
	\bm{p}(V_m, \omega)
	= \sum_n \bm{p_n}
	= \sum_n \alpha_n (\epsilon_0 \bm{E_0} \cdot \bm{v_n}) \bm{v_n}.
$$

This *anisotropic* dipole model was introduced by Rayleigh, Cabannes, and King to explain the experimental observations of light scattered by gases and liquids \[[2](#references) (ch. 18), [4](#references) (ch. 6.32)\]. In order to use it, we must specify three polarizability parameters \\(\alpha_1, \alpha_2, \alpha_3\\). Their values can be obtained in several different ways:

1. molecular polarizability can be determined experimentally, by measuring ... \[TODO: add ref\];
2. an atom or a small molecule[^25] can be modeled quantum-mechanically \[TODO: add ref\];
3. one can use the classical model of a dielectric ellipsoid characterized by its dimensions and permittivity \[[17](#references) (ch. 3.27)\].

[^25]: This may prove to be too challenging for large molecules, but, at the same time, an accurate description of light-matter interaction may also require accounting for contribution of additional multipole terms.

Let us discuss the third method in more detail. According to Eqn. 10.18, the dipole moment \\(\bm{P}\\) is a volume integral of polarization \\(\bm{P}\\):

$$ \tag{21.10}
	\bm{p}(V_m, \omega) = \int\_{V_m} \bm{P}(\bm{r}, \omega) dV_m.
$$

The latter is defined in terms of the dielectric constant \\(\epsilon_r = m^2\\) by Eqn. 10.26:

$$ \tag{21.11}
	\bm{P}(\bm{r}, \omega)
	= \big( m^2(\bm{r}, \omega) - 1 \big) \epsilon_0 \bm{E}(\bm{r}, \omega).
$$

Since \\(x \ll 1\\), the ellipsoid is effectively located in a uniform field \\(\bm{E_0}\\). In addition, \\(|m x| \ll 1\\) implies that the internal field \\(\bm{E}\\) is also uniform. This makes the electrostatic field approximation applicable:

$$ \tag{21.12}
	\bm{p}(V_m, \omega)
	= \epsilon_0 \bm{E} \int\_{V_m} \big( m^2 - 1 \big) dV_m
	= V_m \big( \braket{m^2} - 1 \big) \epsilon_0 \bm{E},
$$

where the volume of the ellipsoid \\(V_m\\) of length \\(2 a_1\\), height \\(2 a_2\\), and width \\(2 a_3\\) is given by the formula

$$ \tag{21.13}
	V_m = \frac{4}{3} \pi a_1 a_2 a_3.
$$

We shall simplify the notation by writing \\(m^2\\) in place of \\(\braket{m^2}\\) going forward.

Eqn. 21.9 and 21.12 describe the same physical quantity. Thus, we can calculate the values of the polarizability parameters \\(\alpha_1, \alpha_2, \alpha_3\\) once we obtain the value of the internal field \\(\bm{E}\\). The latter can be determined by solving the electrostatic boundary value problem for a dielectric ellipsoid in a uniform electric field (which is somewhat simpler than the electrodynamic boundary value problem of the Lorenz-Mie-Debye theory). The process is fairly complicated, and the general solution is expressed in terms of ellipsoidal harmonics. A detailed discussion of the  mathematical method in question is outside of the scope of this article, so we shall simply quote the results \[[17](#references) (ch. 3.27)\].

The solution is given in terms of the radial function

$$ \tag{21.14}
	R(s) = \sqrt{(s + a_1^2) (s + a_2^2) (s + a_3^2)}
$$

and the [elliptic integrals](https://en.wikipedia.org/wiki/Elliptic_integral)

$$ \tag{21.15}
	A_n = \int_{0}^{\infin} \frac{ds}{(s + a_n^2) R(s)},
$$

with \\(n=1,2,3\\), that satisfy

$$ \tag{21.16}
	\sum_n A_n = \frac{2}{a_1 a_2 a_3}.
$$

{{< figure src="/img/elliptic_int.svg" caption="*Figure 21.1: Plot of \\(A_1\\) defined by Eqn. 21.15 as a function of \\(a_1\\) for \\(a_2 = a_3 = 1\\).*" >}}

The value of the component of the internal electric field pointing along one the principal axes of the molecule is

$$ \tag{21.17}
	E_n
	= \bm{E} \cdot \bm{v_n}
	= \frac{\bm{E_0} \cdot \bm{v_n}}{1 + \left( m^2 - 1 \right) \frac{A_n}{\sum_n A_n} }.
$$

In case the ellipsoid is just a regular sphere, \\(a_1 = a_2 = a_3 = a\\), and

$$ \tag{21.18}
	\frac{A_n}{\sum_n A_n} = \frac{1}{3}.
$$

{{< figure src="/img/int_field.svg" caption="*Figure 21.2: Plot of \\(E_1\\) defined by Eqn. 21.17 as a function of \\(a_1\\) for \\(a_2 = a_3 = 1\\) and \\(m = 4/3\\).*" >}}

It is convenient to express the value of the internal field using the so-called *depolarization factors* \\(L_n\\), such that

$$ \tag{21.19}
	E_n = \left( \bm{E_0} - L_n \bm{P} \right) \cdot \bm{v_n}.
$$

By combining Eqn. 21.11, 21.17, and 21.19, we obtain

$$ \tag{21.20}
	L_n = \frac{1}{\epsilon_0} \frac{A_n}{\sum_n A_n}.
$$

Evidently, for any ellipsoid,

$$ \tag{21.21}
	\sum_n L_n = \frac{1}{\epsilon_0}.
$$

When [Gaussian units](https://en.wikipedia.org/wiki/Gaussian_units) are used, the depolarization factors sum up to \\(1\\) \[[4](#references) (ch. 6.32)\].

In order to obtain the polarizability values \\(\alpha_1, \alpha_2, \alpha_3\\), we must project Eqn. 21.12 onto the principal axes of the molecule \\(\bm{v_n}\\), substitute Eqn. 21.17, and compare the resulting expression to Eqn. 21.8:

$$ \tag{21.22}
	\alpha_n = V_m \frac{m^2 - 1}{1 + \left( m^2 - 1 \right) \frac{A_n}{\sum_n A_n} }.
$$

In terms of the depolarization factors given by Eqn. 21.20,

$$ \tag{21.23}
	\alpha_n = V_m \frac{m^2 - 1}{1 + \left( m^2 - 1 \right) \epsilon_0 L_n}.
$$

Thus, we see that the polarizability of a small particle is linearly proportional to its volume \\(V_m\\).

For a spherical particle,

$$ \tag{21.2x}
	\mathcal{\Alpha_m} = \alpha_m
	= 3 V_m \frac{m^2 - 1}{m^2 + 2}
	= 4 \pi a^3 \frac{m^2 - 1}{m^2 + 2}.
$$

In Gaussian units, the polarizability values are smaller by a factor of \\(4 \pi\\) \[[4](#references) (ch. 6.31)\].

{{< figure src="/img/polarizability.svg" caption="*Figure 21.3: Plot of \\(\alpha_1\\) defined by Eqn. 21.22 as a function of \\(a_1\\) for \\(a_2 = a_3 = 1\\) and \\(m = 4/3\\).*" >}}

Substitution of Eqn. 21.22 into 21.17 allows us to quickly determine the components of the internal field:

$$ \tag{21.2y}
	E_n
	= \frac{\alpha_n}{V_m} \frac{\bm{E_0} \cdot \bm{v_n}}{m^2 - 1}.
$$

As shown on Fig. 21.1, the value of \\(A_n\\) monotonically decreases as the particle is stretched out along \\(\bm{v_n}\\). This causes the magnitude of \\(\alpha_n\\) to grow (see Fig. 21.3), making the internal electric field vector more closely aligned with the principal axis of the molecule (see Fig. 21.2).

#### Scattering Matrix

In order to perform light scattering calculations, it is more convenient to use a reference frame that is fixed with respect to the light source rather than the particle. In the so-called laboratory reference frame (described in Sec. 15), the Cartesian components of the electric field phasor \\(\bm{E_0}\\) of the incident wave propagating along the \\(Z\\)-axis are

$$ \tag{21.24}
	\bm{E_0} =
	\begin{bmatrix}
		E_X \cr
		E_Y \cr
	\end{bmatrix} =
	\begin{bmatrix}
		a\_{X} e^{i \delta_X} \cr
		a\_{Y} e^{i \delta_Y} \cr
	\end{bmatrix}.
$$

Conventionally, the scattering coordinate system is rotated relative to the laboratory reference frame. The vector components are transformed according to Eqn. 15.11:

$$ \tag{21.25}
	\bm{E_0} =
	\begin{bmatrix}
		E_x(\phi) \cr
		E_y(\phi) \cr
	\end{bmatrix} =
	R(-\phi)
	\begin{bmatrix}
		E\_{X} \cr
		E\_{Y} \cr
	\end{bmatrix}
	=
	\begin{bmatrix}
		\phantom{-}\cos{\phi} & \sin{\phi} \cr
		-\sin{\phi}           & \cos{\phi} \cr
	\end{bmatrix}
	\begin{bmatrix}
		E\_{X} \cr
		E\_{Y} \cr
	\end{bmatrix},
$$

where \\(\phi\\) denotes both the azimuthal angle of the direction of observation.

Next, let us consider the expression of the polarizability dyadic \\(\mathcal{\Alpha_m}\\) given by Eqn. 21.7. Since \\([\bm{v_1} \thinspace \bm{v_2} \thinspace \bm{v_3}]^{T}\\) is composed of 3 orthogonal unit vectors, it can be represented by a 3x3 rotation matrix that performs a transformation from the scattering coordinate system to the coordinate system of the particle. Even though the matrix contains 9 elements, there are only 3 degrees of freedom called the [Tait-Bryan angles](https://en.wikipedia.org/wiki/Euler_angles#Tait%E2%80%93Bryan_angles): the spherical coordinates \\(\alpha, \beta\\) of the primary axis (e.g. \\(\bm{v_1}\\)) of the particle, and the rotation angle \\(\gamma\\) of the particle around this axis.

It is easier to picture the inverse, \\([\bm{v_1} \thinspace \bm{v_2} \thinspace \bm{v_3}]\\). This transformation can be decomposed into three [elementary](https://en.wikipedia.org/wiki/Rotation_matrix#Basic_rotations) [extrinsic rotations](https://en.wikipedia.org/wiki/Euler_angles#Conventions_by_extrinsic_rotations) about the axes of the scattering coordinate system:

$$ \tag{21.26}
\begin{aligned}
	X(\alpha, \beta, \gamma) &=
	\begin{bmatrix}
		\bm{v_1} & \bm{v_2} & \bm{v_3} \cr
	\end{bmatrix} =
	R_z(\alpha) R_y(\beta - \pi/2) R_x(\gamma)
	\cr
	&= \begin{bmatrix}
		\cos{\alpha} & -\sin{\alpha}           & 0 \cr
		\sin{\alpha} & \phantom{-}\cos{\alpha} & 0 \cr
		0            &            0            & 1 \cr
	\end{bmatrix}
	\begin{bmatrix}
		\sin{\beta} & 0 & -\cos{\beta}           \cr
		0           & 1 & 0                      \cr
		\cos{\beta} & 0 & \phantom{-}\sin{\beta} \cr
	\end{bmatrix}
	\begin{bmatrix}
		1 & 0            & 0                       \cr
		0 & \cos{\gamma} & -\sin{\gamma}           \cr
		0 & \sin{\gamma} & \phantom{-}\cos{\gamma} \cr
	\end{bmatrix},
\end{aligned}
$$

where we first *roll* the ellipsoid about its principal axis (initially aligned with the \\(x\\)-axis), then elevate, or *pitch* it by rotating it around the \\(y\\)-axis, and finally set its *yaw*, or azimuthal angle by swinging it around the \\(z\\)-axis.

[Insert an illustration of the sequence of three rotations here]

From this description, it is evident that

$$ \tag{21.27}
	\alpha \in [-\pi, \pi),
	\quad
	\beta \in [0, \pi],
	\quad
	\gamma \in [-\pi, \pi).
$$

Note that, because we defined the orientation of the ellipsoid in the scattering coordinate system, the axes of which are rotated relative to the laboratory reference frame, \\(\alpha, \beta, \gamma\\) are functions of \\(\phi\\).

The inverse of Eqn. 21.26 can be found by reversing both the sequence of rotations and the signs of the angles:

$$ \tag{21.28}
\begin{aligned}
	X^{-1}(\alpha, \beta, \gamma) &=
	X^T =
	R_x(-\gamma) R_y(-\beta + \pi/2) R_z(-\alpha)
	\cr
	&= \begin{bmatrix}
		1 & 0                       & 0            \cr
		0 & \phantom{-}\cos{\gamma} & \sin{\gamma} \cr
		0 & -\sin{\gamma}           & \cos{\gamma} \cr
	\end{bmatrix}
	\begin{bmatrix}
		\phantom{-}\sin{\beta} & 0 & \cos{\beta} \cr
		0                      & 1 & 0           \cr
		-\cos{\beta}           & 0 & \sin{\beta} \cr
	\end{bmatrix}
	\begin{bmatrix}
		\phantom{-}\cos{\alpha} & \sin{\alpha} & 0 \cr
		-\sin{\alpha}           & \cos{\alpha} & 0 \cr
		0                       & 0            & 1 \cr
	\end{bmatrix}
\end{aligned}.
$$

The final component of the scattering matrix is the dyadic \\((\mathcal{I} - \bm{n_s} \otimes \bm{n_s})\\) that projects a vector onto the tangent plane of a unit sphere. Since, according to Eqn. 15.12, the \\(y\\)- and the \\(\phi\\)-axes are aligned, this transformation is given by Eqn. 15.13:

$$ \tag{21.29}
	\mathcal{I} - \bm{n_s} \otimes \bm{n_s}
	= P_3
	R_y(-\theta) =
	\begin{bmatrix}
		1 & 0 & 0 \cr
		0 & 1 & 0 \cr
	\end{bmatrix}
	\begin{bmatrix}
		\cos{\theta} & 0 & -\sin{\theta} \cr
		0            & 1 & 0             \cr
		\sin{\theta} & 0 & \phantom{-}\cos{\theta} \cr
	\end{bmatrix},
$$

where the projection matrix \\(P_3\\) is used to discard the radial component of the field.

Putting it all together, the form of the scattering matrix \\(S\\) that corresponds to the scattering dyadic \\(\mathcal{S\_{ef}}\\) of an ellipsoidal particle (defined by Eqn. 21.4) is
Putting it all together, the form of the scattering matrix \\(S\\) that corresponds to the scattering dyadic \\(\mathcal{S\_{ef}}\\) of an ellipsoidal particle (defined by Eqn. 21.4) is

$$ \tag{21.30}
	S(\theta, \phi, \omega)
	= \frac{k_0^3(\omega)}{4 \pi}
	P_3 R_y(-\theta)
	X(\alpha, \beta, \gamma)
	D(\alpha_1, \alpha_2, \alpha_3)
	X^{T}(\alpha, \beta, \gamma)
	P_3^{T},
$$

where \\(D\\) is a diagonal matrix, and the projection matrix \\(P_3\\) ensures that the scattering matrix is 2x2, since both the input and the output vectors have the third component set to 0.

The expanded form of Eqn. 21.30 is very large, so we shall not attempt to explicitly write it down. However, the expression for a spherical particle is very simple:

$$ \tag{21.31}
	S(\theta, \omega)
	= \frac{k_0^3(\omega)}{4 \pi}
	\alpha_m
	\begin{bmatrix}
		\cos{\theta} & 0 \cr
		0            & 1 \cr
	\end{bmatrix}
	= \frac{m^2 - 1}{m^2 + 2} x^3
	\begin{bmatrix}
		\cos{\theta} & 0 \cr
		0            & 1 \cr
	\end{bmatrix}.
$$

Due to the definition of polarizability, in Gaussian units, the elements of the scattering matrix are larger by a factor of \\(4 \pi\\) \[[4](#references) (ch. 6.12)\].

It's instructive to compare the resulting scattering matrix with the expression obtained using the Lorenz-Mie-Debye theory. Substitution of Eqn. 19.8 and 19.24 into 19.6 yields an expression that is identical to Eqn. 21.31, confirming both the accuracy of the *electrostatic dipole* approximation and the conditions of its validity. In contrast, the expression of the magnitude of an *electrodynamic dipole* (given by Eqn. 19.23.2) has several additional terms; while most are negligible in practice, it is worth pointing out an additional imaginary term highlighted by Eqn. 19.2y (that models *radiation damping* caused by the [self-force](https://en.wikipedia.org/wiki/Abraham%E2%80%93Lorentz_force)) that is entirely absent from the electrostatic approximation \[[4](#references) (ch. 6.13)\].

#### Light Intensity

Let us calculate the quantities typically used to characterize light scattering by a group of gas molecules. For simplicity, we shall assume that we are dealing with a single type of molecule; that way, averaging over shapes, sizes, and compositions of different kinds of molecules can be avoided. Thus, all that is left is to evaluate the contribution of molecules in various orientations \[[2](#references) (ch. 18), [4](#references) (ch. 6.5)\].

Unless the entire group is placed into a uniform and parallel electromagnetic field, the orientations of the molecules are likely to follow a uniform distribution described by the probability density function

$$ \tag{21.32}
	p(\psi)
	= p(\alpha, \beta, \gamma)
	= p(\alpha) p(\beta) p(\gamma)
	= \frac{1}{2 \pi} \frac{1}{2} \frac{1}{2 \pi}
	= \frac{1}{8 \pi^2},
$$

such that \[[8](#references) (ch. 15)\]

$$ \tag{21.33}
	\int_{\Psi} p(\psi) d\psi =
	\int_{-\pi}^{\pi} d\alpha
	\int_{0}^{\pi} d\beta \sin{\beta}
	\int_{-\pi}^{\pi} d\gamma \thinspace p(\alpha, \beta, \gamma) = 1.
$$

Then, according to Eqn. 17.56.1, the mean intensity of light scattered by a group of molecules is proportional to

$$ \tag{21.34}
\begin{aligned}
	\braket{\Iota_s^{tot}}
	&\propto \int_{\Psi} p(\psi)
	\left| S(\theta, \phi, \psi) \bm{E_0} \right|^2
	d\psi
	\cr
	&= \int_{-\pi}^{\pi} \int_{0}^{\pi} \int_{-\pi}^{\pi}
	p(\alpha, \beta, \gamma)
	\left| S(\theta, \phi, \alpha, \beta, \gamma) \bm{E_0} \right|^2
	\sin{\beta} d\alpha d\beta d\gamma
	\cr
	&= \bm{E_0^H}
	\left( \frac{1}{8 \pi^2}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \int_{-\pi}^{\pi}
	S^H S
	\sin{\beta} d\alpha d\beta d\gamma
	\right)
	\bm{E_0},
\end{aligned}
$$

where the \\(S^H\\) denotes the [conjugate transpose](https://en.wikipedia.org/wiki/Conjugate_transpose) of the scattering matrix.

The resulting integral is considerably simplified by the use of [integrals of trigonometric functions](https://en.wikipedia.org/wiki/List_of_integrals_of_trigonometric_functions) \[[4](#references) (ch. 6.52)\]. If we define the shorthand notation for the self and the cross terms

$$ \tag{21.35}
\begin{aligned}
	15 A &= |\alpha_1|^2+|\alpha_2|^2+|\alpha_3|^2,
	\cr
	15 B &= \mathcal{Re} \lbrace
	\alpha_1 \alpha_2^{\*} +
	\alpha_1 \alpha_3^{\*} +
	\alpha_2 a_3^{\*} \rbrace,
\end{aligned}
$$

then the value of the integral of Eqn. 21.34 can be expressed as

$$ \tag{21.36}
\begin{aligned}
	\frac{1}{8 \pi^2} \int_{-\pi}^{\pi} \int_{0}^{\pi} \int_{-\pi}^{\pi}
	S^H S
	\sin{\beta} d\alpha d\beta d\gamma
	= \frac{k_0^6}{16 \pi^2} (4 A + B)
	\begin{bmatrix}
		\cos^2{\theta} + \Delta \sin^2{\theta} & 0 \cr
		0 & 1 \cr
	\end{bmatrix},
\end{aligned}
$$

where \\(\Delta = (2 A - 2 B)/(4 A + B)\\) is another *depolarization factor* unrelated to \\(L_n\\). For atmospheric air, its measured value is \\(\Delta \approx 0.031\\) \[[4](#references) (ch. 6.53)\]. Unfortunately, the frequency of light used during the experiment is not specified.

For spherical particles, \\(\Delta = 0\\) and \\(4 A + B = 5 A = |\alpha_m|^2\\), and Eqn. 21.36 takes a simple form:

$$ \tag{21.37}
	S^H S
	= \frac{k_0^6}{16 \pi^2} |\alpha_m|^2
	\begin{bmatrix}
		\cos^2{\theta} & 0 \cr
		0 & 1 \cr
	\end{bmatrix}
	= \left| \frac{m^2-1}{m^2+2} \right|^2 x^6
	\begin{bmatrix}
		\cos^2{\theta} & 0 \cr
		0 & 1 \cr
	\end{bmatrix}.
$$

After substitution of Eqn. 21.25 and 21.36 into 17.56.1, it becomes clear that the intensity of light scattered by a group of \\(N\\) molecules depends on the state of the incident light:

$$ \tag{21.38}
	\braket{\Iota_s^{tot}}
	= N \frac{\mu\_0^{-1}}{2} \frac{1}{c}
	 \frac{k_0^4}{16 \pi^2} (4 A + B)
	\left(
		\left( \cos^2{\theta} + \Delta \sin^2{\theta} \right)
		\big| E_x(\phi) \big|^2 +
		\big| E_y(\phi) \big|^2
	\right).
$$

In the special case of natural light, we may average Eqn. 21.38 over all possible orientations of the plane of reference according to Eqn. 19.31. This allows us to directly relate the scattered intensity \\(\Iota_s\\) to the incident irradiance \\(\Epsilon_i\\) (the latter is given by Eqn 16.4):

$$ \tag{21.39}
\begin{aligned}
	\braket{\Iota_s^{tot}}
	&= N \frac{k_0^4}{16 \pi^2}
	(4 A + B)
	\left(
		\cos^2{\theta} + \frac{\Delta + 1}{2} \sin^2{\theta}
	\right) \Epsilon_i
	\cr
	&= N \frac{k_0^4}{16 \pi^2}
	(4 A + B)
	\left(
		\cos^2{\theta} \left( 1 - \frac{\Delta + 1}{2} \right) + \frac{\Delta + 1}{2}
	\right) \Epsilon_i.
\end{aligned}
$$

For spherical particles, the corresponding expression is found by setting \\(\Delta = 0\\) and \\(4 A + B = 5 A = |\alpha_m|^2\\):

$$ \tag{21.40}
	\braket{\Iota_s^{tot}}
	= N \frac{k_0^4}{16 \pi^2} |\alpha_m|^2
	\frac{ \cos^2{\theta} + 1 }{2} \Epsilon_i
	= N \left| \frac{m^2 - 1}{m^2 + 2} \right|^2
	\frac{x^6}{k_0^2} \frac{ \cos^2{\theta} + 1 }{2} \Epsilon_i.
$$

Due to the definition of polarizability, in Gaussian units, the intensity is greater by a factor of \\(16 \pi^2\\) \[[4](#references) (ch. 6.12, 6.52)\].

The total amount of scattered power can be computed by integrating Eqn. 21.38 over the surface of a unit sphere (cf. Eqn. 17.56.2):

$$ \tag{21.41}
	\braket{\Phi_s^{tot}}
	= \int_{-\pi}^{\pi} \int_{0}^{\pi} \braket{\Iota_s^{tot}} \sin{\theta} d\theta d\phi
	= N \frac{k_0^4}{16 \pi^2} \frac{8 \pi}{3} 5 A
	\Epsilon_i.
$$

Since the distribution of scattered light is mirror-symmetrical with respect to the transverse plane of the direction of propagation, the value of the mean cosine \\(\braket{g}\\) (defined by Eqn. 19.35-19.37) is

$$ \tag{21.4x}
\begin{aligned}
	\braket{g}(\omega)
	&= \braket{\cos{\theta}}
	= \int_{-\pi}^{\pi} \int_{0}^{\pi} \frac{\braket{\Iota_s^{tot}}(\theta, \phi, \omega)}{\braket{\Phi_s^{tot}}(\omega)} \cos{\theta} \sin{\theta} d\theta d\phi
	\cr
	&= \int_{0}^{\pi}
	\left(
		\cos^2{\theta} \left( 1 - \frac{\Delta + 1}{2} \right) + \frac{\Delta + 1}{2}
	\right)
	\cos{\theta} \sin{\theta} d\theta = 0.
\end{aligned}
$$

#### Collision Coefficients

The formula of the scattering coefficient \\(\varSigma_s\\) is found by dividing Eqn. 21.41 by \\(\Epsilon_i\\) and replacing \\(N\\) with \\(n(\bm{r})\\), as shown by Eqn. 17.57-17.58:

$$ \tag{21.42}
	\varSigma_s(\bm{r})
	= n(\bm{r}) \braket{C_s}
	= n(\bm{r}) \frac{k_0^4}{16 \pi^2} \frac{8 \pi}{3} 5 A.
$$

For spherical particles, \\(5 A = |\alpha_m|^2\\), which directly leads to

$$ \tag{21.43}
	\varSigma_s(\bm{r})
	= n(\bm{r}) \frac{k_0^4}{16 \pi^2} \frac{8 \pi}{3} |\alpha_m|^2
	= n(\bm{r}) \frac{8 \pi}{3}
	\left| \frac{m^2 - 1}{m^2 + 2} \right|^2
	\frac{x^6}{k_0^2}.
$$

Due to the definition of polarizability, in Gaussian units, the value of the scattering coefficient is greater by a factor of \\(16 \pi^2\\) \[[4](#references) (ch. 6.31, 6.53)\].

We must remember to account for the extinction effect by reducing the intensity of light in the forward direction. According to Eqn. 17.56.3, this involves computing the average of the scattering matrix:

$$ \tag{21.44}
\begin{aligned}
	\braket{\Phi_e^{tot}}
	&\propto \int_{\Psi} p(\psi)
	\mathcal{Im} \left\lbrace
	\bm{E_0^H} S(0, \phi, \psi) \bm{E_0}
	\right\rbrace
	d\psi
	\cr
	&= \int_{-\pi}^{\pi} \int_{0}^{\pi} \int_{-\pi}^{\pi}
	p(\alpha, \beta, \gamma)
	\mathcal{Im} \left\lbrace
	\bm{E_0^H} S(0, \phi, \alpha, \beta, \gamma) \bm{E_0}
	\right\rbrace
	\sin{\beta} d\alpha d\beta d\gamma
	\cr
	&= \mathcal{Im} \left\lbrace \bm{E_0^H}
	\left( \frac{1}{8 \pi^2}
	\int_{-\pi}^{\pi} \int_{0}^{\pi} \int_{-\pi}^{\pi}
	S \sin{\beta} d\alpha d\beta d\gamma
	\right)
	\bm{E_0} \right\rbrace.
\end{aligned}
$$

A straightforward calculation produces a scattering matrix of a spherical particle

$$ \tag{21.45}
\begin{aligned}
	\frac{1}{8 \pi^2} \int_{-\pi}^{\pi} \int_{0}^{\pi} \int_{-\pi}^{\pi}
	S
	\sin{\beta} d\alpha d\beta d\gamma
	= \frac{k_0^3}{4 \pi} \alpha_m
	\begin{bmatrix}
		\cos^2{\theta} & 0 \cr
		0 & 1 \cr
	\end{bmatrix}
\end{aligned}
$$

with the average polarizability

$$ \tag{21.46}
	\alpha_m = \frac{\alpha_1 + \alpha_2 + \alpha_3}{3}.
$$

After setting \\(\theta = 0\\) in Eqn. 21.45, followed by a substitution into Eqn. 17.56.3, and taking Eqn. 16.4 into account, we obtain the  amount of power dissipated by the particle group:

$$ \tag{21.47}
	\braket{\Phi_e^{tot}}
	\stackrel{!}{=} N k_0
	\mathcal{Im} \left\lbrace \alpha_m
	\right\rbrace \Epsilon_i.
$$

If the polarizability is real, and the observer is located in the radiation zone with respect to the entire particle group, then the value of Eqn. 21.47 approaches \\(0\\), which indicates that this expression is *incorrect* \[[4](#references) (ch. 6.13, 6.53)\].

Let us approach the problem from a different angle. Since, according to Eqn. 16.21, 17.56 and 17.57,

$$ \tag{21.48}
	\braket{\Phi_e^{tot}}
	= \braket{\Phi_a^{tot}} + \braket{\Phi_s^{tot}}
	= N \braket{\Phi_a} + N \braket{\Phi_s},
$$

and because the value of \\(\braket{\Phi_s^{tot}}\\) is approximately correct (for spherical particles, verification can be performed using the Lorenz-Mie-Debye theory), we can use Ohm's law to compute the amount of power absorbed by the particle group.

For a single particle in a certain orientation, we can use Eqn. 16.3z and 21.2y to obtain

$$ \tag{21.49}
\begin{aligned}
	\Phi_a
	&= \frac{\mu\_0^{-1}}{2} \frac{k_0}{c} \int\_{V}
	\mathcal{Im} \big\lbrace m^2(\bm{r'}) \big\rbrace \big| \bm{E}(\bm{r'}) \big|^2 dV'
	\cr
	&= \frac{\mu\_0^{-1}}{2} \frac{k_0}{c} V_m
	\mathcal{Im} \big\lbrace m^2 \big\rbrace
	\sum_n \left| \frac{\alpha_n \bm{E_0} \cdot \bm{v_n}}{V_m \left( m^2 - 1 \right)} \right|^2
	\cr
	&= \frac{\mu\_0^{-1}}{2} \frac{k_0}{c}
	\frac{\mathcal{Im} \big\lbrace m^2 \big\rbrace}{V_m \left| m^2 - 1 \right|^2}
	\sum_n | \alpha_n |^2 | \bm{E_0} \cdot \bm{v_n} |^2.
\end{aligned}
$$

Note that the resulting expression only depends on the magnitude of the electric field phasor, while its phase is irrelevant.

Next, we must consider a group of randomly oriented particles. Instead of actively rotating each particle while the source of the incident light remains fixed, we can perform a passive rotation by assuming that the incident electric field vector is equally likely to point in any direction on a unit sphere.

Let us express the components \\(\bm{E_0} \cdot \bm{v_n}\\) of the electric field using the [spherical coordinates](https://en.wikipedia.org/wiki/Spherical_coordinate_system#Cartesian_coordinates):

$$ \tag{21.50}
	\bm{E_0} = |\bm{E_0}|
	\begin{bmatrix}
		\sin{\theta} \cos{\phi} \cr
		\sin{\theta} \sin{\phi} \cr
		\cos{\theta} \cr
	\end{bmatrix}.
$$

It is easy to show that their mean value on the surface of a unit sphere is

$$ \tag{21.51}
	\int_{\Psi} p(\psi)
	| \bm{E_0} \cdot \bm{v_n} |^2 d\psi =
	\frac{1}{4 \pi} \int_{-\pi}^{\pi} \int_{0}^{\pi}
	| \bm{E_0} \cdot \bm{v_n} |^2 \sin{\theta} d\theta d\phi = \frac{|\bm{E_0}|^2}{3}.
$$

Therefore, the ensemble average of Eqn. 21.49 is

$$ \tag{21.52}
	\braket{\Phi_a^{tot}} = N \braket{\Phi_a}
	= N \int_{\Psi} p(\psi) \Phi_a d\psi
	= N k_0
	\frac{\mathcal{Im} \big\lbrace m^2 \big\rbrace}{V_m \left| m^2 - 1 \right|^2}
	 \sum_n \frac{| \alpha_n |^2}{3} \Epsilon_i,
$$

where

$$ \tag{21.53}
	\frac{\mathcal{Im} \big\lbrace m^2 \big\rbrace |\alpha_n|^2}{V_m \left| m^2 - 1 \right|^2}
	= V_m \frac{\mathcal{Im} \big\lbrace m^2 \big\rbrace}{\left| 1 + \left( m^2 - 1 \right) \epsilon_0 L_n \right|^2}.
$$

In order to simplify Eqn. 21.53 further, we must rewrite Eqn. 21.22 so that is has a real denominator:

$$ \tag{21.54}
\begin{aligned}
	\alpha_n
	&= V_m \frac{
		\left( m^2 - 1 \right)
		\left( 1 + \left( \big[ m^2 \big]^{\*} - 1 \right)
		\epsilon_0 L_n \right)
	}{
		\left| 1 + \left( m^2 - 1 \right) \epsilon_0 L_n \right|^2
	}
	\cr
	&= V_m \frac{
		m^2 + \left( |m|^4 - 2 \mathcal{Re} \big\lbrace m^2 \big\rbrace + 1 \right) \epsilon_0 L_n - 1
	}{
		\left| 1 + \left( m^2 - 1 \right) \epsilon_0 L_n \right|^2
	}.
\end{aligned}
$$

Comparison of Eqn. 21.53 to 21.54 should make it apparent that

$$ \tag{21.55}
	\frac{\mathcal{Im} \big\lbrace m^2 \big\rbrace |\alpha_n|^2}{V_m \left| m^2 - 1 \right|^2}
	= \mathcal{Im} \big\lbrace \alpha_n \big\rbrace,
$$

which means that the amount of power absorbed by the particle group is

$$ \tag{21.56}
	\braket{\Phi_a^{tot}}
	= N k_0
	\mathcal{Im} \left\lbrace \alpha_m \right\rbrace \Epsilon_i.
$$

The corresponding expression of the absorption coefficient \\(\varSigma_a\\) is

$$ \tag{21.57}
	\varSigma_a(\bm{r})
	= n(\bm{r}) \braket{C_a}
	= n(\bm{r}) k_0
	\mathcal{Im} \left\lbrace \alpha_m
	\right\rbrace.
$$

For spherical particles, it naturally reduces to

$$ \tag{21.58}
	\varSigma_a(\bm{r})
	= n(\bm{r}) k_0
	\mathcal{Im} \left\lbrace \alpha_m
	\right\rbrace
	= n(\bm{r}) 4 \pi
	\mathcal{Im} \left\lbrace \frac{m^2 - 1}{m^2 + 2}
	\right\rbrace \frac{x^3}{k_0^2}.
$$

Due to the definition of polarizability, in Gaussian units, the value of the absorption coefficient is greater by a factor of \\(4 \pi\\) \[[4](#references) (6.31, 6.53)\].

Thus, we see that Eqn. 21.47 correctly models absorption, but the effect of scattering (radiation damping) has not yet been taken into account. As discussed previously, this can be accomplished by combining Eqn. 21.41 and 21.48. For spherical particles, verification can be performed by replacing the electrostatic expression of the scattering matrix (Eqn. 21.31) with a series expansion found using the Lorenz-Mie-Debye theory and retaining both the real and the imaginary leading terms.

It is interesting to compare the contribution of absorption and scattering to extinction. Since the former is proportional to \\(k_0 |\alpha_m|\\), and the latter -- to \\(k_0^4 |\alpha_m|^2\\), and because \\(\alpha_m \propto V_m\\),

$$ \tag{21.59}
	\frac{\varSigma_a}{\varSigma_s}
	\propto \frac{1}{k_0^3 |\alpha_m| }
	\propto \frac{1}{k_0^3 V_m}
	\propto \frac{1}{x^3}.
$$

As the molecules are small compared to the wavelength of light, \\(x \ll 1\\). Therefore, even a relatively small imaginary component of polarizability can make absorption the dominant effect \[[4](#references) (ch. 6.13)\].

### Light Scattering by Liquid Molecules

### Light Scattering by Particles of Arbitrary Size

In this section, we shall develop a theory of electromagnetic scattering by particles of an arbitrary size. It serves as the extension of the theory of Rayleigh (dipole) scattering for small particles. This can be accomplished in two different ways. The first way is to perform a multipole series expansion of the electromagnetic potential (using Eqn. 18.11 and 18.13), and treat electromagnetic scattering as a boundary value problem; this leads to the Lorenz-Mie-Debye solution of the *differential* form of the Maxwell equations. The second method takes advantage of the *integral* expression of the electromagnetic potential (Eqn. 9.14), with the interpretation of the scattering object as a collection of dipoles (as per Eqn. 10.14); this leads to the surface (Eqn. 1x.14) and the volume integral equations (Eqn. 11.25.1).

Unfortunately, evaluation of these integrals poses a challenge: not just because the expressions themselves are complicated, but also because they require the values of the internal field (not known apriori) to be specified either across the entire interior or on the surface of the scattering object. Formally, this leads to a recursive integral equation (Eqn. 14.22) of the transition operator, and the solution generates a Born series expansion (Eqn. 14.20) of the electric field. In practice, under certain conditions, one can make an educated guess about the approximate form of the internal field, which allows one to bypass the recursive integral equation and jump straight into the volume (or the surface) integral equation. Below, we shall examine these mathematical techniques in more detail.

#### Rayleigh-Gans-Born Approximation

Imagine a particle illuminated by [X-rays](https://en.wikipedia.org/wiki/X-ray). The frequencies (and the momenta \\(p = \hbar \omega / c\\)) of the photons are so high that it renders the particle nearly transparent \[[4](#references) (ch. 7.5)\]. Thus, the squared magnitude of the relative wavenumber is very small:

$$ \tag{22.1}
	\left| m^2 - 1 \right|
	\approx
	2 \left| m - 1 \right| \ll 1.
$$

Under these conditions, according to Eqn. 21.17, the field inside a tiny particle is practically the same as the incident field:

$$ \tag{22.2}
	\bm{E}(\bm{r}, \omega) \approx
	\bm{E_i}(\bm{r}, \omega) =
	\bm{E_0}(\bm{n_i}, \omega) e^{i k(\omega) (\bm{r} \cdot \bm{n_i})}.
$$

Eqn. 22.1 is a sufficient and necessary condition to apply the approximation of Eqn. 22.2 to the Rayleigh scattering. However, for large particles, we must impose another requirement:

$$ \tag{22.3}
	\rho = 2 (m - 1) x \approx \left( m^2 - 1 \right) x,
	\quad
	| \rho | \ll 1,
$$

where, as usual, \\(x = k a\\) is the size parameter, and \\(a\\) is the radius of the bounding sphere of the particle.

Eqn. 22.3 can be interpreted as follows. Imagine a particle with \\(x \gg 1\\), such that the laws of geometric optics are applicable. We isolate a central ray that passes through the particle along the diameter of the bounding sphere. According to Eqn. 22.1, this ray experiences a significant amount of reflection neither inside nor outside the particle. On the other hand, inside, the associated electromagnetic wave undergoes a change of phase velocity and, potentially, a degree of absorption. If we model the internal field in the vicinity of the ray as a plane wave with the wavenumber \\(m k\\), then the expression of the electric field at the back of the particle is

$$ \tag{22.4}
\begin{aligned}
	\bm{E}(\bm{r} + a \bm{n_i}, \omega)
	&= \bm{E_i}(\bm{r} - a \bm{n_i}, \omega) e^{-2 \mathcal{Im}\lbrace m(\omega) \rbrace x(\omega)} e^{i 2 \mathcal{Re}\lbrace m(\omega) \rbrace x(\omega)}
	\cr
	&= \bm{E_i}(\bm{r} + a \bm{n_i}, \omega) e^{-2 \mathcal{Im}\lbrace m(\omega) \rbrace x(\omega)} e^{i 2 \mathcal{Re}\lbrace m(\omega) \rbrace x(\omega)} e^{-i 2x(\omega)}.
\end{aligned}
$$

Both the phase delay and the absorption are eliminated if Eqn. 22.3 is satisfied. Note that the relative wavenumber \\(m\\) can be spatially-varying and frequency-dependent if both conditions hold for all \\(\bm{r}\\) and \\(\omega\\).

It is important to understand the difference between the Rayleigh and the Rayleigh-Gans-Born approximations. The former assumes that \\(x \ll 1\\), while \\(|m|\\) can not be too great. The latter requires \\(|m - 1| \ll 1\\), while \\(x\\) must not be too large. They have a case in common: namely, \\(x \ll 1,|m - 1| \ll 1\\).

Formally, the Rayleigh-Gans-Born approximation corresponds to the first term of the Born series \[while Rayleigh's approximation is the first term of the multipole series\] expansion of the electric field, which makes it the *first-order Born approximation*. According to Eqn. 14.22, the associated expression of the transition dyadic \\(\mathcal{T}\\) is simply

$$ \tag{22.5}
	\mathcal{T} \big( \bm{r}, \bm{r'}, k(\omega), \omega \big)
	\approx k^2(\omega) \big( m^2(\bm{r}, \omega) - 1 \big)
	\delta(\bm{r} - \bm{r'}) \mathcal{I}.
$$

After substituting Eqn. 22.2 into 13.12, the far-field expression of the volume integral equation

$$ \tag{22.6}
	\bm{E_s}(\bm{r}, \omega)
	\approx k^2(\omega) \frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot \bm{E_0}(\bm{n_i}, \omega)
	\int\_{V_p} \big( m^2(\bm{r'}, \omega) - 1 \big)
	e^{i k(\omega) \bm{r'} \cdot (\bm{n_i} - \bm{n_s})} dV'
$$

is considerably simplified. Going forward, we shall also assume that the particle is homogeneous, with \\(m\\) independent of \\(\bm{r'}\\).

Recall that the scattering angle \\(\theta = \arccos(\bm{n_i} \cdot \bm{n_s})\\) can be defined in at least three different ways:

$$ \tag{22.7}
	\bm{n_i} \cdot \bm{n_s} = \cos(\theta),
	\quad
	| \bm{n_i} \times \bm{n_s} | = \sin(\theta),
	\quad
	\frac{|\bm{n_i} - \bm{n_s}|}{2}
	= \sqrt{\frac{1 - \bm{n_i} \cdot \bm{n_s}}{2}}
	= \sin(\theta/2).
$$

This suggests that evaluation of the integral

$$ \tag{22.8}
	I_{rgb}(\theta, \phi)
	= \frac{1}{V_p} \int\_{V_p} e^{i k \bm{r'} \cdot (\bm{n_i} - \bm{n_s})} dV'
$$

can be performed most efficiently in an aligned coordinate system. Let us define

$$ \tag{22.9}
	\bm{w} = \frac{\bm{n_i} - \bm{n_s}}{|\bm{n_i} - \bm{n_s}|},
	\quad
	\bm{v} = \frac{\bm{n_i} \times \bm{n_s}}{| \bm{n_i} \times \bm{n_s} |},
	\quad
	\bm{u} = \bm{v} \times \bm{w},
$$

If \\(\bm{n_i}\\) and \\(\bm{n_s}\\) are collinear, a supporting vector must take the role of \\(\bm{n_s}\\) in order to fix the orientation of the coordinate frame.

Using this convention, the point \\(\bm{r'}\\) in the interior of the particle has the following set of Cartesian coordinates:

$$ \tag{22.10}
\bm{r'} =
\begin{bmatrix}
	r\_{u}' \cr
	r\_{v}' \cr
	r\_{w}' \cr
\end{bmatrix},
\quad
	|\bm{r'}| = \sqrt{(r\_{u}')^2 + (r\_{v}')^2 + (r\_{w}')^2}.
$$

For spherical or ellipsoidal particles, it is highly convenient to utilize [cylindrical coordinates](https://en.wikipedia.org/wiki/Cylindrical_coordinate_system) \[[4](#references) (ch. 7.11)\]:

$$ \tag{22.11}
\bm{r'} =
\begin{bmatrix}
	r\_{u}' \cr
	r\_{v}' \cr
	r\_{w}' \cr
\end{bmatrix} =
\begin{bmatrix}
	\sqrt{(r\_{u}')^2 + (r\_{v}')^2} \cos(\chi) \cr
	\sqrt{(r\_{u}')^2 + (r\_{v}')^2} \sin(\chi) \cr
	r\_{w}' \cr
\end{bmatrix} = a
\begin{bmatrix}
	c \cos(\chi) \cr
	c \sin(\chi) \cr
	b \cr
\end{bmatrix},
$$

such that

$$ \tag{22.??}
	\bm{r'} \cdot (\bm{n_i} - \bm{n_s})
	= r\_{w}' |\bm{n_i} - \bm{n_s}| = 2 \sin(\theta/2) a b.
$$

\[Add an illustration here\]

In particular, for a spherical particle (symmetric with respect to the azimuthal angle \\(\phi\\)), we may set

$$ \tag{22.12}
	y(\theta) = 2 \sin(\theta/2) x,
$$

and easily calculate \[[4](#references) (ch. 7.21)\]

$$ \tag{22.13}
\begin{aligned}
	I_{rgb}(\theta)
	&= \frac{3}{4 \pi a^3} \int_{-1}^{1}
	\int_{-\pi}^{\pi} \int_{0}^{\sqrt{1 - b^2}}
	a^2 c \thinspace dc \thinspace d\chi \thinspace
	e^{i y b} a \thinspace db
	\cr
	&= \frac{3}{4} \int_{-1}^{1}
	\left( 1 - b^2 \right)
	e^{i y b} db
	= \frac{3}{2} \int_{0}^{1}
	\left( 1 - b^2 \right)
	\cos(y b) db
	\cr
	&= \frac{3 \sin(y) - 3 y \cos(y)}{y^3}
	 = \sqrt{\frac{9 \pi}{2 y^3}} J_{3/2}(y)
	 = \frac{3 j_1(y)}{y},
\end{aligned}
$$

where \\(J_n\\) and \\(j_n\\) are the ordinary and the spherical Bessel functions of the first kind, respectively (cf. Eqn. 18.48).

Thus, for a homogeneous spherical particle, Eqn. 22.6 can be written as

$$ \tag{22.14}
	\bm{E_s}(\bm{r}, \omega)
	\approx \frac{e^{i k(\omega) r}}{k(\omega) r}
	\big( m^2(\omega) - 1 \big) x^3(\omega)
	\frac{j_1\negthinspace\big( |\bm{n_i} - \bm{n_s}| x(\omega) \big)}{|\bm{n_i} - \bm{n_s}| x(\omega)}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot
	\bm{E_0}.
$$

Comparison with the expression of the scattered far-field of an arbitrary particle given by Eqn. 15.6.1,

$$ \tag{22.15}
	\bm{E_s}(\bm{r}, \omega)
	\simeq \frac{e^{i k(\omega) r}}{k(\omega) r} \mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega) \cdot \bm{E_0}(\bm{n_i}, \omega),
$$

reveals the formula of the far-field scattering dyadic \\(\mathcal{S\_{ef}}\\) of a homogeneous spherical particle:

$$ \tag{22.16}
	\mathcal{S_{ef}} (\bm{n_s}, \bm{n_i}, \omega)
	\approx \big( m^2(\omega) - 1 \big) x^3(\omega)
	\frac{j_1\negthinspace\big( |\bm{n_i} - \bm{n_s}| x(\omega) \big)}{|\bm{n_i} - \bm{n_s}| x(\omega)}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big).
$$

##### Scattering Matrix

The far-field scattering dyadic \\(\mathcal{S\_{ef}}\\) is composed of a number of scalar factors and the dyadic \\((\mathcal{I} - \bm{n_s} \otimes \bm{n_s})\\) that projects a vector onto the tangent plane of a unit sphere. Since, according to Eqn. 15.12, the \\(y\\)- and the \\(\phi\\)-axes are aligned, this transformation is given by Eqn. 15.13:

$$ \tag{22.17}
	\mathcal{I} - \bm{n_s} \otimes \bm{n_s}
	= P_3
	R_y(-\theta) =
	\begin{bmatrix}
		1 & 0 & 0 \cr
		0 & 1 & 0 \cr
	\end{bmatrix}
	\begin{bmatrix}
		\cos{\theta} & 0 & -\sin{\theta} \cr
		0            & 1 & 0             \cr
		\sin{\theta} & 0 & \phantom{-}\cos{\theta} \cr
	\end{bmatrix},
$$

where the projection matrix \\(P_3\\) is used to discard the radial component of the field.

By combining Eqn. 22.7, 22.16, and 22.17, the expression of the scattering matrix \\(S\\) of a homogeneous spherical particle \[[4](#references) (ch. 7.21)\] is

$$ \tag{22.18}
	S(\theta, \omega)
	= \big( m^2(\omega) - 1 \big) x^3(\omega)
	\frac{j_1\negthinspace\big( 2 \sin(\theta/2) x(\omega) \big)}{2 \sin(\theta/2) x(\omega)}
	\begin{bmatrix}
		\cos{\theta} & 0 \cr
		0            & 1 \cr
	\end{bmatrix}.
$$

It is instructive to compare the result to the Rayleigh scattering matrix for spherical particles (cf. Eqn. 21.31). Expansion of the spherical Bessel function \\(j_1\\) (given by Eqn. 22.13) in the Taylor series (given by Eqn. 19.2z) around \\(0\\) yields

$$ \tag{22.19}
	\frac{j_1(y)}{y}
	= \frac{\sin(y) - y \cos(y)}{y^3}
	= \frac{1 - y^2/6 - 1 + y^2/2 + \mathrm{O}\big( y^4 \big)}{y^2}
	= \frac{1}{3} + \mathrm{O}\big( y^2 \big).
$$

At the same time, if \\(\left| m^2 - 1 \right| \ll 1\\),

$$ \tag{22.20}
	\frac{m^2 - 1}{m^2 + 2}
	\approx \frac{m^2 - 1}{3}.
$$

Thus, Eqn. 21.31 and 22.18 match if the particles are small and the condition imposed by Eqn. 22.1 is satisfied.

##### Light Intensity

Given the expression of the scattering matrix \\(S\\) and the probability distribution \\(p(\psi)\\) of particles, computation of the the mean intensity of scattered light amounts to evaluation of Eqn. 17.56.1:

$$ \tag{22.21}
\begin{aligned}
	\braket{\Iota_s^{tot}}
	&\propto \int_{\Psi} p(\psi)
	\left| S(\theta, \phi, \psi) \bm{E_0} \right|^2
	d\psi
	\cr
	&= \int_{M} \int_{X} p(m, x)
	\left| S(\theta, m, x) \bm{E_0} \right|^2
	\thinspace dm \thinspace dx,
\end{aligned}
$$

where

$$ \tag{22.22}
	\int_{M} \int_{X} p(m, x)
	\thinspace dm \thinspace dx = 1.
$$

The specific expression of \\(p(m, x)\\) depends on the application. The range of values of the relative wavenumber \\(m\\) depends on the types (and the composition) of the particles. For a fixed value of \\(m\\), the graph of the [conditional distribution](https://en.wikipedia.org/wiki/Conditional_probability_distribution) of sizes of particles \\(p(x | m) = p(m, x)/p(m)\\) often takes the shape of a [bell curve](https://en.wikipedia.org/wiki/Normal_distribution).

For illustrative purposes, we shall consider an artificial distribution of identical particles, placed randomly, such that the density within the region \\(V\\) is approximately uniform.

Substitution of Eqn. 21.25 and 22.18 into 17.56.1 yields an expression

$$ \tag{22.23}
\begin{aligned}
	\braket{\Iota_s^{tot}}
	&= \frac{N}{k^2} \frac{\mu\_0^{-1}}{2} \frac{\eta}{c}
	\left| \big( m^2 - 1 \big) x^3
	\frac{j_1\negthinspace\big( 2 \sin(\theta/2) x \big)}{2 \sin(\theta/2) x}
	\begin{bmatrix}
		\cos{\theta} & 0 \cr
		0            & 1 \cr
	\end{bmatrix}
	\begin{bmatrix}
		E_x(\phi) \cr
		E_y(\phi) \cr
	\end{bmatrix} \right|^2
	\cr
	&= N \frac{\mu\_0^{-1}}{2} \frac{\eta}{c}
	\left| m^2 - 1 \right|^2 \frac{x^6}{k^2}
	\frac{j_1^2\negthinspace\big( 2 \sin(\theta/2) x \big)}{4 \sin^2(\theta/2) x^2}
	\left(
		\cos^2(\theta)
		\big| E_x(\phi) \big|^2 +
		\big| E_y(\phi) \big|^2
	\right)
\end{aligned}
$$

that depends on the state of the incident light.

In the special case of natural light, we may average Eqn. 22.24 over all possible orientations of the plane of reference according to Eqn. 19.31. This allows us to directly relate the scattered intensity \\(\Iota_s\\) to the incident irradiance \\(\Epsilon_i\\) (the latter is given by Eqn 16.4):

$$ \tag{22.24}
\begin{aligned}
	\braket{\Iota_s^{tot}}
	&= N
	\left| m^2 - 1 \right|^2 \frac{x^6}{k^2}
	\frac{j_1^2\negthinspace\big( 2 \sin(\theta/2) x \big)}{4 \sin^2(\theta/2) x^2}
	\frac{\cos^2(\theta) + 1}{2} \Epsilon_i.
\end{aligned}
$$

In comparison with Eqn. 21.40 of the Rayleigh scattering, the angular distribution of scattered light of Eqn. 22.24 can take on different forms, depending on the size parameter \\(x\\) of the particle.

Eqn. 22.1 and 22.3 require that \\(|m - 1|\\) is small; in order to allow for a wide range of particle sizes, we shall consider the case of \\(m = 1.001\\). It is interesting to compare the resulting intensity diagrams to those of a water droplet \\((m = 4/3)\\) computed using the Lorenz-Mie-Debye formulae of Sec. 19.

{{< figure src="/img/rgb_x01.svg" caption="*Figure 22.1: Linear plot of the intensity of light scattered by a homogeneous sphere characterized by the parameters \\(m = 1.001, x = 0.01\\).*" >}}

For \\(x \le 0.1\\), the angular distribution of scattered light is practically identical to that of Rayleigh scattering (cf. Fig. 19.N).

{{< figure src="/img/rgb_x1.svg" caption="*Figure 22.2: Linear plot of the intensity of light scattered by a homogeneous sphere characterized by the parameters \\(m = 1.001, x = 1\\).*" >}}

As \\(x \to 1\\), the pear shape is faithfully reproduced (cf. Fig. 19.N).

{{< figure src="/img/rgb_x2.svg" caption="*Figure 22.3: Linear plot of the intensity of light scattered by a homogeneous sphere characterized by the parameters \\(m = 1.001, x = 2\\).*" >}}

At \\(x = 2\\), the Mie effect is very apparent. The forward (diffraction) lobe has roughly the same shape as that of a water droplet (cf. Fig. 19.N); however, in the backward direction, the intensity is not quite the same, owing to the lack of reflection.

{{< figure src="/img/rgb_x10.svg" caption="*Figure 22.4: Linear plot of the intensity of light scattered by a homogeneous sphere characterized by the parameters \\(m = 1.001, x = 10\\).*" >}}

The same applies to larger particles. Note that, for \\(x = 10\\), the side lobes are completely absent (cf. Fig. 19.N).

The total amount of scattered power is given by the integral of Eqn. 22.23 over a unit sphere (cf. Eqn. 17.56.2). Integration over the azimuthal angle \\(\phi\\) can be easily performed with the help of Eqn. 19.31; this leaves us with the integral of Eqn. 22.24 times \\(2 \pi \sin{\theta}\\) over the range spanned by zenith angle \\(\theta\\):

$$ \tag{22.25}
\begin{aligned}
	\braket{\Phi_s^{tot}}
	&= N
	2 \pi \left| m^2 - 1 \right|^2
	\frac{x^6}{k^2} \int_{0}^{\pi}
	\frac{j_1^2\negthinspace\big( 2 \sin(\theta/2) x \big)}{4 \sin^2(\theta/2) x^2}
	\frac{\cos^2(\theta) + 1}{2}
	\sin(\theta) \thinspace d\theta \thinspace \Epsilon_i
	\cr
	&= N 2 \pi \left| m^2 - 1 \right|^2
	\frac{J_{rgb}(x)}{k^2} \Epsilon_i.
\end{aligned}
$$

After making a change of variables

$$ \tag{22.26}
	u = 2 \sin(\theta/2),
	\quad
	du = \cos(\theta/2) \thinspace d\theta,
	\quad
	v = 4 x,
$$

and substituting Eqn. 22.13, integration by parts yields

$$ \tag{22.27}
\begin{aligned}
	J_{rgb}(x)
	&= \frac{x^6}{8} \int_{0}^{2}
	\left( \frac{\sin ^2(u x)}{u^6 x^6}+\frac{\cos ^2(u x)}{u^4 x^4}-\frac{2 \sin (u x) \cos (u x)}{u^5 x^5} \right)
	\left( u^4-4 u^2+8 \right)
	u \thinspace du
	\cr
	&= \frac{16 \left(v^2-4\right) (\mathrm{Ci}(v)-\log (v)-\gamma )+v^4+20 v^2-8 v \sin (v)+56 (\cos (v)-1)}{1024},
\end{aligned}
$$

where \\(\gamma \approx 0.57721\\) is the [Euler-Mascheroni constant](https://en.wikipedia.org/wiki/Euler%27s_constant), and

$$ \tag{22.28}
	\mathrm{Ci}(x) = -\int_{x}^{\infin} \frac{\cos{t}}{t} dt
$$

is the [cosine integral](https://mathworld.wolfram.com/CosineIntegral.html) \[[4](#references) (ch. 7.22)\].

{{< figure src="/img/cos_int.svg" caption="*Figure N: Plot of the cosine integral \\(\mathrm{Ci}(x)\\).*" >}}

Given the wide range of shapes of the scattering diagrams, it is interesting to plot the mean cosine \\(\braket{g}\\) (defined by Eqn. 19.35-19.37)

$$ \tag{22.29}
\begin{aligned}
	\braket{g}\negmedspace(x)
	&= \braket{\cos{\theta}}
	= \int_{-\pi}^{\pi} \int_{0}^{\pi} \frac{\braket{\Iota_s^{tot}}(\theta, \phi, \omega)}{\braket{\Phi_s^{tot}}(\omega)} \cos{\theta} \sin{\theta} d\theta d\phi
	\cr
	&= \frac{x^6}{J_{rgb}(x)} \int_{0}^{\pi}
	\left(
	\frac{j_1^2\negthinspace\big( 2 \sin(\theta/2) x \big)}{4 \sin^2(\theta/2) x^2}
	\frac{\cos^2(\theta) + 1}{2}
	\right)
	\cos{\theta} \sin{\theta} d\theta
	= \frac{K_{rgb}(x)}{J_{rgb}(x)}
\end{aligned}
$$

as a function of the particle size \\(x\\), where

$$ \tag{22.30}
\small
\begin{aligned}
	K_{rgb}(x)
	&= \frac{x^6}{16} \int_{0}^{2}
	\left( \frac{\sin ^2(u x)}{u^6 x^6}+\frac{\cos ^2(u x)}{u^4 x^4}-\frac{2 \sin (u x) \cos (u x)}{u^5 x^5} \right)
	\left(u^4-4 u^2+8\right) \left(2 - u^2\right)
	u \thinspace du
	\cr
	&= \frac{32 v^2 \left(v^2-6\right) \left(\text{Ci}(v)-\log (v)+3/2-\gamma \right)+v^6-4 v^4+8 \left(72-5 v^2\right) (v \sin (v)+\cos (v)-1)}{1024 v^2}.
\end{aligned}
$$

{{< figure src="/img/rgb_asymmetry.svg" caption="*Figure N: Plot of the mean cosine \\(\braket{g}\\) of a homogeneous sphere. The horizontal axis represents the size parameter \\(x\\) of the particle. The solid line corresponds to \\(m=1.001\\), while the dashed -- to \\(m=4/3\\).*" >}}

Comparison of the resulting plot to that of a water droplet (cf. Fig. N) shows that the approximation is reasonably accurate for \\(x \le 1\\). However, for small particles of \\(x \ll 1\\), it is far more efficient to use the Taylor series expansions instead:

$$ \tag{22.31}
\begin{aligned}
	J_{rgb}(x) &= \frac{4 x^6}{27} + \mathrm{O}\negmedspace\left( x^8 \right),
	\cr
	K_{rgb}(x) &= \frac{16 x^8}{675} + \mathrm{O}\negmedspace\left( x^{10} \right),
	\cr
	\braket{g}\negmedspace(x) &= \frac{4 x^2}{25} + \mathrm{O}\negmedspace\left( x^4 \right).
\end{aligned}
$$

On the opposite end of the spectrum, for \\(x \gg 1\\), we have

$$ \tag{22.3x}
	\lim_{x \to \infin} \frac{J_{rgb}(x)}{x^4} =
	\lim_{x \to \infin} \frac{K_{rgb}(x)}{x^4} = \frac{1}{4}.
$$

##### Collision Coefficients

The formula of the scattering coefficient \\(\varSigma_s\\) is found by dividing Eqn. 22.25 by \\(\Epsilon_i\\) and replacing \\(N\\) with \\(n(\bm{r}),\\) as shown by Eqn. 17.57-17.58:

$$ \tag{22.32}
	\varSigma_s(\bm{r})
	= n(\bm{r}) \braket{C_s}
	= n(\bm{r}) 2 \pi \left| m^2 - 1 \right|^2
	\frac{J_{rgb}(x)}{k^2}
	= n(\bm{r}) 2 \pi \left| \rho \right|^2
	\frac{J_{rgb}(x)}{k^2 x^2}.
$$

It is interesting to visualize the mean scattering efficiency factor

$$ \tag{22.33}
	\braket{Q_s}
	= \frac{\braket{C_s}}{C_g}
	= \frac{\braket{C_s}}{\pi a^2}
	= 2 \left| m^2 - 1 \right|^2
	\frac{J_{rgb}(x)}{x^2}
	= 2 \left| \rho \right|^2
	\frac{J_{rgb}(x)}{x^4}
$$

and compare its plot to that of a water droplet (cf. Fig. N).

{{< figure src="/img/qs_rgb.svg" caption="*Figure N: Plot of the mean scattering efficiency factor of a homogeneous sphere of \\(m = 1.001\\). The horizontal axis represents the size parameter \\(x\\) of the particle.*" >}}

Unsurprisingly, the small value of the relative wavenumber \\(m\\) results in low scattering efficiency.

Also, unless \\(m\\) is complex, the scattering matrix is both diagonal and real-valued. Just as for the Rayleigh scattering (cf. Eqn. 21.47), the optical theorem (cf. Eqn. 16.41) fails to register any extinction. Thus, once again, we have to resort to applying Ohm's law given by Eqn. 16.3z:

$$ \tag{22.34}
	\braket{\Phi_a^{tot}}
	= N \frac{\mu\_0^{-1}}{2} \frac{\eta}{c} k
	\mathcal{Im} \left\lbrace \frac{m^2}{\mu\_r} \right\rbrace
	\int\_{V} \big| \bm{E}(\bm{r'}) \big|^2 dV'
	= N 4 \pi \mathcal{Im} \left\lbrace \frac{m^2}{3} \right\rbrace
	\frac{x^3}{k^2} \Epsilon_i.
$$

The same expression can be obtained by setting \\(\theta = 0\\) in Eqn. 22.18, and substituting it (along with Eqn. 16.4) into Eqn. 17.56.3.

The corresponding expression of the absorption coefficient \\(\varSigma_a\\) is

$$ \tag{22.35}
	\varSigma_a(\bm{r})
	= n(\bm{r}) \braket{C_a}
	= n(\bm{r}) 4 \pi
	\mathcal{Im} \left\lbrace \frac{m^2}{3} \right\rbrace
	\frac{x^3}{k^2}
	= n(\bm{r}) 4 \pi
	\mathcal{Im} \left\lbrace \frac{\rho}{3} \right\rbrace
	\frac{x}{k^2}.
$$

It matches the Rayleigh scattering counterpart (cf. Eqn. 21.58) when \\(|m^2 - 1|\\) is small. Thus, for small particles, absorption tend to dominate scattering (cf. Eqn. 21.59), even if the imaginary part of \\(m\\) is fairly small.

#### Anomalous Diffraction Approximation

The Rayleigh-Gans-Born approximation admits a straightforward extension. As before, we assume that the particle is nearly transparent:

$$ \tag{23.1}
	\left| m^2 - 1 \right|
	\approx
	2 \left| m - 1 \right| \ll 1.
$$

For large particles, this implies that surface reflection is almost completely absent, except for grazing angles.

However, unlike in the Rayleigh-Gans-Born approximation, we no longer assume that the phase delay and the absorption experienced by a ray of light as it passes though a (large) particle is negligible, which means that \\(\rho = 2 (m - 1) x\\) can take on arbitrary values \[[4](#references) (ch. 11.1)\].

This idea is most clearly illustrated by the example of a homogeneous particle (cf. Fig 23.1).

[Add an illustration here]

Consider the point \\(\bm{r}\\) located inside the particle. We trace a line along \\(\bm{n_i}\\) that passes through \\(\bm{r}\\); by doing so, we determine the entry and the exit points \\(\bm{r_i}\\) and \\(\bm{r_o}\\), respectively.

The wavenumber of the plane wave outside the particle is \\(k\\); at \\(\bm{r_i}\\), it abruptly transitions to \\(m k\\), and maintains this value up until \\(\bm{r_o}\\). The expression of the internal field \\(\bm{E}\\) takes the form

$$ \tag{23.2}
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&\approx \bm{E_0}(\bm{n_i}, \omega)
	e^{i k(\omega) (\bm{r_i} \cdot \bm{n_i})}
	e^{i m(\omega) k(\omega) (\bm{r} - \bm{r_i}) \cdot \bm{n_i}}
	\cr
	&= \bm{E_i}(\bm{r}, \omega)
	e^{i (m(\omega) - 1) k(\omega) (\bm{r} - \bm{r_i}) \cdot \bm{n_i}}.
\end{aligned}
$$

Thus, the far-field expression of the volume integral equation can be obtained by adding a single complex exponential factor to the integrand of Eqn. 22.6:

$$ \tag{23.3} \small
\begin{aligned}
	\bm{E_s}(\bm{r}, \omega)
	&\simeq k^2(\omega)
	\frac{e^{i k(\omega) r}}{4 \pi r}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot \bm{E_0}(\bm{n_i}, \omega)
	\int\_{V_p} \big( m^2(\omega) - 1 \big)
	e^{i k(\omega) \bm{r'} \cdot (\bm{n_i} - \bm{n_s})}
	e^{i (m(\omega) - 1) k(\omega) (\bm{r'} - \bm{r_i}) \cdot \bm{n_i}} dV'.
\end{aligned}
$$

Keep in mind that \\(\bm{r_i}\\) is spatially-varying across the surface of the particle, since it depends on both \\(\bm{r'}\\) and \\(\bm{n_i}\\) (these parameters have been omitted for brevity). This undermines the effectiveness of the parameterization introduced in Eqn. 22.9-22.11.

Let us replace the the \\(uvw\\) coordinate frame with the following set of basis vectors:

$$ \tag{23.4}
	\bm{W} = \bm{n_i}, \quad
	\bm{V} = \bm{v} = \frac{\bm{n_i} \times \bm{n_s}}{| \bm{n_i} \times \bm{n_s} |}, \quad
	\bm{U} = \bm{V} \times \bm{W}.
$$

If \\(\bm{n_i}\\) and \\(\bm{n_s}\\) are collinear, a supporting vector must take the role of \\(\bm{n_s}\\) in order to fix the orientation of the coordinate frame.

Since, according to Eqn. 22.7, 22.9 and 23.4,

$$ \tag{23.5}
	\bm{w} \cdot \bm{W}
	= \frac{\bm{n_i} \cdot (\bm{n_i} - \bm{n_s})}{|\bm{n_i} - \bm{n_s}|}
	= \frac{1 - \cos(\theta)}{2 \sin(\theta/2)}
	= \sin(\theta/2),
$$

the \\(UVW\\) coordinate frame can be obtained by rotating the \\(uvw\\) basis vectors by \\((\pi-\theta)/2\\) radians around the \\(\bm{v}\\)-axis.

\[Add an illustration here\]

The coordinates rotate in the opposite direction:

$$ \tag{23.6}
\bm{r'} =
\begin{bmatrix}
	r\_{U}' \cr
	r\_{V}' \cr
	r\_{W}' \cr
\end{bmatrix}
= R_v(\theta/2 - \pi/2)
\begin{bmatrix}
	r\_{u}' \cr
	r\_{v}' \cr
	r\_{w}' \cr
\end{bmatrix} =
\begin{bmatrix}
	\sin(\theta/2) & 0 & 		   -\cos(\theta/2) \cr
	             0 & 1 &                         0 \cr
	\cos(\theta/2) & 0 & \phantom{-}\sin(\theta/2) \cr
\end{bmatrix}
\begin{bmatrix}
	r\_{u}' \cr
	r\_{v}' \cr
	r\_{w}' \cr
\end{bmatrix}.
$$

Let us consider a spherical particle of radius \\(a\\). Then, once again, it is convenient to utilize cylindrical coordinates:

$$ \tag{23.7}
\bm{r'} =
\begin{bmatrix}
	r\_{U}' \cr
	r\_{V}' \cr
	r\_{W}' \cr
\end{bmatrix} =
\begin{bmatrix}
	\sqrt{(r\_{U}')^2 + (r\_{V}')^2} \cos(\chi) \cr
	\sqrt{(r\_{U}')^2 + (r\_{V}')^2} \sin(\chi) \cr
	r\_{W}' \cr
\end{bmatrix} = a
\begin{bmatrix}
	c \cos(\chi) \cr
	c \sin(\chi) \cr
	b \cr
\end{bmatrix}.
$$

From the geometry, it is evident that

$$ \tag{23.8}
	\bm{r'} \cdot \bm{n_i}
	= a b,
	\quad
	-\bm{r_i} \cdot \bm{n_i}
	= \sqrt{a^2 - \big( r\_{U}' \big)^2 - \big( r\_{V}' \big)^2}
	= a \sqrt{1 - c^2}.
$$

Furthermore, according to Eqn. ??? and ,

$$ \tag{23.4}
	\bm{r'} \cdot (\bm{n_i} - \bm{n_s}) = ...
$$

Something-something...

$$ \tag{23.4}
	I_{ada}(\theta, \phi)
	= \frac{1}{V_p} \int\_{V_p}
	e^{i k \bm{r'} \cdot (\bm{n_i} - \bm{n_s})}
	e^{i (m - 1) k (\bm{r'} - \bm{r_i}) \cdot \bm{n_i}} dV'
$$

with the corresponding surface integral (cf. Eqn. 1x.14).

Let us first attempt to evaluate Eqn. 23.4 directly. As before, we shall consider the case of a homogeneous spherical particle. Eqn. 22.7, 22.9-22.12 remain applicable; however, Eqn. 22.13 takes a more complicated form due to the additional complex exponential factor


---

Recall that the scattering angle \\(\theta = \arccos(\bm{n_i} \cdot \bm{n_s})\\) can be defined in two different ways:

$$ \tag{22.7}
	\bm{n_i} \cdot \bm{n_s} = \cos(\theta),
	\quad
	| \bm{n_i} \times \bm{n_s} | = \sin(\theta),
	\quad
	\frac{|\bm{n_i} - \bm{n_s}|}{2}
	= \sqrt{\frac{1 - \bm{n_i} \cdot \bm{n_s}}{2}}
	= \sin(\theta/2).
$$

$$ \tag{22.9}
	\bm{w} = \frac{\bm{n_i} - \bm{n_s}}{|\bm{n_i} - \bm{n_s}|},
	\quad
	\bm{v} = \frac{\bm{n_i} \times \bm{n_s}}{| \bm{n_i} \times \bm{n_s} |},
	\quad
	\bm{u} = \bm{v} \times \bm{w},
$$



Using this convention, the point \\(\bm{r'}\\) in the interior of the particle has the following set of Cartesian coordinates:

$$ \tag{22.10}
\bm{r'} =
\begin{bmatrix}
	r\_{u}' \cr
	r\_{v}' \cr
	r\_{w}' \cr
\end{bmatrix},
\quad
	|\bm{r'}| = \sqrt{(r\_{u}')^2 + (r\_{v}')^2 + (r\_{w}')^2}.
$$

For spherical or ellipsoidal particles, it is highly convenient to utilize [cylindrical coordinates](https://en.wikipedia.org/wiki/Cylindrical_coordinate_system) \[[4](#references) (ch. 7.11)\]:

$$ \tag{22.11}
\bm{r'} =
\begin{bmatrix}
	r\_{u}' \cr
	r\_{v}' \cr
	r\_{w}' \cr
\end{bmatrix} =
\begin{bmatrix}
	\sqrt{(r\_{u}')^2 + (r\_{v}')^2} \cos(\chi) \cr
	\sqrt{(r\_{u}')^2 + (r\_{v}')^2} \sin(\chi) \cr
	r\_{w}' \cr
\end{bmatrix} = a
\begin{bmatrix}
	c \cos(\chi) \cr
	c \sin(\chi) \cr
	b \cr
\end{bmatrix}.
$$
UVW coords are obtained from uvw by a rotation




Furthermore,



Combined with 22.11,

$$ \tag{23.xx}
	e^{i (m - 1) k (\bm{r'} - \bm{r_i}) \cdot \bm{n_i}}
	= e^{i \frac{\rho}{2} \left(
		b \cos(\theta/2) - c \cos(\chi) \sin(\theta/2) +
		\sqrt{1 - ( c \cos(\chi) \cos(\theta/2) + b \sin(\theta/2) )^2 - ( c \sin(\chi) )^2}
	\right)}
$$


\[Add an illustration here\]

In particular, for a spherical particle (symmetric with respect to the azimuthal angle \\(\phi\\)), we may set

$$ \tag{22.12}
	y(\theta) = 2 \sin(\theta/2) x,
$$

and easily calculate \[[4](#references) (ch. 7.21)\]

$$ \tag{22.13}
\begin{aligned}
	I_{rgb}(\theta)
	&= \frac{3}{4 \pi a^3} \int_{-1}^{1}
	\int_{-\pi}^{\pi} \int_{0}^{\sqrt{1 - b^2}}
	a^2 c \thinspace dc \thinspace d\chi \thinspace
	e^{i y b} a \thinspace db
	\cr
	&= \frac{3}{4} \int_{-1}^{1}
	\left( 1 - b^2 \right)
	e^{i y b} db
	= \frac{3}{2} \int_{0}^{1}
	\left( 1 - b^2 \right)
	\cos(y b) db
	\cr
	&= \frac{3 \sin(y) - 3 y \cos(y)}{y^3}
	 = \sqrt{\frac{9 \pi}{2 y^3}} J_{3/2}(y)
	 = \frac{3 j_1(y)}{y},
\end{aligned}
$$

$$ \tag{23.4} \small
\begin{aligned}
	I_{ada}(\theta, \phi)
	&= \frac{3}{4 \pi a^3} \int_{-1}^{1}
	\int_{-\pi}^{\pi} \int_{0}^{\sqrt{1 - b^2}}
	e^{i (m - 1) k (\bm{r'} - \bm{r_i}) \cdot \bm{n_i}}
	a^2 c \thinspace dc \thinspace d\chi \thinspace
	e^{i y b} a \thinspace db
	\cr
	&= \frac{3}{4 \pi a^3} \int_{-1}^{1}
	\int_{-\pi}^{\pi} \int_{0}^{\sqrt{1 - b^2}}
	e^{i \frac{\rho}{2} \left(
		b \cos(\theta/2) - c \cos(\chi) \sin(\theta/2) +
		\sqrt{1 - ( c \cos(\chi) \cos(\theta/2) + b \sin(\theta/2) )^2 - ( c \sin(\chi) )^2}
	\right)}
	a^2 c \thinspace dc \thinspace d\chi \thinspace
	e^{i y b} a \thinspace db
	\cr
\end{aligned}
$$

TLDR: Just integrate in UVW coords.

where \\(J_n\\) and \\(j_n\\) are the ordinary and the spherical Bessel functions of the first kind, respectively (cf. Eqn. 18.48).

Thus, for a homogeneous spherical particle, Eqn. 22.6 can be written as

$$ \tag{22.14}
	\bm{E_s}(\bm{r}, \omega)
	\approx \frac{e^{i k(\omega) r}}{k(\omega) r}
	\big( m^2(\omega) - 1 \big) x^3(\omega)
	\frac{j_1\negthinspace\big( |\bm{n_i} - \bm{n_s}| x(\omega) \big)}{|\bm{n_i} - \bm{n_s}| x(\omega)}
	\big( \mathcal{I} - \bm{n_s} \otimes \bm{n_s} \big) \cdot
	\bm{E_0}.
$$


#### Scattering Matrix

The geometric formulation of the problem creates a strong dependence on the shape of the particle.

$$ \tag{15.8}
	\bm{e_z} = \bm{n_i}, \quad
	\bm{e_y} = \frac{\bm{n_i} \times \bm{n_s}}{| \bm{n_i} \times \bm{n_s} |}, \quad
	\bm{e_x} = \bm{e_y} \times \bm{e_z}.
$$

If \\(\bm{n_i}\\) and \\(\bm{n_s}\\) are collinear, a supporting vector must take the role of \\(\bm{n_s}\\) in order to fix the orientation of the coordinate frame.

Imagine that the *scattering coordinate system* \\(xyz\\)

Imagine that the *scattering coordinate system* \\(xyz\\) is initially aligned with the *laboratory reference frame* \\(XYZ\\), such that

$$ \tag{15.9}
	\bm{e_Z} = \bm{n_i}, \quad
	\bm{e_Y} = \bm{n_i} \times \bm{n_p}, \quad
	\bm{e_X} = \bm{n_p},
$$

where \\(\bm{n_p}\\) is the principal axis of the particle (specifically chosen to be orthogonal to \\(\bm{n_i}\\)).

In the case of a spherical particle, its surface can be parametrized using spherical coordinates.

Substitution of Eqn. 23.2 into the volume integral equation yields

13.12, the far-field expression of the volume integral equation



---

#### Second-Order Born Approximation

The biggest drawback of the Rayleigh-Gans-Born approximation stems from the assumption that the internal field is virtually indistinguishable from the incident field. While that drastically simplifies the resulting expressions, it severely limits their range of applicability, since the the relative wavenumber \\(m\\) may not appreciably deviate from unity. Physically, it implies that the particle is composed of dipoles that do not interact with each other. Formally, this interaction is captured by the Born series expansion (cf. Eqn. 14.3, 14.4, 14.20)

$$ \tag{24.1} \small
\begin{aligned}
	\bm{E}(\bm{r}, \omega)
	&= \bm{E_i}(\bm{r}, \omega)
	\cr
	&+ k^2(\omega) \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot
	\bm{E_i}(\bm{r'}, \omega) dV'
	\cr
	&+ k^4(\omega) \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot
	\int\_{V} \big( m^2(\bm{r''}, \omega) - 1 \big)
	\mathcal{G}\_e \big( \bm{r'}, \bm{r''}, k(\omega) \big) \cdot
	\bm{E_i}(\bm{r''}, \omega) dV'' dV'
	\cr
	&+ ...
\end{aligned}
$$

The first two lines of Eqn. 24.1 represent the first-order Born (also known as the Rayleigh-Gans-Born) approximation, which we covered in the previous section. If we add the term from the third line of Eqn. 24.1, we obtain the second-order Born approximation. According to Eqn. 14.22, the associated expression of the transition dyadic \\(\mathcal{T}\\) is

$$ \tag{24.2} \small
	\mathcal{T} \big( \bm{r}, \bm{r'}, k(\omega), \omega \big)
	\approx k^2(\omega) \big( m^2(\bm{r}, \omega) - 1 \big) \big( \delta(\bm{r} - \bm{r'}) \mathcal{I} + k^2(\omega) \big( m^2(\bm{r'}, \omega) - 1 \big) \mathcal{G}\_e (\bm{r}, \bm{r'}, k) \big).
$$

Inspection of Eqn. 24.1 may lead to a (false) conclusion that we can directly substitute the expression of

$$ \tag{24.3}
	I_{ada}(\bm{r'}, \omega) = k^2(\omega) \int\_{V} \big( m^2(\bm{r''}, \omega) - 1 \big)
	\mathcal{G}\_e \big( \bm{r'}, \bm{r''}, k(\omega) \big) \cdot
	\bm{E_i}(\bm{r''}, \omega) dV''
$$

obtained using the the Rayleigh-Gans-Born approximation into the volume integral equation. Unfortunately, that is not the case because, this time, we can no longer use the far-field expression of the electric dyadic \\(\mathcal{G}\_e\\).

Let us begin by expanding the expression of the electric phasor using Eqn. 7.2:

$$ \tag{24.4}
	I_{ada}(\bm{r}, \omega) = k^2(\omega) \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\mathcal{G}\_e \big( \bm{r}, \bm{r'}, k(\omega) \big) \cdot
	\bm{E_0}(\bm{n_i}, \omega) e^{i k(\omega) (\bm{r'} \cdot \bm{n_i})} dV'.
$$

As for the electric dyadic \\(\mathcal{G}\_e\\), in order to avoid dealing with the singularity (by introducing the depolarization dyadic), it is convenient to move the derivatives outside the integral, as shown by Eqn. 9.24:

$$ \tag{24.5} \small
	I_{ada}(\bm{r}, \omega)
	= \left( k^2(\omega) \mathcal{I} + \nabla \otimes \nabla \right) \cdot
	\int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	g(\bm{r} - \bm{r'}, k(\omega))
	\bm{E_0}(\bm{n_i}, \omega) e^{i k(\omega) (\bm{r'} \cdot \bm{n_i})} dV'.
$$

This naturally leads us to consider a simpler integral

$$ \tag{24.6}
\begin{aligned}
	J_{ada}(\bm{r}, \omega)
	&= \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	g(\bm{r} - \bm{r'}, k(\omega))
	e^{i k(\omega) (\bm{r'} \cdot \bm{n_i})} dV'
	\cr
	&= \int\_{V} \big( m^2(\bm{r'}, \omega) - 1 \big)
	\frac{e^{i k(\omega) |\bm{r} - \bm{r'}|}}{4 \pi |\bm{r} - \bm{r'}|}
	e^{i k(\omega) (\bm{r'} \cdot \bm{n_i})} dV'.
\end{aligned}
$$

Above, Eqn. 9.10 of the scalar Green function was used.

For simplicity, we shall only consider the simplest case of a homogeneous spherical particle of radius \\(a\\). Eqn. 24.6 can then be simplified to

$$ \tag{24.7}
\begin{aligned}
	J_{ada}(\bm{r}, \omega)
	&= \frac{k(\omega) \big( m^2(\omega) - 1 \big)}{4 \pi} \int\_{V}
	\frac{e^{i k(\omega) |\bm{r} - \bm{r'}|} e^{i k(\omega) (\bm{r'} \cdot \bm{n_i})}}{k(\omega) |\bm{r} - \bm{r'}|} dV'.
\end{aligned}
$$

---

TODO

[Rayleigh](https://en.wikipedia.org/wiki/Rayleigh_scattering) - [Tyndall](https://en.wikipedia.org/wiki/Tyndall_effect) - [Mie](https://en.wikipedia.org/wiki/Mie_scattering)

Inelastic [Raman scattering](https://en.wikipedia.org/wiki/Raman_scattering)

Field Magnitude <-> Wave Amplitude

Is a spherical cavity the right choice for the depolarization operator?

Should I call relative wavenumber \\(w\\) and relative permeability \\(m\\)?

How to extract a plane wave inside the sphere? Use the internal coefficients? But the corresponding vector spherical wave functions are defined in terms of \\(k r\\) rather than \\(m k r\\)...

Find the transition operator... Try both surface and volume IE!

---

## Acknowledgments

Bohren & Huffman, Larry Travis, Pharr & Jakob, Jeppe Frisvad (Mie scattering), Robin Green (spherical harmonics), Naty Hoffman (Rayleigh scattering), Eugene d'Eon and Andrea Weidlich.

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
17. Stratton, J. A. [Electromagnetic Theory](https://doi.org/10.1002/9781119134640) (1941).
18. Courant, R., & Hilbert, D. [Methods of Mathematical Physics](https://doi.org/10.1002/9783527617210) (1953).
19. Morse, P. M., & Feshbach, H. [Methods of Theoretical Physics](https://www.worldcat.org/title/methods-of-theoretical-physics/oclc/468127798) (1953).
20. Berg, M. J., Sorensen, C. M., & Chakrabarti, A. [A New Explanation of the Extinction Paradox](https://doi.org/10.1016/j.jqsrt.2010.08.024) (2011).
21. Markel, V. A. [Extinction, Scattering and Absorption of Electromagnetic Waves in the Coupled-Dipole Approximation](https://doi.org/10.1016/j.jqsrt.2019.106611) (2019).
22. Brillouin, L. [The Scattering Cross Section of Spheres for Electromagnetic Waves](https://doi.org/10.1063/1.1698280) (1949).
23. Chýlek, P., & Zhan, J. [Interference Structure of the Mie Extinction Cross Section](https://doi.org/10.1364/JOSAA.6.001846) (1989).
24. Chýlek, P. [Partial-Wave Resonances and the Ripple Structure in the Mie Normalized Extinction Cross Section ](https://doi.org/10.1364/JOSA.66.000285) (1976).
25. Tsang, L., Kong, J. A., & Ding, K. H. [Scattering of Electromagnetic Waves: Theories and Applications](10.1002/0471224286) (2000).
26. Rayleigh, L. [On the Transmission of Light Through an Atmosphere Containing Small Particles in Suspension, and on the Origin of the Blue of the Sky](https://doi.org/10.1080/14786449908621276) (1899).
27. Raman, C. V. [On the Molecular Scattering of Light in Water and the Colour of the Sea](https://doi.org/10.1098/rspa.1922.0025) (1922).
28. Wald, R. [Advanced Classical Electromagnetism](https://press.princeton.edu/books/hardcover/9780691220390/advanced-classical-electromagnetism) (2022).
29. Long, D. A. [The Raman Effect: A Unified Treatment of the Theory of Raman Scattering by Molecules](https://doi.org/10.1002/0470845767) (2002).
30. Cohen-Tannoudji, C., Diu, B., & Laloe, F. [Quantum Mechanics, Volume 1](https://www.wiley.com/en-us/Quantum+Mechanics%2C+Volume+1%3A+Basic+Concepts%2C+Tools%2C+and+Applications%2C+2nd+Edition-p-9783527822713) (2nd ed., 2020).
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

Multiply Eqn. 15a by \\(\sin{\phi\_y}\\) and subtract Eqn. 15b multiplied by \\(\sin{\phi\_x}\\). Similarly, multiply Eqn. 15a by \\(\cos{\phi\_y}\\) and subtract Eqn. 15b multiplied by \\(\cos{\phi\_x}\\). The result is

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

The significance of Eqn. 19 can be better seen if we express it using complex numbers

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

Combining Eqn. 19 and 20, we obtain

$$ \tag{21}
\begin{cases}
   \phantom{\pm} \bar{E}\_a \cos(\phi + \omega t) = E\_x \cos{\psi} + E\_y \sin{\psi} \cr
   \pm           \bar{E}\_b \sin(\phi + \omega t) = E\_y \cos{\psi} - E\_x \sin{\psi}
\end{cases}
$$

Substitution of Eqn. 15a and 15b and expansion of sines and cosines of sums yields

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

Squaring Eqn. 23a-23d and adding them allows us to obtain the identity

$$ \tag{24} \bar{E}\_a^2 + \bar{E}\_b^2 = \bar{E}\_x^2 + \bar{E}\_y^2. $$

Similarly, multiplying Eqn. 23a by 23d and 23b by 23c and computing their sum, we get

$$ \tag{25} \pm \bar{E}\_a \bar{E}\_b = \bar{E}\_x \bar{E}\_y \sin(\phi\_x - \phi\_y). $$

The combination of Eqn. 24 and 25 can be used to compute the width and the height of the ellipse.

To compute the rotation angle \\(\psi\\), multiply Eqn. 23a by 23b and Eqn. 23c by 24d, and simplify the resulting identity:

$$ \tag{26} \Big( \bar{E}\_x^2 - \bar{E}\_y^2 \Big) \sin{2 \psi} = 2 \bar{E}\_x \bar{E}\_y \cos{2 \psi} \cos(\phi\_x - \phi\_y), $$

$$ \tag{27} \tan{2 \psi} = \frac{2 \bar{E}\_x \bar{E}\_y \cos(\phi\_x - \phi\_y)}{\bar{E}\_x^2 - \bar{E}\_y^2}. $$

Auxiliary angle ...

Non-paraxial polarization ???

-->
