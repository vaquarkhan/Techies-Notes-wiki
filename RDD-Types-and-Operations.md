**RDD**
On cluster installations, separate data partitions can be on separate nodes. Using the RDD as a handle one can access all partitions and perform computations and transformations using the contained data. Whenever a part of a RDD or an entire RDD is lost, the system is able to reconstruct the data of lost partitions by using lineage information. **Lineage **refers to the sequence of transformations used to produce the current RDD. As a result, Spark is able to recover automatically from most failures.

All RDDs available in Spark derive either directly or indirectly from the class RDD. This class comes with a large set of methods that perform operations on the data within the associated partitions. The class RDD is abstract. Whenever, one uses a RDD, one is actually using a concertized implementation of RDD. These implementations have to overwrite some core functions to make the RDD behave as expected.

One reason why Spark has lately become a very popular system for processing big data is that it does not impose restrictions regarding what data can be stored within RDD partitions. The RDD API already contains many useful operations. But, because the creators of Spark had to keep the core API of RDDs common enough to handle arbitrary data-types, many convenience functions are missing.

The basic RDD API considers each data item as a single value. However, users often want to work with key-value pairs. Therefore Spark extended the interface of RDD to provide additional functions (PairRDDFunctions), which explicitly work on key-value pairs. Currently, there are four extensions to the RDD API available in spark. They are as follows:


**Creating RDDs**

Spark provides two ways to create RDDs: loading an external dataset and parallelizing a collection in your driver program.

The simplest way to create RDDs is to take an existing collection in your program and pass it to SparkContext’s parallelize() method.

This approach is very useful when you are learning Spark, since you can quickly create your own RDDs in the shell and perform operations on them. Keep in mind, however, that outside of prototyping and testing, this is not widely used since
it requires that you have your entire dataset in memory on one machine.

    Example parallelize() method in Python
    lines = sc.parallelize(["pandas", "i like pandas"])

    Example parallelize() method in Scala
    val lines = sc.parallelize(List("pandas", "i like pandas"))

    Example parallelize() method in Java
    JavaRDD<String> lines = sc.parallelize(Arrays.asList("pandas", "i like pandas"));

A more common way to create RDDs is to load data from external storage. we already saw one method that loads a text file as an RDD of strings, SparkContext.textFile(), whichis shown in Examples.

    Example textFile() method in Python
    lines = sc.textFile("/path/to/README.md")

    Example textFile() method in Scala
    val lines = sc.textFile("/path/to/README.md")

    Example textFile() method in Java
    JavaRDD<String> lines = sc.textFile("/path/to/README.md");


******Type of RDDs******


* ****DoubleRDDFunctions ****
  This extension contains many useful methods for aggregating numeric values. They become available if the data items of an RDD are implicitly convertible to the Scala data-type double.

* ****PairRDDFunctions ****
  Methods defined in this interface extension become available when the data items have a two component tuple structure. Spark will interpret the first tuple item (i.e. tuplename. 1) as the key and the second item (i.e. tuplename. 2) as the associated value.

* ****OrderedRDDFunctions ****
  Methods defined in this interface extension become available if the data items are two-component tuples where the key is implicitly sortable.

* ****SequenceFileRDDFunctions ****
  This extension contains several methods that allow users to create Hadoop sequence- les from RDDs. The data items must be two compo- nent key-value tuples as required by the PairRDDFunctions. However, there are additional requirements considering the convertibility of the tuple components to Writable types.






**Creating Pair RDDs**
Pair RDDs are a useful building block in many programs, as they expose operations that allow you to act on each key in parallel or regroup data across the network. For example, pair RDDs have a reduceByKey() method that can aggregate data separately for each key, and a join() method that can merge two RDDs together by grouping elements with the same key.

For example, pair RDDs have a reduceByKey() method that can aggregate data separately for each key, and a join() method that can merge two RDDs together by grouping elements with the same key. It is common to extract fields from an RDD (representing, for instance, an event time, customer ID, or other identifier) and use those fields as keys in pair RDD operations.


There are a number of ways to get pair RDDs in Spark. Many formats we explore loading from in Chapter 5 will directly return pair RDDs for their key/value data. In other cases we have a regular RDD that we want to turn into a pair RDD. We can do this by running a map() function that returns key/value pairs. To illustrate, we show code that starts with an RDD of lines of text and keys the data by the first word in each line.

