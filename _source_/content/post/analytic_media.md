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

Rendering of participating media is an important aspect of every modern renderer. When I say participating media, it's not talking about just not fog, fire and smoke - *everything is volumetric*. All matter is composed of [atoms](https://en.wikipedia.org/wiki/Atom), which can be sparsely (e.g. in a gas) or densely (e.g. in a solid) packed in space. Whether you consider the particle or the wave nature of [light](https://en.wikipedia.org/wiki/Light), it penetrates all matter (yes, even [metals](http://webhome.phy.duke.edu/~qelectron/group/group_reading_Born_and_Wolf.pdf)) to a certain degree, and interacts with its atoms along the way. The nature and the degree of "participation" depends on the material in question.

<!--more-->

Light-material interaction is usually quantified in terms of absorption (conversion of the electromagnetic energy of photons into the kinetic energy of atoms, which manifests itself as the reduction of light intensity) and scattering (change of direction). Therefore, it is common to describe participating media using the *absorption coefficient* \\(\bm{\sigma_a}\\) and the *scattering coefficient* \\(\bm{\sigma_s}\\). These coefficients give the probability of the corresponding event (absorption or scattering, respectively) as the photon travels the distance of 1 meter through the medium, which implies that the units of measurement are \\(m^{-1}\\).

$$ \tag{1} \bm{\sigma_t} = \bm{\sigma_a} + \bm{\sigma_s} $$

