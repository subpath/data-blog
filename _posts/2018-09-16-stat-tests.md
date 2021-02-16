---
layout: post
title:  "Must-have statistical tests for any Data Scientist"
date:   2018-09-16 13:18:29 +0500
permalink: "/must-have-stat-tests/"
---

Hi! Wanna share a subjective short list of statistical tests that every Data Scientist should know about. Data Science is a rapidly developing field where people are coming from different industries with backgrounds in Computer Science, Physics, Statistics, Biology. So depending on background, people can be less familiar with statistical tools.

## Statistical hypothesis testing

Working with data can be very dangerous because you may end up with wrong findings and make the wrong decision.

**So it’s always a good idea to check your results for statistical significance.**

Before any tests normally you need to formalize two hypotheses: null-hypothesis and alternative hypothesis. A null hypothesis is what you want to disprove. For example, if you are testing the new drug, that can cure disease in 5 days and you wanna know if this new drug is better than the old one that can cure disease in 7 days. In that case, your alternative hypothesis will be “New drug is better than the old one” and null hypothesis — “These two drugs are identical”.

### p-value

One of the most important concepts in hypothesis testing is p-value.

P-value is the probability of getting at least as extreme results given that the null hypothesis is correct. P-value is a number you get after running a statistical test. If the p-value is smaller than some threshold (normally 0.05) than you can reject your null-hypothesis, otherwise you can’t reject any hypothesis. Of course in real life things are not always that simple, there are many misconceptions and misuse cases with the p-value, I will make an article about it in the future.

### Student’s t-test

In a Student’s test, we are comparing the means of two normally distributed data. Null-hypothesis is that the means of two distribution are equal and the alternative hypothesis is that they are not equal. The key thing here is that data must be normally distributed. Student’s t-test allows us to catch cases when two data have a significant difference.


For example, we have results from group A and group B, and it’s kind of normally distributed.

{:refdef: style="text-align: center;"}
![picture-1]({{ '/assets/2018-09-16-stat-tests/picture-1.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*Distribution of results in group A and group B*
{: refdef}

In Python, you can run a t-test with two lines of code:

```python
from scipy.stats import ttest_ind
ttest_ind(group_a, group_b)

>>> Ttest_indResult(statistic=-1.4406680274460792, pvalue=0.15125794002026197)

```

P-value of my t-test is 0.15 which means that I can’t say that the means of the group a and b different with statistical significance. 0.15 means that there is 15% chance to get same or more extreme results given that the null hypothesis is correct.


There are different variations one-sided t-test when you are comparing the mean of some distribution to some number, two-sided when you comparing the means of two distributions.


### Mann-Whitney’s U-test

U-test does a similar comparison of two distributions, but unlike t-test, it not required that data must be normally distributed.


{:refdef: style="text-align: center;"}
![picture-2]({{ '/assets/2018-09-16-stat-tests/picture-2.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*Distributions of data in group A and group B, both distributions are not normal. Different data than in the previous example.*
{: refdef}

In Python:

```python
from scipy.stats import mannwhitneyu

mannwhitneyu(group_a, group_b)

>>> MannwhitneyuResult(statistic=1390809.0, pvalue=0.00027773404303550313)
```

p-value 0.0003 < 0.05, which mean we can conclude that data from group A and group B is significantly different.

### Fisher’s F-test

So far we were comparing how the average from group A is different comparing to average from group B. But what if you want to compare a variety of data in two datasets? In that case, you can use Fisher’s F-test, which can help us to calculate how dispersions are different in two distributions.

{:refdef: style="text-align: center;"}
![picture-3]({{ '/assets/2018-09-16-stat-tests/picture-3.png' | relative_url }})
{: refdef}

{:refdef: style="text-align: center;"}
*group A and group B from the first example*
{: refdef}

In Python:

```python
from scipy.stats import f_oneway

f_oneway(group_a, group_b)

>>> F_onewayResult(statistic=4.617502532352513, pvalue=0.03286124143823078)
```

For F-test, data must be normally distributed. Otherwise, you can use [Levene’s test](http://en.wikipedia.org/wiki/Levene%27s_test) or [Bartlett’s test](http://en.wikipedia.org/wiki/Bartlett%27s_test) to check dispersion differences in two datasets.

## In conclusion

Statistical tests are not ultimate solutions, there are many different misconceptions around them and it can be challenging to choose the correct statistical test for your data because each of them has their own limitations.

But at the same time not using any statistical test to validate your hypothesis is much more dangerous. In that case, personal biases can lead to completely wrong conclusions. So I encourage all people who are new to Data Science to use them on a daily basis.


## References:

* [Alex Reinhart “Statistics Done Wrong”, March 2015](https://www.statisticsdonewrong.com/)
* [David Colquhoun “An investigation of the false discovery rate and the misinterpretation of p-values” 2014](https://arxiv.org/abs/1407.5296)