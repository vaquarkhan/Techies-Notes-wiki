**Top Answers to Spark Interview Questions**

**1.Compare MapReduce and Spark**
Criteria	MapReduce	Spark
Processing Speeds	Good	Exceptional
Standalone mode	Needs Hadoop	Can work independently
Ease of use	APIs for Python, Java, & Scala	
Needs extensive Java program
Versatility	Real-time & machine learning applications	Not optimized for real-time & machine learning applications.


**2.What is Apache Spark?**
Spark is a fast, easy-to-use and flexible data processing framework. It has an advanced execution engine supporting cyclic data  flow and in-memory computing. Spark can run on Hadoop, standalone or in the cloud and is capable of accessing diverse data sources including HDFS, HBase, Cassandra and others. 


**3.Explain key features of Spark.**
• Allows Integration with Hadoop and files included in HDFS.
• Spark has an interactive language shell as it has an independent Scala (the language in which Spark is written) interpreter
• Spark consists of RDD’s (Resilient Distributed Datasets), which can be cached across computing nodes in a cluster.
• Spark supports multiple analytic tools that are used for interactive query analysis , real-time analysis and graph processing



**4.Define RDD.**
RDD is the acronym for Resilient Distribution Datasets – a fault-tolerant collection of operational elements that run parallel. The partitioned data in RDD is immutable and distributed. There are primarily two types of RDD:
• Parallelized Collections : The existing RDD’s running parallel with one another
• Hadoop datasets: perform function on each file record in HDFS or other storage system


**5.What does a Spark Engine do?**
Spark Engine is responsible for scheduling, distributing and monitoring the data application across the cluster.


**6.Define Partitions?**

**7.What operations RDD support?**
• Transformations
• Actions


**8.What do you understand by Transformations in Spark?**
Transformations are functions applied on RDD, resulting into another RDD. It does not execute until an action occurs. map() and filer() are examples of transformations, where the former applies the function passed to it on each element of RDD and results into another RDD. The filter() creates a new RDD by selecting elements form current RDD that pass function argument.


**9. Define Actions.**
An action helps in bringing back the data from RDD to the local machine. An action’s execution is the result of all previously created transformations. reduce() is an action that implements the function passed again and again until one value if left. take() action takes all the values from RDD to local node.


**10.Define functions of SparkCore.**
Serving as the base engine, SparkCore performs various important functions like memory management, monitoring jobs, fault-tolerance, job scheduling and interaction with storage systems.


**11.What is RDD Lineage?**
Spark does not support data replication in the memory and thus, if any data is lost, it is rebuild using RDD lineage. RDD lineage is a process that reconstructs lost data partitions. The best is that RDD always remembers how to build from other datasets.


**12.What is Spark Driver?**
Spark Driver is the program that runs on the master node of the machine and declares transformations and actions on data RDDs. In simple terms, driver in Spark creates SparkContext, connected to a given Spark Master.
The driver also delivers the RDD graphs to Master, where the standalone cluster manager runs.Are you interested in the comprehensive Apache Spark and Scala Videos to take your career to the next level?


**13.What is Hive on Spark?**
Hive contains significant support for Apache Spark, wherein Hive execution is configured to Spark:
           
           hive> set spark.home=/location/to/sparkHome;
           hive> set hive.execution.engine=spark;

Hive on Spark supports Spark on yarn mode by default.


**14.Name commonly-used Spark Ecosystems.**
• Spark SQL- for developers
• Spark Streaming for processing live data streams
• GraphX for generating and computing graphs
• MLlib (Machine Learning Algorithms)
• SparkR to promote R Programming in Spark engine.


**15.Define Spark Streaming.**
Spark supports stream processing – an extension to the Spark API , allowing stream processing of live data streams. The data from different sources like Flume, HDFS is streamed and finally processed to file systems, live dashboards and databases. It is similar to batch processing as the input data is divided into streams like batches.Learn about the Top Four Apache Spark use cases in this blog post.


**16.What is GraphX?**
Spark uses GraphX for graph processing to build and transform interactive graphs. The GraphX component enables programmers to reason about structured data at scale.


**17.What does MLlib do?**
MLlib is scalable machine learning library provided by Spark. It aims at making machine learning easy and scalable with common learning algorithms and use cases like clustering, regression filtering, dimensional reduction, and alike.Our in-depth Scala Certification Course can give your career a big boost!


