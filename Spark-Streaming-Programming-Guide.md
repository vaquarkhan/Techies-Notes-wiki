**Creating a SparkSession      **
      

           import org.apache.spark._
           import org.apache.spark.streaming._
           import org.apache.spark.streaming.StreamingContext._


        //set up the spark configuration and create contexts
        val sparkConf = new SparkConf().setAppName("SparkSessionZipsExample").setMaster("local")
        // your handle to SparkContext to access other context like SQLContext
        val sc = new SparkContext(sparkConf).set("spark.some.config.option", "some-value")
        val sqlContext = new org.apache.spark.sql.SQLContext(sc)

Example :
* https://github.com/dmatrix/examples/blob/master/spark/databricks/apps/scala/2.0/src/main/scala/zips/SparkSessionZipsExample.scala

* https://databricks.com/blog/2016/08/15/how-to-use-sparksession-in-apache-spark-2-0.html




https://www.infoq.com/articles/apache-spark-streaming

**DStream**

DStream (short for Discretized Stream) is the basic abstraction in Spark Streaming and represents a continuous stream of data. DStreams can be created either from input data streams from sources such as Kafka, Flume, and Kinesis, or by applying operations on other DStreams. Internally, a DStream is represented as a sequence of RDD objects.

Similar to the transformation and action operations on RDDs, DStreams support the following operations:

*  map
*     flatMap
*     filter
*     count
*     reduce
*     countByValue
*     reduceByKey
*     join
*     updateStateByKey



**Streaming Context**

Similar to SparkContext in Spark, StreamingContext is the main entry point for all streaming functionality.

StreamingContext has built-in methods for receiving streaming data into Spark Streaming program.

Using this context, we can create a DStream that represents streaming data from a TCP source, specified as hostname and port number. For example, if we are using a tool like netcat to test the Spark Streaming program, we would receive data stream from the machine where netcat is running (e.g. localhost) and port number of 9999.

When the code is executed, Spark Streaming only sets up the computation it will perform when it is started, and no real processing is done yet. To start the processing after all the transformations have been setup, we finally call start() method to start the computation and awaitTermination() method to wait for the computation to terminate.



**Spark Streaming API**

Spark Streaming comes with several API methods that are useful for processing data streams. There are RDD-like operations like map, flatMap, filter, count, reduce, groupByKey, reduceByKey, sortByKey, and join. It also provides additional API to process the streaming data based on window and stateful operations. These include window, countByWindow, reduceByWindow, countByValueAndWindow, reduceByKeyAndWindow and updateStateByKey.


**Steps in a Spark Streaming program**

Before we discuss the sample application, let’s take a look at different steps involved in a typical Spark Streaming program.

* Spark Streaming Context is used for processing the real-time data streams. So, the first step is to initialize the StreamingContext object using two parameters, SparkContext and sliding interval time. Sliding interval sets the update window where we process the data coming in as streams. Once the context is initialized, no new computations can be defined or added to the existing context. Also, only one StreamingContext object can be active at the same time.

* After Spark Streaming context is defined, we specify the input data sources by creating input DStreams. In our sample application, the input data source is a log message generator that uses Apache Kafka distributed database and messaging system. Log generator program creates random log messages to simulate a web server run-time environment where log messages are continuously generated as various web applications serve the user traffic.

* Define the computations using the Sparking Streaming Transformations API like map and reduce to DStreams.

* After streaming computation logic is defined, we can start receiving the data and process it using start method in StreamingContext object created earlier.

* Finally, we wait for the streaming data processing to be stopped using the awaitTermination method of StreamingContext object.




https://cdn.infoq.com/statics_s1_20161208-0302u1/resource/articles/apache-spark-streaming/en/resources/spark-streaming-kafka-sample-app.zip

https://github.com/databricks/reference-apps

https://chongyaorobin.wordpress.com/2015/07/08/step-by-step-of-install-apache-kafka-on-ubuntu-standalone-mode/