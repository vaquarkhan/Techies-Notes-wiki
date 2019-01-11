**Spark Architecture**


![](http://www.teckstory.com/wp-content/uploads/2015/08/Spark-Architecture.png)

**Spark Core:** This is the heart of Spark, and is responsible for management functions such as task scheduling. Spark Core implements and depends upon a programming abstraction known as Resilient Distributed Datasets (RDDs), which are discussed in more detail below.

**Spark SQL:** This is Spark's module for working with structured data, and it is designed to support workloads that combine familiar SQL database queries with more complicated, algorithm-based analytics. Spark SQL supports the open source Hive project, and its SQL-like HiveQL query syntax. Spark SQL also supports JDBC and ODBC connections, enabling a degree of integration with existing databases, data warehouses and business intelligence tools. JDBC connectors can also be used to integrate with Apache Drill, opening up access to an even broader range of data sources.

**Spark Streaming:** This module supports scalable and fault-tolerant processing of streaming data, and can integrate with established sources of data streams like Flume (optimized for data logs) and Kafka (optimized for distributed messaging). Spark Streaming's design, and its use of Spark's RDD abstraction, are meant to ensure that applications written for streaming data can be repurposed to analyze batches of historical data with little modification.

**MLlib:** This is Spark's scalable machine learning library, which implements a set of commonly used machine learning and statistical algorithms. These include correlations and hypothesis testing, classification and regression, clustering, and principal component analysis.

**GraphX:** This module began life as a separate UC Berkeley research project, which was eventually donated to the Apache Spark project. GraphX supports analysis of and computation over graphs of data, and supports a version of graph processing's Pregel API. GraphX includes a number of widely understood graph algorithms, including PageRank.
Spark R: This module was added to the 1.4.x release of Apache Spark, providing data scientists and statisticians using R with a lightweight mechanism for calling upon Spark's capabilities.




![](https://spark.apache.org/docs/1.1.1/img/cluster-overview.png)

**Spark Context:** It holds a connection with Spark cluster manager. All Spark applications run as independent set of processes, coordinated by a SparkContext in a program.

**Driver and Worker: **A driver is incharge of the process of running the main() function of an application and creating the SparkContext. A worker, on the other hand, is any node that can run program in the cluster. If a process is launched for an application, then this application acquires executors at worker node.

**Cluster Manager:**   Cluster manager allocates resources to each application in driver program. There are three types of cluster managers supported by Apache Spark – Standalone, Mesos and YARN. Apache Spark is agnostic to the underlying cluster manager, so we can install any cluster manager, each has its own unique advantages depending upon the goal. They all are different in terms of scheduling, security and monitoring. Once SparkContext connects to the cluster manager, it acquires executors on a cluster node, these executors are worker nodes on cluster which work independently on each tasks and interact with each other.

**Hadoop Mapreduce vs Spark**

In Hadoop, tasks are distributed among the nodes of a cluster, which in turn save data on disk. When that data is required for processing, each node has to load the data from the disk and save the data into disk after performing operation. This process ends up adding cost in terms of speed and time, because disk operations are far slower than RAM operations. It also requires time to convert the data in a particular format when writing the data from RAM to disk. This conversion is known as Serialization and reverse is Deserialization.

Let’s look at the MapReduce process to understand the advantage of in-memory computation better. Suppose, there are several map-reduce tasks happening one after another. At the start of the computations, both technologies (Hadoop and Spark), read the data from disk for mapping. Hadoop performs the map operation and saves the results back to hard drive. However, in case of Apache Spark, the results are stored in RAM.

In the next step (Reduce operation), Hadoop reads the saved data from the hard drive, where as Apache Spark reads it from RAM. This creates a difference in a single MapReduce operation. Now imagine, if there were multiple map-reduce operations, how much time difference would you see at the end of task completion.


![](https://www.analyticsvidhya.com/wp-content/uploads/2016/09/big1.png)




**1. Apache Spark**
Apache Spark is a powerful open-source processing engine built around speed, ease of use, and sophisticated analytics, with APIs in Java, Scala, Python, R, and SQL. Spark runs programs up to 100x faster than Hadoop MapReduce in memory, or 10x faster on disk. It can be used to build data applications as a library, or to perform ad-hoc data analysis interactively. Spark powers a stack of libraries including SQL, DataFrames, and Datasets, MLlib for machine learning, GraphX for graph processing, and Spark Streaming. You can combine these libraries seamlessly in the same application. As well, Spark runs on a laptop, Hadoop, Apache Mesos, standalone, or in the cloud. It can access diverse data sources including HDFS, Apache Cassandra, Apache HBase, and S3.

It was originally developed at UC Berkeley in 2009. (Note that Spark’s creator Matei Zaharia has since become CTO at Databricks and faculty member at MIT.) Since its release, Spark has seen rapid adoption by enterprises across a wide range of industries. Internet powerhouses such as Netflix, Yahoo, and Tencent have eagerly deployed Spark at massive scale, collectively processing multiple petabytes of data on clusters of over 8,000 nodes. It has quickly become the largest open source community in big data, with over 1000 code contributors and with over 187,000 members in 420 Apache Spark Meetups groups.

**2. RDD**
At the core of Apache Spark is the notion of data abstraction as distributed collection of objects. This data abstraction, called Resilient Distributed Dataset (RDD), allows you to write programs that transform these distributed datasets.

RDDs are immutable distributed collection of elements of your data that can be stored in memory or disk across a cluster of machines. The data is partitioned across machines in your cluster that can be operated in parallel with a low-level API that offers transformations and actions. RDDs are fault tolerant as they track data lineage information to rebuild lost data automatically on failure.

Below is an Apache Spark code snippet using Python and RDDs to perform a word count.

    # Open textFile for Spark Context RDD
    text_file = spark.textFile("hdfs://...")
    # Execute word count
    text_file.flatMap(lambda line: line.split())
    .map(lambda word: (word, 1))
    .reduceByKey(lambda a, b: a+b)

**3. DataFrame**
Like an RDD, a DataFrame is an immutable distributed collection of data. Unlike an RDD, data is organized into named columns, like a table in a relational database. Designed to make large data sets processing even easier, DataFrame allows developers to impose a structure onto a distributed collection of data, allowing higher-level abstraction; it provides a domain specific language API to manipulate your distributed data; and makes Spark accessible to a wider audience, beyond specialized data engineers.

Below is an Apache Spark code snippet using SQL and DataFrames to query and join different data sources.

    # Read JSON file and register temp view
    context.jsonFile("s3n://...").createOrReplaceTempView("json")
    # Execute SQL query
    results = context.sql("""SELECT * FROM people JOIN json ...""")

**4. Dataset**
Introduced in Spark 1.6, the goal of Spark Datasets is to provide an API that allows users to easily express transformations on domain objects, while also providing the performance and benefits of the robust Spark SQL execution engine.

Note, starting in Spark 2.0, the DataFrame APIs will merge with Datasets APIs, unifying data processing capabilities across all libraries. Because of unification, developers now have fewer concepts to learn or remember, and work with a single high-level and type-safe API called Dataset. Conceptually, the Spark DataFrame is an alias for a collection of generic objects Dataset[Row], where a Row is a generic untyped JVM object. Dataset, by contrast, is a collection of strongly-typed JVM objects, dictated by a case class you define, in Scala or Java.

Screen Shot 2016-06-21 at 18.24.16

    // Define a case class that represents our type-specific Scala JVM Object
    case class Person (email: String, iq: Long, name: String)
    // Read JSON file and convert to Dataset using the case class
    val ds = spark.read.json("...").as[Person]


**5. MLlib**
Apache Spark provides a general machine learning library — MLlib — that is designed for simplicity, scalability, and easy integration with other tools. With the scalability, language compatibility, and speed of Spark, data scientists can solve and iterate through their data problems faster.

From the inception of the Apache Spark project, MLlib was considered foundational for Spark’s success. The key benefit of MLlib is that it allows data scientists to focus on their data problems and models instead of solving the complexities surrounding distributed data (such as infrastructure, configurations, and so on). The data engineers can focus on distributed systems engineering using Spark’s easy-to-use APIs, while the data scientists can leverage the scale and speed of Spark core. Just as important, Spark MLlib is a general-purpose library, providing algorithms for most use cases while at the same time allowing the community to build upon and extend it for specialized use cases. To review the key terms of machine learning, please refer to Matthew Mayo’s Machine Learning Key Terms, Explained.

**6. ML Pipelines**
Typically when running machine learning algorithms, it involves a sequence of tasks including pre-processing, feature extraction, model fitting, and validation stages. For example, when classifying text documents might involve text segmentation and cleaning, extracting features, and training a classification model with cross-validation. Though there are many libraries we can use for each stage, connecting the dots is not as easy as it may look, especially with large-scale datasets. Most ML libraries are not designed for distributed computation or they do not provide native support for pipeline creation and tuning.

Diagram of ML pipelines

The ML Pipelines is a High-Level API for MLlib that lives under the “spark.ml” package. A pipeline consists of a sequence of stages. There are two basic types of pipeline stages: Transformer and Estimator. A Transformer takes a dataset as input and produces an augmented dataset as output. E.g., a tokenizer is a Transformer that transforms a dataset with text into an dataset with tokenized words. An Estimator must be first fit on the input dataset to produce a model, which is a Transformer that transforms the input dataset. E.g., logistic regression is an Estimator that trains on a dataset with labels and features and produces a logistic regression model.

**7. GraphX**
GraphX is the component in Apache Spark for graphs and graph-parallel computation. At a high level, GraphX extends the Spark RDD via a Graph abstraction: a directed multigraph with properties attached to each vertex and edge. To support graph computation, GraphX exposes a set of fundamental operators (e.g., subgraph, joinVertices, and aggregateMessages) as well as an optimized variant of the Pregel API. In addition, GraphX includes a growing collection of graph algorithms and builders to simplify graph analytics tasks.

**8. Spark Streaming**
Spark Streaming is an extension of the core Spark API that allows data engineers and data scientists to process real-time data from various sources including (but not limited to) Kafka, Flume, and Amazon Kinesis. This processed data can be pushed out to filesystems, databases, and live dashboards. Its key abstraction is a Discretized Stream or, in short, a DStream, which represents a stream of data divided into small batches. DStreams are built on RDDs, Spark’s core data abstraction. This allows Spark Streaming to seamlessly integrate with any other Spark components like MLlib and Spark SQL.

Spark Streaming example diagram

This unification of disparate data processing capabilities is the key reason behind Spark Streaming’s rapid adoption. It makes it very easy for developers to use a single framework to satisfy all their processing needs.

**9. Structured Streaming**
Introduced as part of Apache Spark 2.0, structured streaming is a high-level streaming built on top of the Spark SQL engine. It is a declarative API that extends DataFrames and Datasets to support batch, interactive, and streaming queries. The advantage of this approach is that it allows programmers to apply their experience working with static data sets (i.e. batch) and easily apply this to infinite data sets (i.e. streaming).

**10. spark-packages.org**
spark-packages.org is a community package index to track the growing number of open source packages and libraries that work with Apache Spark. Spark Packages makes it easy for users to find, discuss, rate, and install packages for any version of Spark and makes it easy for developers to contribute packages.

Spark Packages features integrations with various data sources, management tools, higher level domain-specific libraries, machine learning algorithms, code samples, and other Spark content. Examples packages include Spark-CSV (which is now included in Spark 2.0) and Spark ML integration packages including GraphFrames and TensorFrames.

**11. Catalyst Optimizer**
Spark SQL is one of the most technically involved components of Apache Spark. It powers both SQL queries and the DataFrame API. At the core of Spark SQL is the Catalyst optimizer, which leverages advanced programming language features (e.g. Scala’s pattern matching and quasiquotes) in a novel way to build an extensible query optimizer.

Catalyst is based on functional programming constructs in Scala and designed with these key two purposes:

Easily add new optimization techniques and features to Spark SQL
Enable external developers to extend the optimizer (e.g. adding data source specific rules, support for new data types, etc.)
As well, Catalyst supports both rule-based and cost-based optimization.

Diagram of the Catalyst Optimizer

For more information, please refer to Deep Dive into Spark SQL’s Catalyst Optimizer and the webinar Apache Spark DataFrames: Simple and Fast Analysis of Structured Data.

**12. Tungsten**
Tungsten is the codename for the umbrella project to make changes to Apache Spark’s execution engine that focuses on substantially improving the efficiency of memory and CPU for Spark applications, to push performance closer to the limits of modern hardware. This effort includes the following initiatives:

Memory Management and Binary Processing: leveraging application semantics to manage memory explicitly and eliminate the overhead of JVM object model and garbage collection
Cache-aware computation: algorithms and data structures to exploit memory hierarchy
Code generation: using code generation to exploit modern compilers and CPUs
No virtual function dispatches: this reduces multiple CPU calls which can have a profound impact on performance when dispatching billions of times.
Intermediate data in memory vs CPU registers: Tungsten Phase 2 places intermediate data into CPU registers. This is an order of magnitudes reduction in the number of cycles to obtain data from the CPU registers instead of from memory
Loop unrolling and SIMD: Optimize Apache Spark’s execution engine to take advantage of modern compilers and CPUs’ ability to efficiently compile and execute simple for loops (as opposed to complex function call graphs).
For more information, please reference Project Tungsten: Bringing Apache Spark Closer to Bare Metal, Deep Dive into Spark SQL’s Catalyst Optimizer, and Apache Spark as a Compiler: Joining a Billion Rows per Second on a Laptop.

**13. Continuous Applications**
In Apache Spark 2.0, adding structure to Spark, through use of high-level DataFrames and Datasets APIs, accommodates a novel approach to look at real-time streaming. That is, look at streaming not as streaming but as either a static table of data (where you know all the data) or a continuous table of data (where new data is continuously arriving).

As such you can build end-to-end continuous applications, in which you can issue the same queries to batch as to real-time data, perform ETL, generate reports, update or track specific data in the stream. This combined batch & real-time query-capabilities to a structured stream is a unique offering—not many streaming engines offer it yet.

* [https://databricks.com/blog/2016/06/22/apache-spark-key-terms-explained.html](https://databricks.com/blog/2016/06/22/apache-spark-key-terms-explained.html)

**Lazy operation:** Lazy operations are used to optimize solutions in Apache Spark. I will discuss about lazy evaluation in later part of this article. For now, we can think that there are some operations which do not execute until we require results.


**Transformations**, which create a new RDD by changing the original through processes such as mapping, filtering, and more;
**Actions**, such as counts, which measure but do not change the original data.
The original RDD remains unchanged throughout. The chain of transformations from RDD1 to RDDn are logged, and can be repeated in the event of data loss or the failure of a cluster node.

Transformations are said to be lazily evaluated, meaning that they are not executed until a subsequent action has a need for the result. This will normally improve performance, as it can avoid the need to process data unnecessarily. It can also, in certain circumstances, introduce processing bottlenecks that cause applications to stall while waiting for a processing action to conclude.



