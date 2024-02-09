---
title: "On Microsurface Transformations"
date: 2024-01-11T13:38:14-08:00
draft: true
---

At EGSR 2022, Atanasov, Koylazov, Dimov, and Wilkie presented a paper titled [Microsurface Transformations](https://onlinelibrary.wiley.com/doi/abs/10.1111/cgf.14590), where the authors discuss linear transformations of microfacet distributions. Since I have been long interested in surface geometry, its representations, and the associated transformations (something I have previously explored in the context of [bump and normal mapping](/post/surface-gradient/)), this work immediately caught my attention. While the subject may seem obscure, the idea of parameterizing surface detail in terms of a (spatially varying) transformation matrix is immensely powerful. It also brings much-needed conceptual clarity by unifying the processing of geometry at all scales using the framework of linear algebra. This is highly desirable if one wishes to model the appearance of deformed objects, with stretching and bending properly affecting fine surface detail (and, thus, the reflectance properties). Perhaps you can imagine other, less conventional applications of microsurface transformations. We shall explore one of them in this article.

<!--more-->

## Microscopic Introduction

Pick an object, perhaps one that sits on your desk or lives in your head. Focus on a tiny fragment of its surface. If the fragment is sufficiently small, or is sufficiently far away, it will look effectively flat (but not necessarily smooth). Therefore, to a negligible degree of error, that fragment may be replaced by a first-order approximation[^1] -- its projection onto the (averaged) tangent plane. In the literature, this projected surface fragment is referred to as the *macrosurface*. It is characterized[^6] by the area $\small A$ and the unit normal vector $\small \bm{n}$. Fixing two (linearly independent, but not necessarily unit or orthogonal) tangent vectors $\small \bm{t_1}$ and $\small \bm{t_2}$ is sufficient to complete the parameterization of the macrosurface.

[^1]: In the sense of a Taylor series expansion.

[^6]: We shall treat all surfaces as one-sided unless stated otherwise.

In contrast, the surface fragment overlaid onto the macrosurface is called[^2] the *microsurface* $\small \mathbb{M^2}$. It is composed of infinitesimal elements called *microfacets*. In the *microfacet theory*, a microsurface is not modeled explicitly, but rather represented by a statistical model.

[^2]: We use the superscript 2 to indicate that the surface is a two-dimensional manifold.

Let $\small \bm{m}$ denote the unit normal vector of the microsurface. Since it may correspond to several distinct points on the microsurface, we must define[^3] $\small dA(\bm{m}) = D(\bm{m}) A d\Omega(\bm{m})$ as the differential area of the portion of the microsurface perpendicular to $\small \bm{m}$, where $\small d\Omega$ is the differential solid angle centered on $\small \bm{m}$, and $\small D$ is the *distribution of normals*[^4] (abbreviated as the *NDF*) associated with the microsurface. The domain of this function, along with the microsurface normals themselves, is typically restricted to the unit hemisphere $\small \mathbb{H^2}$ (with $\small \bm{n}$ serving as the zenith direction), which implies that $\small \mathbb{M^2}$ must be a height field. However, this restriction is not strictly necessary; we shall demonstrate that by letting the microsurface normals potentially cover the entire unit sphere $\small \mathbb{S^2}$.

[^3]: If the microsurface is convex, one can interpret $\small DA$ as a Jacobian of the transformation from the surface to the unit hemisphere.

[^4]: Not to be confused with the normal distribution (a.k.a. the Gaussian distribution) in probability theory.

A valid microsurface and, thus, a valid NDF, must obey the *signed projected area* constraint

$$ \tag{1a}
\begin{aligned}
	&\bm{v} \cdot \bm{n} A
	\cr = \space
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}} \bm{m}(\bm{p}) dA(\bm{p})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} dA(\bm{m})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) A d\Omega(\bm{m})
\end{aligned}
$$

for all $\small \bm{v}$. Expressed this way, it is clear that the constraint is geometric (e.i., coordinate-independent, basis-independent) in nature:

