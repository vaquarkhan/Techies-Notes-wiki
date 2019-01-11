If an RDD has too many partitions, then task scheduling may take more time than the actual execution time. To the contrary, having too less partitions is also not beneficial as some of the worker nodes could just be sitting idle resulting in less concurrency. This could lead to improper resource utilization and data skewing i.e. data might be skewed on a single partition and a worker node might be doing more than other worker nodes. Thus, there is always a trade off when it comes to deciding on the number of partitions.

**Some acclaimed guidelines for the number of partitions in Spark are as follows-**

When the number of partitions is between 100 and 10K partitions based on the size of the cluster and data, the lower and upper bound should be determined.

The lower bound for spark partitions is determined by 2 X number of cores in the cluster available to application.

Determining the upper bound for partitions in Spark, the task should take 100+ ms time to execute. If it takes less time, then the partitioned data might be too small or the application might be spending extra time in scheduling tasks.

**Why Partitioning  is  Important?**

Partitioning has great importance when working with key value pair RDDs. For example aggregating values for certain keys in a distributed RDD element would required fetching values from other nodes before computing final aggregate result per key. That is what we call a “Shuffle Operation” in spark. Locality of a group of elements with similar values on the same node reduces communication costs.




**Types of Partitioning in Apache Spark**

* Hash Partitioning in Spark
* Range Partitioning in Spark


**Hash Partitioning in Spark:**

Hash Partitioning attempts to spread the data evenly across various partitions based on the key. Object.hashCode method is used to determine the partition in Spark as partition = key.hashCode () % numPartitions.

**Range Partitioning in Spark:**

Some Spark RDDs have keys that follow a particular ordering, for such RDDs, range partitioning is an efficient partitioning technique. In range partitioning method, tuples having keys within the same range will appear on the same machine. Keys in a range partitioner are partitioned based on the set of sorted range of keys and ordering of keys.

Spark’s range partitioning and hash partitioning techniques are ideal for various spark use cases but spark does allow users to fine tune how their RDD is partitioned, by using custom partitioner objects. Custom Spark partitioning is available only for pair RDDs i.e. RDDs with key value pairs as the elements can be grouped based on a function of each key. Spark does not provide explicit control of which key will go to which worker node but it ensures that a set of keys will appear together on some node. For instance, you might range partition the RDD based on the sorted range of keys so that elements having keys within the same range will appear on the same node or you might want to hash partition the RDD into 100 partitions so that keys that have same hash value for modulo 100 will appear on the same node.


**What is the shuffle**

You have a list of phone call detail records in a table and you want to calculate amount of calls happened each day. This way you would set the “day” as your key, and for each record (i.e. for each call) you would emit “1” as a value. After this you would sum up values for each key, which would be an answer to your question – total amount of records for each day. But when you store the data across the cluster, how can you sum up the values for the same key stored on different machines? The only way to do so is to make all the values for the same key be on the same machine, after this you would be able to sum them up.

to join two tables on the field “id”, you must be sure that all the data for the same values of “id” for both of the tables are stored in the same chunks. Imagine the tables with integer keys ranging from 1 to 1’000’000. By storing the data in same chunks I mean that for instance for both tables values of the key 1-100 are stored in a single partition/chunk, this way instead of going through the whole second table for each partition of the first one, we can join partition with partition directly, because we know that the key values 1-100 are stored only in these two partitions. To achieve this both tables should have the same number of partitions, this way their join would require much less computations.


**When we get benefits of Partitioning**

When an RDD is partitioned by the previous transformation with the same Partitioner, the shuffle will be avoided on at least one RDD and will reduce communication cost. Following is the list of some of the transformations which will benefit from pre-partitioned RDDs.

* join()
* cogroup()
* groupWith()
* leftOuterJoin()
* rightOuterJoin()
* groupByKey()
* reduceByKey()
* combineByKey()

**When a Partition information is not preserved?**

Not all transformation preserves the partition information. Calling map() on a hash-Partitioned key-value RDD, it is not guaranteed that the partition information will available in resultant RDD, since the function passed in a map transformation can change the key of elements in the RDD and that would be inconsistent.

**How to preserve Partition Information?**

Using transformations mapValues() and flatMapValues() instead of map() and flatMap() on a pair RDD will only transform the values of RDDs keeping keys intact. These transformations only pass values to your function definition.



* https://spark-summit.org/2013/wp-content/uploads/2013/10/Li-AEX-Spark-yahoo.pdf
* http://dev.sortable.com/spark-repartition/
* http://www.edureka.co/blog/demystifying-partitioning-in-spark
* http://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-1/
* https://stackoverflow.com/questions/31610971/spark-repartition-vs-coalesce
* https://0x0fff.com/spark-architecture-shuffle/


----------------------------------------------------------------------

   val ints = sc.parallelize(1 to 100, 4)
   ints.partitions.size


Spark can only run 1 concurrent task for every partition of an RDD, up to the number of cores in your cluster. So if you have a cluster with 50 cores, you want your RDDs to at least have 50 partitions (and probably 2-3x times that).+

As far as choosing a "good" number of partitions, you generally want at least as many as the number of executors for parallelism. You can get this computed value by calling sc.defaultParallelism.+

Partitions get redistributed among nodes whenever shuffle occurs. Repartitioning may cause shuffle to occur in some situations, but it is not guaranteed to occur in all cases. And it usually happens during action stage.+


Preferred way to set up the number of partitions for an RDD is to directly pass it as the second input parameter in the call like rdd = sc.textFile("hdfs://…​/file.txt", 400), where 400 is the number of partitions. In this case, the partitioning makes for 400 splits that would be done by the Hadoop’s TextInputFormat, not Spark and it would work much faster. It’s also that the code spawns 400 concurrent tasks to try to load file.txt directly into 400 partitions