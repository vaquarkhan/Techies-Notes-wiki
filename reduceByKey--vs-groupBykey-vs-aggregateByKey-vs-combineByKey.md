There is two different ways to compute counts:

    val words = Array("one", "two", "two", "three", "three", "three")
    val wordPairsRDD = sc.parallelize(words).map(word => (word, 1))

    val wordCountsWithReduce = wordPairsRDD .reduceByKey(_ + _) .collect()
​
    val wordCountsWithGroup = wordPairsRDD .groupByKey() .map(t => (t._1, t._2.sum)) .collect()

reduceByKey will aggregate y key before shuffling, and groupByKey will shuffle all the value key pairs as the diagrams show.  On large size data the difference is obvious.

**reduceByKey **
![](http://www.ruxizhang.com/uploads/4/4/0/2/44023465/1866882_orig.png)


**groupBykey**
![](http://www.ruxizhang.com/uploads/4/4/0/2/44023465/3009151_orig.png)


Here are more functions to prefer over groupByKey:
* combineByKey can be used when you are combining elements but your return type differs from your input value type.
* foldByKey merges the values for each key using an associative function and a neutral "zero value".

* http://www.slideshare.net/databricks/strata-sj-everyday-im-shuffling-tips-for-writing-better-spark-programs
* http://apache-spark-user-list.1001560.n3.nabble.com/aggregateByKey-vs-combineByKey-td15321.html
* http://stackoverflow.com/questions/24804619/how-does-spark-aggregate-function-aggregatebykey-work