> Join is one of the most expensive operations you will commonly use in Spark, so it is worth doing what you can to shrink your data before performing a join.



- When both RDDs have duplicate keys, the join can cause the size of the data to expand dramatically. It may be better to perform a distinct or combineByKey operation to reduce the key space or to use cogroup to handle duplicate keys instead of producing the full cross product. By using smart partitioning during the combine step, it is possible to prevent a second shuffle in the join (we will discuss this in detail later).

- If keys are not present in both RDDs you risk losing your data unexpectedly. It can be safer to use an outer join, so that you are guaranteed to keep all the data in either the left or the right RDD, then filter the data after the join.

- If one RDD has some easy-to-define subset of the keys, in the other you may be better off filtering or reducing before the join to avoid a big shuffle of data, which you will ultimately throw away anyway.

- In order to join data, Spark needs the data that is to be joined (i.e., the data based on each key) to live on the same partition. The default implementation of a join in Spark is a shuffled hash join. The shuffled hash join ensures that data on each partition will contain the same keys by partitioning the second dataset with the same default partitioner as the first, so that the keys with the same hash value from both datasets are in the same partition. While this approach always works, it can be more expensive than necessary because it requires a shuffle. The shuffle can be avoided if:

1. Both RDDs have a known partitioner.

2. One of the datasets is small enough to fit in memory, in which case we can do a broadcast hash join (we will explain what this is later).

> **Note that if the RDDs are colocated the network transfer can be avoided, along with the shuffle.**
> **Always persist after repartitioning.**

- DataFrame Joins
Joining data between DataFrames is one of the most common multi-DataFrame transformations. The standard SQL join types are all supported and can be specified as the joinType in df.join(otherDf, sqlCondition, joinType) when performing a join. As with joins between RDDs, joining with nonunique keys will result in the cross product (so if the left table has R1 and R2 with key1 and the right table has R3 and R5 with key1 you will get (R1, R3), (R1, R5), (R2, R3), (R2, R5)) in the output. 

> Using a self join and a lit(true), you can produce the cartesian product of your Dataset, which can be useful but also illustrates how joins (especially self joins) can easily result in unworkable data sizes.


https://techmagie.wordpress.com/2015/12/19/understanding-spark-partitioning/



**How many Partitions are good ?**

Having too few and too large number of partitions has certain advantages and disadvantages.So it is recommended to partition judiciously depending upon your cluster configuration and requirements.

**Disadvantages of too few partitions**

- Less concurrency – You are not using advantages of parallelism. There could be worker nodes which are sitting ideal.
Data skewing and improper resource utilization – Your data might be skewed on one partition and hence your one worker might be doing more than other workers and hence resource issues might come at that worker.


**Disadvantages of too many partitions**

- Task scheduling may take more time than actual execution time.

So there is trade off between number of partitions.Below is recommended guideline –

**Usually between 100 and 10K partitions depending upon cluster size and data.**

> Lower bound – 2 X number of cores in cluster available to application

> Upper bound – task should take 100+ ms time to execute.If it is taking less time than your partitioned data is too small and your application might be spending more time in scheduling the tasks.


https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/performance_optimization/how_many_partitions_does_an_rdd_have.html




**What is an optimized way of joining large tables in Spark SQL**

- Use a broadcast join if you can (see this notebook). 

