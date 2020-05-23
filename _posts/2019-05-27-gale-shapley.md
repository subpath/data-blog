---
layout: post
title:  "Gale–Shapley algorithm simply explained"
date:   2019-05-27 13:18:29 +0500
permalink: "/gale-shapley/"
---

{:refdef: style="text-align: center;"}
![pic-1]({{ site.url }}/{{ site.baseurl }}/assets/2019-05-27-gale-shapley/pic-1.png)
{: refdef}

From this article, you will learn about stable pairing or stable marriage problem. You will learn how to solve that problem using [Game Theory](https://en.wikipedia.org/wiki/Game_theory) and the [Gale-Shapley algorithm](https://en.wikipedia.org/wiki/Stable_marriage_problem) in particular. We will use Python to create our own solution using theorem from the original paper from 1962.


## What is a stable marriage or pairing problem?

In the real world, there are cases when we facing paring problem: student choosing their future universities, man and women seeking each other to create families, internet services that need to be connected with users with the smallest amount of time. In all these cases we need to create stable pairs that need to satisfied certain criteria. We can imagine a stable pair for (A, a) as a pair where no A or a have any better options.

Best way to understand it is to solve practical examples, I can recommend book **Insights into Game Theory** [1], it contains plenty of exercises that you can solve on paper. But here I wanna use Python to solve that :)

## Problem definition:

In our example, we will have two groups, women and man. Women’s names will start with a capital letter: **A, B, C, D** and men with lowercase latter: **a, b, c, d**. We need to create stable pairs. Before doing that we made a survey to gather some information as our starting point. Each of them was asked to rank people of the opposite sex by their personal sympathy. Where 1 place person that they like the most and 4 people they like the least.

For example, women A answered this way: **d, c, a, b**

In python, we can create two data frames man and women:

<script src="https://gist.github.com/subpath/199ac94f2d3f661c290bafef26463dc7.js"></script>

In data frames, a rank was replaced by number for convenience. For example, if we wanna know the preferences of the women A, we can read column A in the women data frame: 3, 4, 2, 1 for men a, b, c, d respectively. If we wanna know the preferences of the men a we can read a row in the man data frame: 1, 2, 3, 4 for women A, B, C, D respectively.

Thus pair of man and women can be represented as a pair of numbers, for example, pair A and a will create a pair with numbers (3,1). For the women A man a is third in her list, but for the men a, women A is number one in his list.

So in this case men a is fully happy, but women A can try to create a pair with someone higher from her list, for example, man c, and if for c will be better to create a pair with A than with his current partner, both pair will collapse and create new pairs.

That is an example of an unstable pair, a pair in which one or both participants can improve the situation but creating more preferable pairs for them.

So the task is to find stable pairs for all participants. And turns out that there is a way to achieve equilibrium!

## The Gale-Shapley algorithm in Python

David Gale and Lloyd Shapley proved that in cases with when two sets are equal there always a way to create stable pairs. I recommend reading the original paper[2] to be familiar with the elegant prove they provided.

There are different implementations of the Gale-Shapley algorithm in Python [3, 4, 5], let’s create this algorithm closer to pseudocode solution without going into complicated OOP.

In a pseudocode solution looks like this [6]:

```
function stableMatching {
    Initialize all m ∈ M and w ∈ W to free
    while ∃ free man m who still has a woman w to propose to {
       w = first woman on m’s list to whom m has not yet proposed
       if w is free
         (m, w) become engaged
       else some pair (m', w) already exists
         if w prefers m to m'
            m' becomes free
           (m, w) become engaged 
         else
           (m', w) remain engaged
    }
}
```

In Python I was able to create this solution:

<script src="https://gist.github.com/subpath/fac948b8fa18d0e5a539348231d14915.js"></script>

## Summary:

Game Theory is very fun because it operates with situations from the real world. And it’s also most of the time do not require complicated math apparatus, especially in cooperative games.

There are many things about the Gale-Shapley algorithm that we didn’t touch: general case when the number of men is not equal to the number of women and how many maximum iterations this algorithm may take.

Looking to open-source solutions I was sure that will be able to create more minimalistic and even more Pythonic solution. But I ended up with a pretty messy while cycle.

## References:
1. Insights into Game Theory: An Alternative Mathematical Experience, Cambridge University Press, forthcoming, with Ein-Ya Gura
2. [Gale D., Shapley L.S. College Admissions and the Stability of Marriage // American Mathematical Monthly. 1962. Vol. 69. P.9–15.](http://www.eecs.harvard.edu/cs286r/courses/fall09/papers/galeshapley.pdf)
3. [https://rosettacode.org/wiki/Stable_marriage_problem#Python](https://rosettacode.org/wiki/Stable_marriage_problem#Python)
4. [https://pypi.org/project/matching/](https://pypi.org/project/matching/)
5. [https://github.com/QuantEcon/MatchingMarkets.py](https://github.com/QuantEcon/MatchingMarkets.py)
6. [https://en.wikipedia.org/wiki/Stable_marriage_problem](https://en.wikipedia.org/wiki/Stable_marriage_problem)