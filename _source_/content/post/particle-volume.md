---
title: "From Particle to Volume Scattering"
date: 2020-04-10
categories: [ "Optics" ]
tags: [
    "Volume Rendering",
    "Radiometry"
    ]
---

I have [previously](https://zero-radiance.github.io/post/analytic-media/) covered the basics of the volume scattering process. It describes the macroscopic interaction of light and matter, with the latter represented as a distribution of particles. Fundamentally, the volume scattering process arises from the microscopic interaction of light with individual atoms, typically described using [Maxwell's electromagnetic theory](https://www.osapublishing.org/josaa/abstract.cfm?URI=josaa-35-1-163). It is the domain of optics, and, in my experience, the connection with the [radiative transfer equation](https://en.wikipedia.org/wiki/Radiative_transfer#The_equation_of_radiative_transfer) is neither apparent nor easy to discover. The purpose of this blog post is to familiarize the reader with domain-specific terms and ease the adoption of formulas found in the optics literature.

<!--more-->

Consider an incident electromagnetic wave represented by the electric field \\(\bm{E}\\) and the magnetic field \\(\bm{B}\\).
The direction of propagation and the amount of power per unit area carried by the wave at the time \\(t\\) is given by the [instanteneous Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Formulation_in_terms_of_microscopic_fields) \\(\bm{S}\\)

$$ \tag{1} \bm{S}(t) = \varepsilon_0 c^2 \Big( \bm{E}(t) \times \bm{B}(t) \Big), $$

where \\(\varepsilon_0\\) is the [vacuum permittivity](https://en.wikipedia.org/wiki/Permittivity) of the medium and \\(c\\) is the speed of light.

Since light photon waves oscillate very rapidly, we are interested in the [time-averaged Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector#Time-averaged_Poynting_vector) \\(\langle \bm{S} \rangle\\)

$$ \tag{2} \langle \bm{S} \rangle = \frac{1}{T} \int\_{0}^{T} \bm{S}(t) dt, $$

which can be used to define the [spectral irradiance](https://en.wikipedia.org/wiki/Irradiance#Spectral_irradiance) \\(E\\)

$$ \tag{3} E = \Vert \langle \bm{S} \rangle \Vert. $$

For a [linearly-polarized](https://en.wikipedia.org/wiki/Linear_polarization) [plane wave](https://en.wikipedia.org/wiki/Plane_wave), the expression is particularly [simple](https://en.wikipedia.org/wiki/Irradiance#Property):

$$ \tag{4} E = \frac{1}{2} \varepsilon_0 \eta c E_0^2, $$

where \\(\eta\\) is the [index of refraction](https://en.wikipedia.org/wiki/Refractive_index) of the surrounding medium. The same equation holds for [unpolarized light](https://en.wikipedia.org/wiki/Polarization_(waves)#Unpolarized_and_partially_polarized_light) where the energy is distributed equally between the perpendicular and the parallel components.

The interaction of light with an individual particle is described in terms of the [differential scattering cross section](http://glossary.ametsoc.org/wiki/Differential_(scattering)_cross_section) \\(\sigma_s'\\). It is defined as the ratio of the (far-field) scattered [spectral intensity](https://en.wikipedia.org/wiki/Radiant_intensity#Spectral_intensity) \\(I_s\\) for a given direction \\(\omega_s\\) to the incident spectral irradiance \\(E_i\\):

$$ \tag{5} \sigma_s'(\omega_i, \omega_s) = \frac{d \sigma_s}{d \omega_s} = \frac{I_s}{E_i}. $$

Note the implicit dependence on the orientation of the particle with respect to the incident wave - it comes into play if the particle is anisotropic.

We can obtain the scattering cross section \\(\sigma_s\\) by integrating over \\(4 \pi\\) steradians:

$$ \tag{6} \sigma_s(\omega_i) = \int\_{4 \pi} \sigma_s' d \omega_s = \frac{\int\_{4 \pi} I_s d \omega_s}{E_i} = \frac{\Phi_s}{E_i}, $$

which is just the ratio of the scattered [spectral flux](https://en.wikipedia.org/wiki/Radiant_flux#Spectral_flux) \\(\Phi_s\\) to the incident spectral irradiance \\(E_i\\).

The [attenuation (or extinction) cross section](http://glossary.ametsoc.org/wiki/Extinction_cross_section) \\(\sigma_t\\) relates the total spectral flux \\(\Phi_t\\) scattered and absorbed by the particle to the incident spectral irradiance \\(E_i\\):

$$ \tag{7} \sigma_t(\omega_i) = \sigma_s + \sigma_a = \frac{\Phi_s}{E_i} + \frac{\Phi_a}{E_i} = \frac{\Phi_t}{E_i}. $$

The optical cross sections \\(\sigma_x\\) are related to the geometric cross section \\(\sigma_g\\) by the [efficiencies](https://www.osapublishing.org/josaa/abstract.cfm?URI=josaa-35-1-163) \\(Q_x\\):

$$ \tag{8}
    Q_a = \frac{\sigma_a}{\sigma_g} = \frac{\Phi_a}{\Phi_i}, \qquad
    Q_s = \frac{\sigma_s}{\sigma_g} = \frac{\Phi_s}{\Phi_i}, \qquad
    Q_t = \frac{\sigma_t}{\sigma_g} = \frac{\Phi_t}{\Phi_i}. $$

Note that the value of extinction efficiency can exceed 1. This phenomenon is called the [extinction paradox](https://digitalcommons.unl.edu/cgi/viewcontent.cgi?article=1109&context=usarmyresearch).

The angular distribution of scattered light is described by the [phase function](http://glossary.ametsoc.org/wiki/Phase_function) \\(f_p\\):

$$ \tag{9} f_p(\omega_i, \omega_s) = \frac{I_s}{\frac{1}{4 \pi} \int\_{4 \pi} I_s d \omega_s}
                                   = \frac{I_s}{\frac{1}{4 \pi} \Phi_s}. $$

It is the ratio of the energy per unit solid angle scattered in a given direction \\(\omega_s\\) to the average energy per unit solid angle scattered in all directions. Again, there is an implicit dependence on the orientation of the particle with respect to the incident wave. Note that  the integral of the phase function over \\(4 \pi\\) steradians equals \\(4 \pi\\), which appears to be a common [convention](http://glossary.ametsoc.org/wiki/Phase_function) in optics.

Let's compute the product of the scattering cross section and the phase function:

$$ \tag{10} \sigma_s(\omega_i) f_p(\omega_i, \omega_s) = \frac{\Phi_s}{E_i} \frac{I_s}{\frac{1}{4 \pi} \Phi_s} = \frac{I_s}{\frac{1}{4 \pi} E_i}. $$

The spectral flux cancels out, and we find the relation with the differential scattering cross section:

$$ \tag{11} \sigma_s'(\omega_i, \omega_s) = \sigma_s \frac{f_p}{4 \pi}. $$

Now, let's see how we can extend the theory to scattering from \\(N\\) particles. When a particle interacts with light, it experiences a [periodic perturbation of the electron cloud](http://plaza.ufl.edu/dwhahn/Rayleigh%20and%20Mie%20Light%20Scattering.pdf) which turns it into a source of radiation. Assuming [elastic scattering](https://en.wikipedia.org/wiki/Elastic_scattering), this radiation will have the same frequency as the incident light. Assuming  that all the particles are identical (including their orientation), they will radiate energy in exactly the same way.

The resulting electromagnetic waves will [combine](https://en.wikipedia.org/wiki/Superposition_principle#Wave_superposition) at the detector, experiencing both constructive and destructive [interference](https://en.wikipedia.org/wiki/Superposition_principle#Wave_interference). Since the particles are identical, the only differentiating factor is their position, which means that the scattered waves will have different [phases](https://en.wikipedia.org/wiki/Phase_(waves)). If these particles are randomly distributed in a small region of space, so are their phases, and [it can be shown](https://www.nbi.dk/~ogendal/personal/lho/lightscattering_theory_and_practice.pdf) that the *mean* energy carried by the combined wave increases by a factor of \\(N\\).

Therefore, we can rewrite the Equation 5 for \\(N\\) particles as

$$ \tag{12} I_s = N \sigma_s \frac{f_p}{4 \pi} E_i. $$

It is worth noting that since the size of the geometric cross-section \\(\sigma_g\\) increases by a factor of \\(N\\), so does the incident spectral flux \\(\Phi_i\\).

"Wait a minute", you may object. "Shouldn't we also consider the effect of scattered waves on the particles themselves?" And, in general, indeed, we should. This is a [many-body problem](https://en.wikipedia.org/wiki/Many-body_problem), and it is extremely challenging to solve exactly. In a low-density dielectric (a gas), the interaction between the particles is typically neglected. For dense dielectrics, one way of tackling the problem is to introduce the [electronic polarization vector](https://www.feynmanlectures.caltech.edu/II_11.html) - [electric dipole moment](https://en.wikipedia.org/wiki/Electric_dipole_moment) per unit volume - which can be used to define the [local electric field](https://www.feynmanlectures.caltech.edu/II_32.html).

Finally, we can extend the Equation 12 to take the volume \\(dV\\) occupied by particles into account:

$$ \tag{13} \frac{d}{dV} I_s = \frac{d}{dV} N \sigma_s \frac{f_p}{4 \pi} E_i. $$

Assuming that all the particles are identical (including their orientation), the phase function and the scattering cross section can be taken outside the derivative:

$$ \tag{14} \frac{d}{dV} I_s = \frac{f_p}{4 \pi} \sigma_s \frac{d}{dV} N E_i. $$

And, for a small volume \\(dV\\), the incident spectral irradiance \\(E_i\\) can be approximated as constant:

$$ \tag{15} \frac{d}{dV} I_s = \frac{f_p}{4 \pi} \sigma_s \frac{dN}{dV} E_i. $$

\\(dN/dV\\) is the definition of the [number density](https://en.wikipedia.org/wiki/Number_density) \\(n\\),
and can be used to define the [scattering coefficient](https://zero-radiance.github.io/post/analytic-media/) \\(\mu_s\\):

$$ \tag{16} \mu_s = n \sigma_s. $$

Equation 15 then becomes

$$ \tag{17} \frac{d}{dV} I_s = \mu_s \frac{f_p}{4 \pi} E_i. $$

If we orient the direction of propagation of incident light along the \\(z\\) axis, we can write

$$ \tag{18} \frac{d^2 I_s}{dA dz} = \mu_s \frac{f_p}{4 \pi} E_i. $$

This allows us to find the amount of scattered [spectral radiance](https://en.wikipedia.org/wiki/Radiance#Spectral_radiance) \\(L_s\\)

$$ \tag{19} d L_s = \mu_s \frac{f_p}{4 \pi} E_i dz. $$

Finally, we can express the incident spectral irradiance \\(E_i\\) in terms of spectral radiance:

$$ \tag{19} L_s = \int\_{4 \pi} \mu_s \frac{f_p}{4 \pi} L_i d \omega_i dz. $$