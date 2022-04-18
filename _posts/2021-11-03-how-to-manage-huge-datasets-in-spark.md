---
layout: post
title:  "Managing HUGE Datasets with Scala Spark"
date:   2021-11-03 13:18:29 +0500
permalink: "/managing-huge-datasets-with-scala-spark/"
tags:
- 
    name: scala
    style: primary
- 
    name: spark
    style: info
-
  name: ML
  style: success
    
---
{:refdef: style="text-align: center;"}
![head]({{ '/assets/2021-11-03-how-to-manage-huge-datasets-in-spark/head.jpg' | relative_url }})
{: refdef}

Photo by <a href="https://unsplash.com/@macblades?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Mac Blades</a> on <a href="https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>


Spark is awesome! It’s scalable and fast, especially when you writing in a “native Spark” and avoiding custom Udfs. But when you working with large data frames there are some tips that you can use to avoid OOM errors and speed up the whole computation.
Here is a shortlist of the things that I learned from my personal experience.

# Using configuration suited for the task
It’s always a good idea to start with proper configuration.
In my opinion, Spark has awesome [documentation](https://spark.apache.org/docs/latest/configuration.html), highly recommend starting with it.
Depending on how you use your Spark: inside cluster or in a stand-alone mode your configuration will be different. I’m using spark mostly in standalone mode, so here are my examples:

## 1. Driver memory and driver maxResult:
When you are working with a large dataset you need to increase default memory allocation and maxResultSize value.
```scala
val spark = SparkSession.builder
.config("spark.driver.maxResultSize", "{YOUR-VALUE}")
.config("spark.driver.memory", "{YOUR-VALUE}")
```

## 2. Broadcast timeout, network timeout, and heartbeat
When you trying to save a large data frame to the database or some bucket I noticed that sometimes tasks might fail just because default timeouts thresholds are too small.

```scala
.config("spark.sql.broadcastTimeout", "{YOUR-VALUE}")
.config("spark.sql.debug.maxToStringFields", "{YOUR-VALUE}")
.config("spark.network.timeout", "{YOUR-VALUE}")
.config("spark.executor.heartbeatInterval", "{YOUR-VALUE}")
```

## 3. Garbage collection
You can use the garbage collector provided by JVM
```scala
.config("spark.executor.extraJavaOptions",
"-XX:+UseG1GC -XX:+UnlockDiagnosticVMOptions -XX:+G1SummarizeConcMark -XX:InitiatingHeapOccupancyPercent=35 -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:OnOutOfMemo
```

# Using caching in the right place
Caching is an essential thing in spark and by using it in the correct places you can seriously reduce execution time. Sometime you might consider .persist instead of caching. You can read more about it [here](https://sparkbyexamples.com/spark/spark-difference-between-cache-and-persist/).

```scala
// cache you dataframe after expensive operations
dataFrame
  .select(...)
  .filter(...)
  .groupBy(...)
  .agg(...)
  .cache
```

# Repartition before joining
Joins are a pretty expensive operation to run, there are [several tricks](https://towardsdatascience.com/the-art-of-joining-in-spark-dcbd33d693c) you can use, but the one that I found the most useful is re-partitioning before joining. It helps because awesome by the time you get to join you can already perform some operations on your datasets and partitions might be skewed. And skewed partitions will seriously affect join execution time.

```scala
val leftRepartitioned = left.repartition(256, col("YOUR COLUMN"))
val rightRepartitioned = right.repartition(256, col("YOUR COLUMN"))
val joined = left.join(right, ...)
```

# Repartition after groupBy
The same goes for groupBy, It usually helps a lot
```scala
val groupedDataset = foo
  .groupBy("bar")
  .// your aggregations and other operations
  .
  .repartition(256, col("YOUR COLUMN"))
```

# Data skewness
In order to get the best performance from Spark, you need to pay attention to partitions skew. There are many great articles about it ([1](https://itnext.io/handling-data-skew-in-apache-spark-9f56343e58e8?gi=b742dce2b9f6), [2](https://databricks.com/session/handling-data-skew-adaptively-in-spark-using-dynamic-repartitioning)), so I would not repeat them here. But just keep in mind that sometimes repartitions trick my new work if the column that you chose for partitioning is skewed. If you are not sure which column to choose you can always use the [salting trick](https://dzone.com/articles/why-your-spark-apps-are-slow-or-failing-part-ii-da).

# UDFs
I should also mention elephant in the room — User Defined Functions. Because they give you so much freedom it’s sometimes tempting to use them more often than you actually need them.
Every time when you wanna implement something custom I recommend you to double-check collections with default spark functions from the org.apache.spark.sql.functions. Maybe you can solve your problem using expr :).
I also recommend you to check out [spark-daria](https://github.com/MrPowers/spark-daria). It’s a collection with some useful methods that expand Spark capabilities.

# Deleting cached datasets after you are done with them
If you cached some data frames using `.cache` you can call `.unpersist` to delete it from memory.

```scala
val dataFrameCached = dataframe.cache
// some more code
dataFrameCached.unpersist
```

Or you can flush memory completely using
```scala
sqlContext.clearCache()
```

Thanks for reading! Any feedback is welcome!
-----
Medium link: [https://medium.com/@subpath/managing-huge-datasets-with-scala-spark-9840ad760424](https://medium.com/@subpath/managing-huge-datasets-with-scala-spark-9840ad760424)
