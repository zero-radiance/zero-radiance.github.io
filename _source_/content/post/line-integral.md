---
title: "Line Integrals and the Chapman Function"
date: 2019-06-23T17:53:34-07:00
draft: true
categories: [ "Graphics", "Math" ]
tags: [
    "Atmospheric Scattering",
    "Chapman Function",
    "Volume Rendering",
    ]
---

Rendering of participating media is an important aspect of every modern renderer. And it's not fog, fire and smoke - everything is volumetric. All matter is composed of [atoms](https://en.wikipedia.org/wiki/Atom), which can be sparsely or densely packed in space. Whether you consider the particle or the wave nature of [light](https://en.wikipedia.org/wiki/Light), it penetrates all matter to a certain degree (yes, even [metals](http://webhome.phy.duke.edu/~qelectron/group/group_reading_Born_and_Wolf.pdf)).

Light-material interaction is usually quantified in terms of absorption (reduction of intensity) and scattering (change of direction). Therefore, it is common to describe participating media using its absorption coefficient \\(\sigma_a\\) and the scattering coefficient \\(\sigma_s\\). They give the probability of light being absorbed (or scattered, respectively) as it travels 1 meter through the volume, which means that the coefficients are measured in units of \\(m^{-1}\\). Their sum is the extinction (or [attenuation](https://en.wikipedia.org/wiki/Attenuation_coefficient)) coefficient \\(\sigma_t\\), which gives the probability of absorption or out-scattering.

Mass attenuation coefficient ...


<!--more-->

Goodbye