Reduce by key internally calls combineBykey. Hence the basic way of task execution is same for both.

The choice of CombineByKey over reduceBykey is when the input Type and output Type is not expected to be the same. So combineByKey will have a extra overhead of converting one type to another .

If the type conversion is omitted there is no difference at all .

Please follow the following links

* http://bytepadding.com/big-data/spark/reducebykey-vs-combinebykey

* http://bytepadding.com/big-data/spark/groupby-vs-reducebykey

* http://bytepadding.com/big-data/spark/combine-by-key-to-find-max


-----------------------


CombineByKey() is very similar to combiner of the mapreduce framework. In the MR framework, the combiner function is called in the map phase to do a local reduction and this value is then eventually sent over to reducer, this results in large savings if network bandwith.

In SPARK, groupByKey() doesnt do any local aggregation while computing on the partition’s data, this is where combineByKey() comes in handy.
In combineByKey values are merged into one value at each partition, finally each value from each partition is merged into a single value.

combineByKey takes 3 function arguments:


- createCombiner: This function is invoked only once for every key.It creates the initial value of the accumulator

- mergeValue: This function is called to add the new value to the existing accumulator of that key(which was created by createCombiner)

- mergeCombiners: This Function is called to combine values of a key across multiple partitions



      val rdd = sc.parallelize(List(
      ("A", 3), 
      ("A", 9), 
      ("A", 12), 
      ("A", 0), 
      ("A", 5),
      ("B", 4),
      ("B", 10), 
      ("B", 11),
      ("B", 20), 
      ("B", 25),
      ("C", 32), 
      ("C", 91),
      ("C", 122), 
      ("C", 3), 
      ("C", 55)
           ), 2)
       /*
       Logic:
       1.(x:Int) => (x, 1),(acc:(Int, Int), x) 
       //created key (A,B,C) withvalue 1 so every time will get in RDD plus one in results

       2.   => (acc._1 + x, acc._2 + 1),
             (acc1:(Int, Int), acc2:(Int, Int))
          => (acc1._1 + acc2._1, acc1._2 + acc2._2)
          
       //adding key with key and value with value
        */

       val workCountcombinebykey = rdd.combineByKey(
          (x:Int) => (x, 1),(acc:(Int, Int), x) 
          => (acc._1 + x, acc._2 + 1),
             (acc1:(Int, Int), acc2:(Int, Int))
          => (acc1._1 + acc2._1, acc1._2 + acc2._2)
             ).collect()

