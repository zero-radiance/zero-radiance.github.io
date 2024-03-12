---
title: "On Microsurface Transformations"
date: 2024-01-11T13:38:14-08:00
draft: true
---

At EGSR 2022, Atanasov, Koylazov, Dimov, and Wilkie presented a paper titled [Microsurface Transformations](https://onlinelibrary.wiley.com/doi/abs/10.1111/cgf.14590), where the authors discuss linear transformations of microfacet distributions. Since I have been long interested in surface geometry, its representations, and the associated transformations (something I have previously explored in the context of [bump and normal mapping](/post/surface-gradient/)), this work immediately caught my attention. While the subject may seem obscure, the idea of parameterizing surface detail in terms of a (spatially varying) transformation matrix is immensely powerful. It also brings much-needed conceptual clarity by unifying the processing of geometry at all scales using the framework of linear algebra. This is highly desirable if one wishes to model the appearance of deformed objects, with stretching and bending properly affecting fine surface detail (and, thus, the reflectance properties). Perhaps you can imagine other, less conventional applications of microsurface transformations. We shall explore one of them in this article.

<!--more-->

## Construction of a Microfacet Specular BSDF: A Geometric Approach

### Microsurface and Macrosurface

Pick an object, perhaps one that sits on your desk or lives in your head. Focus on a tiny fragment of its surface. If the fragment is sufficiently small, or is sufficiently far away, it will look effectively flat (but not necessarily smooth). Therefore, to a negligible degree of error, that fragment may be replaced by a first-order approximation[^1] -- its orthogonal projection onto the (averaged) tangent plane (see Fig. 1). In the literature, this projected surface is referred to as the *macrosurface*. It is characterized by the area $\small A$ and the unit normal vector[^6] $\small \bm{n}$. Fixing two (linearly independent, but not necessarily unit or orthogonal) tangent vectors is sufficient to complete the parameterization of the macrosurface.

[^1]: In the sense of a Taylor series expansion.

[^6]: We shall treat all surfaces as one-sided unless stated otherwise.

{{< figure src="/img/mst/fig1.png" >}}

In contrast, the surface fragment overlaid onto the macrosurface is called[^2] the *microsurface* $\small \mathbb{M^2}$. It is composed of infinitesimal elements called *microfacets*. In the *microfacet theory*, a microsurface is not modeled explicitly, but rather represented by a statistical model.

[^2]: We use the superscript 2 to indicate that a surface is a two-dimensional manifold.

### Distribution of Normals

Let $\small \bm{m}$ denote the unit normal vector of the microsurface. Since it may correspond to several distinct points on the microsurface, we must define[^3] $\small dA(\bm{m}) = D(\bm{m}) A d\Omega(\bm{m})$ as the differential area of the portion of the microsurface perpendicular to $\small \bm{m}$, where $\small d\Omega$ is the differential solid angle centered on $\small \bm{m}$, and $\small D$ is the *distribution of normals*[^4] (abbreviated as the *NDF*) associated with the microsurface. The domain of this function, along with the microsurface normals themselves, is typically restricted to the unit hemisphere $\small \mathbb{H^2}$ (with $\small \bm{n}$ pointing at the zenith), which implies that $\small \mathbb{M^2}$ must be a height field. However, this restriction is not strictly necessary; we shall demonstrate that by letting the microsurface normals potentially cover the entire unit sphere $\small \mathbb{S^2}$.

[^3]: If the microsurface is convex, one can interpret $\small DA$ as a Jacobian of the transformation from the surface to the unit hemisphere.

[^4]: Not to be confused with the normal distribution (a.k.a. the Gaussian distribution) in probability theory.

A valid microsurface and, thus, a valid NDF, must obey the *signed (orthogonally) projected area* constraint

$$ \tag{1a}
\begin{aligned}
	&\bm{v} \cdot \bm{n} A
	\cr =
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}} \bm{m}(\bm{p}) dA(\bm{p})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} dA(\bm{m})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) A d\Omega(\bm{m})
\end{aligned}
$$

for all $\small \bm{v}$. Expressed this way, it is clear that the constraint is geometric (e.i., coordinate-independent, basis-independent) in nature:

$$ \tag{1b}
	\bm{n} = \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) d\Omega(\bm{m}).
$$

Since the right-hand side of Eqn. 1b is independent of $\small A \text{ and } \bm{n}$, this integral serves as the formal definition of the macrosurface.

Eqn. 1 tells us a few things. Geometrically, it says that the signed projected areas of the microsurface and the macrosurface must coincide in any given direction. Furthermore, by substituting a constant NDF (or, from the definition), we can see that it is measured in units of reciprocal solid angle.

In the special case of $\small \bm{v} = \bm{n}$, we obtain

$$ \tag{1c}
	1 = \int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
$$

In order for $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$ to be a valid probability density function, it must be non-negative for all $\small \bm{m}$. That is the case only if $\small \mathbb{M^2}$ is a height field, since this ensures that $\small (\bm{n} \cdot \bm{m}) \ge 0$.

{{< figure src="/img/mst/fig2.png" >}}

One of the simplest examples of a valid microsurface is a box[^5] (see Fig. 2 above), with the flipped bottom face playing the role of the macrosurface. It is instructive to analyze its two-dimensional counterpart -- a rectangle.

[^5]: Using the tools of calculus, we can decompose an arbitrary surface into a (possibly infinite) number of (sufficiently small) boxes.

{{< figure src="/img/mst/fig3.png" >}}

*Linearity* of Eqn. 1 allows us to consider the individual microfacets separately and to sum up their contributions. As we apply Eqn. 1 to the box, observe that the signed projected areas of the opposite faces cancel each other, leaving just the top (that is, of course, equivalent to the bottom). Another consequence is that a linearly transformed surface (a parallelepiped, in our case; see Fig. 3 above) still results in a valid combination of the microsurface and the macrosurface. We can see why that is the case by picturing Eqn. 1 geometrically and (passively) transforming the coordinate axes rather than (actively transforming) the surface itself[^51]. Of course, while the surface lines remain unchanged, the signed projected areas (and, thus, the values of the NDF) do not stay the same.

[^51]: This requires the transformation to be invertible. If the transformation were nonlinear, the transformed axes would vary from point to point, forming vector fields.

Clearly, a microsurface does not have to be smooth (e.i. continuously differentiable); however, this ensures the continuity of the NDF, which is desirable, unless the goal is to model a flat surface.

Erasing any part of the box leads to a signed projected area mismatch for certain angles. Therefore, the constraint may seem to imply that the microsurface must be continuous, but, unfortunately, that is not the case. The issue lies in the *translation invariance*; in our case, it means that the orthogonally projected area of an object is independent of its location. This property may seem innocuous at first, but, coupled with the linearity, it spells disaster: we may freely translate different microfacets in different directions without affecting the value of the integral.

### Masking Function

Since the signed projected area is an incomplete description of a real surface (as opposed to a microfacet soup or a salad[^52]), we may additionally specify its *visible (orthogonally) projected area*

[^52]: In technical terms, the surface may have an extremely short auto-correlation distance.

