* Dataset is a distributed collection of data. Dataset is a new interface added in Spark 1.6 that provides the benefits of RDDs (strong typing, ability to use powerful lambda functions) with the benefits of Spark SQL’s optimized execution engine.

* DataFrame is a Dataset organized into named columns. It is conceptually equivalent to a table in a relational database.

With a SparkSession, applications can create DataFrames from an existing RDD, from a Hive table, or from Spark data sources.

As an example, the following creates a DataFrame based on the content of a JSON file:

      val df = spark.read.json("/home/osboxes/Sparkdatafile/employee.json")
      df.show()


**DataFrame Operations**

        // This import is needed to use the $-notation
        import spark.implicits._
        // Print the schema in a tree format
        df.printSchema()
       // root
       // |-- age: long (nullable = true)
       // |-- name: string (nullable = true)

        // Select only the "name" column
        df.select("name").show()
       // +-------+
       // |   name|
       // +-------+
      // |Michael|
      // |   Andy|
      // | Justin|
      // +-------+

      // Select everybody, but increment the age by 1
       df.select($"name", $"age" + 1).show()

      // Select people older than 21
       df.filter($"age" > 21).show()
       // Count people by age
       df.groupBy("age").count().show()



* Datasets are similar to RDDs, however, instead of using Java serialization or Kryo they use a specialized Encoder to serialize the objects for processing or transmitting over the network.


         case class Person(name: String, age: Long)

         // Encoders are created for case classes
         val caseClassDS = Seq(Person("Andy", 32)).toDS()
         caseClassDS.show()

        // Encoders for most common types are automatically provided by importing spark.implicits._
        val primitiveDS = Seq(1, 2, 3).toDS()
        primitiveDS.map(_ + 1).collect() // Returns: Array(2, 3, 4)

            // DataFrames can be converted to a Dataset by providing a class. Mapping will be done by name
            val path = "examples/src/main/resources/people.json"
            val peopleDS = spark.read.json(path).as[Person]
            peopleDS.show()


* Spark SQL introduces a tabular data abstraction called Dataset (that was previously DataFrame). 



**Spark SQL defines three types of functions:**

* Built-in functions or User-Defined Functions (UDFs) that take values from a single row as input to generate a single return value for every input row.

* Aggregate functions that operate on a group of rows and calculate a single return value per group.

* Windowed Aggregates (Windows) that operate on a group of rows and calculate a single return value for each row in a group.