- Consider using a very large cluster (it's cheaper that you may think).

- Use the same partitioner. 

https://stackoverflow.com/questions/28395376/does-a-join-of-co-partitioned-rdds-cause-a-shuffle-in-apache-spark

- If the data is huge and/or your clusters cannot grow such that even (3) above leads to OOM, use a two-pass approach.
  -  First, re-partition the data and persist using partitioned tables (dataframe.write.partitionBy()).
  -  join sub-partitions serially in a loop, "appending" to the same final result table.

Side note: I say "appending" above because in production I never use SaveMode.Append. It is not idempotent and that's a dangerous thing. I use SaveMode.Overwrite deep into the subtree of a partitioned table tree structure. Prior to 2.0.0 and 1.6.2 you'll have to delete _SUCCESS or metadata files or dynamic partition discovery will choke.





https://databricks.com/blog/2016/05/23/apache-spark-as-a-compiler-joining-a-billion-rows-per-second-on-a-laptop.html


https://umbertogriffo.gitbooks.io/apache-spark-best-practices-and-tuning/content/avoiding_shuffle_less_stage,_more_fast/joining-a-large-and-a-small-rdd.html

----------------------------------------------------------

Spark will have better read performance when data is filtered at data source.

However, just adding any predicates against Cassandra table columns will guarantee that data will be filtered at Cassandra data source. There are certain predicate pushdown rules apply to Spark-SQL. These rules depend on the data source behind each table. I will be covering Cassandra predicate pushdown rules in a separate post.

Optimization Rule #2:  Minimize number of spark tasks in scan/read phase

Spark plan creates multiple stages in read phase to read each table. There is a separate stage for each table. The number of tasks in each stage depends on the number of data partitions spark has to read into memory. By having efficient partitioning strategy on tables and utilizing proper predicates against partitions you can minimize the number of tasks in read stage.

For example, Single partition scans using = operator will create fewer tasks than multi-partition scans using “IN” operator.

Optimization Rule #3:  Order of tables in FROM clause matters. Keep the table with the largest size at the top.

Spark table is based on Dataframe which is based on RDD. In simple terms, RDD is a distribute collection. Spark SQL JOIN operation is very similar to fold left operation on a collection.

This post is not about Scala or functional programming concepts. However, it helps to know how fold left operation works on a collection. Refer to below link for the explanation of fold left. http://alvinalexander.com/scala/how-to-walk-scala-collections-reduceleft-foldright-cookbook

As you can see in the picture of Spark plan JOIN operation requires shuffling data from second table executors to first table executors to perform JOIN operation. Shuffle is very expensive operation on IO & CPU. This operation is repeated until all tables to the right are merged with the result on the left.

By keeping the table with largest data size in join operation at the top you are avoiding shuffling largest data.

Optimization Rule #4:  Keep consistent partitioning strategy across JOINing tables 

By having common partition keys it makes it easier to write queries with filters that can be applied across many of the joining tables.

Optimization Rule #5:  Minimize the number of tables in JOIN.

As you can see in the timeline of the plan picture, reads are parallel operations. However, JOINs are sequential steps. That means every join step adds to the timeline of the total execution of the query.

Use proper denormalization techniques to reduce the number of tables in your data model.


-----------------------------------------------------------

**Joining a large and a small RDD**

If the small RDD is small enough to fit into the memory of each worker we can turn it into a broadcast variable and turn the entire operation into a so called map side join for the larger RDD [23]. In this way the larger RDD does not need to be shuffled at all. This can easily happen if the smaller RDD is a dimension table.

      val smallLookup = sc.broadcast(smallRDD.collect.toMap)
      largeRDD.flatMap { case(key, value) =>
      smallLookup.value.get(key).map { otherValue =>
      (key, (value, otherValue))
       }
       }


  **Joining a large and a medium size RDD**

If the medium size RDD does not fit fully into memory but its key set does, it is possible to exploit this [23]. As a join will discard all elements of the larger RDD that do not have a matching partner in the medium size RDD, we can use the medium key set to do this before the shuffle. If there is a significant amount of entries that gets discarded this way, the resulting shuffle will need to transfer a lot less data.

     val keys = sc.broadcast(mediumRDD.map(_._1).collect.toSet)
     val reducedRDD = largeRDD.filter{ case(key, value) => keys.value.contains(key) }
     reducedRDD.join(mediumRDD)

It is important to note that the efficiency gain here depends on the filter operation actually reducing the size of the larger RDD. If there are not a lot of entries lost here (e.g., because the medium size RDD is some king of large dimension table), there is nothing to be gained with this strategy


--------------------------------------------------------------

* http://www.treselle.com/blog/apache-spark-on-yarn-performance-and-bottlenecks/
* https://umbertogriffo.gitbooks.io/apache-spark-best-practices-and-tuning/content/avoiding_shuffle_less_stage,_more_fast/joining-a-large-and-a-medium-size-rdd.html
* https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/performance_optimization/how_many_partitions_does_an_rdd_have.html
* https://dzone.com/articles/apache-spark-performance-tuning-degree-of-parallel

------------------------------------------------------------------------
- PROCESS_LOCAL means data is in the same JVM as the code that's running, so
 it's really fast.

- NODE_LOCAL means that a block is being read on this node, therefore the "network" part is omitted, therefore NODE_LOCAL in general should be faster(might mean that the data is in HDFS on the  same node, or in another executor on the same node, so is a little slower because the data has to travel across an IPC connection.) .

- RACK_LOCAL means that a block is being read from an HDD on a remote node and then is passed over network(data is on a different server so needs to be sent over the network.).

> Spark switches to lower locality levels when there's no unprocessed data on a node that has idle CPUs.  In that situation you have two options: wait  until the busy CPUs free up so you can start another task that uses data on
> that server, or start a new task on a farther away server that needs to  bring data from that remote place.  What Spark typically does is wait a bit in the hopes that a busy CPU frees up.  Once that timeout expires, it  starts moving the data from far away to the free CPU.


> The main tunable option is how far long the scheduler waits before  starting to move data rather than code.  Those are the spark.locality.

> * settings here: http://spark.apache.org/docs/latest/configuration.html
>  If you want to prevent this from happening entirely, you can set the  values to ridiculously high numbers.  The documentation also mentions that "0" has special meaning, so you can try that as well.

 -------------------------------------------------------------------------------------

**Spark Data Locality**

What is data locality

Data locality is used to describe how spark maps tasks and input data. It is done to minimize overhead to transfer input data to task. Here you can find detaied post how Spark calculates data locality for RDD.

according to spark documentation there are 5 types of data locality:

* PROCESS_LOCAL
* NODE_LOCAL
* NO_PREF
* RACK_LOCAL
* ANY

After some node finished current work, Spark starts look for new task for it. Going though all pending tasks Spark tries to find task with data locality not greater than the current MaxDataLocality. MaxDataLocality is calculated based on diff between current time and last launch time: curTime - lastLaunchTime >= localityWaits(currentLocalityIndex).

Wait time is configured by spark job configuration:

* spark.locality.wait (change wait time for all cases)
* spark.locality.wait.node (for NODE_LOCAL)
* spark.locality.wait.process (for PROCESS_LOCAL)
* spark.locality.wait.rack (for RACK_LOCAL)

The wait timeout for fallback between each level can be configured individually or all together in one parameter; see the spark.locality parameters on the configuration page for details.
See class org.apache.spark.scheduler.TaskSetManager for mode detailed logic of recalculating locality levels available for execution.

> ** spark.locality.wait to 0 to insure that all tasks are being worked on immediately**

> The Thumb Rule to decide the partition size while working with HDFS is 128 MB.

Example : input dataset size is about 1.5 GB (1500 MB) and going with 128 MB per partition, the number of partitions will be:

**Total input dataset size / partition size => 1500 / 128 = 11.71 = ~12 partitions**



**“Stragglers” are tasks within a stage that take much longer to execute than other tasks.**

* http://www.treselle.com/blog/apache-spark-performance-tuning-straggler-tasks/
* https://spark.apache.org/docs/latest/tuning.html


Other Pages:

- https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-Tuning-and-Debugging
- https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-Performance-tuning
- https://github.com/vaquarkhan/vk-wiki-notes/wiki/Tuning-and-Debugging-Apache-Spark