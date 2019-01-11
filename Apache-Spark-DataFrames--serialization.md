When using RDD’s in your Java or Scala Spark code, Spark distributes the data to nodes within the cluster by using the default Java serialization. For Java and Scala objects, Spark has to send the data and structure between nodes. Java serialization doesn’t result in small byte-arrays, whereas Kyro serialization does produce smaller byte-arrays. Thus, you can store more using the same amount of memory when using Kyro. Furthermore, you can also add compression such as snappy.

WIth RDD's and Java serialization there is also an additional overhead of garbage collection.

If your working with RDD's, use Kyro serialization.

With DataFrames, a schema is used to describe the data and Spark only passes data between nodes, not the structure. Thus, for certain types of computation on specific file formats you can expect faster performance.

It's not 100% true that DataFrames always outperform RDD's. Please see my post here:

https://community.hortonworks.com/content/kbentry/42027/rdd-vs-dataframe-vs-sparksql.html