The way to build key-value RDDs differs by language. In Python, for the functions on keyed data to work we need to return an RDD composed of tuples Creating a pair RDD using the first word as the key in Python

    pairs = lines.map(lambda x: (x.split(" ")[0], x))

In Scala, for the functions on keyed data to be available, we also need to return tuples . An implicit conversion on RDDs of tuples exists to provide the additional key/value functions.

Creating a pair RDD using the first word as the key in Scala

    val pairs = lines.map(x => (x.split(" ")(0), x))

Java doesn’t have a built-in tuple type, so Spark’s Java API has users create tuples using the scala.Tuple2 class. This class is very simple: Java users can construct a new tuple by writing new Tuple2(elem1, elem2) and can then access its elements with the ._1() and ._2() methods.

Java users also need to call special versions of Spark’s functions when creating pair RDDs. For instance, the mapToPair() function should be used in place of the basic map() function. This is discussed in more detail in “Java” on page 43, but let’s look at a simple case in Example 

Creating a pair RDD using the first word as the key in Java

    PairFunction<String, String, String> keyData =
    new PairFunction<String, String, String>() {
    public Tuple2<String, String> call(String x) {
    return new Tuple2(x.split(" ")[0], x);
    }
    };
JavaPairRDD<String, String> pairs = lines.mapToPair(keyData);

    **SparkContext.parallelize()**


**Difference between RDD and Pair RDD**

Pair RDD is just a way of referring to an RDD containing key/value pairs, i.e. tuples of data. It's not really a matter of using one as opposed to using the other. For instance, if you want to calculate something based on an ID, you'd group your input together by ID. This example just splits a line of text and returns a Pair RDD using the first word as the key [1]:

    val pairs = lines.map(x => (x.split(" ")(0), x))
The Pair RDD that you end up with allows you to reduce values or to sort data based on the key, to name a few examples.

It would probably do you good to read the link at the bottom, from which I shamelessly copied the example, since the understanding of Pair RDDs and how to work with tuples is quite fundamental to many of the things that you will do in Spark. Read up on 'Transformations on Pair RDDs' to get an understanding of what you typically would want to do once you have your pairs.

* https://www.safaribooksonline.com/library/view/learning-spark/9781449359034/ch04.html

**How Many Partitions Does An RDD Have?**


* [https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/performance_optimization/how_many_partitions_does_an_rdd_have.html](https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/performance_optimization/how_many_partitions_does_an_rdd_have.html)

****Apache Spark Functions****

* [http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html](http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html)

