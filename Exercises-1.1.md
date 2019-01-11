**Caching**

We can use persist() to tell spark to store the partitions
On node failures (that persist data), lost partitions are recomputed by spark
In Scala and Java, default persist() stores the data on heap as unserialized objects
We can also replicate the data on to multiple nodes to handle node failures without slowdown
Persistence is at partition level. i.e. there should be enough memory to cache all the data of a partition. Partial caching isn't supported as of 1.5.2
LRU policy is used to evict cached partitions to make room for new ones. Wen evicting,
memory_only ones are recomputed next time when they are accessed
memory_and_disk ones are written to disk
TODO

add examples of various persist mechanisms
example for tachyon
tungston



**Pair RDD**

Exercise 1

Goal

Understand different ways of creating Pair RDD's
Problem(s)
    Problem 1: Create a Pair RDD of this collection Seq("the", "quick", "brown", "fox", "jumps", "over", "the", "lazy",     "dog")?
    Problem 2: Create Pair RDD's from external storages.
Answer(s)
    Answer 1
    In general, we can extract a key from the data by running a map() transformation on it. Pair's are represented using     Tuples (key, value).+

    scala> val words=sc.parallelize(Seq("the", "quick", "brown", "fox", "jumps", "over", "the", "lazy", "dog"))
    words: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[45] at parallelize at <console>:27

    scala> val wordPair=words.map( w => (w.charAt(0), w) )
    wordPair: org.apache.spark.rdd.RDD[(Char, String)] = MapPartitionsRDD[46] at map at <console>:29

    scala> wordPair.foreach(println)
    (o,over)
    (l,lazy)
    (d,dog)
    (t,the)
    (j,jumps)
    (t,the)
    (f,fox)
    (b,brown)
    (q,quick)




**Pair RDD - Transformations**

Reference: http://spark.apache.org/docs/latest/programming-guide.html#transformations
Excercise 1

**Goal**

Understand the usage of reduceByKey() transformation.
   reduceByKey(func, [numTasks]): When called on a dataset of (K, V) pairs, returns a dataset of (K, V) pairs where the     values for each key are aggregated using the given reduce function func, which must be of type (V,V) => V. Like in       groupByKey, the number of reduce tasks is configurable through an optional second argument.
Problem(s)
Problem 1: Calculate page views by day for the star wars video (download link).
Problem 2: Word count
Answer(s)
Answer 1
    scala> val logs=sc.textFile("file:///c:/_home/swk_small.log")
    logs: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[50] at textFile at <console>:27

    scala> val parsedLogs=logs.map(line => parseAccessLog(line))
    parsedLogs: org.apache.spark.rdd.RDD[scala.collection.mutable.Map[String,String]] = MapPartitionsRDD[51] at map at    <console>:31

    scala> //create a pair RDD and do reduceByKey() transformation

   scala> val viewsByDate=parsedLogs.map( m => (m.getOrElse("accessDate","unknown"), 1)).reduceByKey((x,y) => x+y)
   viewsByDate: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[55] at reduceByKey at <console>:33

    scala> viewsByDate.foreach(println)
    (03-02-2003,1)
    (05-04-2003,1)
(12-04-2003,1913)
(02-03-2003,1)
(26-02-2003,2)
(07-02-2003,1)
(05-01-2003,1)
(22-03-2003,1)
(19-02-2003,1)
(02-01-2003,2)
(11-04-2003,4162)
(22-01-2003,1)
(21-03-2003,1)
(10-04-2003,3902)
(01-03-2003,1)
(05-02-2003,1)
(18-03-2003,2)
(25-02-2003,1)
(06-02-2003,1)
(09-04-2003,1)
(20-02-2003,1)
(23-03-2003,1)
(06-04-2003,1)
Answer 2
Excercise 2

Goal

Understand the usage of foldByKey() transformation.
Problem 1: Calculate page views by day for the star wars video (download link).
Answer(s)
Answer 1
scala> val logs=sc.textFile("file:///c:/_home/swk_small.log")
logs: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[74] at textFile at <console>:27

scala> val parsedLogs=logs.map(line => parseAccessLog(line))
parsedLogs: org.apache.spark.rdd.RDD[scala.collection.mutable.Map[String,String]] = MapPartitionsRDD[75] at map at <console>:31

