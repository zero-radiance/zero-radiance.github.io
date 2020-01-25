---
title: "Sampling Analytic Participating Media"
date: 2019-12-17
hidden: true
draft: true
categories: [ "Graphics", "Math" ]
tags: [
    "Chapman Function",
    "Importance Sampling",
    "Volume Rendering",
    ]
---

Participating media rendering is an important aspect of every modern renderer. When I say participating media, I am not just talking about fog, fire, and smoke - *everything is volumetric*. All matter is composed of [atoms](https://en.wikipedia.org/wiki/Atom) (containing electrons), which can be sparsely (e.g. in a gas) or densely (e.g. in a solid) distributed in space. Whether we consider the particle or the wave nature of [light](https://en.wikipedia.org/wiki/Light), it penetrates all matter (even [metals](http://webhome.phy.duke.edu/~qelectron/group/group_reading_Born_and_Wolf.pdf)) to a certain degree and interacts with its atoms along the way. The nature and the degree of "participation" depend on the material in question.

<!--more-->

In the [radiative transfer](https://archive.org/details/RadiativeTransfer) literature, light-material interaction is usually quantified in terms of absorption (conversion of electromagnetic energy of photons into kinetic energy of atoms, which manifests itself as reduction of light intensity) and scattering (absorption and induced emission of electromagnetic energy on interaction). Therefore, it is common to describe participating media using the collision coefficients of its particles: the *absorption coefficient* \\(\bm{\mu_a}\\) and the *scattering coefficient* \\(\bm{\mu_s}\\). These coefficients give the probability density of the corresponding event per unit distance traveled by a photon, which implies the [SI units](https://en.wikipedia.org/wiki/International_System_of_Units) of measurement are \\(m^{-1}\\).

The [attenuation coefficient](https://en.wikipedia.org/wiki/Attenuation_coefficient) \\(\bm{\mu_t}\\)

$$ \tag{1} \bm{\mu_t} = \bm{\mu_a} + \bm{\mu_s} $$

gives the probability density of absorption or scattering (or, in other words, the collision rate) as a photon travels a unit distance through the medium. All these coefficients are spectral (they depend on the frequency \\(\nu\\), which is simpler to handle than the wavelength \\(\lambda\\) since the latter is [modified at the interface](http://graphics.stanford.edu/papers/veach_thesis/)), and can be represented as vectors (boldface notation). At this point in time, it is not entirely clear (at least to me) how to *correctly* perform volume rendering using tristimulus (RGB) values (which would require some sort of pre-integration using [color matching functions](https://en.wikipedia.org/wiki/CIE_1931_color_space#Color_matching_functions)), so I will focus on pure spectral rendering, which is well-defined.

A more artist-friendly parametrization uses the [single-scattering albedo](https://en.wikipedia.org/wiki/Single-scattering_albedo) \\(\bm{\alpha\_{ss}}\\)

$$ \tag{2} \bm{\alpha\_{ss}} = \frac{\bm{\mu_s}}{\bm{\mu_t}}, $$

which gives the deflection probability (or, in other words, the scattering rate), and the [mean free path](https://en.wikipedia.org/wiki/Mean_free_path) \\(\bm{d}\\)

$$ \tag{3} \bm{d} = \frac{1}{\bm{\mu_t}}, $$

which corresponds to the average collision-free (or free-flight) distance.

Taking a small detour, for metals, the absorption coefficient is directly related to the [extinction coefficient](http://www.sfu.ca/~gchapman/e376/e376l7.pdf) \\(\bm{\kappa}\\), which is the imaginary part of the [complex index of refraction](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\bm{\eta} + i \bm{\kappa}\\):

$$ \tag{4} \bm{\kappa} = \frac{\bm{\lambda}}{4 \pi} \bm{\mu_a}. $$

For this reason, \\(\bm{\eta}\\) is called the [refractive index](https://www.feynmanlectures.caltech.edu/I_31.html) (or IOR), and \\(\bm{\kappa}\\) is sometimes referred to as the [absorption index](http://www.sfu.ca/~gchapman/e376/e376l7.pdf). Note that I am not talking about the IOR of an individual particle (which influences the [microscopic scattering](https://en.wikipedia.org/wiki/Mie_scattering) process), but rather about the macroscopic properties of the medium itself.

The tuple \\(\lbrace \bm{\eta}, \bm{\kappa}, \bm{\mu_s} \rbrace\\) \\(\big(\\)or, alternatively, \\(\lbrace \bm{\eta}, \bm{d}, \bm{\alpha\_{ss}} \rbrace \big) \\) contains sufficient information to describe both the behavior at the surface (boundary) and the (isotropic) multiple-scattering process (known as [subsurface scattering](https://en.wikipedia.org/wiki/Subsurface_scattering)) inside the volume that ultimately gives rise to what we perceive as the surface albedo \\(\bm{\alpha\_{ms}}\\). Note that certain materials (metals, in particular) require modeling of [wave interference](https://en.wikipedia.org/wiki/Wave_interference) to obtain expected reflectance values (so a solution based on geometric optics would yield wrong results).

A surface, then, is just an optical interface signified by a discontinuity of optical properties of the medium (in reality, the [transition at the boundary is continuous](https://www.feynmanlectures.caltech.edu/II_33.html), with a thickness of several atomic layers, but we can ignore this fact at scales relevant to computer graphics).

Sometimes, it is convenient to specify the concentration (density) of the medium, and not its effective optical properties. For example, the attenuation coefficient can be computed using the following formula:

$$ \tag{5} \bm{\mu_t} = \rho \bm{\sigma_t}, $$

where \\(\rho\\) is the [mass density](https://en.wikipedia.org/wiki/Mass_density) (measured in units of \\(kg/m^{3}\\)) and \\(\bm{\sigma_t}\\) is the [mass attenuation coefficient](https://en.wikipedia.org/wiki/Mass_attenuation_coefficient) (in units of \\(m^{2}/kg\\)) - [effective cross section](http://www.sfu.ca/~gchapman/e376/e376l7.pdf) per unit mass. Other coefficients have the same linear relation with density.

But what about the IOR? Often, one assumes that it is independent of density. But, if you consider, for example, water and steam (which has a lower concentration of water molecules), our experience tells us that their refractive properties are not the same.

There are several known relations between density and the IOR. One of them is given by the [Lorentz–Lorenz equation](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation):

$$ \tag{6} \frac{\bm{n}^2 - 1}{\bm{n}^2 + 2} = \frac{4}{3} \pi \frac{\rho}{m} \bm{\alpha_m}, $$

where \\(m\\) is the [molecular mass](https://en.wikipedia.org/wiki/Molecular_mass) (in \\(kg\\)) and \\(\bm{\alpha_m}\\) is the [molecular polarizability](https://en.wikipedia.org/wiki/Electric_susceptibility#Molecular_polarizability) (in \\(m^3\\), watch out for different [conventions](https://en.wikipedia.org/wiki/Electric_susceptibility#Ambiguity_in_the_definition)). Incidentally, this equation presents a way to compute the IOR of a mixture of several substances. The corresponding [Lorentz–Lorenz mixture rule](https://www.sciencedirect.com/science/article/pii/S0021850208001183) is based on four principles of additivity of mole, mass, volume, and molecular polarizability, with the last two assumption being rather context-dependent.

For materials with small mass densities, the molecules are far apart from one another, the molecular interactions are weak, and the IOR is close to 1. Therefore, for matter in a gas state, the following approximation can be made:

$$ \begin{aligned} \tag{7}
    \bm{n}^2 & \approx 1 + 4 \pi \frac{\rho}{m} \bm{\alpha_m} = 1 + 2 \bm{c} \rho, \cr
    \bm{n}   & \approx 1 + 2 \pi \frac{\rho}{m} \bm{\alpha_m} = 1 + \bm{c} \rho,
\end{aligned} $$

where \\(\bm{c}\\) is the [light dispersion coefficient](https://ui.adsabs.harvard.edu/abs/1996CoSka..26...23K/abstract). This equation implies that the [relative brake power](https://www.sciencedirect.com/topics/chemistry/optical-refraction) \\((\bm{n} - 1)\\) has an approximately linear relation with density. Similar relations can be found for [temperature and and pressure](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation) (in fact, all coefficients are highly [temperature-dependent](http://www.sfu.ca/~gchapman/e376/e376l7.pdf)). Also, while the discussion above mostly concerns dielectrics, the formula for metals is [very similar](https://www.feynmanlectures.caltech.edu/II_32.html#mjx-eqn-EqII3238).

Continuous variation of the IOR poses many challenges for path tracing. In piecewise-homogeneous media, paths are composed of straight segments joined at scattering locations. Unfortunately, due to [Fermat's principle](https://en.wikipedia.org/wiki/Fermat%27s_principle), continuously varying IOR forces photons to travel along paths of least time that [continuously refract](http://www.waves.utoronto.ca/prof/svhum/ece422/notes/20a-atmospheric-refr.pdf) towards regions of higher density according to [Snell's law](https://en.wikipedia.org/wiki/Snell%27s_law). This fact alone makes basic actions like visibility testing very complicated. And since the IOR can depend on the frequency, it may cause [dispersion](https://en.wikipedia.org/wiki/Dispersion_(optics)) not only at the interfaces, but also continuously, along the entire path. Finally, one must model losses and gains due to [continuous reflection](https://www.scirp.org/pdf/opj_2013112009301643.pdf), which is mathematically challenging. So it is not too surprising that most renderers ignore this behavior (even though, physically, that doesn't make much sense). For small density gradients and small distances, it is a valid approximation that, on average, gives reasonably accurate results. On the other hand, for certain atmospheric effects, [atmospheric refraction](https://en.wikipedia.org/wiki/Atmospheric_refraction) and reflection make a non-negligible contribution.

For practical reasons, all further discussion assumes that, within volume boundaries, the IOR is invariant with respect to position. If you are interested in continuous refraction, check out the [Refractive Radiative Transfer Equation](https://dl.acm.org/doi/abs/10.1145/2557605) paper.

## Radiative Transfer Equation

Intelligent sampling requires understanding which parts make a large contribution. Therefore, we must briefly discuss the radiative transfer equation (or RTE) used to render scenes with participating media. While the full [derivation](https://archive.org/details/RadiativeTransfer) is outside the scope, we will try to touch the important aspects.

The integral form of the RTE is a recursive line integral. It works by collecting photons along the path, while at the same time modeling energy losses as photons travel from the sources towards the sensor.

One of the primary loss factors is opacity \\(\bm{O}\\), which can be defined as the fraction of photons lost along the path from \\(\bm{y}\\) to \\(\bm{x}\\):

$$ \tag{8} \bm{O}(\bm{x}, \bm{y}) = \frac{\bm{L}(\bm{y}, \bm{\hat{v}\_{xy}}) - \bm{L}(\bm{x}, \bm{\hat{v}\_{xy}})}{\bm{L}(\bm{y}, \bm{\hat{v}\_{xy}})} = 1 - \frac{\bm{L}(\bm{x}, \bm{\hat{v}\_{xy}})}{\bm{L}(\bm{y}, \bm{\hat{v}\_{xy}})}, $$

where \\(\bm{\hat{v}\_{xy}} = (\bm{x} - \bm{y})/ \Vert \bm{x} - \bm{y} \Vert \\) is the normalized view direction. Intuitively, \\(\bm{O}(\bm{x}, \bm{y}) = \bm{O}(\bm{y}, \bm{x})\\).

Its complement is transmittance \\(\bm{T}\\):

$$ \tag{9} \bm{T}(\bm{x}, \bm{y}) = 1 - \bm{O}(\bm{x}, \bm{y}). $$

For a single photon, transmittance gives the probability of a free flight.

Volumetric transmittance is given by the [Beer–Lambert–Bouguer law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law) for [isotropic](http://www.eugenedeon.com/hitchhikers) [uncorrelated](https://cs.dartmouth.edu/~wjarosz/publications/bitterli18framework.html) media in terms of [optical depth](https://en.wikipedia.org/wiki/Optical_depth) (or optical thickness) \\(\bm{\tau}\\):

$$ \tag{10} \bm{\tau}(\bm{x}, \bm{y}) = -\log{\bm{T}(\bm{x}, \bm{y})} = \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) du, $$

where \\(\bm{u}\\) is the point at the distance \\(u\\) along the path. It implies that while transmittance is multiplicative, with values restricted to the unit interval, optical depth is additive and can take on any non-negative value. In other words, transmittance is a [product integral](https://www.wikiwand.com/en/Product_integral):

$$ \tag{11} \bm{T}(\bm{x}, \bm{y}) = e^{- \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) du} = \prod\_{\bm{x}}^{\bm{y}} \Big( 1 - \bm{\mu_t}(\bm{u}) du \Big). $$

Other [integral formulations](https://cs.dartmouth.edu/~wjarosz/publications/georgiev19integral.html) of volumetric transmittance exist.

We model 3 energy sources: volumetric emission \\(\bm{L_e}\\), volumetric in-scattering \\(\bm{L_s}\\), and surface in-scattering \\(\bm{L_g}\\) (which is the standard surface geometry term with a BSDF). The in-scattering term \\(\bm{L_s}\\) is an integral over all directions on the sphere \\(S^2\\):

$$ \tag{12} \bm{L_s}(\bm{x}, \bm{v}) = \int\_{\bm{S}^2} f_p(\bm{x}, \bm{v}, \bm{l}) \bm{L}(\bm{x}, \bm{l}) d\sigma(\bm{l}), $$

where \\(f_p\\) denotes the [phase function](http://www.pbr-book.org/3ed-2018/Volume_Scattering/Phase_Functions.html).

Carefully putting it all together yields the [volume rendering equation](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html):

$$ \begin{aligned} \tag{13}
    \bm{L}(\bm{x}, \bm{v}) =
    \int\_{\bm{x}}^{\bm{y}} \bm{T}(\bm{x}, \bm{u}) \Big[
        & \bm{\mu_a}(\bm{u}) \bm{L_e}(\bm{u}, \bm{v}) \; + \cr
        & \bm{\mu_s}(\bm{u}) \bm{L_s}(\bm{u}, \bm{v})
    \Big] du +
    \bm{T}(\bm{x}, \bm{y}) \bm{L_g}(\bm{y}, \bm{v}),
\end{aligned} $$

where \\(\bm{y}\\) denotes the position of the closest surface along the ray.

We can leave [volumetric emission](https://dl.acm.org/doi/10.1111/cgf.13228) out by setting \\(\bm{L_e} = 0\\):

$$ \tag{14}
    \bm{L}(\bm{x}, \bm{v}) =
    \int\_{\bm{x}}^{\bm{y}} \bm{T}(\bm{x}, \bm{u}) \bm{\mu_s}(\bm{u}) \bm{L_s}(\bm{u}, \bm{v}) du +
    \bm{T}(\bm{x}, \bm{y}) \bm{L_g}(\bm{y}, \bm{v}).
$$

Next, let's split the scattering coefficient into attenuation and albedo:

$$ \tag{15}
    \bm{L}(\bm{x}, \bm{v}) =
    \int\_{\bm{x}}^{\bm{y}} \bm{T}(\bm{x}, \bm{u}) \bm{\mu_t}(\bm{u}) \bm{\alpha\_{ss}}(\bm{u}) \bm{L_s}(\bm{u}, \bm{v}) du +
    \bm{T}(\bm{x}, \bm{y}) \bm{L_g}(\bm{y}, \bm{v}).
$$

We can evaluate this integral using one of the [Monte Carlo](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration.html) methods. The [Monte Carlo estimator](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/The_Monte_Carlo_Estimator.html) of the integral (for a single frequency) takes the following form:

$$ \tag{16} L(\bm{x}, \bm{v})
    \approx \frac{1}{N} \sum\_{i=1}^{N} \frac{\mu_t(\bm{u_i}) T(\bm{x}, \bm{u_i}) \alpha\_{ss}(\bm{u_i}) L_s(\bm{u_i}, \bm{v})}{p( u_i | \lbrace \bm{x}, \bm{v} \rbrace)} + T(\bm{x}, \bm{y}) L_g(\bm{y}, \bm{v}),
$$

where sample locations \\(\bm{u_i}\\) at the distance \\(u_i\\) along the path are distributed according to the [PDF](https://en.wikipedia.org/wiki/Probability_density_function) \\(p\\).

We can [importance sample](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Importance_Sampling.html) the integrand (distribute  samples according to the PDF) in several ways. Ideally, we would like to make the PDF proportional to the [product of all terms](https://cgg.mff.cuni.cz/~jaroslav/papers/2014-zerovar/) of the integrand. However, unless we use [path guiding](https://cgg.mff.cuni.cz/~jirka/path-guiding-in-production/2019/index.htm), that is typically not possible. We will focus on the technique called [free path sampling](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html) that makes the PDF proportional to the analytic product \\(\mu_t T\\) (effectively, by assuming that the rest of the integrand varies slowly; in practice, this may or may not be the case - for example, for regions near light sources, [equiangular sampling](http://library.imageworks.com/pdfs/imageworks-library-importance-sampling-of-area-lights-in-participating-media.pdf) can give vastly superior results).

---

Slightly jumping ahead, let's define the attenuation-transmittance integral as

$$ \tag{12}
      \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) \bm{T_v}(\bm{x}, \bm{u}) du
    = \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) e^{-\bm{\tau}(\bm{x}, \bm{u})} du.
$$

If we use the [fundamental theorem of calculus](https://en.wikipedia.org/wiki/Fundamental_theorem_of_calculus#First_part) to interpret the attenuation coefficient as a derivative

$$ \tag{13} \bm{\mu_t}(\bm{u}) = \frac{\partial \bm{\tau}}{\partial u}, $$

we can use the one of the [exponential identities](https://en.wikipedia.org/wiki/List_of_integrals_of_exponential_functions#Integrals_involving_only_exponential_functions) to simplify the attenuation-transmittance integral:

$$ \tag{14}
    \int\_{\bm{x}}^{\bm{y}} \frac{\partial \bm{\tau}(\bm{x}, \bm{u})}{\partial u} e^{-\bm{\tau}(\bm{x}, \bm{u})} du
    = -e^{-\bm{\tau}(\bm{x}, \bm{u})} \Big\vert\_{\bm{x}}^{\bm{y}}
    = 1 - \bm{T_v}(\bm{x}, \bm{y}).
$$

Most remarkably, optical depth can be evaluated in a forward or backward fashion, and the [result is the same](https://cs.dartmouth.edu/~wjarosz/publications/georgiev19integral.html)!

$$ \tag{15}
    \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) e^{-\bm{\tau}(\bm{x}, \bm{u})} du =
    \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) e^{-\bm{\tau}(\bm{u}, \bm{y})} du.
$$

In order to turn it into a valid PDF, the product must be normalized using the Equation 13:

$$ \tag{19} p(y | \lbrace \bm{x}, \bm{v} \rbrace)
    = \frac{\mu_t(\bm{y}) T(\bm{x}, \bm{y})}{\int\_{\bm{x}}^{\bm{y\_{max}}} \mu_t(\bm{u}) T(\bm{x}, \bm{u}) du}
    = \frac{\mu_t(\bm{y}) T(\bm{x}, \bm{y})}{O(\bm{x}, \bm{\bm{y\_{max}}})}. $$

Substitution of the Equation 19 radically simplifies the form of the estimator (again, for a single frequency):

$$ \tag{20} L(\bm{x}, \bm{v}) \approx O(\bm{x}, \bm{\bm{y\_{max}}}) \frac{1}{N} \sum\_{i=1}^{N} \alpha\_{ss}(\bm{y_i}) L_s(\bm{y_i}, \bm{v}). $$

This equation can be seen as a form of [premultiplied alpha blending](https://graphics.pixar.com/library/Compositing/) (where alpha is opacity), which explains why particle cards can be so convincing. Additionally, it offers yet another way to parametrize the attenuation coefficient - namely, by opacity at distance (which is similar to [transmittance at distance](https://blog.selfshadow.com/publications/s2015-shading-course/burley/s2015_pbs_disney_bsdf_notes.pdf) used by Disney). It is the most RGB rendering friendly parametrization that I am aware of.

Extending the Equation 20 to handle the surface contribution is trivial. If the closest surface along the ray is at the distance \\(y\_{surf} \geq y\_{max}\\), we simply need to add the surface contribution (another integral) attenuated by transmittance (which is one minus opacity):

$$ \tag{21}
    L(\bm{x}, \bm{v}) \approx O(\bm{x}, \bm{\bm{y\_{max}}}) \frac{1}{N} \sum\_{i=1}^{N} \alpha\_{ss}(\bm{y_i}) L_s(\bm{y_i}, \bm{v}) +
    \big( 1 - O(\bm{x}, \bm{\bm{y\_{max}}}) \big) L\_{surf}(\bm{y\_{surf}}, \bm{v}).
$$

In this context, total opacity along the ray serves as the probability of a collision event in the volume, and can be used to randomly pick the type of the sample (surface or volume).

In order to sample the integrand, we must be also able to [invert](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Sampling_Random_Variables.html#TheInversionMethod) the [CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function) \\(P\\):

$$ \tag{22} P(y | \lbrace \bm{x}, \bm{v} \rbrace)
    = \int\_{0}^{y} p(u | \lbrace \bm{x}, \bm{v} \rbrace) du
    = \int\_{\bm{x}}^{\bm{y}} \frac{\mu_t(\bm{u}) T(\bm{x}, \bm{u}) du}{O(\bm{x}, \bm{\bm{y\_{max}}})}
    = \frac{O(\bm{x}, \bm{\bm{y}})}{O(\bm{x}, \bm{\bm{y\_{max}}})},
$$

which is just fractional opacity.

In practice, this means that we need to solve for the distance \\(y\\) given the value of optical depth \\(\tau\\):

$$ \tag{23} \tau(\bm{x}, \bm{y}) = -\mathrm{log} \big( 1 - P(y | \lbrace \bm{x}, \bm{v} \rbrace) O(\bm{x}, \bm{\bm{y\_{max}}}) \big). $$

## Types of Analytic Participating Media

If your background is in real-time rendering, you may have heard of [constant, linear and exponential fog](http://www.terathon.com/lengyel/Lengyel-UnifiedFog.pdf). These names refer to the way density varies in space (typically, with respect to height), and can be used to model effects like height fog and atmospheric scattering. In these scenarios, albedo is usually assumed to be constant.

### Constant Density

A homogeneous medium has uniform density across the entire volume:

$$ \tag{24} \rho_c = b. $$

This formulation makes computing optical depth easy (recall the Equations 5 and 10):

$$ \tag{25} \bm{\tau_c}(\bm{x}, \bm{y})
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{y}} \rho du
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{y}} b du
    = \bm{\sigma_t} b \Vert \bm{y} - \bm{x} \Vert
    = \bm{\sigma_t} b t.
$$

The sampling "recipe" for distance \\(t\\) can be found by inverting the CDF:

$$ \tag{26} t = \frac{\tau_c}{\sigma_t b}, $$

which is consistent with [previous work](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html).

The resulting sampling algorithm is very simple:

1. compute total opacity along the ray;
2. generate a random CDF value;
3. compute optical depth using the Equation 23;
4. compute the distance using the Equation 26.

### Linear Variation of Density with Altitude in Rectangular Coordinates

Without loss of generality, let's assume that density varies with the third coordinate of the position \\(\bm{x}\\), which we interpret as the altitude. This is your typical "linear height fog on flat Earth" case:

$$ \tag{27} \rho\_{lr}(\bm{x}) = k h(\bm{x}) + b = k x_3 + b. $$

This formulation can be reduced to homogeneous media by setting \\(k = 0\\).

What we would like to evaluate the following integral:

$$ \tag{28} \bm{\tau\_{lr}}(\bm{x}, \bm{y})
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{y}} \rho{\big(h(\bm{u}) \big)} du
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{y}} \big(k h(\bm{u}) + b \big) du.
$$

The task can be simplified by making a change of variables and integrating with respect to the altitude \\(h\\) rather than the parametric distance \\(u\\) along the path. Let's start with the simplest case of a straight path, by assuming the IOR value of 1. Graphically, we want to compute the ratio of infinitesimal lengths \\(ds\\) and \\(dh\\) which is, of course, just the secant of the zenith angle.

{{< figure src="/img/dh_ds.png">}}

Mathematically, for an arbitrary function \\(f(h)\\), the change of variables can be expressed using the [chain rule](https://en.wikipedia.org/wiki/Chain_rule):

$$ \tag{29}
    \int\_{\bm{x}}^{\bm{y}} f \big(h(\bm{s}) \big) ds =
    \int\_{h\_{x}}^{h\_{y}} f(h) \frac{ds}{dh} dh =
    \int\_{h\_{x}}^{h\_{y}} f(h) \sec{\theta} dh =
    \int\_{h\_{x}}^{h\_{y}} f(h) \frac{\Vert \bm{y} - \bm{x} \Vert}{h_y - h_x} dh. $$

The general case of curved trajectories requires application of [Snell's law](https://en.wikipedia.org/wiki/Snell%27s_law):

$$ \tag{30}
    n(h_0) \sin{\theta\_0} = n(h_1) \sin{\theta\_1}. $$

We can approach this problem by representing the atmosphere with an infinite number of homogeneous layers of infinitesimal thickness. For each layer, the product of the IOR and the sine is [invariant](http://www.waves.utoronto.ca/prof/svhum/ece422/notes/20a-atmospheric-refr.pdf) along the path, making a curve composed of an infinite number of tiny straight segments.

{{< figure src="/img/dh_du.png">}}

Again, we use the chain rule, this time relating the distance along the curved path \\(du\\) to the height \\(dh\\).

$$ \tag{31}
    \int\_{\bm{x}}^{\bm{y}} f \big(h(\bm{u}) \big) du =
    \int\_{\bm{x}}^{\bm{y}} f(h) \frac{du}{dh} dh =
    \int\_{h\_{x}}^{h\_{y}} f(h) \sec{\theta(h)} dh. $$

The value of the secant can be computed using Snell's law:

$$ \tag{32}
    \int\_{h\_{x}}^{h\_{y}} f(h) \sec{\theta(h)} dh =
    \int\_{h\_{x}}^{h\_{y}} \frac{f(h)}{\sqrt{1 - \sin^2{\theta(h)}}} dh =
    \int\_{h\_{x}}^{h\_{y}} \frac{f(h) n(h)}{\sqrt{n^2(h) - n^2(h_x) \sin^2{\theta_x}}} dh, $$

where \\(\theta_x\\) is the zenith angle at the start of the path.

Combining Equations 28 and 32 gives us the following expression of optical depth:

$$ \tag{33}
    \bm{\tau}(\bm{x}, \bm{y})
    = \bm{\sigma_t} \int\_{h\_{x}}^{h\_{y}} \rho(h) \sec{\theta_h} dh
    = \bm{\sigma_t} \int\_{h\_{x}}^{h\_{y}} \frac{\rho(h) n(h)}{\sqrt{n^2(h) - n^2(h_x) \sin^2{\theta_x}}} dh.
$$

Note that while this expression is quite general and imposes no restrictions on how the density varies, it is only valid in rectangular coordinates.

For our application, we shall use the low density approximation (Equation 7):

$$ \tag{34}
    \bm{\tau}(\bm{x}, \bm{y})
    \approx \bm{\sigma_t} \int\_{h\_{x}}^{h\_{y}} \frac{\rho(h) \big( 1 + c \rho(h) \big)}{\sqrt{\big( 1 + 2 c \rho(h) \big) - \big( 1 + c \rho(h_x) \big)^2 \sin^2{\theta_x}}} dh.
$$

We can further simplify this expression by integrating in terms of linear density (given by the Equation 27):

$$ \tag{35} \begin{aligned}
    \bm{\tau\_{lr}}(\bm{x}, \bm{y})
    &\approx \bm{\sigma_t} \int\_{h\_{x}}^{h\_{y}} \frac{\rho (1 + c \rho)}{\sqrt{(1 + 2 c \rho) - (1 + c \rho_x)^2 \sin^2{\theta_x}}} \frac{dh}{d\rho} d\rho \cr
    &= \frac{\bm{\sigma_t}}{k} \int\_{\rho\_{x}}^{\rho\_{y}} \frac{\rho (1 + c \rho)}{\sqrt{(1 + 2 c \rho) - (1 + c \rho_x)^2 \sin^2{\theta_x}}} d\rho \cr
\end{aligned} $$

---

The expression of optical depth remains simple:

$$ \tag{24}
\bm{\tau\_{lp}}(\bm{x}, \bm{v}, t)
    = \bm{\sigma_t} \int\_{0}^{t} \Big( m \big(x_3 + s v_3) + k \Big) ds
    = \bm{\sigma_t} \Big(m \big(x_3 + \frac{t}{2} v_3 \big) + k \Big) t,
$$

which is the product of the average attenuation coefficient and the length of the interval, as expected.

The sampling "recipe" for distance \\(t\\) is given by the following formula:

$$ \tag{25}
    t = -\frac{k + m x_3}{m v_3}
    \pm \sqrt{\Bigg( \frac{k + m x_3}{m v_3} \Bigg)^2 + \frac{2 \tau\_{lp}}{\sigma_t m v_3}},
$$

where the sign in front of the square root is the sign of \\(v_3\\). Note that homogeneous media \\( \big( m v_3 = 0 \big) \\) require special care.

### Exponential Variation of Density with Altitude in Rectangular Coordinates

We can replace the linear density function with an exponential:

$$ \tag{26} \rho\_{ep}(\bm{x}) = k e^{-x_3 / H}, $$

where \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height), measured in meters. Another way to think about it is as of the reciprocal of the falloff exponent \\(n\\):

$$ \tag{27} \rho\_{ep}(\bm{x}) = k e^{-n x_3}. $$

Setting \\(n = 0\\) results in a homogeneous medium.

After plugging it in into the formula of optical depth, we obtain the following expression:

$$ \tag{28}
\bm{\tau\_{ep}}(\bm{x}, \bm{v}, t)
    = \bm{\sigma_t} \int\_{0}^{t} k e^{-n (x_3 + s v_3)} ds
    = \bm{\sigma_t} k e^{-n x_3} \int\_{0}^{t} e^{-s n v_3} ds
    = \bm{\sigma_t} k t e^{-n x_3} \frac{1 - e^{-t n v_3}}{n v_3}.
$$
Solving for the distance \\(t\\) is straightforward:

$$ \tag{29} t = -\frac{1}{n v_3} \log \left(1 - \frac{\tau\_{ep} n v_3}{\sigma_t k e^{-n x_3}}\right). $$

Note that homogeneous media \\( \big( n v_3 = 0 \big) \\) require special care.

Sample code is listed below.

```c++
spectrum EvalOptDepthRectExpMedium(float height, float cosTheta, float t,
                                   spectrum seaLvlAtt, float rcpH)
{
    // Equation 21.
    spectrum d = seaLvlAtt * t;

    float p = cosTheta * rcpH;

    if (abs(p) > FLT_EPS)
    {
        // Equation 28.
        d *= exp(-height * rcpH) * (1 - exp(-t * p)) * rcp(p);
    }

    return d;
}

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

### Exponential Variation of Density with Altitude in Spherical Coordinates

This is where things get interesting. We would like to model an exponential density distribution on a sphere:

$$ \tag{30} \rho\_{es}(\bm{x}) = k e^{-h(\bm{x}) / H} = k e^{-(\Vert \bm{x} - \bm{c} \Vert - R) / H} = k e^{-n (\Vert \bm{x} - \bm{c} \Vert - R)}, $$

where \\(\bm{c}\\) is the center of the sphere, \\(R\\) is its radius, \\(h\\) is the altitude, and \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height) as before. In this context, \\(k\\) and \\(\bm{\sigma_t} k\\) represent the density and the value of the attenuation coefficient at the sea level, respectively. This formula gives the density of an [isothermal atmosphere](http://www.feynmanlectures.caltech.edu/I_40.html) that does not exhibit [atmospheric refraction](https://en.wikipedia.org/wiki/Atmospheric_refraction), which is not physically plausible.

Before proceeding with the derivation, it is helpful to understand the geometric setting (after all, a picture is worth a thousand words). Personally, I found the article by [Christian Schüler](https://twitter.com/aries_code) in [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) to be immensely helpful, and I encourage you to check it out if you still have questions after reading my explanation below.

#### Geometric Configuration of a Spherical Atmosphere

Our goal is to simplify the problem using its inherent spherical symmetry. Take a look at the diagram below:

{{< figure src="/img/spherical_param.png">}}

We start by recognizing the fact that every ordered pair of position and direction \\(\lbrace \bm{x}, \bm{v} \rbrace\\) can be reduced to a pair of radial distance and zenith angle \\(\lbrace r, \theta \rbrace\\), which means the phase space 2-dimensional.

In order to find the parametric equation of altitude \\(h\\) along the ray, we can use a right triangle with legs \\(r_0\\) and \\(t_0\\) corresponding to the initial conditions:

$$ \tag{31} r_0 = r \sin{\theta}, \qquad t_0 = r \cos{\theta}. $$

This allows us to obtain the following expression of optical depth:

$$ \tag{32} \begin{aligned}
\bm{\tau\_{es}}(r, \theta, t)
    &= \bm{\sigma_t} \int\_{0}^{t} k e^{-n h(s)} ds \cr
    &= \bm{\sigma_t} \int\_{0}^{t} k e^{-n (\sqrt{r_0^2 + (t_0 + s)^2} - R)} ds \cr
    &= \bm{\sigma_t} \int\_{0}^{t} k e^{-n (\sqrt{(r \sin{\theta})^2 + (r \cos{\theta} + s)^2} - R)} ds \cr
    &= \bm{\sigma_t} \int\_{0}^{t} k e^{-n (\sqrt{r^2 + 2 r s \cos{\theta} + \bm{S}^2} - R)} ds \cr
    &= \bm{\sigma_t} \frac{k}{n} e^{-n (r - R)} \int\_{0}^{t} e^{n (r - \sqrt{r^2 + 2 r s \cos{\theta} + \bm{S}^2})} n ds.
\end{aligned} $$

The resulting integral is very complex. As a first step, let's take the nested integral, and extend the upper limit of integration to infinity. With the following change of variables:

$$ \tag{33} u = n s, \qquad z = n r, \qquad Z = n R, $$

we obtain what is known in the physics community as the [Chapman function](https://en.wikipedia.org/wiki/Chapman_function) << bad link! >> (or the obliquity function, or the relative optical air mass) \\(C\\):

$$ \tag{34} C(z, \cos{\theta}) = \int\_{0}^{\infty} e^{z - \sqrt{z^2 + 2 z u \cos{\theta} + u^2}} du. $$

It is convenient to define the rescaled Chapman function \\(C_r\\)

$$ \tag{35} C_r(z, Z, \cos{\theta}) = e^{Z - z} C(z, \cos{\theta}) = \int\_{0}^{\infty} e^{Z - \sqrt{z^2 + 2 z u \cos{\theta} + u^2}} du, $$

which has a better numerical behavior, and further simplifies the expression of optical depth between \\(\bm{x}\\) and \\(\bm{y}\\):

$$ \tag{36}
\bm{\tau\_{es}}(z_x, \cos{\theta_x}, z_y, \cos{\theta_y})
    = \bm{\sigma_t} \frac{k}{n} \Bigg( C_r \Big(z_x, Z, \cos{\theta_x} \Big) - C_r \Big(z_y, Z, \cos{\theta_y} \Big) \Bigg).
$$

What the Equation 36 tells us is that we should evaluate the optical depth integral twice (in the same direction, along the entire ray, from 0 to \\(\infty\\)), at the start and at the end of the interval, and subtract the results to "clip" the ray.

It is interesting to contemplate the physical meaning of optical depth and the Chapman function. Generally speaking, the value of a line integral of density (such as given by \\(\bm{\tau} / \bm{\sigma_t}\\)) corresponds to mass. Therefore, the integral

$$ \tag{37} \int\_{h = (r - R)}^{\infty} k e^{-n s} ds = \frac{k}{n} e^{-n h} $$

gives the mass of an infinitely tall vertical column starting at height \\(h\\). At the ground level, its mass is \\(k/n = kH\\).

Optical depth, then, is a *product* of the mass of the vertical column *and* the value of the obliquity function (which, intuitively, gives the absolute optical air mass along the oblique ray) *times* the mass attenuation coefficient.

#### Examining the Chapman Function

It is always a good idea to examine a function visually, as a graph. Let's do that.

{{< figure src="/img/chapman_ref.png" caption="*Plot of the Chapman function for \\(r = 6600\\).*">}}

Above, I plotted values of the Chapman function (vertical axis) varying with the angle \\(\theta\\) (horizontal axis, in degrees) for different values of the scale height \\(H\\): \\(1\\) (blue), \\(10\\) (orange), \\(20\\) (green), \\(40\\) (red), \\(60\\) (purple), \\(80\\) (brown), \\(100\\) (cyan).
Arguably, the first two are the most important, since they roughly correspond to scale heights of aerosols and air of Earth's atmosphere. However, it is also nice to be able to support larger values to model atmospheres of [other planets](https://en.wikipedia.org/wiki/Scale_height#Planetary_examples).

Being an obliquity function, \\(C(z, 0) = 1\\). The function varies slowly, as long as the angle is far from being horizontal (which suggests an opportunity for a [small-angle approximation](https://en.wikipedia.org/wiki/Small-angle_approximation)).

The Chapman function has an [analytic expression](https://ui.adsabs.harvard.edu/abs/1996CoSka..26...23K/abstract) << approximation! >>

$$ \tag{38} C(z, \cos{\theta}) = \frac{1}{2} \cos{\theta} + \frac{1}{2} \Big(\frac{1}{\sqrt{z}} + 2 \sqrt{z} - \sqrt{z} (\cos{\theta})^2 \Big) \sqrt{\frac{\pi}{2}} \Bigg[ e^{\big( \frac{1}{\sqrt{2}} \sqrt{z} \cos{\theta} \big)^2} \mathrm{erfc}{\Big(\frac{1}{\sqrt{2}} \sqrt{z} \cos{\theta}\Big)} \Bigg], $$

which, unfortunately, is not [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression#Analytic_expression), since it contains the [complementary error function](http://mathworld.wolfram.com/Erfc.html) \\(\mathrm{erfc}\\).

Unfortunately, I was unable to re-derive this expression. I suspect that I am either missing something (accounting for continuous variation of the IOR, for instance), or perhaps this is not a full analytic solution, but rather a truncated series expansion (similar to [this one](https://www.sciencedirect.com/science/article/pii/S0022407300001072)). Also, while the formula should in theory work for angles beyond 90 degrees, it deviates from values of the numerically evaluated integral rather quickly.

For the zenith angle of 90 degrees, the formula reduces to << not exact! >>

$$ \tag{39} C_h(z) = \frac{1}{2} \sqrt{\frac{\pi}{2}} (\frac{1}{\sqrt{z}} + 2 \sqrt{z}). $$

Beyond the 90 degree angle, the following identity can be used:

$$ \tag{40} C_l(z, \cos{\theta}) = 2 e^{z - z \sin{\theta}} C_h(z \sin{\theta}) - C(z, -\cos{\theta}), $$

which means that we must find a position \\(\bm{p}\\) (sometimes called the [periapsis](https://en.wikipedia.org/wiki/Apsis) point, see the diagram in the previous section) along the ray where it is orthogonal to the surface normal, evaluate the horizontal Chapman function there (twice, forwards and backwards, to cover the entire real line), and subtract the value of the Chapman function at the original position with the reversed direction (towards the atmospheric boundary), which isolates the integral to the desired ray segment.

Christian Schüler proposes an approximation of the Chapman function for the upper hemisphere in his [GPU Gems 3](http://www.gameenginegems.net/gemsdb/article.php?id=1133) article:

$$ \tag{41} C_{u\\_cs}(z, \cos{\theta}) \approx \frac{C_h(z)}{(C_h(z) - 1) \cos{\theta} + 1}. $$

It is a pretty good approximation, especially considering the cost.

{{< figure src="/img/chapman_chris.png" caption="*Plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

However, if you care about accuracy, and plot the relative error graph, it paints a slightly less attractive picture.

{{< figure src="/img/chapman_chris_error.png" caption="*Relative error plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

The physics literature has [many approximations](https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1029/2011JD016706) of the Chapman function. Unfortunately, most of them are specific to Earth's atmosphere.

Instead of approximating the entire function (for which we have an analytic expression), we can take a different, simpler approach. All we have to do is approximate \\(\mathrm{erfc}\\) (or, more specifically, the term of the Equation 38 inside the square brackets). Luckily, the physics literature has an efficient [approximation](https://rmets.onlinelibrary.wiley.com/doi/full/10.1002/asl.154) (for positive values of \\(x\\)) readily available:

$$ \tag{42} e^{x^2} \mathrm{erfc}(x) \approx \frac{2.911}{(2.911 - 1) \sqrt{\pi x^2} + \sqrt{\pi x^2 + 2.911^2}}. $$

The resulting approximation of the Chapman function has up to 50 times lower relative error, and is acceptable for our use case. The full expression of transmittance features even lower numerical error.

{{< figure src="/img/chapman_erfc_error.png" caption="*Relative error plot of the new approximation of the Chapman function for \\(r = 6600\\).*">}}

For reference, the full numerical approximation of the Chapman function for the upper hemisphere is:

$$ \tag{43} C_{u\\_a}(z, \cos{\theta}) \approx \frac{\cos{\theta}}{2} + \frac{0.761643 (1 + z (2 - \cos^2{\theta}))}{z \cos{\theta} + \sqrt{z (1.47721 + 0.273828 z \cos^2{\theta})}}. $$

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

A small but important note is that we can always use \\( \vert \cos{\theta} \vert \\) to evaluate the upper part of the Chapman function since, if the angle is greater than 90 degrees, the direction of the ray can be reversed, and the cosine negated.

#### Evaluating Optical Depth Using the Chapman Function

A numerical approximation of the Chapman function, in conjunction with the Equation 36, allows us to evaluate optical depth along an arbitrary ray segment.

However, the approximation of the Chapman function contains a branch (upper/lower hemisphere), and using the full formulation twice may be unnecessarily expensive for many use cases.

For example, an implementation of the [Precomputed Atmospheric Scattering](https://dl.acm.org/citation.cfm?id=2383467) paper requires an ability to evaluate optical depth along the ray, where the ray may hit either the spherical planet, or nothing at all.

To start with, we may want to know whether the ray intersects the planet, or, in other words, whether it points above the horizon.

{{< figure src="/img/spherical_param_2.png">}}

Since a "horizon" ray always intersects the planet at a 90 degree angle, the (obtuse) horizon angle \\( \cos{\theta_h} \\) at the query point can be found using basic trigonometry:

$$ \tag{44} \cos{\theta_h} = -\frac{\mathrm{adjacent}}{\mathrm{hypotenuse}} = -\frac{\sqrt{r^2 - R^2}}{r} = -\sqrt{1 - (R/r)^2}. $$

If the ray points above the horizon, the regular Chapman function gets the job done. And if the ray points below the horizon, it may seem that we need to evaluate the full Chapman function twice (as per Equation 36), once at the starting position \\(\bm{x}\\), where the ray points into the lower hemisphere, and once at the intersection point \\(\bm{y}\\) (using the same ray direction).

However, we can utilize the fact that \\(\bm{\tau}(\bm{x}, \bm{y}) = \bm{\tau}(\bm{y}, \bm{x})\\), and instead consider the ray segment \\(\bm{yx}\\) which starts at the intersection point \\(\bm{y}\\) and has a reversed direction.

Take a look at the updated diagram below:

{{< figure src="/img/spherical_param_3.png">}}

Using basic trigonometry, we can deduce the cosine of the angle at the intersection point

$$ \tag{45} \sin{\gamma} = \frac{\mathrm{opposite}}{\mathrm{hypotenuse}} = \frac{r_0}{R} = \frac{r \sin{\theta}}{R} \qquad \cos{\gamma} = \sqrt{1 - \sin^2{\gamma}} $$

that determines the value of the Chapman function below horizon:

$$ \tag{46} C_b(z, \cos{\theta}, Z, \cos{\gamma}) = C_u(Z, \cos{\gamma}) - C_u(z, \vert \cos{\theta} \vert). $$

Sample code is listed below. While it is possible to use the `RescaledChapmanFunction` in this scenario, the following implementation is slightly more efficient.

```c++
float ComputeCosineOfHorizonAngle(float r, float R)
{
    float sinHoriz = R * rcp(r);
    return -sqrt(saturate(1 - sinHoriz * sinHoriz));
}

// This variant of the function assumes that the ray goes to infinity.
spectrum EvalOptDepthSpherExpMedium(float r, float cosTheta,
                                    spectrum seaLvlAtt, float Z,
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

If desired, it is possible to reduce execution divergence by utilizing `ChapmanUpperApprox` (with the cosine value of 0) instead of `ChapmanHorizontal`, but I will retain this version for clarity.

Now we are ready to tackle the most general case of evaluating optical depth between two arbitrary points \\(\bm{x}\\) and \\(\bm{y}\\). It may seem really complicated at first when, in fact, it is very similar to the problem we just solved. We have to consider three distinct possibilities:

1\. \\(\cos{\theta_x} \geq 0 \\), which means that the ray points into the upper hemisphere with respect to the surface normal at the point \\(\bm{x}\\). This also means it points into the upper hemisphere at any point \\(\bm{y}\\) along the ray (it is fairly obvious if you sketch it). Optical depth is given by the Equation 36, which we specialize by replacing \\(C\\) with \\(C_u\\) which is restricted to the upper hemisphere:

$$ \tag{47}
\bm{\tau\_{uu}}(z_x, \cos{\theta_x}, z_y, \cos{\theta_y})
    = \bm{\sigma_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_u(z_x, \cos{\theta_x}) - e^{Z - z_y} C_u(z_y, \cos{\theta_y}) \Bigg).
$$

2\. \\(\cos{\theta_x} < 0 \\) and \\(\cos{\theta_y} < 0 \\) occurs e.g. when looking straight down. It is also easy to handle, we just flip the direction of the ray (by taking the absolute value of the cosine), replace the segment \\(\bm{xy}\\) with the segment \\(\bm{yx}\\) and fall back to case 1.

3\. \\(\cos{\theta_x} < 0 \\) and \\(\cos{\theta_y} \geq 0 \\). This is the most complicated case, since we have to evaluate the Chapman function three times, twice at \\(\bm{x}\\) and once at \\(\bm{y}\\):

$$ \tag{48} \begin{aligned}
\bm{\tau\_{ul}}(z_x, \cos{\theta_x}, z_y, \cos{\theta_y})
    &= \bm{\sigma_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_l(z_x, \cos{\theta_x}) - e^{Z - z_y} C_u(z_y, \cos{\theta_y}) \Bigg).
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

// This variant of the function takes the distance 't' along the ray into account.
spectrum EvalOptDepthSpherExpMedium(float r, float cosTheta, float t,
                                    spectrum seaLvlAtt, float Z,
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

Note that using this function (rather than calling `EvalOptDepthSpherExpMedium` twice and subtracting the results) is beneficial not only for performance, but also for correctness: it avoids numerical instability near the horizon where rays are prone to alternate between the above/below states, which could cause subtraction to result in negative optical depth values. For performance, it may be also worth considering a special case is when the point \\(\bm{y}\\) is considered to be outside the atmosphere (if `exp(Z - zY) < EPS`, for instance). In that case, `chY = 0` is an adequate approximation.

#### Sampling Exponential Media in Spherical Coordinates

In order to sample participating media, we must be able to solve the optical depth equation for distance. Analysis presented in the previous section indicates that we must consider two cases: the ray pointing into the same hemisphere at both endpoints (Equation 47), and into opposite ones (Equation 48).

First, let's establish a couple of useful identities. Recalling the Equation 32, we can compute the radial distance from the center \\(\bm{c}\\) to the point \\(\bm{x} + t \bm{v}\\) along the ray using the Pythagorean theorem:

$$ \tag{49}
\mathcal{R}(r, \theta, t)
    = \sqrt{r_0^2 + (t_0 + t)^2}
    = \sqrt{(r \sin{\theta})^2 + (r \cos{\theta} + t)^2}
    = \sqrt{r^2 + t^2 + 2 t r \cos{\theta}}.
$$

The cosine of the ray direction with respect to the surface normal at that point can be computed similarly:

$$ \tag{50}
\mathcal{C}(r, \theta, t)
    = \frac{\mathrm{adjacent}}{\mathrm{hypotenuse}}
    = \frac{t_0 + t}{\mathcal{R}(r, \theta, t)}
    = \frac{r \cos{\theta} + t}{\sqrt{(r \sin{\theta})^2 + (r \cos{\theta} + t)^2}}.
$$

Using the new, compact notation, and after performing a few algebraic manipulations, we can reduce the CDF inversion problem to solving the following equation:

$$ \tag{51}
q = C_r \Big( n \mathcal{R}(r, \theta, t), Z, \mathcal{C}(r, \theta, t) \Big),
$$

which means that we need to find the distance \\(t\\) at which the value of the rescaled Chapman function is \\(q\\).

This equation has too many parameters. We can reduce dimensionality by solving for \\(t' = nt\\):

$$ \tag{52}
q = C_r \Big( \sqrt{(z \sin{\theta})^2 + (z \cos{\theta} + t')^2}, Z, \frac{z \cos{\theta} + t'}{\sqrt{(z \sin{\theta})^2 + (z \cos{\theta} + t')^2}} \Big).
$$

After getting rid of \\(n\\), the next step is to group common terms:

$$ \tag{53} \phi = z \sin{\theta} \qquad \psi = z \cos{\theta} + t', $$

$$ \tag{54}
q = C_r \Bigg( \sqrt{\phi^2+\psi^2}, Z, \frac{\psi}{\sqrt{\phi^2+\psi^2}} \Bigg). $$

Both \\(\phi\\) and \\(\psi\\) are known to be positive. We must solve for \\(\psi\\). \\(Z\\) could theoretically be removed, but it is here to keep the solution within the sane numerical range.

This appears to be the simplest formulation of the problem, using the fewest number of parameters. I gave it a shot but, unfortunately, I was unable to analytically solve this equation, and I tried plugging in both the closed and the approximate forms of the Chapman function. Perhaps **you** will have better luck?

While that's an unfortunate development, it is a minor setback. If we can't solve the equation analytically, we can solve it numerically, using the [Newton–Raphson method](https://en.wikipedia.org/wiki/Newton%27s_method), for instance. The derivative of the Chapman function exists, and is not too difficult to compute.

In fact, there is a [better way](http://lib-www.lanl.gov/la-pubs/00367066.pdf), which is even simpler. Recall that Newton's method requires being able to make an initial guess, evaluate the function, and take its derivative. If we solve for the entire optical depth formulation (Equation 17), we know that its derivative is just the attenuation coefficient \\(\mu_t\\) (Equation 9), and making a good initial guess is easy by simply ignoring curvature of the planet.

This method is very general and works for arbitrary continuous density distributions (see the [paper](http://lib-www.lanl.gov/la-pubs/00367066.pdf) for details).

Sample code is listed below.

```c++
float SampleSpherExpMedium(float optDepth, float r, float cosTheta,
                           float R, float H, float rcpH,
                           float seaLvlAtt, float rcpSeaLvlAtt,
                           float maxOptDepth, float maxDist)
    {
        const float rcpOptDepth = rcp(optDepth);

        // Make an initial guess.
    #if 0
        // Homogeneous assumption.
        float t = optDepth * rcp(maxOptDepth);
    #else
        // Exponential assumption.
        float t = SampleRectExpMedium(optDepth, r - R, cosTheta, rcpSeaLvlAtt, rcpH);
    #endif

        uint  numIter = 0;
        float absDiff = optDepth, relDiff = 1;
        do // Perform a Newton–Raphson iteration.
        {
            float radAtDist = RadAtDist(r, cosTheta, t);
            float cosAtDist = CosAtDist(r, cosTheta, t, radAtDist);
            // Evaluate the function and its derivatives:
            // f  (t) = OptDepthAtDist(t) - GivenOptDepth = 0,
            // f' (t) = AttCoefAtDist(t),
            // f''(t) = AttCoefAtDist'(t) = -AttCoefAtDist(t) * CosAtDist(t) / H.
            float optDepthAtDist = EvalOptDepthSpherExpMedium(r, cosTheta, t, seaLvlAtt,
                                                              R * rcpH, H, rcpH);
            float attAtDist      = seaLvlAtt * exp((R - radAtDist) * rcpH);
            float attAtDistDeriv = -attAtDist * cosAtDist * rcpH;

            float   f = optDepthAtDist - optDepth;
            float  df = attAtDist;
            float ddf = attAtDistDeriv;

            // Be careful not to divide by 0 below.
        #if 0
            // https://en.wikipedia.org/wiki/Newton%27s_method
            float dt = f * rcp(df);
        #else
            // https://en.wikipedia.org/wiki/Halley%27s_method
            float dt = (f * df) * rcp(df * df - 0.5 * f * ddf);
        #endif

            // Refine the initial guess.
            t = clamp(t - dt, 0, maxDist); // Basic overshoot handling

            absDiff = abs(optDepthAtDist - optDepth);
            relDiff = abs(optDepthAtDist * rcpOptDepth - 1);

            numIter++;

            // Stop when the accuracy goal has been reached.
            // Note that this uses the accuracy of the old value of 't'.
            // The new value of 't' we just computed is even more accurate.
        } while ((absDiff > EPS_ABS) && (relDiff > EPS_REL) && (numIter < NUM_ITER));

        return t;
    }
```

The function given above is somewhat simplified. You must make sure to not divide by 0, that the attenuation coefficient at the sampled location is not a denormalized floating point number (that may be flushed to 0), you must also be able to handle overshoot, and so on.

The extension for multiple overlapping volumes (with arbitrary distributions) is straightforward. Since it is not generally possible to represent a combination of several volumes by a single exponential volume, we can make an initial guess by assuming that the combined volume is homogeneous along the ray. The rest of the algorithm remains virtually unchanged.

Since optical depth is a smooth monotonically increasing function of distance, this numerical procedure will converge very quickly (typically, after a couple of iterations). If desired, the cost can be fixed by using an iteration counter to terminate the loop, potentially trading accuracy for consistent performance.

It is worth noting that since the code internally uses a numerical approximation of the function, it is not always possible to reach an arbitrary accuracy goal. Using `FLT_EPSILON` results in a high degree of accuracy at the cost of a large number of iterations (typically, 1-10), while 1-2 iterations are sufficient to stay below the relative error level of 0.001.

In fact, curvature of the planet can be ignored for moderate distances, making the rectangular function a relatively efficient and accurate approximation. Can we exploit this idea for arbitrary distances?

Let's say that we are not interested in brute force path tracing (which would require accurate numerical inversion as discussed above). Instead, we are trying to gather in-scattered radiance along the ray using the Equation 18, where \\(\bm{L_s}\\) is known (which limits us to single and precomputed multiple scattering).

If we sort our random CDF values in ascending order, and make sure that the sampling rate is sufficiently high, we can build an incremental sampling algorithm which effectively models piecewise-flat (or polygonal) planet. We will refer to it as *incremental importance sampling*.

It is a direct replacement for randomized ray marching, and shares the traits of being biased, but consistent. Unlike ray marching, the algorithm is intelligent, and adapts to the properties of the participating medium, which makes it more robust.

Let's try to understand how it works in more detail.

For the first sample along the ray, we must solve

$$ \tag{55} -\mathrm{log} \big( 1 - P(t_1 | \lbrace \bm{x}, \bm{v} \rbrace) O(\bm{x}, \bm{v}, t\_{max}) \big) = \tau(\bm{x}, \bm{v}, t_1) = \tau_1. $$

Basically, we must determine the (approximate) distance \\(t_1\\) to the first sample along the ray given the corresponding optical depth \\(\tau_1\\). To solve this equation, we substitute the analytic rectangular function of optical depth (Equation 29), which is a valid approximation for short distances. Total opacity along the entire ray can be computed accurately using our numerical approximation (Equation 43).

The second sample forms the next edge of the polygon (\\(\tau_2\\) is given):

$$ \tag{56} \tau(\bm{x}, \bm{v}, t_1) + \tau(\bm{x} + t_1 \bm{v}, \bm{v}, t_2 - t_1) = \tau_2. $$

We solve for the relative distance given relative optical depth (w.r.t. the previous sample):

$$ \tag{57} \tau(\bm{x} + t_1 \bm{v}, \bm{v}, \Delta t_2) = \Delta \tau_2. $$

And so on. Code that implements incremental importance sampling can be found below.

```c++
// Uniforms.
float    R, Z, H, rcpH, seaLvlAtt, rcpSeaLvlAtt;
float3   C;
spectrum ssAlbedo;

spectrum IncrementalImportanceSampling(float3 X, float3 V, float maxDist, uint numSamples)
{
    // Compute the initial parameters.
    float3 P        = X;
    float  r        = distance(P, C);
    float  cosTheta = dot(P - C, V) * rcp(r);

    // This snippet only supports monochromatic attenuation coefficients.
    float maxOptDepth = EvalOptDepthSpherExpMedium(r, cosTheta, t, seaLvlAtt, Z, H, rcpH);
    float maxOpacity  = OpacityFromOpticalDepth(maxOptDepth);

    spectrum radiance = 0;
    float    optDepth = 0;
    float    t        = 0;

    for (uint i = 0; i < numSamples; i++)
    {
        // Samples must be arranged in the ascending order,
        // s.t. for any i, sample[i] < sample[i + 1].
        float cdf = GetOrderedUnitIntervalRandomSample(i, numSamples);

        // Convert to absolute optical depth (Equation 17).
        float absOptDepth = -log(1 - cdf * maxOpacity);

        // Convert to relative optical depth.
        float relOptDepth = absOptDepth - optDepth;

        // Solve for the relative distance (Equation 57).
        float dt = SampleRectExpMedium(relOptDepth, r - R, cosTheta, rcpSeaLvlAtt, rcpH);

        // Update the state for the next iteration.
        t        = min(t + dt, maxDist);
        optDepth = absOptDepth; // += relOptDepth
        P        = X + t * V;
        r        = RadAtDist(X, V, t);
        cosTheta = CosAtDist(X, V, t, r)

        // Equation 18 (inner sum).
        radiance += ComputeInScatteredRadiance(P, V);
    }

    // Equation 18 (outer terms).
    radiance *= ssAlbedo * maxOpacity * rcp(numSamples);

    return radiance;
}
```

## Conclusion

This article has presented several methods for sampling common types of analytic participating media. They are particularly useful for modeling low-frequency variations of density. While planetary atmospheres cannot be sampled analytically, the proposed numerical approach works well in practice. I look forward to faster and simpler methods which will be undoubtedly discovered by the rendering community.

## What's Next?

The second part of the blog post (published separately) will discuss handling of spectrally varying participating media. It is an exciting (but challenging) topic, and I look forward to sharing my findings with you.

## Acknowledgments

I would like to thank Julian Fong and Sébastien Hillaire for discussion and offering thoughtful comments.

<!---

## Handling Spectral Coefficients

While using monochromatic attenuation is acceptable for certain use cases (such as modeling aerosols and fog), generally speaking, we would like to support spectrally-varying coefficients. This means that given a fixed distance, light is going to be attenuated to a different degree depending on the wavelength. This poses a problem for importance sampling, which would have to convert opacity (now a vector) into distance (a scalar).

The simplest solution is to trace one path per wavelength. For instance, we could either densely sample the entire [visible spectrum](https://en.wikipedia.org/wiki/Visible_spectrum) (e.g. 380 to 780 nm with a 10 nm step size resulting in 40 paths), or pick several wavelengths stochastically (distributed either uniformly, or according to the [luminous efficiency function](https://en.wikipedia.org/wiki/Luminosity_function), or opacity spectrum, or the product of both). This brute force approach increases the rendering cost by a factor of \\(n\\) (where \\(n\\) denotes the number of wavelength samples), but it provides a good reference point for comparison.

Another approach suggested during the [Production Volume Rendering](https://graphics.pixar.com/library/ProductionVolumeRendering/paper.pdf) course is to always construct paths using the average attenuation coefficient across the visible spectrum. This solution is rather attractive from the performance point of view, since you only end up tracing a single path. The downside is that it assumes that the resulting radiance distribution is close to monochromatic. This may not be the case for subsurface scattering in skin, for instance, in which case red wavelengths scatter much farther than the rest (and the spectral distribution is rather compact), and using this technique may result in an excessive amount of color noise. On the other hand, using the maximum value of the attenuation coefficient across the spectrum assumes that the distribution is symmetric around the peak, which may not be the case. Intuitively, using some kind of weighted average wavelength probably makes the most sense.

{{< figure src="/img/skew.png">}}

Let's start with understanding the problem we are trying to solve.

The tristimulus values \\(X\\), \\(Y\\) and \\(Z\\) of a pixel centered at the point \\(\bm{x}\\) on the camera sensor can be computed as an integral of incoming spectral radiance \\(L\_{\lambda}\\) over the visible spectrum \\(\Lambda\\), the pixel area \\(A\\) and the hemisphere of directions \\(\Omega\\) weighted by the [normalized color matching function](https://en.wikipedia.org/wiki/CIE_1931_color_space#Color_matching_functions) \\(\bar{c}\\) \\(\big( \bar{x}\\) for \\(X\\), \\(\bar{y}\\) for \\(Y\\), \\(\bar{z}\\) for \\(Z \big)\\) and the sensor response ([pixel filter](https://ieeexplore.ieee.org/document/4061554/)) \\(W\\):

$$ \tag{58} I = C(\bm{x}) = \int\_{\Lambda} \bar{c}(\lambda) \int\_{A} \int\_{\Omega} W(\bm{r}, \bm{v}, \lambda) L\_{\lambda}(\bm{x} + \bm{r}, \bm{v}, \lambda) d\bm{v} dA(\bm{r}) d\lambda. $$

Recalling that \\(L\_{\lambda}\\) is itself a nested integral, we can generalize our problem using the [path integral formulation](http://graphics.stanford.edu/papers/veach_thesis/):

$$ \tag{59} I = \int\_{\Lambda} \int\_{\mathrm{P}} f(\rho, \lambda) d\sigma(\rho) d\lambda, $$

where \\(\mathrm{P}\\) is the path space (a set of paths), \\(\rho\\) is a path (an ordered set of vertices), \\(\sigma(\rho)\\) is its [measure](https://en.wikipedia.org/wiki/Measure_\(mathematics\)), and \\(f\\) is the measurement contribution function. Clearly, this formulation has some redundancy - we define the domain of integration as a [Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) of all paths and all wavelengths when, in fact, certain paths are perfectly valid for many wavelengths. While their *contribution* is likely to be different, path *geometry* remains the same.

The Monte Carlo formulation \\((I = E[F])\\) of the brute force single wavelength solution takes the following form:

$$ \tag{60} F =
    \frac{1}{m} \sum\_{j=1}^{m} \frac{f(\rho_j, \lambda_j)}{p(\rho_j, \lambda_j)} =
    \frac{1}{m} \sum\_{j=1}^{m} \frac{f(\rho_j, \lambda_j)}{p(\rho_j | \lambda_j) p(\lambda_j)},
$$

where \\(\rho_j\\) is constructed using \\(\lambda_j\\), and its contribution \\(f\\) is evaluated using \\(\lambda_j\\).

Our goal is to sample a path once, and use it to evaluate the contribution of an entire set of wavelengths. The [Monte Carlo Methods for Physically Based Volume Rendering](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte-sig.html) course offers several compelling solutions.

### Spectral Multiple Importance Sampling

One way to achieve this is to use [spectral multiple importance sampling](https://jo.dreggn.org/home/2014_herowavelength.pdf).

What you see below is my interpretation. I encourage the reader to compare it to the [original paper](https://jo.dreggn.org/home/2014_herowavelength.pdf).

We start by defining the set of wavelengths \\(\Lambda_j\\) of size \\(n_j\\) \\( (\forall k, \lambda_j^k \in \Lambda_j) \\). These wavelengths can all be importance sampled or, alternatively, all but the first one can be distributed in a stratified manner (note that the authors of the paper insist that, theoretically, they *must be QMC stratified* rather than randomly sampled to form a discrete rather than a continuous distribution).

Next, we pick one wavelength to guide our path sampling decisions - the authors refer to it as the *hero wavelength*. This wavelength is picked uniformly from the set. Therefore, the probability density of sampling (on average) the path \\(\rho_j\\) using a randomly chosen \\(\lambda_j \in \Lambda_j\\) is taken as the average across the entire set (e.i. we [marginalize](https://en.wikipedia.org/wiki/Marginal_distribution#Marginal_probability_mass_and_density_functions) the discrete PDF). In effect, we define another valid PDF that is not unlike Veach's *combined sample density*:

$$ \tag{61} p(\rho_j, \lambda_j) =
    \frac{1}{n_j} p(\rho_j) =
    \frac{1}{n_j} \sum\_{k=1}^{n_j} p_k(\rho_j, \lambda_j^k) =
    \frac{1}{n_j} \sum\_{k=1}^{n_j} p(\rho_j | \lambda_j^k) p(\lambda_j^k).
$$

Then, we simply evaluate the Monte Carlo estimator using \\(m\\) paths:

$$ \tag{62}
    F = \frac{1}{m} \sum\_{j=1}^{m} \frac{1}{n_j} \sum\_{i=1}^{n_j} \frac{f(\rho_j, \lambda_j^i)}{p(\rho_j, \lambda_j)}
      = \frac{1}{m} \sum\_{j=1}^{m} \sum\_{i=1}^{n_j} \frac{f(\rho_j, \lambda_j^i)}{\sum\_{k=1}^{n_j} p_k(\rho_j, \lambda_j^k)}.
 $$

Or, to simplify the notation, for \\(x \in X\\),

$$ \tag{63} \begin{aligned}
    F = \frac{1}{m} \sum\_{j=1}^{m} \sum\_{i=1}^{n_j} \frac{f(x\_{ji})}{\sum\_{k=1}^{n_j} p_k(x\_{jk})}.
 \end{aligned} $$

If we fix the set size \\( (\forall j, n_j = n) \\), we can change the order of summation to obtain a formulation which corresponds to a multi-sample estimator with \\(n\\) techniques (and \\(m\\) samples per technique) combined using the [balance heuristic](http://graphics.stanford.edu/papers/veach_thesis/):

$$ \tag{64} \begin{aligned}
    F &= \frac{1}{m} \sum\_{j=1}^{m} \sum\_{i=1}^{n} \frac{f(\rho_j, \lambda_j^i)}{\sum\_{k=1}^{n} p_k(\rho_j, \lambda_j^k)} \cr
    &= \frac{1}{m} \sum\_{i=1}^{n} \sum\_{j=1}^{m} \frac{f(\rho_j, \lambda_j^i)}{\sum\_{k=1}^{n} p_k(\rho_j, \lambda_j^k)} \cr
    &= \frac{1}{m} \sum\_{i=1}^{n} \sum\_{j=1}^{m} f(\rho_j, \lambda_j^i) \frac{w_i(\rho_j, \lambda_j^i)}{p_i(\rho_j, \lambda_j^i)},
\end{aligned} $$

where the balance heuristic weight \\(w\\) is defined as

$$ \tag{65}
    w_i(\rho_j, \lambda_j^i)
    = \frac{p_i(\rho_j, \lambda_j^i)}{\sum\_{k=1}^{n} p_k(\rho_j, \lambda_j^k)},
$$

from which it follows that

$$ \tag{66}
    \sum\_{i=1}^{n} w(\rho_i, \lambda_j^i) = 1.
$$

This formulation makes it easy to extend the method to support additional techniques for increased robustness.

In the end, we divided some averages by some other averages. Do we get the correct result on average? Using the formalism introduced in the Equation 63,

$$ \tag{67} \begin{aligned}
    E[F] &= \int\_{X} F(x) p(x) d \sigma(x) \cr
         &= \frac{1}{m} \sum\_{j=1}^{m} \sum\_{i=1}^{n} \int\_{X} \frac{f(x)}{\sum\_{k=1}^{n} p_k(x)} p_i(x) d \sigma(x) \cr
         &= \frac{1}{m} \sum\_{j=1}^{m} \sum\_{i=1}^{n} \int\_{X} f(x) \frac{p_i(x)}{\sum\_{k=1}^{n} p_k(x)} d \sigma(x) \cr
         &= \frac{1}{m} \sum\_{j=1}^{m} \int\_{X} f(x) \sum\_{i=1}^{n} w_i(x) d \sigma(x) \cr
         &= \frac{1}{m} \sum\_{j=1}^{m} \int\_{X} f(x) d \sigma(x) = I,
 \end{aligned} $$

so it appears that everything works out nicely.

A high-level implementation of the algorithm is listed below.

```c++
float3 SpectralMIS(float3 X, float3 V, uint numWavelengths, uint numPaths)
{
    float3 color = 0;

    const uint n = numWavelengths;
    const uint m = numPaths;

    for (uint j = 0; j < m; j++)
    {
        // spectrum is a data structure with 'numWavelengths' items.
        spectrum waves, wavePdfs;

        for (uint i = 0; i < n; i++)
        {
            // Must take eye sensitivity and volume opacity into account.
            // These details have been omitted for readability.
            SampleWavelength(i, j, waves[i], wavePdfs[i]);
        }

        float heroWave = SelectUniformly(waves, n);

        // Trace a single path for the chosen wavelength.
        path  heroPath = SamplePath(X, V, heroWave);

        float3 meanContribution = 0;
        float  meanPdf          = 0;

        for (uint i = 0; i < n; i++)
        {
            // Evaluate the path for a single wavelength.
            float pathContribution, pathPdf;
            EvaluatePath(heroPath, waves[i], pathContribution, pathPdf);

            float3 normCMF = EvaluateNormalizedColorMatchingFunc(waves[i]);

            meanContribution += pathContribution * normCMF;
            meanPdf          += pathPdf * wavePdfs[i];
        }

        color += meanContribution * rcp(meanPdf);
    }

    color *= rcp(numPaths);

    return color;
}
```

One of the concerns with the implementation outlined above is the necessity to store the geometry of the entire path. A performance-oriented implementation of unidirectional path tracing can avoid storing the path by accumulating its contribution for several wavelengths during the path tracing step.

### Spectral Tracking

[Spectral tracking](https://dl.acm.org/citation.cfm?id=3073665) presents an alternative way to accelerate spectral rendering. It uses a radically different approach by incorporating [null collisions](https://hal.archives-ouvertes.fr/hal-01688110/) into the radiative transfer equation.

The basic idea of the null-collision integral is add another type of collision event which has no effect on light transport. While it may seem pointless at first glance, it is a very useful mathematical trick that allows analytic sampling of heterogeneous media (by padding it with transparent forward-scattering particles) either in space or across the spectral domain (or both).

The framework introduces two new types of collision coefficients: the null coefficient \\(\bm{\mu_n}\\) and the majorant \\(\bm{\bar{\mu}}\\) that satisfy

$$ \tag{68} \bm{\mu_a} + \bm{\mu_s} + \bm{\mu_n} = \bm{\bar{\mu}}. $$

Note that while \\(\bm{\mu_n}\\) does not have to be positive, it is usually a [good idea](https://hal.archives-ouvertes.fr/hal-01688110/) in order to keep variance low. For our use case, we compute \\(\bar{\mu}\\) by taking the [maximum of the absolute value](http://mathworld.wolfram.com/L-Infinity-Norm.html) of the attenuation coefficient across the spectral domain:

$$ \tag{69} \bar{\mu} = ||\mu_t(\lambda)||\_{\infty}. $$

Note that it has to be a scalar since it will be used for importance sampling.

Both the derivation and the proof of correctness of the null-scattering integral are rather long and complicated, so I will only present the [final result](https://dl.acm.org/citation.cfm?id=3073665), which is

$$ \tag{70} \bm{L}(\bm{x}, \bm{v})
    = \int\_{0}^{t\_{max}} \bar{\mu}(\bm{x}, \bm{v}, s) \bar{T}(\bm{x}, \bm{v}, s) \bm{L_i}(\bm{x} + s \bm{v}, \bm{v}) ds,
$$

where \\(\bar{T}\\) is transmittance evaluated using the majorant (rather than attenuation) coefficient. Note that the majorant coefficient doesn't actually have to be a constant - it can be any analytic function (e.g. varying with height) serving as the upper bound for the attenuation coefficient.

The incoming radiance term \\(\bm{L_i}\\) is defined as

$$ \tag{71} \begin{aligned}
    \bm{L_i}(\bm{x}, \bm{v})
    &= P_a(\bm{x}, \bm{v}, s) \bm{w_a}(\bm{x}, \bm{v}, s) \bm{L_e}(\bm{x}, \bm{v}) \cr
    &+ P_s(\bm{x}, \bm{v}, s) \bm{w_s}(\bm{x}, \bm{v}, s) \bm{L_s}(\bm{x}, \bm{v}) \cr
    &+ P_n(\bm{x}, \bm{v}, s) \bm{w_n}(\bm{x}, \bm{v}, s) \bm{L}(\bm{x}, \bm{v})
\end{aligned}
$$

where \\(\bm{L_e}\\) is emission and \\(\bm{L_s}\\) is defined in the same way as in the Equation 13.

Each individual event has a corresponding weight:

$$ \tag{72}
\begin{aligned}
    \bm{w_a}(\bm{x}, \bm{v}, s) &= \frac{\bm{\mu_a}(\bm{x}, \bm{v}, s)}{\bar{\mu}(\bm{x}, \bm{v}, s) P_a(\bm{x}, \bm{v}, s)} \cr
    \bm{w_s}(\bm{x}, \bm{v}, s) &= \frac{\bm{\mu_s}(\bm{x}, \bm{v}, s)}{\bar{\mu}(\bm{x}, \bm{v}, s) P_s(\bm{x}, \bm{v}, s)} \cr
    \bm{w_n}(\bm{x}, \bm{v}, s) &= \frac{\bm{\mu_n}(\bm{x}, \bm{v}, s)}{\bar{\mu}(\bm{x}, \bm{v}, s) P_n(\bm{x}, \bm{v}, s)}
\end{aligned}
$$

The simplest (but [not the only](https://dl.acm.org/citation.cfm?id=3073665)) way to define the collision probabilities is as follows:

$$ \tag{73} \begin{aligned}
    P_a(\bm{x}, \bm{v}, s) &= \frac{||\mu_a(\lambda)||\_{\infty}}{||\mu_a(\lambda)||\_{\infty} + ||\mu_s(\lambda)||\_{\infty} + ||\mu_n(\lambda)||\_{\infty}}, \cr
    P_s(\bm{x}, \bm{v}, s) &= \frac{||\mu_s(\lambda)||\_{\infty}}{||\mu_a(\lambda)||\_{\infty} + ||\mu_s(\lambda)||\_{\infty} + ||\mu_n(\lambda)||\_{\infty}}, \cr
    P_n(\bm{x}, \bm{v}, s) &= \frac{||\mu_n(\lambda)||\_{\infty}}{||\mu_a(\lambda)||\_{\infty} + ||\mu_s(\lambda)||\_{\infty} + ||\mu_n(\lambda)||\_{\infty}},
\end{aligned}
$$

where the position and the direction on the right-hand side are implicit for clarity.

If the medium is known to not be emissive, we can redistribute the absorption probability between \\(P_s\\) and \\(P_n\\):

$$ \tag{74} \begin{aligned}
    P'\_a(\bm{x}, \bm{v}, s) &= 0, \cr
    P'\_s(\bm{x}, \bm{v}, s) &= \frac{P_s}{P_s + P_n}, \cr
    P'\_n(\bm{x}, \bm{v}, s) &= \frac{P_n}{P_s + P_n}.
\end{aligned}
$$

Comparing the Equations 13 and 70, we can see that they are indeed very similar (and so is the estimator). As before, the leading term is canceled out after dividing by the PDF, so the only difference is that we must handle several types of events once a collision actually occurs.

A high-level implementation of the algorithm is listed below.

```c++
float3 SpectralTracking(float3 X, float3 V, uint numWavelengths, uint numPaths)
{
    float3 color = 0;

    const uint n = numWavelengths;
    const uint m = numPaths;

    for (uint j = 0; j < m; j++)
    {
        // spectrum is a data structure with 'numWavelengths' items.
        spectrum waves, wavePdfs;

        waves[0] = GetMajorantWavelength();

        for (uint i = 1; i < n; i++)
        {
            // TODO: Do these have to be stratified? Can these be importance sampled?
            SampleWavelength(i, j, waves[i], wavePdfs[i]);
        }

        spectrum pathWeights = spectrum(1);

        float3 pos = X, dir = V;

        while (true) // Multiple bounces.
        {
            float tMax = RayTraceGeometry(pos, dir);

            // Compute opacity using the majorant.
            float maxOptDepth = EvalOptDepth(pos, dir, waves[0], tMax);
            float maxOpacity  = OpacityFromOpticalDepth(maxOptDepth);

            float opacity = Rnd(); // Sample the CDF.

            if (opacity < maxOpacity) // Volume contribution cancels out the opacity term.
            {
                // Convert to optical depth.
                float optDepth = -log(1 - opacity);
                // Perform distance sampling using the majorant.
                float t = SampleVolumes(optDepth, pos, dir, waves[0]);
                // Step along the ray.
                pos += t * dir;
                // Query the coefficients at the new location.
                spectrum absK, scaK, nulK;
                float    majK;
                LookUpVolumeCoefficients(pos, dir, waves, absK, scaK, nulK, majK);
                // Compute event probabilities.
                // Use the Equation 73, or one of the alternatives from the paper.
                float absP, scaP, nulP;
                ComputeVolumeEventProbabilities(absP, scaP, nulP);

                float u = Rnd(); // Select an event.

                if (u < absP) // Absorption & emission.
                {
                    pathWeights *= absK / (absP * majK);

                    spectrum emission = LookUpEmission(pos, dir, waves);

                    for (uint i = 0; i < n; i++)
                    {
                        float3 normCMF = EvaluateNormalizedColorMatchingFunc(waves[i]);

                        color += normCMF * pathWeights[i] * emission[i];
                    }

                    break; // Terminate the loop due to the absorption event.
                }
                else if (u < (absP + scaP)) // In-scattering.
                {
                    pathWeights *= scaK / (scaP * majK);

                    // A phase function is a PDF, so it does not modify the weight.
                    dir = SamplePhaseFunction(pos, dir);
                    /* Next event estimation omitted for brevity. */
                }
                else // Null-scattering.
                {
                    pathWeights *= nulK / (nulP * majK);
                }
            }
            else // Surface contribution cancels out the transmittance term.
            {
                pos += tMax * dir;
                /* Omitted for brevity. */
            }
        }
    }

    color *= rcp(numPaths);

    return color;
}
```

-->
