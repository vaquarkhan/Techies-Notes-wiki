**Spark SQL**

**Spark Sql Exposes 3 processing interfaces:**
 *  SQL
 *   HiveQL 
 *   Language integrated queries 

Can be used in 2 modes as a library data processing tasks are expressed as SQL, HiveQL or integrated queries in a Scala app

**3 key abstractions**
* SQLContext
* HiveContext
* DataFrame
as a distributed SQL execution engine allows multiple users to share a single spark cluster allows centralized caching across all jobs and across multiple data stores

DataFrame represents a distributed collection of rows organized into named columns Executing SQL queries provides us a DataFrame as the result is schema aware i.e knows names and types of columns provides methods for processing data
can be easily converted to regular RDD can be registered as a temporary table to run SQL/HiveQL 2 ways to create a DF
from existing RDD toDF if we can infer schema using a case class createDataFrame is you want to pass the Schema explicitly (StructType, StructField) from external DataSources single unified interace to create DF, either from data stores (MySQL, PostgresSQL, Oracle, Cassandra) or from files (JSON, Parquet, ORC, CSV, HDFS, local, S3) built-in support for JSON, Parquet, ORC, JDBC, Hive uses DataFrameReader to read data from external datasources can specify partiotioning, format and data source specific options SQL/HiveContext has a factory method called read() that returns an instance of dataFrameReader uses DataFrameWriter to write data to external datasources can specify partiotioning, format and data source specific options build-in functions optimized for faster execution through code generation

    import org.apache.spark.sql.functions._

* Optimization techniques used 
* Reduced Disk IO
* Skip rows

if a data store (for e.g. Parquet, ORC) maintains statistical info about data (min, max of a particular column in a group) then SparkSQL can take advantage of it to skip these groups
* Skip columns (Use support of Parquet)
* skip non-required partitions

Predicate Push down i.e. pushing filtering predicates to run natively on data stores like Cassandra, DB etc.
* In-memory columnar caching
* cache only required columns from any data store
* compress the cached columns (using snappy) to minimize memory usage and GC

SparkSQL can automatically select a compression codec for a column based on data type columnar format enables using of efficient compression techniques run length encoding
* delta encoding
* dictionary encoding
* Query optimization
uses both cost-based (in the physical planning phase) and rule-based (in the logical optimization phase) optimization
can even optimize across functions code generation


