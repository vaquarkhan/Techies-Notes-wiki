This works with Spark 2 with Oracle jar file added to

$SPARK_HOME/conf/ spark-defaults.conf 
 
 
spark.driver.extraClassPath      /home/hduser/jars/ojdbc6.jar
spark.executor.extraClassPath    /home/hduser/jars/ojdbc6.jar
 
and you get
 cala> val s = HiveContext.read.format("jdbc").options(
     | Map("url" -> _ORACLEserver,
     | "dbtable" -> "(SELECT to_char(ID) AS ID, to_char(CLUSTERED) AS CLUSTERED, to_char(SCATTERED) AS SCATTERED, to_char(RANDOMISED) AS RANDOMISED, RANDOM_STRING, SMALL_VC, PADDING FROM scratchpad.dummy)",
     | "partitionColumn" -> "ID",
     | "lowerBound" -> "1",
     | "upperBound" -> "100000000",
     | "numPartitions" -> "10",
     | "user" -> _username,
     | "password" -> _password)).load
s: org.apache.spark.sql.DataFrame = [ID: string, CLUSTERED: string ... 5 more fields]

that works.
However, with CDH 5.5.2 (Spark 1.5) it fails with error

java.sql.SQLException: No suitable driver
  at java.sql.DriverManager.getDriver(DriverManager.java:315)
  at org.apache.spark.sql.execution.datasources.jdbc.JdbcUtils$$anonfun$2.apply(JdbcUtils.scala:54)
  at org.apache.spark.sql.execution.datasources.jdbc.JdbcUtils$$anonfun$2.apply(JdbcUtils.scala:54)
  at scala.Option.getOrElse(Option.scala:121)
  at org.apache.spark.sql.execution.datasources.jdbc.JdbcUtils$.createConnectionFactory(JdbcUtils.scala:53)
  at org.apache.spark.sql.execution.datasources.jdbc.JDBCRDD$.resolveTable(JDBCRDD.scala:123)
  at org.apache.spark.sql.execution.datasources.jdbc.JDBCRelation.<init>(JDBCRelation.scala:117)
  at org.apache.spark.sql.execution.datasources.jdbc.JdbcRelationProvider.createRelation(JdbcRelationProvider.scala:53)
  at org.apache.spark.sql.execution.datasources.DataSource.resolveRelation(DataSource.scala:315)
  at org.apache.spark.sql.DataFrameReader.load(DataFrameReader.scala:149)
  at org.apache.spark.sql.DataFrameReader.load(DataFrameReader.scala:122)