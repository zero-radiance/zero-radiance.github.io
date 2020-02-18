---
title: "Sampling Analytic Participating Media"
date: 2020-02-17
hidden: true
draft: true
categories: [ "Graphics", "Math" ]
tags: [
    "Chapman Function",
    "Importance Sampling",
    "Volume Rendering",
    ]
---

Participating media rendering is an important aspect of every modern renderer. When I say participating media, I am not just talking about fog, fire, and smoke. All matter is composed of [atoms](https://courses.lumenlearning.com/boundless-chemistry/chapter/the-structure-of-the-atom/), which can be sparsely (e.g. in a gas) or densely (e.g. in a solid) distributed in space. Whether we consider the particle or the wave nature of [light](https://en.wikipedia.org/wiki/Light), it penetrates all matter (even [metals](http://webhome.phy.duke.edu/~qelectron/group/group_reading_Born_and_Wolf.pdf)) to a certain degree and interacts with its atoms along the way. The nature and the degree of "participation" depend on the material in question.

<!--more-->

In the [radiative transfer](https://archive.org/details/RadiativeTransfer) literature, light-material interaction is usually quantified in terms of absorption (conversion of electromagnetic energy of photons into kinetic energy of atoms, which manifests itself as reduction of light intensity) and scattering (absorption followed by [stimulated emission](https://en.wikipedia.org/wiki/Stimulated_emission) of electromagnetic energy on interaction). Therefore, it is common to describe participating media using the collision coefficients: the *absorption coefficient* \\(\bm{\mu_a}\\) and the *scattering coefficient* \\(\bm{\mu_s}\\). These coefficients give the probability density of the corresponding event per unit distance traveled by a photon, which implies the [SI units](https://en.wikipedia.org/wiki/International_System_of_Units) of measurement are \\(m^{-1}\\). They vary spatially, and may, in general, depend on the orientation unless the particle distribution is [isotropic](http://www.eugenedeon.com/hitchhikers).

The [attenuation coefficient](https://en.wikipedia.org/wiki/Attenuation_coefficient) \\(\bm{\mu_t}\\)

$$ \tag{1} \bm{\mu_t} = \bm{\mu_a} + \bm{\mu_s} $$

gives the probability density of absorption or scattering (or, in other words, the collision rate) as a photon travels a unit distance through the medium. All these coefficients are spectral (they depend on the frequency \\(\nu\\), which is simpler to handle than the wavelength \\(\lambda = c / (\eta \nu)\\) since the latter depends on the [index of refraction](https://en.wikipedia.org/wiki/Refractive_index) \\(\eta\\)), and can be represented as vectors (boldface notation). At this point in time, it is not entirely clear (at least to me) how to *correctly* perform volume rendering using tristimulus (RGB) values (which would require some sort of pre-integration using [color matching functions](https://en.wikipedia.org/wiki/CIE_1931_color_space#Color_matching_functions)), so I will focus on pure spectral rendering, which is well-defined.

A more artist-friendly parametrization uses the [single-scattering albedo](https://en.wikipedia.org/wiki/Single-scattering_albedo) \\(\bm{\alpha\_{ss}}\\)

$$ \tag{2} \bm{\alpha\_{ss}} = \frac{\bm{\mu_s}}{\bm{\mu_t}}, $$

which gives the deflection probability (or, in other words, the scattering rate), and the [mean free path](https://en.wikipedia.org/wiki/Mean_free_path) \\(\bm{d}\\)

$$ \tag{3} \bm{d} = \frac{1}{\bm{\mu_t}}, $$

which corresponds to the average collision-free (or free-flight) distance.

Taking a small detour, for metals, the absorption coefficient is directly related to the [extinction coefficient](http://www.sfu.ca/~gchapman/e376/e376l7.pdf) \\(\bm{\kappa}\\), which is the imaginary part of the [complex index of refraction](https://en.wikipedia.org/wiki/Refractive_index#Complex_refractive_index) \\(\bm{\eta} + i \bm{\kappa}\\):

$$ \tag{4} \bm{\kappa} = \frac{\bm{\lambda}}{4 \pi} \bm{\mu_a}. $$

For this reason, \\(\bm{\eta}\\) is called the [refractive index](https://www.feynmanlectures.caltech.edu/I_31.html) (or IOR), and \\(\bm{\kappa}\\) is sometimes referred to as the [absorption index](http://www.sfu.ca/~gchapman/e376/e376l7.pdf). Note that I am not talking about the IOR of an individual particle (which influences the [microscopic scattering](https://en.wikipedia.org/wiki/Mie_scattering) process), but rather about the macroscopic properties of the medium itself.

The tuple \\(\lbrace \bm{\eta}, \bm{\kappa}, \bm{\mu_s} \rbrace\\) \\(\big(\\)or, alternatively, \\(\lbrace \bm{\eta}, \bm{d}, \bm{\alpha\_{ss}} \rbrace \big) \\) contains sufficient information to describe both the behavior at the surface (boundary) and the (isotropic) multiple-scattering process (known as [subsurface scattering](https://en.wikipedia.org/wiki/Subsurface_scattering)) inside the volume that ultimately gives rise to what we perceive as the surface albedo \\(\bm{\alpha\_{ms}}\\). Note that certain materials (metals, in particular) require modeling of [wave interference](https://en.wikipedia.org/wiki/Wave_interference) to obtain expected reflectance values (so a solution based on geometric optics would yield inaccurate results).

A surface, then, is just an optical interface signified by a discontinuity of optical properties of the medium (in reality, the [transition at the boundary is continuous](https://www.feynmanlectures.caltech.edu/II_33.html), with a thickness of several atomic layers, but we can ignore this fact at scales relevant to computer graphics).

Sometimes, it is convenient to specify the concentration (density) of the medium, and not its effective optical properties. For example, the attenuation coefficient can be computed using the following formula:

$$ \tag{5} \bm{\mu_t} = \rho \bm{\sigma_t}, $$

where \\(\rho\\) is the [mass density](https://en.wikipedia.org/wiki/Mass_density) (measured in units of \\(kg/m^{3}\\)) and \\(\bm{\sigma_t}\\) is the [mass attenuation coefficient](https://en.wikipedia.org/wiki/Mass_attenuation_coefficient) (in units of \\(m^{2}/kg\\)) - [effective cross section](http://www.sfu.ca/~gchapman/e376/e376l7.pdf) per unit mass. Other coefficients have the same linear relation with density.

But what about the IOR? Often, one assumes that it is independent of density. But, if you consider, for example, water and steam (which has a lower concentration of water molecules), our experience tells us that their refractive properties are not the same.

There are several known relations between density and the IOR. One of them is given by the [Lorentz–Lorenz equation](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation):

$$ \tag{6} \frac{\bm{\eta}^2 - 1}{\bm{\eta}^2 + 2} = \frac{4}{3} \pi \frac{\rho}{m} \bm{\alpha_m}, $$

where \\(m\\) is the [molecular mass](https://en.wikipedia.org/wiki/Molecular_mass) (in \\(kg\\)) and \\(\bm{\alpha_m}\\) is the [molecular polarizability](https://en.wikipedia.org/wiki/Electric_susceptibility#Molecular_polarizability) (in \\(m^3\\), watch out for different [conventions](https://en.wikipedia.org/wiki/Electric_susceptibility#Ambiguity_in_the_definition)). Incidentally, this equation presents a way to compute the IOR of a mixture of several substances. The corresponding [Lorentz–Lorenz mixture rule](https://www.sciencedirect.com/science/article/pii/S0021850208001183) is based on four principles of additivity (namely, of mole, mass, volume, and molecular polarizability, with the last two assumption being rather context-dependent).

For materials with small mass densities, the molecules are far apart, the molecular interactions are weak, and the IOR is close to 1. Therefore, for matter in the gas state, the following approximation can be made:

$$ \begin{aligned} \tag{7}
    \bm{\eta}^2 & \approx 1 + 4 \pi \frac{\rho}{m} \bm{\alpha_m} = 1 + 2 \bm{c} \rho, \cr
    \bm{\eta}   & \approx 1 + 2 \pi \frac{\rho}{m} \bm{\alpha_m} = 1 + \bm{c} \rho,
\end{aligned} $$

where \\(\bm{c}\\) is the [light dispersion coefficient](https://ui.adsabs.harvard.edu/abs/1996CoSka..26...23K/abstract). This equation implies that the [relative brake power](https://www.sciencedirect.com/topics/chemistry/optical-refraction) \\((\bm{n} - 1)\\) has an approximately linear relation with density. Similar relations can be found for [temperature and and pressure](https://en.wikipedia.org/wiki/Clausius%E2%80%93Mossotti_relation) (in fact, all coefficients are highly [temperature-dependent](http://www.sfu.ca/~gchapman/e376/e376l7.pdf)). Also, while the discussion above mostly concerns dielectrics, the formula for metals is [very similar](https://www.feynmanlectures.caltech.edu/II_32.html#mjx-eqn-EqII3238).

Continuous variation of the IOR poses many challenges for path tracing. In piecewise-homogeneous media, paths are composed of straight segments joined at scattering locations. Unfortunately, due to [Fermat's principle](https://en.wikipedia.org/wiki/Fermat%27s_principle), continuously varying IOR forces photons to travel along paths of least time that [bend](http://www.waves.utoronto.ca/prof/svhum/ece422/notes/20a-atmospheric-refr.pdf) towards regions of higher density according to [Snell's law](https://en.wikipedia.org/wiki/Snell%27s_law). This fact alone makes basic actions like visibility testing quite complicated. And since the IOR may depend on the frequency, it can cause [dispersion](https://en.wikipedia.org/wiki/Dispersion_(optics)) not only at the interfaces, but also continuously, along the entire path. Finally, one must model losses and gains due to [continuous reflection](https://www.scirp.org/pdf/opj_2013112009301643.pdf), which is mathematically challenging. So it is not too surprising that most renderers ignore this behavior (even though, physically, that doesn't make much sense). For small density gradients and small distances, it is a valid approximation that, on average, gives reasonably accurate results. On the other hand, for certain atmospheric effects, [atmospheric refraction](https://en.wikipedia.org/wiki/Atmospheric_refraction) and reflection make a non-negligible contribution.

For practical reasons, further discussion will use a (typical) assumption that, within volume boundaries, the IOR is invariant with respect to position. If you are interested in continuous refraction, I encourage you to check out the [Refractive Radiative Transfer Equation](https://dl.acm.org/doi/abs/10.1145/2557605) paper.

## Radiative Transfer Equation

Intelligent sampling of a function requires understanding which parts make a large contribution. Therefore, we must briefly discuss the [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer) (or RTE) used to render scenes with participating media. While the full [derivation](https://archive.org/details/RadiativeTransfer) is outside the scope of this article, we will try to touch the important aspects.

The integral form of the RTE is that of a recursive line integral. Intuitively, it models the process of photons traveling along the ray from sources towards the sensor, while at the same time accounting for energy losses.

Primarily, these losses are expressed by the opacity term \\(\bm{O}\\), which is defined as the fraction of photons (quantified as radiance \\(\bm{L}\\)) lost along the ray from \\(\bm{x}\\) to \\(\bm{y}\\) due to absorption and out-scattering:

$$ \tag{8} \bm{O}(\bm{x}, \bm{y}) = \frac{\bm{L}(\bm{y}, \bm{\hat{v}}) - \bm{L}(\bm{x}, \bm{\hat{v}})}{\bm{L}(\bm{y}, \bm{\hat{v}})} = 1 - \frac{\bm{L}(\bm{x}, \bm{\hat{v}})}{\bm{L}(\bm{y}, \bm{\hat{v}})}, $$

where \\(\bm{\hat{v}} = (\bm{x} - \bm{y})/ \Vert \bm{x} - \bm{y} \Vert \\) is the normalized view direction. Intuitively, \\(\bm{O}(\bm{x}, \bm{y}) = \bm{O}(\bm{y}, \bm{x})\\).

Its complement is transmittance \\(\bm{T}\\):

$$ \tag{9} \bm{T}(\bm{x}, \bm{y}) = 1 - \bm{O}(\bm{x}, \bm{y}). $$

For a single photon, transmittance gives the probability of a free flight.

Volumetric transmittance is given by the [Beer–Lambert–Bouguer law](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law) for [uncorrelated](https://cs.dartmouth.edu/~wjarosz/publications/bitterli18framework.html) media in terms of [optical depth](https://en.wikipedia.org/wiki/Optical_depth) (or optical thickness) \\(\bm{\tau}\\), which is a line integral from \\(\bm{x}\\) to \\(\bm{y}\\):

$$ \tag{10} \bm{\tau}(\bm{x}, \bm{y})
    = -\log{\bm{T}(\bm{x}, \bm{y})}
    = \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) d\mu(\bm{u})
    = \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) du,
$$

where \\(\bm{u} = \bm{x} - u \bm{\hat{v}}\\) is the point at the distance \\(u\\) along the ray, and \\(d\mu(\bm{u})\\) is the [measure](https://en.wikipedia.org/wiki/Lebesgue_integration#Measure_theory) of \\(\bm{u}\\) abbreviated as \\(du\\). This formula implies that while transmittance is multiplicative, with values restricted to the unit interval, optical depth is additive and can take on any non-negative value. In other words, transmittance is a [product integral](https://www.wikiwand.com/en/Product_integral):

$$ \tag{11} \bm{T}(\bm{x}, \bm{y})
    = e^{- \int\_{\bm{x}}^{\bm{y}} \bm{\mu_t}(\bm{u}) du}
    = \prod\_{\bm{x}}^{\bm{y}} \Big( 1 - \bm{\mu_t}(\bm{u}) du \Big).
$$

Other [integral formulations](https://cs.dartmouth.edu/~wjarosz/publications/georgiev19integral.html) of volumetric transmittance exist.

The RTE models three types of energy sources: [volumetric emission](https://en.wikipedia.org/wiki/Spontaneous_emission) \\(\bm{L_e}\\), volumetric in-scattering \\(\bm{L_s}\\), and surface in-scattering \\(\bm{L_g}\\) (which is the standard surface geometry term with a BSDF). The volumetric in-scattering term \\(\bm{L_s}\\) is an integral over a sphere of directions \\(S^2\\):

$$ \tag{12} \bm{L_s}(\bm{x}, \bm{\hat{v}}) = \int\_{S^2} \bm{\alpha\_{ss}}(\bm{x}) f_p(\bm{x}, \bm{\hat{v}}, \bm{\hat{l}}) \bm{L}(\bm{x}, \bm{\hat{l}}) d\hat{l}, $$

where \\(f_p\\) denotes the [phase function](http://www.pbr-book.org/3ed-2018/Volume_Scattering/Phase_Functions.html) which models the scattering distribution of an [individual particle](https://en.wikipedia.org/wiki/Mie_scattering) (as opposed to collision coefficients which describe the properties of bulk matter).

Carefully putting it all together yields the [volume rendering equation](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html) along the ray \\(\bm{u} = \bm{x} - u \bm{\hat{v}}\\):

$$ \tag{13}
    \bm{L}(\bm{x}, \bm{\hat{v}}) =
    \int\_{\bm{x}}^{\bm{y}} \bm{T}(\bm{x}, \bm{u}) \Big(
        \bm{\mu_a}(\bm{u}) \bm{L_e}(\bm{u}, \bm{\hat{v}}) +
        \bm{\mu_t}(\bm{u}) \bm{L_s}(\bm{u}, \bm{\hat{v}})
    \Big) du +
    \bm{T}(\bm{x}, \bm{y}) \bm{L_g}(\bm{y}, \bm{\hat{v}}),
$$

where \\(\bm{y}\\) denotes the position of the closest surface along the ray.

We can leave [volumetric emission](https://dl.acm.org/doi/10.1111/cgf.13228) out by setting \\(\bm{L_e} = 0\\):

$$ \tag{14}
    \bm{L}(\bm{x}, \bm{\hat{v}}) =
    \int\_{\bm{x}}^{\bm{y}} \bm{T}(\bm{x}, \bm{u}) \bm{\mu_t}(\bm{u}) \bm{L_s}(\bm{u}, \bm{\hat{v}}) du +
    \bm{T}(\bm{x}, \bm{y}) \bm{L_g}(\bm{y}, \bm{\hat{v}}).
$$

We can evaluate this integral using one of the [Monte Carlo](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration.html) methods. The [Monte Carlo estimator](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/The_Monte_Carlo_Estimator.html) of the integral (for a particular frequency) takes the following form:

$$ \tag{15} L(\bm{x}, \bm{\hat{v}})
    \approx \frac{1}{N} \sum\_{i=1}^{N} \frac{T(\bm{x}, \bm{u_i}) \mu_t(\bm{u_i}) L_s(\bm{u_i}, \bm{\hat{v}})}{p( u_i | \lbrace \bm{x}, \bm{\hat{v}} \rbrace)} + T(\bm{x}, \bm{y}) L_g(\bm{y}, \bm{\hat{v}}),
$$

where sample locations \\(\bm{u_i}\\) at the distance \\(u_i\\) along the ray are distributed according to the [PDF](https://en.wikipedia.org/wiki/Probability_density_function) \\(p\\).

We can [importance sample](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Importance_Sampling.html) the integrand (distribute samples according to the PDF) in several ways. Ideally, we would like to make the PDF proportional to the [product](https://cgg.mff.cuni.cz/~jaroslav/papers/2014-zerovar/) of all terms of the integrand. However, unless we use [path guiding](https://cgg.mff.cuni.cz/~jirka/path-guiding-in-production/2019/index.htm), that is typically not possible. We will focus on the technique called [free path sampling](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html) that makes the PDF proportional to the transmittance-attenuation product \\(T \mu_t\\) (effectively, by assuming that the rest of the integrand varies slowly; in practice, this may or may not be the case - for example, for regions near light sources, [equiangular sampling](http://library.imageworks.com/pdfs/imageworks-library-importance-sampling-of-area-lights-in-participating-media.pdf) can give vastly superior results).

In order to turn the attenuation-transmittance product \\(T \mu_t\\) into a PDF, it must be normalized over the domain of integration, \\(\bm{x}\\) to \\(\bm{y}\\). We must compute the normalization factor

$$ \tag{16}
      \int\_{\bm{x}}^{\bm{y}} \bm{T}(\bm{x}, \bm{u}) \bm{\mu_t}(\bm{u}) du
    = \int\_{\bm{x}}^{\bm{y}} e^{-\bm{\tau}(\bm{x}, \bm{u})} \bm{\mu_t}(\bm{u}) du.
$$

If we use the [fundamental theorem of calculus](https://en.wikipedia.org/wiki/Fundamental_theorem_of_calculus#First_part) to interpret the attenuation coefficient as a derivative

$$ \tag{17} \bm{\mu_t}(\bm{u}) = \frac{\partial \bm{\tau}}{\partial u}, $$

we can use the one of the [exponential identities](https://en.wikipedia.org/wiki/List_of_integrals_of_exponential_functions#Integrals_involving_only_exponential_functions) to simplify the attenuation-transmittance integral:

$$ \tag{18}
    \int\_{\bm{x}}^{\bm{y}} e^{-\bm{\tau}(\bm{x}, \bm{u})} \frac{\partial \bm{\tau}(\bm{x}, \bm{u})}{\partial u} du
    = -e^{-\bm{\tau}(\bm{x}, \bm{u})} \Big\vert\_{\bm{x}}^{\bm{y}}
    = 1 - \bm{T}(\bm{x}, \bm{y}) = \bm{O}(\bm{x}, \bm{y}).
$$

Most remarkably, optical depth can be evaluated in a forward or backward fashion, and the [result is the same](https://cs.dartmouth.edu/~wjarosz/publications/georgiev19integral.html)!

$$ \tag{19}
    \int\_{\bm{x}}^{\bm{y}} e^{-\bm{\tau}(\bm{x}, \bm{u})} \bm{\mu_t}(\bm{u}) du =
    \int\_{\bm{x}}^{\bm{y}} e^{-\bm{\tau}(\bm{u}, \bm{y})} \bm{\mu_t}(\bm{u}) du.
$$

We can now define the normalized sampling PDF:

$$ \tag{20} p(u | \lbrace \bm{x}, \bm{\hat{v}} \rbrace)
    = \frac{T(\bm{x}, \bm{u}) \mu_t(\bm{u})}{O(\bm{x}, \bm{\bm{y}})}. $$

Substitution of the PDF into Equation 15 radically simplifies the estimator (again, for a particular frequency):

$$ \tag{21} L(\bm{x}, \bm{\hat{v}}) \approx O(\bm{x}, \bm{\bm{y}}) \frac{1}{N} \sum\_{i=1}^{N} L_s(\bm{u_i}, \bm{\hat{v}}) + T(\bm{x}, \bm{y}) L_g(\bm{y}, \bm{\hat{v}}). $$

This equation can be seen as a form of [premultiplied alpha blending](https://graphics.pixar.com/library/Compositing/) (where alpha is opacity), which explains why particle cards can be so convincing. Additionally, it offers yet another way to parametrize the attenuation coefficient - namely, by opacity at distance (which is similar to [transmittance at distance](https://blog.selfshadow.com/publications/s2015-shading-course/burley/s2015_pbs_disney_bsdf_notes.pdf) used by Disney). It is the most RGB rendering friendly parametrization that I am aware of.

In this context, total opacity along the ray serves as the probability of a collision event within the medium, and can be used to make a random choice of the type of the sample (surface or volume):

$$ \tag{22} L(\bm{x}, \bm{\hat{v}}) \approx O(\bm{x}, \bm{\bm{y}}) \frac{1}{N} \sum\_{i=1}^{N} L_s(\bm{u_i}, \bm{\hat{v}}) + \big(1 - O(\bm{x}, \bm{\bm{y}}) \big) L_g(\bm{y}, \bm{\hat{v}}).
$$

In order to sample the integrand in Equation 14, we must be also able to [invert](http://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Sampling_Random_Variables.html#TheInversionMethod) the [CDF](https://en.wikipedia.org/wiki/Cumulative_distribution_function) \\(P\\):

$$ \tag{23} P(u | \lbrace \bm{x}, \bm{\hat{v}} \rbrace)
    = \int\_{0}^{u} p(s | \lbrace \bm{x}, \bm{\hat{v}} \rbrace) ds
    = \frac{O(\bm{x}, \bm{\bm{u}})}{O(\bm{x}, \bm{\bm{y}})}.
$$

In practice, this means that we need to solve for the distance \\(u\\) given the value of optical depth \\(\tau\\):

$$
\tag{24} \tau(\bm{x}, \bm{u}) =
    -\mathrm{log} \big( O(\bm{x}, \bm{u}) \big) =
    -\mathrm{log} \Big( 1 - P \big(u | \lbrace \bm{x}, \bm{\hat{v}} \rbrace \big) O \big( \bm{x}, \bm{\bm{y}} \big) \Big).
$$

## Types of Analytic Participating Media

If your background is in real-time rendering, you may have heard of [constant, linear and exponential fog](http://www.terathon.com/lengyel/Lengyel-UnifiedFog.pdf). These names refer to the way density varies in space (typically, with respect to height), and can be used to model effects like height fog and atmospheric scattering. In these scenarios, the albedo is usually assumed to be constant.

### Constant Density

A homogeneous medium has uniform density across the entire volume:

$$ \tag{25} \rho = k. $$

This formulation makes computing optical depth easy (recall Equations 5 and 10):

$$ \tag{26} \bm{\tau}(\bm{x}, \bm{u})
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{u}} \rho{(\bm{s})} ds
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{u}} k ds
    = \bm{\sigma_t} k \Vert \bm{u} - \bm{x} \Vert
    = \bm{\sigma_t} k u.
$$

The sampling "recipe" for the distance \\(u\\) (and for a particular frequency \\(\nu\\)) is thus simply

$$ \tag{27} u = \frac{\tau}{\sigma_t k}, $$

which is consistent with [previous work](https://cs.dartmouth.edu/~wjarosz/publications/novak18monte.html).

The resulting sampling algorithm is very simple:

1. compute total opacity \\(O \big( \bm{x}, \bm{\bm{y}} \big)\\) along the ray;
2. generate a random CDF value \\(P \big(u | \lbrace \bm{x}, \bm{\hat{v}} \rbrace \big)\\);
3. compute optical depth \\(\tau(\bm{x}, \bm{u})\\) using Equation 24;
4. compute the distance \\(u\\) using Equation 27.

### Linear Variation of Density with Altitude in Rectangular Coordinates

Without loss of generality, let's assume that density varies with the third coordinate of the position \\(\bm{x}\\), which we interpret as the altitude. This is your typical "linear height fog on flat Earth" case:

$$ \tag{28} \rho(\bm{x}) = a h(\bm{x}) + k = a x_3 + k. $$

This formulation can be reduced to homogeneous media by setting \\(a = 0\\).

We would like to evaluate the optical depth integral:

$$ \tag{29} \bm{\tau}(\bm{x}, \bm{u})
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{u}} \rho{(\bm{s})} ds
    = \bm{\sigma_t} \int\_{\bm{x}}^{\bm{u}} \Big( a h(\bm{s}) + k \Big) ds.
$$

In practice, it's actually simpler to integrate with respect to the parametric coordinates of the ray:

$$ \tag{30}
\bm{\tau}(\bm{x}, \bm{\hat{v}}, u)
    = \bm{\sigma_t} \int\_{0}^{u} \Big( a \big(x_3 - s \hat{v}_3) + k \Big) ds
    = \bm{\sigma_t} \Big( (a x_3 + k) - \frac{a \hat{v}_3}{2} u \Big) u,
$$

which is the product of the average attenuation coefficient and the length of the interval, as expected.

The inversion process involves solving the quadratic equation for the distance \\(u\\):

$$ \tag{31}
    u = \frac{(a x_3 + k) \pm \sqrt{ (a x_3 + k)^2 - 2 a \hat{v}\_3 (\tau / \sigma_t)}}{a \hat{v}\_3}.
$$

Phusically, we are only interested in the smaller root (with the negative sign), since it gives the solution for positive density values. Note that homogeneous media \\( \big( m \hat{v}\_3 = 0 \big) \\) require special care.

### Exponential Variation of Density with Altitude in Rectangular Coordinates

We can replace the linear density function with an exponential:

$$ \tag{32} \rho(\bm{x}) = k e^{-h(\bm{x}) / H} = k e^{-x_3 / H}, $$

where \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height), measured in meters. Another way to think about it is as of the reciprocal of the falloff exponent \\(n\\):

$$ \tag{33} \rho(\bm{x}) = k e^{-n x_3}. $$

Setting \\(n = 0\\) results in a homogeneous medium.

The expression of optical depth remains simple:

$$ \tag{34}
\bm{\tau}(\bm{x}, \bm{\hat{v}}, u)
    = \bm{\sigma_t} \int\_{0}^{u} k e^{-n (x_3 - s \hat{v}_3)} ds
    = \bm{\sigma_t} k e^{-n x_3} \int\_{0}^{u} e^{s n \hat{v}_3} ds
    = \frac{\bm{\sigma_t} k}{n \hat{v}\_3} e^{-n x_3} \Big( e^{n \hat{v}\_3 u} - 1 \Big).
$$

Solving for the distance \\(u\\) is straightforward:

$$ \tag{35} u = \frac{1}{n \hat{v}\_3} \log \left(1 + \frac{\tau}{\sigma_t k} n \hat{v}\_3 e^{n x_3} \right). $$

Note that homogeneous media \\( \big( n \hat{v}\_3 = 0 \big) \\) require special care.

Sample code is listed below.

```c++
// 'height' is the altitude.
// 'viewZ' is the Z coordinate of the view direction.
// 'dist' is the distance.
// seaLvlAtt = (sigma_t * k) is the sea-level (height = 0) attenuation coefficient.
// rcpH = rcp(H) is the falloff exponent, where 'H' is the scale height.
spectrum OptDepthRectExpMedium(float height, float viewZ, float dist,
                               spectrum seaLvlAtt, float rcpH)
{
    float p = viewZ * rcpH;

    // Equation 26.
    spectrum optDepth = seaLvlAtt * dist;

    if (abs(p) > FLT_EPS) // Homogeneity check
    {
        // Equation 34.
        optDepth = seaLvlAtt * rcp(p) * exp(-height * rcpH) * (exp(p * dist) - 1);
    }

    return optDepth;
}

// 'optDepth' is the value of optical depth.
// rcpSeaLvlAtt = rcp(seaLvlAtt).
float SampleRectExpMedium(float optDepth, float height, float viewZ,
                          float rcpSeaLvlAtt, float rcpH)
{
    float p = viewZ * rcpH;

    // Equation 27.
    float dist = optDepth * rcpSeaLvlAtt;

    if (abs(p) > FLT_EPS) // Homogeneity check
    {
        // Equation 35.
        dist = rcp(p) * log(1 + dist * p * exp(height * rcpH));
    }

    return dist;
}
```

### Exponential Variation of Density with Altitude in Spherical Coordinates

This is where things get interesting. We would like to model an exponential density distribution on a sphere:

$$ \tag{36} \rho(\bm{x}) = k e^{-h(\bm{x}) / H} = k e^{-(\Vert \bm{x} - \bm{c} \Vert - R) / H} = k e^{-n (\Vert \bm{x} - \bm{c} \Vert - R)}, $$

where \\(\bm{c}\\) is the center of the sphere, \\(R\\) is its radius, \\(h\\) is the altitude, and \\(H\\) is the [scale height](https://en.wikipedia.org/wiki/Scale_height) as before. In this context, \\(k\\) and \\(\bm{\sigma_t} k\\) represent the density and the value of the attenuation coefficient at the sea level, respectively. This formula gives the density of an [isothermal atmosphere](http://www.feynmanlectures.caltech.edu/I_40.html), which is not physically plausible.

#### Geometric Configuration of a Spherical Atmosphere

We can simplify the problem using its inherent spherical symmetry. Take a look at the diagram below.

{{< figure src="/img/spherical_param.png">}}

We start by recognizing the fact that every ordered pair of position and direction \\(\lbrace \bm{x}, \bm{\hat{v}} \rbrace\\) can be reduced to a pair of radial distance and zenith angle \\(\lbrace r, \theta \rbrace\\), which means that our phase space is 2-dimensional.

In order to find the parametric equation of altitude \\(h\\) along the ray, we can use a right triangle with sides of length \\(r_0\\) and \\(s_0\\) corresponding to the initial position \\(\bm{x}\\):

$$ \tag{37} r_0 = r \sin{\theta}, \qquad s_0 = r \cos{\theta}. $$

This allows us to easily determine the radial distance and the zenith angle at any point along the ray.

$$ \tag{38}
\mathcal{R}(r, \theta, s)
    = \sqrt{r_0^2 + (s_0 + s)^2}
    = \sqrt{(r \sin{\theta})^2 + (r \cos{\theta} + s)^2}
    = \sqrt{r^2 + s (2 r \cos{\theta} + s)}.
$$

$$ \tag{39}
\mathcal{C}(r, \theta, s)
    = \frac{\mathrm{adjacent}}{\mathrm{hypotenuse}}
    = \frac{s_0 + s}{\mathcal{R}(r, \theta, s)}
    = \frac{r \cos{\theta} + s}{\sqrt{r^2 + s (2 r \cos{\theta} + s)}}.
$$

We can now compose the optical depth integral:

$$ \tag{40} \begin{aligned}
\bm{\tau}(r, \theta, u)
    &= \bm{\sigma_t} k \int\_{0}^{u} e^{-n h(s)} ds \cr
    &= \bm{\sigma_t} k \int\_{0}^{u} e^{-n \big( \mathcal{R}(r, \theta, s) - R \big)} ds \cr
    &= \bm{\sigma_t} \frac{k}{n} e^{n (R - r)} \int\_{0}^{u} e^{n \big( r - \mathcal{R}(r, \theta, s) \big)} n ds \cr
    &= \bm{\sigma_t} \frac{k}{n} e^{n (R - r)} \int\_{0}^{u} e^{n \big( r - \sqrt{r^2 + s (2 r \cos{\theta} + s)} \big)} n ds.
\end{aligned} $$

The resulting integral is very complex. If we simplify using the following change of variables

$$ \tag{41} t = n s, \qquad z = n r, \qquad Z = n R $$

and change the upper limit of integration to infinity, we obtain what is known in the physics community as the [Chapman's grazing incidence integral](https://ui.adsabs.harvard.edu/abs/1931PPS....43...26C/abstract) (or the obliquity function, or the relative optical air mass) \\(C\\):

$$ \tag{42} C(z, \theta) = \int\_{0}^{\infty} e^{z - \sqrt{z^2 + t (2 z \cos{\theta} + t)}} dt. $$

It is convenient to define the rescaled Chapman function \\(C_r\\)

$$ \tag{43} C_r(z, \theta) = e^{Z - z} C(z, \theta) = \int\_{0}^{\infty} e^{Z - \sqrt{z^2 + t (2 z \cos{\theta} + t)}} dt, $$

which has a better numerical behavior, and further simplifies the expression of optical depth between \\(\bm{x}\\) and \\(\bm{y}\\):

$$ \tag{44}
\bm{\tau}(\bm{x}, \bm{y})
    = \bm{\sigma_t} \frac{k}{n} \Bigg( C_r \Big(z(\bm{x}), \cos{\theta(\bm{x})} \Big) - C_r \Big(z(\bm{y}), \cos{\theta(\bm{y})} \Big) \Bigg).
$$

What Equation 44 tells us is that we should evaluate the optical depth integral twice (in the same direction, along the entire ray, from 0 to \\(\infty\\)), at the start and at the end of the interval, and subtract the results to "clip" the ray.

It is interesting to contemplate the physical meaning of optical depth and the Chapman function. Generally speaking, the value of a line integral of density (such as given by \\(\bm{\tau} / \bm{\sigma_t}\\)) corresponds to mass. Therefore, the integral

$$ \tag{45} \int\_{h = (r - R)}^{\infty} k e^{-n s} ds = \frac{k}{n} e^{-n h} $$

gives the mass of an infinitely tall vertical column starting at the altitude \\(h\\). At the ground level, its mass is \\(k/n = kH\\).

Optical depth, then, is a *product* of the mass of the vertical column *and* the value of the obliquity function (which, intuitively, gives the absolute optical air mass along the oblique ray) *times* the mass attenuation coefficient.

#### Examining the Chapman Function

It is always a good idea to examine a function visually, as a graph. Let's do that.

{{< figure src="/img/chapman_ref.png" caption="*Plot of the Chapman function for r = 6600.*">}}

Above, I plotted values of the Chapman function (vertical axis) varying with the angle \\(\theta\\) (horizontal axis, in degrees) for different values of the scale height \\(H\\): \\(1\\) (blue), \\(10\\) (orange), \\(20\\) (green), \\(40\\) (red), \\(60\\) (purple), \\(80\\) (brown), \\(100\\) (light blue).
Arguably, the first two are the most important, since they roughly correspond to scale heights of aerosols and air of Earth's atmosphere. However, it is also nice to be able to support larger values to model atmospheres of [other planets](https://en.wikipedia.org/wiki/Scale_height#Planetary_examples).

Being an obliquity function, \\(C(z, 0) = 1\\). The function varies slowly, as long as the angle is far from being horizontal (which suggests an opportunity for a [small angle approximation](https://en.wikipedia.org/wiki/Small-angle_approximation)).

To my knowledge, the Chapman function does not have a [closed-form](https://en.wikipedia.org/wiki/Closed-form_expression#Analytic_expression) expression. Many [approximations](https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1029/2011JD016706) exist. Unfortunately, most of them are specific to Earth's atmosphere, and we are interested in a general solution. The most accurate approximation I have found was developed by [David Huestis](https://ui.adsabs.harvard.edu/abs/2001JQSRT..69..709H/abstract). It is based on a power series expansion of the integrand. Using the first two terms results in the following formula for \\(\theta \leq \pi/2\\):

$$ \begin{aligned} \tag{46} C_u(z, \theta) \approx
    & \sqrt{\frac{1 - \sin{\theta}}{1 + \sin{\theta}}} \Bigg(1 - \frac{1}{2 (1 + \sin{\theta})} \Bigg) + \frac{\sqrt{\pi z}}{\sqrt{1 + \sin{\theta}}} \times \cr
    & \Bigg[ e^{z - z \sin{\theta}} \text{erfc}\left(\sqrt{z - z \sin{\theta}}\right) \Bigg] \Bigg( -\frac{1}{2} + \sin{\theta} + \frac{1}{1 + \sin{\theta}} + \frac{2 (1 + \sin{\theta}) - 1}{4 z (1 + \sin{\theta})} \Bigg).
\end{aligned}$$

The approximation itself is also not closed-form, since it contains the [complementary error function](http://mathworld.wolfram.com/Erfc.html) \\(\mathrm{erfc}\\). It's also somewhat annoying that the result is given in terms of \\(\sin{\theta}\\) rather than \\(\cos{\theta}\\), but this reparametrization is actually necessarily to expand the function in the power series.

For the angle of 90 degrees, the integral is given using the [modified Bessel function of the second kind](http://mathworld.wolfram.com/ModifiedBesselFunctionoftheSecondKind.html) \\(K_1\\):

$$ \tag{47} C_h(z) = C(z,\frac{\pi}{2}) = z e^z K_1(z) \approx \sqrt{\frac{\pi z}{2}} \left(1 + \frac{3}{8 z} -\frac{15}{128 z^2}\right). $$

We use a slightly more [accurate approximation](https://ui.adsabs.harvard.edu/abs/2001JQSRT..69..709H/abstract) than \\(C_u(z, \pi/2)\\) (we add the quadratic term) to obtain some extra precision near 0.

Beyond the 90 degree angle, the following [identity](https://ui.adsabs.harvard.edu/abs/2001JQSRT..69..709H/abstract) can be used:

$$ \tag{48} C_l(z, \theta) = 2 C_h(z \sin{\theta}) e^{z - z \sin{\theta}} - C_u(z, \pi - \theta), $$

which means that we must find a position \\(\bm{p}\\) (sometimes called the [periapsis](https://en.wikipedia.org/wiki/Apsis) point, see the diagram in the previous section) along the ray where it is orthogonal to the surface normal, evaluate the horizontal Chapman function there (twice, forward and backward, to cover the entire real line), and subtract the value of the Chapman function at the original position with the reversed direction (towards the atmospheric boundary), which isolates the integral to the desired ray segment.

Sample implementation is listed below.

```c++
float ChapmanUpper(float z, float cosTheta)
{
    float sinTheta = sqrt(saturate(1 - cosTheta * cosTheta));

    float zm12 = rsqrt(z);        // z^(-1/2)
    float zp12 = z * zm12;        // z^(+1/2)

    float tp   = 1 + sinTheta;    // 1 + Sin
    float rstp = rsqrt(tp);       // 1 / Sqrt[1 + Sin]
    float rtp  = rstp * rstp;     // 1 / (1 + Sin)
    float stm  = cosTheta * rstp; // Sqrt[1 - Sin] = Cos / Sqrt[1 + Sin]
    float arg  = zp12 * stm;      // Sqrt[z - z * Sin], argument of Erfc
    float e2ec = Exp2Erfc(arg);   // Exp[x^2] * Erfc[x]

    // Term 1 of Equation 46.
    float mul1 = cosTheta * rtp; // Sqrt[(1 - Sin) / (1 + Sin)] = Cos / (1 + Sin)
    float trm1 = mul1 * (1 - 0.5 * rtp);

    // Term 2 of Equation 46.
    float mul2 = SQRT_PI * rstp * e2ec; // Sqrt[Pi / (1 + Sin)] * Exp[x^2] * Erfc[x]
    float trm2 = mul2 * (zp12 * (-1.5 + tp + rtp) +
                         zm12 * 0.25 * (2 * tp - 1) * rtp);
    return trm1 + trm2;
}

float ChapmanHorizontal(float z)
{
    float zm12 = rsqrt(z);           // z^(-1/2)
    float zm32 = zm12 * zm12 * zm12; // z^(-3/2)

    float p = -0.14687275046666018 + z * (0.4699928014933126 + z * 1.2533141373155001);

    // Equation 47.
    return p * zm32;
}

// z = (r / H), Z = (R / H).
float RescaledChapman(float z, float Z, float cosTheta)
{
    float sinTheta = sqrt(saturate(1 - cosTheta * cosTheta));

    // Cos[Pi - theta] = -Cos[theta],
    // Sin[Pi - theta] =  Sin[theta],
    // so we can just use Abs[Cos[theta]].
    float ch = ChapmanUpper(z, abs(cosTheta)) * exp(Z - z); // Rescaling adds 'exp'

    if (cosTheta < 0)
    {
        // Ch[z, theta] = 2 * Exp[z - z_0] * Ch[z_0, Pi/2] - Ch[z, Pi - theta].
        // z_0 = r_0 / H = (r / H) * Sin[theta] = z * Sin[theta].
        float z_0 = z * sinTheta;
        float chP = ChapmanHorizontal(z_0) * exp(Z - z_0); // Rescaling adds 'exp'

        // Equation 48.
        ch = 2 * chP - ch;
    }

    return ch;
}
```

We can evaluate the quality of the approximation by computing the error with respect to the integral numerically evaluated in Mathematica.

{{< figure src="/img/chapman_approx_abs.png" caption="*Absolute error plot of the approximation of the Chapman function for r = 6600.*">}}

{{< figure src="/img/chapman_approx_rel.png" caption="*Relative error plot of the approximation of the Chapman function for r = 6600.*">}}

We can also represent the relative error as *precision* by plotting the number of digits after the decimal point. Since decimal precision of 32-bit floating numbers is between [6-8 digits](https://www.exploringbinary.com/decimal-precision-of-binary-floating-point-numbers/), the approximation can be considered relatively accurate (particularly so for the range of typical values).

{{< figure src="/img/chapman_approx_dig.png" caption="*Precision plot of the approximation of the Chapman function for r = 6600.*">}}

Of course, we must address the elephant in the room, \\(\mathrm{erfc}\\). Since it is [related](https://www.johndcook.com/erf_and_normal_cdf.pdf) to the [normal distribution](https://en.wikipedia.org/wiki/Normal_distribution), it has numerous applications, and, as a result, dozens of existing approximations. Unfortunately, most of them are not particularly accurate, especially across a huge range of values (as in our case), and accuracy of \\(\mathrm{erfc}\\) greatly affects the quality of the full approximation.

It took me a while to find the approximation developed by [Takuya Ooura](http://www.kurims.kyoto-u.ac.jp/~ooura/gamerf.html). He provides an impressive implementation (written C) accurate to 16 decimal digits. That's actually too accurate (and too expensive) for our needs, but it's relatively easy to reduce the degree of the polynomial in order to obtain a single-precision version. A great thing about his approximation is that it includes the \\(\exp(x^2)\\) factor, which means we can approximate the entire term of Equation 46 inside the square brackets.

The implementation of Takuya Ooura is reproduced below (with minor changes).

```c++
// Computes (Exp[x^2] * Erfc[x]) for (x >= 0).
// Range of inputs:  [0, Inf].
// Range of outputs: [0, 1].
float Exp2Erfc(float x)
{
    float t, u, y, z;

    t = 3.97886080735226 / (x + 3.97886080735226);
    u = t - 0.5;
#ifdef DOUBLE_PRECISION
    // Original implementation kindly provided by Takuya Ooura.
    // http://www.kurims.kyoto-u.ac.jp/~ooura/gamerf.html
    y = (((((((((0.00127109764952614092 * u + 1.19314022838340944e-4) * u -
        0.003963850973605135) * u - 8.70779635317295828e-4) * u +
        0.00773672528313526668) * u + 0.00383335126264887303) * u -
        0.0127223813782122755) * u - 0.0133823644533460069) * u +
        0.0161315329733252248) * u + 0.0390976845588484035) * u +
        0.00249367200053503304;
#else
    y = (0.0019898212777384947 * u + 0.039833135938877856) * u +
        0.0034132959838498976;
#endif
    z = ((((((((((((y * u - 0.0838864557023001992) * u -
        0.119463959964325415) * u + 0.0166207924969367356) * u +
        0.357524274449531043) * u + 0.805276408752910567) * u +
        1.18902982909273333) * u + 1.37040217682338167) * u +
        1.31314653831023098) * u + 1.07925515155856677) * u +
        0.774368199119538609) * u + 0.490165080585318424) * u +
        0.275374741597376782) * t;

    return z;
}
```

The approximation looks and performs well (in both single and double precision), as you can see from the graphs of the single-precision version.

{{< figure src="/img/exp2erfc.png" caption="*Plot of \\(exp(x^2) erfc(x)\\). The function approaches 0 as the value of the argument increases.*">}}

{{< figure src="/img/exp2erfc_error.png" caption="*Relative error plot of the approximation of \\(exp(x^2) erfc(x)\\).*">}}

I do not include the Relative error plot of \\(\exp(x^2) \mathrm{erfc}(x)\\) since it looks very similar to its absolute error plot. And since the error of this term is lower than the error of the approximation of the Chapman function, substituting the former does not visibly affect the error of the latter.

The proposed approximation is relatively expensive. It is particularly useful for path tracing, since the inversion process (described later) requires a certain degree of accuracy. If high accuracy is not required, you can (and probably should) use the approximation proposed by [Christian Schüler](http://www.gameenginegems.net/gemsdb/article.php?id=1133) in his GPU Gems 3 article:

$$ \tag{49} C_{cs}(z, \theta) \approx \frac{C_h(z)}{(C_h(z) - 1) \cos{\theta} + 1}. $$

It models the shape of the function pretty well, especially considering the cost.

{{< figure src="/img/chapman_chris.png" caption="*Plot of the approximation of the Chapman function by Christian Schüler for r = 6600.*">}}

However, if you care about accuracy, and plot the relative error graph, it paints a slightly less attractive picture.

{{< figure src="/img/chapman_chris_error.png" caption="*Relative error plot of the approximation of the Chapman function by Christian Schüler for \\(r = 6600\\).*">}}

As always, there is a compromise. If you need accuracy (for a certain algorithm or technique), you must use a more accurate implementation. If every last cycle matters, it's perfectly fine to "cheat" as long as the error is not very apparent.

I should also mention that Christian references another analytic expression of the Chapman function originally proposed by [Miroslav Kocifaj](https://ui.adsabs.harvard.edu/abs/1996CoSka..26...23K/abstract). Miroslav's paper has two equations of interest: one for arbitrary altitudes (11a), and one for small altitudes (11b), with the latter referenced in the GPU Pro article. Both look very similar to the power series expansion we are using, while at the same time featuring several orders of magnitude higher error (so both are clearly *approximations*, *not exact* solutions). Additionally, his formula for arbitrary altitudes depends on the planetary radius term (which can not be removed via simplification) which is not present in the integral formulation (Equation 42), which leads me to believe that the paper contains an error.

#### Evaluating Optical Depth Using the Chapman Function

A numerical approximation of the Chapman function, in conjunction with Equation 44, allows us to evaluate optical depth along an arbitrary ray segment.

However, the approximation of the Chapman function contains a branch (upper/lower hemisphere), and using the full formulation twice may be unnecessarily expensive for many use cases.

In order to evaluate optical depth between two arbitrary points \\(\bm{x}\\) and \\(\bm{y}\\), we have to consider three distinct possibilities:

1\. \\(\cos{\theta_x} \geq 0 \\), which means that the ray points into the upper hemisphere with respect to the surface normal at the point \\(\bm{x}\\). This also means it points into the upper hemisphere at any point \\(\bm{y}\\) along the ray (it is fairly obvious if you sketch it). Optical depth is given by Equation 44, which we specialize by replacing \\(C\\) with \\(C_u\\) which is restricted to the upper hemisphere:

$$ \tag{50}
\bm{\tau\_{uu}}(z_x, \theta_x, z_y, \theta_y)
    = \bm{\sigma_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_u(z_x, \theta_x) - e^{Z - z_y} C_u(z_y, \theta_y) \Bigg).
$$

2\. \\(\cos{\theta_x} < 0 \\) and \\(\cos{\theta_y} < 0 \\) occurs e.g. when looking straight down. It is also easy to handle, we just flip the direction of the ray (by taking the absolute value of the cosine), replace the segment \\(\bm{xy}\\) with the segment \\(\bm{yx}\\) and fall back to case 1.

3\. \\(\cos{\theta_x} < 0 \\) and \\(\cos{\theta_y} \geq 0 \\). This is the most complicated case, since we have to evaluate the Chapman function three times, twice at \\(\bm{x}\\) and once at \\(\bm{y}\\):

$$ \tag{51} \begin{aligned}
\bm{\tau\_{lu}}(z_x, \theta_x, z_y, \theta_y)
    &= \bm{\sigma_t} \frac{k}{n} \Bigg( e^{Z - z_x} C_l(z_x, \theta_x) - e^{Z - z_y} C_u(z_y, \theta_y) \Bigg).
\end{aligned} $$

Sample code is listed below.

```c++
float RadAtDist(float r, float cosTheta, float t)
{
    float r2 = r * r + t * (t + 2 * (r * cosTheta));

    // Equation 38.
    return sqrt(r2);
}

float CosAtDist(float r, float cosTheta, float t)
{
    float r2 = r * r + t * (t + 2 * (r * cosTheta));

    // Equation 39.
    return (t + r * cosTheta) * rsqrt(r2);
}

// 'r' is the radial distance from the center of the planet.
// 'viewZ' is the Z coordinate of the view direction.
// 'dist' is the distance.
// seaLvlAtt = (sigma_t * k) is the sea-level (height = 0) attenuation coefficient.
// 'R' is the radius of the planet.
// 'H' is the scale height.
// rcpH = rcp(H) is the falloff exponent.
spectrum OptDepthSpherExpMedium(float r, float viewZ, float dist,
                                spectrum seaLvlAtt, float R,
                                float H, float rcpH)
{
    float rX        = r;
    float cosThetaX = -viewZ; // p = x - s * v
    float rY        = RadAtDist(rX, cosThetaX, dist);
    float cosThetaY = CosAtDist(rX, cosThetaX, dist);

    // Potentially swap X and Y.
    // Convention: at the point Y, the ray points up.
    cosThetaX = (cosThetaY >= 0) ? cosThetaX : -cosThetaX;
    cosThetaY = abs(cosThetaY);

    float Z   = R  * rcpH;
    float zX  = rX * rcpH;
    float zY  = rY * rcpH;

    float chX = RescaledChapman(zX, Z, cosThetaX);
    float chY = ChapmanUpper(zY, cosThetaY) * exp(Z - zY); // Rescaling adds 'exp'

    // We may have swapped X and Y.
    float ch = abs(chX - chY);

    return ch * H * seaLvlAtt;
}
```

Note that using this function (rather than calling `OptDepthSpherExpMedium` twice and subtracting the results) is beneficial not only for performance but also for correctness: it avoids numerical instability near the horizon where rays directions are prone to alternating between the two hemispheres, which could cause subtraction to result in negative optical depth values. For performance, it may be also worth considering a special case is when the point \\(\bm{y}\\) is considered to be outside the atmosphere (if `exp(Z - zY) < EPS`, for instance). In that case, `chY = 0` is an adequate approximation.

#### Sampling Exponential Media in Spherical Coordinates

One does not simply sample the Chapman function. There doesn't appear to be a way to invert the integral formulation (Equation 40), and attempts at solving numerical approximations for distance seem futile. Of course, we still have the option of looking for a numerical fit for the tabulated inverse... But we are not going to do that. And here's why.

In order to sample participating media, we must be able to solve the optical depth equation for distance. If you only have a single analytically-defined volume, sampling it is (usually) trivial. However, once you have several heterogeneous overlapping volumes, you start running into issues. While optical depth is additive, the sampled distance is not. So, what do we do?

 If we can't solve the equation analytically, we can solve it numerically, using the [Newton–Raphson method](https://en.wikipedia.org/wiki/Newton%27s_method). Recall that it requires being able to make an initial guess, evaluate the function, and take its derivative. Our function is the total optical depth. We can make an initial guess by assuming that the combined medium is homogeneous (or, under certain assumptions, rect-exponential). And since we know that the derivative of optical depth is just the attenuation coefficient \\(\mu_t\\) (Equation 17), so we have all the pieces we need.

This method is very general and works for arbitrary [continuous density distributions](http://lib-www.lanl.gov/la-pubs/00367066.pdf).

Sample code for spherical atmospheres is listed below.

```c++
// 'optDepth' is the value to solve for.
// 'maxOptDepth' is the maximum value along the ray, s.t. (maxOptDepth >= optDepth).
// 'maxDist' is the maximum distance along the ray.
float SampleSpherExpMedium(float optDepth, float r, float viewZ,
                           float R, float H, float rcpH,
                           float seaLvlAtt, float rcpSeaLvlAtt,
                           float maxOptDepth, float maxDist)
    {
        const float rcpOptDepth = rcp(optDepth);
        const float Z           = R * rcpH;

        // Make an initial guess.
    #if 1
        // Homogeneous assumption.
        float t = optDepth * rcp(maxOptDepth);
    #else
        // Exponential assumption.
        float t = SampleRectExpMedium(optDepth, r - R, viewZ, rcpSeaLvlAtt, rcpH);
    #endif

        uint  numIter = 0;
        float absDiff = optDepth, relDiff = 1;
        do // Perform a Newton–Raphson iteration.
        {
            float radAtDist = RadAtDist(r, cosTheta, t);
            float cosAtDist = CosAtDist(r, cosTheta, t);
            // Evaluate the function and its derivatives:
            // f  [t] = OptDepthAtDist[t] - GivenOptDepth = 0,
            // f' [t] = AttCoefAtDist[t],
            // f''[t] = AttCoefAtDist'[t] = -AttCoefAtDist[t] * CosAtDist[t] / H.
            float optDepthAtDist = OptDepthSpherExpMedium(r, viewZ, t, seaLvlAtt,
                                                          R, H, rcpH);
            float attAtDist      = seaLvlAtt * exp(Z - radAtDist * rcpH);
            float attAtDistDeriv = -attAtDist * cosAtDist * rcpH;

            float   f = optDepthAtDist - optDepth;
            float  df = attAtDist;
            float ddf = attAtDistDeriv;

            // May happen due to the limited precision of floating-point arithmetic.
            // This should not happen (e.g. 'maxDist' and 'maxOptDepth' should be
            // reduced to prevent this from happening). Ideally, we should write
            // assert(df != 0).
            if (df == 0) return t;

        #if 1
            // https://en.wikipedia.org/wiki/Newton%27s_method
            float dt = -f * rcp(df);
        #else
            // https://en.wikipedia.org/wiki/Halley%27s_method
            float dt = -(f * df) * rcp(df * df - 0.5 * f * ddf);
        #endif

            // Refine the initial guess.
            t = clamp(t + dt, 0, maxDist); // Basic overshoot handling

            absDiff = abs(optDepthAtDist - optDepth);
            relDiff = abs(optDepthAtDist * rcpOptDepth - 1);

            numIter++;

            // Stop when the accuracy goal has been reached.
            // Note that this uses the accuracy of the old value of 't'.
            // The new value of 't' we just computed should be even more accurate.
        } while ((absDiff > EPS_ABS) && (relDiff > EPS_REL) && (numIter < NUM_ITER));

        return t;
    }
```

Since optical depth is a smooth monotonically increasing function of distance, this numerical procedure will converge very quickly (typically, after a couple of iterations). If desired, the cost can be fixed by using an iteration counter to terminate the loop, potentially trading accuracy for consistent performance.

It is worth noting that since the code internally uses a numerical approximation of the Chapman function, it may not always be possible to reach an arbitrary accuracy goal. Using `FLT_EPSILON` results in a high degree of accuracy at the cost of a large number of iterations (typically, 1-10), while 1-2 iterations are sufficient to stay below the relative error level of 0.001.

In fact, the curvature of the planet can be ignored for moderate distances, making the rectangular inverse a relatively efficient and accurate approximation.

## Conclusion

This article has presented several methods for sampling common types of analytic participating media. They are particularly useful for modeling low-frequency variations of density. While planetary atmospheres cannot be sampled analytically, the proposed numerical approach works well in practice. None of these techniques require voxelization, which allows simulation of various volumetric effects at real-time frame rates.

## What's Next?

The second part of the article (published separately) will discuss rendering of spectrally-varying participating media. It is an exciting (but challenging) topic, and I look forward to sharing my findings.

## Acknowledgments

I would like to thank Julian Fong and Sébastien Hillaire for their thoughtful comments and feedback.

<!---

## Handling Spectral Coefficients

While using monochromatic attenuation is acceptable for certain use cases (such as modeling aerosols and fog), generally speaking, we would like to support spectrally-varying coefficients. This means that given a fixed distance, light is going to be attenuated to a different degree depending on the wavelength. This poses a problem for importance sampling, which would have to convert opacity (now a vector) into distance (a scalar).

The simplest solution is to trace one path per wavelength. For instance, we could either densely sample the entire [visible spectrum](https://en.wikipedia.org/wiki/Visible_spectrum) (e.g. 380 to 780 nm with a 10 nm step size resulting in 40 paths), or pick several wavelengths stochastically (distributed either uniformly, or according to the [luminous efficiency function](https://en.wikipedia.org/wiki/Luminosity_function), or opacity spectrum, or the product of both). This brute force approach increases the rendering cost by a factor of \\(n\\) (where \\(n\\) denotes the number of wavelength samples), but it provides a good reference point for comparison.

Another approach suggested during the [Production Volume Rendering](https://graphics.pixar.com/library/ProductionVolumeRendering/paper.pdf) course is to always construct paths using the average attenuation coefficient across the visible spectrum. This solution is rather attractive from the performance point of view, since you only end up tracing a single path. The downside is that it assumes that the resulting radiance distribution is close to monochromatic. This may not be the case for subsurface scattering in skin, for instance, in which case red wavelengths scatter much farther than the rest (and the spectral distribution is rather compact), and using this technique may result in an excessive amount of color noise. On the other hand, using the maximum value of the attenuation coefficient across the spectrum assumes that the distribution is symmetric around the peak, which may not be the case. Intuitively, using some kind of weighted average wavelength probably makes the most sense.

{{< figure src="/img/skew.png">}}

Let's start with understanding the problem we are trying to solve.

The tristimulus values \\(X\\), \\(Y\\) and \\(Z\\) of a pixel centered at the point \\(\bm{x}\\) on the camera sensor can be computed as an integral of incoming spectral radiance \\(L\_{\lambda}\\) over the visible spectrum \\(\Lambda\\), the pixel area \\(A\\) and the hemisphere of directions \\(\Omega\\) weighted by the [normalized color matching function](https://en.wikipedia.org/wiki/CIE_1931_color_space#Color_matching_functions) \\(\bar{c}\\) \\(\big( \bar{x}\\) for \\(X\\), \\(\bar{y}\\) for \\(Y\\), \\(\bar{z}\\) for \\(Z \big)\\) and the sensor response ([pixel filter](https://ieeexplore.ieee.org/document/4061554/)) \\(W\\):

$$ \tag{58} I = C(\bm{x}) = \int\_{\Lambda} \bar{c}(\lambda) \int\_{A} \int\_{\Omega} W(\bm{r}, \bm{\hat{v}}, \lambda) L\_{\lambda}(\bm{x} + \bm{r}, \bm{\hat{v}}, \lambda) d\bm{\hat{v}} dA(\bm{r}) d\lambda. $$

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

In the end, we divided some averages by some other averages. Do we get the correct result on average? Using the formalism introduced in Equation 63,

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

$$ \tag{70} \bm{L}(\bm{x}, \bm{\hat{v}})
    = \int\_{0}^{t\_{max}} \bar{\mu}(\bm{x}, \bm{\hat{v}}, s) \bar{T}(\bm{x}, \bm{\hat{v}}, s) \bm{L_i}(\bm{x} + s \bm{\hat{v}}, \bm{\hat{v}}) ds,
$$

where \\(\bar{T}\\) is transmittance evaluated using the majorant (rather than attenuation) coefficient. Note that the majorant coefficient doesn't actually have to be a constant - it can be any analytic function (e.g. varying with height) serving as the upper bound for the attenuation coefficient.

The incoming radiance term \\(\bm{L_i}\\) is defined as

$$ \tag{71} \begin{aligned}
    \bm{L_i}(\bm{x}, \bm{\hat{v}})
    &= P_a(\bm{x}, \bm{\hat{v}}, s) \bm{w_a}(\bm{x}, \bm{\hat{v}}, s) \bm{L_e}(\bm{x}, \bm{\hat{v}}) \cr
    &+ P_s(\bm{x}, \bm{\hat{v}}, s) \bm{w_s}(\bm{x}, \bm{\hat{v}}, s) \bm{L_s}(\bm{x}, \bm{\hat{v}}) \cr
    &+ P_n(\bm{x}, \bm{\hat{v}}, s) \bm{w_n}(\bm{x}, \bm{\hat{v}}, s) \bm{L}(\bm{x}, \bm{\hat{v}})
\end{aligned}
$$

where \\(\bm{L_e}\\) is emission and \\(\bm{L_s}\\) is defined in the same way as in Equation 13.

Each individual event has a corresponding weight:

$$ \tag{72}
\begin{aligned}
    \bm{w_a}(\bm{x}, \bm{\hat{v}}, s) &= \frac{\bm{\mu_a}(\bm{x}, \bm{\hat{v}}, s)}{\bar{\mu}(\bm{x}, \bm{\hat{v}}, s) P_a(\bm{x}, \bm{\hat{v}}, s)} \cr
    \bm{w_s}(\bm{x}, \bm{\hat{v}}, s) &= \frac{\bm{\mu_s}(\bm{x}, \bm{\hat{v}}, s)}{\bar{\mu}(\bm{x}, \bm{\hat{v}}, s) P_s(\bm{x}, \bm{\hat{v}}, s)} \cr
    \bm{w_n}(\bm{x}, \bm{\hat{v}}, s) &= \frac{\bm{\mu_n}(\bm{x}, \bm{\hat{v}}, s)}{\bar{\mu}(\bm{x}, \bm{\hat{v}}, s) P_n(\bm{x}, \bm{\hat{v}}, s)}
\end{aligned}
$$

The simplest (but [not the only](https://dl.acm.org/citation.cfm?id=3073665)) way to define the collision probabilities is as follows:

$$ \tag{73} \begin{aligned}
    P_a(\bm{x}, \bm{\hat{v}}, s) &= \frac{||\mu_a(\lambda)||\_{\infty}}{||\mu_a(\lambda)||\_{\infty} + ||\mu_s(\lambda)||\_{\infty} + ||\mu_n(\lambda)||\_{\infty}}, \cr
    P_s(\bm{x}, \bm{\hat{v}}, s) &= \frac{||\mu_s(\lambda)||\_{\infty}}{||\mu_a(\lambda)||\_{\infty} + ||\mu_s(\lambda)||\_{\infty} + ||\mu_n(\lambda)||\_{\infty}}, \cr
    P_n(\bm{x}, \bm{\hat{v}}, s) &= \frac{||\mu_n(\lambda)||\_{\infty}}{||\mu_a(\lambda)||\_{\infty} + ||\mu_s(\lambda)||\_{\infty} + ||\mu_n(\lambda)||\_{\infty}},
\end{aligned}
$$

where the position and the direction on the right-hand side are implicit for clarity.

If the medium is known to not be emissive, we can redistribute the absorption probability between \\(P_s\\) and \\(P_n\\):

$$ \tag{74} \begin{aligned}
    P'\_a(\bm{x}, \bm{\hat{v}}, s) &= 0, \cr
    P'\_s(\bm{x}, \bm{\hat{v}}, s) &= \frac{P_s}{P_s + P_n}, \cr
    P'\_n(\bm{x}, \bm{\hat{v}}, s) &= \frac{P_n}{P_s + P_n}.
\end{aligned}
$$

Comparing Equations 13 and 70, we can see that they are indeed very similar (and so is the estimator). As before, the leading term is canceled out after dividing by the PDF, so the only difference is that we must handle several types of events once a collision actually occurs.

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
                // Use Equation 73, or one of the alternatives from the paper.
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