$$ \tag{1b}
	\bm{n} = \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) d\Omega(\bm{m}).
$$

Eqn. 1 tells us a few things. Geometrically, it says that the signed projected areas of the microsurface and the macrosurface must coincide in any given direction. Furthermore, by substituting a constant NDF (or from the definition), we can see that it is measured in units of reciprocal solid angle.

In the special case of $\small \bm{v} = \bm{n}$, we obtain

$$ \tag{1c}
	1 = \int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
$$

In order for $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$ to be a valid probability density function, it must be non-negative for all $\small \bm{m}$. That is the case only if $\small \mathbb{M^2}$ is a height field, since this ensures that $\small (\bm{n} \cdot \bm{m}) \ge 0$.

One of the simplest examples of a valid microsurface is a box[^5] (with the flipped bottom face playing the role of the macrosurface). It is instructive to analyze its two-dimensional counterpart -- a rectangle.

[^5]: Using the tools of calculus, we can decompose an arbitrary surface into a (possibly infinite) number of (sufficiently small) boxes.

*Linearity* of Eqn. 1 allows us to consider the individual microfacets separately and to sum up their contributions. As we apply Eqn. 1 to the box, observe that the signed projected areas of the opposite faces cancel each other, leaving just the top (that is, of course, equivalent to the bottom). Another consequence is that a linearly transformed surface (a parallelepiped, in our case) still results in a valid combination of the microsurface and the macrosurface. We can see why that is the case by picturing Eqn. 1 geometrically and (passively) transforming the coordinate axes rather than (actively transforming) the surface itself [^51]. Of course, while the surface lines remain unchanged, the signed projected areas (and, thus, the values of the NDF) are no longer the same.

[^51]: This requires the transformation to be invertible. If the transformation were nonlinear, the transformed axes would vary from point to point, forming a vector field.

Clearly, a microsurface does not have to be smooth (e.i. continuously differentiable); however, this ensures the continuity of the NDF, which is desirable, unless the goal is to model a flat surface.

Erasing any part of the box leads to a signed projected area mismatch for certain angles. Therefore, the constraint may seem to imply that the microsurface must be continuous, but that is not the case. The issue lies in the *translation invariance*, which simply means that the projected area of an object is independent of its location. This property may seem innocuous at first, but, coupled with the linearity, it spells disaster: we may freely translate different microfacets in different directions without affecting the value of the integral.

Since the signed projected area is an incomplete description of a real surface (as opposed to a microfacet soup or a salad[^52]), we may additionally specify its *visible projected area*

[^52]: In technical terms, the surface may have a very short auto-correlation distance.

$$ \tag{2a}
\begin{aligned}
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) dA(\bm{m})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) D(\bm{m}) A d\Omega(\bm{m}),
\end{aligned}
$$

which obviously depends on where the microfacets are located relative to one another. It is defined in terms of the dimensionless *masking function* $\small G_1(\bm{v}, \bm{m})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along $\small \bm{v}$. In other words, it is the *average visibility* (along $\small \bm{v}$) of the microfacets with the normal $\small \bm{m}$. The masking function is closely related to the binary *visibility function* $\small V(\bm{v}, \bm{p})$ that outputs 0 if the point $\small \bm{p}$ is occluded along $\small \bm{v}$, and 1 otherwise. Both functions take *self-occlusion* into account: $\small V = G_1 = 0$ if $\small (\bm{v} \cdot \bm{m}) \le 0$. This subtle point helps us emphasize the geometric (e.i., coordinate-independent, basis-independent) nature of Eqn. 2.

The masking (and the visibility) function possesses an important property called *stretch invariance*, or, more generally, *invariance under linear transformations*. We have already seen that, in the context of the microfacet theory, a linearly transformed surface remains valid; however, unlike the NDF, the masking function is dimensionless: it only encodes visibility, and so it remains unaffected by a transformation of the coordinate axes. Of course, this kind of transformation must be applied to everything, including the view direction.

Eqn. 1 and 2 are closely related. For a valid microsurface, the values of the integrals are the same for $\small \bm{v} = \bm{n}$:

$$ \tag{2b}
\begin{aligned}
	1 =
	&\int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	(\bm{n} \cdot \bm{m}) G_1(\bm{n}, \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
\end{aligned}
$$

In general, *the visible projected area is greater or equal to the signed projected area*. This inequality is a consequence of self-occlusion, which eliminates the (formerly negative) contribution of back-facing microfacets. The two types of projected areas coincide only if the view angle is sufficiently steep, or the microsurface -- sufficiently short, so that the latter does not extend outside the volume swept by the macrosuface translated along the view direction. This leads to one of the key assumptions of the microfacet theory: *a microsurface must be infinitesimally tall*. This limitation manifests itself at grazing angles, and should be familiar to those who have practical experience with bump and normal mapping. In addition, since the signed projected areas of the microsurface and the macrosurface coincide, we can only allow the view angles that obey $\small (\bm{v} \cdot \bm{n}) \ge 0$. In other words, *a microsurface must be facing the observer*. Otherwise, we must must reverse the signs of both $\small \bm{n}$ and $\small \bm{m}$, which will give rise to a microsurface with different statistics. More specifically, while $D(-\bm{m})$ will give the correct number, the visibility will be different, which is particularly evident if you take a box and flip it upside down. For a tiling surface (such as a sawtooth), reversing the sign of $\small \bm{v}$, so that it points in the same hemisphere as $\small \bm{n}$, produces a reasonable approximation. This ensures that self-occlusion is properly accounted for.

With the assumption (that all projected surface areas are the same) in place, we can combine Eqn. 1a and 2a into

$$ \tag{3a}
	1 =
	\int_{\bm{m} \in \mathbb{S^2}} D_{vis}(\bm{v}, \bm{m}) d\Omega(\bm{m}),
$$

where

$$ \tag{3b}
	D_{vis}(\bm{v}, \bm{m}) =
	\frac{(\bm{v} \cdot \bm{m})}{(\bm{v} \cdot \bm{n})} G_1(\bm{v}, \bm{m}) D(\bm{m})
$$

is called the *distribution of visible normals*[^7] (abbreviated as the *VNDF*). Its meaning becomes clear in the context of Eqn. 2a: $\small ((\bm{v} \cdot \bm{m})/(\bm{v} \cdot \bm{n}))^{-1} D_{vis}(\bm{v}, \bm{m}) d\Omega(\bm{m})$ is the differential area of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along $\small \bm{v}$. Like $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$, the VNDF is a valid probability density function; and because it is always non-negative, it is not restricted to height fields.

[^7]: After comparing Eqn. 1c with 3a and taking the definitions into account, it would be more natural to simply let $\small D_{vis} = G_1 D$. Nevertheless, we stick with Eqn. 3b to conform to the existing body of literature.

In practice, one usually does not create (nor is provided) a microsurface, but specifies the statistical distributions directly instead. In fact, one generally goes one step further: since the NDF and the masking function are not independent (they must satisfy Eqn. 3), a reasonable course of action is to specify just one and derive the other. Which distribution should we choose as the starting point? The answer is clear from the definitions: $\small G_1$ is built on top of $\small D$, and its role is to endow the latter with the visibility data.

Thus, the common approach is "NDF first". Unfortunately, as we have already seen, such a surface description is incomplete. This forces us to introduce additional assumptions in the form of a *microsurface profile*. Arguably, the simplest assumption is the *normal-masking independence* introduced by Smith:

$$ \tag{99a}
	G_1^S(\bm{v}, \bm{m}) = \Theta(\bm{v} \cdot \bm{m}) W(\bm{v}),
$$

where

$$ \tag{99b}
	\Theta(x) =
	\begin{cases}
	   1 &\text{if } x \ge 0, \cr
	   0 &\text{otherwise},
	\end{cases}
$$

is the *Heaviside step function* which is used to model self-occlusion. As the name implies, the remaining masking term $\small W$ is independent of the microsurface normal $\small \bm{m}$.

In reality, visibility has a more complicated dependence on the surface normal. For instance, in the case of a height field, for shallow viewing angles, vertical microfacets are more likely to be occluded compared to horizonal ones. Fortunately, the issue is not very apparent unless one examines a very rough surface at a grazing angle, where, as we have already seen, the foundations of the microfacet theory itself are on a shaky ground.

Substitution of Eqn. 99a into 3b and 3a yields a ratio of projected areas:

$$ \tag{99c}
	W(\bm{v})
	= \frac{(\bm{v} \cdot \bm{n})}{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})}
	= \frac{(\bm{v} \cdot \bm{n}) A}{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m})}.