$$ \tag{2a}
\begin{aligned}
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) dA(\bm{m})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) D(\bm{m}) A d\Omega(\bm{m}),
\end{aligned}
$$

which obviously depends on the relative location of the microfacets (see Fig. 4 below). It is defined in terms of the dimensionless *masking function* $\small G_1(\bm{v}, \bm{m})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along $\small \bm{v}$. In other words, it is the *average visibility* (along $\small \bm{v}$) of the microfacets with the normal $\small \bm{m}$. The masking function is closely related to the binary *visibility function* $\small V(\bm{v}, \bm{p})$ that outputs 0 if the point $\small \bm{p}$ is occluded along $\small \bm{v}$, and 1 otherwise. Both functions take *self-occlusion* into account: $\small V = G_1 = 0$ if $\small (\bm{v} \cdot \bm{m}) \le 0$. This subtle point helps us emphasize the geometric (e.i., coordinate-independent, basis-independent) nature of Eqn. 2.

{{< figure src="/img/mst/fig4.png" >}}

The masking and visibility functions possess an important property called *stretch invariance*, or, more generally, *invariance under linear transformations*. We have already seen that, in the context of the microfacet theory, a linearly transformed surface remains valid; however, unlike the NDF, the masking function is dimensionless: it only encodes visibility, and so it remains unaffected by a transformation of the coordinate axes. Of course, this means that the transformation must be applied to everything, including the view vector $\small \bm{v}$.

### Assumptions Underpinning the Microfacet Theory

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

In the special case of a height field, $\small G_1(\bm{n}, \bm{m}) = 1$.

{{< figure src="/img/mst/fig5.png" >}}

For a continuous surface, *the visible projected area is greater or equal to the signed projected area*. This inequality stems from self-occlusion, which eliminates the (formerly negative) contribution of back-facing microfacets. The two types of projected areas coincide only if the view angle is sufficiently steep, or the microsurface -- sufficiently thin, so that the latter does not extend outside the volume swept by the macrosuface translated along the view vector (see Fig. 5 above). The relative error can be reduced by tiling the microsurface, which also increases the macrosurface area (see Fig. 6 below).

{{< figure src="/img/mst/fig6.png" >}}

Of course, as the view angle tends to zero, so does the signed projected area, while the visible projected area becomes proportional to the height of the microsurface. This leads to one of the key assumptions of the microfacet theory: *a microsurface must be infinitesimally thick*. This limitation is particularly apparent at grazing angles, and should be familiar to those who have practical experience with bump and normal mapping. Furthermore, since the signed projected areas of the microsurface and the macrosurface coincide, we can only allow the view angles that obey $\small (\bm{v} \cdot \bm{n}) > 0$. In other words, *a microsurface must be facing the observer*. Otherwise, we must reverse the signs of both $\small \bm{n}$ and $\small \bm{m}$, which will produce a microsurface with different statistics. More specifically, while $D(-\bm{m})$ will give the correct number, the visibility will be different, which is particularly evident if you take a box and flip it upside down. For a tiling surface (such as a sawtooth), reversing the sign of $\small \bm{v}$, so that it points in the same hemisphere as $\small \bm{n}$, produces a reasonable approximation. It guarantees that self-occlusion is properly accounted for.

With the assumption (that all projected surface areas are the same) in place, we can combine Eqn. 1a and 2a into

$$ \tag{3a}
	1 =
	\int_{\bm{m} \in \mathbb{S^2}} D_{vis}(\bm{v}, \bm{n}, \bm{m}) d\Omega(\bm{m}),
$$

where

$$ \tag{3b}
	D_{vis}(\bm{v}, \bm{n}, \bm{m}) =
	\frac{(\bm{v} \cdot \bm{m})}{(\bm{v} \cdot \bm{n})} G_1(\bm{v}, \bm{m}) D(\bm{m})
$$

is called the *distribution of visible normals*[^7] (abbreviated as the *VNDF*). Its meaning becomes clear in the context of Eqn. 2a: $\small ((\bm{v} \cdot \bm{m})/(\bm{v} \cdot \bm{n}))^{-1} D_{vis}(\bm{v}, \bm{n}, \bm{m}) d\Omega(\bm{m})$ is the differential area of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along $\small \bm{v}$. Like $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$, the VNDF is a valid probability density function; but because it is always non-negative, this property is not limited to height fields.

[^7]: After comparing Eqn. 1c with 3a and taking the definitions into account, it seems more natural to simply let $\small D_{vis} = G_1 D$. Nevertheless, we stick with Eqn. 3b to conform to the existing body of literature.

---

### Smith's Approximation of the Masking Function (to be omitted)

In practice, one usually neither creates nor is given an actual microsurface, but rather specifies the statistical distributions directly. In fact, one generally goes one step further: since the NDF and the masking function are not independent (they satisfy Eqn. 3), a reasonable course of action is to specify just one and attempt to derive the other. Which distribution should we choose as the starting point? The answer comes from the definitions: $\small G_1$ is built on top of $\small D$, and the role of the former is to endow the latter with visibility data.

Thus, one typically starts with the NDF. Unfortunately, as we have already seen, it does not provide a complete surface description. This forces us to introduce additional assumptions in the form of a *microsurface profile*. Arguably, the simplest assumption is the *normal-occlusion independence* introduced by Smith:

$$ \tag{3Xa}
	G_1^S(\bm{v}, \bm{m}) = \Theta(\bm{v} \cdot \bm{m}) W_1(\bm{v}),
$$

where

$$ \tag{3Xb}
	\Theta(x) =
	\begin{cases}
	   1 &\text{if } x \ge 0, \cr
	   0 &\text{otherwise},
	\end{cases}
$$

is the *Heaviside step function*, which is used to model self-occlusion. As the name implies, the remaining occlusion term $\small W_1$ that models the average occlusion is independent of the microsurface normal $\small \bm{m}$.

In reality, the way the visibility depends on the surface normal is more complicated. Take a height field as an example: for shallow view angles, vertical microfacets are more likely to be occluded compared to horizontal ones. Fortunately, the issue is not very apparent unless one examines a rough surface at a grazing angle, where, as we have previously remarked, the foundations of the microfacet theory itself are on shaky ground.

Substitution of Eqn. 3X into 3 yields a ratio of projected areas (signed to front-facing; see Fig. 7 below):

$$ \tag{3Xc}
\begin{aligned}
	W_1(\bm{v})
	&= \frac{(\bm{v} \cdot \bm{n})}{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})}
	\cr
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} (\bm{v} \cdot \bm{m}) dA(\bm{m})}{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m})}
	\cr
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} (\bm{v} \cdot \bm{m}) dA(\bm{m})}{\int_{\bm{m} \in \mathbb{S^2}} (\bm{v} \cdot \bm{m}) dA(\bm{m}) + \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, -\bm{v} \cdot \bm{m}) dA(\bm{m})}
	\cr
	&= \frac{1}{1 + \Lambda(\bm{v})},
\end{aligned}
$$

where

$$ \tag{3Xd}
\begin{aligned}
	\Lambda(\bm{v})
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, -\bm{v} \cdot \bm{m}) dA(\bm{m})}{(\bm{v} \cdot \bm{n}) A}
	\cr
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, -\bm{v} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})}{(\bm{v} \cdot \bm{n})}
\end{aligned}
$$

