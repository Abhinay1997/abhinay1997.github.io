---
layout: post
title: Intuitions on Autoencoders
description: A dive into autoencoders with practical examples of their applications. Colab included.
---

An Autoencoder is a machine learning model that can be broken down into two parts. An encoder network and a decoder network. The encoder network takes some input data and converts it into an intermediate representation a.k.a an encoding. The decoder network takes this intermediate representation and rebuilds the input data from it.

Usually the input data lies in a higher dimensional space and the encoding in a lower dimensional space.

For example, take a picture of a cat. The image would be a 3d array of pixel values while the encoding can be as simple as "Picture of a white cat". This encoding when passed to the decoder network would then be capable of reproducing the original picture of the cat.

<!-- Insert picture of cat , encoder, encoding, decoder and rebuilt cat picture. -->

One thing to note is that because we are compressing information from a higher dimensional space to a lower dimensional space, data loss is inevitable. So when an image is passed through an autoencoder, the rebuilt image would not be the exact same as the original.

Also, autoencoders trained on a specific data distribution perform best on similar data. For example an autoencoder trained on images of cats would perform poorly when encountering pictures of trees.

Intution about the encoding:-

When we spoke about the encoding, we discussed how it lies in a lower dimensional space. Let's say we have pictures of 3 cats: each White, Black and Grey and let's say that the encoder learnt to represent them as the encodings (-1,0), (1,0) and (2,0) in the latent space. Now the encoding (0,0) is also part of the same latent space and so logically the decoder should be able to make sense of it. Passing the (0,0) to the decoder would likely result in a Brown cat.

To conclude on this:-
1. The encoder learns to map all the input data to points in a latent space.
2. The decoder learns to convert any point in the latent space to a data sample that would be similar to the input data distribution.

How does an autoencoder learn ? 

The way we train the autoencoder is that we chain the encoder and decoder, so our input data and the expected output data would be the same.

For the case of our Cats autoencoder, we pass in images of Cats and penalize it depending on how different the output images are when compared to the originals. Over time, the encoder and decoder come up with an encoding that ensures that each and every image of Cat in the input dataset can adequately match the original.

Training an autoencoder on MNIST Fashion

Visualizing the latent space

Sampling the latent space

Denoising Images

Superresolution