$$

Eqn. 99c confirms that that Smith's microsurface profile only models one type of occlusion -- self-occlusion, and that the connection between a microfacet and its neighborhood is entirely missing. Therefore, we may once again end up with a microfacet soup or a salad instead of a continuous surface. The only way to solve this problem is by using a more sophisticated microsurface profile.

A VNDF can be used to construct a *bidirectional scattering distribution function* $\small f_s$ (also known as a *BSDF*). By definition, it is a ratio of the differential outgoing radiance to the differential incident irradiance, the latter being the product of the incident radiance and the projected differential solid angle $\small d\Omega_n(\bm{l}) = \vert \bm{n} \cdot \bm{l} \vert d\Omega(\bm{l})$:

$$ \tag{4a}
	f_s(\bm{v}, \bm{n}, \bm{l}) =
	\frac{dL(\bm{v})}{dE_n(\bm{l})} =
	\frac{dL(\bm{v})}{L(\bm{l}) d\Omega_n(\bm{l})}.
$$

In other words, the outgoing radiance is a weighted average of the incident radiance across the unit sphere:

$$ \tag{4b}
	L(\bm{v}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) L(\bm{l}) d\Omega_n(\bm{l}).
$$

In order for a BSDF to be physically meaningful, it must satisfy three properties[^8]:

[^8]: Kinetic energy of the photon is only conserved if the integral is equal to 1. Otherwise, by definition, the collision is called inelastic. The momentum and the total energy of the system are always conserved.

$$ \tag{4c}
\begin{aligned}
	\textit{non-negativity: }
	&f_s \ge 0;
	\cr
	\textit{reciprocity: }
	&\frac{f_s(\bm{v}, \bm{n}, \bm{l})}{f_s(\bm{l}, \bm{n}, \bm{v})} = \frac{\eta_v^2}{\eta_l^2};
	\cr
	\textit{energy conservation: }
	&\int_{\bm{v} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{v}) \le 1;
\end{aligned}
$$

where $\small \eta_v$ and $\small \eta_l$ are the real[^9] indices of refraction (the *IORs*) associated with the directions of exitance $\small (\bm{v})$ and incidence $\small (\bm{l})$, respectively. In particular, reciprocity is a direct consequence of the law of refraction (also valid for reflection)

$$ \tag{5a}
	\eta_v \Vert \bm{v} \times \bm{n} \Vert = \eta_l \Vert \bm{l} \times \bm{n} \Vert
$$

which leads to the compression of projected solid angles

$$ \tag{5b}
	\eta_v^2 d\Omega_n(\bm{v}) = \eta_l^2 d\Omega_n(\bm{l})
$$

and, thus, a change in energy density (provided $\small \eta_v \neq \eta_l$). Keep in mind that, in general, the relationship between the ordinary solid angles is not as simple:

$$ \tag{5c}
	\eta_v^2 d\Omega(\bm{v}) \neq \eta_l^2 d\Omega(\bm{l}).
$$

[^9]: We will not consider absorptive or magnetic media in this article. A discussion involving the physics of conductors (and certain dielectrics) would quickly become quite involved and take us too far afield, away from our original topic of microsurface transformations.

This is a very good reason to utilize the projected solid angle measure. Failure to consistently do so often results in complicated expressions that are difficult to interpret and prone to errors.

