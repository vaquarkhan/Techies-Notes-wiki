* Spark application consists of a driver program that runs the user’s main function and executes various parallel operations on a cluster.

* Spark provides is a resilient distributed dataset (RDD), which is a collection of elements partitioned across the nodes of the cluster that can be operated on in parallel.

* RDDs automatically recover from node failures.

* Spark is shared variables that can be used in parallel operations. By default, when Spark runs a function in parallel as a set of tasks on different nodes, it ships a copy of each variable used in the function to each task. Sometimes, a variable needs to be shared across tasks, or between tasks and the driver program.

* Spark supports two types of shared variables: 
   1. Broadcast variables, which can be used to cache a value in memory on all node.
   2. Accumulators, which are variables that are only “added” to, such as counters and sums.


* There are two ways to create RDDs:
  1. Parallelizing an existing collection in your driver program.

                  val data = Array(1, 2, 3, 4, 5)
                  val distData = sc.parallelize(data)

                  distData.reduce((a, b) => a + b) 

  2. Referencing a dataset in an external storage system, such as a shared filesystem, HDFS, HBase, or any data source offering a Hadoop InputFormat.


**Note :One important parameter for parallel collections is the number of partitions to cut the dataset into. Spark will run one task for each partition of the cluster. Typically you want 2-4 partitions for each CPU in your cluster. Normally, Spark tries to set the number of partitions automatically based on your cluster. However, you can also set it manually by passing it as a second parameter to parallelize (e.g. sc.parallelize(data, 10)). Note: some places in the code use the term slices (a synonym for partitions) to maintain backward compatibility.**

* External Datasets:
         
         val textFile = sc.textFile("///home/osboxes/spark/README.md")
         textFile: org.apache.spark.rdd.RDD[String] = ///home/osboxes/spark/README.md MapPartitionsRDD[13] 
         at textFile at <console>:25



*** Some notes on reading files with Spark:**

1. If using a path on the local filesystem, the file must also be accessible at the same path on worker nodes. Either copy the file to all workers or use a network-mounted shared file system.

1. All of Spark’s file-based input methods, including textFile, support running on directories, compressed files, and wildcards as well. For example, you can use textFile("/my/directory"), textFile("/my/directory/*.txt"), and textFile("/my/directory/*.gz").

1. The textFile method also takes an optional second argument for controlling the number of partitions of the file. By default, Spark creates one partition for each block of the file (blocks being 64MB by default in HDFS), but you can also ask for a higher number of partitions by passing a larger value. Note that you cannot have fewer partitions than blocks.

1. Apart from text files, Spark’s Scala API also supports several other data formats:

1. SparkContext.wholeTextFiles lets you read a directory containing multiple small text files, and returns each of them as (filename, content) pairs. This is in contrast with textFile, which would return one record per line in each file.

1. For SequenceFiles, use SparkContext’s sequenceFile[K, V] method where K and V are the types of key and values in the file. These should be subclasses of Hadoop’s Writable interface, like IntWritable and Text. In addition, Spark allows you to specify native types for a few common Writables; for example, sequenceFile[Int, String] will automatically read IntWritables and Texts.

1. For other Hadoop InputFormats, you can use the SparkContext.hadoopRDD method, which takes an arbitrary JobConf and input format class, key class and value class. Set these the same way you would for a Hadoop job with your input source. You can also use SparkContext.newAPIHadoopRDD for InputFormats based on the “new” MapReduce API (org.apache.hadoop.mapreduce).

1. RDD.saveAsObjectFile and SparkContext.objectFile support saving an RDD in a simple format consisting of serialized Java objects. While this is not as efficient as specialized formats like Avro, it offers an easy way to save any RDD.



**RDD Operations**

  1. Transformations, which create a new dataset from an existing one(All transformations Lazy not executed until call action).
  1. Actions, which return a value to the driver program after running a computation on the dataset.

**NOTE:By default, each transformed RDD may be recomputed each time you run an action on it. However, you may also persist an RDD in memory using the persist (or cache) method, in which case Spark will keep the elements around on the cluster for much faster access the next time you query it. There is also support for persisting RDDs on disk, or replicated across multiple nodes.**

**Passing Functions to Spark**
Spark’s API relies heavily on passing functions in the driver program to run on the cluster. There are two recommended ways to do this

  1. Anonymous function syntax, which can be used for short pieces of code. 
  1. Static methods in a global singleton object. 