**18.What is Spark SQL?**
SQL Spark is a novel module introduced in Spark to work with structured data and perform structured data processing. Through this module, Spark executes relational SQL queries on the data. The core of the component supports an altogether different RDD called SchemaRDD, composed of rows objects and schema objects defining data type of each column in the row. It is similar to a table in relational database.


**19.What is a Parquet file?**
Parquet is a columnar format file supported by many other data processing systems. Spark SQL performs both read and write operations with Parquet file and consider it be one of the best big data analytics format so far.


**20.What file systems Spark support?**
• Hadoop Distributed File System (HDFS). 
• Local File system
• S3


**21.What is Yarn?**
Similar to Hadoop, Yarn is one of the key features in Spark, providing a central and resource management platform to deliver scalable operations across the cluster . Running Spark on Yarn necessitates a binary distribution of Spar as built on Yarn support.


**22.List the functions of Spark SQL.**
Spark SQL is capable of:
• Loading data from a variety of structured sources
• Querying data using SQL statements, both inside a Spark program and from external tools that connect to Spark SQL through standard database connectors (JDBC/ODBC). For instance, using business intelligence tools like Tableau. Get to know more about Tableau in this Tableau Tutorial.
• Providing rich integration between SQL and regular Python/Java/Scala code, including the ability to join RDDs and SQL tables, expose custom functions in SQL, and more.


**23.What are benefits of Spark over MapReduce?**
• Due to the availability of in-memory processing, Spark implements the processing around 10-100x faster than Hadoop MapReduce. MapReduce makes use of persistence storage for any of the data processing tasks.
• Unlike Hadoop, Spark provides in-built libraries to perform multiple tasks form the same core like batch processing, Steaming, Machine learning, Interactive SQL queries. However, Hadoop only supports batch processing.
• Hadoop is highly disk-dependent whereas Spark promotes caching and in-memory data storage
• Spark is capable of performing computations multiple times on the same dataset. This is called iterative computation while there is no iterative computing implemented by Hadoop.Read more in this blog about the comparison of Spark and MapReduce.


**24.Is there any benefit of learning MapReduce, then?**
Yes, MapReduce is a paradigm used by many big data tools including Spark as well. It is extremely relevant to use MapReduce when the data grows bigger and bigger. Most tools like Pig and Hive convert their queries into MapReduce phases to optimize them better. Learn more in this MapReduce Tutorial.


**25.What is Spark Executor?**
When SparkContext connect to a cluster manager, it acquires an Executor on nodes in the cluster. Executors are Spark processes that run computations and store the data on the worker node. The final tasks by SparkContext are transferred to executors for their execution.


**26.Name types of Cluster Managers in Spark.**
The Spark framework supports three major types of Cluster Managers:
• Standalone: a basic manager to set up a cluster
• Apache Mesos: generalized/commonly-used cluster manager, also runs Hadoop MapReduce and other applications
• Yarn: responsible for resource management in Hadoop


**27.What do you understand by worker node?**
Worker node refers to any node that can run the application code in a cluster.


**28.What is PageRank?**
A unique feature and algorithm in graph, PageRank is the measure of each vertex in the graph. For instance, an edge from u to v represents endorsement of v’s importance by u. In simple terms, if a user at Instagram is followed massively, it will rank high on that platform.


**29.Do you need to install Spark on all nodes of Yarn cluster while running Spark on Yarn?**
No because Spark runs on top of Yarn.


**30.Illustrate some demerits of using Spark.**
Since Spark utilizes more storage space compared to Hadoop and MapReduce, there may arise certain problems. Developers need to be careful while running their applications in Spark. Instead of running everything on a single node, the work must be distributed over multiple clusters.


**31.How to create RDD?**
Spark provides two methods to create RDD:• By parallelizing a collection in your Driver program. This makes use of SparkContext’s ‘parallelize’ methodval IntellipaatData = Array(2,4,6,8,10)
val distIntellipaatData = sc.parallelize(IntellipaatData)• By loading an external dataset from external storage like HDFS, HBase, shared file system.

-----------------------------------------------------------------------------------------------------------------

What is Catalyst framework?

Answer: Catalyst framework is a new optimization framework present in Spark SQL. It allows Spark to automatically transform SQL queries by adding new optimizations to build a faster processing system.

Why is BlinkDB used?

Answer: BlinkDB is a query engine for executing interactive SQL queries on huge volumes of data and renders query results marked with meaningful error bars. BlinkDB helps users balance ‘query accuracy’ with response time.

What is a “Parquet” in Spark?

Answer: “Parquet” is a columnar format file supported by many data processing systems. Spark SQL performs both read and write operations with the “Parquet” file.