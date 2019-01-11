* https://www.slideshare.net/SparkSummit/top-5-mistakes-when-writing-spark-applications-63071421

* https://www.youtube.com/watch?v=WyfHUNnMutg

* http://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-2/

* https://github.com/tresata/spark-skewjoin

* https://datarus.wordpress.com/2015/05/04/fighting-the-skew-in-spark/

* http://blog.cloudera.com/blog/2015/07/how-to-do-data-quality-checks-using-apache-spark-dataframes/



**Mistake #1**

Deciding on the number of executors, cores and memory. Spark architecture recap

Answer #1 - most granularHave smallest sized executors

Turns out that is the wrong ans weWhy?
You are not using the benefits of running multiple tasks in the same executor.
Answer #2: Least granularGive it all the memory and all the cores in the cluster. 

Turns out that is the wrong answer. Why?Because we need to leave some overhead for OS/Hadoop daemons to run.

Answer #3 - with overheadLeave out one core per node and leave out 1G of RAM from the node and we give the rest to the executor. 

That is also the wrong answer. Lets assume..You are running Spark on YARN

There are 3 things we need to talk about to get to the right answer. 

1 - memory overhead

2 - YARN AM needs a core - client modeCluster mode. 

3-HDFS throughput Best to keep under 5 cores per executors. Calculations Correct answer Not etched in stone. 

If you have lots of IO, your CPU is not the culprit. You can up your cores. Dynamic allocation helps with this though, right?Dynamic allocation allows Spark to dynamically scale the cluster resources allocated to your application based on the workload. Works with Spark on Yarn only. 

But it can you dynamically figure out the number of executors you need, but not with the number of cores for each executor or the memory for each executor.

**Mistake #2**

Application failureWhy?No Spark shuffle block can be greater than 2GB. What's a shuffle block again?In MapReduce terminology, a file written from one Mapper for a Reducer. 

The Reducer makes a local copy of this file and then 'reduces' it Defining shuffle and partition Once again You will get an overflow exception if shuffle block size > 2GB.

 Whats going on here?Spark uses ByteBuffer as abstraction for blocks Spark SQL Default number of partitions to use when doing shuffles is 200. So what can I do?

1. Increase the number of partitions - thereby reducing the average partition size

2. Get rid of skew in your data - more later How exactly?

In Spark SQL, increase the value of spark.sql.shuffle.partitions

In regular Spark applications, use rdd.repartition() or rdd.coalesce()

How many partitions should I haveRule of thumb is around 128MB per partitionThere's moreSpark uses a different data structure for bookkeeping during shuffles, when the number of partitions is less than 2000 vs more than 2000. 

This 2000 number is hard coded in Spark. So what are you saying?If number of partitions < 2000, but not by much, bump it to be slightly higher than 2000. Can you summarize?Dont have too big partitions - your job will fail due to 2 GB limit
Dont have too few partitions - your job will be slow, not making use of parallelism

Rule of thumb ~ 128 MB per partition

If partitions < 2000, but close, bump to just > 2000

**Mistake #3**

Slow jobs on Join/ShuffleYour dataset takes 20 seconds to run over a with a map job, but takes 4 hours when joined or shuffled. What went wrong?
Mistake - skewSkew happens because you have a NULL key, or you have a popular value. 

All your work will be going through a single core.

Mistake - skew: answers Salting

Normal key: 'foo'
Salted key: 'foo' + random.nextInt(saltFactor)Managing parallelismBefore and after salting. 

Two stage aggregation
-stage one to do operations on the salted keys
-stage two to do operation access unsalted key results

Mistake - skew: isolated saltingSecond stage only required for isolated keys. 
Mistake - skew: isolated map joinManaging parallelismCartesian Join

How to fight cartesian join
-Nested structures. 

This is going to be multiple magnitudes faster than doing a Cartesian. 

Repartitioning

**Mistake #4**

Out of luck?Do you ever run out of memory? 

Do you ever have more than 20 stages?

Is your driver doing a lot of work?Mistake - DAG managementShuffles are to be avoided

  ReduceByKey over GroupByKey
  TreeReduce over Reduce

Use Complex/Nested TypesMistake - DAG management: shufflesMap side reduction, where possible

Think about partitioning/bucketing ahead of time

Do as much as possible with a single shuffle

Only send what you have to send

Avoid skew and cartesiansReduceByKey over GroupByKeyReduceByKey can do almost anything that GroupByKey can do

-aggregations
-windowing
-use memory
-but you have more control

ReduceByKey has a fixed limit of Memory requirements

GroupByKey is unbounded and dependent on dataTreeReduce over ReduceTreeReduce and Reduce return some result to driver
TreeReduce does more work on the executors

While Reduce bring everything back to the driverComplex Types

**Mistake #5**

Ever seen this?Method not found exception. 

ShadingBring your own dependency using shadedPattern. Future of shadingSpark 2 has some libraries shaded
-Gauva is fully shadedSummary5 mistakesSize up your executors right

2 GB limit on Spark shuffle blocks

Skew and cartesians are evil

Learn to manage your DAG

Do shady stuff, don't let classpath leaks mess you up Questions Some of the sophistication from auto-generation could be built into the product. How is that coming along?I guess we are trudging through it. Some cannot be built in the auto-generated part. 

Some of the DAG management will get better as the query optimizer gets better. Skew is very hard to fix with zero cost. Do you think GroupByKey is overly demonized? Do you think there is no use case where it is better than ReduceByKey?You can do everything GroupByKey does with ReduceByKey. The reason why I demonize it is because I don't want a phone call at night time. 

Unless I have super strong guarantees on the data that's coming in it could cause trouble. How do you set your Python worker memory in your Spark?We are both Scala people, sorry. But I don't think there is any significant performance degradation in Python. 