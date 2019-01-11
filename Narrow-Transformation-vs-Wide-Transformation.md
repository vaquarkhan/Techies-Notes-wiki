Narrow transformation – In Narrow transformation, all the elements that are required to compute the records in single partition live in the single partition of parent RDD. A limited subset of partition is used to calculate the result. Narrow transformations are the result of map(), filter()

![](http://cdn.data-flair.training/blogs/wp-content/uploads/spark-narrow-transformation-2.jpg)


Wide transformation – In wide transformation, all the elements that are required to compute the records in the single partition may live in many partitions of parent RDD. The partition may live in many partitions of parent RDD. Wide transformations are the result of groupbyKey() and reducebyKey().

![](http://cdn.data-flair.training/blogs/wp-content/uploads/spark-wide-transformation-1.jpg)




http://data-flair.training/blogs/spark-rdd-operations-transformations-actions/