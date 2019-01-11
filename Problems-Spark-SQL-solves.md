Spark SQL Core

Execution of queries as RDDs
Reading data sets in multiple file formats Parquet, JSON, Avro etc.
Reading data sources both SQL and NOSQL Data sources
Hive Support

HQL, MetaStore, SerDes, UDFs
Catalyst Optimizer

it optimizes the Relational algebra + expressions
it does Query optimization
Problems Spark SQL solves

Spark SQL provides a unified framework with no movement of data outside the cluster. No extra modules need to be installed or integrated. It provides a unified load/save interface irrespective of the data source and the programming language.

The example below shows how easy it is to both load data from avro and convert it into parquet.

**val df = sqlContext.load("mydata.avro", "com.databricks.spark.avro")**
**df.save("mydata.parquet", "parquet")**

Spark SQL has a unified framework to solve the same analytics problem both for batch & streaming, which has been the Holy Grail in data processing. Frameworks have been built which do either one of them and do it well in terms of scalability, performance and feature set, but having a unified framework for doing both batch and streaming was never feasible, before Spark / Spark SQL led the way. With Spark framework the same code (logic) can work either with batch data – RDDs or with Streaming Data Sets (DStreams – Discretized Streams). DStream is just a series of RDDs. This representation allows batch and streaming workloads to work seamlessly. This vastly reduces code maintenance overheads and training developers with 2 different skill sets.

**Reading from JDBC Data Sources**

Data source for reading from JDBC has been added as built-in source for Spark SQL. Spark SQL can extract data from any existing relational databases that supports JDBC.  Examples include mysql, postgres, H2, and more.  Reading data from one of these systems is as simple as creating virtual table that points to the external table.  Data from this table can then be easily read in and joined with any of the other sources that Spark SQL supports.

**Spark SQL with Data Frames**

Data Frames are distributed collection of rows organized into named columns, an abstraction for selecting, filtering, aggregating and plotting, structured data – it was previously used to be called SchemaRDD.

DataFrame API can perform relational operations on both external data sources and Spark’s built-in distributed collections.

DataFrame offers rich relational/procedural integration within Spark programs. DataFrames are collections of structured records that can be manipulated using Spark’s procedural API, or using new relational APIs that allow richer optimizations. They can be created directly from Spark’s built-in distributed collections of objects, enabling relational processing in existing Spark.

DataFrames are more convenient and more efﬁcient than Spark’s procedural API. They make it easy to compute multiple aggregates in one pass using a SQL statement, something that is difﬁcult to express in traditional functional APIs.

Unlike RDDs, DataFrames keep track of their schema and support various relational operations that lead to more optimized execution. They infer the schema using reflection.

Spark DataFrames are lazy in that each DataFrame object represents a logical plan to compute a dataset, but no execution occurs until the user calls a special “output operation” such as save. This enables rich optimization across all operations.

DataFrames evolve Spark’s RDD model, making it faster and easier for Spark developers to work with structured data by providing simplified methods for filtering, aggregating, and projecting over large datasets. DataFrames are available in Spark’s Java, Scala, and Python API.

Spark SQL’s data source API can read and write DataFrames from a wide variety of data sources and data formats – Avro, parquet, ORC, JSON, H2.

Example of how writing less code– using plain RDDs and using DataFrame APIs for SQL

The scala example below shows equivalent code – one using Sparks RDD APIs and other one using Spark’s DataFrame API. Let us have an object – People – firstname, lastname, age being the attributes and the objective is to get the basic stats on age – People – grouped by firstname.

case class People(firstname: String, lastname: String, age: Intger)
val people = rdd.map(p => (people.firstname, people.age)).cache()

    // RDD Code
    val minAgeByFN = people.reduceByKey( scala.math.min(_, _) )
    val maxAgeByFN = people.reduceByKey( scala.math.max(_, _) )
    val avgAgeByFN = people.mapValues(x => (x, 1)).reduceByKey((x, y) => (x._1 + y._1, x._2 + y._2))
    val countByFN =  people.mapValues(x => 1).reduceByKey(_ + _)

    // Data Frame Code
    df = people.toDF
    people = df.groupBy("firstname").agg(
    min("age"),
    max("age"),
    avg("age"),
    count("*"))

Data Frames are faster than using the plain RDDs due to the catalyst optimizer. DataFrames provide the same operations as relational query languages like SQL and Pig.

Approach to solving Streaming Analytics with Spark SQL
**Data Frames are faster than using the plain RDDs due to the catalyst optimizer. DataFrames provide the same operations as relational query languages like SQL and Pig.**

