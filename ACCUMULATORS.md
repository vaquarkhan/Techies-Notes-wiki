Accumulators provide a syntax to aggregate values from all worker nodes back to the driver program.  For example, a common pattern for accumulators is counting events which occur during a job execution.  An example using ham radio operators’ call log is provided to count the number of times the log contains a blank line.

Scala and Python examples are provided which use the SparkContext from console:

blankLines = sc.accumulator(0)

**How do accumulators work?**

As seen in code example above, Accumulators are created in the driver program by calling the SparkContext.accumulator( initialValue). The return type is an org.apache.spark.Accumulator[ T] object, where T is the type of initialValue. Worker code can add to the accumulator with += method (or add in Java). The driver program can obtain value of the accumulator by calling value().

Spark supports accumulators of type Double, Long, and Float natively and developers may write their own custom accumulators as well.

**ACCUMULATORS AND FAULT TOLERANCE**

Spark automatically deals with failed or slow machines by re-executing failed or slow tasks.

How does this fault tolerance potentially affect accumulators?

In order to obtain a true value counter which is immune to any node failures or retry of failed tasks, it must be put inside an action like foreach().

**BROADCAST VARIABLES**

Spark broadcast variables are used by the driver program to efficiently send a large, read-only values to all the worker nodes. For example, if your application needs to send a large, read-only lookup reference table to all the nodes, broadcasting this as a variable is a solid approach.

**Broadcast Background**

It’s important to remember that Spark automatically sends all variables referenced in closures to the worker nodes. While this is convenient, it can also be inefficient because the same variable may be used in multiple parallel operations, but Spark will send it separately for each operation.  Hence, the value of broadcast variables.

Show examples of using broadcast variables in ham radio logs; Scala example:

val signPrefixes = sc.broadcast( loadCallSignTable())

where loadCallSignTable might be a lookup of ham radio call logs.

**BROADCAST OPTIMIZATION**

When broadcasting large values, it is important to choose an appropriate data serialization format.  Java Serialization, the default serialization library used in Spark’s Scala and Java APIs, can be very inefficient except arrays of primitive types. Optimizations may be made by utilizing an alternative serialization library such as Kryo.  This will be covered in more detail in Chapter 8.

**UTILIZING PARTITIONS FOR PERFORMANCE**

As previously mentioned, working with data on a per-partition basis allows us to avoid redoing setup work for each element. Spark has per-partition versions of map and foreach as well as mapPartitions(), mapPartitionsWithIndex(), foreachPartition()

Similar to previously mentioned serialization library for broadcast optimization, partitions and affect on performance is revisited in Chapter 8.

**SPARK AND EXTERNAL PROGRAMS**

Spark provides the pipe() method on RDDs to pipe data to programs in other languages, like R scripts.  Spark’s pipe() requires the external program to be able to read and write to Unix standard streams.

**NUMERIC RDD OPERATIONS**
Not mentioned in the overview, but Spark provides several statistics operations on RDDs which contain numeric data.

Methods available include count(), mean(), sum(), max(), min(), variance(), sampleVariance(), stdev(), sampleStdev()

These are in addition to the more complex statistical and machine learning methods we will be covered Chapter  11.

**Caveats**

When using accumulators there are some caveats that we as programmers need to be aware of,

Computations inside transformations are evaluated lazily, so unless an action happens on an RDD the transformationsare not executed. As a result of this, accumulators used inside functions like map() or filter() wont get executed unless some action happen on the RDD.
Spark guarantees to update accumulators inside actionsonly once. So even if a task is restarted and the lineage is recomputed, the accumulators will be updated only once.
Spark does not guarantee this for transformations. So if a task is restarted and the lineage is recomputed, there are chances of undesirable side effects when the accumulators will be updated more than once.
To be on the safe side, always use accumulators inside actions ONLY.

----------------------------------------------------------------------------------------------

    scala> val accum = sc.accumulator(0)

    scala> sc.parallelize(Array(1, 2, 3, 4)).foreach(x => accum += x)
 
     scala> accum.value
 
--------------------------------------------------------------------------------------------

* http://spark.apache.org/docs/latest/programming-guide.html#accumulators

* https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-accumulators.html

* https://www.tutorialspoint.com/apache_spark/advanced_spark_programming.htm

* https://stackoverflow.com/questions/29494452/when-are-accumulators-truly-reliable

