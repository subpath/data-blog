---
layout: post
title:  "Markov Chain for music generation"
date:   2019-07-08 13:18:29 +0500
permalink: "/markov-chain-for-music/"
---
<p align="center">
<img src="{{ site.url }}/assets/2019-07-08-markov-chain-for-music/head.jpeg" />
</p>

{:refdef: style="text-align: center;"}
![head]({{ site.url }}/assets/2019-07-08-markov-chain-for-music/head.jpeg)
{: refdef}

From this article, you will learn about the Markov Chain model and how it can be applied for the music generation.

## What is the Markov chain?

Markov chain is a model that describes a sequence of possible events. This sequence needs to satisfied Markov assumption — the probability of the next state depends on a previous state and not on all previous states in a sequence.

It may sound like a simplification of the real cases. For example to applied Markov chain for the weather prediction, we need to make an assumption that weather tomorrow only depends on current weather and pretend that there are no other factors, like time of the year, e.t.c.

In spite this simplification in many cases, we will be able to generate useful predictions, but at the same time, we will be able to solve our task faster, by making it less computationally expensive.

Markov chain models have many applications in finance, natural language processing and anywhere where you have time series data.

## Generating music with Markov Chain

There are plenty of great papers and blog posts explaineing Markov Chain. So without digging into theoretical details, let’s apply this model on practice! The most popular application of the Markov Chain is language and speech, for example, predict next word in a sentence. But what if we try to generate music?

Same as natural languages we may think about music as a sequence of notes. But because I play guitar I’ll be operating with chords. If we take chords sequence and learn its patternt we may find that certain chords may follow particular chords more often, and other chords rarely follow that chords. We will build our model to find and understand this pattern.

Okay, here is the plan:

1. Take corpus of chords
2. Calculate probability distribution for chords to follow a particular chord
3. Define the first chord or make a random choice
4. Make a random choice of the next chord taking into an account probability distribution
5. Repeat steps 4 for the generated chord
6. …
7. Stochastic music is awesome!

## A step by step guide:

For the data source, I prepared a CSV file with chords sequence that I took from one famous band from Liverpool. You can find this file on the GitHub.

Example of sequence:

```python
['F', 'Em7', 'A7', 'Dm', 'Dm7', 'Bb', 'C7', 'F', 'C', 'Dm7',...]
```

First, we make bigrams:

```python
['F Em7', 'Em7 A7', 'A7 Dm', 'Dm Dm7', 'Dm7 Bb', 'Bb C7', ...]
```

Now if I take chord F as initial chord in a sequence, what will be the probability for other chords to follow it?

There are 18 bigrams that start with chord F:

```python
['F Em7', 'F C', 'F F', 'F Em7', 'F C', 'F A7sus4', 'F A7sus4', ...]
```

Then we’ll calculate the frequency of each unique bigram to appear in the sequence:

```python
{'F Em7': 4, 'F C': 4, 'F F': 3, 'F A7sus4': 4, 'F Fsus4': 2, 'F G7': 1}
```

If we normalize it, we’ll get probabilities:

```python
{'F Em7': 0.222,
 'F C': 0.222,
 'F F': 0.167,
 'F A7sus4': 0.222,
 'F Fsus4': 0.111,
 'F G7': 0.056}
```

This often can be interpreted in the form of a graph:

{:refdef: style="text-align: center;"}
![graph]({{ site.url }}/assets/2019-07-08-markov-chain-for-music/graph.png)
{: refdef}

{:refdef: style="text-align: center;"}
*Weighted graph of possible next chord*
{: refdef}

Each node of this graph, except initial node F in the center, represents possible states that our sequence can achieve, in our case they are chords that may follow F. Some of the chords have higher probabilities than other, some chords can’t follow F chord at all, for example, Am, because there was no bigram than combine this chord with F.

Now, Markov Chain is a stochastic process, or random process is you prefer. In order to move to the next state, we will be choosing chord randomly but according to the probability distribution, in our case, that means that we are more likely to choice chord C than G7.

For a given chord F, we have 6 candidates for the next chord:

```python
options
>>> ['Em7', 'C', 'F', 'A7sus4', 'Fsus4', 'G7']
```

