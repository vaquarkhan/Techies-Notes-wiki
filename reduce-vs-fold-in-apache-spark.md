There is no practical difference when it comes to performance whatsoever:

RDD.fold action is using fold on the partition Iterators which is implemented using foldLeft.
RDD.reduce is using reduceLefton the partition Iterators.
Both methods keep mutable accumulator and process partitions sequentially using simple loops with foldLeft implemented like 

https://github.com/scala/scala/blob/2.12.x/src/library/scala/collection/TraversableOnce.scala#L155

Practical difference between these methods in Spark is only related to their behavior on empty collections and ability to use mutable buffer (arguably it is related to performance). You'll find some discussion 

https://stackoverflow.com/questions/34529953/why-is-the-fold-action-necessary-in-spark


Moreover there is no difference in the overall processing model:

- Each partition is processed sequentially using a single thread.
- Partitions are processed in parallel using multiple executors / executor threads.
- Final merge is performed sequentially using a single thread on the driver.



https://issues.apache.org/jira/browse/SPARK-6416

https://issues.apache.org/jira/browse/SPARK-7683

---------------------------------------------

Aggregate the elements of each partition, and then the results for all the partitions, using a given associative and commutative function and a neutral "zero value". The function op(t1, t2) is allowed to modify t1 and return it as its result value to avoid object allocation; however, it should not modify t2.

This behaves somewhat differently from fold operations implemented for non-distributed collections in functional languages like Scala. This fold operation may be applied to partitions individually, and then fold those results into the final result, rather than apply the fold to each element sequentially in some defined ordering. For functions that are not commutative, the result may differ from that of a fold applied to a non-distributed collection.
To illustrate what is going on lets try to simulate what is going on step by step:

            val rdd = sc.parallelize(Array(2., 3.))

            val byPartition = rdd.mapPartitions(
            iter => Array(iter.fold(0.0)((p, v) => (p +  v * v))).toIterator).collect()

It gives us something similar to this Array[Double] = Array(0.0, 0.0, 0.0, 4.0, 0.0, 0.0, 0.0, 9.0) and

byPartition.reduce((p, v) => (p + v * v))
returns 97

Important thing to note is that results can differ from run to run depending on an order in which partitions are combined.

-----------------------------------------------

fold and reduce both aggregate over a collection by implementing an operation you specify, the major different is the starting point of the aggregation. For fold(), you have to specify the starting value, and for reduce() the starting value is the first (or possibly an arbitrary) element in the collection. 

Simple examples - we can sum the numbers in a collection using both functions: 

     (1 until 10).reduce( (a,b) => a+b ) 
     (1 until 10).fold(0)( (a,b) => a+b ) 

With fold, we want to start at 0 and cumulatively add each element. In this case, the operation passed to fold() and reduce() were very similar, but it is helpful to think about fold in the following way. For the operation we pass to fold(), imagine its two arguments are (i) the current accumulated value and (ii) the next value in the collection, 

     (1 until 10).fold(0)( (accumulated_so_far, next_value) => accumulated_so_far + next_value ). 

So the result of the operation, accumulated_so_far + next_value, will be passed to the operation again as the first argument, and so on. 

In this way, we could count the number of elements in a collection using fold, 

        (1 until 10).fold(0)( (accumulated_so_far, next_value) => accumulated_so_far + 1 ). 


When it comes to Spark, here’s another thing to keep in mind. For both reduce and fold, you need to make sure your operation is both commutative and associative. For RDDs, reduce and fold are implemented on each partition separately, and then the results are combined using the operation.  With fold, this could get you into trouble because an empty partition will emit fold’s starting value, so the number of partitions might erroneously affect the result of the calculation, if you’re not careful about the operation. This would occur with the ( (a,b) => a+1) operation from above (see http://stackoverflow.com/questions/29150202/pyspark-fold-method-output). 
