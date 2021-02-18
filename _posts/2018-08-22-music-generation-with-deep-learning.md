---
layout: post
title:  "Music generation with Deep Learning"
date:   2018-08-22 12:00:00 +0500
permalink: "/music-generation-with-deep-learning/"
tags:
- 
  name: GAN
  style: warning
-
  name: ML
  style: success
---

> GAN of the Week is a series of notes about Generative Models, including GANs and Autoencoders. Every week I’ll review a new model to help you keep up with these rapidly developing types of Neural Networks.

## This week GAN of the week is a [C-RNN-GAN](http://mogren.one/publications/2016/c-rnn-gan/mogren2016crnngan.pdf)

C-RNN-GAN is a continuous recurrent neural network with adversarial training that contains LSTM cells, therefore it works very well with continuous time series data, for example, music files!

![c-rnn-gan]({{ '/assets/2018-08-22-music-generation-with-deep-learning/c-rnn-gan.png' | relative_url }})

*Structure of Discriminator and Generator in C-RNN-GAN (picture from original paper)*

## How C-RNN-GAN works?

C-RNN-GAN is a recurrent neural network with adversarial training. The adversaries are two different deep recurrent neural models, a generator (G) and a discriminator (D). The generator is trained to generate data that is indistinguishable from real data, while the discriminator is trained to identify the generated data. The training becomes a zero-sum game for which the Nash equilibrium is when the generator produces data that the discriminator cannot tell from real data. We define the following loss functions LD and LG:

![formula]({{ '/assets/2018-08-22-music-generation-with-deep-learning/formula1.png' | relative_url }})

*where z (i) is a sequence of uniform random vectors in [0, 1] k , and x (i) is a sequence from the training data. k is the dimensionality of the data in the random sequence.*

C-RNN-GAN uses feature matching, an approach to encourage greater variance in G, and avoid overfitting to the current discriminator by replacing the standard generator loss, LG. Normally, the objective for the generator is to maximize the error that the discriminator makes, but with feature matching, the objective is instead to produce an internal representation at some level in the discriminator that matches that of real data.

![c-rnn-gan-loss]({{ '/assets/2018-08-22-music-generation-with-deep-learning/c-rnn-gan-loss.png' | relative_url }})

*Statistics of generated music from the evaluated models (picture from the original paper)*

## Music generation with C-RNN-GAN

Right! So the purpose of this GAN is to generate music, let’s try how it works!
I used code from this GitHub repository: [https://github.com/olofmogren/c-rnn-gan/](https://github.com/olofmogren/c-rnn-gan/) which is based on original paper.

**This is the results I was able to achieve:**

<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/487959168&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>

And this is what was published by authors of the [original paper](http://mogren.one/publications/2016/c-rnn-gan/).

<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/487958301&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>

**Well, this does not sound like something a human can call music, but you can say the same thing about many of modern artists!**

## Music generated with simple Recurrent Network:
In a contrast to that, here is the track I was able to generate with a simple recurrent network. Even though it still does not sound like music, my RNN definitely have more expression!


<iframe width="100%" height="300" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/487957746&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true&visual=true"></iframe>

I wrote this RNN while ago, my code can be found here: [https://github.com/subpath/Keras_music_gereration](https://github.com/subpath/Keras_music_gereration)

## In conclusion:

GAN is famous for being a type of networks that is particularly hard to train if you are trying to achieve some impressive results.

Even though I like the idea of GAN, I prefer my version generated with RNN — the model is much smaller and the code is cleaner.

In contrast with GAN that generates picture, models for music still need to be developed a lot in order to generate something similar to music that was made by humans.

## References:

1. Original paper: [http://mogren.one/publications/2016/c-rnn-gan/mogren2016crnngan.pdf](http://mogren.one/publications/2016/c-rnn-gan/mogren2016crnngan.pdf)

2. Code from GitHub: [https://github.com/olofmogren/c-rnn-gan/](https://github.com/olofmogren/c-rnn-gan/)

3. My code with RNN for music: [https://github.com/subpath/Keras_music_gereration](https://github.com/subpath/Keras_music_gereration)