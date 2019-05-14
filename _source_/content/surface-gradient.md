---
hidden: true
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

The surface gradient framework is a set of tools to transform and combine normal (or bump) maps originally introduced by Morten Mikkelsen in his 2010 paper titled ["Bump Mapping Unparametrized Surfaces on the GPU"
](https://www.dropbox.com/s/l1yl164jb3rhomq/mm_sfgrad_bump.pdf), and further extended to handle triplanar mapping in his subsequent [blog post](http://mmikkelsen3d.blogspot.com/2013/10/volume-height-maps-and-triplanar-bump.html). While there's nothing wrong with these two publications, to really understand what's going on, it really helps to also read his [thesis](http://image.diku.dk/projects/media/morten.mikkelsen.08.pdf), and at 109 pages, it's quite a time investment.

Instead, I will attempt to give a shorter derivation, assuming no prior knowledge of the topic, starting from the the first principles. Still with me? Let's dive in.

<!--more-->

## Preliminaries, Part 1: Tangent Frame

We start with a 3D surface \\(S\\). Assume that it has a unique 2D parametrization, e.i. an injective (one to one) mapping from a 2D texture coordinate to an object-space 3D coordinate:

$$ \tag{1} S(u,v) = \lbrace x,y,z \rbrace. $$

You can think of it as a transformation from a 2D plane to a 3D surface. The injectivity requirement is differentiability in disguise, the property we will heavily lean on during the derivation.

Application of an affine transformation yields a set of transformed texture coordinates, which is something that's often done to tile and translate the texture at runtime:

$$ \tag{2} \lbrace s,t \rbrace = \lbrace au+c, bv+d \rbrace. $$

In our notation, we will always use \\(\lbrace u,v \rbrace\\) for unique values and \\(\lbrace s,t \rbrace\\) for scaled-translated ones. Note that we don't care about a particular surface parametrization: we can use UV-mapping, planar mapping, or something else entirely.

Armed with the surface parametrization, we can compute the tangent and the bitangent at the surface point:

$$ \tag{3} T(u,v) = \frac{\partial S}{\partial u}, \quad B(u,v) = \frac{\partial S}{\partial v}. $$

These two vectors span the tangent plane, and are typically neither mutually orthogonal nor of unit length. It's important to remember that they are not independent from the surface parametrization.

We can use them to compute the geometric normal of the surface. To make the normal independent from the surface parametrization, it should be normalized:

$$ \tag{4} N(u,v) = \frac{T \times B}{\Vert T \times B \Vert}. $$

This completes the tangent frame, which can be represented as a (scaled) tangent-to-object-space matrix:

$$ \tag{5} M\_{tangent}(u,v) = [T | B | N]. $$

We will need the inverse of the matrix going forward:

$$ \tag{6} \begin{aligned}
    (M\_{tangent}^{-1}(u,v))^{\mathrm{T}} = \frac{\mathrm{cof}(M\_{tangent})}{\mathrm{det}(M\_{tangent})}
    &= \frac{[B \times N | N \times T | T \times B]}{\langle T \times B, N \rangle} \cr
    &= \Bigg[\frac{B \times N}{\langle T \times B, N \rangle} \Bigg| \frac{N \times T}{\langle T \times B, N \rangle} \Bigg| N\Bigg].
\end{aligned} $$

It's important to note that, in practice, the surface parametrization used to derive the normal may be different from the texture coordinate parametrization. This means that it's possible to encounter a mesh with vertex normals \\(N\\) pointing in the direction opposite from \\((T \times B)\\). Of course, we always want to use the forward-facing normal \\(N\\). Luckily, the math is on our side, and using the full matrix inversion procedure as described above works in all cases. More details about surface re-parametrization can be found in Morten's [thesis](http://image.diku.dk/projects/media/morten.mikkelsen.08.pdf) (see Section 2.4).

## Preliminaries, Part 2: Height Maps

The simplest representation of a tangent-space normal (or bump) map is a height map \\(h = h(s,t)\\). A height map is a scalar field. The *gradient of a scalar field* is a vector field which points in the direction of the greatest rate of increase of the scalar field, with the magnitude corresponding to this rate:

$$ \tag{7} \nabla h(s,t) = \frac{\partial h}{\partial s} I + \frac{\partial h}{\partial t} J = \Big\lbrace \frac{\partial h}{\partial s}, \frac{\partial h}{\partial t} \Big\rbrace, $$

where \\({\partial h}/{\partial s}\\) and \\({\partial h}/{\partial t}\\) are the slopes of the height map.

Geometrically, a height map defines an implicit surface of the form \\(z=z(x,y)\\), or

$$ \tag{8} f(x,y,z) = z - z(x,y) = 0. $$

The direction of the height map normal is given by the *gradient of the implicit surface*:

$$ \tag{9} \nabla f(x,y,z) = \frac{\partial f}{\partial x} I + \frac{\partial f}{\partial y} J + \frac{\partial f}{\partial z} K, $$

where \\(\lbrace I,J,K \rbrace\\) is the set of orthonormal basis vectors. Substitution of the height map equation readily yields the following expression:

$$ \tag{10} G(s,t) = K - \frac{\partial h}{\partial s} I - \frac{\partial h}{\partial t} J = \Big\lbrace -\frac{\partial h}{\partial s}, -\frac{\partial h}{\partial t}, 1 \Big\rbrace, $$

$$ \tag{11} G(s,t) = K - \nabla h. $$

To avoid confusion with the *surface gradient*, we'll refer to \\(G\\) as the *height map gradient*.

It's convenient to get the re-parametrization induced by transformed texture coordinates out of the way:

$$ \tag{12} G(u,v) = \Big\lbrace -\frac{\partial h}{\partial s} \frac{\partial s}{\partial u}, -\frac{\partial h}{\partial t} \frac{\partial t}{\partial v}, 1 \Big\rbrace = \Big\lbrace -a \frac{\partial h}{\partial s}, -b \frac{\partial h}{\partial t}, 1 \Big\rbrace. $$

This simply means that we need to rescale the slopes in order to adjust the displacements as we tile the texture.

For completeness, we can convert a tangent-space normal into the slope (gradient) form in the following way:

$$ \tag{13} G(s,t) = \Big\lbrace - \frac{\partial h}{\partial s}, - \frac{\partial h}{\partial t}, 1 \Big\rbrace = \frac{N\_{t}}{n\_{t,z}}, $$

$$ \tag{14} G(u,v) = \Big\lbrace -\frac{\partial h}{\partial s} \frac{\partial s}{\partial u}, -\frac{\partial h}{\partial t} \frac{\partial t}{\partial v}, 1 \Big\rbrace = \Big\lbrace a \frac{n\_{t,x}}{n\_{t,z}}, b \frac{n\_{t,y}}{n\_{t,z}}, 1 \Big\rbrace = M\_{tile} \frac{N\_{t}}{n\_{t,z}}. $$

To perturb the surface using the height map, we need to perform a change of basis from the orthonormal tangent-space set \\(\lbrace I,J,K \rbrace\\) to our previously defined object-space set \\(\lbrace T,B,N \rbrace\\). Doing this correctly is slightly more involved than it may seem at first glance.

## Preliminaries, Part 3: Wrinkled Surfaces

The [original formulation](https://www.microsoft.com/en-us/research/publication/simulation-of-wrinkled-surfaces/) of bump mapping defines a height-mapped surface by applying a perturbation along the normal of the original surface. Note that this is the right place to apply the object's scale matrix, as it should affect the displacements (ultimately, by scaling the slopes):

$$ \tag{15} P(u, v) = M\_{scale} \Big( S(u, v) + h(u, v) N(u, v) \Big). $$

Just as we did before, we can take derivatives of this equation to compute the perturbed normal.

$$ \tag{16} T_p(u,v) = \frac{\partial P}{\partial u}
                     = M\_{scale}(T + \frac{\partial h}{\partial u} N + h \frac{\partial N}{\partial u})
                     \approx M\_{scale}(T + \frac{\partial h}{\partial u} N), $$
$$ \tag{17} B_p(u,v) = \frac{\partial P}{\partial v}
                     = M\_{scale}(B + \frac{\partial h}{\partial v} N + h \frac{\partial N}{\partial v})
                     \approx M\_{scale}(B + \frac{\partial h}{\partial v} N). $$

Jim Blinn claims that for small displacements and moderately curved surfaces, the last term is negligibly small and can be set to 0. For those interested, Morten verifies that this is indeed the case in his [thesis](http://image.diku.dk/projects/media/morten.mikkelsen.08.pdf) (see Section 2.6).

We can compute the perturbed normal by taking the cross product. Just as before, we replace normalization in the denominator with the scalar triple product to avoid flipped normals:

$$ \tag{18} N_p(u,v) = \frac{T_p \times B_p}{\langle T_p \times B_p, N \rangle}
                     \approx \frac{1}{\langle T_p \times B_p, N \rangle} \Big((M\_{scale}(T + \frac{\partial h}{\partial u} N)) \times
                     ( M\_{scale}(B + \frac{\partial h}{\partial v} N)) \Big). $$

We can move the matrix outside of the brackets with the help of the following [identity](https://en.wikipedia.org/wiki/Cross_product#Algebraic_properties):

$$ \tag{19} (MA) \times (MB) = (\mathrm{det}(M))(M^{-1})^{\mathrm{T}}(A \times B)
                             = (\mathrm{cof}(M)) (A \times B), $$

$$ \tag{20} N_p(u,v) \approx \frac{\mathrm{cof}(M\_{scale})}{\langle T_p \times B_p, N \rangle} \Big((T + \frac{\partial h}{\partial u} N) \times (B + \frac{\partial h}{\partial v} N)\Big). $$

It's instructive to expand the cross product:

$$ \tag{21} N_p(u,v) \approx \frac{\mathrm{cof}(M\_{scale})}{\langle T_p \times B_p, N \rangle} \Big((T \times B) + \frac{\partial h}{\partial u} (N \times B) + \frac{\partial h}{\partial v} (T \times N) + \frac{\partial h}{\partial u} \frac{\partial h}{\partial v} (N \times N)\Big). $$

The last term vanishes because the value of the cross product is 0:

$$ \tag{22} N_p(u,v) \approx \frac{\mathrm{cof}(M\_{scale})}{\langle T_p \times B_p, N \rangle} \Big((T \times B) + \frac{\partial h}{\partial u} (N \times B) + \frac{\partial h}{\partial v} (T \times N)\Big). $$

If we swap the operands of cross products, this equation will take a familiar form:

$$ \tag{23} N_p(u,v) \approx \frac{\langle T \times B, N \rangle}{\langle T_p \times B_p, N \rangle} \mathrm{cof}(M\_{scale}) \frac{[B \times N | N \times T | T \times B]}{\langle T \times B, N \rangle} \Big\lbrace -\frac{\partial h}{\partial u}, -\frac{\partial h}{\partial v}, 1 \Big\rbrace, $$

$$ \tag{24} N_p(u,v) \approx \frac{\langle T \times B, N \rangle}{\langle T_p \times B_p, N \rangle} \mathrm{cof}(M\_{scale}) (M\_{tangent}^{-1})^{\mathrm{T}}G. $$

The first term of the equation (24) can be a bit surprising. Intuitively, it accounts for the change of volume from the old to the new coordinate frame. Since it's a non-negative scalar (negative signs of dot products, if present, cancel out), it does not modify the direction of the normal, and we can take care of it by normalizing the resulting vector.

The important part is, since normals are [bivectors](https://en.wikipedia.org/wiki/Bivector#Geometric_interpretation), they are transformed by the inverse-transpose of the matrix. Conversely, projection of an (unscaled) object-space normal into the tangent space is performed using the transpose:

$$ \tag{25} N_o(u,v) = \frac{(M\_{tangent}^{-1})^{\mathrm{T}} G}{\Vert (M\_{tangent}^{-1})^{\mathrm{T}} G \Vert}, $$

$$ \tag{26} \frac{(M\_{tangent})^{\mathrm{T}} N_o}{\Vert (M\_{tangent})^{\mathrm{T}} N_o \Vert} = (M\_{tangent})^{\mathrm{T}} (M\_{tangent}^{-1})^{\mathrm{T}} G = (M\_{tangent}^{-1} M\_{tangent})^{\mathrm{T}} G = G. $$

"This seems quite expensive", you may say. Does correctness come at a high cost? Not really.

Let's say we just have a tangent-space normal map, which we would like to tile and perhaps scale, and that's it. To compute the perturbed normal in the world space, all we have to do is the following:

$$ \tag{27} N_w(u,v) \approx \frac{\big( M\_{rot} (\mathrm{cof}(M\_{scale})) (M\_{tangent}^{-1})^{\mathrm{T}} \big) M\_{tile} N\_{t} / n\_{t,z}}{\Vert \big( M\_{rot} (\mathrm{cof}(M\_{scale})) (M\_{tangent}^{-1})^{\mathrm{T}} \big) M\_{tile} N\_{t} / n\_{t,z} \Vert}, $$

$$ \tag{28} N_w(u,v) \approx \frac{M\_{world} M\_{tile} N\_{t}}{\Vert M\_{world} M\_{tile} N\_{t} \Vert}. $$

The tangent-normal-to-world matrix \\(M\_{world}\\) can be precomputed, and all that's left to do at runtime is a few multiplications and one normalization.

## Surface Gradient Framework

The [surface gradient](https://en.wikipedia.org/wiki/Surface_gradient) is defined as the orthographic projection of the height map gradient onto the tangent plane:

$$ \tag{29} \Gamma(u,v) = (M\_{tangent}^{-1})^{\mathrm{T}} \Big\lbrace \frac{\partial h}{\partial u}, \frac{\partial h}{\partial v}, 0 \Big\rbrace = -\frac{[B \times N | N \times T] }{\langle T \times B, N \rangle} \nabla h, $$

$$ \tag{30} \Gamma(u,v) = N - (M\_{tangent}^{-1})^{\mathrm{T}} G. $$

By definition, it is a vector in the tangent plane with the direction pointing up along the steepest slope and the magnitude of the rate at which height increases in that direction. Note that the equation (30), as well as the preceding derivation, assume that \\(\langle N,G \rangle = 1\\). If that's not the case, the height map gradient must be rescaled.

Given the surface gradient, it's easy to obtain ("resolve") the perturbed normal:

$$ \tag{31} N_p(u,v) = \frac{(\mathrm{cof}(M\_{scale}))(N - \Gamma)}{\Vert (\mathrm{cof}(M\_{scale}))(N - \Gamma) \Vert}. $$

Why is it useful? Since it's just a matrix transformation, the surface gradient is a linear operator, which, combined with the fact that the derivative is a linear operator as well, makes many common operations straightforward. Additionally, the surface gradient, like the surface normal, is independent from the surface parametrization. And finally, the surface gradient is compact, since we do not need to retain the entire tangent frame.

#### 1. Rescaling the Height Map

Want to flatten the normal map, or make it more bumpy? Simply rescale the surface gradient:

$$ \tag{32} \alpha \Gamma(u,v) = (M\_{tangent}^{-1})^{\mathrm{T}} \Big\lbrace \frac{\partial}{\partial u} (\alpha h), \frac{\partial}{\partial v} (\alpha h), 0 \Big\rbrace. $$

#### 2. Combining Height Maps

Due to linearity, combining height maps is straightforward:

$$ \tag{33} \alpha \Gamma\_{1}(u,v) + \beta \Gamma\_{2}(u,v) = (M\_{tangent}^{-1})^{\mathrm{T}} \Big\lbrace \frac{\partial}{\partial u} (\alpha h_1 + \beta h_2) , \frac{\partial}{\partial v}(\alpha h_1 + \beta h_2), 0 \Big\rbrace. $$

Clearly, linear blends of surface gradients are more meaningful than blends of normals themselves.

#### 3. Object-Space Normals

Object-space normals are considered *already perturbed*, e.i. they do not depend on the normal of the surface. Nevertheless, it's convenient to process them within the same framework. It's worth keeping in mind that object-space normals retrieved from a texture should be affected by the object's scale:

$$ \tag{34} N_p(u,v) = \frac{(\mathrm{cof}(M\_{scale})) N_o}{ \Vert (\mathrm{cof}(M\_{scale})) N_o \Vert }, $$

$$ \tag{35} N_o(u,v) = \frac{G_o}{ \Vert G_o \Vert } = \frac{N - \Gamma}{ \Vert N - \Gamma \Vert }. $$

It's interesting to compare the last equation with the equation (11), and to see that they indeed match (up to the normalization factor).

While we can solve this equation by projecting the object-space normal onto the tangent plane using the equations (26) and (29), it's more efficient to find the solution geometrically:

{{< figure src="/img/grad_obj.png" alt="Surface gradient and the object-space normal." >}}

$$ \tag{36} \Gamma(u,v) = N - (N - \Gamma) = N -\frac{N_o}{\langle N_o, N \rangle}. $$

This formula rescales the normal to obtain the corresponding height map gradient which satisfied the condition \\(\langle N,G \rangle = 1\\), and produces correct results even for negative values of the dot product, as well as non-unit normals \\(N_o\\). Since we only deal with normals and the surface gradient, this formulation is also independent from the surface parametrization -- the way we compute the texture coordinates does not matter.

#### 4. Planar Mapping

One of the alternatives to UV-mapping is to obtain texture coordinates by projecting the surface onto a plane. Typically, the normals are stored in one of the three spaces: object space, plane space (spanned by the principal axes of the plane and their normalized cross product), or tangent space.

The solution for the already perturbed (e.g. object-space or plane-space) normals is given in the previous section. In fact, normals can be in any space, as long as both \\(N\\) and \\(N_o\\) are represented with respect to the same frame; due to the independence from the surface parametrization, everything just works. The only subtlety is accounting for tiling of plane-space normals, which can be achieved as per equation (12).

Let's find the solution for tangent-space normals. Without loss of generality, let's consider the X-Y plane for planar mapping. Our surface \\(S\\) then becomes a height map w.r.t. this plane (we assume that this is a suitable parametrization for the particular surface):

$$ \tag{37} z = z(x, y), $$
$$ \tag{38} \lbrace u,v \rbrace = \lbrace x,y \rbrace. $$

Let's complete the tangent frame for the new surface parametrization:

$$ \tag{39} T(u,v) = \frac{\partial S}{\partial u} = \Big\lbrace 1, 0, \frac{\partial z}{\partial u} \Big\rbrace, \quad B(u,v) = \frac{\partial S}{\partial v} = \Big\lbrace 0, 1, \frac{\partial z}{\partial v} \Big\rbrace. $$

Recall the equation (13) which relates the height map gradient and the height map normal (which, in our interpretation, is the surface normal). We can use it to compute the values of partial derivatives:

$$ \tag{40} T(u,v) = \Big\lbrace 1, 0, -\frac{n\_{x}}{n\_{z}} \Big\rbrace, \quad B(u,v) = \Big\lbrace 0, 1, -\frac{n\_{y}}{n\_{z}} \Big\rbrace. $$

This completes the TBN for the new surface parametrization, and we can now use the equation (29) to compute the surface gradient:

$$ \tag{41} \Gamma(u,v) = -\frac{[\lbrace n_y^2/n_z+n_z, -n_x n_y/n_z, -n_x \rbrace | \lbrace -n_x n_y/n_z, n_x^2/n_z+n_z, -n_y \rbrace] }{(n_x^2 + n_y^2 + n_z^2) / n_z} \nabla h, $$

$$ \tag{42} \Gamma(u,v) = -[\lbrace n_y^2+n_z^2, -n_x n_y, -n_x n_z \rbrace | \lbrace -n_x n_y, n_x^2+n_z^2, -n_y n_z \rbrace] \nabla h. $$

TODO: is there another way to derive this? Maybe perform some kind of change of variables?

#### 5. Tri-Planar Mapping

Tri-planar mapping is an efficient alternative to storing already perturbed normals in a cube map, or using an octahedral or lat-long projection. Instead, the same 2D texture is reused three times for [X-Y, Y-Z and Z-X planes](http://www.slideshare.net/icastano/cascades-demo-secrets). You can think of it as a cube map with all 6 faces of the cube using the same planar map. The same normal map spaces supported by planar mapping are supported here. In fact, the math is almost exactly the same - the only difference is that you have to do it (up to) three times, and blend the results using the weight function which depends on the direction of the surface normal.

But how do we blend the normals? If we convert them into the surface gradient form, blending becomes simple and meaningful. The only remaining catch is that the resulting gradient



Using object-space normals with tri-planar mapping is relatively straightforward. All we have to do is blend three surface gradients (one per plane) obtained from object-space normals using the equation (36):

$$ \tag{43} \Gamma(x,y,z) = N - w(x,y) \frac{N_1}{\langle N_1, N \rangle}
                              - w(y,z) \frac{N_2}{\langle N_2, N \rangle}
                              - w(z,x) \frac{N_3}{\langle N_3, N \rangle}, $$

where I labeled the normals of X-Y, Y-Z and Z-X planes as \\(N_1\\), \\(N_2\\) and \\(N_3\\), respectively.

Handling plane-space normals is conceptually quite similar. Let's examine the solution presented by Morten in his [blog post](http://mmikkelsen3d.blogspot.com/2013/10/volume-height-maps-and-triplanar-bump.html).

Imagine a height map defined on a sphere:

$$ \tag{44} h(x,y,z) = w(x,y) h(x,y) + w(y,z) h(y,z) + w(z,x) h(z,x), $$

where \\(w\\) is the weight function. Recalling the definition of the gradient of a scalar field, and considering the equation (11) should convince you that the gradient of this height field is the surface gradient in this case:

$$ \tag{45} \begin{aligned}
\Gamma(x,y,z) = \nabla h(x,y,z)
    &= \frac{\partial f}{\partial x} X + \frac{\partial f}{\partial y} Y + \frac{\partial f}{\partial z} K \cr
    &= X \frac{\partial}{\partial x} (w(x,y) h(x,y) + w(z,x) h(z,x)) \cr
    &+ Y \frac{\partial}{\partial y} (w(x,y) h(x,y) + w(y,z) h(y,z)) \cr
    &+ Z \frac{\partial}{\partial z} (w(y,z) h(y,z) + w(z,x) h(z,x)).
\end{aligned} $$

The expression is a little complicated, particularly because both the height and the weight functions depend on the coordinates, so we would need to apply the product rule in order to compute the derivative. Instead, Morten employs a simplification, assuming that the weights vary linearly (and uniformly) w.r.t. the coordinates:

$$ \tag{46} \frac{\partial w(x,y)}{\partial x} = \frac{\partial w(z, x)}{\partial x} = \frac{\partial w(x,y)}{\partial y} = \frac{\partial w(y,z)}{\partial y} = \frac{\partial w(y,z)}{\partial z} = \frac{\partial w(z,x)}{\partial z} = k. $$

It acts as a simple scaling factor for the height map. This considerably simplifies the initial equation:

$$ \tag{47} \begin{aligned}
\Gamma(x,y,z)
    &= k X \Big(w(x,y) \frac{\partial h(x,y)}{\partial x} + w(z,x) \frac{\partial h(z,x)}{\partial x} \Big) \cr
    &+ k Y \Big(w(x,y) \frac{\partial h(x,y)}{\partial y} + w(y,z) \frac{\partial h(y,z)}{\partial y} \Big) \cr
    &+ k Z \Big(w(y,z) \frac{\partial h(y,z)}{\partial z} + w(z,x) \frac{\partial h(z,x)}{\partial z} \Big).
\end{aligned} $$

We can compute the height derivatives by converting normals from the normal map into the slope using the equation (13):

$$ \tag{48} \begin{aligned}
\Gamma(x,y,z)
    &= k X \Big(-w(x,y) \frac{n\_{1,x}}{n\_{1,z}} -w(z,x) \frac{n\_{3,x}}{n\_{3,y}} \Big) \cr
    &+ k Y \Big(-w(x,y) \frac{n\_{1,y}}{n\_{1,z}} -w(y,z) \frac{n\_{2,y}}{n\_{2,x}} \Big) \cr
    &+ k Z \Big(-w(y,z) \frac{n\_{2,z}}{n\_{2,x}} -w(z,x) \frac{n\_{3,z}}{n\_{3,y}} \Big).
\end{aligned} $$

As a sanity check, we can compute our result to the blend of three surface gradients obtained using the equation (35):

## Conclusion

## Acknowledgments

I would like to thank Jim Blinn and Morten Mikkelsen for reviewing this blog post, offering thoughtful comments and answering my questions.