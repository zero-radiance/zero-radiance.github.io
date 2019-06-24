---
title: "Sampling Analytic Participating Media"
date: 2019-06-23T17:53:34-07:00
draft: true
categories: [ "Graphics", "Math" ]
tags: [
    "Chapman Function",
    "Importance Sampling",
    "Volume Rendering",
    ]
---

Rendering of participating media is an important aspect of every modern renderer. When I say participating media, it's not talking about just not fog, fire and smoke - *everything is volumetric*. All matter is composed of [particles](https://en.wikipedia.org/wiki/Elementary_particle), which can be sparsely (e.g. in a gas) or densely (e.g. in a solid) packed in space. Whether you consider the particle or the wave nature of [light](https://en.wikipedia.org/wiki/Light), it penetrates all matter (yes, even [metals](http://webhome.phy.duke.edu/~qelectron/group/group_reading_Born_and_Wolf.pdf)) to a certain degree, and interacts with its particles along the way. The nature and the degree of "participation" depends on the material in question.

<!--more-->

Light-material interaction is usually quantified in terms of absorption (conversion of the electromagnetic energy of photons into the kinetic energy of atoms, which manifests itself as the reduction of light intensity) and scattering (change of direction). Therefore, it is common to describe participating media using the *absorption coefficient* \\(\bm{\sigma_a}\\) and the *scattering coefficient* \\(\bm{\sigma_s}\\). These coefficients give the probability of the corresponding event (absorption or scattering, respectively) as the photon travels the distance of 1 meter through the medium, which implies that the units of measurement are \\(m^{-1}\\).

$$ \tag{1} \bm{\sigma_t} = \bm{\sigma_a} + \bm{\sigma_s} $$

