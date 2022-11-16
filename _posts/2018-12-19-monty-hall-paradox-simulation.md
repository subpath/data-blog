---
layout: post
title:  "Monty Hall’s paradox — solve it by simulation!"
date:   2018-12-19 13:18:29 +0500
permalink: "/monty-hall-paradox-simulation/"
tags:
- name: statistics
  style: primary
- name: 2018
  style: secondary
---

{:refdef: style="text-align: center;"}
![head]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/head.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*Picture from Brooklyn 9–9 show season 4 episode 8*
{: refdef}


Hey! Have you heard about Monty Hall’s paradox? I mean come on, it’s in every book about Bayesian statistics! Thing is even though I know a formal solution to it, it is still counter-intuitive to me, and that why it called a paradox.

A few days ago I was discussing this with my wife, and I found that it’s the same case for her. She has major in analytical chemistry by the way. My major is in computational chemistry, so we both come from the same world. In natural science, we need to confirm everything in an experiment.

So I made a simulation of Monty Hall’s game to illustrate this paradox to my wife.

## What Monty’s Hall paradox is about?
*If you know what it is please skip this paragraph :)*

Paradox based on the old popular (in the US) show, you may find different versions with doors or with boxes. In a general case, you have three doors, behind one of the doors you have a prize. Initially, the prize was hidden randomly. And you have a host (Monty Hall obviously) that know where the prize is. And then this happened:

1. You are picking a door, where you think the prize may be.
2. Host opens one of the **remain two doors** and shows that there is no prize behind this door. He can always do that because he knows where the prize is.
3. Now you have only two doors remains — one that you chose initially and another door. You allowed to change your mind and pick remain door instead of the door that you chose initially.
4. The main question is: **Should you change your initial decision?**

Now, it seems like there is no point to this question! Initially, when you’ve had 3 options, you’ve had a 33% chance to choose correctly, now you have only two options left, so your chances are 50%, right?

The paradox is, that your chances are actually different. **You more likely to win, if you change your initial decision**. And this is counter-intuitive! Let’s solve this with help of Bayesian formula.

## The formal solution to Monty’s Hall paradox
Let’s use the Bayesian formula to solve this!


{:refdef: style="text-align: center;"}
![formula-1]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/formula-1.png' | relative_url }})
{: refdef}

```
where p(H) — a probability of the hypothesis before getting new data D — prior,

p(H|D) — a probability of the hypothesis given new data D — posterior,

p(D|H) — a probability of the data given hypothesis — likelihood,

p(D) — a probability of the data — normalize constant
```

We have 3 hypotheses A, B, and C, represent that prize behind door 1, 2, or 3. Hypothesis A means that prize behind door A, B behind door B and so on. D in our case is when the host choosing door B **and** there is no prize behind it.

Let’s create a table.

{:refdef: style="text-align: center;"}
![table-1]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/table-1.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*The table where we know only priors*
{: refdef}


Because initially prize was hidden behind the door randomly, the prior probability that prize is behind one of the doors is 1/3.

So far so go! Now, let’s think about likelihood.

1. If the prize behind door A, the host can open doors B or C. In that case probability of him open door B is 1/2. But since that prize behind door A, the probability that prize not behind door B is 1.
2. If the prize behind door B, the host can only open door C, so the probability of him opens door B is 0
3. If the prize behind door C, the host can open door B with probability 1.



{:refdef: style="text-align: center;"}
![table-2]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/table-2.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*The table where we figured out our likelihood*
{: refdef}

Next is easy


{:refdef: style="text-align: center;"}
![table-3]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/table-3.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*The completed table*
{: refdef}

Wow! So you are more likely to win (66%) if you change your initial answer. Key to understanding is simple: Host is affecting the probability because he is not choosing randomly which door to open.

## Simulation of the paradox

So I made a simple experiment to simulate the paradox. You have options to always keep your choice same as an initial answer or always change your initial choice. Please, find Gist below, the repository can be also found on my [GitHub repo](https://github.com/subpath/Monty_Hall_paradox_simulation).

<script src="https://gist.github.com/subpath/39d9f85421445dbd7a46b95e42aeb113.js"></script>

## Results:

Below you can find a visualization of the experiment. In a 1000 experiments, I’ve got a 67% chance to win if player changing his initial choice, and only 34% chance to win if I would always keep my initial choice unchanged. Results are very close to the formal solution with the Bayesian formula.

{:refdef: style="text-align: center;"}
![plot-1]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/plot-1.gif' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*34% of winning*
{: refdef}


{:refdef: style="text-align: center;"}
![plot-2]({{ '/assets/2018-12-19-monty-hall-paradox-simulation/plot-2.gif' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*67% of winning*
{: refdef}



## Instead of a conclusion:

* Computational statistics if fun! And at least for me, it super clearly illustrates the solution.
* Monty Hall’s paradox is not easy to digest, even when you now correct answer, that can be obtained in different ways.