is the [attenuation](https://en.wikipedia.org/wiki/Attenuation_coefficient) (or extinction) coefficient, which gives the probability of absorption or out-scattering as light travels through the volume. All these coefficients are typically spectral (vary with wavelength), and can be represented as vectors (boldface notation).

A more artist-friendly parametrization uses the [single-scattering (volume) albedo](https://en.wikipedia.org/wiki/Single-scattering_albedo) \\(\bm{\alpha}\\):

$$ \tag{2} \bm{\alpha} = \frac{\bm{\sigma_s}}{\bm{\sigma_t}} $$

and the (also spectral!) [mean free path](https://en.wikipedia.org/wiki/Mean_free_path) \\(\bm{d}\\)

$$ \tag{3} \bm{d} = \frac{1}{\bm{\sigma_t}}. $$

Integral of the attenuation coefficient along the ray segment from the point \\(\bm{x}\\) to the point \\(\bm{y}\\) corresponds to the [optical depth](https://en.wikipedia.org/wiki/Optical_depth) (or optical thickness):

$$ \tag{4} \bm{\tau}(\bm{x}, \bm{y}) = \int\_{0}^{\Vert \bm{y} - \bm{x} \Vert} \bm{\sigma_t} \Bigg(\bm{x} + s \frac{\bm{y} - \bm{x}}{\Vert \bm{y} - \bm{x} \Vert} \Bigg) ds, $$

Once we know optical depth, it's easy to compute transmittance using the [Beer-Lamber law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law):

$$ \tag{5} \bm{T}(\bm{x}, \bm{y}) = e^{-\bm{\tau}(\bm{x}, \bm{y})}, $$

While optical depth can take any non-negative value, transmittance is restricted to the unit interval \\([0, 1]\\). Opacity is, then,

$$ \tag{6} \bm{O}(\bm{x}, \bm{y}) = 1 - \bm{T}(\bm{x}, \bm{y}). $$

It's worth briefly mentioning how transmittance and optical depth are composited in presence of several overlapping participating media. Optical depth is additive, while transmittance is multiplicative.

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

where sample locations \\(\bm{y_i}\\) are distributed according to the [PDF](https://en.wikipedia.org/wiki/Probability_density_function) \\(p\\).

We can [importance sample](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Importance_Sampling.html) the integrand using the analytic product (effectively, by assuming that the value of \\(\bm{L_s}\\) varies slowly). This means that we must choose the PDF that is proportional to the analytic product (since the PDF determines the sample distribution). A valid PDF integrates to 1 over its domain (which, in our case, spans the range from 0 to \\(t\_{max}\\)), so we must normalize the analytic product using the value of its integral:

$$ \tag{10} \bm{I}(\bm{x}, \bm{v}, t)
	= \int\_{0}^{t} \bm{T}(\bm{x},\bm{x} + s \bm{v}) \bm{\sigma_s}(\bm{x} + s \bm{v}) ds
	= \int\_{0}^{t} \bm{\alpha}(\bm{x} + s \bm{v}) \bm{\sigma_t}(\bm{x} + s \bm{v}) e^{-\int\_{0}^{s} \bm{\sigma_t}(\bm{x} + u \bm{v}) du} ds
$$

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

# Types of Analytic Participating Media

Sometimes, it's convenient to specify the concentration (density) of the medium, and not just its effective optical properties. For example, the attenuation coefficient can be computed using the following formula:

$$ \tag{14} \bm{\sigma_t} = \rho \bm{\mu_t}, $$

where \\(\rho\\) is the [volumetric mass density](https://en.wikipedia.org/wiki/Mass_density) (measured in units of \\(kg/m^{3}\\)) and \\(\bm{\mu_t}\\) is the [mass attenuation coefficient](https://en.wikipedia.org/wiki/Mass_attenuation_coefficient) (in units of \\(m^{2}/kg\\)).

If your background is in real-time rendering, you may have heard of [constant, linear and exponential fog](http://www.terathon.com/lengyel/Lengyel-UnifiedFog.pdf). These names refer to variation of density, typically with respect to height, and can be used to model height fog and atmospheric scattering.

All of these types of participating media assume that values of the volume albedo \\(\bm{\alpha}\\) and the mass attenuation coefficient \\(\bm{\mu_t}\\) are *constant* across the entire volume.

## Constant Density

A constant (or homogeneous) medium has uniform density across the entire volume:

$$ \tag{15} \rho_c = k. $$

This formulation makes computing optical depth (Equation 4) and transmittance (Equation 5) easy:

$$ \tag{16} \bm{\tau_c}(\bm{x}, \bm{y}) = \bm{\mu_t} k \Vert \bm{y} - \bm{x} \Vert, $$

The value of the integral (Equation 8) is thus:

$$ \tag{17} \bm{I_c}(\bm{x}, \bm{v}, t)
	= \bm{\alpha} \bm{\mu_t} k \int\_{0}^{t} e^{-\bm{\mu_t} k s} ds
	= \bm{\alpha} (1 - e^{-\bm{\mu_t} k t}).
$$

We can immediately recognize that the meaning of the integral:

$$ \tag{18} \bm{I_c}(\bm{x}, \bm{v}, t) = \bm{\alpha} \bm{O}(\bm{x}, \bm{x} + t \bm{v}), $$

which is simply the product of volume albedo and volume opacity (Equations 2 and 6).

## Linear Variation of Density with Altitude in Rectangular Coordinates

This is your typical "linear height fog on flat Earth" case. Density varies with the third (\\(z\\)) coordinate, which we interpret as the altitude:

$$ \tag{19} \rho\_{lp}(\bm{x}) = m x_3 + k. $$

This formulation can be reduced to homogeneous media by setting \\(m = 0\\).

Expressions of optical depth and transmittance remain fairly simple:

$$ \tag{20} \begin{aligned}
\bm{\tau\_{lp}}(\bm{x}, \bm{y})
	&= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} \Big( m \big(x_3 + s (y_3 - x_3) / t \big) + k \Big) ds \cr
	&= \bm{\mu_t} (m x_3 + k) t + \bm{\mu_t} (y_3 - x_3) \frac{m}{t} \int\_{0}^{t} s ds \cr
	&= \bm{\mu_t} (m x_3 + k) t + \bm{\mu_t} m \frac{y_3 - x_3}{2} t \cr
	&= \bm{\mu_t} \Big(m \frac{x_3 + y_3}{2} + k \Big) t,
\end{aligned} $$

which is the product of the average attenuation coefficient and the length of the interval, as expected.

The integral looks a little bit complicated:

$$ \tag{21} \bm{I\_{lp}}(\bm{x}, \bm{v}, t)
	= \bm{\alpha} \bm{\mu_t} \int\_{0}^{t} \big(m (x_3 + s v_3) + k \big) e^{- \bm{\mu_t} \big(m (x_3 + s v_3 / 2) + k \big) s} ds
$$

If you plug this expression into a computer algebra system, you will get the following result:

$$ \tag{22} \bm{I\_{lp}}(\bm{x}, \bm{v}, t)
	= \bm{\alpha} \Big(1 - e^{- \bm{\mu_t} \big(m (x_3 + t v_3 / 2) + k \big) t} \Big)
	= \bm{\alpha} \bm{O}(\bm{x}, \bm{x} + t \bm{v}). $$

This expression looks [familiar](https://www.youtube.com/watch?v=z_KmNZNT5xw). Indeed, it has the same form as the Equation 18.

## Exponential Variation of Density with Altitude in Rectangular Coordinates

We can replace the linear density function with an exponential:

$$ \tag{23} \rho\_{ep}(\bm{x}) = k e^{-x_3 / H}, $$

where \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height), measured in meters. Another way to think about it is as of the reciprocal of the falloff exponent \\(n\\):

$$ \tag{24} \rho\_{ep}(\bm{x}) = k e^{-n x_3}. $$

Setting \\(n = 0\\) results in a homogeneous medium.

Plugging this in into the equation of optical depth, we obtain the following expression:

$$ \tag{25} \begin{aligned}
\bm{\tau\_{ep}}(\bm{x}, \bm{y})
	&= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n \big(x_3 + s (y_3 - x_3) / t \big)} ds \cr
	&= \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} e^{-s n (y_3 - x_3) / t} ds \cr
	&= \bm{\mu_t} k t e^{-n x_3} \frac{1 - e^{-n (y_3 - x_3)}}{n (y_3 - x_3)} \cr
	&= \bm{\mu_t} k t \frac{e^{-n x_3} - e^{-n y_3}}{n (y_3 - x_3)}.
\end{aligned} $$

We can still integrate transmittance analytically (if you think that's complicated, wait until you see read the next section):

$$ \tag{26} \begin{aligned}
\bm{I\_{ep}}(\bm{x}, \bm{v}, t)
	&= \bm{\alpha} \bm{\mu_t} \int\_{0}^{t} k e^{-n (x_3 + s v_3)} \mathrm{exp} \Bigg( - \bm{\mu_t} k s e^{-n x_3} \frac{1 - e^{-n \big((x_3 + s v_3) - x_3 \big)}}{n \big((x_3 + s v_3) - x_3 \big)} \Bigg) ds \cr
	&= \bm{\alpha} \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t} e^{-n s v_3} \mathrm{exp} \Bigg( - \bm{\mu_t} k s e^{-n x_3} \frac{1 - e^{-n s v_3}}{n s v_3} \Bigg) ds \cr
	&= \bm{\alpha} \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t} e^{-n s v_3} \mathrm{exp} \Bigg( - \bm{\mu_t} k e^{-n x_3} \frac{1 - e^{-n s v_3}}{n v_3} \Bigg) ds \cr
	&= \bm{\alpha} \bm{\mu_t} k e^{-n x_3} \mathrm{exp} \Bigg( -\bm{\mu_t} \frac{k e^{-n x_3}}{n v_3} \Bigg) \int\_{0}^{t} e^{-n s v_3} \mathrm{exp} \Bigg( \bm{\mu_t} \frac{k e^{-n x_3}}{n v_3} e^{-n s v_3} \Bigg) ds.
\end{aligned} $$

Let's simplify the calculation via the following substitution:

$$ \tag{27} \beta = \frac{k e^{-n x_3}}{n v_3}. $$

The integral then becomes

$$ \tag{28} \begin{aligned}
\bm{I\_{ep}}(\bm{x}, \bm{v}, t)
	&= \bm{\alpha} \bm{\mu_t} k e^{-n x_3} e^{- \bm{\mu_t} \beta} \int\_{0}^{t} e^{\bm{\mu_t} \beta e^{-n s v_3}} e^{-n s v_3} ds \cr
	&= \bm{\alpha} \bm{\mu_t} k e^{-n x_3} e^{- \bm{\mu_t} \beta} \frac{e^{\bm{\mu_t} \beta} - e^{\bm{\mu_t} \beta e^{-n t v_3}}}{\bm{\mu_t} \beta n v_3} \cr
	&= \bm{\alpha} e^{- \bm{\mu_t} \beta} \Big( e^{\bm{\mu_t} \beta} - e^{\bm{\mu_t} \beta e^{-n t v_3}} \Big) \cr
	&= \bm{\alpha} \Big( 1 - e^{- \bm{\mu_t} \beta + \bm{\mu_t} \beta e^{-n t v_3}} \Big) \cr
	&= \bm{\alpha} \Big( 1 - e^{- \bm{\mu_t} \beta (1 - e^{-n t v_3} )} \Big) \cr
	&= \bm{\alpha} \Bigg( 1 - \mathrm{exp} \Big( - \bm{\mu_t} k t e^{-n x_3} \frac{1 - e^{-n t v_3}}{n t v_3} \Big) \Bigg).
\end{aligned} $$

Comparing the result to the equation 25, we can observe that, [again](https://www.youtube.com/watch?v=MDpuTqBI0RM),

$$ \tag{29} \bm{I\_{ep}}(\bm{x}, \bm{v}, t) = \bm{\alpha} \bm{O}(\bm{x}, \bm{x} + t \bm{v}). $$

## Exponential Variation of Density with Altitude in Spherical Coordinates

This is where things get interesting. We would like to model an exponential density distribution on a spherical planet:

$$ \tag{30} \rho\_{es}(\bm{x}) = k e^{-h(\bm{x}) / H} = k e^{-(\Vert \bm{x} - \bm{c} \Vert - R) / H} = k e^{-n (\Vert \bm{x} - \bm{c} \Vert - R)}, $$

where \\(\bm{c}\\) is the center of the planet, \\(R\\) is its radius, \\(h\\) is the altitude, and \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height) as before. In this case, \\(k\\) represents the density at the sea level.

Before we proceed with the derivation, it's helpful to understand the geometric setting (after all, a picture is worth a thousand words). Personally, I found the article by Christian Schüler in [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) to be a very helpful introduction to the relevant concepts, and I encourage you to check it out if you have questions after reading my explanation below.

### Geometric Configuration

Our goal is to simplify the problem using the inherent spherical symmetry of the setting. Please take a look at the diagram below:

{{< figure src="/img/spherical_param.png">}}

We start by recognizing the fact that every ordered pair of position and direction \\(\lbrace \bm{x}, \bm{v} \rbrace\\) can be represented as a pair of radial distance and zenith angle \\(\lbrace r, \theta \rbrace\\).

In order to find the parametric equation of the radial distance along the ray, we can use a right triangle with legs \\(r_0\\) and \\(t_0\\) corresponding to the initial pair of position and direction \\(\lbrace r, \theta \rbrace\\):

$$ \tag{31} r_0(r, \theta) = r \mathrm{sin}(\theta), $$
$$ \tag{32} t_0(r, \theta) = r \mathrm{cos}(\theta). $$

The expression of optical depth is then:

$$ \tag{33} \begin{aligned}
\bm{\tau\_{es}}(\bm{x}, \bm{y})
	&= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n h(s)} ds \cr
	&= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n (\sqrt{r_0^2 + (t_0 + s)^2} - R)} ds \cr
	&= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n (\sqrt{(r \mathrm{sin}(\theta))^2 + (r \mathrm{cos}(\theta) + s)^2} - R)} ds \cr
	&= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n (\sqrt{r^2 + 2 r s \mathrm{cos}(\theta) + s^2} - R)} ds \cr
	&= \bm{\mu_t} \frac{k}{n} e^{-n (r - R)} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} n e^{n (r - \sqrt{r^2 + 2 r s \mathrm{cos}(\theta) + s^2})} ds.
