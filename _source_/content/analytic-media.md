---
title: "Sampling Analytic Participating Media"
date: 2019-06-23T17:53:34-07:00
hidden: true
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

Light-material interaction is usually quantified in terms of absorption (conversion of  electromagnetic energy of photons into kinetic energy of atoms, which manifests itself as the reduction of light intensity) and scattering (change of direction). Therefore, it is common to describe participating media using the *absorption coefficient* \\(\bm{\sigma_a}\\) and the *scattering coefficient* \\(\bm{\sigma_s}\\). These coefficients give the probability of the corresponding event (absorption and scattering, respectively) as the photon travels the distance of 1 meter through the medium, which implies the units of measurement are \\(m^{-1}\\).

$$ \tag{1} \bm{\sigma_t} = \bm{\sigma_a} + \bm{\sigma_s} $$

is the [attenuation](https://en.wikipedia.org/wiki/Attenuation_coefficient) (or extinction) coefficient, which gives the probability of absorption or out-scattering as light travels through the volume. All these coefficients are typically spectral (vary with wavelength), and can be represented as vectors (boldface notation).

A more artist-friendly parametrization uses the [single-scattering (volume) albedo](https://en.wikipedia.org/wiki/Single-scattering_albedo) \\(\bm{\alpha}\\):

$$ \tag{2} \bm{\alpha} = \frac{\bm{\sigma_s}}{\bm{\sigma_t}} $$

and the (also spectral!) [mean free path](https://en.wikipedia.org/wiki/Mean_free_path) \\(\bm{d}\\)

$$ \tag{3} \bm{d} = \frac{1}{\bm{\sigma_t}}. $$

Integral of the attenuation coefficient along the ray segment from the point \\(\bm{x}\\) to the point \\(\bm{y}\\) corresponds to the [optical depth](https://en.wikipedia.org/wiki/Optical_depth) (or optical thickness):

$$ \tag{4} \bm{\tau}(\bm{x}, \bm{y}) = \int\_{0}^{\Vert \bm{y} - \bm{x} \Vert} \bm{\sigma_t} \Bigg(\bm{x} + s \frac{\bm{y} - \bm{x}}{\Vert \bm{y} - \bm{x} \Vert} \Bigg) ds, $$

Given the value of optical depth, it's easy to compute transmittance using the [Beer–Lambert–Bouguer law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law):

$$ \tag{5} \bm{T}(\bm{x}, \bm{y}) = e^{-\bm{\tau}(\bm{x}, \bm{y})}, $$

While optical depth can take any non-negative value, transmittance is restricted to the unit interval \\([0, 1]\\). Volume opacity is, then,

$$ \tag{6} \bm{O}(\bm{x}, \bm{y}) = 1 - \bm{T}(\bm{x}, \bm{y}). $$

It's worth briefly mentioning how transmittance and optical depth are composited in presence of several overlapping participating media: optical depth is additive, while transmittance is multiplicative.

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

We can [importance sample](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Importance_Sampling.html) the integrand according to the analytic product (effectively, by assuming that the value of \\(\bm{L_s}\\) varies slowly). This means that we must choose the PDF that is proportional to the analytic product (since the PDF determines the sample distribution). A valid PDF integrates to 1 over its domain (which, in our case, spans the range from 0 to \\(t\_{max}\\)), so we must normalize the analytic product using the value of its integral:

$$ \tag{10}
\bm{A}(\bm{x}, \bm{v}, t)
	= \int\_{0}^{t} \bm{T}(\bm{x},\bm{x} + s \bm{v}) \bm{\sigma_s}(\bm{x} + s \bm{v}) ds,
$$

$$ \tag{11} p(\bm{x}, \bm{y}) = \frac{T(\bm{x},\bm{y}) \sigma_s(\bm{y})}{A(\bm{x}, \frac{\bm{y} - \bm{x}}{\Vert \bm{y} - \bm{x} \Vert}, t\_{max})}. $$

This radically simplifies evaluation of the estimate:

$$ \tag{12} L(\bm{x}, \bm{v}) \approx A(\bm{x}, \bm{v}, t\_{max}) \frac{1}{N} \sum\_{i=1}^{N} L_s(\bm{y_i}, \bm{v}). $$

Additionally, in order to distribute the samples according to the PDF, we must be able to [invert](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Sampling_Random_Variables.html#TheInversionMethod) the [CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function) \\(P\\), which, again, contains the integral \\(A\\):

$$ \tag{13} P(\bm{x}, \bm{v}, t)
    = \int\_{0}^{t} p(\bm{x}, \bm{x} + s \bm{v}) ds
    = \int\_{0}^{t} \frac{T(\bm{x},\bm{x} + s \bm{v}) \sigma_s(\bm{x} + s \bm{v})}{A(\bm{x}, \bm{v}, t\_{max})} ds
    = \frac{A(\bm{x}, \bm{v}, t)}{A(\bm{x}, \bm{v}, t\_{max})}.
$$

Clearly, there is some utility in being able to evaluate this integral. Let's take a closer look at some examples.

# Types of Analytic Participating Media

Sometimes, it's convenient to specify the concentration (density) of the medium, and not just its effective optical properties. For example, the attenuation coefficient can be computed using the following formula:

$$ \tag{14} \bm{\sigma_t} = \rho \bm{\mu_t}, $$

where \\(\rho\\) is the [volumetric mass density](https://en.wikipedia.org/wiki/Mass_density) (measured in units of \\(kg/m^{3}\\)) and \\(\bm{\mu_t}\\) is the [mass attenuation coefficient](https://en.wikipedia.org/wiki/Mass_attenuation_coefficient) (in units of \\(m^{2}/kg\\)).

If your background is in real-time rendering, you may have heard of [constant, linear and exponential fog](http://www.terathon.com/lengyel/Lengyel-UnifiedFog.pdf). These names refer to variation of density, typically with respect to height, and can be used to model height fog and atmospheric scattering.

Note that continuous variation of density results in continuous [variation of the index of refraction](https://www.sciencedirect.com/science/article/pii/S0021850208001183), which means that [light no longer travels along straight lines](https://en.wikipedia.org/wiki/Atmospheric_refraction). We will ignore this for for simplicity.

All of these types of participating media assume a constant albedo value, which leads to a simplified expression of the analytic integral:

$$ \tag{15} \begin{aligned}
\bm{A}(\bm{x}, \bm{v}, t)
	&= \int\_{0}^{t} \bm{\sigma_s}(\bm{x} + s \bm{v}) \bm{T}(\bm{x},\bm{x} + s \bm{v})  ds \cr
	&= \int\_{0}^{t} \Big( \bm{\alpha}(\bm{x} + s \bm{v}) \bm{\sigma_t}(\bm{x} + s \bm{v}) \Big) \bm{T}(\bm{x},\bm{x} + s \bm{v})  ds \cr
	&\approx \bm{\alpha} \int\_{0}^{t} \bm{\sigma_t}(\bm{x} + s \bm{v}) \bm{T}(\bm{x},\bm{x} + s \bm{v}) ds \cr
	&= \bm{\alpha} \bm{I}(\bm{x}, \bm{v}, t).
\end{aligned} $$

We will refer to \\(\bm{I}\\) as the *extinction-transmittance integral*.

## Constant Density

A constant (or homogeneous) medium has uniform density across the entire volume:

$$ \tag{16} \rho_c = k. $$

This formulation makes computing optical depth (Equation 4) and transmittance (Equation 5) easy:

$$ \tag{17} \bm{\tau_c}(\bm{x}, \bm{y}) = \bm{\mu_t} k \Vert \bm{y} - \bm{x} \Vert, $$

The value of the integral (Equation 8) is thus:

$$ \tag{18} \bm{I_c}(\bm{x}, \bm{v}, t)
    = \bm{\mu_t} k \int\_{0}^{t} e^{-\bm{\mu_t} k s} ds
    = 1 - e^{-\bm{\mu_t} k t}.
$$

We can immediately recognize that the meaning of the integral:

$$ \tag{19} \bm{I_c}(\bm{x}, \bm{v}, t) = \bm{O}(\bm{x}, \bm{x} + t \bm{v}), $$

which is simply volume opacity (Equation 6).

The sampling "recipe" for distance \\(t\\) can be found by [inverting](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Sampling_Random_Variables.html#TheInversionMethod) the [CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function) the CDF \\(P^-1(u)\\) (Equation 13):

$$ \tag{20} t = P^{-1}(\bm{x}, \bm{v}, \xi)
	= -\frac{\mathrm{log}(1 - \xi O(\bm{x}, \bm{x} + t\_{max} \bm{v}))}{\mu_t k}
	= -\frac{\mathrm{log}(1 - \xi (1 - e^{-\mu_t k t\_{max}}))}{\mu_t k}, $$

which is consistent with [other publications](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html).

## Linear Variation of Density with Altitude in Rectangular Coordinates

This is your typical "linear height fog on flat Earth" case. Density varies with the third (\\(z\\)) coordinate, which we interpret as the altitude:

$$ \tag{21} \rho\_{lp}(\bm{x}) = m x_3 + k. $$

This formulation can be reduced to homogeneous media by setting \\(m = 0\\).

Expressions of optical depth and transmittance remain fairly simple:

$$ \tag{22} \begin{aligned}
\bm{\tau\_{lp}}(\bm{x}, \bm{y})
    &= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} \Big( m \big(x_3 + s (y_3 - x_3) / t \big) + k \Big) ds \cr
    &= \bm{\mu_t} (m x_3 + k) t + \bm{\mu_t} (y_3 - x_3) \frac{m}{t} \int\_{0}^{t} s ds \cr
    &= \bm{\mu_t} (m x_3 + k) t + \bm{\mu_t} m \frac{y_3 - x_3}{2} t \cr
    &= \bm{\mu_t} \Big(m \frac{x_3 + y_3}{2} + k \Big) t,
\end{aligned} $$

which is the product of the average attenuation coefficient and the length of the interval, as expected.

The integral looks a little bit complicated:

$$ \tag{23} \bm{I\_{lp}}(\bm{x}, \bm{v}, t)
    = \bm{\mu_t} \int\_{0}^{t} \big(m (x_3 + s v_3) + k \big) e^{- \bm{\mu_t} \big(m (x_3 + s v_3 / 2) + k \big) s} ds
$$

If you plug this expression into a [computer algebra system](https://en.wikipedia.org/wiki/Computer_algebra_system), you will get the following result:

$$ \tag{24} \bm{I\_{lp}}(\bm{x}, \bm{v}, t)
    = 1 - e^{- \bm{\mu_t} \big(m (x_3 + t v_3 / 2) + k \big) t}
    = \bm{O}(\bm{x}, \bm{x} + t \bm{v}), $$

which, [surprisingly](https://www.youtube.com/watch?v=z_KmNZNT5xw), exactly matches the form of the Equation 19.

The sampling "recipe" for distance \\(t\\) is given by the following formula:

$$ \tag{25}
	t = -\frac{k + m x_3}{m v_3}
	\pm \sqrt{\Bigg( \frac{k + m x_3}{m v_3} \Bigg)^2 - 2 \frac{\mathrm{log} \big(1 - \xi O(\bm{x}, \bm{x} + t\_{max} \bm{v}) \big)}{\mu_t m v_3}},
$$

where the sign of the square root is the sign of \\(v_3\\). Please note that homogeneous participating media along the ray (\\(m v_3 = 0\\)) require special treatment.

## Exponential Variation of Density with Altitude in Rectangular Coordinates

We can replace the linear density function with an exponential:

$$ \tag{26} \rho\_{ep}(\bm{x}) = k e^{-x_3 / H}, $$

where \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height), measured in meters. Another way to think about it is as of the reciprocal of the falloff exponent \\(n\\):

$$ \tag{27} \rho\_{ep}(\bm{x}) = k e^{-n x_3}. $$

Setting \\(n = 0\\) results in a homogeneous medium.

Plugging this in into the equation of optical depth, we obtain the following expression:

$$ \tag{28} \begin{aligned}
\bm{\tau\_{ep}}(\bm{x}, \bm{y})
    &= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n \big(x_3 + s (y_3 - x_3) / t \big)} ds \cr
    &= \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} e^{-s n (y_3 - x_3) / t} ds \cr
    &= \bm{\mu_t} k t e^{-n x_3} \frac{1 - e^{-n (y_3 - x_3)}}{n (y_3 - x_3)} \cr
    &= \bm{\mu_t} k t \frac{e^{-n x_3} - e^{-n y_3}}{n (y_3 - x_3)}.
\end{aligned} $$

We can still integrate transmittance analytically (if you think that's complicated, wait until you see read the next section):

$$ \tag{29} \begin{aligned}
\bm{I\_{ep}}(\bm{x}, \bm{v}, t)
    &= \bm{\mu_t} \int\_{0}^{t} k e^{-n (x_3 + s v_3)} \mathrm{exp} \Bigg( - \bm{\mu_t} k s e^{-n x_3} \frac{1 - e^{-n \big((x_3 + s v_3) - x_3 \big)}}{n \big((x_3 + s v_3) - x_3 \big)} \Bigg) ds \cr
    &= \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t} e^{-n s v_3} \mathrm{exp} \Bigg( - \bm{\mu_t} k s e^{-n x_3} \frac{1 - e^{-n s v_3}}{n s v_3} \Bigg) ds \cr
    &= \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t} e^{-n s v_3} \mathrm{exp} \Bigg( - \bm{\mu_t} k e^{-n x_3} \frac{1 - e^{-n s v_3}}{n v_3} \Bigg) ds \cr
    &= \bm{\mu_t} k e^{-n x_3} \mathrm{exp} \Bigg( -\bm{\mu_t} \frac{k e^{-n x_3}}{n v_3} \Bigg) \int\_{0}^{t} e^{-n s v_3} \mathrm{exp} \Bigg( \bm{\mu_t} \frac{k e^{-n x_3}}{n v_3} e^{-n s v_3} \Bigg) ds.
\end{aligned} $$

Let's simplify the calculation via the following substitution:

$$ \tag{30} \beta = \frac{k e^{-n x_3}}{n v_3}. $$

The integral then becomes

$$ \tag{31} \begin{aligned}
\bm{I\_{ep}}(\bm{x}, \bm{v}, t)
    &= \bm{\mu_t} k e^{-n x_3} e^{- \bm{\mu_t} \beta} \int\_{0}^{t} e^{\bm{\mu_t} \beta e^{-n s v_3}} e^{-n s v_3} ds \cr
    &= \bm{\mu_t} k e^{-n x_3} e^{- \bm{\mu_t} \beta} \frac{e^{\bm{\mu_t} \beta} - e^{\bm{\mu_t} \beta e^{-n t v_3}}}{\bm{\mu_t} \beta n v_3} \cr
    &= e^{- \bm{\mu_t} \beta} \Big( e^{\bm{\mu_t} \beta} - e^{\bm{\mu_t} \beta e^{-n t v_3}} \Big) \cr
    &= 1 - e^{- \bm{\mu_t} \beta + \bm{\mu_t} \beta e^{-n t v_3}} \cr
    &= 1 - e^{- \bm{\mu_t} \beta (1 - e^{-n t v_3} )} \cr
    &= 1 - \mathrm{exp} \Bigg( - \bm{\mu_t} k t e^{-n x_3} \frac{1 - e^{-n t v_3}}{n t v_3} \Bigg).
\end{aligned} $$

Comparing the result to the Equation 28, we can observe that, [again](https://www.youtube.com/watch?v=MDpuTqBI0RM),

$$ \tag{32} \bm{I\_{ep}}(\bm{x}, \bm{v}, t) = \bm{O}(\bm{x}, \bm{x} + t \bm{v}). $$

Inversion of the CDF allows us to sample the distance \\(t\\):

$$ \tag{33} t = -\frac{1}{n v_3} \log \left(1 + \frac{ n v_3 \log \big(1 - \xi O \left(\bm{x}, \bm{x} + t\_{max} \bm{v} \right) \big)}{\mu_t k e^{-n x_3}}\right). $$

Please note that homogeneous participating media along the ray (\\(n v_3 = 0\\)) require special treatment.

## Exponential Variation of Density with Altitude in Spherical Coordinates

This is where things get interesting. We would like to model an exponential density distribution on in spherical coordinates:

$$ \tag{34} \rho\_{es}(\bm{x}) = k e^{-h(\bm{x}) / H} = k e^{-(\Vert \bm{x} - \bm{c} \Vert - R) / H} = k e^{-n (\Vert \bm{x} - \bm{c} \Vert - R)}, $$

where \\(\bm{c}\\) is the center of the planet, \\(R\\) is its radius, \\(h\\) is the altitude, and \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height) as before. In this context, \\(k\\) and \\(\bm{\mu_t} k\\) represent the density and the value of the attenuation coefficient at the sea level, respectively.

Before we proceed with the derivation, it's helpful to understand the geometric setting (after all, a picture is worth a thousand words). Personally, I found the article by Christian Schüler in [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) to be a very helpful introduction to the relevant concepts, and I encourage you to check it out if you have questions after reading my explanation below.

### Geometric Configuration of a Spherical Atmosphere

Our goal is to simplify the problem using its inherent spherical symmetry. Please take a look at the diagram below:

{{< figure src="/img/spherical_param.png">}}

We start by recognizing the fact that every ordered pair of position and direction \\(\lbrace \bm{x}, \bm{v} \rbrace\\) can be reduced to a pair of radial distance and zenith angle \\(\lbrace r, \theta \rbrace\\).

In order to find the parametric equation of altitude \\(h\\) along the ray, we can use a right triangle with legs \\(r_0\\) and \\(t_0\\) corresponding to the initial pair of position and direction \\(\lbrace r, \theta \rbrace\\):

$$ \tag{35} r_0(r, \theta) = r \mathrm{sin}{\theta}, $$
$$ \tag{36} t_0(r, \theta) = r \mathrm{cos}{\theta}. $$

If we ignore [atmospheric refraction](https://en.wikipedia.org/wiki/Atmospheric_refraction), we obtain the following expression of optical depth:

$$ \tag{37} \begin{aligned}
\bm{\tau\_{es}}(\bm{x}, \bm{y})
    &= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n h(s)} ds \cr
    &= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n (\sqrt{r_0^2 + (t_0 + s)^2} - R)} ds \cr
    &= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n (\sqrt{(r \mathrm{sin}{\theta})^2 + (r \mathrm{cos}{\theta} + s)^2} - R)} ds \cr
    &= \bm{\mu_t} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} k e^{-n (\sqrt{r^2 + 2 r s \mathrm{cos}{\theta} + s^2} - R)} ds \cr
    &= \bm{\mu_t} \frac{k}{n} e^{-n (r - R)} \int\_{0}^{t = \Vert \bm{y} - \bm{x} \Vert} e^{n (r - \sqrt{r^2 + 2 r s \mathrm{cos}{\theta} + s^2})} n ds.
\end{aligned} $$

The resulting integral is very complex. (Don't believe me? Try evaluating it analytically!). In order to make our life easier, we will factor out the nested integral, and extend the upper limit of integration to infinity. With the following change of variables:

$$ \tag{38} u = n s \qquad z = n r \qquad Z = n R, $$

the nested integral becomes what is known in the physics community as the [Chapman function](https://en.wikipedia.org/wiki/Chapman_function) (or the obliquity function, or the relative optical air mass) \\(C\\):

$$ \tag{39} C(z, \mathrm{cos}{\theta}) = \int\_{0}^{\infty} e^{z - \sqrt{z^2 + 2 z u \mathrm{cos}{\theta} + u^2}} du. $$

It's convenient to define the rescaled Chapman function \\(C_r\\):

$$ \tag{40} C_r(z, Z, \mathrm{cos}{\theta}) = e^{Z - z} C(z, \mathrm{cos}{\theta}) = \int\_{0}^{\infty} e^{Z - \sqrt{z^2 + 2 z u \mathrm{cos}{\theta} + u^2}} du, $$

which has a better numerical behavior, and further simplifies the expression of optical depth:

$$ \tag{41} \begin{aligned}
\bm{\tau\_{es}}(\bm{x}, \bm{y})
    = \bm{\mu_t} \frac{k}{n} \Bigg( &C_r \Big(n \Vert \bm{x} - \bm{c} \Vert, n R, \mathrm{cos}{(\bm{x} - \bm{c}, \bm{y} - \bm{x})} \Big) - \cr
    &C_r \Big(n \Vert \bm{y} - \bm{c} \Vert, n R, \mathrm{cos}{(\bm{y} - \bm{c}, \bm{y} - \bm{x})} \Big) \Bigg),
\end{aligned} $$

where

$$ \tag{42}
\mathrm{cos}{(\bm{n}, \bm{v})}
	= \Big\langle \frac{\bm{n}}{\Vert \bm{n} \Vert}, \frac{\bm{v}}{\Vert \bm{v} \Vert} \Big\rangle $$

is a shorthand for the cosine of the angle between the normal and the vector vector. What the Equation 41 says is that we should evaluate the optical depth integral (along the entire ray, from 0 to \\(\infty\\)) twice, at the start and at the end of the interval, and subtract the results.

From the practical standpoint, it's worth noting that, for short distances, this expression of optical depth can be well approximated by its "rectangular" version (which ignores the curvature), potentially saving many ALU instructions.

It's interesting to consider the physical meaning of the Chapman function. Generally speaking, the value of a line integral of density (such as given by \\(\bm{\tau} / \bm{\mu_t}\\)) corresponds to mass. Therefore, the integral

$$ \tag{43} \int\_{h = (r - R)}^{\infty} k e^{-n s} ds = \frac{k}{n} e^{-n h} $$

corresponds to mass of an infinitely tall vertical column with its lower end at height \\(h\\).

Optical depth, then, is the *product* of the mass of the vertical column *and* the value of the obliquity function (which, intuitively, gives the absolute optical air mass along the ray) *times* the mass attenuation coefficient.

### Examining the Chapman Function

It's always a good idea to examine a function visually, as a graph. Let's do that.

{{< figure src="/img/chapman_ref.png" caption="*Plot of the Chapman function for \\(r = 6600\\).*">}}

Above, I plotted values of the Chapman function (vertical axis) varying with the angle \\(\theta\\) (horizontal axis, in degrees) for different values of the scale height \\(H\\): \\(1\\) (dark blue), \\(10\\) (orange), \\(20\\) (green), \\(40\\) (red), \\(60\\) (purple), \\(80\\) (brown), \\(100\\) (cyan).
Arguably, the first two are the most important, since they roughly correspond to scale heights of aerosols and air of Earth's atmosphere. It's also nice to be able to support larger values to model atmospheres on [other planets](https://en.wikipedia.org/wiki/Scale_height#Planetary_examples).

Being an obliquity function, \\(C(z, 0) = 1\\). The function varies slowly, as long as the angle is far from the horizon (which suggests an opportunity for a [small angle approximation](https://en.wikipedia.org/wiki/Small-angle_approximation)).

The Chapman function has an [analytic expression](https://en.wikipedia.org/wiki/Closed-form_expression#Analytic_expression) derived by [Miroslav Kocifaj](https://ui.adsabs.harvard.edu/abs/1996CoSka..26...23K/abstract):

$$ \tag{44} C(z, \mathrm{cos}{\theta}) = \frac{1}{2} \mathrm{cos}{\theta} + \frac{1}{2} \Big(\frac{1}{\sqrt{z}} + 2 \sqrt{z} - \sqrt{z} (\mathrm{cos}{\theta})^2 \Big) \sqrt{\frac{\pi}{2}} \Big[ e^{\big( \frac{1}{\sqrt{2}} \sqrt{z} \mathrm{cos}{\theta} \big)^2} \mathrm{erfc}{\Big(\frac{1}{\sqrt{2}} \sqrt{z} \mathrm{cos}{\theta}\Big)} \Big], $$

which, unfortunately, is not [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression#Analytic_expression), since it contains the [complementary error function](http://mathworld.wolfram.com/Erfc.html) \\(\mathrm{erfc}\\).

Unfortunately, even after expending a considerable amount of time and effort, I have been unable to re-derive this expression. I have a nagging suspicion that this is not a full analytic solution, but rather a truncated series expansion (similar to [this one](https://www.sciencedirect.com/science/article/pii/S0022407300001072)). Also, while the formula does work for angles beyond 90 degrees, it deviates from the numerically evaluated integral rather quickly.

For the zenith angle of 90 degrees, it reduces to

$$ \tag{45} C_h(z) = \frac{1}{2} \sqrt{\frac{\pi}{2}} (\frac{1}{\sqrt{z}} + 2 \sqrt{z}). $$

Beyond the 90 degree angle, the following identity can be used:

$$ \tag{46} C_l(z, \mathrm{cos}{\theta}) = 2 e^{z - z \mathrm{sin}{\theta}} C_h(z \mathrm{sin}{\theta}) - C(z, -\mathrm{cos}{\theta}), $$

which means that we must find a position \\(\bm{p}\\) (sometimes called the [periapsis](https://en.wikipedia.org/wiki/Apsis) point, see the diagram in the previous section) along the ray where the ray direction is orthogonal to the surface normal, evaluate the horizontal Chapman function there (integrate twice, forwards and backwards, e.i. along the entire line, from \\(-\infty\\) to \\(\infty\\)), and subtract the value of the Chapman function at the original position with the reversed direction (towards the atmospheric boundary), which isolates the integral to the desired ray segment.

Christian Schüler proposes an approximation of the Chapman function for the upper hemisphere in his [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) article:

$$ \tag{47} C_{u\\_cs}(z, \mathrm{cos}{\theta}) \approx \frac{C_h(z)}{(C_h(z) - 1) \mathrm{cos}{\theta} + 1}. $$

It's a pretty good approximation, especially considering the cost.

{{< figure src="/img/chapman_chris.png" caption="*Plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

However, if you care about accuracy, and plot the the relative error graph, it paints a slightly less attractive picture.

{{< figure src="/img/chapman_chris_error.png" caption="*Relative error plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

The physics literature has [many approximations](https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1029/2011JD016706) of the Chapman function. Unfortunately, most of them are specific to Earth's atmosphere.

Instead, we can take a different, simpler approach. Instead of approximating the entire function (for which we have an analytic expression), all we have to do is approximate \\(\mathrm{erfc}\\) (or, more specifically, the term of the Equation 44 inside the square brackets). Luckily, the physics literature already has an efficient [approximation](https://rmets.onlinelibrary.wiley.com/doi/full/10.1002/asl.154) (for positive values of \\(x\\)) that we can use:

$$ \tag{48} e^{x^2} \mathrm{erfc}(x) \approx \frac{2.911}{(2.911 - 1) \sqrt{\pi x^2} + \sqrt{\pi x^2 + 2.911^2}}. $$

The resulting approximation of the Chapman function has up to 50 times lower relative error, and is acceptable for our use case (the full expression of transmittance features even lower numerical error).

{{< figure src="/img/chapman_erfc_error.png" caption="*Relative error plot of the new approximation of the Chapman function for \\(r = 6600\\).*">}}

For reference, the full numerical approximation of the Chapman function for the upper hemisphere is:

$$ \tag{49} C_{u\\_a}(z, \mathrm{cos}{\theta}) \approx \frac{\mathrm{cos}{\theta}}{2} + \frac{0.761643 (1 + z (2 - \mathrm{cos}^2{\theta}))}{z \mathrm{cos}{\theta} + \sqrt{z (1.47721 + 0.273828 z \mathrm{cos}^2{\theta})}}. $$

Sample code which implements the Equation 40 is listed below.

```c++
float ChapmanUpperApprox(float z, float cosTheta)
{
    float c = cosTheta;
    float n = 0.761643 * ((1 + 2 * z) - (z * c * c));
    float d = z * c + sqrt(z * (1.47721 + 0.273828 * (z * c * c)));

    return 0.5 * c + (n * rcp(d));
}

float ChapmanHorizontal(float z)
{
    float r = rsqrt(z);
    float s = z * r; // sqrt(z)

    return 0.626657 * (r + 2 * s);
}

// z = (r / H), Z = (R / H).
float RescaledChapmanFunction(float z, float Z, float cosTheta)
{
    float sinTheta = sqrt(saturate(1 - cosTheta * cosTheta));

    // cos(Pi - theta) = -cos(theta).
    float ch = ChapmanUpperApprox(z, abs(cosTheta)) * exp(Z - z); // Rescaling adds 'exp'

    if (cosTheta < 0)
    {
        // Ch(z, theta) = 2 * exp(z - z_0) * Ch(z_0, Pi/2) - Ch(z, Pi - theta).
        // z_0 = r_0 / H = (r / H) * sin(theta) = z * sin(theta).
        float z_0 = z * sinTheta;
        float chP = ChapmanHorizontal(z_0) * exp(Z - z_0); // Rescaling adds 'exp'

        ch = 2 * chP - ch;
    }

    return ch;
}
```

A small but important note is that we can always use \\( \vert \mathrm{cos}{\theta} \vert \\) to evaluate the upper part of the Chapman function since, even if the angle is greater than 90 degrees, the ray direction is reversed, and the cosine is negated.

### Evaluating Optical Depth Using the Chapman Function

A numerical approximation of the Chapman function, in conjunction with the Equation 41, allows us to evaluate optical depth along an arbitrary ray segment.

However, since the approximation of the Chapman function contains a branch (upper/lower hemisphere), using the full formulation may be unnecessarily expensive for many use cases.

For example, an implementation of the [Precomputed Atmospheric Scattering](https://dl.acm.org/citation.cfm?id=2383467) paper requires an ability to evaluate optical depth along the ray, where the ray may hit either the spherical planet, or nothing at all.

To start with, we may want to know whether the ray intersects the planet, or, in other words, whether it points above the horizon.

{{< figure src="/img/spherical_param_2.png">}}

Since a "horizon" ray always intersects the planet at the 90 degree angle with respect to the normal at the intersection point, the (obtuse) horizon angle \\( \mathrm{cos}{\theta_h} \\) at the query point can be found using basic trigonometry:

$$ \tag{50} \mathrm{cos}{\theta_h} = -\frac{\mathrm{adjacent}}{\mathrm{hypotenuse}} = -\frac{\sqrt{r^2 - R^2}}{r} = -\sqrt{1 - (R/r)^2}. $$

If the ray points above the horizon, the regular Chapman function gets the job done. And if the ray points below the horizon, it may seem that we need to evaluate the full Chapman function twice (as per Equation 41), once at the starting point \\(\bm{x}\\), where the ray points into the lower hemisphere, and once at the intersection point \\(\bm{y}\\) (using the same ray direction).

Please take a look at the updated diagram below:

{{< figure src="/img/spherical_param_3.png">}}

However, we can utilize the fact that \\(\bm{\tau}(\bm{x}, \bm{y}) = \bm{\tau}(\bm{y}, \bm{x})\\), and instead consider the ray segment \\(\bm{yx}\\) which starts at the intersection point \\(\bm{y}\\) and has a reversed direction.

Using basic trigonometry, we can deduce the cosine of the angle at the intersection point

$$ \tag{51} \mathrm{sin}{\gamma} = \frac{\mathrm{opposite}}{\mathrm{hypotenuse}} = \frac{r_0}{R} = \frac{r \mathrm{sin}{\theta}}{R} \qquad \mathrm{cos}{\gamma} = \sqrt{1 - \mathrm{sin}^2{\gamma}} $$

that determines the value of the Chapman function below horizon:

$$ \tag{52} C_b(z, \mathrm{cos}{\theta}, Z, \mathrm{cos}{\gamma}) = C_u(Z, \mathrm{cos}{\gamma}) - C_u(z, \vert \mathrm{cos}{\theta} \vert). $$

Sample code is listed below. While it's possible to use the `RescaledChapmanFunction` in this scenario, the following implementation is slightly more efficient.

```c++
float R, rcpR, H, rcpH, Z;
float3 C, seaLevelAttenuationCoefficient;

float ComputeCosineOfHorizonAngle(float r)
{
    float sinHoriz = R * rcp(r);
    return -sqrt(saturate(1 - sinHoriz * sinHoriz));
}

// Ray equation: (X + t * V).
float3 ComputeOpticalDepthAlongRay(float3 X, float3 V)
{
    float r = distance(X, C);
    float z = r * rcpH;

    float cosHoriz = ComputeCosineOfHorizonAngle(r);
    float cosTheta = dot(X - C, V) * rcp(r);
    float sinTheta = sqrt(saturate(1 - cosTheta * cosTheta));

	// cos(Pi - theta) = -cos(theta).
    float ch = ChapmanUpperApprox(z, abs(cosTheta)) * exp(Z - z); // Rescaling adds 'exp'

    if (cosTheta < cosHoriz) // Below horizon, intersect sphere
    {
        float sinGamma = (r * sinTheta) * rcpR;
        float cosGamma = sqrt(saturate(1 - sinGamma * sinGamma));
        float chY      = ChapmanUpperApprox(Z, cosGamma);

        ch = chY - ch;
    }
    else if (cosTheta < 0) // Above horizon, lower hemisphere
    {
        // Ch(z, theta) = 2 * exp(z - z_0) * Ch(z_0, Pi/2) - Ch(z, Pi - theta).
        // z_0 = r_0 / H = (r / H) * sin(theta) = z * sin(theta).
        float z_0 = z * sinTheta;
        float chP = ChapmanHorizontal(z_0) * exp(Z - z_0); // Rescaling adds 'exp'

        ch = 2 * chP - ch;
    }

    return ch * H * seaLevelAttenuationCoefficient;
}
```

If desired, it is possible to reduce divergence by utilizing `ChapmanUpperApprox` (with the cosine value of 0) instead of `ChapmanHorizontal`, but I will retain this version for clarity of exposition.

Now, let's tackle the most general case of evaluating optical depth between two points \\(\bm{x}\\) and \\(\bm{y}\\). It may seem really complex at first, when, in fact, it's very similar to the problem we just solved. We have to consider three distinct possibilities:

1\. \\(\mathrm{cos}(\bm{x} - \bm{c}, \bm{y} - \bm{x}) \geq 0 \\), which means that the ray points into the upper hemisphere with respect to the normal at the point \\(\bm{x}\\). This also means it points into the upper hemisphere at any point \\(\bm{y}\\) along the ray (I don't have a rigorous proof, but it's fairly obvious if you sketch it). Optical depth is given by the Equation 41, which we specialize by replacing \\(C\\) with \\(C_u\\) which is restricted to the upper hemisphere:

$$ \tag{53}
\bm{\tau\_{uu}}(z_x, \mathrm{cos}{\theta_x}, z_y, \mathrm{cos}{\theta_y})
    = \bm{\mu_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_u(z_x, \mathrm{cos}{\theta_x}) - e^{Z - z_y} C_u(z_y, \mathrm{cos}{\theta_y}) \Bigg),
$$

2\. \\(\mathrm{cos}(\bm{x} - \bm{c}, \bm{y} - \bm{x}) < 0 \\) and \\(\mathrm{cos}(\bm{y} - \bm{c}, \bm{y} - \bm{x}) \rangle < 0 \\) occurs e.g. when looking straight down. It's also easy to handle, we just flip the direction (by taking the absolute value of the cosine), replace the segment \\(\bm{xy}\\) with the segment \\(\bm{yx}\\) and fall back to the case 1.

3\. \\(\mathrm{cos}(\bm{x} - \bm{c}, \bm{y} - \bm{x}) \rangle < 0 \\) and \\(\mathrm{cos}(\bm{y} - \bm{c}, \bm{y} - \bm{x})  \rangle \geq 0 \\). This is the most complicated case, since we have to evaluate the Chapman function three times, twice using the position of \\(\bm{x}\\) with the direction pointing into the lower hemisphere, and once using the position of \\(\bm{y}\\) with the ray pointing in the upper hemisphere.

$$ \tag{54} \begin{aligned}
\bm{\tau\_{ul}}(z_x, \mathrm{cos}{\theta_x}, z_y, \mathrm{cos}{\theta_y})
    &= \bm{\mu_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_l(z_x, \mathrm{cos}{\theta_x}) - e^{Z - z_y} C_u(z_y, \mathrm{cos}{\theta_y}) \Bigg).
\end{aligned} $$

Sample code is listed below.

```c++
float R, rcpR, H, rcpH, Z;
float3 C, seaLevelAttenuationCoefficient;

float3 ComputeOpticalDepthAlongRaySegment(float3 X, float3 Y)
{
    float3 V = normalize(Y - X);

    float rX = distance(X, C);
    float rY = distance(Y, C);

    float zX = rX * rcpH;
    float zY = rY * rcpH;

    float cosThetaX = dot(X - C, V) * rcp(rX);
    float cosThetaY = dot(Y - C, V) * rcp(rY);

    // Potentially swap X and Y.
    // Convention: at the point Y, the ray points up.
    cosThetaX = (cosThetaY >= 0) ? cosThetaX : -cosThetaX;

    float chX = RescaledChapmanFunction(zX, Z, cosThetaX);
    float chY = ChapmanUpperApprox(zY, abs(cosThetaY)) * exp(Z - zY);

    // We may have swapped X and Y.
    float ch = abs(chX - chY);

    return ch * H * seaLevelAttenuationCoefficient;
}
```

The code above works for all finite inputs. An important special case is when the point \\(\bm{y}\\) is considered to be outside the atmosphere (if `exp(Z - zY) < EPS`, for instance). In that case, `chY = 0` is an adequate approximation.

### Sampling Exponential Media in Spherical Coordinates

Around 10 pages before, we were quite interested in evaluating the extinction-transmittance integral (Equations 15, 19, 24, 32). What about our spherical exponential distributions? Is the value of the integral still identical to opacity?

Unsurprisingly, trying to evaluate the integral analytically is an unsurmountable task. However, that shouldn't stop us from finding the answer. We can still evaluate the integral numerically, and since we know the likely outcome, we can simply plot and compare the two results.

Experimentally, it's possible to verify that, indeed, the rule holds true:

$$ \tag{55} \bm{I\_{es}}(\bm{x}, \bm{v}, t) = \bm{O}(\bm{x}, \bm{x} + t \bm{v}). $$

You can find the plot of the extinction-transmittance integral below.

{{< figure src="/img/ext_transm_int.png" caption="*Plot of the extinction-transmittance integral for \\(\mu_t k = 0.01, r = 6450, R = 6400,\\) and varying \\(H\\).*">}}

To sample proportionally to opacity, we need to find a way to invert the CDF (Equation 13). The analysis presented in the previous section indicates that there are two cases we must consider: the ray either points into the same (upper) hemisphere at both endpoints (Equation 53), or into the opposite ones (Equation 54).

First, let's establish some useful identities. Recalling the Equation 37, we can compute the radial distance from the center \\(\bm{c}\\) to the point \\(\bm{x} + t \bm{v}\\) along the ray using the following formula:

$$ \tag{56} \begin{aligned}
\mathcal{R}(\bm{x}, \bm{v}, t)
    &= \sqrt{r_0^2 + (t_0 + t)^2} \cr
    &= \sqrt{\big( \Vert \bm{x}-\bm{c} \Vert \mathrm{sin}(\bm{x}-\bm{c}, \bm{v}) \big)^2 + \big(\Vert \bm{x}-\bm{c} \Vert \mathrm{cos}(\bm{x}-\bm{c}, \bm{v}) + t \big)^2} \cr
    &= \sqrt{(r \mathrm{sin}{\theta})^2 + (r \mathrm{cos}{\theta} + t)^2}
\end{aligned} $$

The cosine of the ray direction with the normal at that point can be computed as follows:

$$ \tag{57}
\mathcal{C}(\bm{x}, \bm{v}, t)
    = \frac{\mathrm{adjacent}}{\mathrm{hypotenuse}}
    = \frac{t_0 + t}{\mathcal{R}(\bm{x}, \bm{v}, t)}
    = \frac{r \mathrm{cos}{\theta} + t}{\sqrt{(r \mathrm{sin}{\theta})^2 + (r \mathrm{cos}{\theta} + t)^2}}.
$$

This allows us to express the value of the extinction-transmittance integral, or opacity, in a compact way:

$$ \tag{58}
\bm{I\_{uu}}(\bm{x}, \bm{v}, t)
    = 1 - \mathrm{exp}\Big(-\bm{\tau\_{uu}} \big(n \mathcal{R}(\bm{x}, \bm{v}, 0), \mathcal{C}(\bm{x}, \bm{v}, 0), n \mathcal{R}(\bm{x}, \bm{v}, t), \mathcal{C}(\bm{x}, \bm{v}, t) \big) \Big).
$$

To importance sample, we must be able to solve the following equation for \\(t\\) given the value of the CDF \\(P\\):

$$ \tag{59}
P = \frac{
    1 - \mathrm{exp}\Big(-\tau\_{uu} \big(n \mathcal{R}(\bm{x}, \bm{v}, 0), \mathcal{C}(\bm{x}, \bm{v}, 0), n \mathcal{R}(\bm{x}, \bm{v}, t), \mathcal{C}(\bm{x}, \bm{v}, t) \big) \Big)}
    {1 - \mathrm{exp}\Big(-\tau\_{uu} \big(n \mathcal{R}(\bm{x}, \bm{v}, 0), \mathcal{C}(\bm{x}, \bm{v}, 0), n \mathcal{R}(\bm{x}, \bm{v}, t\_{max}), \mathcal{C}(\bm{x}, \bm{v}, t\_{max}) \big) \Big)}.
$$

The expression for the opposite hemisphere case is similar.

After several somewhat tedious algebraic manipulations, the problem can be reduced to solving the following equation:

$$ \tag{60}
q = e^{Z - n \mathcal{R}(\bm{x}, \bm{v}, t) } C_u \Big( n \mathcal{R}(\bm{x}, \bm{v}, t), \mathcal{C}(\bm{x}, \bm{v}, t) \Big),
$$

which means that we need to find the distance \\(t\\) at which the value of the rescaled Chapman function is \\(q\\).

The equation has too many parameters. We can reduce the dimensionality by solving for \\(t' = nt\\):

$$ \tag{61}
q = e^{Z - \sqrt{(z \mathrm{sin}{\theta})^2 + (z \mathrm{cos}{\theta} + t')^2} } C_u \Big( \sqrt{(z \mathrm{sin}{\theta})^2 + (z \mathrm{cos}{\theta} + t')^2}, \frac{z \mathrm{cos}{\theta} + t'}{\sqrt{(z \mathrm{sin}{\theta})^2 + (z \mathrm{cos}{\theta} + t')^2}} \Big).
$$

After getting rid of \\(n\\), the next step is to group common terms:

$$ \tag{62} \phi = z \mathrm{sin}{\theta} \qquad \psi = z \mathrm{cos}{\theta} + t', $$

$$ \tag{63}
q = e^{Z - \sqrt{\phi^2+\psi^2} } C_u \Big( \sqrt{\phi^2+\psi^2}, \frac{\psi}{\sqrt{\phi^2+\psi^2}} \Big). $$

Both \\(\phi\\) and \\(\psi\\) are known to be positive. We must solve for \\(\psi\\). \\(Z\\) could theoretically be removed, but it's here to keep the solution within a sane numerical range.

To my knowledge, this is the simplest formulation of the problem, using the fewest number of parameters. And yet, I was unable to analytically solve this equation, and I tried both the closed and the approximate forms of the Chapman function. Perhaps you will have better luck?

While that's an unfortunate development, it's a minor setback. If we can't solve the equation analytically, we can solve it numerically, using [Newton's method](https://en.wikipedia.org/wiki/Newton%27s_method), for instance. The derivative of the Chapman function exists, and is not too difficult to compute. The function is smooth, and the initial guess can be made using the approximation in the rectangular coordinates (Equation 33).

In fact, curvature of the planet can be ignored for moderate distances, making it a relatively efficient and accurate approximation. Can we exploit this idea for arbitrary distances? Let's take another look at the Equation 13. Assuming a constant albedo, the value of the CDF \\(P\\) along the ray segment of length \\(t\_{max}\\) is given as:

$$ \tag{64} P(\bm{x}, \bm{v}, t)
    = \frac{O(\bm{x} + t \bm{v})}{O(\bm{x} + t\_{max} \bm{v})}.
$$

To invert it means to find the distance \\(t\\) along the ray at which fractional opacity is equal to \\(P\\). In other words, we want to find the distance \\(t\\) along the ray at which opacity has the same value as the product of the CDF and opacity of the entire ray segment:

$$ \tag{65} O(\bm{x} + t \bm{v}) = P(\bm{x}, \bm{v}, t) O(\bm{x} + t\_{max} \bm{v}). $$

Total opacity of the ray can be efficiently computed using our analytic approximation. The only issue is solving for distance given opacity.

Let's say that we are not interested in brute-force path tracing (which would require numerical inversion). Instead, we are trying to compute radiance along the ray using the Equation 8, where \\(\bm{L_s}\\) is known (which limits us to single and pre-computed multiple scattering).

If we make the assumption that our random CDF values are ordered in ascending order, and that the sampling rate is sufficiently high, we can build an incremental sampling algorithm which effectively models piecewise-flat (or polygonal) planet. We will refer to it as *incremental importance sampling*.

It is a direct replacement for randomized ray marching, and shares the traits of being biased, but consistent. Unlike ray marching, the algorithm is intelligent, and adapts to properties of the participating medium, so it's much more efficient.

Let's try to understand how it works in more detail.

Instead of solving the Equation 65 using opacity, we could solve it using transmittance instead. Rearranging things a bit, the first iteration of the algorithm solves

$$ \tag{66} T(\bm{x} + t_1 \bm{v}) = 1 - P(\bm{x}, \bm{v}, t_1) O(\bm{x} + t\_{max} \bm{v}) = T_1. $$

It determines the (approximate) distance \\(t_1\\) to the first sample given the corresponding transmittance \\(T_1\\). The second sample starts the next edge of the polygon, so we would like to compute distance and transmittance relative to the previous sample:

$$ \tag{67} T \big(\bm{x} + t_1 \bm{v} \big) T \big((\bm{x} + t_1 \bm{v}) + (t_2 - t_1) \bm{v} \big) = T_2. $$

Or, more simply put,

$$ \tag{68} T \big((\bm{x} + t_1 \bm{v}) + \Delta t_2 \bm{v} \big) = T_2 / T_1. $$

Both Equations 66 and 68 can be solved using the Equation 33. Basically, we solve for relative distance given relative transmittance w.r.t. the previous sample.

Code that implements incremental importance sampling can be found below.

```c++
// Only monochromatic coefficients are supported by this code snippet.
float seaLevelAttenuationCoefficient, rcpSeaLevelAttenuationCoefficient;
float R, rcpR, H, rcpH, Z;
float3 C, ssAlbedo;

float SampleRectExpMedium(float transmittance, float height, float cosTheta,
                          float rcpSeaLevelAttenuationCoefficient, float rcpH)
{
    // Equation 20.
    float d = rcpSeaLevelAttenuationCoefficient * exp(height * rcpH);
    float t = -log(transmittance) * d;

    float p = cosTheta * rcpH;

    if (abs(p) > FLT_EPS)
    {
        // Equation 33.
        t = -log(1 - p * t) * rcp(p);
    }

    return t;
}

float3 IntegrateRadianceAlongRaySegment(float3 X, float3 Y, uint numSamples)
{
    // (1/seaLevelAttenuationCoefficient) for a single wavelength used for sampling.
    float  rcpA = rcpSeaLevelAttenuationCoefficient;

    float  tMax = distance(Y, X);
    float3 V    = normalize(Y - X);

    float totalOpticalDepth = ComputeOpticalDepthAlongRaySegment(X, Y);
    float totalOpacity      = OpacityFromOpticalDepth(totalOpticalDepth);

    // Compute initial parameters.
    float3 P        = X;
    float  r        = distance(P, C);
    float  cosTheta = dot(P - C, V) * rcp(r);

    float  t             = 0;
    float3 radiance      = 0;
    float  transmittance = 1;

    for (uint i = 0; i < numSamples; i++)
    {

        // Samples must be arranged in the ascending order, s.t.
        // for any i, sample[i] < sample[i + 1].
        float cdf = GetOrderedUnitIntervalRandomSample(i, numSamples);

        // Total value of transmittance (Equation 66).
        float totTransm = 1 - cdf * totalOpacity;

        // Transmittance relative to the previous sample (Equation 68).
        float relTransm = totTransm * rcp(transmittance);

        // Update the value of transmittance up to the current sample.
        transmittance = totTransm;

        // Ignore curvature of the planet (polygonal planet approximation).
        // The approximation is accurate assuming high sampling rate.
        float dt = SampleRectExpMedium(relTransm, r - R, cosTheta, rcpA, rcpH);

        // Since the distance is approximate, it's a good idea to clamp.
        t        = min(t + dt, tMax);
        P        = X + t * V;
        r        = distance(P, C);
        cosTheta = dot(P - C, V) * rcp(r);

    #if 0
        // Equation 34.
        float extinction = seaLevelAttenuationCoefficient * exp((R - r) * rcpH);

        // Equations 11. By the Equation 15, albedo cancels out.
        // The PDF value is also approximate (since the the distance 't' is).
        float pdf = extinction * transmittance / totalOpacity;
    #endif

        // Equation 12.
        // For a single wavelength, and with high enough sampling density,
        // weight = scattering * transmittance / pdf ≈ ssAlbedo * totalOpacity.
        radiance += ComputeInScatteredRadiance(P, V);
    }

    // Equation 12 (normalization).
    radiance *= ssAlbedo * totalOpacity * rcp(numSamples);

    return radiance;
}
```

# Handling Spectral Coefficients

WIP

Average coefficient across wave lengths, hero wavelength, single sample MIS...

Several components (air, aerosols)?

