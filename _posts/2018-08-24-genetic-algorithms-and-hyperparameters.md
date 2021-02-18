---
layout: post
title:  "Genetic algorithms and hyperparameters"
date:   2018-08-24 12:00:00 +0500
permalink: "/genetic-algorithms-and-hyperparameters/"
tags:
-
  name: ML
  style: success
---

Hi! Do you like [genetic algorithms](https://en.wikipedia.org/wiki/Genetic_algorithm) as much as I do? I hope so!

This week I wanna share my experience with searching best hyperparameters for Neural Networks and how you can use auto-ML (to some extent) right now!

## What are genetic algorithms?

So a genetic algorithm is a solution to the optimization problem, for example, if you need to find the best set of parameters to minimize some loss function. Genetic algorithms are part of the bigger group of evolutionary algorithms. The idea is inspired by nature and natural selection.

In nature you need at least three requirements for evolution to take place:
1. **Inheritance** — organisms must be able to transfer some specific characteristics to the offspring;

2. **Variation** — organisms within the population must have different characteristics;

3. **Competition** — more offspring are produced than can survive.

Now imagine that instead of organisms you have some objects coded in a specific programming environment. So how would it work if, for example, you will have a population of ML models?

1. Firstly you generate your initial population of ML models and randomly choose hyperparameters.
2. You calculate your loss function for each model, for example, log-loss.
3. Then you choose some amount of models with the lowest error.
4. Now you need to create offspring, so you create a population of new ML models based on the best models from the previous generation and slightly change their hyperparameters. Your new population will be contained from models from the previous population and freshly generated models in some proportion, for example, 50/50.
5. You calculate your loss function, sort your models and repeat the whole process again.

Genetic algorithms are not perfect, you still need to specify you loss-function, your population size, a ratio of offspring with changed parameters and so on.

## How can you use genetic algorithms for hyperparameter tuning?

Hyperparameters are very important, they can have a crucial effect on model performance. It is not easy to find the best set of hyperparameters, because it often can be computationally expensive. Let’s see how we can apply genetic algorithms for hyperparameters tuning. Fortunately, like almost everything else in Python, you don’t need to write this from scratch.

### TPOT

Probably the most user-friendly library for genetic hyperparameter tuning.

**GitHub:** [https://github.com/EpistasisLab/tpot](https://github.com/EpistasisLab/tpot)


![tpot]({{ '/assets/2018-08-24-genetic-algorithms-and-hyperparameters/tpot.gif' | relative_url }})

*animation from TPOT GitHub*

First time I used it, it was not obvious to me how to extract the best hyperparameters and write them to some variable. So below is my way of extracting best hyperparameters from TPOT.

[https://gist.github.com/subpath/7a197125834bb879e32070735b5477ec](https://gist.github.com/subpath/7a197125834bb879e32070735b5477ec)

```python
# Search for best hyperparameters for XGBoost
from xgboost import XGBClassifier
from tpot import TPOTClassifier, TPOTRegressor
from deap.gp import Primitive

# Define your hyperparameters
params = {'max_depth': np.arange(1,200,1),
          'learning_rate': np.arange(0.0001,0.1,0.0001),
          'n_estimators': np.arange(1,200,1),
          'nthread':[6],
          'gamma':np.arange(0.00001,0.1,0.00001),
          'subsample':np.arange(0.1,2,0.1),
          'reg_lambda': np.arange(0.1,200,1),
          'reg_alpha': np.arange(1,200,1),
          'min_child_weight': np.arange(1,200,1),
          'gamma': np.arange(0.1,2,0.1),
          'colsample_bytree': np.arange(0.1,2,0.1),
          'colsample_bylevel': np.arange(0.1,2,0.1)
         }

# Run TPOT
tpot_classifier = TPOTClassifier(generations=20, population_size=500, offspring_size=250,
                                verbosity=2, early_stop=8,
                                config_dict={'xgboost.XGBClassifier': params}, cv = 10, scoring = 'accuracy')
tpot_classifier.fit(X, Y)

# Extract best parameters
args = {}
for arg in tpot_classifier._optimized_pipeline:
    if type(arg) != Primitive:
        try:
            if arg.value.split('__')[1].split('=')[0] in ['max_depth', 'n_estimators', 'nthread','min_child_weigh']:
                args[arg.value.split('__')[1].split('=')[0]] = int(arg.value.split('__')[1].split('=')[1])
            else:
                args[arg.value.split('__')[1].split('=')[0]] = float(arg.value.split('__')[1].split('=')[1])
        except:
            pass
params = args

# Now you can use XGBoost with best hyperparameters
xgb = XGBClassifier(**params)
```

### How to tune Neural Network?

In a case with neural networks, you can also use genetic algorithms and they work much faster then grid-search based approach.

I found this GitHub repository-[Evolve a neural network with a genetic algorithm](https://github.com/harvitronix/neural-network-genetic-algorithm) and really liked this approach. So I made a wrapper from that and a python package, so you can simply install it with pip and use with whatever data you want (the initial example uses only several hardcoded datasets).

#### Example of usage:

```python
from evolution import NeuroEvolution

params = {
    "epochs": [10, 20, 35],
    "batch_size": [10, 20, 40],
    "n_layers": [1, 2, 3, 4],
    "n_neurons": [20, 40, 60],
    "dropout": [0.1, 0.2, 0.5],
    "optimizers": ["nadam", "adam"],
    "activations": ["relu", "sigmoid"],
    "last_layer_activations": ["sigmoid"],
    "losses": ["binary_crossentropy"],
    "metrics": ["accuracy"]
}
```

```python
# x_train, y_train, x_test, y_test - prepared data

search = NeuroEvolution(generations = 10, population = 10, params=params)

search.evolve(x_train, y_train, x_test, y_test)


100%|██████████| 10/10 [05:37<00:00, 29.58s/it]
100%|██████████| 10/10 [03:55<00:00, 25.55s/it]
100%|██████████| 10/10 [02:05<00:00, 15.05s/it]
100%|██████████| 10/10 [01:37<00:00, 14.03s/it]
100%|██████████| 10/10 [02:49<00:00, 22.53s/it]
100%|██████████| 10/10 [02:37<00:00, 23.14s/it]
100%|██████████| 10/10 [02:36<00:00, 21.37s/it]
100%|██████████| 10/10 [01:57<00:00, 18.56s/it]
100%|██████████| 10/10 [02:42<00:00, 25.29s/it]

"best accuracy: 0.79, best params: {'epochs': 35, 'batch_size': 40, 'n_layers': 2, 'n_neurons': 20, 'dropout': 0.1, 'optimizers': 'nadam', 'activations': 'relu', 'last_layer_activations': 'sigmoid', 'losses': 'binary_crossentropy', 'metrics': 'accuracy'}"

# or you can call it with
search.best_params
```

So it can also search for the best number of layers and neurons and wherever hyperparameters you defined.

### Genetic algorithms are cool!

I’m a little bit biased for genetic algorithms, I think that they are very intuitive and yet powerful enough to solve complex computational tasks. But again it is not a silver bullet for every optimization task you might need to solve, in the future I will try to explore this topic in depth.

### References:

1. TPOT: [https://github.com/EpistasisLab/tpot](https://github.com/EpistasisLab/tpot)

2. NeuroEvolution: [https://github.com/subpath/neuro-evolution](https://github.com/subpath/neuro-evolution)