is another ratio of projected areas (back-facing to signed; see Fig. 7 below). For a height field, $\small \Lambda(\bm{n}) = 0$.

{{< figure src="/img/mst/fig7.png" >}}

Eqn. 3Xc confirms that the connection between a microfacet and its neighborhood is absent. This puts us at risk of ending up with a microfacet soup or a salad (instead of a continuous surface) again. The only way to remedy this deficiency is by using a more sophisticated microsurface profile.

**Q: How accurate is Smith's approximation of the masking function?**

---

### BSDF: Definition and Properties

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

[^8]: The kinetic energy of the photon is only conserved if the integral is equal to 1. Otherwise, by definition, the collision is called inelastic. The momentum and the total energy of the system are always conserved.

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

where $\small \eta_v$ and $\small \eta_l$ are the real[^9] indices of refraction (the *IORs*) associated with the directions of exitance (along the view vector $\small \bm{v}$) and incidence (along the light vector $\small \bm{l}$), respectively. In particular, reciprocity is a direct consequence of the law of refraction (also valid for reflection)

$$ \tag{5a}
	\eta_v \Vert \bm{v} \times \bm{n} \Vert = \eta_l \Vert \bm{l} \times \bm{n} \Vert
$$

which leads to the compression of projected solid angles

$$ \tag{5b}
	\eta_v^2 d\Omega_n(\bm{v}) = \eta_l^2 d\Omega_n(\bm{l})
$$

and, thus, a change in energy density (provided $\small \eta_v \neq \eta_l$). Note that the relationship between the ordinary solid angles is not as simple:

$$ \tag{5c}
	\eta_v^2 d\Omega(\bm{v}) \neq \eta_l^2 d\Omega(\bm{l}).
$$

[^9]: We will not consider absorptive or magnetic media in this article. A discussion involving the physics of conductors (and certain dielectrics) would quickly become quite involved and take us too far afield, away from our original topic of microsurface transformations.

This is a good reason to utilize the projected solid angle measure. Failure to consistently do so often results in complicated expressions that are difficult to interpret and prone to errors.

The properties of a valid BSDF are by no means obvious. In particular, ensuring reciprocity may seem like a non-trivial task. Typically, that is done by decomposing the BSDF into a sum of two components: reflection (the *BRDF*) and transmission (the *BTDF*). The reflection component is always *symmetric*: since $\small \bm{v}$ and $\small \bm{l}$ always point away from the surface, $\small \eta_v = \eta_l$, which results in $\small f_r(\bm{v}, \bm{n}, \bm{l}) = f_r(\bm{l}, \bm{n}, \bm{v})$.

### Perfect Specular BSDF

In order to make things clear, we shall illustrate these properties using a concrete example. Consider a perfectly smooth, planar surface. Its BSDF (often referred to as the *perfect specular* BSDF) can be expressed in terms of the *Dirac delta "function"* $\small \delta$ defined as a projected solid angle measure by the equation

$$ \tag{6}
	f(\bm{r}) =
	\int_{\bm{l} \in \mathbb{S^2}} f(\bm{l}) \delta_{\Omega_n}(\bm{l} - \bm{r}) d\Omega_n(\bm{l})
$$

valid for any function $\small f: \mathbb{S^2} \to \mathbb{R}$.

Now, according to the law of reflection, the view and the light angles must be the same:

$$ \tag{7a}
	\sin{\theta_v} = \Vert \bm{v} \times \bm{n} \Vert = \Vert \bm{l} \times \bm{n} \Vert,
	\quad
	\cos{\theta_v} = \vert \bm{v} \cdot \bm{n} \vert = \vert \bm{l} \cdot \bm{n} \vert.
$$

Furthermore, we need to distinguish between the IORs associated with the exterior (above the surface) and the interior (below the surface):

$$ \tag{7b}
	\eta_v = \eta_l,
	\quad
	\eta_t =
	\begin{cases}
	   \eta_{int} &\text{if } \eta_v = \eta_{ext}, \cr
	   \eta_{ext} &\text{otherwise}.
	\end{cases}
$$

Using this formalism, the reflection component of the BSDF can be written as

$$ \tag{7c}
	f_r(\bm{v}, \bm{n}, \bm{l}) =
	F(\theta_v, \eta_v/\eta_t)
	\delta_{\Omega_n}\negmedspace\left( \bm{n} - \frac{\bm{v} + \bm{l}}{(\bm{v} + \bm{l}) \cdot \bm{n}} \right),
$$

where $\small 0 \le F \le 1$ is the dimensionless *Fresnel reflectance*. This BRDF is non-negative, symmetric, and energy-conserving, which can be verified by substituting Eqn. 7c into 4c. In particular,

$$ \tag{7d}
	\int_{\bm{v} \in \mathbb{S^2}}
	f_r(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{v}) =
	F(\theta_v, \eta_v/\eta_t) \le 1.
$$

The trigonometric form of the Fresnel term is

$$ \tag{8a}
	F(\theta_v, \eta_v/\eta_t)
	= (1 - \alpha) R_s(\theta_v, \eta_v/\eta_t) + \alpha R_p(\theta_v, \eta_v/\eta_t),
$$

where the value of $\small \alpha$ depends on the state of polarization of the incident light: $\small \alpha = 0$ corresponds to the s-polarized light, $\small \alpha = 1$ -- to the p-polarized light, and $\small \alpha = 1/2$ -- to the unpolarized (natural) light.

The two mutually perpendicular components are defined as follows:

$$ \tag{8b}
\begin{aligned}
	R_s(\theta_v, \eta_v/\eta_t)
	&= \left| \frac{\cos{\theta_v} / \cos{\theta_t} - \sin{\theta_t} / \sin{\theta_v}}{\cos{\theta_v} / \cos{\theta_t} + \sin{\theta_t} / \sin{\theta_v}} \right|^2
	= \left| \frac{\sin(2 \theta_v) - \sin(2 \theta_t)}{\sin(2 \theta_v) + \sin(2 \theta_t)} \right|^2,
	\cr
	R_p(\theta_v, \eta_v/\eta_t)
	&= \left| \frac{\cos{\theta_t} / \cos{\theta_v} - \sin{\theta_t} / \sin{\theta_v}}{\cos{\theta_t} / \cos{\theta_v} + \sin{\theta_t} / \sin{\theta_v}} \right|^2
	= \left| \frac{\sin(\theta_v - \theta_t)}{\sin(\theta_v + \theta_t)} \right|^2.
\end{aligned}
$$

The angle of refraction (or transmission) $\small \theta_t$ can be determined from the view angle $\small \theta_v$ and the relative IOR $\small \eta_v/\eta_t$ using the (already familiar) law of refraction and basic trigonometry:

$$ \tag{8c}
	\sin{\theta_t} = \frac{\eta_v}{\eta_t} \sin{\theta_v},
	\quad
	\cos{\theta_t} = \sqrt{1 - \sin^2{\theta_t} }.
$$