And each chord has its own probabilities accordingly:

```python
probabilities
>>> [0.222, 0.222, 0.167, 0.222, 0.111, 0.056]
```

Numpy since version 1.7.0 can perform random sampling according to probability distribution it was given, let’s use that:

```python
import numpy as np

choise = np.random.choice(options, p=probabilities)
```

Let’s say our result of this random choice was Em7. Now we have a new state and can repeat the whole process again.

The overall workflow looks like this:

```python
# Our current state
chord = 'F'
# create list of bigrams which stats with current chord
bigrams_with_current_chord = [bigram for bigram in bigrams if bigram.split(' ')[0]==chord]

# count appearance of each bigram
count_appearance = dict(Counter(bigrams_with_current_chord))

# convert apperance into probabilities
for ngram in count_appearance.keys():
  count_appearance[ngram] = count_appearance[ngram]/len(bigrams_with_current_chord)
  
# create list of possible options for the next chord
options = [key.split(' ')[1] for key in count_appearance.keys()]

# create  list of probability distribution
probabilities = list(count_appearance.values()

)# Make random prediction
np.random.choice(options, p=probabilities)
```

Because it’s a stochastic process each time you will run this model it will give you different results. For repeatability you can set seed like that:

```python
np.random.seed(42)
```

We can summarize the whole process into two functions:

```python
def predict_next_state(chord:str, data:list=bigrams):
    """Predict next chord based on current state."""
    # create list of bigrams which stats with current chord
    bigrams_with_current_chord = [bigram for bigram in bigrams if bigram.split(' ')[0]==chord]
    # count appearance of each bigram
    count_appearance = dict(Counter(bigrams_with_current_chord))
    # convert apperance into probabilities
    for ngram in count_appearance.keys():
        count_appearance[ngram] = count_appearance[ngram]/len(bigrams_with_current_chord)
    # create list of possible options for the next chord
    options = [key.split(' ')[1] for key in count_appearance.keys()]
    # create  list of probability distribution
    probabilities = list(count_appearance.values())
    # return random prediction
    return np.random.choice(options, p=probabilities)def generate_sequence(chord:str=None, data:list=bigrams, length:int=30):
    """Generate sequence of defined length."""
    # create list to store future chords
    chords = []
    for n in range(length):
        # append next chord for the list
        chords.append(predict_next_state(chord, bigrams))
        # use last chord in sequence to predict next chord
        chord = chords[-1]
    return chords
```

Now we can generate a sequence of the length we want:

```python
generate_sequence('C')
```

Example of a sequence:

```python
['Bb',
 'Dm',
 'C',
 'Bb',
 'C7',
 'F',
 'Em7',
 'A7',
 'Dm',
 'Dm7',
 'Bb',
 'Dm',
 'Gm6'
 ..
]
```

I’ve tried to play it on the guitar and it does sound like a song that band from the Liverpool may write. The only thing that missing is text, but we can use the same model trained on text corpus to generate text for a song :).

## Summary

We only touched the tip of the iceberg with simple Markov Chain, the worlds of the stochastic model are so large, including Hidden Markov Chain, Markov Chain Monte Carlo, Hamiltonian Monte Carlo, and many others. But in the essence of each models same Markov assumption — next state depends upon current state and not on the previous sequence of states.

Because of this simple yet powerful rule, Markov chain models found applications in many areas and can be also successfully applied for the music generation.

One of the coolest things here is we will get different results based on a corpus we train our model. Train on a corpus from the Radiohead and model will generate chord sequences in a Radiohead style.

Code and dataset can be found in my [GitHub repository](https://github.com/subpath/Markov_chain_for_music_generation).

## References:

1. Eugene Seneta. “Markov and the creation of Markov chains” (2006) [Original Paper PDF](https://www.csc2.ncsu.edu/conferences/nsmc/MAM2006/seneta.pdf)
2. Hayes, Brian. “First links in the Markov chain.” American Scientist 101.2 (2013): 252. [Original Paper PDF](http://www.americanscientist.org/libraries/documents/201321152149545-2013-03Hayes.pdf)