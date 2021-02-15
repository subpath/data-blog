---
layout: post
title:  "Vanilla GAN "
date:   2018-08-15 13:18:29 +0500
permalink: "/vanilla-gan/"
- 
  name: GAN
  style: primary
-
  name: ML
  style: success
---

For the sake of introduction let’s build the simplest GAN possible using Keras and TensorFlow. But, first…

## What is GAN anyway?

GAN — generative adversarial network was introduced by Ian Goodfellow in 2014 and they becoming more and more popular over time. On a high level, it’s a very cool concept where you have two neural networks competing with each other. One of them is taking random noise and try to generate something similar to the input data, a so-called Generator. Another is taking fake data generated from Generator and real input and learns to distinguish the difference between them, a so-called Discriminator. So during the learning process Generator became more and more skilful in generating fake data and Discriminator became better in the classification of real and fake sets of data. By doing that we can extract important features from the data.

{:refdef: style="text-align: center;"}
![gan-flow]({{ site.url }}/{{ site.baseurl }}/assets/2018-08-15-vanilla-gan/gan-flow.png)
{: refdef}

{:refdef: style="text-align: center;"}
*GAN flow*
{: refdef}

## Some of the math behind it

Generator and Discriminant are training separately but as part of the same neural network.

We are doing k-steps of Discriminator training in each step parameters changing in direction of reducing cross-entropy.


{:refdef: style="text-align: center;"}
![formula-1]({{ site.url }}/{{ site.baseurl }}/assets/2018-08-15-vanilla-gan/formula-1.png)
{: refdef}

{:refdef: style="text-align: center;"}
*where D is discriminator and G is a generator, Xs is an input data, Z is a sample of random noize from some normal distribution P(Z)*
{: refdef}


Then we’re updating parameters of the generator in a direction of increasing of the logarithm of the probability that Discriminator will choose fake data instead of real.

{:refdef: style="text-align: center;"}
![formula-2]({{ site.url }}/{{ site.baseurl }}/assets/2018-08-15-vanilla-gan/formula-2.png)
{: refdef}

{:refdef: style="text-align: center;"}
*where D is discriminator and G is a generator, Z is a sample of random noize from some normal distribution P(Z)*
{: refdef}


## What can we do with GAN?

Currently, GANs are very popular for image generation and video real-time processing, but the general idea is very fresh so new use cases coming out every week. In this series, we will try to explore them!

## Practical part

So as was promised let’s build the simplest GAN to generate images, we will use the [MNIST dataset](https://en.wikipedia.org/wiki/MNIST_database) (because it feels like there are not enough examples with the MNIST dataset). We will try much more interesting datasets in the future, I promise.

```python
import numpy as np
from keras.datasets import mnist
from keras.layers import Input, Dense, Reshape, Flatten, Dropout
from keras.layers import BatchNormalization
from keras.layers.advanced_activations import LeakyReLU
from keras.models import Sequential
from keras.optimizers import Adam
from logger import logger
import matplotlib.pyplot as plt

plt.switch_backend('agg') 

shape = (28, 28, 1)
epochs = 4000
batch = 32
save_interval = 100

def generator():
    model = Sequential()
    model.add(Dense(256, input_shape=(100,)))
    model.add(LeakyReLU(alpha=0.2))
    model.add(BatchNormalization(momentum=0.8))
    model.add(Dense(512))
    model.add(LeakyReLU(alpha=0.2))
    model.add(BatchNormalization(momentum=0.8))
    model.add(Dense(1024))
    model.add(LeakyReLU(alpha=0.2))
    model.add(BatchNormalization(momentum=0.8))
    model.add(Dense(28 * 28 * 1, activation='tanh'))
    model.add(Reshape(shape))
    return model


def discriminator():
    model = Sequential()
    model.add(Flatten(input_shape=shape))
    model.add(Dense((28 * 28 * 1), input_shape=shape))
    model.add(LeakyReLU(alpha=0.2))
    model.add(Dense(int((28 * 28 * 1) / 2)))
    model.add(LeakyReLU(alpha=0.2))
    model.add(Dense(1, activation='sigmoid'))
    model.summary()
    return model


def stacked_generator_discriminator(D, G):
    D.trainable = False
    model = Sequential()
    model.add(G)
    model.add(D)
    return model


def plot_images(samples=16, step=0):
    filename = "mnist_%d.png" % step
    noise = np.random.normal(0, 1, (samples, 100))
    images = Generator.predict(noise)
    plt.figure(figsize=(10, 10))

    for i in range(images.shape[0]):
        plt.subplot(4, 4, i + 1)
        image = images[i, :, :, :]
        image = np.reshape(image, [28, 28])
        plt.imshow(image, cmap='gray')
        plt.axis('off')
    plt.tight_layout()
    plt.savefig(filename)
    plt.close('all')


Generator = generator()
Generator.compile(loss='binary_crossentropy', optimizer=Adam(lr=0.0002, beta_1=0.5, decay=8e-8))

Discriminator = discriminator()
Discriminator.compile(loss='binary_crossentropy', optimizer=Adam(lr=0.0002, beta_1=0.5, decay=8e-8),
                      metrics=['accuracy'])

stacked_generator_discriminator = stacked_generator_discriminator(Discriminator, Generator)
stacked_generator_discriminator.compile(loss='binary_crossentropy', optimizer=Adam(lr=0.0002, beta_1=0.5, decay=8e-8))

(X_train, _), (_, _) = mnist.load_data()
X_train = (X_train.astype(np.float32) - 127.5) / 127.5
X_train = np.expand_dims(X_train, axis=3)

for cnt in range(epochs):

    random_index = np.random.randint(0, len(X_train) - batch / 2)
    legit_images = X_train[random_index: random_index + batch // 2].reshape(batch // 2, 28, 28, 1)
    gen_noise = np.random.normal(0, 1, (batch // 2, 100))
    syntetic_images = Generator.predict(gen_noise)

    x_combined_batch = np.concatenate((legit_images, syntetic_images))
    y_combined_batch = np.concatenate((np.ones((batch // 2, 1)), np.zeros((batch // 2, 1))))

    d_loss = Discriminator.train_on_batch(x_combined_batch, y_combined_batch)

    noise = np.random.normal(0, 1, (batch, 100))
    y_mislabled = np.ones((batch, 1))

    g_loss = stacked_generator_discriminator.train_on_batch(noise, y_mislabled)

    logger.info('epoch: {}, [Discriminator: {}], [Generator: {}]'.format(cnt, d_loss[0], g_loss))

    if cnt % save_interval == 0:
        plot_images(step=cnt)
```


{:refdef: style="text-align: center;"}
![loss]({{ site.url }}/{{ site.baseurl }}/assets/2018-08-15-vanilla-gan/loss.png)
{: refdef}

{:refdef: style="text-align: center;"}
*Training process*
{: refdef}

## Results

As the result, I made a classic gif. You can see how it starts from random noise and gradually learns to generate images that look like handwritten digits.

{:refdef: style="text-align: center;"}
![results]({{ site.url }}/{{ site.baseurl }}/assets/2018-08-15-vanilla-gan/results.gif)
{: refdef}

## References:
* Generative Adversarial Networks - [original paper](https://arxiv.org/abs/1406.2661)