* [http://alvinhenrick.com/2016/07/10/apache-spark-user-defined-functions/](http://alvinhenrick.com/2016/07/10/apache-spark-user-defined-functions/)


https://docs.google.com/spreadsheets/d/1F7NYBMvaH8Pq0rcF3ZfHFyjcaQIRoXnaqZQQsy-deAU/edit#gid=0


**Understand RDD operations: transformations and actions**

**Transformation** 


* filter(f: T => Boolean)	Return a FilteredRDD[T] having elemnts that f return true

* mapPartitions(Iterator[T] => Iterator[U])	Return a new MapPartitionsRDD[U] by applying a function to each partition

* sample(withReplacement, fraction, seed)	Return a new PartitionwiseSampledRDD[T] which is a sampled subset

* union(otherRdd[T])	Return a new UnionRDD[T] by making union with another Rdd

* intersection(otherRdd[T])	Return a new RDD[T] by making intersection with another Rdd

* distinct()	Return a new RDD[T] containing distinct elements

* groupByKey()	Being called on (K,V) Rdd, return a new RDD[([K], Iterable[V])]

* reduceByKey(f: (V, V) => V)	Being called on (K, V) Rdd, return a new RDD[(K, V)] by aggregating values using feg: 
reduceByKey(_+_)

* sortByKey([ascending])	Being called on (K,V) Rdd where K implements Ordered, return a new RDD[(K, V)] sorted by K

* join(other: RDD[(K, W))	Being called on (K,V) Rdd, return a new RDD[(K, (V, W))] by joining them

* cogroup(other: RDD[(K, W))	Being called on (K,V) Rdd, return a new RDD[(K, (Iterable[V], Iterable[W]))] such that for each key k in this & other, get a tuple with the list of values for that key in this as well as other

* cartesian(other: RDD[U])	 Return a  new RDD[(T, U)] by applying product



**Actions**

* reduce(f: (T, T) => T)	 return T by reducing the elements using specified commutative and associative binary operator
Example:

*     1 rdd = sc.parallelize(Array(1, 2, 3, 4, 5))
*     2 rdd.reduce((a, b) => a + b)

* collect()	Return an Array[T] containing all elements

* count()	Return the number of elements

* first()	Return the first element

* take(num)	Return an Array[T] taking first num elements

* takeSample(withReplacement, fraction, seed)	Return an Array[T] which is a sampled subset

* takeOrdered(num)(order)	Return an Array[T] having num smallest or biggest (depend on order) elements

* saveAsTextFile(fileName)

* saveAsSequenceFile(fileName)

* saveAsObjectFile(fileName)	Save (serialized) Rdd

* countByValue()	Return a Map[T, Long] having the count of each unique value

* countByKey()	Return a Map[K, Long] counting the number of elements for each key

* foreach(f: T=>Unit)	Apply function f to each element



**Transformation & lazy evaluation will bring us more chance of optimizing our job**

![https://trongkhoanguyenblog.files.wordpress.com/2014/11/schedule-process.png?w=640](https://trongkhoanguyenblog.files.wordpress.com/2014/11/schedule-process.png?w=640)



Suppose we are running a simple word count job:

    /* SimpleApp.scala */
    val logFile = "YOUR_SPARK_HOME/README.md"
    val conf = new SparkConf().setAppName("Simple Application")
    val sc = new SparkContext(conf)
    val logData = sc.textFile(logFile, 2).cache()
    val numAs = logData.filter(line => line.contains("a")).count()
    println("Lines with a: %s".format(numAs))

Starting by creating a Rdd object by using SparkContext, then we transform it with the filter transformation and finally call action count. When an action is called on rdd, the SparkContext will submit a job to the DAGScheduler – where the very first optimizations happen.

The DAGSchedule receives target Rdds, functions to run on each partition (pipe the transformations, action), and a listener for results. It will:
 – build Stages of Task objects (code + preferred location)
 – submit them to TaskScheduler as ready
 – Resubmit failed Stages if outputs are lost

The TaskScheduler is responsible for launching tasks at executors in our cluster, re-launch failed tasks several times, return the result to DAGScheduler.

We can now quickly summarize:
+ We submit a jar application which contains jobs
+ The job gets submitted to DAGScheduler via SparkContext will be split in to Stages. The DAGScheduler schedules the run order of these stages.
+ A Stage contains a set of tasks to run on Executors. The TaskScheduler schedules the run of tasks.


[RDD Dependency types and the optimization at DAGScheduler:](RDD Dependency types and the optimization at DAGScheduler:)

![](https://trongkhoanguyenblog.files.wordpress.com/2014/11/dependencies.png)


– Narrow dependency:  each partition of the parent RDD is used by at most one partition of the child RDD. This means the task can be executed locally and we don’t have to shuffle. (Eg: map, flatMap, Filter, sample)
– Wide dependency: multiple child partitions may depend on one partition of the parent RDD. This means we have to shuffle data unless the parents are hash-partitioned (Eg: sortByKey, reduceByKey, groupByKey, cogroupByKey, join, cartesian)

Thanks to the lazy evaluation technique, the Scheduler will be able to optimize the stages before submitting the job: pipelines narrow operations within a stage, picks join algorithms based on partitioning (try to minimize shuffles), reuses previously cached data.


![](https://trongkhoanguyenblog.files.wordpress.com/2014/11/scheduler.png)

* [https://trongkhoanguyenblog.wordpress.com/2015/01/05/source-code-analysis-narrow-dependency-wide-dependency-implementation-in-spark/](https://trongkhoanguyenblog.wordpress.com/2015/01/05/source-code-analysis-narrow-dependency-wide-dependency-implementation-in-spark/)
* [http://www.trongkhoanguyen.com/](http://www.trongkhoanguyen.com/)

