**What exactly is the problem**

When you want to make a dataset, Spark "requires an encoder (to convert a JVM object of type T to and from the internal Spark SQL representation) that is generally created automatically through implicits from a SparkSession, or can be created explicitly by calling static methods on Encoders" (taken from the docs on createDataset). An encoder will take the form Encoder[T] where T is the type you are encoding. The first suggestion is to add import spark.implicits._ (which gives you these implicit encoders) and the second suggestion is to explicitly pass in the implicit encoder using this set of encoder related functions.

There is no encoder available for regular classes, so

         import spark.implicits._
          class MyObj(val i: Int)
          // ...
            val d = spark.createDataset(Seq(new MyObj(1),new MyObj(2),new MyObj(3)))

will give you the following implicit related compile time error:

Unable to find encoder for type stored in a Dataset. Primitive types (Int, String, etc) and Product types (case classes) are supported by importing sqlContext.implicits._ Support for serializing other types will be added in future releases
However, if you wrap whatever type you just used to get the above error in some class that extends  Product, the error confusingly gets delayed to runtime, so

     import spark.implicits._
     case class Wrap[T](unwrap: T)
     class MyObj(val i: Int)
     // ...
     val d = spark.createDataset(Seq(Wrap(new MyObj(1)),Wrap(new MyObj(2)),Wrap(new MyObj(3))))

Compiles just fine, but fails at runtime with

**java.lang.UnsupportedOperationException: No Encoder found for MyObj**

The reason for this is that the encoders Spark creates with the implicits are actually only made at runtime (via scala relfection). In this case, all Spark checks at compile time is that the outermost class extends Product (which all case classes do), and only realizes at runtime that it still doesn't know what to do with MyObj (the same problem occurs if I tried to make a Dataset[(Int,MyObj)] - Spark waits until runtime to barf on MyObj). These are central problems that are in dire need of being fixed:

some classes that extend Product compile despite always crashing at runtime and
there is no way of passing in custom encoders for nested types (I have no way of feeding Spark an encoder for just MyObj such that it then knows how to encode Wrap[MyObj] or (Int,MyObj)).
Just use kryo

The solution everyone suggests is to use the kryo encoder.

    import spark.implicits._
    class MyObj(val i: Int)
    implicit val myObjEncoder = org.apache.spark.sql.Encoders.kryo[MyObj]
    // ...
    val d = spark.createDataset(Seq(new MyObj(1),new MyObj(2),new MyObj(3)))

This gets pretty tedious fast though. Especially if your code is manipulating all sorts of datasets, joining, grouping etc. You end up racking up a bunch of extra implicits. So, why not just make an implicit that does this all automatically?

      import scala.reflect.ClassTag
      implicit def kryoEncoder[A](implicit ct: ClassTag[A]) = 
      org.apache.spark.sql.Encoders.kryo[A](ct)

