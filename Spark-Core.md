

> This tutorial provides a quick introduction to using Spark. It demonstrates the basic functionality of RDD and DataFrame API.We have tried to cover basics of Spark Core, SQL, Streaming, ML and GraphX programming contexts.

> Spark Core contains the basic functionality of Spark, including components for task
scheduling, memory management, fault recovery, interacting with storage systems,
and more. Spark Core is also home to the API that defines resilient distributed data‐
sets (RDDs), which are Spark’s main programming abstraction. RDDs represent a
collection of items distributed across many compute nodes that can be manipulated
in parallel. Spark Core provides many APIs for building and manipulating these
collections.

We are using SparkCommon  from Utils package to run the Examples of  in this tutorial.

```Scala
object SparkCommon {

  lazy val conf = {
    new SparkConf(false)
      .setMaster("local[*]")
      .setAppName("Spark Tutorial")
  }
  lazy val sparkContext = new SparkContext(conf)
  lazy val sparkSQLContext = SQLContext.getOrCreate(sparkContext)
  lazy val streamingContext = StreamingContext.getActive()
    .getOrElse(new StreamingContext(sparkContext, Seconds(2)))
}
```

#### Introduction to Apache Spark
> Apache Spark is a powerful open source processing engine built around speed, ease of use, and sophisticated analytics.It is a cluster computing framework originally developed in the AMPLab at University of California, Berkeley but was later donated to the Apache Software Foundation where it remains today.
> Apache Spark is a lightning-fast cluster computing technology, designed for fast computation.
> It is a framework for performing general data analytics on distributed computing cluster like Hadoop.
> The main feature of Spark is its in-memory cluster computing that increases the processing speed of an application.
> It provides in memory computations for increase speed and data process over mapreduce.It runs on top of existing hadoop cluster and access hadoop data store (HDFS), can also process structured data in Hive and Streaming data from HDFS,Flume,Kafka,Twitter.


#### Features of Apache Spark

> Some of Spark's features which are really highlighting it in the Big Data world.

##### 1. Speed

> Spark can be 100x faster than Hadoop for large scale data processing by exploiting in memory computing and other optimizations.
> Spark makes it possible by reducing number of read/write to disc. It stores this intermediate processing data in-memory. It uses the concept of an Resilient Distributed Dataset (RDD), which allows it to transparently store data on memory and persist it to disc only it’s needed.

##### 2. Ease of Use

> Spark has easy-to-use APIs for operating on large datasets. This includes a collection of over 100 operators for transforming data and familiar data frame APIs for manipulating semi-structured data.
> Spark lets you quickly write applications in Java, Scala, or Python. This helps developers to create and run their applications on their familiar programming languages and easy to build parallel apps. 

##### 3.Combines SQL, streaming, and complex analytics.

> In addition to simple “map” and “reduce” operations, Spark supports SQL queries, streaming data, and complex analytics such as machine learning and graph algorithms out-of-the-box. Not only that, users can combine all these capabilities seamlessly in a single workflow.

##### 4. Advanced Analytics 

> Spark not only supports ‘Map’ and ‘reduce’. It also supports SQL queries, Streaming data, Machine learning (ML), and Graph algorithms.

##### 5. A Unified Engine 

> Spark comes packaged with higher-level libraries, including support for SQL queries, streaming data, machine learning and graph processing.
> These standard libraries increase developer productivity and can be seamlessly combined to create complex workflows.

##### 6.Runs Everywhere

> Spark runs on Hadoop, Mesos, standalone, or in the cloud. It can access diverse data sources including HDFS, Cassandra, HBase, S3.

#### Initializing Spark

> Spark program must do is to create a SparkContext object, which tells Spark how to access a cluster. To create a SparkContext you first need to build a SparkConf object that contains information about your application.
> The appName parameter is a name for your application to show on the cluster UI. master is a Spark, Mesos or YARN cluster URL, or a special “local” string to run in local mode. 

