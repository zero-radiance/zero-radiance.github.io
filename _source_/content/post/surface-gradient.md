---
draft: true
title: "Exploring the Surface Gradient"
date: 2019-04-06T15:39:51-07:00
categories: [ "Graphics", "Math" ]
tags: [
	"Height Mapping",
	"Normal Mapping",
	"Surface Gradient",
	]
---

Realistic rendering at high frame rates remains at the core of real-time computer graphics. High performance and high fidelity are often at odds, requiring clever tricks and approximations to reach the desired quality bar.

One of the oldest tricks in the book is [bump mapping](https://www.microsoft.com/en-us/research/publication/simulation-of-wrinkled-surfaces/). Introduced in 1978 by Jimm Blinn, it is a simple way to add mesoscopic detail without increasing the geometric complexity of the scene. Most modern real-time renderers support a variation of this technique called normal mapping. While it's fast and easy to use, certain operations, such as blending, are [not as easy as they seem](https://blog.selfshadow.com/publications/blending-in-detail/). This is where the so-called "surface gradient framework" comes into play.

The surface gradient framework is a set of tools to transform and combine normal (or bump) maps originally introduced by Morten S. Mikkelsen in his 2010 paper titled ["Bump Mapping Unparametrized Surfaces on the GPU"
](http://jbit.net/~sparky/sfgrad_bump/mm_sfgrad_bump.pdf), and further extended to handle triplanar mapping in his subsequent [blog post](http://mmikkelsen3d.blogspot.com/2013/10/volume-height-maps-and-triplanar-bump.html). While there's nothing wrong with these two publications, to really understand what's going on, it really helps to also read his [thesis](http://image.diku.dk/projects/media/morten.mikkelsen.08.pdf), and at 109 pages, it's quite a time investment.

Instead, I will attempt to give a shorter derivation, assuming no prior knowledge of the topic, starting from the the first principles. Still with me? Let's dive in.

<!--more-->

## Preliminaries, Part 1: Tangent Frame

We start with a 3D surface \\(S\\). Assume that it has some unique 2D parametrization, e.i. an injective (one to one) mapping from a parametric 2D coordinate to an object-space 3D coordinate:

$$ \tag{1} S(u,v) = \lbrace x,y,z \rbrace. $$

You can think of it as a transformation from a 2D plane to a 3D surface. The injectivity requirement is differentiability in disguise, the property we will heavily lean on during the derivation.

Application of an affine transformation yields a set of texture coordinates:

$$ \tag{2} \lbrace s,t \rbrace = \lbrace au+c, bv+d \rbrace. $$

While it's often called a UV set, we'll stick to using \\(\lbrace u,v \rbrace\\) for unique values and \\(\lbrace s,t \rbrace\\) for scaled-translated ones. Note that we don't care about a particular surface parametrization: we can use UV-mapping, planar mapping, or something else entirely.

Armed with the surface parametrization, we can compute the tangent and the bitangent at the surface point:

$$ \tag{3} T(u,v) = \frac{\partial S}{\partial u}, \quad B(u,v) = \frac{\partial S}{\partial v}. $$

These two vectors span the tangent plane, and are typically neither mutually orthogonal nor of unit length. It's important to remember that they are not independent from the surface parametrization.

We can use them to compute the geometric normal of the surface. To make the normal independent from the surface parametrization, it should be normalized:

$$ \tag{4} N(u,v) = \frac{T \times B}{\Vert T \times B \Vert}. $$

This completes the tangent frame, which can be represented as a (scaled) tangent-to-object-space matrix:

$$ \tag{5} M\_{tangent}(u,v) = [T | B | N]. $$

We will need the inverse of the matrix going forward:

$$ \tag{6} (M\_{tangent}^{-1}(u,v))^{\mathrm{T}} = \frac{\mathrm{cof}(M\_{tangent})}{\mathrm{det}(M\_{tangent})}
                  = \frac{[B \times N | N \times T | T \times B]}{(T \times B) \boldsymbol{\cdot} N}
                  = \Bigg[\frac{B \times N}{\Vert T \times B \Vert} \Bigg| \frac{N \times T}{\Vert T \times B \Vert} \Bigg| N\Bigg]. $$

## Preliminaries, Part 2: Height Maps

The simplest representation of a tangent-space normal (or bump) map is a height map \\(h = h(s,t)\\). Geometrically, a height map defines an implicit surface of the form \\(z=z(x,y)\\), or

$$ \tag{7} f(x,y,z) = z - z(x,y) = 0. $$

The direction of the height map normal is given by the *gradient of the surface*:

$$ \tag{8} \nabla f(x,y,z) = \frac{\partial f}{\partial x} I + \frac{\partial f}{\partial y} J + \frac{\partial f}{\partial z} K, $$

where \\(\lbrace I,J,K \rbrace\\) is a set of orthonormal basis vectors. Substitution of the height map equation readily yields the following expression:

$$ \tag{9} G(s,t) = -\frac{\partial h}{\partial s} I -\frac{\partial h}{\partial t} J + K = \lbrace -\frac{\partial h}{\partial s}, -\frac{\partial h}{\partial t}, 1 \rbrace, $$

where \\({\partial h}/{\partial s}\\) and \\({\partial h}/{\partial t}\\) are the slopes of the height map. To avoid confusion with Morten's *surface gradient*, we'll refer to \\(G\\) as the *height map gradient*. The coordinates are given w.r.t. the basis vectors of the tangent space.

It's convenient to get the surface re-parametrization induced by the texture coordinates out of the way:

$$ \tag{10} G(u,v) = \lbrace -\frac{\partial h}{\partial s} \frac{\partial s}{\partial u}, -\frac{\partial h}{\partial t} \frac{\partial t}{\partial v}, 1 \rbrace = \lbrace -a \frac{\partial h}{\partial s}, -b \frac{\partial h}{\partial t}, 1 \rbrace. $$

This simply means that we need to rescale the slopes in order to adjust the displacements as we tile the texture.

For completeness, we can convert a tangent-space normal into the slope (gradient) form in the following way:

$$ \tag{11} G(s,t) = \lbrace - \frac{\partial h}{\partial s}, - \frac{\partial h}{\partial t}, 1 \rbrace = \frac{N\_{t}}{n\_{t,z}}, $$

$$ \tag{12} G(u,v) = \lbrace -\frac{\partial h}{\partial s} \frac{\partial s}{\partial u}, -\frac{\partial h}{\partial t} \frac{\partial t}{\partial v}, 1 \rbrace = \lbrace a \frac{n\_{t,x}}{n\_{t,z}}, b \frac{n\_{t,y}}{n\_{t,z}}, 1 \rbrace = M\_{tile} \frac{N\_{t}}{n\_{t,z}}. $$

To perturb the surface using the height map, we need to perform a change of basis from the orthonormal tangent-space set \\(\lbrace I,J,K \rbrace\\) to our previously defined object-space set \\(\lbrace T,B,N \rbrace\\). Doing this correctly is slightly more involved than it may seem at first glance.

## Preliminaries, Part 3: Wrinkled Surfaces

The original formulation of bump maps defines a height-mapped surface by applying a perturbation along the normal of the original surface. Note that this is the right place to apply the object's scale matrix, as it should affect the displacements (ultimately, by scaling the slopes):

$$ \tag{13} P(u, v) = M\_{scale} \Big( S(u, v) + h(u, v) N(u, v) \Big). $$

Just as we did before, we can take derivatives of this equation to compute the perturbed normal.

$$ \tag{14} T_p(u,v) = \frac{\partial P}{\partial u}
					 = M\_{scale}(T + \frac{\partial h}{\partial u} N + h \frac{\partial N}{\partial u})
					 \approx M\_{scale}(T + \frac{\partial h}{\partial u} N), $$
$$ \tag{15} B_p(u,v) = \frac{\partial P}{\partial v}
					 = M\_{scale}(B + \frac{\partial h}{\partial v} N + h \frac{\partial N}{\partial v})
					 \approx M\_{scale}(B + \frac{\partial h}{\partial v} N). $$

Jim claims that for small displacements and moderately curved surfaces, the last term is negligibly small and can be set to 0. For those interested, Morten verifies that this is indeed the case in his [thesis](http://image.diku.dk/projects/media/morten.mikkelsen.08.pdf) [TODO: MORTEN - MORE PRECISE REF NEEDED].

We can compute the perturbed normal by taking the cross product:

$$ \tag{16} N_p(u,v) = \frac{T_p \times B_p}{\Vert T_p \times B_p \Vert}
					 \approx \frac{1}{\Vert T_p \times B_p \Vert} \Big((M\_{scale}(T + \frac{\partial h}{\partial u} N)) \times
					 ( M\_{scale}(B + \frac{\partial h}{\partial v} N)) \Big). $$

We can move the matrix outside of the brackets with the help of the following [identity](https://en.wikipedia.org/wiki/Cross_product#Algebraic_properties):

$$ \tag{17} (MA) \times (MB) = (\mathrm{det}(M))(M^{-1})^{\mathrm{T}}(A \times B)
							 = (\mathrm{cof}(M)) (A \times B), $$

$$ \tag{18} N_p(u,v) \approx \frac{\mathrm{cof}(M\_{scale})}{\Vert T_p \times B_p \Vert} \Big((T + \frac{\partial h}{\partial u} N) \times (B + \frac{\partial h}{\partial v} N)\Big). $$

It's instructive to expand the cross product:

$$ \tag{19} N_p(u,v) \approx \frac{\mathrm{cof}(M\_{scale})}{\Vert T_p \times B_p \Vert} \Big((T \times B) + \frac{\partial h}{\partial u} (N \times B) + \frac{\partial h}{\partial v} (T \times N) + \frac{\partial h}{\partial u} \frac{\partial h}{\partial v} (N \times N)\Big). $$

The last term vanishes because the value of the cross product is 0:

$$ \tag{20} N_p(u,v) \approx \frac{\mathrm{cof}(M\_{scale})}{\Vert T_p \times B_p \Vert} \Big((T \times B) + \frac{\partial h}{\partial u} (N \times B) + \frac{\partial h}{\partial v} (T \times N)\Big). $$

If we swap the operands of cross products, this equation will take a familiar form:

$$ \tag{21} N_p(u,v) \approx \frac{\Vert T \times B \Vert}{\Vert T_p \times B_p \Vert} \mathrm{cof}(M\_{scale}) \frac{[B \times N | N \times T | T \times B]}{\Vert T \times B \Vert} \lbrace -\frac{\partial h}{\partial u}, -\frac{\partial h}{\partial v}, 1 \rbrace, $$

$$ \tag{22} N_p(u,v) \approx \frac{\Vert T \times B \Vert}{\Vert T_p \times B_p \Vert} \mathrm{cof}(M\_{scale}) (M\_{tangent}^{-1})^{\mathrm{T}}G. $$

The first term of the equation (22) can be a bit surprising. Intuitively, it accounts for the change of volume from the old to the new coordinate frame. Since it's a non-negative scalar, it does not modify the direction of the normal, and we can take care of it by normalizing the resulting vector.

The important part is, since normals are [bivectors](https://en.wikipedia.org/wiki/Bivector#Geometric_interpretation), they are transformed by the inverse-transpose of the matrix. Conversely, projection of an (unscaled) object-space normal into the tangent space is performed using the transpose:

$$ \tag{23} N_o(u,v) = \frac{(M\_{tangent}^{-1})^{\mathrm{T}} G}{\Vert (M\_{tangent}^{-1})^{\mathrm{T}} G \Vert}, $$

$$ \tag{24} \frac{(M\_{tangent})^{\mathrm{T}} N_o}{\Vert (M\_{tangent})^{\mathrm{T}} N_o \Vert} = (M\_{tangent})^{\mathrm{T}} (M\_{tangent}^{-1})^{\mathrm{T}} G, $$

$$ \tag{25} \frac{(M\_{tangent})^{\mathrm{T}} N_o}{\Vert (M\_{tangent})^{\mathrm{T}} N_o \Vert} = (M\_{tangent}^{-1} M\_{tangent})^{\mathrm{T}} G = G. $$

"This seems quite expensive", you may say. Does correctness come at a high cost? Not really.

Let's say we just have a tangent-space normal map, which we would like to tile and perhaps scale, and that's it. To compute the perturbed normal in the world space, all we have to do is the following:

$$ \tag{26} N_w(u,v) \approx \frac{\big( M\_{rot} (\mathrm{cof}(M\_{scale})) (M\_{tangent}^{-1})^{\mathrm{T}} \big) M\_{tile} N\_{t} / n\_{t,z}}{\Vert \big( M\_{rot} (\mathrm{cof}(M\_{scale})) (M\_{tangent}^{-1})^{\mathrm{T}} \big) M\_{tile} N\_{t} / n\_{t,z} \Vert}, $$

$$ \tag{27} N_w(u,v) \approx \frac{M\_{world} M\_{tile} N\_{t}}{\Vert M\_{world} M\_{tile} N\_{t} \Vert}. $$

The tangent-space-normal-to-world matrix \\(M\_{world}\\) can be precomputed, and all that's left to do at runtime is a few multiplications and one normalization.

## Surface Gradient Framework

Morten defines the surface gradient as the projection of the height map gradient onto the tangent plane:

$$ \tag{28} \Gamma(u,v) = (M\_{tangent}^{-1})^{\mathrm{T}} \lbrace \frac{\partial h}{\partial u}, \frac{\partial h}{\partial v}, 0 \rbrace = N - (M\_{tangent}^{-1})^{\mathrm{T}} G.$$

By definition, it is a vector in the tangent plane with the direction pointing up along the steepest slope and the magnitude of the rate at which height increases in that direction.
Given the surface gradient, it's easy to obtain ("resolve") the perturbed normal:

$$ \tag{29} N_p(u,v) = \frac{(\mathrm{cof}(M\_{scale}))(N - \Gamma)}{\Vert (\mathrm{cof}(M\_{scale}))(N - \Gamma) \Vert}. $$

Why is it useful? Since it's just a matrix transformation, the surface gradient is a linear operator, which, combined with the fact that the derivative is a linear operator as well, makes many common operations straightforward. Additionally, the surface gradient, like the surface normal, is independent from the surface parametrization. And finally, the surface gradient is compact, since we do not need to retain the entire tangent frame.

#### 1. Rescaling the Height Map

Want to flatten the normal map, or make it more bumpy? Simply rescale the surface gradient:

$$ \tag{30} \alpha \Gamma(u,v) = (M\_{tangent}^{-1})^{\mathrm{T}} \lbrace \frac{\partial}{\partial u} (\alpha h), \frac{\partial}{\partial v} (\alpha h), 0 \rbrace. $$

#### 2. Combining Height Maps

Due to linearity, combining height maps is straightforward:

$$ \tag{31} \alpha \Gamma\_{1}(u,v) + \beta \Gamma\_{2}(u,v) = (M\_{tangent}^{-1})^{\mathrm{T}} \lbrace \frac{\partial}{\partial u} (\alpha h_1 + \beta h_2) , \frac{\partial}{\partial v}(\alpha h_1 + \beta h_2), 0 \rbrace. $$

#### 3. Object-Space Normals

Object-space normals are considered *already perturbed*, e.i. they do not depend on the normal of the surface. Nevertheless, it's convenient to process them within the same framework. It's worth keeping in mind that object-space normals retrieved from a texture should be affected by the object's scale.

$$ \tag{32} N_p(u,v) = \frac{(\mathrm{cof}(M\_{scale})) N_o}{ \Vert (\mathrm{cof}(M\_{scale})) N_o \Vert }, $$

$$ \tag{33} N_o(u,v) = \frac{N - \Gamma}{ \Vert N - \Gamma \Vert }. $$

While we can solve this equation by projecting the object-space normal onto the tangent plane using equations (25) and (28), it's more efficient to find the solution geometrically:

{{< figure src="/img/grad_obj.png" alt="Surface gradient and the object-space normal." >}}

$$ \tag{34} \Gamma(u,v) = N - (N - \Gamma) = N - G_o = N -\frac{N_o}{( N_o \boldsymbol{\cdot} N)}. $$

This formula will give correct results even for negative values of the dot product (just make sure not to divide by 0). It is also independent from the surface parametrization -- the way we compute texture coordinates does not matter.

The equation (34) can be generalized to work with arbitrary object-space gradients. Using the same geometric intuition, projection onto the tangent plane can be performed in the following way:

$$ \tag{35} \Gamma(u,v) =  (G_o \boldsymbol{\cdot} N) N - G_o. $$

#### 4. Planar Mapping

One of the alternatives to UV-mapping is to obtain texture coordinates by projecting the mesh onto a plane.

We will consider two types of normals stored within the normal map -- object-space normals and tangent-space normals. The solution for object-space normals is given in the previous section. Here, we will find a solution for tangent-space normals.

Without loss of generality, let's consider the X-Y plane for planar mapping. Our surface \\(S\\) then becomes a height map w.r.t. this plane (we assume that this is a suitable parametrization for the particular surface):

$$ \tag{36} z = z(x, y), $$
$$ \tag{37} \lbrace u,v \rbrace = \lbrace x,y \rbrace. $$

Let's complete the tangent frame for the new surface parametrization:

$$ \tag{38} T(u,v) = \frac{\partial S}{\partial u} = \lbrace 1, 0, \frac{\partial z}{\partial u} \rbrace, \quad B(u,v) = \frac{\partial S}{\partial v} = \lbrace 0, 1, \frac{\partial z}{\partial v} \rbrace. $$

Recall the equation (11) which relates the height map gradient and the height map normal:

$$ \tag{39} T(u,v) = \lbrace 1, 0, -\frac{n\_{x}}{n\_{z}} \rbrace, \quad B(u,v) = \lbrace 0, 1, -\frac{n\_{y}}{n\_{z}} \rbrace. $$

This completes the TBN for the new surface parametrization, and we can now use the equation (28) to compute the surface gradient: (flip???)

$$ \tag{40} \Gamma(u,v) = -\frac{[B \times N | N \times T | 0] }{(T \times B) \boldsymbol{\cdot} N} G, $$

$$ \tag{41} \Gamma(u,v) = -\frac{[\lbrace n_y^2/n_z+n_z, -n_x n_y/n_z, -n_x \rbrace | \lbrace -n_x n_y/n_z, n_x^2/n_z+n_z, -n_y \rbrace | 0] }{1 / \sqrt{n_z^2}} G, $$

$$ \tag{42} \Gamma(u,v) = -\mathrm{sign}(n_z) [\lbrace n_y^2+n_z^2, -n_x n_y, -n_x n_z \rbrace | \lbrace -n_x n_y, n_x^2+n_z^2, -n_y n_z \rbrace | 0] G, $$


#### 5. Tri-Planar Mapping

...