The properties of a valid BSDF are by no means obvious. In particular, making it reciprocal may seem like a non-trivial task. Typically, that is done by splitting the BSDF in two components: reflection (the *BRDF*) and transmission (the *BTDF*). The reflection component is always *symmetric*: since $\small \bm{v}$ and $\small \bm{l}$ always point away from the surface, $\small \eta_v = \eta_l$, which results in $\small f_r(\bm{v}, \bm{n}, \bm{l}) = f_r(\bm{l}, \bm{n}, \bm{v})$.


In order to make things clear, we shall illustrate these properties using a concrete example. Consider a perfectly smooth, planar surface. Its BSDF (often referred to as the *perfect specular* BSDF) can be expressed in terms of the *Dirac delta "function"* $\small \delta$ defined as a projected solid angle measure by the equation

$$ \tag{6}
	f(\bm{r}) =
	\int_{\bm{l} \in \mathbb{S^2}} f(\bm{l}) \delta_{\Omega_n}(\bm{l} - \bm{r}) d\Omega_n(\bm{l})
$$

valid for any function $\small f: \mathbb{S^2} \to \mathbb{R}$.

Now, according to the law of reflection, the angles of incidence and reflection must be the same:

$$ \tag{7a}
	\sin{\theta_i} = \Vert \bm{l} \times \bm{n} \Vert = \Vert \bm{v} \times \bm{n} \Vert,
	\quad
	\cos{\theta_i} = \vert \bm{l} \cdot \bm{n} \vert = \vert \bm{v} \cdot \bm{n} \vert.
$$

Furthermore, we need to distinguish between the IORs associated with the exterior (above the surface) and the interior (below the surface):

$$ \tag{7b}
	\eta_i = \eta_l = \eta_v,
	\quad
	\eta_t =
	\begin{cases}
	   \eta_{int} &\text{if } \eta_i = \eta_{ext}, \cr
	   \eta_{ext} &\text{otherwise}.
	\end{cases}
$$

Using this formalism, the reflection component of the BSDF can be written as

$$ \tag{7c}
	f_r(\bm{v}, \bm{n}, \bm{l}) =
	F(\theta_i, \eta_i/\eta_t)
	\delta_{\Omega_n}\negmedspace\left( \bm{n} - \frac{\bm{v} + \bm{l}}{(\bm{v} + \bm{l}) \cdot \bm{n}} \right),
$$

where $\small 0 \le F \le 1$ is the dimensionless *Fresnel reflectance*. This BRDF is non-negative, symmetric, and energy-conserving, which can be verified by substituting Eqn. 7c into 4c. In particular,

$$ \tag{7d}
	\int_{\bm{v} \in \mathbb{S^2}}
	f_r(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{v}) =
	F(\theta_i, \eta_i/\eta_t) \le 1.
$$

The trigonometric form of the Fresnel term is

$$ \tag{8a}
	F(\theta_i, \eta_i/\eta_t)
	= (1 - \alpha) R_s(\theta_i, \eta_i/\eta_t) + \alpha R_p(\theta_i, \eta_i/\eta_t),
$$

where the value of $\small \alpha$ depends on the state of polarization of the incident light: $\small \alpha = 0$ corresponds to the s-polarized light, $\small \alpha = 1$ -- to the p-polarized light, and $\small \alpha = 1/2$ -- to the unpolarized (natural) light.

The two mutually perpendicular components are defined as follows:

$$ \tag{8b}
\begin{aligned}
	R_s(\theta_i, \eta_i/\eta_t)
	&= \left| \frac{\cos{\theta_i} / \cos{\theta_t} - \sin{\theta_t} / \sin{\theta_i}}{\cos{\theta_i} / \cos{\theta_t} + \sin{\theta_t} / \sin{\theta_i}} \right|^2
	= \left| \frac{\sin(2 \theta_i) - \sin(2 \theta_t)}{\sin(2 \theta_i) + \sin(2 \theta_t)} \right|^2,
	\cr
	R_p(\theta_i, \eta_i/\eta_t)
	&= \left| \frac{\cos{\theta_t} / \cos{\theta_i} - \sin{\theta_t} / \sin{\theta_i}}{\cos{\theta_t} / \cos{\theta_i} + \sin{\theta_t} / \sin{\theta_i}} \right|^2
	= \left| \frac{\sin(\theta_i - \theta_t)}{\sin(\theta_i + \theta_t)} \right|^2.