```scala
val conf = new SparkConf().setAppName("appName").setMaster("master")
new SparkContext(conf)
```


`Note:` Only one SparkContext may be active per JVM. You must stop() the active SparkContext before creating a new one.

#### RDDs

> An RDD is an immutable distributed collection of objects. Where Each RDD is split into multiple partitions, which may be computed on different nodes of the cluster. RDDs can contain any type ofScala ,Python , orJava   objects, including user-defined classes.

##### Create RDDs

> There are two ways to create RDDs − parallelizing an existing collection in your driver program, or referencing a  dataset in an external storage system

##### Create RDDs using parallelize() method of SparkContext 

```scala
val lines = sc.parallelize(List("pandas", "i like pandas"))
lines.collect().map(println)
```
![alt text](https://github.com/rklick-solutions/spark-tutorial/wiki/images/Parallelized.png)

##### Create RDDs using External Datasets textFile() method of SparkContext

```scala

val inputFile = sc.textFile("src/main/resources/test_file.txt") // Load our input data.
val count = inputFile.flatMap(line => line.split(" ")) // Split it up into words.
.map(word => (word, 1)).reduceByKey(_ + _) // Transform into pairs and count.
```
![alt text](https://github.com/rklick-solutions/spark-tutorial/wiki/images/text.png)

#### RDDs Operations
> The concept of a resilient distributed dataset (RDD), which is a fault-tolerant collection of elements that can be operated on in parallel.
> RDDs support two types of operations: transformations, which create a new dataset from an existing one, and actions, which return a value to the driver program after running a computation on the dataset. 

#### RDDs support two types of operations:

##### 1.Transformations
> Transformations are operations on RDDs that return a new RDD.
> Some basic common transformations fuctions supported by Spark.

##### map()

> Apply a map() function to each element in the RDD and return an RDD of the result.

```scala
val input1 = sc.parallelize(List(1, 2, 3, 4,5,6,7,8,9))
val result1 = input.map(x => x + 1)
println("Mapping:" + result1.collect().mkString(","))
```

![map](https://github.com/rklick-solutions/spark-tutorial/wiki/images/map.png)

##### filter()

> filter() transformation  function Return an RDD consisting of only elements
> that pass the condition passed to filter()

```scala
val filterInput = sc.parallelize(List(1, 2, 3, 4,5,6,7))
val filterResult = filterInput.filter(x => x != 1)
println("Filter:" + filterResult.collect().mkString(","))
```

![filter.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/filter.png)

##### flatMap()

> Similar to map, but each input item can be mapped to 0 or more output items (so func should return a Seq rather than a single item).

```scala
val flatMapLines = sc.parallelize(List("hello world", "hi"))
val flatMapResult = inputLine.flatMap(line => line.split(" "))
println("flatMap:" + flatMapResult.first())
```

![flatmap.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/flatmap.png)

##### mapPartitions()

> Similar to map, but runs separately on each partition (block) of the RDD.

```scala
val inputData = sc.parallelize(1 to 9, 3)
val inputData1 = sc.parallelize(1 to 9)
val mapPartitionResult = inputData.mapPartitions(x => List(x.next).iterator)
println("mapPartition is :" + mapPartitionResult.collect().mkString(","))
```

![mapPartition.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/mapPartition.png)

##### mapPartitionsWithIndex()

> Integer of partition number,and Iterator of the elements in that partition.

```scala
val inputData =sc.parallelize(1 to 9 ,3)    
val mapPartitionsWithIndexRseult = inputData.mapPartitionsWithIndex((index: Int, it: Iterator[Int])
=> it.toList.map(x => index + ", " + x).iterator)
println("mapPartitionsWithIndex :" + mapPartitionsWithIndexRseult.collect().mkString(","))
```

![mapPartitionWithIndex.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/mapPartitionWithIndex.png)

##### union()

> Returns a new dataset that contains the union of the elements in the source dataset and the argument.

```scala
val inputRdd1 = sc.parallelize(List(1, 2, 3))
val inputRdd2 = sc.parallelize(List(3, 5, 7))
val resultInputUnion = inputRdd1.union(inputRdd2)
println("Union:" + resultInputUnion.collect().mkString(","))
```

![union.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/union.png)

##### intersection()

> Returns a new RDD that contains the intersection of elements in the source dataset and the argument.

```scala
val inputRdd1 = sc.parallelize(List(1, 2, 3))
val inputRdd2 = sc.parallelize(List(3, 5, 7))
val resultIntersection = inputRdd1.intersection(inputRdd2)
println("Intersection:" + resultIntersection.collect().mkString(","))
```

![intersection.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/intersection.png)

##### distinct()

> Returns a new dataset that contains the distinct elements of the source dataset.

```scala
val distinctInput = sc.parallelize(List(1, 2, 3, 4, 2, 1, 3, 4, 2, 5, 6))
val distinctResult = distinctInput.distinct()
println("distinct:" + distinctResult.collect().mkString(","))
```

![distinct.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/distinct.png)

##### subtract()

> subtract() transformation  function Remove the contents of one RDD(e.g.,remove training data).

```scala
val inputSubtract1 = sc.parallelize(List(1, 2, 3))
val inputSubtract2 = sc.parallelize(List(3, 5, 6))
val resultSub = inputSubtract1.subtract(inputSubtract2)
println("subtract:" + resultSub.collect().mkString(","))
```

![subtract.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/subtract.png)

##### groupByKey()

> Group values with the same key.

```scala
val inputTupplesList = sc.parallelize(List((1, 2), (3, 5), (3, 7)))
val resultGroupByKey = inputTupplesList.reduceByKey((x, y) => x + y)
println("Group values with the same key:" + resultGroupByKey.collect().mkString(","))
```

![group.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/group.png)

##### reduceByKey()

> Combine values with the same key.

```scala
val inputTupplesIs = sc.parallelize(List((1, 2), (3, 4), (4, 6)))
val resultReduceByKey = inputTupplesIs.reduceByKey((x, y) => x + y)
println("Combine values with the same Key:" + resultReduceByKey.collect().mkString(","))
```

![combine.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/combine.png)

##### sortByKey()

> When called on a dataset of (K, V) pairs where K implements Ordered, returns a dataset of (K, V) pairs sorted by keys in ascending or descending order, as specified in the Boolean ascending argument.

```scala
val inputTupples = sc.parallelize(List((1, 2), (3, 5), (3, 7)))
val sortKey = inputTupples.sortByKey()
println("sortkey:" + sortKey.collect().mkString(","))
```

![sort.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/sort.png)

##### join()

> When called on datasets of type (K, V) and (K, W), returns a dataset of (K, (V, W)) pairs with all pairs of     elements for each key. 

```scala
val inputTupple1 = sc.parallelize(List((1, 2), (3, 4), (4, 7)))
val inputTupple2 = sc.parallelize(List((5, 9)))
val resultJoin = inputTupple1.join(inputTupple2)
println("inner join between two RDDs." + resultJoin.collect().mkString(","))
```
![inner.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/inner.png)

##### cogroup()

> Group data from both RDDs sharing the same key.

```scala
val inputElementTupple1 = sc.parallelize(List((1, 2), (3, 4), (4, 6)))
val inpuElementtTupple2 = sc.parallelize(List((4, 9)))
val resultGroup = inputElementTupple1.cogroup(inputElementTupple1)
println("Group data from both RDDs sharing the same key :" + resultGroup.collect().mkString(","))
```
![cog.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/cog.png)

##### cartesian()

> When called on datasets of types T and U, returns a dataset of (T, U) pairs (all pairs of elements).

```scala
val inputCartesian1 = sc.parallelize(List(1, 2, 3))
val inputCartesian2 = sc.parallelize(List(3, 5, 7))
val resultCartesian = inputCartesian1.cartesian(inputCartesian2)
println("cartesian:" + resultCartesian.collect().mkString(","))
```
![cartesian.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/cartesian.png)

##### 2.Actions

> Actions are operations that return a result to the driver program or write it to storage
> Some basic common actions fuctions supported by Spark.

##### reduce()

> reduce(func) Combine the elements of the RDD together in parallel.

```scala
val input = sc.parallelize(List(3, 2, 4, 6))
val inputs= sc.parallelize(List(2, 4, 2, 3))
val rUnion = input.union(inputs)
val resultReduce = rUnion.reduce((x, y) => x + y)
println("reduce:" + resultReduce + " ")
```
![reduce.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/reduce.png)

##### collect()

> collect() Return all elements from the RDD.

```scala
val inputElement = sc.parallelize(List(2, 3, 4, 4))
println("collect" + inputElement.collect().mkString(","))
```
![collect.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/collect.png)

##### count()

> count() returns a count of the elements the RDD.

```scala
val inputCount = sc.parallelize(List(2, 3, 4, 4))
println(" count:" + inputCount.count())
```
![count.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/count.png)

##### first()

> Returns the first element of the dataset (similar to take (1)).

```scala
val inputFirst =sc.parallelize(List(2, 3, 4, 4))
println("fist:"+ inputFirst.first())
```
![first.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/first.png)

##### take()

> take(num) Return num elements from the RDD.

```scala
val inputTake = sc.parallelize(List(2, 3, 4, 4))
println("take :" + inputTake.take(2).mkString(","))
```
![take.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/take.png)

##### takeSample()

> Similar to take, in return type of array with size of n.
> Includes boolean option of with or without replacement and random generator seed.

```scala
val takeSampleInput = sc.parallelize(List(2, 3, 4, 4))
val takeSampleResult =takeSampleInput.takeSample(true, 1)
println("takeSample :" + takeSampleResult.mkString(","))
```
![takesample.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/takesample.png)

##### takeOrdered()

> takeOrdered(num)(ordering) Return num elements based on provided ordering

```scala
val inputOrder = sc.parallelize(List(2, 3, 4, 4))
println("Take Order :" + inputOrder.takeOrdered(2).mkString(","))
```
![takeorder.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/takeorder.png)

##### saveAsTextFile()

> Write the elements of the dataset as a text file (or set of text files) in a given directory in the local filesystem.

```scala
val inputFile = sc.textFile("src/main/resources/test_file.txt") // Load our input data.
val count = inputFile.flatMap(line => line.split(" ")) // Split it up into words.
.map(word => (word, 1)).reduceByKey(_ + _) // Transform into pairs and count.
//Save the word count back out to a text file, causing evaluation.
count.saveAsTextFile(s"src/main/resources/${UUID.randomUUID()}")
println("OK")
```
![on.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/ok.png)
![filesave.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/filesave.png)

##### lookup(key)

>lookup(key) Return all values associated with the provided key.

```scala
val inputLookUp = sc.parallelize(List((1, 2), (2, 3), (3, 4)))
println("lookup key:" + inputLookUp.lookup(3).mkString(","))
```
![lookup.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/lookup.png)

##### countByKey()

> countByKey() Count the number of elements for each key.

```scala
val inputAction = sc.parallelize(List((1, 2), (2, 3), (5, 4)))
println("countByKey :" + inputAction.countByKey().mkString(","))
```
![countkey.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/countkey.png)

##### foreach()

> Runs a function func on each element of the dataset.

```scala
val inputForeach = sc.parallelize(List(2, 3, 4, 4))
inputForeach.foreach(x => println(x + 1))
```
![each.png](https://github.com/rklick-solutions/spark-tutorial/wiki/images/each.png)