If you have a physics background, the labeling of the angles and the indices of refraction may appear unconventional, since Eqn. 8a-8c are typically expressed in terms of the direction of incidence rather than exitance. The present notation is motivated by the definition of the BSDF given by Eqn. 4a-4b. The validity of this formulation stems from the geometric symmetry: the solution of the Fresnel equations given by Eqn. 8 is invariant under the exchange of $\small \theta_v$ and $\small \theta_t$. Intuitively, the reflectance depends only on the path taken by light, and is independent of its direction.

The definition of the transmission component of the perfect specular BSDF is marginally more complicated. Let

$$ \tag{9a}
\begin{aligned}
	\sin{\theta_v} &= \Vert \bm{v} \times \bm{n} \Vert,
	&
	\cos{\theta_v} &= \vert \bm{v} \cdot \bm{n} \vert,
	\cr
	\sin{\theta_t} &= \Vert \bm{l} \times \bm{n} \Vert,
	&
	\cos{\theta_t} &= \vert \bm{l} \cdot \bm{n} \vert.
\end{aligned}
$$

By extension, we shall associate

$$ \tag{9b}
	\eta_t = \eta_l.
$$

After taking the reciprocity and the conservation of energy into account, the perfect specular BTDF can be expressed as

$$ \tag{9c}
	f_t(\bm{v}, \bm{n}, \bm{l}) =
	\frac{\eta_v^2}{\eta_l^2} \big( 1 - F(\theta_v, \eta_v/\eta_t) \big) \delta_{\Omega_n}\big( \eta_v (\bm{v} \times \bm{n}) + \eta_l (\bm{l} \times \bm{n}) \big),
$$

with the first term responsible for the projected solid angle compression, which can be readily verified by placing the surface inside a *white furnace* (e.i. setting $\small L(\bm{l}) = 1$ for all $\small \bm{l}$) and evaluating Eqn. 4b.

*Question: Are Eqn. 18 and 21 in Walter's paper correct? A factor of $\small \eta_o^2 / \eta_i^2 = \eta_v^2 / \eta_l^2$ appears to be missing.*

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

Unfortunately, Eqn. 9d fails to make it apparent that these two Dirac delta "functions" are not the same: their purpose is to measure two different projected solid angles, and their ratio is precisely the same as the missing factor of $\small \eta_l^2 / \eta_v^2$. To see why that is the case, we must recall that Eqn. 7c and 9c are only valid in the context of Eqn. 6, which is an integral over the domain of directions of incidence. If we use the reciprocal of a BSDF, the directions of incidence and exitance are interchanged, and Eqn. 6 must also be modified accordingly.

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
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) \le 1.
\end{aligned}
$$

Since Eqn. 7d and 9e sum up to 1, we have just demonstrated that the full BSDF is energy-conserving.

### Construction of a Rough Specular BSDF

We can model a rough specular surface by treating the latter as locally (rather than globally) planar and smooth. The amount of radiance scattered by the microsurface can then be expressed as a weighted average of the contributions of its visible microfacets. To proceed further, we must recall that, by definition, the radiance is the amount of power moving in a certain direction, per unit solid angle associated with this direction, per unit area perpendicular to this direction. If the source of light is very small (or very far away), it will appear point-like, and the variation of the view vector across its surface can be safely neglected. The same cannot be said for the visible projected area, which must be properly normalized (or we would calculate the intensity instead of the radiance; see Fig. 9 \[borrowed from Eric Heitz\] below).

{{< figure src="/img/mst/fig9.png" >}}

Thus,

$$ \tag{11a}
	L(\bm{v}) =
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) L(\bm{v}, \bm{p})
	V(\bm{v}, \bm{p}) dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}
$$

The general expression of the outgoing radiance term in the numerator of Eqn. 11a is given by the spatially varying version of Eqn. 4b:

$$ \tag{11c}
	L(\bm{v}, \bm{p}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}, \bm{p}) d\Omega_m(\bm{l}).
$$

Unfortunately, Eqn. 11c is recursive. In our case, this means that light scattered by one microfacet can be used to illuminate another in a process known as *multiple scattering*. For simplicity (and at the cost of correctness), when building microfacet models, this effect is often neglected, with only the distant illumination (and, thus, *single scattering*) taken into account:

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
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}.
$$

If we substitute the expressions of the perfect specular BSDF given by Eqn. 7c and 9c, the inner integral can be evaluated analytically. Starting with the reflection component,

$$ \tag{12}
	L_r(\bm{v})
	=
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	V(\bm{r}, \bm{p}) V(\bm{v}, \bm{p})
	dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})},
$$

where

$$ \tag{13a}
	\bm{r} = \bm{R}(\bm{v}, \bm{m}) = -\bm{v} + 2 (\bm{v} \cdot \bm{m}) \bm{m}
$$

is the reflected view vector that points along the direction of incidence (see Fig. 8 below).

{{< figure src="/img/mst/fig8.png" >}}

Now, according to the law of reflection, $\small \vert \bm{r} \cdot \bm{m} \vert = \vert \bm{v} \cdot \bm{m} \vert = \Vert \bm{r} + \bm{v} \Vert / 2$. Thus, we can solve Eqn. 13a for $\small \bm{m}$ if we recall that $\small (\bm{v} \cdot \bm{m}) > 0$ due to self-occlusion:

$$ \tag{13b}
	\bm{m} = \frac{\bm{r} + \bm{v}}{\Vert \bm{r} + \bm{v} \Vert}.
$$

In the absence of multiple scattering, we only consider distant sources of illumination; those located in the lower hemisphere are guaranteed to be occluded by the microsurface, which reduces the set of potentially useful directions of incidence to those with $\small (\bm{r} \cdot \bm{n}) > 0$.

The transmission component of a microfacet BSDF can be defined analogously:

$$ \tag{14}
	L_t(\bm{v})
	=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	V(\bm{t}, \bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})},
$$

where

$$ \tag{15a}
	\bm{t} = \bm{T}(\bm{v}, \bm{m}, \eta_v/\eta_t) =
	 -\frac{\eta_v}{\eta_t}\bm{v} + \left( \frac{\eta_v}{\eta_t}(\bm{v} \cdot \bm{m}) - \mathrm{sgn}(\bm{v} \cdot \bm{m}) \sqrt{1 - \frac{\eta_v^2}{\eta_t^2} \Vert \bm{v} \times \bm{m} \Vert^2 } \right) \bm{m}
$$

is the refracted (or transmitted) view vector (see Fig. 8 above). We must caution that, in certain cases, the value of the expression inside the square root is a negative number. This invalidates the refracted direction and implies that the light has been *totally internally reflected* by the surface.

Eqn. 15a can be solved for $\small \bm{m}$ provided $\small (\bm{v} \cdot \bm{m}) > 0$. The derivation is provided in the Appendix; here, we simply quote the result:

$$ \tag{15f}
	\bm{m} =
	\frac{-(\eta_v \bm{v} + \eta_t \bm{t})}{\eta_t \cos{\theta_t} - \eta_v \cos{\theta_v}}.
$$

Note that the denominator is positive only if $\small \eta_t > \eta_v$, which has an obvious effect on the direction of $\small \bm{m}$. The latter is such that $\small (\bm{v} \cdot \bm{m}) > 0 \text{ while } (\bm{t} \cdot \bm{m}) < 0$. As for the direction of incidence, distant light sources are always occluded by the microsurface unless $\small (\bm{t} \cdot \bm{n}) < 0$.