\end{aligned}
$$

The angle of refraction (or transmission) $\small \theta_t$ can be determined from the angle of incidence $\small \theta_i$ and the relative IOR $\small \eta_i/\eta_t$ using the (already familiar) law of refraction and basic trigonometry:

$$ \tag{8c}
	\sin{\theta_t} = \frac{\eta_i}{\eta_t} \sin{\theta_i},
	\quad
	\cos{\theta_t} = \sqrt{1 - \sin^2{\theta_t} }.
$$

The definition of the transmission component of the perfect specular BSDF is marginally more complicated. Let

$$ \tag{9a}
\begin{aligned}
	\sin{\theta_i} &= \Vert \bm{l} \times \bm{n} \Vert,
	&
	\cos{\theta_i} &= \vert \bm{l} \cdot \bm{n} \vert,
	\cr
	\sin{\theta_t} &= \Vert \bm{v} \times \bm{n} \Vert,
	&
	\cos{\theta_t} &= \vert \bm{v} \cdot \bm{n} \vert.
\end{aligned}
$$

By extension, we shall associate

$$ \tag{9b}
	\eta_i = \eta_l,
	\quad
	\eta_t = \eta_v.
$$

Taking reciprocity and energy conservation into account, the perfect specular BTDF can then be expressed as

$$ \tag{9c}
	f_t(\bm{v}, \bm{n}, \bm{l}) =
	\frac{\eta_t^2}{\eta_i^2} \big( 1 - F(\theta_i, \eta_i/\eta_t) \big) \delta_{\Omega_n}\big( \eta_v (\bm{v} \times \bm{n}) + \eta_l (\bm{l} \times \bm{n}) \big).
$$

Is Eqn. 9c consistent with Eqn. 4c? The Dirac term is clearly symmetric. Geometrically, the Fresnel term is invariant under the exchange of $\small \theta_i$ and $\small \theta_t$ according to Eqn. 8b. Intuitively, it only depends on the path taken by light, and is unaffected by a reversal of its direction. Finally, the first term is responsible for the projected solid angle compression, which can be readily verified by placing the surface inside a *white furnace* (e.i. setting $\small L(\bm{l}) = 1$ for all $\small \bm{l}$) and evaluating Eqn. 4b.

Is this BTDF reciprocal? Naive substitution of Eqn. 9c into 4c leads to

$$ \tag{9d}
	\frac{f_t(\bm{v}, \bm{n}, \bm{l})}{f_t(\bm{l}, \bm{n}, \bm{v})}
	= \frac{\eta_v^4}{\eta_l^4}
	\frac{
		\delta_{\Omega_n}\big( \eta_v (\bm{v} \times \bm{n}) + \eta_l (\bm{l} \times \bm{n}) \big)
	}{
		\delta_{\Omega_n}\big( \eta_l (\bm{l} \times \bm{n}) + \eta_v (\bm{v} \times \bm{n}) \big)
	}.
$$

Unfortunately, Eqn. 9d fails to make it apparent that these two Dirac delta "functions" are not the same: they are used to measure two different projected solid angles, and their ratio is precisely the same as the missing factor of $\small \eta_l^2 / \eta_v^2$. To see why that is the case, we must recall that Eqn. 7c and 9c are only valid in the context of Eqn. 6, which is an integral over the domain of directions of incidence. If we use the reciprocal of a BSDF, the directions are interchanged, and Eqn. 6 must also be modified accordingly.

A better way to verify reciprocity is by combining Eqn. 4c, 5b, and 6:

