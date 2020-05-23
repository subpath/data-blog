---
layout: post
title:  "Least Squares GAN "
date:   2018-09-12 13:18:29 +0500
permalink: "/least-squares-gan/"
---

## This week GAN of the week is a Least Squares GAN

Least Squares GAN is similar to [DCGAN](https://medium.com/cindicator/introduction-to-the-gan-of-the-week-e271e71ab8ff) but it is using different loss functions for Discriminator and for Generator, this adjustment allows increasing the stability of learning in comparison to [vanilla GAN](https://subpath.github.io/data-blog//vanilla-gan)

So the loss functions look like this:

![formula-1]({{ site.url }}/{{ site.baseurl }}/assets/2018-09-12-least-squares-gan/formula-1.png)

*Discriminator loss function*

![formula-2]({{ site.url }}/{{ site.baseurl }}/assets/2018-09-12-least-squares-gan/formula-2.png)

*Generator loss function*

![picture-1]({{ site.url }}/{{ site.baseurl }}/assets/2018-09-12-least-squares-gan/picture-1.png)

*The illustrations of different behaviors of two loss functions. (a) Decision boundaries of two loss functions. Note that the decision boundary should go across the real data distribution for successful GANs learning. Otherwise, the learning process is saturated. (b) The decision boundary of the sigmoid cross entropy loss function. It gets very small errors for the fake samples (in magenta) for updating G as they are on the correct side of the decision boundary. From the [original paper](https://arxiv.org/pdf/1611.04076.pdf).*

## Results

I made LSGAN implementation with PyTorch, the code can be found on my [GitHub](https://github.com/subpath/LSGAN-with-PyTorch). In order to improve stability, you can try to play with hyperparameters that can be found in config.toml.

I’ve tried to generate emoji and got pretty creepy results, especially if you look on the smiley face.

{:refdef: style="text-align: center;"}
![gan-emoji]({{ site.url }}/{{ site.baseurl }}/assets/2018-09-12-least-squares-gan/gan-emoji.gif)
{: refdef}

{:refdef: style="text-align: center;"}
*Generating creepy emoji*
{: refdef}

## References:

LSGAN original paper — [https://arxiv.org/abs/1611.04076](https://arxiv.org/abs/1611.04076)

My PyTorch LSGAN implementation — [https://github.com/subpath/LSGAN-with-PyTorch](https://github.com/subpath/LSGAN-with-PyTorch)