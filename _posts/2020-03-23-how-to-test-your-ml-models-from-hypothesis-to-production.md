---
layout: post
title:  "How to test your ML models from hypothesis to production"
date:   2020-03-23 13:18:29 +0500
permalink: "/ml-from-hypothesis-to-production/"
tags:
- 
    name: statistics
    style: primary
-
  name: ML
  style: success
    
---
{:refdef: style="text-align: center;"}
![head]({{ '/assets/2020-03-23-how-to-test-your-ml-models-from-hypothesis-to-production/head.png' | relative_url }})
{: refdef}

Photo by [Victor Garcia](https://unsplash.com/@victor_g) on [Unsplash](https://unsplash.com/s/photos/pipeline)

Often the goal of the Data Science team is to produce predictive models (classifiers, regressors, etc) which then will be used to improve the company’s products.

## But how would we know that the new model will be better than the current one?

The A/B test with a proper control group and randomized permutation test might give us a pretty good understanding. The problem is that many companies can’t afford testing models in a real-life scenario. Imagine if Tesla would A/B test their new autopilot on you, and your car will be in a group with a weaker model. Even if we not talking about people's lives, often companies might experience negative side-effects from the poorer performed models.

To minimize the negative effects I prefer to be confident in the models' candidates for A/B testing. Of cource we would never be 100% confident, but here 4 steps testing I would normally implement in the teams where I’m working. Each step will incrementally increase our confidence in the model’s quality.


### 4 steps model testing:

#### 1. Local development

Model development could often start with a hypothesis, say

*“What if we would use the RandomForest model as our classifier?”*

Some team-member then will take that idea as a task that he will work for the next week. At that point, you would probably already have a labeled dataset on which you can train and test your model. So it would be the responsibility of this researcher to correctly split that data for cross-validation or backtesting in case of time-series data.

So this Data Scientists played with that model and concluded that cross-validation gave him an average accuracy of 95% and the current baseline model has an accuracy of 93% on the same dataset. Good Data Scientists will not stop at these numbers and will perform the permutation test and will calculate p-value to have an idea of whether this difference in accuracy might occur randomly due to data variation.

**Tip: Use the permutation test or other stat-tests that you think more suitable for your particular case. P-value is not a “silver bullet” but it’s useful to always think about random chances getting better performance.**

So our Data Scientists concluded that difference is statistically significant. Great! That means we are on to something. Data Scientists prepared a model for the pipeline, push his code and made Pull-request.

#### 2. Testing in CI/CD

The second step in the ML model testing I recommend you to implement is testing as part of CI/CD. There are many different ways how you can actually implement that and they all depend on your current architecture, but the main idea is this:
 * CI/CD environment has access to some out-of-sample dataset that no one in the Data Science team has access to.
 * The new model tested automatically the same way always, and the author is not able to affect that.
 * Metrics calculated automatically. It’s a good idea to display them on some dashboard where everybody in the team can view it.
 * Based on the successful testing Pull-request with a new model can be accepted to join develop-branch and proceed for the next step.

You need to make sure that your out-of-sample dataset is representative and can provide a reliable evaluation. It also a good idea to have some ceremonies around that CI/CD testing, so your teammate would not spend all day hacking out-of-sample data trying different things and adjusting his model towards better performance on this particular data.

In my team that kind of testing only be possible when a person proves on the locally made research correctness to other team members. We also had a monitoring system when all teammates and product-manager would receive an email with testing results. So it would be noticeable when anyone might try to abuse that testing.

#### 3. Stage testing / Shadow testing

The next step is to test your model in the production-like environment. For example, we would deploy develop branch on the stage-server and make it run on the exact same data as production pipeline, the only difference would be that the end-user will not see the results of the develop-branch and results will be stored in the database. There are several questions you would wanna answer before running such an experiment, but the main one is:

**— “When do I stop that experiment?”**

Similar to A/B testing to answer that you need to figure out the effect size you expected to capture and statistical power.

To make final conclusion about model quality I suggest you use a statistical test to protect yourself from being fulled by randomness, but keep in mind it’s not 100% robust.

#### 4. A/B test

3 steps above will allow you to gain you pretty good confidence in your new model. In some cases A/B test is not possible so you could stop on the shadow testing, some companies can afford to experiment with many different models at the same time, in that cases, shadow testing can be unnecessary and after testing on out-of-sample data model can be tested as part of A/B test.

### Conclusion

Data Science often can be tricky, there are many things that can mislead you during model development.

* Never trust 100% to your model performance, use Bayesian thinking.
* If you are manager/team lead/data scientists, don’t hesitate to question model performance that your team member reported
* Use statistical tests to compare models performance

-----
Medium link: [https://towardsdatascience.com/how-to-test-your-ml-models-from-hypothesis-to-production-7553430382a9](https://towardsdatascience.com/how-to-test-your-ml-models-from-hypothesis-to-production-7553430382a9)