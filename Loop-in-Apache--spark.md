val data = Array(Array(1,2,3), Array(4,5,6,7,8))
val rdd = sc.parallelize(data, 2)

// Print the RDD of arrays.
rdd.collect().foreach(a => println(a.size))

// Use map() to create an RDD with the array sizes.
val countRDD = rdd.map(a => a.size)

// Print the elements of this new RDD.
countRDD.collect().foreach(a => println(a))

// Use filter() to create an RDD with just the longer arrays.
val bigRDD = rdd.filter(a => a.size > 3)

// Print each remaining array.
bigRDD.collect().foreach(a => {
    a.foreach(e => print(e + " "))
    println()
  })




      3
      5 
      3
       5
       4 5 6 7 8 
      data: Array[Array[Int]] = Array(Array(1, 2, 3), Array(4, 5, 6, 7, 8))
      rdd: org.apache.spark.rdd.RDD[Array[Int]] = ParallelCollectionRDD[0] at parallelize at command-4074328521026683:5
      countRDD: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[1] at map at command-4074328521026683:11
      bigRDD: org.apache.spark.rdd.RDD[Array[Int]] = MapPartitionsRDD[2] at filter at command-4074328521026683:17