---

### Derivation of the Refracted View Vector (to be moved to the Appendix)

Eqn. 15a can be derived as follows. Let us define two unit vectors

$$ \tag{15b}
\begin{aligned}
	\bm{x}
	&= \frac{\bm{m} \times (\bm{v} \times \bm{m})}{\Vert \bm{m} \times (\bm{v} \times \bm{m}) \Vert}
	= \frac{\bm{v} - (\bm{v} \cdot \bm{m}) \bm{m}}{\Vert \bm{v} \times \bm{m} \Vert},
	\cr
	\bm{y}
	&= \mathrm{sgn}(\bm{v} \cdot \bm{m}) \bm{m},
\end{aligned}
$$

such that

$$ \tag{15c}
\begin{aligned}
	\sin{\theta_v}
	&= \bm{v} \cdot \bm{x}
	= \frac{1 - (\bm{v} \cdot \bm{m})^2}{\Vert \bm{v} \times \bm{m} \Vert}
	= \Vert \bm{v} \times \bm{m} \Vert \ge 0,
	\cr
	\cos{\theta_v}
	&= \bm{v} \cdot \bm{y}
	= \vert \bm{v} \cdot \bm{m} \vert \ge 0.
\end{aligned}
$$

In this coordinate system,

$$ \tag{15d}
\begin{aligned}
	\bm{v}
	&= \sin{\theta_v} \bm{x} + \cos{\theta_v} \bm{y},
	\cr
	\bm{t}
	&= -(\sin{\theta_t} \bm{x} + \cos{\theta_t} \bm{y}),
\end{aligned}
$$

where

$$ \tag{15dd}
\begin{aligned}
	\sin{\theta_t}
	&= \Vert \bm{t} \times \bm{m} \Vert \ge 0,
	\cr
	\cos{\theta_t}
	&= \vert \bm{t} \cdot \bm{m} \vert \ge 0,
\end{aligned}
$$

by convention.

Application of the law of refraction given by Eqn. 8c readily yields Eqn. 15a. This method has the following geometric interpretation: we reverse the direction of $\small \bm{v}$, shorten it by a factor of $\small \eta_v / \eta_t$, and stretch it along $\small \bm{m}$ until its length reaches the value of 1.

Eqn. 15d can be solved for $\small \bm{m}$ provided $\small (\bm{v} \cdot \bm{m}) > 0$. Then $\small \bm{y} = \bm{m}$, and we can eliminate $\small \bm{x}$ by rescaling $\small \bm{t}$ by a factor of $\small \sin{\theta_v} / \sin{\theta_t}$:

$$ \tag{15e}
	\bm{v} + \frac{\sin{\theta_v}}{\sin{\theta_t}} \bm{t}
	= \left( \cos{\theta_v} - \frac{\sin{\theta_v}}{\sin{\theta_t}} \cos{\theta_t} \right) \bm{m}.
$$

After applying the law of refraction once more and rearranging the terms, we obtain

$$ \tag{15f}
	\bm{m} =
	\frac{-(\eta_v \bm{v} + \eta_t \bm{t})}{\eta_t \cos{\theta_t} - \eta_v \cos{\theta_v}}.
$$

---

### Shadowing-Masking Function

Let us return to Eqn. 12 and 14. Upon close examination, the visibility terms (and the vector differential area $\small \bm{m} dA$) are the only ones that explicitly depend on the position $\small \bm{p}$. Intuitively, that is because visibility is a non-local property -- it connects a point to the entire surface.

We can evaluate both of these equations numerically by breaking the microsurface into individual microfacets and sorting the latter by their orientation. For a fixed view vector $\small \bm{v}$, a group of microfacets with the same normal $\bm{m}$ will also share the values of $\small F$ and $\small L$. During the final step, we must calculate the total visible area of the microfacets within each group.

The statistical method of evaluation of Eqn. 12 and 14 mirrors Eqn. 2a. We must introduce the dimensionless *shadowing-masking function* $\small G_2(\bm{v}, \bm{m}, \bm{l})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along both $\small \bm{v} \text{ and } \bm{l}$. In other words, it is the *average visibility* (along both $\small \bm{l} \text{ and } \bm{v}$) of the microfacets with the normal $\small \bm{m}$:

$$ \tag{16a}
\begin{aligned}
	&\int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{l}, \bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_2(\bm{v}, \bm{m}, \bm{l}) dA(\bm{m})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_2(\bm{v}, \bm{m}, \bm{l}) D(\bm{m}) A d\Omega(\bm{m}).
\end{aligned}
$$

Once projected onto $\small \bm{v} \text{ or } \bm{l}$, Eqn. 16a also represents the visible projected area, except that the visibility is now bidirectional. Similarly, it takes self-occlusion into account: $\small V = G_2 = 0$ if $\small (\bm{v} \cdot \bm{m}) \le 0 \text{ or } \small (\bm{l} \cdot \bm{m}) \le 0$.

A valid shadowing-masking function also has the following properties:

$$ \tag{16b}
\begin{aligned}
	\textit{directional symmetry: }
	&G_2(\bm{v}, \bm{m}, \bm{l}) = G_2(\bm{l}, \bm{m}, \bm{v});
	\cr
	\textit{directional correlation: }
	&G_2(\bm{v}, \bm{m}, \bm{v}) = G_1(\bm{v}, \bm{m});
	\cr
	\textit{height correlation: }
	&G_2(\bm{v}, \bm{m}, \bm{n}) = G_1(\bm{v}, \bm{m}).
\end{aligned}
$$

**Q: is the height correlation condition above correct? It simply tests whether the surface is a height field. I don't think so.**

**Q: Does height correlation make sense for arbitrary surfaces? I think so (e.g. consider a height field with a number of tunnels). This answers the question above.**

The last one is only applicable to height fields. Essentially, it says that the visibility of a microfacet depends on its altitude. The second property is more general; it implies that a bidirectional average cannot be factored into the product of unidirectional averages:

$$ \tag{16c}
	G_2(\bm{v}, \bm{m}, \bm{l}) \ne G_1(\bm{v}, \bm{m}) G_1(\bm{l}, \bm{m}).
$$

For example, consider a number of evenly spaced boxes -- a square wave. For $\small \bm{m} = \bm{n}$, the unidirectional visibility will approach 50% as the view angle increases. Thus, for shallow angles, $\small G_1(\bm{v}, \bm{n}) G_1(\bm{l}, \bm{n}) \approx 1/4$, while the correct value of $\small G_2(\bm{v}, \bm{n}, \bm{l}) \approx 1/2$.

---

### Smith's Approximation of the Shadowing-Masking Function (to be omitted)

In order to derive the shadowing-masking function from the distribution of normals, we may once again utilize Smith's assumption of normal-visibility independence:

$$ \tag{17a}
	G_2^S(\bm{v}, \bm{m}, \bm{l}) = \Theta(\bm{v} \cdot \bm{m}) \Theta(\bm{l} \cdot \bm{m}) W_2(\bm{v}, \bm{l}).
