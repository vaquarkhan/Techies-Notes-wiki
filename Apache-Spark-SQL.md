in Spark 2.0, DataFrames are just Dataset of Rows in Scala and Java API. These operations are also referred as “untyped transformations” in contrast to “typed transformations” come with strongly typed Scala/Java Datasets.

**Starting Point: SparkSession**

    import org.apache.spark.sql.SparkSession

    val spark = SparkSession
      .builder()
      .appName("Spark SQL basic example")
      .config("spark.some.config.option", "some-value")
      .getOrCreate()

     // For implicit conversions like converting RDDs to DataFrames
     import spark.implicits._


**Note **

- val sqlContext = new org.apache.spark.sql.SQLContext(sc)
constructor SQLContext in class SQLContext is deprecated: Use SparkSession.builder instead.

- registerTempTable in class Dataset is deprecated: Use createOrReplaceTempView(viewName) instead.


--------------------------------------------------------------
Creating Dataframe 


      import org.apache.spark.sql.functions._


     val client = sc.parallelize(Seq(
     ("Abhishek", "C1"), 
     ("XUELAN", "C2"),
     ("Xahir", "C3")

    )).toDF("ClientName", "ClientCode")

    client.show()





    val amount = sc.parallelize(Seq(
     ("C1", "C11",3122), 
     ("C1", "C12",4312), 
     ("C2", "C21",21431), 
     ("C2", "C31",87588), 
     ("C3", "C32",98769), 
     ("C3", "C33",86567), 
     ("C3", "C34",23112)
 

    )).toDF("ClientCode", "OperationCode" ,"opAmount")

    amount.show()


      val dfAverage = amount.join(client,"clientCode") .groupBy(client("clientName"))
       .agg(avg(amount("opAmount")).as("average"))
        .select("clientName","average")

       dfAverage.show()


        import sqlContext.implicits._
        import org.apache.spark.sql._
        import org.apache.spark.sql.functions._
     
         client.createOrReplaceTempView("client")
         amount.createOrReplaceTempView("amount")

         val result = spark.sqlContext.sql("SELECT client.ClientName,avg(amount.opAmount)as average FROM amount JOIN client on 
       amount.ClientCode=client.ClientCode GROUP BY client.ClientName")

       result.show()


         +----------+----------+
         |ClientName|ClientCode|
         +----------+----------+
         |  Abhishek|        C1|
         |    XUELAN|        C2|
         |     Xahir|        C3|
         +----------+----------+

         +----------+-------------+--------+
         |ClientCode|OperationCode|opAmount|
         +----------+-------------+--------+
         |        C1|          C11|    3122|
         |        C1|          C12|    4312|
         |        C2|          C21|   21431|
         |        C2|          C31|   87588|
         |        C3|          C32|   98769|
         |        C3|          C33|   86567|
         |        C3|          C34|   23112|
         +----------+-------------+--------+

       +----------+-----------------+
       |clientName|          average|
       +----------+-----------------+
       |  Abhishek|           3717.0|
       |     Xahir|69482.66666666667|
       |    XUELAN|          54509.5|
       +----------+-----------------+

------------------------------------------




      val df = spark.read.json("/FileStore/tables/eqfgreea1507828581573/people.json")
      //
      df.show()
      //
      import spark.implicits._
      //
      df.printSchema()
      //
     //select
     df.select("name","age").show()
    //add +1
    df.select($"name", $"age" + 1).show()
    //filter
    df.filter($"age" > 21).show()
    //
    df.groupBy("age").count().show()



---------------------------------------

**Running SQL Queries Programmatically**

     // Register the DataFrame as a SQL temporary view
     df.createOrReplaceTempView("people")

    val sqlDF = spark.sql("SELECT * FROM people")
     sqlDF.show()
 

**Note**

Temporary views in Spark SQL are session-scoped and will disappear if the session that creates it terminates. If you want to have a temporary view that is shared among all sessions and keep alive until the Spark application terminates, you can create a global temporary view. Global temporary view is tied to a system preserved database global_temp, 

      // Register the DataFrame as a global temporary view
      df.createGlobalTempView("people")



       // Register the DataFrame as a global temporary view
        df.createGlobalTempView("people1")

        spark.sql("SELECT * FROM global_temp.people1").show()

       spark.newSession().sql("SELECT * FROM global_temp.people1").show()

-------------------------------------
**Datasets** 

Datasets are similar to RDDs, however, instead of using Java serialization or Kryo they use a specialized [Encoder](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Encoder)to serialize the objects for processing or transmitting over the network.

http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset

                   case class Person(name: String, age: Long)

                  // Encoders are created for case classes
                   val caseClassDS = Seq(Person("Andy", 32)).toDS()
                  caseClassDS.show()

                 //
                // Encoders for most common types are automatically provided by 
                importing spark.implicits._
                val primitiveDS = Seq(1, 2, 3).toDS()

               primitiveDS.map(_ + 1).collect() // Returns: Array(2, 3, 4)
               // DataFrames can be converted to a Dataset by providing a class. Mapping will be done by name
              val path = "/FileStore/tables/eqfgreea1507828581573/people.json"
              val peopleDS = spark.read.json(path).as[Person]
             peopleDS.show()


* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-SQL-programming-guide-notes-1
* https://github.com/vaquarkhan/vk-wiki-notes/wiki/How-do-I-flatten-JSON-blobs-into-a-Data-Frame-using-Spark-Spark-SQL
* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Problems-Spark-SQL-solves
* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Spark-SQL
* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Spark-SQL-and-dataset-type
* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Spark-SQL-links