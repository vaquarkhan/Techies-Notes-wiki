%md
## The Abstraction of Resilient Distributed Dataset (RDD)

#### RDD is a fault-tolerant collection of elements that can be operated on in parallel

#### Two types of Operations are possible on an RDD
* Transformations
* Actions

**(watch now 2:26)**:

[![RDD in Spark by Anthony Joseph in BerkeleyX/CS100.1x](http://img.youtube.com/vi/3nreQ1N7Jvk/0.jpg)](https://www.youtube.com/v/3nreQ1N7Jvk?rel=0&autoplay=1&modestbranding=1&start=1&end=146)

***

## Transformations
**(watch now 1:18)**:

[![Spark Transformations by Anthony Joseph in BerkeleyX/CS100.1x](http://img.youtube.com/vi/360UHWy052k/0.jpg)](https://www.youtube.com/v/360UHWy052k?rel=0&autoplay=1&modestbranding=1)

***


## Actions
**(watch now 0:48)**:

[![Spark Actions by Anthony Joseph in BerkeleyX/CS100.1x](http://img.youtube.com/vi/F2G4Wbc5ZWQ/0.jpg)](https://www.youtube.com/v/F2G4Wbc5ZWQ?rel=0&autoplay=1&modestbranding=1&start=1&end=48)

***

**Key Points**
* Resilient distributed datasets (RDDs) are the primary abstraction in Spark.
* RDDs are immutable once created:
    * can transform it.
    * can perform actions on it.
    * but cannot change an RDD once you construct it.
* Spark tracks each RDD's lineage information or recipe to enable its efficient recomputation if a machine fails.
* RDDs enable operations on collections of elements in parallel.
* We can construct RDDs by:
    * parallelizing Scala collections such as lists or arrays
    * by transforming an existing RDD,
    * from files in distributed file systems such as (HDFS, S3, etc.).
* We can specify the number of partitions for an RDD
* The more partitions in an RDD, the more opportunities for parallelism
* There are **two types of operations** you can perform on an RDD:
    * **transformations** (are lazily evaluated) 
      * map
      * flatMap
      * filter
      * distinct
      * ...
    * **actions** (actual evaluation happens)
      * count
      * reduce
      * take
      * collect
      * takeOrdered
      * ...
* Spark transformations enable us to create new RDDs from an existing RDD.
* RDD transformations are lazy evaluations (results are not computed right away)
* Spark remembers the set of transformations that are applied to a base data set (this is the lineage graph of RDD) 
* The allows Spark to automatically recover RDDs from failures and slow workers.
* The lineage graph is a recipe for creating a result and it can be optimized before execution.
* A transformed RDD is executed only when an action runs on it.
* You can also persist, or cache, RDDs in memory or on disk (this speeds up iterative ML algorithms that transforms the initial RDD iteratively).
* Here is a great reference URL for working with Spark.
    * [The latest Spark programming guide](http://spark.apache.org/docs/latest/programming-guide.html) and it is embedded below in-place for your convenience.
    