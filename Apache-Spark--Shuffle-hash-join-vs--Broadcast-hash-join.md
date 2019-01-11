
The default implementation of a join in Spark is a shuffled hash join. The shuffled hash join ensures that data on each partition will contain the same keys by partitioning the second dataset with the same default partitioner as the first, so that the keys with the same hash value from both datasets are in the same partition. While this approach always works, it can be more expensive than necessary because it requires a shuffle. The shuffle can be avoided if:

1. Both RDDs have a known partitioner.

2. One of the datasets is small enough to fit in memory, in which case we can do a broadcast hash join 


The easiest optimization is that if one of the datasets is small enough to fit in memory, it should be broadcasted to every compute node. This use case is very common as data needs to be combined with side data, such as a dictionary, all the time.

Mostly joins are slow due to too much data being shuffled over the network. With the Broadcast join, the smaller dataset is copied to all the worker nodes so the original parallelism of the larger DataFrame is maintained. 

Example :

      import org.apache.spark.sql.functions.broadcast  

      val employeesDF = employeesRDD.toDF
      val departmentsDF = departmentsRDD.toDF

      // materializing the department data
      val tmpDepartments = broadcast(departmentsDF.as("departments"))

      import context.implicits._

      employeesDF.join(broadcast(tmpDepartments), 
      $"depId" === $"id",  // join by employees.depID == departments.id 
      "inner").show()

   
   
- https://github.com/jaceklaskowski/mastering-apache-spark-book/blob/master/spark-sql-joins-broadcast.adoc
- https://www.youtube.com/watch?v=fp53QhSfQcI   
- https://books.google.com/books/about/High_Performance_Spark.html?id=90glDwAAQBAJ