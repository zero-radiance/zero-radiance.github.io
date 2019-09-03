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

In the [radiative transfer](https://archive.org/details/RadiativeTransfer) literature, light-material interaction is usually quantified in terms of absorption (conversion of electromagnetic energy of photons into kinetic energy of atoms, which manifests itself as reduction of light intensity) and scattering (change of photon's flight direction). Therefore, it is common to describe participating media using the collision coefficients: the *absorption coefficient* \\(\bm{\sigma_a}\\) and the *scattering coefficient* \\(\bm{\sigma_s}\\). These coefficients give the probability of the corresponding event per unit distance traveled, which implies the [SI units](https://en.wikipedia.org/wiki/International_System_of_Units) of measurement are \\(m^{-1}\\).

The [attenuation coefficient](https://en.wikipedia.org/wiki/Attenuation_coefficient)

$$ \tag{1} \bm{\sigma_t} = \bm{\sigma_a} + \bm{\sigma_s} $$

gives the probability of absorption or scattering (or collision rate) as a photon travels a unit distance through the volume. All these coefficients are typically spectral (vary with the wavelength \\(\lambda\\)), and can be represented as vectors (boldface notation). At this point in time, it is not entirely clear (at least to me) how to correctly perform volume rendering using tristimulus (RGB) values (which would require pre-integration using [color matching functions](https://en.wikipedia.org/wiki/CIE_1931_color_space#Color_matching_functions)), so I will focus on pure spectral rendering, which is well-defined.

A more artist-friendly parametrization uses the [single-scattering (volume) albedo](https://en.wikipedia.org/wiki/Single-scattering_albedo) \\(\bm{\alpha\_{ss}}\\)

$$ \tag{2} \bm{\alpha\_{ss}} = \frac{\bm{\sigma_s}}{\bm{\sigma_t}}, $$

which gives the probability of a photon "surviving" a collision event, and the (also spectral) [mean free path](https://en.wikipedia.org/wiki/Mean_free_path) \\(\bm{d}\\)

$$ \tag{3} \bm{d} = \frac{1}{\bm{\sigma_t}}, $$

which corresponds to the average collision-free distance.

Taking a small detour, for surfaces, the absorption coefficient is directly proportional to the [extinction coefficient](http://webhome.phy.duke.edu/~qelectron/group/group_reading_Born_and_Wolf.pdf) \\(\bm{\kappa}\\), which is the imaginary part of the [complex index of refraction](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\bm{\eta} + i \bm{\kappa}\\):

$$ \tag{4} \bm{\kappa} = \frac{\bm{\lambda \sigma_a}}{4 \pi}. $$

Therefore, a triple \\(\lbrace \bm{\eta}, \bm{\kappa}, \bm{\sigma_s} \rbrace\\) \\(\big(\\)or, alternatively, \\(\lbrace \bm{\eta}, \bm{d}, \bm{\alpha\_{ss}} \rbrace  \big) \\) is sufficient to describe both the behavior at the boundary and the multiple-scattering process (known as [subsurface scattering](https://en.wikipedia.org/wiki/Subsurface_scattering)) inside the volume, that ultimately gives rise to what we perceive as the surface albedo \\(\bm{\alpha\_{ms}}\\). Please note that certain materials (metals, in particular) require modeling of [interference](https://en.wikipedia.org/wiki/Wave_interference) to obtain expected reflectance values.

Integral of the attenuation coefficient along the span of ray segment of length \\(t\\) from the point \\(\bm{x}\\) in the direction \\(\bm{v}\\) corresponds to [optical depth](https://en.wikipedia.org/wiki/Optical_depth) (or optical thickness):

$$ \tag{5} \bm{\tau}(\bm{x}, \bm{v}, t) = \int\_{0}^{t} \bm{\sigma_t} (\bm{x}, \bm{v}, s) ds, $$

Given the value of optical depth, it's easy to compute [transmittance](https://en.wikipedia.org/wiki/Transmittance) using the [Beer–Lambert–Bouguer law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law):

$$ \tag{6} \bm{T}(\bm{x}, \bm{v}, t) = e^{-\bm{\tau}(\bm{x}, \bm{v}, t)}. $$

While optical depth can take on any non-negative value, transmittance is restricted to the unit interval, and can be interpreted as the probability of a collision-free flight. Its complement is volume opacity:

$$ \tag{7} \bm{O}(\bm{x}, \bm{v}, t) = 1 - \bm{T}(\bm{x}, \bm{v}, t). $$

It's worth briefly mentioning how transmittance and optical depth are composited in presence of several overlapping participating media: while optical depth is additive, transmittance is multiplicative.

Slightly jumping ahead, let's define the attenuation-transmittance integral as

$$ \tag{8}
      \int\_{0}^{t} \bm{\sigma_t}(\bm{x}, \bm{v}, s) \bm{T}(\bm{x}, \bm{v}, s) ds
    = \int\_{0}^{t} \bm{\sigma_t}(\bm{x}, \bm{v}, s) e^{-\bm{\tau}(\bm{x}, \bm{v}, s)} ds.
$$

If we use the [fundamental theorem of calculus](https://en.wikipedia.org/wiki/Fundamental_theorem_of_calculus) to interpret the attenuation coefficient as a derivative

$$ \tag{9} \bm{\sigma_t} (\bm{x}, \bm{v}, s) = \frac{\partial \bm{\tau}}{\partial s}, $$

we can use the one of the [exponential identities](https://en.wikipedia.org/wiki/List_of_integrals_of_exponential_functions#Integrals_involving_only_exponential_functions) to simplify the integral:

$$ \tag{10}
      \int\_{0}^{t} \frac{\partial \bm{\tau}(\bm{x}, \bm{v}, s)}{\partial s} e^{-\bm{\tau}(\bm{x}, \bm{v}, s)} ds
    = e^{-\bm{\tau}(\bm{x}, \bm{v}, 0)} - e^{-\bm{\tau}(\bm{x}, \bm{v}, t)}
    = 1 - \bm{T}(\bm{x}, \bm{v}, t)
    = \bm{O}(\bm{x}, \bm{v}, t).
$$

We will use this fact in just a moment.

To shade our (non-emissive) medium, we must evaluate the [recursive in-scattering integral](http://www.pbr-book.org/3ed-2018/Light_Transport_II_Volume_Rendering/The_Equation_of_Transfer.html) along the ray:

$$ \tag{11} \bm{L}(\bm{x}, \bm{v})
    = \int\_{0}^{t\_{max}} \bm{T}(\bm{x}, \bm{v}, s) \bm{\sigma_s}(\bm{x}, \bm{v}, s) \int\_{S^2} f(\bm{x} + s \bm{v}, \bm{v},\bm{l}) \bm{L}(\bm{x} + s \bm{v}, \bm{l}) \bm{dl} ds,
$$

where \\(\bm{L}\\) is the amount of radiance at the position \\(\bm{x}\\) in the direction \\(\bm{v}\\), and \\(f\\) denotes the [phase function](http://www.pbr-book.org/3ed-2018/Volume_Scattering/Phase_Functions.html). To simplify notation, the maximum distance \\(t\_{max}\\) along the ray (which typically corresponds to the distance to the closest boundary) is kept implicit.

We can evaluate this integral using one of the [Monte Carlo](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration.html) methods. The first step is to split it in two parts: the part we can evaluate analytically, and the part that has to be integrated numerically. We can group the product of transmittance and the scattering coefficient together, and leave the nested integral as the "numerical" term:

$$ \tag{12} \bm{L}(\bm{x}, \bm{v})
    = \int\_{0}^{t\_{max}} \bm{T}(\bm{x}, \bm{v}, s) \bm{\sigma_s}(\bm{x}, \bm{v}, s) \bm{L_s}(\bm{x} + s \bm{v}, \bm{v}) ds.
$$

Next, let's split the scattering coefficient into attenuation and albedo:

$$ \tag{13} \bm{L}(\bm{x}, \bm{v})
    = \int\_{0}^{t\_{max}} \bm{\sigma_t}(\bm{x}, \bm{v}, s) \bm{T}(\bm{x}, \bm{v}, s) \bm{\alpha\_{ss}}(\bm{x}, \bm{v}, s) \bm{L_s}(\bm{x} + s \bm{v}, \bm{v}) ds.
$$

The [Monte Carlo estimator](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/The_Monte_Carlo_Estimator.html) of the integral (for a single wavelength) takes the following form:

$$ \tag{14} L(\bm{x}, \bm{v})
    \approx \frac{1}{N} \sum\_{i=1}^{N} \frac{\sigma_t(\bm{x}, \bm{v}, s_i) T(\bm{x}, \bm{v}, s_i) \alpha\_{ss}(\bm{x}, \bm{v}, s_i) L_s(\bm{x} + s_i \bm{v}, \bm{v})}{p( s_i | \lbrace \bm{x}, \bm{v} \rbrace)},
$$

where sample locations \\(s_i\\) are distributed according to the [PDF](https://en.wikipedia.org/wiki/Probability_density_function) \\(p\\).

We can [importance sample](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Importance_Sampling.html) the integrand according to the analytic product \\(\sigma_t T\\) (effectively, by assuming that the rest of the integrand varies slowly). In order to turn it into a valid PDF, we must normalize this term over its domain (which, in our case, spans the range from 0 to \\(t\_{max}\\)):

$$ \tag{15} p(t | \lbrace \bm{x}, \bm{v} \rbrace)
    = \frac{\sigma_t(\bm{x}, \bm{v}, t) T(\bm{x}, \bm{v}, t)}{\int\_{0}^{t\_{max}} \sigma_t(\bm{x}, \bm{v}, s) T(\bm{x}, \bm{v}, s) ds}
    = \frac{\sigma_t(\bm{x}, \bm{v}, t) T(\bm{x}, \bm{v}, t)}{O(\bm{x}, \bm{v}, t\_{max})}. $$

This radically simplifies evaluation of the estimate (again, for a single wavelength):

$$ \tag{16} L(\bm{x}, \bm{v}) \approx O(\bm{x}, \bm{v}, t\_{max}) \frac{1}{N} \sum\_{i=1}^{N} \alpha\_{ss}(\bm{x}, \bm{v}, s_i) L_s(\bm{x} + s_i \bm{v}, \bm{v}). $$

It can be seen as a form of [premultiplied alpha blending](https://graphics.pixar.com/library/Compositing/), which explains why particle cards can be so convincing.

In order to distribute the samples according to the PDF, we must be able to [invert](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Sampling_Random_Variables.html#TheInversionMethod) the [CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function) \\(P\\):

$$ \tag{17} P(t | \lbrace \bm{x}, \bm{v} \rbrace)
    = \int\_{0}^{t} p(s | \lbrace \bm{x}, \bm{v} \rbrace) ds
    = \int\_{0}^{t} \frac{\sigma_t(\bm{x}, \bm{v}, s) T(\bm{x}, \bm{v}, s) ds}{O(\bm{x}, \bm{v}, t\_{max})}
    = \frac{O(\bm{x}, \bm{v}, t)}{O(\bm{x}, \bm{v}, t\_{max})}.
$$

In practice, this means that we need to solve for distance \\(t\\) given the value of optical depth \\(\tau\\):

$$ \tag{18} \tau(\bm{x}, \bm{v}, t) = -\mathrm{log} \big( 1 - P(t | \lbrace \bm{x}, \bm{v} \rbrace) O(\bm{x}, \bm{v}, t\_{max}) \big). $$

# Types of Analytic Participating Media

Sometimes, it's convenient to specify the concentration (density) of the medium, and not just its effective optical properties. For example, the attenuation coefficient can be computed using the following formula:

$$ \tag{19} \bm{\sigma_t} = \rho \bm{\mu_t}, $$

where \\(\rho\\) is the [volumetric mass density](https://en.wikipedia.org/wiki/Mass_density) (measured in units of \\(kg/m^{3}\\)) and \\(\bm{\mu_t}\\) is the [mass attenuation coefficient](https://en.wikipedia.org/wiki/Mass_attenuation_coefficient) (in units of \\(m^{2}/kg\\)).

If your background is in real-time rendering, you may have heard of [constant, linear and exponential fog](http://www.terathon.com/lengyel/Lengyel-UnifiedFog.pdf). These names refer to variation of density, typically with respect to height, and can be used to model height fog and atmospheric scattering. In most cases, the albedo is assumed to be constant.

## Constant Density

A constant (or homogeneous) medium has uniform density across the entire volume:

$$ \tag{20} \rho_c = k. $$

This formulation makes computing optical depth easy:

$$ \tag{21} \bm{\tau_c}(\bm{x}, \bm{v}, t) = \bm{\mu_t} \int\_{0}^{t} k ds =  \bm{\mu_t} k t. $$

The sampling "recipe" for distance \\(t\\) can be found by inverting the CDF:

$$ \tag{22} t = \frac{\tau_c}{\mu_t k}, $$

which is consistent with [previous work](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html).

## Linear Variation of Density with Altitude in Rectangular Coordinates

This is your typical "linear height fog on flat Earth" case. Density varies with the third (\\(z\\)) coordinate, which we interpret as the altitude:

$$ \tag{23} \rho\_{lp}(\bm{x}) = m x_3 + k. $$

This formulation can be reduced to homogeneous media by setting \\(m = 0\\).

The expression of optical depth remains simple:

$$ \tag{24}
\bm{\tau\_{lp}}(\bm{x}, \bm{v}, t)
    = \bm{\mu_t} \int\_{0}^{t} \Big( m \big(x_3 + s v_3) + k \Big) ds
    = \bm{\mu_t} \Big(m \big(x_3 + \frac{t}{2} v_3 \big) + k \Big) t,
$$

which is the product of the average attenuation coefficient and the length of the interval, as expected.

The sampling "recipe" for distance \\(t\\) is given by the following formula:

$$ \tag{25}
	t = -\frac{k + m x_3}{m v_3}
	\pm \sqrt{\Bigg( \frac{k + m x_3}{m v_3} \Bigg)^2 + \frac{2 \tau\_{lp}}{\mu_t m v_3}},
$$

where the sign in front of the square root is the sign of \\(v_3\\). Please note that homogeneous media \\( \big( m v_3 = 0 \big) \\) require special care.

## Exponential Variation of Density with Altitude in Rectangular Coordinates

We can replace the linear density function with an exponential:

$$ \tag{26} \rho\_{ep}(\bm{x}) = k e^{-x_3 / H}, $$

where \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height), measured in meters. Another way to think about it is as of the reciprocal of the falloff exponent \\(n\\):

$$ \tag{27} \rho\_{ep}(\bm{x}) = k e^{-n x_3}. $$

Setting \\(n = 0\\) results in a homogeneous medium.

After plugging it in into the formula of optical depth, we obtain the following expression:

$$ \tag{28}
\bm{\tau\_{ep}}(\bm{x}, \bm{v}, t)
    = \bm{\mu_t} \int\_{0}^{t} k e^{-n (x_3 + s v_3)} ds
    = \bm{\mu_t} k e^{-n x_3} \int\_{0}^{t} e^{-s n v_3} ds
    = \bm{\mu_t} k t e^{-n x_3} \frac{1 - e^{-t n v_3}}{n v_3}.
$$
Solving for the distance \\(t\\) is straightforward:

$$ \tag{29} t = -\frac{1}{n v_3} \log \left(1 - \frac{\tau\_{ep} n v_3}{\mu_t k e^{-n x_3}}\right). $$

Please note that homogeneous media \\( \big( n v_3 = 0 \big) \\) require special care.

Sample code is listed below.

```c++
// Spectral version.
float3 EvalOptDepthRectExpMedium(float height, float cosTheta, float t,
                                 float3 seaLvlAtt, float rcpH)
{
    // Equation 21.
    float3 d = seaLvlAtt * t;

    float p = cosTheta * rcpH;

    if (abs(p) > FLT_EPS)
    {
        // Equation 28.
        d *= exp(-height * rcpH) * (1 - exp(-t * p)) * rcp(p);
    }

    return d;
}

// Single wavelength version.
float SampleRectExpMedium(float optDepth, float height, float cosTheta,
                          float rcpSeaLvlAtt, float rcpH)
{
    // Equation 22.
    float t = optDepth * rcpSeaLvlAtt;

    float p = cosTheta * rcpH;

    if (abs(p) > FLT_EPS)
    {
        // Equation 29.
        t = -log(1 - p * t * exp(height * rcpH)) * rcp(p);
    }

    return t;
}
```

## Exponential Variation of Density with Altitude in Spherical Coordinates

This is where things get interesting. We would like to model an exponential density distribution on a sphere:

$$ \tag{30} \rho\_{es}(\bm{x}) = k e^{-h(\bm{x}) / H} = k e^{-(\Vert \bm{x} - \bm{c} \Vert - R) / H} = k e^{-n (\Vert \bm{x} - \bm{c} \Vert - R)}, $$

where \\(\bm{c}\\) is the center of the planet, \\(R\\) is its radius, \\(h\\) is the altitude, and \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height) as before. In this context, \\(k\\) and \\(\bm{\mu_t} k\\) represent the density and the value of the attenuation coefficient at the sea level, respectively.

Before proceeding with the derivation, it's helpful to understand the geometric setting (after all, a picture is worth a thousand words). Personally, I found the article by Christian Schüler in [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) to be a very helpful introduction to the relevant concepts, and I encourage you to check it out if you still have questions after reading my explanation below.

### Geometric Configuration of a Spherical Atmosphere

Our goal is to simplify the problem using its inherent spherical symmetry. Take a look at the diagram below:

{{< figure src="/img/spherical_param.png">}}

We start by recognizing the fact that every ordered pair of position and direction \\(\lbrace \bm{x}, \bm{v} \rbrace\\) can be reduced to a pair of radial distance and zenith angle \\(\lbrace r, \theta \rbrace\\).

In order to find the parametric equation of altitude \\(h\\) along the ray, we can use a right triangle with legs \\(r_0\\) and \\(t_0\\) corresponding to the initial conditions:

$$ \tag{31} r_0 = r \mathrm{sin}{\theta}, \qquad t_0 = r \mathrm{cos}{\theta}. $$

If we ignore [atmospheric refraction](https://en.wikipedia.org/wiki/Atmospheric_refraction), we obtain the following expression of optical depth:

$$ \tag{32} \begin{aligned}
\bm{\tau\_{es}}(r, \theta, t)
    &= \bm{\mu_t} \int\_{0}^{t} k e^{-n h(s)} ds \cr
    &= \bm{\mu_t} \int\_{0}^{t} k e^{-n (\sqrt{r_0^2 + (t_0 + s)^2} - R)} ds \cr
    &= \bm{\mu_t} \int\_{0}^{t} k e^{-n (\sqrt{(r \mathrm{sin}{\theta})^2 + (r \mathrm{cos}{\theta} + s)^2} - R)} ds \cr
    &= \bm{\mu_t} \int\_{0}^{t} k e^{-n (\sqrt{r^2 + 2 r s \mathrm{cos}{\theta} + s^2} - R)} ds \cr
    &= \bm{\mu_t} \frac{k}{n} e^{-n (r - R)} \int\_{0}^{t} e^{n (r - \sqrt{r^2 + 2 r s \mathrm{cos}{\theta} + s^2})} n ds.
\end{aligned} $$

The resulting integral is very complex. As a first step, let's take the nested integral, and extend the upper limit of integration to infinity. With the following change of variables:

$$ \tag{33} u = n s, \qquad z = n r, \qquad Z = n R, $$

we obtain what is known in the physics community as the [Chapman function](https://en.wikipedia.org/wiki/Chapman_function) (or the obliquity function, or the relative optical air mass) \\(C\\):

$$ \tag{34} C(z, \mathrm{cos}{\theta}) = \int\_{0}^{\infty} e^{z - \sqrt{z^2 + 2 z u \mathrm{cos}{\theta} + u^2}} du. $$

It's convenient to define the rescaled Chapman function \\(C_r\\)

$$ \tag{35} C_r(z, Z, \mathrm{cos}{\theta}) = e^{Z - z} C(z, \mathrm{cos}{\theta}) = \int\_{0}^{\infty} e^{Z - \sqrt{z^2 + 2 z u \mathrm{cos}{\theta} + u^2}} du, $$

which has a better numerical behavior, and further simplifies the expression of optical depth between \\(\bm{x}\\) and \\(\bm{y}\\):

$$ \tag{36}
\bm{\tau\_{es}}(z_x, \mathrm{cos}{\theta_x}, z_y, \mathrm{cos}{\theta_y})
    = \bm{\mu_t} \frac{k}{n} \Bigg( C_r \Big(z_x, Z, \mathrm{cos}{\theta_x} \Big) - C_r \Big(z_y, Z, \mathrm{cos}{\theta_y} \Big) \Bigg).
$$

What the Equation 36 tells us is that we should evaluate the optical depth integral twice (along the entire ray, from 0 to \\(\infty\\)), at the start and at the end of the interval, and subtract the results to "clip" the ray.

It's interesting to contemplate the physical meaning of the Chapman function. Generally speaking, the value of a line integral of density (such as given by \\(\bm{\tau} / \bm{\mu_t}\\)) corresponds to mass. Therefore, the integral

$$ \tag{37} \int\_{h = (r - R)}^{\infty} k e^{-n s} ds = \frac{k}{n} e^{-n h} $$

specifies the mass of an infinitely tall vertical column with its lower end starting at height \\(h\\).

Optical depth, then, is a *product* of the mass of the vertical column *and* the value of the obliquity function (which, intuitively, gives the absolute optical air mass along the oblique ray) *times* the mass attenuation coefficient.

### Examining the Chapman Function

It's always a good idea to examine a function visually, as a graph. Let's do that.

{{< figure src="/img/chapman_ref.png" caption="*Plot of the Chapman function for \\(r = 6600\\).*">}}

Above, I plotted values of the Chapman function (vertical axis) varying with the angle \\(\theta\\) (horizontal axis, in degrees) for different values of the scale height \\(H\\): \\(1\\) (blue), \\(10\\) (orange), \\(20\\) (green), \\(40\\) (red), \\(60\\) (purple), \\(80\\) (brown), \\(100\\) (cyan).
Arguably, the first two are the most important, since they roughly correspond to scale heights of aerosols and air of Earth's atmosphere. However, it's also nice to be able to support larger values to model atmospheres of [other planets](https://en.wikipedia.org/wiki/Scale_height#Planetary_examples).

Being an obliquity function, \\(C(z, 0) = 1\\). The function varies slowly, as long as the angle is far from horizontal (which suggests an opportunity for a [small-angle approximation](https://en.wikipedia.org/wiki/Small-angle_approximation)).

The Chapman function has an [analytic expression](https://ui.adsabs.harvard.edu/abs/1996CoSka..26...23K/abstract):

$$ \tag{38} C(z, \mathrm{cos}{\theta}) = \frac{1}{2} \mathrm{cos}{\theta} + \frac{1}{2} \Big(\frac{1}{\sqrt{z}} + 2 \sqrt{z} - \sqrt{z} (\mathrm{cos}{\theta})^2 \Big) \sqrt{\frac{\pi}{2}} \Bigg[ e^{\big( \frac{1}{\sqrt{2}} \sqrt{z} \mathrm{cos}{\theta} \big)^2} \mathrm{erfc}{\Big(\frac{1}{\sqrt{2}} \sqrt{z} \mathrm{cos}{\theta}\Big)} \Bigg], $$

which, unfortunately, is not [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression#Analytic_expression), since it contains the [complementary error function](http://mathworld.wolfram.com/Erfc.html) \\(\mathrm{erfc}\\).

Unfortunately, I was unable to re-derive this expression. I suspect that I am either missing something (accounting for continuous variation of the IOR, for instance), or perhaps this is not a full analytic solution, but rather a truncated series expansion (similar to [this one](https://www.sciencedirect.com/science/article/pii/S0022407300001072)). Also, while the formula should in theory work for angles beyond 90 degrees, it deviates from values of the numerically evaluated integral rather quickly.

For the zenith angle of 90 degrees, it reduces to

$$ \tag{39} C_h(z) = \frac{1}{2} \sqrt{\frac{\pi}{2}} (\frac{1}{\sqrt{z}} + 2 \sqrt{z}). $$

Beyond the 90 degree angle, the following identity can be used:

$$ \tag{40} C_l(z, \mathrm{cos}{\theta}) = 2 e^{z - z \mathrm{sin}{\theta}} C_h(z \mathrm{sin}{\theta}) - C(z, -\mathrm{cos}{\theta}), $$

which means that we must find the position \\(\bm{p}\\) (sometimes called the [periapsis](https://en.wikipedia.org/wiki/Apsis) point, see the diagram in the previous section) along the ray where the ray direction is orthogonal to the surface normal, evaluate the horizontal Chapman function there (twice, forwards and backwards, to cover the entire real line), and subtract the value of the Chapman function at the original position with the reversed direction (towards the atmospheric boundary), which isolates the integral to the desired ray segment.

Christian Schüler proposes an approximation of the Chapman function for the upper hemisphere in his [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) article:

$$ \tag{41} C_{u\\_cs}(z, \mathrm{cos}{\theta}) \approx \frac{C_h(z)}{(C_h(z) - 1) \mathrm{cos}{\theta} + 1}. $$

It's a pretty good approximation, especially considering the cost.

{{< figure src="/img/chapman_chris.png" caption="*Plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

However, if you care about accuracy, and plot the the relative error graph, it paints a slightly less attractive picture.

{{< figure src="/img/chapman_chris_error.png" caption="*Relative error plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

The physics literature has [many approximations](https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1029/2011JD016706) of the Chapman function. Unfortunately, most of them are specific to Earth's atmosphere.

Instead of approximating the entire function (for which we have an analytic expression), we can take a different, simpler approach. All we have to do is approximate \\(\mathrm{erfc}\\) (or, more specifically, the term of the Equation 38 inside the square brackets). Luckily, the physics literature has an efficient [approximation](https://rmets.onlinelibrary.wiley.com/doi/full/10.1002/asl.154) (for positive values of \\(x\\)) readily available:

$$ \tag{42} e^{x^2} \mathrm{erfc}(x) \approx \frac{2.911}{(2.911 - 1) \sqrt{\pi x^2} + \sqrt{\pi x^2 + 2.911^2}}. $$

The resulting approximation of the Chapman function has up to 50 times lower relative error, and is acceptable for our use case. The full expression of transmittance features even lower numerical error.

{{< figure src="/img/chapman_erfc_error.png" caption="*Relative error plot of the new approximation of the Chapman function for \\(r = 6600\\).*">}}

For reference, the full numerical approximation of the Chapman function for the upper hemisphere is:

$$ \tag{43} C_{u\\_a}(z, \mathrm{cos}{\theta}) \approx \frac{\mathrm{cos}{\theta}}{2} + \frac{0.761643 (1 + z (2 - \mathrm{cos}^2{\theta}))}{z \mathrm{cos}{\theta} + \sqrt{z (1.47721 + 0.273828 z \mathrm{cos}^2{\theta})}}. $$

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

A small but important note is that we can always use \\( \vert \mathrm{cos}{\theta} \vert \\) to evaluate the upper part of the Chapman function since, if the angle is greater than 90 degrees, the ray direction is reversed, and the cosine is negated.

### Evaluating Optical Depth Using the Chapman Function

A numerical approximation of the Chapman function, in conjunction with the Equation 36, allows us to evaluate optical depth along an arbitrary ray segment.

However, the approximation of the Chapman function contains a branch (upper/lower hemisphere), and using the full formulation may be unnecessarily expensive for many use cases.

For example, an implementation of the [Precomputed Atmospheric Scattering](https://dl.acm.org/citation.cfm?id=2383467) paper requires an ability to evaluate optical depth along the ray, where the ray may hit either the spherical planet, or nothing at all.

To start with, we may want to know whether the ray intersects the planet, or, in other words, whether it points above the horizon.

{{< figure src="/img/spherical_param_2.png">}}

Since a "horizon" ray always intersects the planet at a 90 degree angle, the (obtuse) horizon angle \\( \mathrm{cos}{\theta_h} \\) at the query point can be found using basic trigonometry:

$$ \tag{44} \mathrm{cos}{\theta_h} = -\frac{\mathrm{adjacent}}{\mathrm{hypotenuse}} = -\frac{\sqrt{r^2 - R^2}}{r} = -\sqrt{1 - (R/r)^2}. $$

If the ray points above the horizon, the regular Chapman function gets the job done. And if the ray points below the horizon, it may seem that we need to evaluate the full Chapman function twice (as per Equation 36), once at the starting position \\(\bm{x}\\), where the ray points into the lower hemisphere, and once at the intersection point \\(\bm{y}\\) (using the same ray direction).

However, we can utilize the fact that \\(\bm{\tau}(\bm{x}, \bm{y}) = \bm{\tau}(\bm{y}, \bm{x})\\), and instead consider the ray segment \\(\bm{yx}\\) which starts at the intersection point \\(\bm{y}\\) and has a reversed direction.

Take a look at the updated diagram below:

{{< figure src="/img/spherical_param_3.png">}}

Using basic trigonometry, we can deduce the cosine of the angle at the intersection point

$$ \tag{45} \mathrm{sin}{\gamma} = \frac{\mathrm{opposite}}{\mathrm{hypotenuse}} = \frac{r_0}{R} = \frac{r \mathrm{sin}{\theta}}{R} \qquad \mathrm{cos}{\gamma} = \sqrt{1 - \mathrm{sin}^2{\gamma}} $$

that determines the value of the Chapman function below horizon:

$$ \tag{46} C_b(z, \mathrm{cos}{\theta}, Z, \mathrm{cos}{\gamma}) = C_u(Z, \mathrm{cos}{\gamma}) - C_u(z, \vert \mathrm{cos}{\theta} \vert). $$

Sample code is listed below. While it's possible to use the `RescaledChapmanFunction` in this scenario, the following implementation is slightly more efficient.

```c++
float ComputeCosineOfHorizonAngle(float r, float R)
{
    float sinHoriz = R * rcp(r);
    return -sqrt(saturate(1 - sinHoriz * sinHoriz));
}

// Spectral version.
float3 EvalOptDepthSpherExpMedium(float r, float cosTheta,
                                  float3 seaLvlAtt, float Z,
                                  float R, float rcpR,
                                  float H, float rcpH)
{
    float z = r * rcpH;

    float sinTheta = sqrt(saturate(1 - cosTheta * cosTheta));
    float cosHoriz = ComputeCosineOfHorizonAngle(r, R);

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

    return ch * H * seaLvlAtt;
}
```

If desired, it is possible to reduce execution divergence by utilizing `ChapmanUpperApprox` (with the cosine value of 0) instead of `ChapmanHorizontal`, but I will retain this version for clarity of exposition.

Now we are ready to tackle the most general case of evaluating optical depth between two arbitrary points \\(\bm{x}\\) and \\(\bm{y}\\). It may seem really difficult at first when, in fact, it's very similar to the problem we just solved. We have to consider three distinct possibilities:

1\. \\(\mathrm{cos}{\theta_x} \geq 0 \\), which means that the ray points into the upper hemisphere with respect to the surface normal at the point \\(\bm{x}\\). This also means it points into the upper hemisphere at any point \\(\bm{y}\\) along the ray (it's fairly obvious if you sketch it). Optical depth is given by the Equation 36, which we specialize by replacing \\(C\\) with \\(C_u\\) which is restricted to the upper hemisphere:

$$ \tag{47}
\bm{\tau\_{uu}}(z_x, \mathrm{cos}{\theta_x}, z_y, \mathrm{cos}{\theta_y})
    = \bm{\mu_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_u(z_x, \mathrm{cos}{\theta_x}) - e^{Z - z_y} C_u(z_y, \mathrm{cos}{\theta_y}) \Bigg).
$$

2\. \\(\mathrm{cos}{\theta_x} < 0 \\) and \\(\mathrm{cos}{\theta_y} < 0 \\) occurs e.g. when looking straight down. It's also easy to handle, we just flip the direction (by taking the absolute value of the cosine), replace the segment \\(\bm{xy}\\) with the segment \\(\bm{yx}\\) and fall back to case 1.

3\. \\(\mathrm{cos}{\theta_x} < 0 \\) and \\(\mathrm{cos}{\theta_y} \geq 0 \\). This is the most complicated case, since we have to evaluate the Chapman function three times, twice at \\(\bm{x}\\) with the direction pointing into the lower hemisphere, and once at \\(\bm{y}\\) with the ray pointing in the upper hemisphere:

$$ \tag{48} \begin{aligned}
\bm{\tau\_{ul}}(z_x, \mathrm{cos}{\theta_x}, z_y, \mathrm{cos}{\theta_y})
    &= \bm{\mu_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_l(z_x, \mathrm{cos}{\theta_x}) - e^{Z - z_y} C_u(z_y, \mathrm{cos}{\theta_y}) \Bigg).
\end{aligned} $$

Sample code is listed below.

```c++
float RadAtDist(float r, float cosTheta, float t)
{
    return sqrt(r * r + t * (t + 2 * (r * cosTheta)));
}

float CosAtDist(float r, float cosTheta, float t, float radAtDist)
{
    return (t + r * cosTheta) * rcp(radAtDist);
}

// Spectral version.
float3 EvalOptDepthSpherExpMedium(float r, float cosTheta, float t,
                                  float3 seaLvlAtt, float Z,
                                  float H, float rcpH)
{
    float rX        = r;
    float cosThetaX = cosTheta;
    float rY        = RadAtDist(rX, cosThetaX, t);
    float cosThetaY = CosAtDist(rX, cosThetaX, t, rY);

    // Potentially swap X and Y.
    // Convention: at the point Y, the ray points up.
    cosThetaX = (cosThetaY >= 0) ? cosThetaX : -cosThetaX;
    cosThetaY = abs(cosThetaY);

    float zX  = rX * rcpH;
    float zY  = rY * rcpH;

    float chX = RescaledChapmanFunction(zX, Z, cosThetaX);
    float chY = ChapmanUpperApprox(zY, cosThetaY) * exp(Z - zY); // Rescaling adds 'exp'

    // We may have swapped X and Y.
    float ch = abs(chX - chY);

    return ch * H * seaLvlAtt;
}
```

The code listed above works for all finite inputs. An important special case is when the point \\(\bm{y}\\) is considered to be outside the atmosphere (if `exp(Z - zY) < EPS`, for instance). In that case, `chY = 0` is an adequate approximation.

### Sampling Exponential Media in Spherical Coordinates

In order to be able to sample participating media, we must be able to solve the optical depth equation for distance (Equation 18). Analysis presented in the previous section indicates that there are two cases we must consider: the ray either points into the same hemisphere at both endpoints (Equation 47), or into the opposite ones (Equation 48).

First, let's establish a couple of useful identities. Recalling the Equation 32, we can compute the radial distance from the center \\(\bm{c}\\) to the point \\(\bm{x} + t \bm{v}\\) along the ray using the Pythagorean theorem:

$$ \tag{49}
\mathcal{R}(r, \theta, t)
    = \sqrt{r_0^2 + (t_0 + t)^2}
    = \sqrt{(r \mathrm{sin}{\theta})^2 + (r \mathrm{cos}{\theta} + t)^2}
    = \sqrt{r^2 + t^2 + 2 t r \mathrm{cos}{\theta}}.
$$

The cosine of the ray direction with respect to the surface normal at that point can be computed as follows:

$$ \tag{50}
\mathcal{C}(r, \theta, t)
    = \frac{\mathrm{adjacent}}{\mathrm{hypotenuse}}
    = \frac{t_0 + t}{\mathcal{R}(r, \theta, t)}
    = \frac{r \mathrm{cos}{\theta} + t}{\sqrt{(r \mathrm{sin}{\theta})^2 + (r \mathrm{cos}{\theta} + t)^2}}.
$$

Using the new, compact notation, and after performing some algebraic manipulation, we can reduce the problem to solving the following equation:

$$ \tag{51}
q = C_r \Big( n \mathcal{R}(r, \theta, t), Z, \mathcal{C}(r, \theta, t) \Big),
$$

which means that we need to find the distance \\(t\\) at which the value of the rescaled Chapman function is \\(q\\).

This equation has too many parameters. We can reduce dimensionality by solving for \\(t' = nt\\):

$$ \tag{52}
q = C_r \Big( \sqrt{(z \mathrm{sin}{\theta})^2 + (z \mathrm{cos}{\theta} + t')^2}, Z, \frac{z \mathrm{cos}{\theta} + t'}{\sqrt{(z \mathrm{sin}{\theta})^2 + (z \mathrm{cos}{\theta} + t')^2}} \Big).
$$

After getting rid of \\(n\\), the next step is to group common terms:

$$ \tag{53} \phi = z \mathrm{sin}{\theta} \qquad \psi = z \mathrm{cos}{\theta} + t', $$

$$ \tag{54}
q = C_r \Big( \sqrt{\phi^2+\psi^2}, Z, \frac{\psi}{\sqrt{\phi^2+\psi^2}} \Big). $$

Both \\(\phi\\) and \\(\psi\\) are known to be positive. We must solve for \\(\psi\\). \\(Z\\) could theoretically be removed, but it's here to keep the solution within the sane numerical range.

This appears to be the simplest formulation of the problem, using the fewest number of parameters. I gave it a shot, but, unfortunately, I was unable to analytically solve this equation, and I tried plugging in both the closed and the approximate forms of the Chapman function. Perhaps **you** will have better luck?

While that's an unfortunate development, it's a minor setback. If we can't solve the equation analytically, we can solve it numerically, using the [Newton–Raphson method](https://en.wikipedia.org/wiki/Newton%27s_method), for instance. The derivative of the Chapman function exists, and is not too difficult to compute.

In fact, there is a [better way](http://lib-www.lanl.gov/la-pubs/00367066.pdf), which is even simpler. Recall that Newton's method requires being able to make an initial guess, evaluate the function, and take its derivative. If we solve using the entire optical depth function \\(\tau\\) (Equation 18), we know that its derivative is just the extinction coefficient \\(\sigma_t\\) (Equation 9), and making a good initial guess is easy by simply ignoring curvature of the planet.

This method is very general and works for completely arbitrary continuous density distributions (see the [paper](http://lib-www.lanl.gov/la-pubs/00367066.pdf)).

Sample code is listed below.

```c++
// Single wavelength version.
float SampleSpherExpMedium(float optDepth, float r, float cosTheta,
                           float rcpSeaLvlAtt, float Z, float R, float H, float rcpH)
{
    // This allows us to use (seaLvlAtt = rcpSeaLvlAtt = 1) below.
    optDepth *= rcpSeaLvlAtt;

    float rcpOptDepth = rcp(optDepth); // Must not be 0

    // Make an initial guess.
    float t = SampleRectExpMedium(optDepth, r - R, cosTheta, 1, rcpH);

    float relDiff;

    do // Perform a Newton–Raphson iteration
    {
        float radAtDist = RadAtDist(r, cosTheta, t);

        // Evaluate the function and its (reciprocal) derivative.
        // f(t) = OptDepthAtDist(t) - GivenOptDepth = 0.
        // The sea level attenuation coefficient has been removed by the division.
        float optDepthAtDist = EvalOptDepthSpherExpMedium(r, cosTheta, t, 1, Z, H, rcpH);
        float rcpAttCoeffAtDist = 1 * exp((radAtDist - R) * rcpH);

        // Refine the initial guess.
        // t1 = t0 - f(t0) / f'(t0).
        t = t - (optDepthAtDist - optDepth) * rcpAttCoeffAtDist;

        relDiff = optDepthAtDist * rcpOptDepth - 1;

    } while (abs(relDiff) > EPS); // Until the accuracy goal has been reached

    return t;
}
```

The spectral coefficient cancels out! Amazing!!

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

