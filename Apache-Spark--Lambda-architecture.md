Lambda architecture is a data-processing architecture designed to handle massive quantities of data by taking advantage of both batch- and stream-processing methods. This approach to architecture attempts to balance latency, throughput, and fault-tolerance by using batch processing to provide comprehensive and accurate views of batch data, while simultaneously using real-time stream processing to provide views of online data. The two view outputs may be joined before presentation. The rise of lambda architecture is correlated with the growth of big data, real-time analytics, and the drive to mitigate the latencies of map-reduce.[1]


![](http://s6.postimg.org/r0m8bpjw1/recommender_architecture.png)
Lambda architecture depends on a data model with an append-only, immutable data source that serves as a system of record.[2]:32 It is intended for ingesting and processing timestamped events that are appended to existing events rather than overwriting them. State is determined from the natural time-based ordering of the data.


* https://en.wikipedia.org/wiki/Lambda_architecture
* http://www.slideshare.net/tmatyashovsky/lambda-architecture-with-apache-spark
* http://blog.cloudera.com/blog/2014/08/building-lambda-architecture-with-spark-streaming/
* https://spark-summit.org/2014/wp-content/uploads/2014/07/Lambda-Architecture-Jim-Scott..pdf
* https://spark-summit.org/2014/applying-the-lambda-architecture-with-spark/
* http://lambda-architecture.net/
* https://www.datanami.com/2016/06/01/merging-batch-streaming-post-lambda-world/
* http://www.slideshare.net/dbtsai/2015-0117-lambda-architecture
* http://www.slideshare.net/tagomoris/lambda-architecture-using-sql-hadoopcon-2014-taiwan
* https://twitter.com/lambdaarch
![](https://pbs.twimg.com/media/CDEQuWvVIAAbVMr.png)
* ****http://www.kdnuggets.com/2015/11/fast-big-data-apache-flink-spark-streaming.html****
* https://mahout.apache.org/users/algorithms/intro-cooccurrence-spark.html
* http://www.slideshare.net/gschmutz/apache-stoapache-storm-vs-spark-streaming-two-stream-processing-platforms-comparedrm-vsapachesparkv11
* https://www.mapr.com/developercentral/lambda-architecture