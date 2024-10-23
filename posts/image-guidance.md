# Flux Image Guidance comparisons with different CFG-like methods

## TOC
1. [Introduction](#introduction)
2. [Guidance Embedding](#)
2. [CFG a.k.a True CFG](#chapter-1-getting-started)
3. [CFG++](#chapter-2-basic-concepts)
4. [SEG](#chapter-3-advanced-techniques)
5. [APG](#conclusion)
6. [PAG](#pag)

## Introduction
Classifier Free Guidance is a technique used to strengthen the effect of the prompt on the image generation of a diffusion model. Lets compare CFG with some alternative approaches that aim to do the same. All tests use Flux.1 Dev

## CFG a.k.a True CFG
CFG adds a weighted contribution of the conditional model output in comparision to the unconditional model output. The stronger the weight (guidance scale) \(w \ \epsilon \ [1, Inf]\) the more the prompt is strongly followed. However the image quality is affected.

Sweeping the guidance scale from 1 to 4.

## Guidance Embedding
Flux.1 Dev is a guidance distilled model which means that instead of using a conditional and unconditional model output during generation, it uses a guidance embedding to condition the image output. However in practice, its not a strong as True CFG.

Sweeping the guidance scale from 1 to 10

## CFG++
CFG++ as proposed in [CFG++: Manifold-constrained Classifier Free Guidance For Diffusion Models](https://arxiv.org/abs/2406.08070) argues that True CFG pushes the samples at every denoising step out of the diffusion manifolds. a.k.a the guidance weighting acts as an extrapolating term. CFG++ proposes to use a low guidance scale \(\lambda \ \epsilon \ [0, 1]\) and modify the sampling so that it acts more like an interpolation on the manifold itself.

One intersting consequence of this is that, increasing the number of denoising steps increases the strength of the guidance.

Sweeping the guidance scale from 0 to 1

## PAG
Perturbed Attention Guidance

Sweeping the scale and the layers its applied to

## SEG
Smoothed Energy Guidance as proposed in [Smoothed Energy Guidance: Guiding Diffusion Models with Reduced Energy Curvature of Attention](https://arxiv.org/abs/2408.00760.pdf)

Sweeping the scale and the layers its applied to

## TSG and ICG
Skipped but mentioned for completeness

## APG
Adaptive Projection Guidance as proposed in [Eliminating Oversaturation And Artifacts Of
High Guidance Scales In Diffusion Models](https://arxiv.org/pdf/2410.02416.pdf)

This can be applied on the model output or on the denoised sample at each timestep.

With and without cfg.

## Citations