$$ \tag{10}
	\int_{\bm{v'} \in \mathbb{S^2}} f_s(\bm{v'}, \bm{n}, \bm{l}) d\Omega_n(\bm{v'})
	= \int_{\bm{l'} \in \mathbb{S^2}} f_s(\bm{l'}, \bm{n}, \bm{v}) d\Omega_n(\bm{l'}).
$$

If $\small \bm{v}$, $\small \bm{n}$, and $\small \bm{l}$ do not define a valid light path, both integrals vanish. Otherwise, substitution of Eqn. 9c results in identical values. In particular,

$$ \tag{9e}
\begin{aligned}
	\int_{\bm{l} \in \mathbb{S^2}} f_t(\bm{l}, \bm{n}, \bm{v}) d\Omega_n(\bm{l})
	=
	\int_{\bm{l} \in \mathbb{S^2}} \frac{\eta_{l}^2}{\eta_v^2} f_t(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{l}) =
	\big( 1 - F(\theta_i, \eta_i/\eta_t) \big) \le 1.
\end{aligned}
$$

Since Eqn. 7d and 9e sum up to 1, this demonstrates that the entire BSDF is energy conserving.

*Comment: are Eqn. 17, 18, and 21 in Walter's paper correct? $\small \eta_i^2$ appears to be missing in the denominator.*

We can extend this plain BSDF to a microfacet model of a rough surface by treating the latter as locally (rather than globally) planar and smooth. The amount of radiance scattered by the microsurface can then be expressed as a weighted average of the contributions of its visible microfacets. In order to proceed, we must recall that, by definition, the radiance is the amount of power moving in a certain direction, per unit solid angle associated with this direction, per unit area perpendicular to this direction. If the source of light is very small (or very far away), it will appear point-like, and the variation of the view direction across its surface can be safely neglected. The same cannot be said for the visible projected area, which must be properly normalized (or we would calculate the intensity instead of the radiance):

$$ \tag{11a}
	L(\bm{v}) =
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) L(\bm{v}, \bm{p})
	V(\bm{v}, \bm{p}) dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}
$$

According to the microfacet theory, the factor in the denominator -- the visible projected area of the microsurface -- is identical to the signed projected area of the macrosurface (see Eqn. 1-3):

$$ \tag{11b}
\begin{aligned}
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) D(\bm{m}) A d\Omega(\bm{m})
	\cr = \space
	&\bm{v} \cdot \bm{n} \int_{\bm{m} \in \mathbb{S^2}}
	D_{vis}(\bm{v}, \bm{m}) A d\Omega(\bm{m})
	\cr = \space
	&\bm{v} \cdot \bm{n} A.
\end{aligned}
$$

The general expression of the outgoing radiance term in the numerator of Eqn. 11a is given by the spatially-varying version of Eqn. 4b:

$$ \tag{11c}
	L(\bm{v}, \bm{p}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}, \bm{p}) d\Omega_m(\bm{l}).
$$

Unfortunately, Eqn. 11c is recursive. In our case, this means that light scattered by a microfacet can be used to illuminate another in a process known as multiple scattering. For simplicity (and at the cost of correctness), when building microfacet models, this effect is usually neglected, with only the distant illumination taken into account:

$$ \tag{11d}
	L(\bm{v}, \bm{p}) \approx
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}) V(\bm{l}, \bm{p}) d\Omega_m(\bm{l}).
$$

Eqn. 11a can thus be approximated as

$$ \tag{11e}
	L(\bm{v}) \approx
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) \int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}) V(\bm{l}, \bm{p}) d\Omega_m(\bm{l}) dA(\bm{p})}
	{\bm{v} \cdot \bm{n} A}.
$$

If we substitute the expressions of the perfect specular BSDF given by Eqn. 7c and 9c, the inner integral can be evaluated analytically:

$$ \tag{11f}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	F(\theta_i, \eta_i/\eta_t) L(\bm{i})
	V(\bm{i}, \bm{p}) V(\bm{v}, \bm{p})
	dA(\bm{p})}
	{\bm{v} \cdot \bm{n} A},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_i^2}
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	\big( 1 - F(\theta_i, \eta_i/\eta_t) \big) L(\bm{t})
	V(\bm{t}, \bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}
	{\bm{v} \cdot \bm{n} A},
