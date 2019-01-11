%md
## Creating DataFrames

With a [`SQLContext`](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.SQLContext), applications can create [`DataFrame`](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.DataFrame) 
* from an existing `RDD`, 
* from a Hive table, or 
* from various other data sources.

#### Just to recap: 
* A DataFrame is a distributed collection of data organized into named columns. 
* You can think of it as being organized into table RDD of case class `Row` (which is not exactly true). 
* DataFrames, in comparison to RDDs, are backed by rich optimizations, including:
  * tracking their own schema, 
  * adaptive query execution, 
  * code generation including whole stage codegen, 
  * extensible Catalyst optimizer, and 
  * project [Tungsten](https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html). 

> Note that performance for DataFrames is the same across languages Scala, Java, Python, and R. This is due to the fact that the only planning phase is language-specific (logical + physical SQL plan), not the actual execution of the SQL plan.

![DF speed across languages](https://databricks.com/wp-content/uploads/2015/02/Screen-Shot-2015-02-16-at-9.46.39-AM-1024x457.png)