* [https://www.youtube.com/watch?v=h71MNWRv99M](https://www.youtube.com/watch?v=h71MNWRv99M)

* [https://github.com/parmarsachin/spark-dataframe-demo](https://github.com/parmarsachin/spark-dataframe-demo)

* [https://www.cloudera.com/documentation/enterprise/5-6-x/topics/spark_sparksql.html](https://www.cloudera.com/documentation/enterprise/5-6-x/topics/spark_sparksql.html)

* [https://www.cloudera.com/documentation/enterprise/5-6-x/topics/spark_sparksql.html](https://www.cloudera.com/documentation/enterprise/5-6-x/topics/spark_sparksql.html)

* [http://www.slideshare.net/jeykottalam/spark-sqlamp-camp2014](http://www.slideshare.net/jeykottalam/spark-sqlamp-camp2014)

* [http://www.meetup.com/Bangalore-Spark-Enthusiasts/events/227008581/](http://www.meetup.com/Bangalore-Spark-Enthusiasts/events/227008581/)

Spark SQL architecture contains three layers namely, Language API, Schema RDD, and Data Sources.

Language API − Spark is compatible with different languages and Spark SQL. It is also, supported by these languages- API (python, scala, java, HiveQL).

Schema RDD − Spark Core is designed with special data structure called RDD. Generally, Spark SQL works on schemas, tables, and records. Therefore, we can use the Schema RDD as temporary table. We can call this Schema RDD as Data Frame.

Data Sources − Usually the Data source for spark-core is a text file, Avro file, etc. However, the Data Sources for Spark SQL is different. Those are Parquet file, JSON document, HIVE tables, and Cassandra database.

* [https://rklicksolutions.wordpress.com/2016/03/03/tutorial-spark-1-6-sql-and-dataframe-operations/](https://rklicksolutions.wordpress.com/2016/03/03/tutorial-spark-1-6-sql-and-dataframe-operations/)
* Build Spark : [http://mbonaci.github.io/mbo-spark/](http://mbonaci.github.io/mbo-spark/)
* http://spark.apache.org/docs/latest/building-spark.html

Spark SQL example practice [https://www.infoq.com/articles/apache-spark-sql](https://www.infoq.com/articles/apache-spark-sql) Add a custom footer





**Alter Database**


    ALTER (DATABASE|SCHEMA) db_name SET DBPROPERTIES (key1=val1, ...)

Set one or more properties in the specified database. If a particular property is already set in the database, this will override the the old value with the new one.


**Alter Table or View**

    ALTER (TABLE|VIEW) [db_name.]table_name RENAME TO [db_name.]new_table_name

Rename an existing table or view. If the destination table name already exists, an exception will be thrown. This operation does not support moving tables across databases.


    ALTER (TABLE|VIEW) table_name SET TBLPROPERTIES (key1=val1, key2=val2, ...)

Set the properties of an existing table or view. If a particular property was already set, this will override the old value with the new one.


      ALTER (TABLE|VIEW) table_name UNSET TBLPROPERTIES
      [IF EXISTS] (key1, key2, ...)

Drop one or more properties of an existing table or view. If a specified property does not exist, an exception will be thrown.

       IF EXISTS
           If a specified property does not exist, nothing will happen.

      ALTER TABLE table_name [PARTITION part_spec] SET SERDE serde
         [WITH SERDEPROPERTIES (key1=val1, key2=val2, ...)]

      ALTER TABLE table_name [PARTITION part_spec]
         SET SERDEPROPERTIES (key1=val1, key2=val2, ...)

      part_spec:
          : (part_col_name1=val1, part_col_name2=val2, ...)

Set the SerDe and/or the SerDe properties of a table or partition. If a specified SerDe property was already set, this will override the old value with the new one. Setting the SerDe is only allowed for tables created using the Hive format.


**Alter Table Partitions**

      ALTER TABLE table_name ADD [IF NOT EXISTS]
          (PARTITION part_spec [LOCATION path], ...)

       part_spec:
         : (part_col_name1=val1, part_col_name2=val2, ...)

Add partitions to the table, optionally with a custom location for each partition added. This is only supported for tables created using the Hive format.

     IF NOT EXISTS
        If the specified partitions already exist, no action will be taken.

     ALTER TABLE table_name PARTITION part_spec RENAME TO PARTITION part_spec

       part_spec:
         : (part_col_name1=val1, part_col_name2=val2, ...)

Changes the partitioning field values of a partition. This operation is only allowed for tables created using the Hive format.


       ALTER TABLE table_name DROP [IF EXISTS] (PARTITION part_spec, ...)
       part_spec:
         : (part_col_name1=val1, part_col_name2=val2, ...)

Drops partitions from a table or view. This operation is only allowed for tables created using the Hive format.

      IF EXISTS
         If the specified partition does not exists, no action will be taken.



     ALTER TABLE table_name PARTITION part_spec SET LOCATION path

      part_spec:
        : (part_col_name1=val1, part_col_name2 =val2, ...)

Sets the location of the specified partition. Setting the location of individual partitions is only allowed for tables created using the Hive format.



**Analyze Table**


       ANALYZE TABLE [db_name.]table_name COMPUTE STATISTICS analyze_option

Write statistics about a table into the underlying metastore for future query optimizations. Currently the only analyze option supported is NOSCAN, which means the table won’t be scanned to generate the statistics. This command is only supported for tables created using the Hive format.


**Cache Table**


        CACHE [LAZY] TABLE [db_name.]table_name

Cache the contents of the table in memory. Subsequent queries on this table will bypass scanning the original files containing its contents as much as possible.


LAZY
    Cache the table lazily instead of eagerly scanning the entire table. 



**Clear Cache**

Clears the cache associated with a SQLContext.


     CLEAR CACHE



**Create Database**


        CREATE (DATABASE|SCHEMA) [IF NOT EXISTS] db_name
           [COMMENT comment_text]
           [LOCATION path]
           [WITH DBPROPERTIES (key1=val1, key2=val2, ...)]

Create a database. If a database with the same name already exists, an exception will be thrown.

      IF NOT EXISTS
          If a database with the same name already exists, nothing will happen.
      LOCATION
            If the specified path does not already exist in the underlying file system,
             this command will try to    create a directory with the path. When the database is dropped later, this directory will be deleted.




**Create Function**


      CREATE [TEMPORARY] FUNCTION [db_name.]function_name AS class_name
          [USING resource, ...]

      resource:
          : (JAR|FILE|ARCHIVE) file_uri

Create a function. The specified class for the function must extend either UDF or UDAF in org.apache.hadoop.hive.ql.exec, or one of AbstractGenericUDAFResolver, GenericUDF, or GenericUDTF in org.apache.hadoop.hive.ql.udf.generic. If a function with the same name already exists in the database, an exception will be thrown. Note: This command is supported only when Hive support is enabled.

TEMPORARY
    The created function will be available only in this session and will not be persisted to the underlying metastore, if any. No database name may be specified for temporary functions.
USING <resources>
    Specify the resources that must be loaded to support this function. A list of jar, file, or archive URIs may be specified. Known issue: adding jars does not work from the Spark shell (SPARK-8586).



**Create Table**


      CREATE [TEMPORARY] TABLE [IF NOT EXISTS] [db_name.]table_name
        [(col_name1[:] col_type1 [COMMENT col_comment1], ...)]
        USING datasource
        [OPTIONS (key1=val1, key2=val2, ...)]
        [PARTITIONED BY (col_name1, col_name2, ...)]
           [CLUSTERED BY (col_name3, col_name4, ...) INTO num_buckets BUCKETS]
           [AS select_statement]

Create a table using a data source. If a table with the same name already exists in the database, an exception will be thrown.

TEMPORARY
    The created table will be available only in this session and will not be persisted to the underlying metastore, if any. This may not be specified with IF NOT EXISTS or AS <select_statement>. To use AS <select_statement> with TEMPORARY, one option is to create a TEMPORARY VIEW instead.



     CREATE TEMPORARY VIEW table_name AS select_statement

There is also “CREATE OR REPLACE TEMPORARY VIEW” that may be handy if you don’t care whether the temporary view already exists or not. Note that for TEMPORARY VIEW you cannot specify datasource, partition or clustering options since a view is not materialized like tables.

      IF NOT EXISTS
           If a table with the same name already exists in the database, nothing will happen. This may not be    specified when creating a temporary table.

     USING <data source>
      Specify the file format to use for this table. The data source may be one of TEXT, CSV, JSON, JDBC, PARQUET, ORC, and LIBSVM, or a fully qualified class name of a custom implementation of org.apache.spark.sql.sources.DataSourceRegister.

PARTITIONED BY
    The created table will be partitioned by the specified columns. A directory will be created for each partition.

CLUSTERED BY
    Each partition in the created table will be split into a fixed number of buckets by the specified columns. This is typically used with partitioning to read and shuffle less data. Support for SORTED BY will be added in a future version.
AS <select_statement>
    Populate the table with input data from the select statement. This may not be specified with TEMPORARY TABLE or with a column list. To specify it with TEMPORARY, use CREATE TEMPORARY VIEW instead.

Examples:


          CREATE TABLE boxes (width INT, length INT, height INT) USING CSV

          CREATE TEMPORARY TABLE boxes
             (width INT, length INT, height INT)
              USING PARQUET
              OPTIONS ('compression'='snappy')

          CREATE TABLE rectangles
           USING PARQUET
           PARTITIONED BY (width)
           CLUSTERED BY (length) INTO 8 buckets
           AS SELECT * FROM boxes

          CREATE OR REPLACE TEMPORARY VIEW temp_rectangles
            AS SELECT * FROM boxes

         Create Table with Hive format


            CREATE [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
                [(col_name1[:] col_type1 [COMMENT col_comment1], ...)]
                [COMMENT table_comment]
                [PARTITIONED BY (col_name2[:] col_type2 [COMMENT col_comment2], ...)]
                [ROW FORMAT row_format]
                [STORED AS file_format]
                [LOCATION path]
                [TBLPROPERTIES (key1=val1, key2=val2, ...)]
                [AS select_statement]

      row_format:
         : SERDE serde_cls [WITH SERDEPROPERTIES (key1=val1, key2=val2, ...)]
         | DELIMITED [FIELDS TERMINATED BY char [ESCAPED BY char]]
          [COLLECTION ITEMS TERMINATED BY char]
          [MAP KEYS TERMINATED BY char]
          [LINES TERMINATED BY char]
          [NULL DEFINED AS char]

         file_format:
        : TEXTFILE | SEQUENCEFILE | RCFILE | ORC | PARQUET | AVRO
        | INPUTFORMAT input_fmt OUTPUTFORMAT output_fmt

Create a table using the Hive format. If a table with the same name already exists in the database, an exception will be thrown. When the table is dropped later, its data will be deleted from the file system. Note: This command is supported only when Hive support is enabled.


EXTERNAL
    The created table will use the custom directory specified with LOCATION. Queries on the table will be able to access any existing data previously stored in the directory. When an EXTERNAL table is dropped, its data is not deleted from the file system. This flag is implied if LOCATION is specified.
IF NOT EXISTS
    If a table with the same name already exists in the database, nothing will happen.
PARTITIONED BY
    The created table will be partitioned by the specified columns. This set of columns must be distinct from the set of non-partitioned columns. Partitioned columns may not be specified with AS <select_statement>.

ROW FORMAT
    Use the SERDE clause to specify a custom SerDe for this table. Otherwise, use the DELIMITED clause to use the native SerDe and specify the delimiter, escape character, null character etc.

STORED AS
    Specify the file format for this table. Available formats include TEXTFILE, SEQUENCEFILE, RCFILE, ORC, PARQUET and AVRO. Alternatively, the user may specify his own input and output formats through INPUTFORMAT and OUTPUTFORMAT. Note that only formats TEXTFILE, SEQUENCEFILE, and RCFILE may be used with ROW FORMAT SERDE, and only TEXTFILE may be used with ROW FORMAT DELIMITED.

LOCATION
    The created table will use the specified directory to store its data. This clause automatically implies EXTERNAL.
AS <select_statement>
    Populate the table with input data from the select statement. This may not be specified with PARTITIONED BY.

        Examples:


     CREATE TABLE my_table (name STRING, age INT)

      CREATE EXTERNAL TABLE IF NOT EXISTS my_table (name STRING, age INT)
        COMMENT 'This table is created with existing data'
       LOCATION 'spark-warehouse/tables/my_existing_table'

     CREATE TABLE my_table (name STRING, age INT)
      COMMENT 'This table is partitioned'
      PARTITIONED BY (hair_color STRING COMMENT 'This is a column comment')
      TBLPROPERTIES ('status'='staging', 'owner'='andrew')

CREATE TABLE my_table (name STRING, age INT)
    COMMENT 'This table specifies a custom SerDe'
        ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.orc.OrcSerde'
    STORED AS
        INPUTFORMAT 'org.apache.hadoop.hive.ql.io.orc.OrcInputFormat'
        OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.orc.OrcOutputFormat'

CREATE TABLE my_table (name STRING, age INT)
    COMMENT 'This table uses the CSV format'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE

      CREATE TABLE your_table
       COMMENT 'This table is created with existing data'
       AS SELECT * FROM my_table

    Create Table Like


    CREATE TABLE [IF NOT EXISTS] [db_name.]table_name1 LIKE [db_name.]table_name2

    Create a table using the metadata of an existing

    table. The created table always uses its own directory in the default warehouse location even if the existing table is EXTERNAL                                                                                                                                                                                                                                                                             . The existing table must not be a temporary table.





****Describe Database****

     DESCRIBE DATABASE [EXTENDED] db_name

Return the metadata of an existing database (name, comment and location). If the database does not exist, an exception will be thrown.

    EXTENDED
        Also display the database properties


****Describe Function****


          DESCRIBE FUNCTION [EXTENDED] [db_name.]function_name

Return the metadata of an existing function (implementing class and usage). If the function does not exist, an exception will be thrown.

EXTENDED
    Also show extended usage information. 





****Describe Table****


DESCRIBE [EXTENDED] [db_name.]table_name

Return the metadata of an existing table (column names, data types, and comments). If the table does not exist, an exception will be thrown.

EXTENDED
    Display detailed information about the table, including parent database, table type, storage information, and properties.



****Drop Database****


Drop Database


       DROP (DATABASE|SCHEMA) [IF EXISTS] db_name [(RESTRICT|CASCADE)]

       Drop a database. If the database to drop does not exist, an exception will be thrown. This also deletes the directory associated with the database from the file system.

IF EXISTS
    If the database to drop does not exist, nothing will happen.
RESTRICT
    Dropping a non-empty database will trigger an exception. Enabled by default
CASCADE
    Dropping a non-empty database will also drop all associated tables and functions.



****Drop Function****


Drop Function


      DROP [TEMPORARY] FUNCTION [IF EXISTS] [db_name.]function_name

Drop an existing function. If the function to drop does not exist, an exception will be thrown. Note: This command is supported only when Hive support is enabled.

TEMPORARY
    Whether to function to drop is a temporary function.
IF EXISTS
    If the function to drop does not exist, nothing will happen.





****Drop Table****


       DROP TABLE [IF EXISTS] [db_name.]table_name

Drop a table. If the table to drop does not exist, an exception will be thrown. This also deletes the directory associated with the table from the file system if this is not an EXTERNAL table.

IF EXISTS
    If the table to drop does not exist, nothing will happen.




****Explain****


       EXPLAIN [EXTENDED | CODEGEN] statement

Provide detailed plan information about the given statement without actually running it. By default this only outputs information about the physical plan. Explaining `DESCRIBE TABLE` is not currently supported.

EXTENDED
    Also output information about the logical plan before and after analysis and optimization.
CODEGEN
    Output the generated code for the statement, if any.




****Insert****


       INSERT INTO [TABLE] [db_name.]table_name [PARTITION part_spec] select_statement

       INSERT OVERWRITE TABLE [db_name.]table_name [PARTITION part_spec] select_statement

part_spec:
    : (part_col_name1=val1, part_col_name2=val2, ...)

Insert data into a table or a partition using a select statement.

OVERWRITE
    Whether to override existing data in the table or the partition. If this flag is not provided, the new data is appended.



****Load Data****


         LOAD DATA [LOCAL] INPATH path [OVERWRITE] INTO TABLE [db_name.]table_name [PARTITION part_spec]

part_spec:
    : (part_col_name1=val1, part_col_name2=val2, ...)

Load data from a file into a table or a partition in the table. The target table must not be temporary. A partition spec must be provided if and only if the target table is partitioned. Note: This is only supported for tables created using the Hive format.

LOCAL
    If this flag is provided, the local file system will be used load the path. Otherwise, the default file system will be used.
OVERWRITE
    If this flag is provided, the existing data in the table will be deleted. Otherwise, the new data will be appended to the table


**Refresh Table**


REFRESH TABLE [db_name.]table_name

Refresh all cached entries associated with the table. If the table was previously cached, then it would be cached lazily the next time it is scanned.





****Reset****

RESET

Reset all properties to their default values. The Set command output will be empty after this.




****Select****



      SELECT [ALL|DISTINCT] named_expression[, named_expression, ...]
         FROM relation[, relation, ...]
         [lateral_view[, lateral_view, ...]]
         [WHERE boolean_expression]
         [aggregation [HAVING boolean_expression]]
            [ORDER BY sort_expressions]
            [CLUSTER BY expressions]
            [DISTRIBUTE BY expressions]
            [SORT BY sort_expressions]
            [WINDOW named_window[, WINDOW named_window, ...]]
            [LIMIT num_rows]

named_expression:
    : expression [AS alias]

relation:
    | join_relation
    | (table_name|query|relation) [sample] [AS alias]
    : VALUES (expressions)[, (expressions), ...]
          [AS (column_name[, column_name, ...])]

expressions:
    : expression[, expression, ...]

sort_expressions:
    : expression [ASC|DESC][, expression [ASC|DESC], ...]

Output data from one or more relations.

A relation here refers to any source of input data. It could be the contents of an existing table (or view), the joined result of two existing tables, or a subquery (the result of another select statement).

ALL
    Select all matching rows from the relation. Enabled by default.
DISTINCT
    Select all matching rows from the relation then remove duplicate results.
WHERE
    Filter rows by predicate.
HAVING
    Filter grouped result by predicate.
ORDER BY
    Impose total ordering on a set of expressions. Default sort direction is ascending. This may not be used with SORT BY, CLUSTER BY, or DISTRIBUTE BY.
DISTRIBUTE BY
    Repartition rows in the relation based on a set of expressions. Rows with the same expression values will be hashed to the same worker. This may not be used with ORDER BY or CLUSTER BY.
SORT BY
    Impose ordering on a set of expressions within each partition. Default sort direction is ascending. This may not be used with ORDER BY or CLUSTER BY.
CLUSTER BY
    Repartition rows in the relation based on a set of expressions and sort the rows in ascending order based on the expressions. In other words, this is a shorthand for DISTRIBUTE BY and SORT BY where all expressions are sorted in ascending order. This may not be used with ORDER BY, DISTRIBUTE BY, or SORT BY.
WINDOW
    Assign an identifier to a window specification (more details below).
LIMIT
    Limit the number of rows returned.
VALUES
    Explicitly specify values instead of reading them from a relation.

Examples:


       SELECT * FROM boxes
       SELECT width, length FROM boxes WHERE height=3
       SELECT DISTINCT width, length FROM boxes WHERE height=3 LIMIT 2
       SELECT * FROM VALUES (1, 2, 3) AS (width, length, height)
       SELECT * FROM VALUES (1, 2, 3), (2, 3, 4) AS (width, length, height)
       SELECT * FROM boxes ORDER BY width
       SELECT * FROM boxes DISTRIBUTE BY width SORT BY width
SELECT * FROM boxes CLUSTER BY length

Sampling

sample:
    | TABLESAMPLE ((integer_expression | decimal_expression) PERCENT)
    : TABLESAMPLE (integer_expression ROWS)

Sample the input data. Currently, this can be expressed in terms of either a percentage (must be between 0 and 100) or a fixed number of input rows.

Examples:

      SELECT * FROM boxes TABLESAMPLE (3 ROWS)
      SELECT * FROM boxes TABLESAMPLE (25 PERCENT)

****Joins****

join_relation:
    | relation join_type JOIN relation (ON boolean_expression | USING (column_name[, column_name, ...]))
    : relation NATURAL join_type JOIN relation
join_type:
    | INNER
    | (LEFT|RIGHT) SEMI
    | (LEFT|RIGHT|FULL) [OUTER]
    : [LEFT] ANTI

INNER JOIN
    Select all rows from both relations where there is match.
OUTER JOIN
    Select all rows from both relations, filling with null values on the side that does not have a match.
SEMI JOIN
    Select only rows from the side of the SEMI JOIN where there is a match. If one row matches multiple rows, only the first match is returned.
LEFT ANTI JOIN
    Select only rows from the left side that match no rows on the right side.

Examples:


      SELECT * FROM boxes INNER JOIN rectangles ON boxes.width = rectangles.width
      SELECT * FROM boxes FULL OUTER JOIN rectangles USING (width, length)
      SELECT * FROM boxes NATURAL JOIN rectangles

Lateral View

lateral_view:
    : LATERAL VIEW [OUTER] function_name (expressions)
          table_name [AS (column_name[, column_name, ...])]

Generate zero or more output rows for each input row using a table-generating function. The most common built-in function used with LATERAL VIEW is explode.

LATERAL VIEW OUTER
    Generate a row with null values even when the function returned zero rows.

Examples:


      SELECT * FROM boxes LATERAL VIEW explode(Array(1, 2, 3)) my_view
      SELECT name, my_view.grade FROM students LATERAL VIEW OUTER explode(grades) my_view AS grade

Aggregation


aggregation:
    : GROUP BY expressions [(WITH ROLLUP | WITH CUBE | GROUPING SETS (expressions))]

Group by a set of expressions using one or more aggregate functions. Common built-in aggregate functions include count, avg, min, max, and sum.

ROLLUP
    Create a grouping set at each hierarchical level of the specified expressions. For instance, For instance, GROUP BY a, b, c WITH ROLLUP is equivalent to GROUP BY a, b, c GROUPING SETS ((a, b, c), (a, b), (a), ()). The total number of grouping sets will be N + 1, where N is the number of group expressions.
CUBE
    Create a grouping set for each possible combination of set of the specified expressions. For instance, GROUP BY a, b, c WITH CUBE is equivalent to GROUP BY a, b, c GROUPING SETS ((a, b, c), (a, b), (b, c), (a, c), (a), (b), (c), ()). The total number of grouping sets will be 2^N, where N is the number of group expressions.
GROUPING SETS
    Perform a group by for each subset of the group expressions specified in the grouping sets. For instance, GROUP BY x, y GROUPING SETS (x, y) is equivalent to the result of GROUP BY x unioned with that of GROUP BY y.

Examples:


SELECT height, COUNT(*) AS num_rows FROM boxes GROUP BY height
SELECT width, AVG(length) AS average_length FROM boxes GROUP BY width
SELECT width, length, height FROM boxes GROUP BY width, length, height WITH ROLLUP
SELECT width, length, avg(height) FROM boxes GROUP BY width, length GROUPING SETS (width, length)

Window Functions


window_expression:
    : expression OVER window_spec

named_window:
    : window_identifier AS window_spec

window_spec:
    | window_identifier
    : ((PARTITION|DISTRIBUTE) BY expressions
          [(ORDER|SORT) BY sort_expressions] [window_frame])

window_frame:
    | (RANGE|ROWS) frame_bound
    : (RANGE|ROWS) BETWEEN frame_bound AND frame_bound

frame_bound:
    | CURRENT ROW
    | UNBOUNDED (PRECEDING|FOLLOWING)
    : expression (PRECEDING|FOLLOWING)

Compute a result over a range of input rows. A windowed expression is specified using the OVER keyword, which is followed by either an identifier to the window (defined using the WINDOW keyword) or the specification of a window.

PARTITION BY
    Specify which rows will be in the same partition, aliased by DISTRIBUTE BY.
ORDER BY
    Specify how rows within a window partition are ordered, aliased by SORT BY.
RANGE bound
    Express the size of the window in terms of a value range for the expression.
ROWS bound
    Express the size of the window in terms of the number of rows before and/or after the current row.
CURRENT ROW
    Use the current row as a bound.
UNBOUNDED
    Use negative infinity as the lower bound or infinity as the upper bound.
PRECEDING
    If used with a RANGE bound, this defines the lower bound of the value range. If used with a ROWS bound, this determines the number of rows before the current row to keep in the window.
FOLLOWING
    If used with a RANGE bound, this defines the upper bound of the value range. If used with a ROWS bound, this determines the number of rows after the current row to keep in the window.









****Set****


SET [-v]
SET property_key[=property_value]

Set a property, return the value of an existing property, or list all existing properties. If a value is provided for an existing property key, the old value will be overridden.

-v
    Also output the meaning of the existing properties.
<property_key>
    Set or return the value of an individual property.





****Show Columns****


SHOW COLUMNS (FROM | IN) [db_name.]table_name

Return the list of columns in a table. If the table does not exist, an exception will be thrown.




****Show Create Table****


      SHOW CREATE TABLE [db_name.]table_name

Return the command used to create an existing table. If the table does not exist, an exception will be thrown.




****Show Functions****


SHOW [USER|SYSTEM|ALL] FUNCTIONS ([LIKE] regex | [db_name.]function_name)

Show functions matching the given regex or function name. If no regex or name is provided then all functions will be shown. IF USER or SYSTEM is declared then these will only show user-defined Spark SQL functions and system-defined Spark SQL functions respectively.

LIKE
    This qualifier is allowed only for compatibility and has no effect.






****Show Partitions****


        SHOW PARTITIONS [db_name.]table_name [PARTITION part_spec]

part_spec:
    : (part_col_name1=val1, part_col_name2=val2, ...)

List the partitions of a table, filtering by given partition values. Listing partitions is only supported for tables created using the Hive format and only when the Hive support is enabled.




       Show Table Properties


       SHOW TBLPROPERTIES [db_name.]table_name [(property_key)]

Return all properties or the value of a specific property set in a table. If the table does not exist, an exception will be thrown.





****Truncate Table****


    TRUNCATE TABLE table_name [PARTITION part_spec]

part_spec:
    : (part_col1=value1, part_col2=value2, ...)

Delete all rows from a table or matching partitions in the table. The table must not be a temporary table, an external table, or a view.

PARTITION
    Specify a partial partition spec to match partitions to be truncated. This is only supported for tables created using the Hive format.






**Uncache Table**


UNCACHE TABLE [db_name.]table_name

      Drop all cached entries associated with the table


**Use Database**


     USE db_name

Set the current database. All subsequent commands that do not explicitly specify a database will use this one. If the provided database does not exist, an exception will be thrown. The default current database is “default”.



****User Defined Aggregate Functions - Scala****

This notebook contains examples of a UDAF and how to register them for use in Spark SQL.
Implement the UserDefinedAggregateFunction
Copy to clipboardCopy

import org.apache.spark.sql.expressions.MutableAggregationBuffer
import org.apache.spark.sql.expressions.UserDefinedAggregateFunction
import org.apache.spark.sql.Row
import org.apache.spark.sql.types._

class GeometricMean extends UserDefinedAggregateFunction {
  // This is the input fields for your aggregate function.
  override def inputSchema: org.apache.spark.sql.types.StructType =
    StructType(StructField("value", DoubleType) :: Nil)

  // This is the internal fields you keep for computing your aggregate.
  override def bufferSchema: StructType = StructType(
    StructField("count", LongType) ::
    StructField("product", DoubleType) :: Nil
  )

  // This is the output type of your aggregatation function.
  override def dataType: DataType = DoubleType

  override def deterministic: Boolean = true

  // This is the initial value for your buffer schema.
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = 0L
    buffer(1) = 1.0
  }

  // This is how to update your buffer schema given an input.
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Long](0) + 1
    buffer(1) = buffer.getAs[Double](1) * input.getAs[Double](0)
  }

  // This is how to merge two objects with the bufferSchema type.
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Long](0) + buffer2.getAs[Long](0)
    buffer1(1) = buffer1.getAs[Double](1) * buffer2.getAs[Double](1)
  }

  // This is where you output the final value, given the final value of your bufferSchema.
  override def evaluate(buffer: Row): Any = {
    math.pow(buffer.getDouble(1), 1.toDouble / buffer.getLong(0))
  }
}

Register the UDAF with Spark SQL
Copy to clipboardCopy

sqlContext.udf.register("gm", new GeometricMean)

Use your UDAF
Copy to clipboardCopy

// Create a DataFrame and Spark SQL Table to query.
import org.apache.spark.sql.functions._

val ids = sqlContext.range(1, 20)
ids.registerTempTable("ids")
val df = sqlContext.sql("select id, id % 3 as group_id from ids")
df.registerTempTable("simple")

Copy to clipboardCopy

%sql
-- Use a group_by statement and call the UDAF.
select group_id, gm(id) from simple group by group_id

Copy to clipboardCopy

// Or use Dataframe syntax to call the aggregate function.

// Create an instance of UDAF GeometricMean.
val gm = new GeometricMean

// Show the geometric mean of values of column "id".
df.groupBy("group_id").agg(gm(col("id")).as("GeometricMean")).show()


// Invoke the UDAF by its assigned name.
df.groupBy("group_id").agg(expr("gm(id) as GeometricMean")).show()






****Register the function as a UDF****


val squared = (s: Int) => {
  s * s
}
sqlContext.udf.register("square", squared)

Call the UDF in Spark SQL
Copy to clipboardCopy

sqlContext.range(1, 20).registerTempTable("test")

Copy to clipboardCopy

%sql select id, square(id) as id_squared from test