And now, it seems like I can do almost anything I want (the example below won't work in the spark-shell where spark.implicits._ is automatically imported)

     class MyObj(val i: Int)

      val d1 = spark.createDataset(Seq(new MyObj(1),new MyObj(2),new MyObj(3)))
      val d2 = d1.map(d => (d.i+1,d)).alias("d2") // mapping works fine and ..
      val d3 = d1.map(d => (d.i,  d)).alias("d3") // .. deals with the new type
      val d4 = d2.joinWith(d3, $"d2._1" === $"d3._1") // Boom!

Or almost. The problem is that using kryo leads to Spark just storing every row in the dataset as a flat binary object. For map, filter, foreach that is enough, but for operations like join, Spark really needs these to be separated into columns. Inspecting the schema for d2 or d3, you see there is just one binary column:

     d2.printSchema
     // root
     //  |-- value: binary (nullable = true)

Partial solution for tuples

So, using the magic of implicits in Scala (more in 6.26.3 Overloading Resolution), I can make myself a series of implicits that will do as good a job as possible, at least for tuples, and will work well with existing implicits:

      import org.apache.spark.sql.{Encoder,Encoders}
      import scala.reflect.ClassTag
      import spark.implicits._  // we can still take advantage of all the old implicits

      implicit def single[A](implicit c: ClassTag[A]): Encoder[A] = Encoders.kryo[A](c)

       implicit def tuple2[A1, A2](
       implicit e1: Encoder[A1],
           e2: Encoder[A2]
       ): Encoder[(A1,A2)] = Encoders.tuple[A1,A2](e1, e2)

       implicit def tuple3[A1, A2, A3](
       implicit e1: Encoder[A1],
           e2: Encoder[A2],
           e3: Encoder[A3]
        ): Encoder[(A1,A2,A3)] = Encoders.tuple[A1,A2,A3](e1, e2, e3)

// ... you can keep making these
Then, armed with these implicits, I can make my example above work, albeit with some column renaming

    class MyObj(val i: Int)

    val d1 = spark.createDataset(Seq(new MyObj(1),new MyObj(2),new MyObj(3)))
    val d2 = d1.map(d => (d.i+1,d)).toDF("_1","_2").as[(Int,MyObj)].alias("d2")
    val d3 = d1.map(d => (d.i  ,d)).toDF("_1","_2").as[(Int,MyObj)].alias("d3")
    val d4 = d2.joinWith(d3, $"d2._1" === $"d3._1")

I haven't yet figured out how to get the expected tuple names (_1, _2, ...) by default without renaming them - if someone else wants to play around with this, this is where the name "value" gets introduced and this is where the tuple names are usually added. However, the key point is that that I now have a nice structured schema:

    d4.printSchema
    // root
    //  |-- _1: struct (nullable = false)
    //  |    |-- _1: integer (nullable = true)
    //  |    |-- _2: binary (nullable = true)
    //  |-- _2: struct (nullable = false)
    //  |    |-- _1: integer (nullable = true)
    //  |    |-- _2: binary (nullable = true)

So, in summary, this workaround:

allows us to get separate columns for tuples (so we can join on tuples again, yay!)
we can again just rely on the implicits (so no need to be passing in kryo all over the place)
is almost entirely backwards compatible with import spark.implicits._ (with some renaming involved)
does not let us join on the kyro serialized binary columns, let alone on fields those may have
has the unpleasant side-effect of renaming some of the tuple columns to "value" (if necessary, this can be undone by converting .toDF, specifying new column names, and converting back to a dataset - and the schema names seem to be preserved through joins, where they are most needed).
Partial solution for classes in general

This one is less pleasant and has no good solution. However, now that we have the tuple solution above, I have a hunch the implicit conversion solution from another answer will be a bit less painful too since you can convert your more complex classes to tuples. Then, after creating the dataset, you'd probably rename the columns using the dataframe approach. If all goes well, this is really an improvement since I can now perform joins on the fields of my classes. If I had just used one flat binary kryo serializer that wouldn't have been possible.

Here is an example that does a bit of everything: I have a class MyObj which has fields of types Int, java.util.UUID, and Set[String]. The first takes care of itself. The second, although I could serialize using kryo would be more useful if stored as a String (since UUIDs are usually something I'll want to join against). The third really just belongs in a binary column.

     class MyObj(val i: Int, val u: java.util.UUID, val s: Set[String])

     // alias for the type to convert to and from
      type MyObjEncoded = (Int, String, Set[String])

     // implicit conversions
     implicit def toEncoded(o: MyObj): MyObjEncoded = (o.i, o.u.toString, o.s)
     implicit def fromEncoded(e: MyObjEncoded): MyObj =
     new MyObj(e._1, java.util.UUID.fromString(e._2), e._3)

Now, I can create a dataset with a nice schema using this machinery:

     val d = spark.createDataset(Seq[MyObjEncoded](
     new MyObj(1, java.util.UUID.randomUUID, Set("foo")),
     new MyObj(2, java.util.UUID.randomUUID, Set("bar"))
     )).toDF("i","u","s").as[MyObjEncoded]
   
And the schema shows me I columns with the right names and with the first two both things I can join against.

    d.printSchema
    // root
    //  |-- i: integer (nullable = false)
    //  |-- u: string (nullable = true)
    //  |-- s: binary (nullable = true)



---------------------------------------------------


Json 

       {"id" : "1201", "name" : "satish", "age" : "25"},
       {"id" : "1202", "name" : "krishna", "age" : "28"},
       {"id" : "1203", "name" : "amith", "age" : "28"},
       {"id" : "1204", "name" : "javed", "age" : "23"},
       {"id" : "1205", "name" : "mendy", "age" : "25"},
       {"id" : "1206", "name" : "rob", "age" : "24"},
       {"id" : "1207", "name" : "prudvi", "age" : "23"}



Example 



    package dataframe;
    import org.apache.spark.sql.SparkSession
    import org.apache.spark.{ SparkConf, SparkContext }
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.expressions._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.Row
    import org.apache.spark.sql.catalyst.expressions._

     object Test1 {
      def main(args: Array[String]) {
      val spark =
       SparkSession.builder()
        .appName("SQL-JSON")
        .master("local[4]")
        .getOrCreate()

    import spark.implicits._

    // easy enough to query test1 JSON
    val people = spark.read.json("src/main/resources/data/test1.json")
    //print schema
     people.printSchema()
     println("------------------------------------------------------")
     //create table 
     people.createOrReplaceTempView("people")
     //fire query 
     val peoplesql = spark.sql("SELECT * FROM people")
     //print Json
      peoplesql.foreach(r => println(r))
      //print dataframe
      println("------------------------------------------------------")
      peoplesql.show();
      println("------------------------------------------------------")

     /*
      * DataFrame is to group by age, order by id and filter all age group with more than 1 student. I use the following 
      script:
       */
        val df = spark.sqlContext.read.json("src/main/resources/data/test1.json")

        val arrLen = udf { a: Seq[Row] => a.length > 1 }

        val mergedDF = df.withColumn("newCol", collect_set(struct("age", "id", 
        "name")).over(Window.partitionBy("age").orderBy("id"))).select("newCol", "age")

        val filterd = mergedDF.filter(arrLen(col("newCol")))

        filterd.show();
        println("------------------------------------------------------")

       /**
         * [WrappedArray([28,1203,amith], [28,1202,krishna]),28]
         * [WrappedArray([25,1201,satish], [25,1205,mendy]),25]
         * [WrappedArray([23,1204,javed], [23,1207,prudvi]),23]
         */
       /**
        *  merge those two students rows inside the WrappedArray into one,
        * taking for example the id of the first student and the name of the second student.
        */
        filterd.foreach { x =>
          val student = PrintOne(x.getAs[Seq[Row]](0), x.getAs[String]("age"))
         println("merged student: " + student)
        }

       //same into map throwing error without encoder
        //TODO val merged = filterd.map { row => (row.getAs[String]("age"), PrintOne(row.getAs[Seq[Row]](0), 
      row.getAs[String]
       ("age"))) }
       println("------------------------------------------------------")

        import org.apache.spark.sql.types._
        import org.apache.spark.sql.{ Encoder, Encoders }
        import org.apache.spark.sql.catalyst.encoders.RowEncoder

      val encoder = Encoders.tuple(
         Encoders.STRING,
          RowEncoder(
           // The same as df.schema in your case
            StructType(Seq(
               StructField("age", StringType),
               StructField("id", StringType),
               StructField("name", StringType)))))

        filterd.map { row =>
           (
            row.getAs[String]("age"),
             PrintOne(row.getAs[Seq[Row]](0), row.getAs[String]("age")))
          }(encoder)
      }

       println("------------------------------------------------------")

       /*
       * Function 
       */
       def PrintOne(List: Seq[Row], age: String): Row = {
        val studentsDetails = Array(age, List(0).getAs[String]("id"), List(1).getAs[String]("name"))
        val mergedStudent = new GenericRowWithSchema(studentsDetails.toArray, List(0).schema)

           mergedStudent
        }

     }