\end{aligned}
$$

where

$$
	\bm{i} = \bm{R}\big(\bm{v}, \bm{m}(\bm{p})\big) = -\bm{v} + 2 (\bm{v} \cdot \bm{m}) \bm{m}
$$

is the reflected view vector pointing in the direction of the incident light. The associated angle $\small \theta_i$ is similarly defined relative to $\small \bm{m}$ rather than $\small \bm{n}$ (see Eqn. 7a).

Notice that the visibility terms (and the vector area $\small \bm{m} dA$) are the only ones that explicitly depend on the position $\small \bm{p}$. That is because visibility is a global property -- it connects a point to the entire surface.

We can evaluate Eqn. 11f numerically by breaking the microsurface into the individual microfacets and sorting the latter by their orientation. For a fixed view direction $\small \bm{v}$, a group of microfacets with the same normal $\bm{m}$ will also share the values of $\small F$ and $\small L$. Within each group, we shall calculate the total visible area of the microfacets.

The statistical method of evaluation of Eqn. 11f mirrors Eqn. 2a. We must introduce the dimensionless *shadowing-masking function* $\small G_2(\bm{l}, \bm{v}, \bm{m})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along both $\small \bm{v} \text{ and } \bm{l}$. In other words, it is the *average visibility* (along both $\small \bm{l} \text{ and } \bm{v}$) of the microfacets with the normal $\small \bm{m}$:

$$ \tag{12a}
\begin{aligned}
	&\int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{l}, \bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr = \space
	&\int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_2(\bm{l}, \bm{v}, \bm{m}) dA(\bm{m})
	\cr = \space
	&\int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_2(\bm{l}, \bm{v}, \bm{m}) D(\bm{m}) A d\Omega(\bm{m})
\end{aligned}
$$

Once projected onto $\small \bm{l} \text{ or } \bm{v}$, Eqn. 12a also represents visible projected area, except that the visibility is now bidirectional. Similarly, it takes self-occlusion into account: $\small V = G_2 = 0$ if $\small (\bm{l} \cdot \bm{m}) \le 0 \text{ or } \small (\bm{v} \cdot \bm{m}) \le 0$.

In general, a bidirectional average cannot be factored into a product of unidirectional averages:

$$ \tag{12b}
	G_2(\bm{l}, \bm{v}, \bm{m}) \ne G_1(\bm{l}, \bm{m}) G_1(\bm{v}, \bm{m}).
$$

As an example, consider a number of evenly-spaced boxes -- a square wave. For $\small \bm{m} = \bm{n}$, the unidirectional visibility will approach 50% as the view angle increases. Thus, for shallow angles, $\small G_1(\bm{l}, \bm{n}) G_1(\bm{v}, \bm{n}) \approx 1/4$, while $\small G_2(\bm{l}, \bm{v}, \bm{n}) \approx 1/2$. More generally, $\small G_1^2(\bm{v}, \bm{m}) \le G_2(\bm{v}, \bm{v}, \bm{m})$.

TODO: height-correlated?

Substitution of Eqn. 12a into 11f yields

$$ \tag{13a}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m}
	F(\theta_i, \eta_i/\eta_t) L(\bm{i})
	G_2(\bm{i}, \bm{v}, \bm{m})
	D(\bm{m}) d\Omega(\bm{m})}
	{\bm{v} \cdot \bm{n}},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_i^2}
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	\big( 1 - F(\theta_i, \eta_i/\eta_t) \big) L(\bm{t})
	G_2(-\bm{t}, \bm{v}, \bm{m})
	D(\bm{m}) d\Omega(\bm{m})}
	{\bm{v} \cdot \bm{n}}.
\end{aligned}
$$

Note that we had to reverse the direction of the refracted light because the microsurface is one-sided; refer to the discussion below Eqn. 2b for details.

TODO: change of variables?

---

## Acknowledgements

## References and Suggestions for Further Reading

Veach's thesis

Eric's understanding

Walter's ggx

Atanasov's transformations