scala> val viewDates=parsedLogs.map( m => (m.getOrElse("accessDate","unknown"), 1))
viewDates: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[77] at map at <console>:33

scala> val viewsByDate=viewDates.foldByKey(0)((x,y)=> x+y)
viewsByDate: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[78] at foldByKey at <console>:35

scala> viewsByDate.foreach(println)
(03-02-2003,1)
(26-02-2003,2)
(05-04-2003,1)
(05-01-2003,1)
(19-02-2003,1)
(11-04-2003,4162)
(21-03-2003,1)
(01-03-2003,1)
(12-04-2003,1913)
(18-03-2003,2)
(02-03-2003,1)
(06-02-2003,1)
(20-02-2003,1)
(07-02-2003,1)
(23-03-2003,1)
(22-03-2003,1)
(06-04-2003,1)
(02-01-2003,2)
(22-01-2003,1)
(10-04-2003,3902)
(05-02-2003,1)
(25-02-2003,1)
(09-04-2003,1)
Excercise 3

Goal

Understand the usage of combineByKey() transformation.
def combineByKey[C] (createCombiner: V => C, mergeValue: (C, V) => C, mergeCombiners: (C, C) => C): RDD[(K, C)]
Problem 1: From here
Answer(s)
Answer 1
scala> val a = sc.parallelize(List("dog","cat","gnu","salmon","rabbit","turkey","wolf","bear","bee"), 3)
a: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[87] at parallelize at <console>:27

scala> val b = sc.parallelize(List(1,1,2,2,2,1,2,2,2), 3)
b: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[88] at parallelize at <console>:27

scala> val c = b.zip(a)
c: org.apache.spark.rdd.RDD[(Int, String)] = ZippedPartitionsRDD2[89] at zip at <console>:31

scala> c.collect
res78: Array[(Int, String)] = Array((1,dog), (1,cat), (2,gnu), (2,salmon), (2,rabbit), (1,turkey), (2,wolf), (2,bear), (2,bee))

scala> def createCombiner(v:String):List[String] = List[String](v)
createCombiner: (v: String)List[String]

scala> def mergeValue(acc:List[String], value:String) : List[String] = value :: acc
mergeValue: (acc: List[String], value: String)List[String]

scala> def mergeCombiners(acc1:List[String], acc2:List[String]) : List[String] = acc2 ::: acc1
mergeCombiners: (acc1: List[String], acc2: List[String])List[String]

scala> val result=c.combineByKey(createCombiner,mergeValue,mergeCombiners)
result: org.apache.spark.rdd.RDD[(Int, List[String])] = ShuffledRDD[91] at combineByKey at <console>:39

scala> result.collect
res80: Array[(Int, List[String])] = Array((1,List(turkey, cat, dog)), (2,List(bee, bear, wolf, rabbit, salmon, gnu)))



Parallelism

Every RDD has a fixed number of partitions that determine the degree of parallelism to use when executing operations on RDD
Tuning

When performing aggregations and grouping operations, you can tell Spark to use a specific number of partitions. For e.g. reduceByKey(func, customParallelismOrPartitionsToSue)
If you want to change the partitioning of an RDD outside of these grouping or aggregation operations, then you can use repartition() or coaleesce()
repartition() reshuffles the data acroos network to create a new set of partitions
coalesce() avoids reshuffling data ONLY if we are decreasing the number of existing RDD partitions (Use rdd.partitions.size() to see the current RDD partitions)
Tackle performance intensive commands like collect(), groupByKey(), reduceByKey()
See this post to see a small e.g. of how coalesc works
We can run multiple jobs within a single SparkContext parallerly by using threads. Look at the answers from Patrick Liu and yuanbosoft in this post
Within each Spark application, multiple “jobs” (Spark actions) may be running concurrently if they were submitted by different threads. This is common if your application is serving requests over the network.
Improving Garbage Collection
Below JDK 1.7 update 4
CMS - Concurrent Mark and Sweep
focus is on low-latency (doesn't do compaction)
hence use it for real-time streaming
Parallel GC
Focus is on higher-throughput but results in higher pause-times (performs whole-heap only compaction.
hence use it for batch
Above or equal to JDK 1.7 update 4
G1
Primarily meant for server-side and multi-core machines with large memory