$$

**Q: geometrically, what is $\small \bm{W_2}$ supposed to be?**

The height-correlated bidirectional visibility term can be constructed by adding shadowing to the denominator of its unidirectional counterpart (see Eqn. 3Xc, 3Xd):

$$ \tag{17b}
\begin{aligned}
	W_2(\bm{v}, \bm{l})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v}) + \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{v} \cdot \bm{n}) A
	}{
		(\bm{v} \cdot \bm{n}) A + (\bm{v} \cdot \bm{n}) A \Lambda(\bm{v}) + (\bm{v} \cdot \bm{n}) A \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{v} \cdot \bm{n}) A
	}{
		\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m}) + (\bm{v} \cdot \bm{n}) A \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{l} \cdot \bm{n}) (\bm{v} \cdot \bm{n}) A
	}{
		(\bm{l} \cdot \bm{n}) \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m}) + (\bm{v} \cdot \bm{n}) (\bm{l} \cdot \bm{n}) A \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{l} \cdot \bm{n}) A (\bm{v} \cdot \bm{n}) A
	}{
		(\bm{l} \cdot \bm{n}) A \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m}) + (\bm{v} \cdot \bm{n}) A \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{l} \cdot \bm{m}) dA(\bm{m})
	}
	\cr
\end{aligned}
$$

$$
	W_2(\bm{v}, \bm{n})
	= \frac{
		1
	}{
		1 + \Lambda(\bm{v})
	}
$$

The combined occlusion value is always either greater than or equal to the occlusion in any given direction. In particular, when the view and light vectors are aligned, overestimation leads to the loss of the so-called “hotspot effect”:

$$
	W_2(\bm{v}, \bm{v})
	= \frac{
		1
	}{
		1 + 2 \Lambda(\bm{v})
	}
	\le
	\frac{
		1
	}{
		1 + \Lambda(\bm{v}).
	}
$$

**Q: how to derive the height-correlated function geometrically, for an arbitrary surface?**

Directional correlation can be introduced by subtracting a symmetric correction term:

$$ \tag{17c}
\begin{aligned}
	W_2(\bm{v}, \bm{l})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v}) + \Lambda(\bm{l}) - g(\bm{v} \cdot \bm{l}) \sqrt{\Lambda(\bm{v}) \Lambda(\bm{l})}
	}
	\cr
\end{aligned}
$$

$$
\begin{aligned}
	W_2(\bm{v}, \bm{v})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v})
	}
	\cr
\end{aligned}
$$

$$
\begin{aligned}
	W_2(\bm{v}, \bm{n})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v})
	}
	\cr
\end{aligned}
$$

A valid modification must satisfy $\small g(1) = 1$.

**Q: what is a good choice of g? Does it depend on roughness? Is the result still height-correlated?**

---

### Construction of a Microfacet Specular BSDF

In order to arrive at the expression of a microfacet BSDF, we must convert Eqn. 12 and 14 into a statistical form. The numerator can be rewritten in terms of the shadowing-masking function defined by Eqn. 16a. As for the denominator, according to the microfacet theory, the visible projected area of the microsurface is identical to the signed projected area of the macrosurface (see Eqn. 1-3). Therefore,

$$ \tag{18a}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) d\Omega(\bm{m})}
	{\bm{v} \cdot \bm{n}},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m}
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) d\Omega(\bm{m})}
	{\bm{v} \cdot \bm{n}}.
\end{aligned}
$$

The second equation required a minor modification: we had to reverse the refracted view vector $\small \bm{t}$ because the microsurface is one-sided; refer to the discussion below Eqn. 2b for details. This formulation is non-reciprocal: an equivalent (proper but verbose) expression uses $\small G_2(\mathrm{sgn}(\bm{n} \cdot \bm{v}) \bm{v}, \bm{m}, \mathrm{sgn}(\bm{n} \cdot \bm{t}) \bm{t})$.

Comparison of Eqn. 18a with 4b, which serves as a definition of a BSDF, reveals that the domain of integration is not the same. Therefore, we must perform a change of variables from the microsurface normal to the direction of incidence. This can be accomplished using the *Jacobian* determinant of the transformation:

$$ \tag{18b}
	J(\bm{m}, \bm{r}) = \frac{\partial \Omega(\bm{m})}{\partial \Omega(\bm{r})},
	\quad
	J(\bm{m}, \bm{t}) = \frac{\partial \Omega(\bm{m})}{\partial \Omega(\bm{t})}.
$$

Since a determinant can be negative, we shall utilize its absolute value:

$$ \tag{18c}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\bm{v} \cdot \int_{\bm{r} \in \mathbb{S^2}}
	\bm{m}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) \big\vert J(\bm{m}, \bm{r}) \big\vert d\Omega(\bm{r})}
	{\bm{v} \cdot \bm{n}},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\bm{v} \cdot \int_{\bm{t} \in \mathbb{S^2}}
	\bm{m}
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) \big\vert J(\bm{m}, \bm{t}) \big\vert d\Omega(\bm{t})}
	{\bm{v} \cdot \bm{n}}.
\end{aligned}
$$

The Jacobian of Eqn. 18b describes the rate at which one solid angle changes relative to the other. The notation has been purposefully chosen to mimic the one-dimensional partial[^10] derivative $\small \partial x / \partial t$. Similarly, the one-dimensional change of variables $\small dx = (\partial x / \partial t) dt$ is completely analogous to

[^10]: The derivative is partial because the microsurface normal implicitly depends on the view vector.

$$ \tag{18d}
	d\Omega(\bm{m})
	= \left\vert \frac{\partial \Omega(\bm{m})}{\partial \Omega(\bm{r})} \right\vert d\Omega(\bm{r})
	= \big\vert J(\bm{m}, \bm{r}) \big\vert d\Omega(\bm{r}).
$$

Intuitively, the Jacobian is just a ratio of two differential solid angles, each corresponding to a projected differential area. Unfortunately, making this statement more precise requires a background in vector analysis. For the reader who wishes to understand the derivation (and not just apply the results), we provide a brief introduction to the properties of differential solid angles in the Appendix.

---

### Geometric Properties of Differential Solid Angles (to be moved to the Appendix)

Consider a surface parameterized by two coordinates (e.g. the Cartesian or the spherical coordinates):

$$ \tag{19a}
	\bm{p} = \bm{p}(x,y).
$$

The change in the surface position is given by the chain rule:

$$ \tag{19b}
	d\bm{p}
	= \frac{\partial \bm{p}}{\partial x} dx + \frac{\partial \bm{p}}{\partial y} dy.
$$

Let us define a local coordinate frame:

$$ \tag{19c}
	\bm{i}
	= \frac{\partial \bm{p}}{\partial x},
	\quad
	\bm{j}
	= \frac{\partial \bm{p}}{\partial y},
	\quad
	\bm{k}
	= \bm{i} \times \bm{j}.
$$

Clearly, Eqn. 19b is a sum of two linearly independent vectors:

$$ \tag{19d}
	d\bm{p} = \bm{i} dx + \bm{j} dy.
$$

They form a little parallelogram that can be represented by the vector differential area

