**collectAsMap() action operation **
Scaladoc says :
def collectAsMap(): Map[K, V]
Return the key-value pairs in this RDD to the master as a Map.
Warning: this doesn't return a multimap (so if you have multiple values to the same key, only one value per key is preserved in the map returned)
Note : this method should only be used if the resulting data is expected to be small, as all the data is loaded into the driver's memory.

              scala> val rdd1 = sc.parallelize(Seq(("Spark",78),("Hive",95),("spark",15),("HBase",25),("spark",39),("BigData",78),("spark",49)))
              rdd1: org.apache.spark.rdd.RDD[(String, Int)] = ParallelCollectionRDD[0] at parallelize at <console>:27

             scala> rdd1.collectAsMap
             res0: scala.collection.Map[String,Int] = Map(spark -> 49, BigData -> 78, Spark -> 78, HBase -> 25, Hive -> 95)

I was unable to understand why it returns spark-> 49. It returns 49 based on hashcode / hashvalue or what i could not get it.
Docs says that from multiple values it returns only one value 

