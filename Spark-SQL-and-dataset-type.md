A Dataset is a strongly-typed, immutable collection of objects that are mapped to a relational schema.  At the core of the Dataset API is a new concept called an encoder, which is responsible for converting between JVM objects and tabular representation. The tabular representation is stored using Spark’s internal Tungsten binary format, allowing for operations on serialized data and improved memory utilization.  Spark 1.6 comes with support for automatically generating encoders for a wide variety of types, including primitive types (e.g. String, Integer, Long), Scala case classes, and Java Beans.

Spark has built-in support for automatically generating encoders for primitive types (e.g. String, Integer, Long), Scala case classes, and Java Beans. 

**Run SQL on files directly**

    val sqlDF = spark.sql("SELECT * FROM parquet.`examples/src/main/resources/users.parquet`")

* SaveMode.ErrorIfExists (default)	"error" (default)	When saving a DataFrame to a data source, if data already exists, an exception is expected to be thrown.

* SaveMode.Append	"append"	When saving a DataFrame to a data source, if data/table already exists, contents of the DataFrame are expected to be appended to existing data.

* SaveMode.Overwrite	"overwrite"	Overwrite mode means that when saving a DataFrame to a data source, if data/table already exists, existing data is expected to be overwritten by the contents of the DataFrame.

* SaveMode.Ignore	"ignore"	Ignore mode means that when saving a DataFrame to a data source, if data already exists, the save operation is expected to not save the contents of the DataFrame and to not change the existing data. This is similar to a CREATE TABLE IF NOT EXISTS in SQL.


**Parquet Files**

Hive metastore Parquet table conversion
When reading from and writing to Hive metastore Parquet tables, Spark SQL will try to use its own Parquet support instead of Hive SerDe for better performance. This behavior is controlled by the spark.sql.hive.convertMetastoreParquet configuration, and is turned on by default.

Hive/Parquet Schema Reconciliation

There are two key differences between Hive and Parquet from the perspective of table schema processing.

Hive is case insensitive, while Parquet is not
Hive considers all columns nullable, while nullability in Parquet is significant
Due to this reason, we must reconcile Hive metastore schema with Parquet schema when converting a Hive metastore Parquet table to a Spark SQL Parquet table. The reconciliation rules are:

Fields that have the same name in both schema must have the same data type regardless of nullability. The reconciled field should have the data type of the Parquet side, so that nullability is respected.

The reconciled schema contains exactly those fields defined in Hive metastore schema.

Any fields that only appear in the Parquet schema are dropped in the reconciled schema.
Any fields that only appear in the Hive metastore schema are added as nullable field in the reconciled schema.


**JSON Datasets**

    // A JSON dataset is pointed to by path.
    // The path can be either a single text file or a directory storing text files
    val path = "examples/src/main/resources/people.json"
   val peopleDF = spark.read.json(path)

   // The inferred schema can be visualized using the printSchema() method
    peopleDF.printSchema()
   // root
   //  |-- age: long (nullable = true)
   //  |-- name: string (nullable = true)

   // Creates a temporary view using the DataFrame
   peopleDF.createOrReplaceTempView("people")

   // SQL statements can be run by using the sql methods provided by spark
   val teenagerNamesDF = spark.sql("SELECT name FROM people WHERE age BETWEEN 13 AND 19")
   teenagerNamesDF.show()
   // +------+
   // |  name|
   // +------+
   // |Justin|
   // +------+

   // Alternatively, a DataFrame can be created for a JSON dataset represented by
   // an RDD[String] storing one JSON object per string
   val otherPeopleRDD = spark.sparkContext.makeRDD(
    """{"name":"Yin","address":{"city":"Columbus","state":"Ohio"}}""" :: Nil)
   val otherPeople = spark.read.json(otherPeopleRDD)
     otherPeople.show()
   // +---------------+----+
   // |        address|name|
   // +---------------+----+
   // |[Columbus,Ohio]| Yin|
   // +---------------+----+


**Data Types**
Spark SQL and DataFrames support the following data types:

* Numeric types
* ByteType: Represents 1-byte signed integer numbers. The range of numbers is from -128 to 127.
* ShortType: Represents 2-byte signed integer numbers. The range of numbers is from -32768 to 32767.
* IntegerType: Represents 4-byte signed integer numbers. The range of numbers is from -2147483648 to 2147483647.
* LongType: Represents 8-byte signed integer numbers. The range of numbers is from -9223372036854775808 to 9223372036854775807.
* FloatType: Represents 4-byte single-precision floating point numbers.
* DoubleType: Represents 8-byte double-precision floating point numbers.
* DecimalType: Represents arbitrary-precision signed decimal numbers. Backed internally by java.math.BigDecimal. A BigDecimal consists of an arbitrary precision integer unscaled value and a 32-bit integer scale.
* String type
* StringType: Represents character string values.
* Binary type
* BinaryType: Represents byte sequence values.
* Boolean type
* BooleanType: Represents boolean values.
* Datetime type
* TimestampType: Represents values comprising values of fields year, month, day, hour, minute, and second.
* DateType: Represents values comprising values of fields year, month, day.
* Complex types
* ArrayType(elementType, containsNull): Represents values comprising a sequence of elements with the type of elementType. containsNull is used to indicate if elements in a ArrayType value can have null values.
* MapType(keyType, valueType, valueContainsNull): Represents values comprising a set of key-value pairs. The data type of keys are described by keyType and the data type of values are described by valueType. For a MapType value, keys are not allowed to have null values. valueContainsNull is used to indicate if values of a MapType value can have null values.
* StructType(fields): Represents values with the structure described by a sequence of StructFields (fields).
* StructField(name, dataType, nullable): Represents a field in a StructType. The name of a field is indicated by name. The data type of a field is indicated by dataType. nullable is used to indicate if values of this fields can have null values.


