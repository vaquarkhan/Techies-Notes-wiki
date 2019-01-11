mapPartitions() Example

mapPartitions() can be used as an alternative to map() & foreach(). mapPartitions() is called once for each Partition unlike map() & foreach() which is called for each element in the RDD. The main advantage being that, we can do initialization on Per-Partition basis instead of per-element basis(as done by map() & foreach())

Consider the case of Initializing a database. If we are using map() or foreach(), the number of times we would need to initialize will be equal to the no of elements in RDD. Whereas if we use mapPartitions(), the no of times we would need to initialize would be equal to number of Partitions

We get Iterator as an argument for mapPartition, through which we can iterate through all the elements in a Partition. 

In this example, we will use mapPartitionsWithIndex(), which apart from similar to mapPartitions() also provides an index to track the Partition No


    `scala> val rdd1 =  sc.parallelize(`
     `|                List(`
     `|                   "yellow",   "red",`
     `|                   "blue",     "cyan",`
     `|                   "black"`
     `|                ),`
     `|                3`
     `|             )`
      `rdd1: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[10] at parallelize at :21`

      `scala>`

      `scala> val mapped =   rdd1.mapPartitionsWithIndex{`
     `|                   // 'index' represents the Partition No`
     `|                   // 'iterator' to iterate through all elements`
     `|                   //                         in the partition`
     `|                   (index, iterator) => {`
     `|                      println("Called in Partition -> " + index)`
     `|                      val myList = iterator.toList`
     `|                      // In a normal user case, we will do the`
     `|                      // the initialization(ex : initializing database)`
     `|                      // before iterating through each element`
     `|                      myList.map(x => x + " -> " + index).iterator`
     `|                   }`
     `|                }`
      `mapped: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[11] at mapPartitionsWithIndex at :23`

      `scala>`
     `| mapped.collect()`
      `Called in Partition -> 1`
      `Called in Partition -> 2`
       `Called in Partition -> 0`
       `res7: Array[String] = Array(yellow -> 0, red -> 1, blue -> 1, cyan -> 2, black -> 2)`