$$ \tag{19e}
	d\bm{A}
	= (\bm{i} dx) \times (\bm{j} dy)
	= (\bm{i} \times \bm{j}) dx dy
	= \bm{k} dx dy.
$$

Its magnitude is the scalar differential area

$$ \tag{19f}
	dA
	= \Vert d\bm{A} \Vert
	= dx dy,
$$

where we have made a simplifying assumption that the basis vectors are mutually orthogonal unit vectors.

Now, let us perform a change of variables

$$ \tag{20a}
	x = x(s,t),
	\quad
	y = y(s,t).
$$

According to the chain rule,

$$ \tag{20b}
	d\bm{p} = \frac{\partial \bm{p}}{\partial s} ds + \frac{\partial \bm{p}}{\partial t} dt.
$$

The transformed basis vectors are:

$$ \tag{20c}
	\bm{i'}
	= \frac{\partial \bm{p}}{\partial s},
	\quad
	\bm{j'}
	= \frac{\partial \bm{p}}{\partial t},
	\quad
	\bm{k'}
	= \bm{i'} \times \bm{j'}.
$$

Eqn. 20b can thus be written as

$$ \tag{20d}
	d\bm{p} = \bm{i'} ds + \bm{j'} dt.
$$

The expression of the vector differential area mirrors Eqn. 19e:

$$ \tag{20e}
	d\bm{A}
	= (\bm{i'} ds) \times (\bm{j'} dt)
	= (\bm{i'} \times \bm{j'}) ds dt
	= \bm{k'} ds dt.
$$

If we do not assume that the transformed basis is orthonormal, the expression of the scalar differential area must account for the length of the basis vectors and the angle between them:

$$ \tag{20f}
	dA
	= \Vert d\bm{A} \Vert
	= \Vert \bm{k'} \Vert ds dt
	= \Vert \bm{i'} \times \bm{j'} \Vert ds dt.
$$

Now, if we want to replace Eqn. 19f with 20f in an area integral, their value must be the same[^11]. The ratio of the areas of the parallelograms formed by the basis vectors is given by the absolute value of the Jacobian

[^11]: The same is true for Eqn. 19e and 20e. Intuitively, both the orientation and the size (but not the shape) of the surface elements must be independent of the choice of coordinates (the tessellation scheme).

$$ \tag{21}
	\vert J \vert = \frac{dx dy}{ds dt} = \Vert \bm{i'} \times \bm{j'} \Vert.
$$

The Jacobian is also frequently used in triple integrals, where its absolute value corresponds to the ratio of the volumes of two parallelepipeds.

Once the Eqn. 20a that relates the sets of coordinates has been specified, the expression of the Jacobian can be determined algebraically, by calculating the determinant of the matrix of partial derivatives. However, in our case, the geometric (e.i., coordinate-independent, basis-independent) approach is both simpler and more insightful.

First, we must point out two obvious properties of the vector differential area. It remains unchanged if the surface is translated along a constant vector $\small \bm{q}$:

$$ \tag{22a}
	d\bm{A}(\bm{p} + \bm{q}) = d\bm{A}(\bm{p}).
$$

Furthermore, the area scales quadratically with the dimensions of the surface. For a constant $\small \lambda$,

$$ \tag{22b}
	d\bm{A}(\lambda \bm{p}) = \lambda^2 d\bm{A}(\bm{p}).
$$

For a unit vector $\small \bm{m}$ and a variable $\small \lambda(\bm{m})$, the total differential in the spherical coordinates is

$$ \tag{22c}
	d(\lambda \bm{m})
	= \left( \frac{\partial \lambda}{\partial \theta} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \theta} \right) d\theta
	+ \left( \frac{\partial \lambda}{\partial \phi} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \phi} \right) d\phi.
$$

All three vectors, $\small \bm{m}, \partial \bm{m} / \partial \theta, \text{and } \partial \bm{m} / \partial \phi$, are mutually orthogonal. Thus,

$$ \tag{22d}
\begin{aligned}
	\bm{m} \cdot d\bm{A}(\lambda \bm{m})
	&= \bm{m} \cdot \Bigg( \left( \frac{\partial \lambda}{\partial \theta} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \theta} \right)
	\times \left( \frac{\partial \lambda}{\partial \phi} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \phi} \right) \Bigg) d\theta d\phi
	\cr
	&= \bm{m} \cdot \lambda^2 \left( \frac{\partial \bm{m}}{\partial \theta}
	\times
	\frac{\partial \bm{m}}{\partial \phi} \right) d\theta d\phi
	= \bm{m} \cdot \lambda^2 d\bm{A}(\bm{m}).
\end{aligned}
$$

Now, recall that, by definition, the solid angle subtended by an object is the surface area of its (radial) projection onto the unit sphere. If we consider an infinitesimal surface fragment of the vector area $\small d\bm{A}$ located at the point $\small \bm{p}$, the associated differential solid angle is

$$ \tag{23a}
	d\Omega(\bm{p})
	= \left\vert \frac{\bm{p}}{\Vert \bm{p} \Vert} \cdot \frac{d\bm{A}(\bm{p})}{\Vert \bm{p} \Vert^2} \right \vert.
$$

In particular, for an area element located on the surface of a unit sphere,

$$ \tag{23b}
	d\bm{A}(\bm{m}) = \bm{m} d\Omega(\bm{m}).
$$

As an angular quantity, the solid angle is scale-invariant:

$$ \tag{23c}
	d\Omega(\lambda \bm{m})
	= \left\vert \frac{\lambda \bm{m}}{\Vert \lambda \bm{m} \Vert} \cdot \frac{d\bm{A}(\lambda \bm{m})}{\Vert \lambda \bm{m} \Vert^2} \right \vert
	= d\Omega(\bm{m}).
$$

However, a radial projection is not translation-invariant:

$$ \tag{23d}
\begin{aligned}
	d\Omega(\bm{p} + \bm{q})
	&= \left\vert \frac{\bm{p} + \bm{q}}{\Vert \bm{p} + \bm{q} \Vert} \cdot \frac{d\bm{A}(\bm{p} + \bm{q})}{\Vert \bm{p} + \bm{q} \Vert^2} \right \vert
	\cr
	&= \left\vert \frac{\bm{p} + \bm{q}}{\Vert \bm{p} + \bm{q} \Vert} \cdot \frac{d\bm{A}(\bm{p})}{\Vert \bm{p} + \bm{q} \Vert^2} \right \vert.
\end{aligned}
$$

---

We can utilize these geometric properties to find the expression of the Jacobian in Eqn. 18d. Taking $\small \bm{v}$ as a constant, substitution of Eqn. 13b into 23a and utilization of Eqn. 23b-23d yields

$$ \tag{24a}
	d\Omega(\bm{m})
	= d\Omega(\bm{r} + \bm{v})
	= \frac{\big\vert \bm{m} \cdot d\bm{A}(\bm{r}) \big\vert}{\Vert \bm{r} + \bm{v} \Vert^2}
	= \frac{\vert \bm{m} \cdot \bm{r} \vert}{\Vert \bm{r} + \bm{v} \Vert^2} d\Omega(\bm{r}).
$$

Thus, according to the definitions of the absolute value of the Jacobian (Eqn. 18d) and the law of reflection (Eqn. 7a),

$$ \tag{24b}
	\big\vert J(\bm{m}, \bm{r}) \big\vert
	= \frac{\vert \bm{r} \cdot \bm{m} \vert}{\Vert \bm{r} + \bm{v} \Vert^2}
	= \frac{1}{4 |\bm{r} \cdot \bm{m}|}.
$$

This derivation is illustrated on Fig. 10 \[borrowed from Bruce Walter\] below.

{{< figure src="/img/mst/fig10.png" >}}

Similarly, we can use Eqn. 15f to show that

$$ \tag{25a}
	d\Omega(\bm{m})
	= d\Omega(\eta_v \bm{v} + \eta_t \bm{t})
	= \frac{\big\vert \bm{m} \cdot d\bm{A}(\eta_t \bm{t}) \big\vert}{\Vert \eta_v \bm{v} + \eta_t \bm{t} \Vert^2}
	= \frac{\eta_t^2 \vert \bm{m} \cdot \bm{t} \vert}{\Vert \eta_v \bm{v} + \eta_t \bm{t} \Vert^2} d\Omega(\bm{t}).
$$

Therefore, according to the law of refraction (see Eqn. 8c and 15c-15f),

$$ \tag{25b}
\begin{aligned}
	\big\vert J(\bm{m}, \bm{t}) \big\vert
	&= \frac{\eta_t^2 \vert \bm{t} \cdot \bm{m} \vert}{\Vert \eta_v \bm{v} + \eta_t \bm{t} \Vert^2}
	\cr
	&= \frac{\eta_t^2 \vert \bm{t} \cdot \bm{m} \vert}{\Vert \eta_t \cos{\theta_t} - \eta_v \cos{\theta_v} \Vert^2}
	\cr
	&= \frac{\eta_t^2 \vert \bm{t} \cdot \bm{m} \vert}{\big( \eta_t (\bm{t} \cdot \bm{m}) + \eta_v (\bm{v} \cdot \bm{m}) \big)^2}
	\cr
	&= \frac{\eta_t^2 \vert \bm{t} \cdot \bm{m} \vert}{\left( \eta_t (\bm{t} \cdot \bm{m}) + \sqrt{\eta_v^2 - \eta_t^2 \Vert \bm{t} \times \bm{m} \Vert^2 } \right)^2}.
\end{aligned}
$$

This derivation is illustrated on Fig. 11 \[borrowed from Bruce Walter\] below.

{{< figure src="/img/mst/fig11.png" >}}

We have finally reached the point where we have all the parts required for assembly of a microfacet BSDF. Let us substitute Eqn. 24b and 25b into Eqn. 18c:

$$ \tag{26a}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\int_{\bm{r} \in \mathbb{S^2}}
	(\bm{v} \cdot \bm{m})
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m})  d\Omega(\bm{r})}
	{4 |\bm{r} \cdot \bm{m}| (\bm{v} \cdot \bm{n})},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^4}{\eta_v^2}
	\frac{\int_{\bm{t} \in \mathbb{S^2}}
	(\bm{v} \cdot \bm{m})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) |\bm{t} \cdot \bm{m}| d\Omega(\bm{t})}
	{\big( \eta_v (\bm{v} \cdot \bm{m}) + \eta_t (\bm{t} \cdot \bm{m}) \big)^2 (\bm{v} \cdot \bm{n})}.