-----------------------------------

Results 


------------------------------------------------------

   root
    |-- age: string (nullable = true)
    |-- id: string (nullable = true)
    |-- name: string (nullable = true)

------------------------------------------------------

    [25,1201,satish]
    [28,1202,krishna]
    [28,1203,amith]
    [23,1204,javed]
    [25,1205,mendy]
    [24,1206,rob]
    [23,1207,prudvi]

------------------------------------------------------

      +---+----+-------+
      |age|  id|   name|
      +---+----+-------+
      | 25|1201| satish|
      | 28|1202|krishna|
      | 28|1203|  amith|
      | 23|1204|  javed|
      | 25|1205|  mendy|
      | 24|1206|    rob|
      | 23|1207| prudvi|
      +---+----+-------+

------------------------------------------------------


     [Stage 11:=========================================>             (75 + 7) / 100]
                                                                               
																			   

     +--------------------+---+
     |              newCol|age|
     +--------------------+---+
     |[[28,1203,amith],...| 28|
     |[[23,1204,javed],...| 23|
     |[[25,1201,satish]...| 25|
     +--------------------+---+

------------------------------------------------------


    [Stage 15:=============================>                        (110 + 8) / 200]
    [Stage 15:=========================================>            (153 + 4) / 200]
    [Stage 15:===================================================>  (189 + 4) / 200]

    merged student: [28,1203,krishna]
    merged student: [23,1204,prudvi]
    merged student: [25,1201,mendy]

                                                                                
------------------------------------------------------