is the [attenuation](https://en.wikipedia.org/wiki/Attenuation_coefficient) (or extinction) coefficient, which gives the probability of absorption or out-scattering as light travels through the volume. All these coefficients are typically spectral (vary with wavelength), and can be represented as vectors (boldface notation).

A more [artist-friendly](http://www.pbr-book.org/3ed-2018/Volume_Scattering/Volume_Scattering_Processes.html#Out-ScatteringandAttenuation) parametrization uses the single-scattering (volume) albedo \\(\bm{\alpha_v}\\)

$$ \tag{2} \bm{\alpha_v} = \frac{\bm{\sigma_s}}{\bm{\sigma_t}} $$

and the (also spectral!) mean free path \\(\bm{d}\\)

$$ \tag{3} \bm{d} = \frac{1}{\bm{\sigma_s}}. $$

Integral of the attenuation coefficient along the ray segment from the point \\(\bm{x}\\) to the point \\(\bm{y}\\) corresponds to the [optical depth](https://en.wikipedia.org/wiki/Optical_depth) (or optical thickness):

$$ \tag{4} \bm{\tau}(\bm{x}, \bm{y}) = \int\_{0}^{\Vert \bm{y} - \bm{x} \Vert} \bm{\sigma_t}(\bm{x} + s \frac{\bm{y} - \bm{x}}{\Vert \bm{y} - \bm{x} \Vert}) ds, $$

Once we know optical depth, it's easy to compute transmittance using the [Beer-Lamber law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law):

$$ \tag{5} \bm{T}(\bm{x}, \bm{y}) = e^{-\bm{\tau}(\bm{x}, \bm{y})}, $$

While optical depth can take any non-negative value, transmittance is restricted to the unit interval \\([0, 1]\\). Opacity is, then,

$$ \tag{6} \bm{O}(\bm{x}, \bm{y}) = 1 - \bm{T}(\bm{x}, \bm{y}). $$

To shade our (non-emissive) medium, we must evaluate the [recursive in-scattering integral](http://www.pbr-book.org/3ed-2018/Light_Transport_II_Volume_Rendering/The_Equation_of_Transfer.html) along the ray:

$$ \tag{7} \bm{L}(\bm{x}, \bm{v})
	= \int\_{0}^{t\_{max}} \bm{T}(\bm{x},\bm{x} + s \bm{v}) \bm{\sigma_s}(\bm{x} + s \bm{v}) \int\_{S^2} f(\bm{x} + s \bm{v}, \bm{v},\bm{l}) \bm{L}(\bm{x} + s \bm{v}, \bm{l}) \bm{dl} ds,
$$

where \\(\bm{L}\\) is the amount of radiance at a certain position \\(\bm{x}\\) in a certain direction \\(\bm{v}\\), and \\(f\\) denotes the [phase function](http://www.pbr-book.org/3ed-2018/Volume_Scattering/Phase_Functions.html). To simplify notation, the maximum distance \\(t\_{max}\\) along the ray (which could correspond to the distance to the closest surface) is kept implicit.

We can evaluate this integral using one of the [Monte Carlo](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration.html) methods. The first step is to split it into two parts: the part we can evaluate analytically, and the part we can not precompute. We can group the product of transmittance and the scattering coefficient together, and leave the nested integral as the unknown term:

$$ \tag{8} \bm{L}(\bm{x}, \bm{v})
	= \int\_{0}^{t\_{max}} \bm{T}(\bm{x},\bm{x} + s \bm{v}) \bm{\sigma_s}(\bm{x} + s \bm{v}) \bm{L_s}(\bm{x} + s \bm{v}, \bm{v}) ds.
$$

The [Monte Carlo estimate](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/The_Monte_Carlo_Estimator.html) of the in-scattering integral (for a **single wavelength**) takes the following form:

$$ \tag{9} L(\bm{x}, \bm{v})
	\approx \frac{1}{N} \sum\_{i=1}^{N} \frac{T(\bm{x},\bm{y_i}) \sigma_s(\bm{y_i}) L_s(\bm{y_i}, \bm{v})}{p(\bm{x}, \bm{y_i})},
$$

where the sample locations \\(\bm{y_i}\\) are distributed according to the [PDF](https://en.wikipedia.org/wiki/Probability_density_function) \\(p\\).

We can importance sample the integrand using the analytic product (effectively, by assuming that the value of the nested integral varies slowly). This means that we must choose the PDF that is proportional to the analytic product (since the PDF determines the sample distribution). A valid PDF integrates to 1 over its domain (which, in our case, spans the range from 0 to \\(t\_{max}\\)), so we must normalize the analytic product using the value of its integral:

$$ \tag{10} \bm{I}(\bm{x}, \bm{v}, t) = \int\_{0}^{t} \bm{T}(\bm{x},\bm{x} + s \bm{v}) \bm{\sigma_s}(\bm{x} + s \bm{v}) ds, $$

$$ \tag{11} p(\bm{x}, \bm{y}) = \frac{T(\bm{x},\bm{y}) \sigma_s(\bm{y})}{I(\bm{x}, \frac{\bm{y} - \bm{x}}{\Vert \bm{y} - \bm{x} \Vert}, t\_{max})}. $$

This radically simplifies the value of the estimate:

$$ \tag{12} L(\bm{x}, \bm{v}) \approx I(\bm{x}, \bm{v}, t\_{max}) \frac{1}{N} \sum\_{i=1}^{N} L_s(\bm{y_i}, \bm{v}). $$

Additionally, in order to distribute the samples according to the PDF, we must be able to [invert](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Sampling_Random_Variables.html#TheInversionMethod) the [CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function) \\(P\\), which, again, contains the integral \\(I\\):

$$ \tag{13} P(\bm{x}, \bm{v}, t)
	= \int\_{0}^{t} p(\bm{x}, \bm{x} + s \bm{v}) ds
	= \int\_{0}^{t} \frac{T(\bm{x},\bm{x} + s \bm{v}) \sigma_s(\bm{x} + s \bm{v})}{I(\bm{x}, \bm{v}, t\_{max})} ds
	= \frac{I(\bm{x}, \bm{v}, t)}{I(\bm{x}, \bm{v}, t\_{max})}.
$$

Clearly, there is some utility in being able to evaluate this integral. Let's take a closer look at some examples.

## Types of Analytic Participating Media

Sometimes, it's convenient to specify the concentration (density) of the medium, and not just its effective optical properties. For example, the attenuation coefficient can be computed using the following formula:

$$ \tag{14} \bm{\sigma_t} = \rho \bm{\mu_t}, $$

where \\(\rho\\) is the [volumetric mass density](https://en.wikipedia.org/wiki/Mass_density) (measured in units of \\(kg/m^{3}\\)) and \\(\bm{\mu_t}\\) is the [mass attenuation coefficient](https://en.wikipedia.org/wiki/Mass_attenuation_coefficient) (in units of \\(m^{2}/kg\\)).

If your background is in real-time rendering, you may have heard of [constant, linear and exponential fog](http://www.terathon.com/lengyel/Lengyel-UnifiedFog.pdf). These names refer to variation of density, typically with respect to height, and can be used to model height fog and atmospheric scattering.

### Constant Density

A constant (or homogeneous) medium has the same density value across the entire volume:

$$ \tag{15} \rho_c = k. $$

This formulation makes computing optical depth (Equation 4) and transmittance (Equation 5) easy:

$$ \tag{16} \bm{\tau_c}(\bm{x}, \bm{y}) = \Vert \bm{y} - \bm{x} \Vert k \bm{\mu_t}, $$

$$ \tag{17} \bm{T_c}(\bm{x}, \bm{y}) = e^{-\Vert \bm{y} - \bm{x} \Vert k \bm{\mu_t}}. $$

The value of the integral (Equation 8) is thus:

$$ \tag{18} \bm{I_c}(\bm{x}, \bm{v}, t)
	= k \bm{\mu_s} \int\_{0}^{t} e^{-s k \bm{\mu_t}} ds
	= \frac{\bm{\sigma_s}}{\bm{\sigma_t}} (1 - e^{-t k \bm{\mu_t}}).
$$

We can recognize that the value of this integral has a more simple form:

$$ \tag{19} \bm{I_c}(\bm{x}, \bm{v}, t) = \bm{\alpha_v} \bm{O}(\bm{x}, \bm{x} + t \bm{v}), $$

which is simply the product of volume albedo and volume opacity (Equations 2 and 6).

### Linear Variation of Density with Height in Rectangular Coordinates

This is your typical "linear height fog on flat Earth" case. Density varies with the third (\\(z\\)) coordinate:

$$ \tag{20} \rho\_{lp}(\bm{x}) = m x_3 + k. $$

It is a generalization of the homogeneous medium (\\(m = 0\\)).

Expressions of optical depth and transmittance remain fairly simple:

$$ \tag{21} \begin{aligned}
\bm{\tau\_{lp}}(\bm{x}, \bm{y})
	&= \bm{\mu_t} \int\_{0}^{\Vert \bm{y} - \bm{x} \Vert} \Big( m \Big(x_3 + s \frac{y_3 - x_3}{\Vert \bm{y} - \bm{x} \Vert}\Big) + k \Big) ds \cr
	&= \bm{\mu_t} \Vert \bm{y} - \bm{x} \Vert (m x_3 + k) + \bm{\mu_t} m \frac{y_3 - x_3}{\Vert \bm{y} - \bm{x} \Vert} \int\_{0}^{\Vert \bm{y} - \bm{x} \Vert} s ds \cr
	&= \bm{\mu_t} \Vert \bm{y} - \bm{x} \Vert (m x_3 + k) + \frac{1}{2} \bm{\mu_t} m (y_3 - x_3) \Vert \bm{y} - \bm{x} \Vert \cr
	&= \bm{\mu_t} \Vert \bm{y} - \bm{x} \Vert \Big(m \frac{x_3 + y_3}{2} + k \Big)
\end{aligned} $$

$$ \tag{22} \bm{T\_{lp}}(\bm{x}, \bm{y}) = e^{-\bm{\tau\_{lp}}(\bm{x}, \bm{y})}. $$

The integral becomes a little bit complicated, but, with a little bit of effort, we can solve it:

$$ \tag{23} \bm{I\_{lp}}(\bm{x}, \bm{v}, t)
	= \bm{\mu_s} \int\_{0}^{t} e^{- s \bm{\mu_t} (m (x_3 + s v_3 / 2) + k)} \Big(m (x_3 + s v_3) + k \Big) ds
$$

The process is a little tedious, but if you plug this expression into a computer algebra system, you will get the following result:

$$ \tag{24} \bm{I\_{lp}}(\bm{x}, \bm{v}, t)
	= \frac{\bm{\sigma_s}}{\bm{\sigma_t}} \Big(1 - e^{- t \bm{\mu_t} (m (x_3 + t v_3 / 2) + k)} \Big)
	= \bm{\alpha_v} \bm{O}(\bm{x}, \bm{x} + t \bm{v}). $$

You may have just had a little [déjà vu](https://www.youtube.com/watch?v=z_KmNZNT5xw)...

### Exponential Variation of Density with Height in Rectangular Coordinates



Spectral coefficients... Hero wavelength? Average coefficient? Single sample MIS?

https://www.youtube.com/watch?v=z_KmNZNT5xw