\end{aligned}
$$

Recall that the microfacet theory requires $\small (\bm{v} \cdot \bm{n}) > 0$. In addition, the single scattering approximation permits us to neglect the directions of incidence that fail to satisfy either $\small (\bm{r} \cdot \bm{n}) > 0 \text{ or } (\bm{t} \cdot \bm{n}) < 0$. Finally, due to the law of reflection and self-occlusion, $\small (\bm{v} \cdot \bm{m}) = (\bm{r} \cdot \bm{m}) > 0 \text{ and } (\bm{t} \cdot \bm{m}) < 0$. We may use these properties to simplify and rearrange Eqn. 26a:

$$ \tag{26b}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\int_{\bm{r} \in \mathbb{H_{+}^2}}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) |\bm{r} \cdot \bm{n}| d\Omega(\bm{r})}
	{4 (\bm{v} \cdot \bm{n}) (\bm{r} \cdot \bm{n})},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^4}{\eta_v^2}
	\frac{\int_{\bm{t} \in \mathbb{H_{-}^2}}
	(\bm{v} \cdot \bm{m}) (\bm{t} \cdot \bm{m})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) |\bm{t} \cdot \bm{n}| d\Omega(\bm{t})}
	{\big( \eta_v (\bm{v} \cdot \bm{m}) + \eta_t (\bm{t} \cdot \bm{m}) \big)^2 (\bm{v} \cdot \bm{n}) (\bm{t} \cdot \bm{n})}.
\end{aligned}
$$

To facilitate comparison with Eqn. 4b, we shall utilize the projected solid angle notation:

$$ \tag{26c}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\int_{\bm{r} \in \mathbb{H_{+}^2}}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) d\Omega_n(\bm{r})}
	{4 (\bm{v} \cdot \bm{n}) (\bm{r} \cdot \bm{n})},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^4}{\eta_v^2}
	\frac{\int_{\bm{t} \in \mathbb{H_{-}^2}}
	(\bm{v} \cdot \bm{m}) (\bm{t} \cdot \bm{m})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) d\Omega_n(\bm{t})}
	{\big( \eta_v (\bm{v} \cdot \bm{m}) + \eta_t (\bm{t} \cdot \bm{m}) \big)^2 (\bm{v} \cdot \bm{n}) (\bm{t} \cdot \bm{n})},
\end{aligned}
$$

which makes extracting the expressions of the BRDF and the BTDF particularly easy:

$$ \tag{26d}
\begin{aligned}
	f_r(\bm{v}, \bm{n}, \bm{l})
	&=
	\frac{
	F(\theta_v, \eta_v/\eta_t))
	G_2(\bm{v}, \bm{m}, \bm{l})
	D(\bm{m})}
	{4 (\bm{v} \cdot \bm{n}) (\bm{l} \cdot \bm{n})},
	\cr
	f_t(\bm{v}, \bm{n}, \bm{l})
	&=
	\frac{\eta_l^4}{\eta_v^2}
	\frac{(\bm{v} \cdot \bm{m}) (\bm{l} \cdot \bm{m})}
	{\big( \eta_v (\bm{v} \cdot \bm{m}) + \eta_l (\bm{l} \cdot \bm{m}) \big)^2}
	\frac{
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big)
	G_2(\bm{v}, \bm{m}, -\bm{l})
	D(\bm{m})}
	{ (\bm{v} \cdot \bm{n}) (\bm{l} \cdot \bm{n})}.
\end{aligned}
$$

This concludes our derivation of a microfacet specular BSDF.

## Acknowledgements

...

## References and Suggestions for Further Reading

Veach's thesis

Eric's understanding

Walter's ggx

Walter's ellipsoid

Atanasov's transformations

## Naming convention

view vector, light vector

view angle, light angle

direction of incidence, direction of exitance
