%md

Overview
========

Spark Streaming is an extension of the core Spark API that enables
scalable, high-throughput, fault-tolerant stream processing of live data
streams. 

Data can be ingested from many sources like 

* [Kafka](http://kafka.apache.org/documentation.html#introduction), 
* [Flume](https://flume.apache.org/),
* [Twitter](https://twitter.com/) [Streaming](https://dev.twitter.com/streaming/overview) and [REST](https://dev.twitter.com/rest/public) APIs, 
* [ZeroMQ](http://zeromq.org/), 
* [Amazon Kinesis](https://aws.amazon.com/kinesis/streams/), or 
* [TCP sockets](http://www.gnu.org/software/mit-scheme/documentation/mit-scheme-ref/TCP-Sockets.html), 

and can be processed using
complex algorithms expressed with high-level functions like `map`,
`reduce`, `join` and `window`. 
  
Finally, processed data can be pushed out
to filesystems, databases, and live dashboards. In fact, you can apply Spark's 
* [machine learning](http://spark.apache.org/docs/latest/mllib-guide.html) and
* [graph processing](http://spark.apache.org/docs/latest/graphx-programming-guide.html) algorithms 
on data streams.

![Spark Streaming architecture](http://spark.apache.org/docs/latest/img/streaming-arch.png)

#### Internally, it works as follows: 
* Spark Streaming receives live input data streams and 
* divides the data into batches, 
* which are then processed by the Spark engine 
* to generate the final stream of results in batches.

![Spark Streaming data flow](http://spark.apache.org/docs/latest/img/streaming-flow.png)

Spark Streaming provides a high-level abstraction called **discretized
stream** or **DStream**, which represents a continuous stream of data.
DStreams can be created either from input data streams from sources such
as Kafka, Flume, and Kinesis, or by applying high-level operations on
other DStreams. Internally, a **DStream is represented as a sequence of
[RDDs](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD)**.

This guide shows you how to start writing Spark Streaming programs with
DStreams. You can write Spark Streaming programs in Scala, Java or
Python (introduced in Spark 1.2), all of which are presented in this
[guide](http://spark.apache.org/docs/latest/streaming-programming-guide.html). 

Here, we will focus on Streaming in Scala.

* * * * *

http://snowplowanalytics.com/blog/2015/06/10/spark-streaming-example-project-0.1.0-released/