\end{aligned} $$

The resulting integral is very complex. (Don't believe me? Try evaluate it analytically!). In order to make our life easier, we will factor out the nested integral, and extend the upper limit of integration to infinity. With the following change of variables:

$$ \tag{34} z = r n \qquad u = s n, $$

the nested integral then becomes what is known in the physics community as the [Chapman](https://en.wikipedia.org/wiki/Chapman_function) (or obliquity) function :

$$ \tag{35} C_h(z, \theta) = \int\_{0}^{\infty} e^{z - \sqrt{z^2 + 2 z u \mathrm{cos}(\theta) + u^2}} du. $$

It's interesting to consider the physical meaning of the function. Generally speaking, the value of a line integral of density (such as given by \\(\bm{\tau} / \bm{\mu_t}\\)) corresponds to mass. The integral

$$ \tag{36} \int\_{h = (r - R)}^{\infty} k e^{-n s} ds = \frac{k}{n} e^{-n h} $$

corresponds to the mass of the vertical column starting at height \\(h\\).

Optical depth, then, is the *product* of the mass of the vertical column *times* the value of the obliquity function (which, intuitively, transforms the vertical column into an oblique one) *times* the mass attenuation coefficient.

\\(\Chi\\)

You may have just had a little [déjà vu](https://www.youtube.com/watch?v=z_KmNZNT5xw)...
Spectral coefficients... Hero wavelength? Average coefficient? Single sample MIS?
Note that for RGB surface albedo, it's not just 3 spectral samples, but rather the response of the trichromatic visual system to a complicated spectral distribution.

https://www.youtube.com/watch?v=z_KmNZNT5xw