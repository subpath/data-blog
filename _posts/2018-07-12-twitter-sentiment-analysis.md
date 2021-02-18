---
layout: post
title:  "Building microservice for Twitter real-time sentiment analysis"
date:   2018-06-29 12:00:00 +0500
permalink: "/twitter-sentiment-analysis/"
tags:
- 
  name: infrastructure
  style: danger
-
  name: ML
  style: success
---

{:refdef: style="text-align: center;"}
![twitter_logo]({{ '/assets/2018-07-12-twitter-sentiment-analysis/twitter-sentiment-logo.png' | relative_url }})
{: refdef}

## Building microservice for Twitter Real-time data collection and sentiment analysis.

First of all, I would like to point out that the skill of building MVP and microservices for a Data Scientist is extremely useful! When you can build a prototype and test it working environment it just feels so much better and allows you to deeper understand your final product.

There is a famous Venn diagram made by [Drew Conway](http://drewconway.com/zia/2013/3/26/the-data-science-venn-diagram) where hacker skills refer to programming skills. So according to this diagram programming skills are important for Data Science in general and for a Data Scientist particularly.

{:refdef: style="text-align: center;"}
![venn-diagram]({{ '/assets/2018-07-12-twitter-sentiment-analysis/venn-diagram.png' | relative_url }})
{: refdef}



## Right, so programming skills are important, what next?

The first thing a data scientist needs is data. Twitter is a treasure trove of data that is publicly available. It’s a unique data set that anyone can access. There are plenty of studies that showed the predictive power of Twitter sentiment.


### Here are some examples:

[Bollen, J., Mao, H. and Zeng, X., 2011. Twitter mood predicts the stock market. Journal of computational science, 2(1), pp.1–8.](https://arxiv.org/pdf/1010.3003.pdf)

[Pang, B. and Lee, L., 2008. Opinion mining and sentiment analysis. Foundations and Trends in Information Retrieval, 2(1–2), pp.1–135.](http://www.cs.cornell.edu/home/llee/omsa/omsa.pdf)

[Wen, M., Yang, D. and Rose, C., 2014, July. Sentiment Analysis in MOOC Discussion Forums: What does it tell us?. In Educational data mining 2014.](http://educationaldatamining.org/EDM2014/uploads/procs2014/long%20papers/130_EDM-2014-Full.pdf)

## Practical project

Practical project for this weekend is building a microservice that can collect Twitter Data and perform some basic sentiment analysis.

**Source code can be found in my repository:**[https://github.com/subpath/twitter_sentimet_analysis_microservice](https://github.com/subpath/twitter_sentimet_analysis_microservice)

### What it does:
1. It receives POST request with instruction of which twitter it needs to collect
2. Then it opens socket connection with Twitter API and starts to receive real-time Twitter data
3. Each tweet assigned with its sentiment score with help of TextBlob
4. All data then goes to the PostgreSQL database

### Main features:

* Because it uses Nameko and RabbitMQ, you can asynchronously run many different tasks and collect different topics.
* Because it is based on Flask, you can easily connect this with other services that will communicate with this service by POST requests
* For each tweet, the service assigns sentiment score using TextBlob and stores it to the PostgreSQL, so later you can perform analysis on that.
* It scalable and expandable — you can easily add other sentiment analysis tools or creative visualization or web interface because it is based on Flask

### Example of POST request json: 
```json
{
"duration": 60,
"query": ["Bitcoin", "BTC", "Cryptocurrency", "Crypto"],
"translate": false
}
```

where *duration* is how many minutes you want to collect streaming twitter data, query is a list of search queries that Twitter API will use to send you relevant tweets, translate is boolean in case False will collect only English tweets, otherwise, the service will try machine translation from TextBlob.

## What can be done next

* You can make for real-time visualization system (like on the picture on the cover)
* You can make models for predicting assets movements (for example Bitcoin price)
* You can try different sentiment analysis tools like Vader and Watson
* You can collect data enough for deep exploratory analysis and find some cool insights, like who is “an opinion leader” in some topic