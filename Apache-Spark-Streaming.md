Spark Streaming is a real-time processing tool, that has a high level API, is fault tolerant, and is easy to integrate with SQL DataFrames and GraphX.

On a high level Spark Streaming works by running receivers that receive data from for example S3, Cassandra, Kafka etc... and it divides these data into blocks, then pushes these blocks into Spark, then Spark will work with these blocks of data as RDDs, from here you get your results. The following diagram will demonstrate the process:

![](https://camo.githubusercontent.com/d8d7532657253d5ccba79f7572e8326ccc5b2d2f/687474703a2f2f322e62702e626c6f6773706f742e636f6d2f2d323732445f7566325159412f56647671304554493956492f41414141414141414152452f4342724b2d5237434b736f2f733634302f53637265656e25324253686f74253242323031352d30382d32342532426174253242392e30392e3530253242504d2e706e67)


Why is Spark Streaming being adopted widely?

Has an easy high level API
Easy to integrate with other parts of Spark ecosystem like Spark SQL.
If you know how to use Spark, Spark Streaming will be intuitive and easy to operate as well (very similar)
Some of the companies that use Spark Streaming and how they use it?

Netflix

They are interested in using Spark Streaming for collecting data about trending movies and shows in real time, and using that data to give up to date recommendations to users based on their interest and habits [2].

Pearson

Pearson is the world's largest education company, they help people all over the world to aim higher and improve their lives through education [4].

They are using Spark Streaming to monitor each student's progress and what ways are more effective for them to learn faster [3].

Demonstrating how to use Spark Streaming with Kafka in Java

We will use the Spark Streaming receivers to receive data from Kafka, this data will be stored in what is called Spark executors then Spark Streaming can process the data.

In order to ensure that data won't be lost when there is a failure you have to enable Write Ahead Logs in Spark Streaming.

In order to incorporate Spark Streaming into your application that uses Kafka, you need to do the following three steps; Link Spark Streaming to your project, Program your application, and Deploy your application:

1 . Link Spark Streaming Kafka into your project by importing the following artifact from maven central:

Import Spark Streaming Kafka into your project

<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming-kafka_2.10</artifactId>
    <version>1.4.1</version>
</dependency>
For linking other Streaming sources like Flume, Kinesis, Twitter, and MQTT use the following for the artifactId:

       spark-streaming-flume_2.10
       spark-streaming-kinesis-asl_2.10
       spark-streaming-twitter_2.10
       spark-streaming-mqtt_2.10

2 . The programing part: Initialize a Streaming Context, this is the entry point for all Spark Streaming functionalities. It can be created from a SparkConf object. SparkConf enables you to configure some properties such as Spark Master and application name, as well as arbitrary key-value pairs through the set() method.

Spark Master is the cluster manager to connect to, some of the allowed URLs:

local (Run Spark locally with one worker thread)

local[K] (Run Spark locally with K threads, usually k is set up to match the number of cores on your machine)

spark://HOST:PORT (Connect to a given cluster master. The port must match with your master, default is 7077)

Setting up SparkConf and JavaStreamingContext

                 import org.apache.spark.*;
                 import org.apache.spark.streaming.api.java.*;

                SparkConf conf = new SparkConf().setAppName(appName).setMaster(master);
                JavaStreamingContext ssc = new JavaStreamingContext(conf, Duration(1000));
                String master is a special “local[*]” string to run in local mode.

Duration(1000) is a batch interval this has to be set based on your performance needs. for more details on this check out this doc on Performance Tuning.

You can also create a JavaStreamingContext from a JavaSparkContext:

Create JavaStreamingContext from JavaSparkContext

          import org.apache.spark.streaming.api.java.*;

         JavaSparkContext sc =new JavaSparkContext(); //existing JavaSparkContext
        JavaStreamingContext ssc = new JavaStreamingContext(sc, Durations.seconds(1));
Then create an input DStrream:

Create an input DStream

                 import org.apache.spark.streaming.kafka.*;

           JavaPairReceiverInputDStream<String, String> kafkaStream = 
             KafkaUtils.createStream(streamingContext,
    
 [ZK quorum], [consumer group id], [per-topic number of Kafka partitions to consume]);
DStream represents a chain of RDDs each RDD contains data from a certain time interval. The following represents a high level diagram of a DStream:

3 . Finally for deploying your application, if you are using Scala or Java simply package spark-streaming-kafka_2.10 and its dependencies into the application JAR. Then use spark-submit to launch your application. For more details on deploying your spark streaming application visit this link deploying information

To Spark submit your application

            ./bin/spark-submit --packages org.apache.spark:spark-streaming-kafka_2.10:1.4.1 ...

The following is a simple example to demonstrate how to use Spark Streaming.

Simple example on Spark Streaming

This example is from Spark's documentations [1]. Say we have a data server listening on a TCP socket and we want to count the words received by that server:

As mentioned previously first we need to create a JavaStreamingContext:

Create JavaStreamingContext

         import org.apache.spark.*;
         import org.apache.spark.api.java.function.*;
         import org.apache.spark.streaming.*;
         import org.apache.spark.streaming.api.java.*;
         import scala.Tuple2;

                // Create a local StreamingContext with two working thread and batch interval of 1 second
                SparkConf conf = new SparkConf().setMaster("local[2]").setAppName("NetworkWordCount")
                JavaStreamingContext jssc = new JavaStreamingContext(conf, Durations.seconds(1))

Then we need to create a DStream:

Create a DStream

           // Create a DStream that will connect to hostname:port, like localhost:9999
           JavaReceiverInputDStream<String> lines = jssc.socketTextStream("localhost", 9999);

Note DStream lines will receive the data from the data server. Each block of data received is a line of words, remember we want to count the words so we need to transform the lines into words by looking for the spaces between the words and split them:

Transform the lines DStream into a words DStream

          // Split each line into words
          JavaDStream<String> words = lines.flatMap(
            new FlatMapFunction<String, String>() {
             @Override public Iterable<String> call(String x) {
               return Arrays.asList(x.split(" "));
             }
         });

flatMap is a DStream method that will make a new DStream made of smaller blocks than the original stream in this case it is making lines into words.

Then we need to count the words received:

Counting the words

          // Count each word in each batch
           JavaPairDStream<String, Integer> pairs = words.mapToPair(
           new PairFunction<String, String, Integer>() {
             @Override public Tuple2<String, Integer> call(String s) {
               return new Tuple2<String, Integer>(s, 1);
             }
            });

         JavaPairDStream<String, Integer> wordCounts = pairs.reduceByKey(
          new Function2<Integer, Integer, Integer>() {
           @Override public Integer call(Integer i1, Integer i2) {
           return i1 + i2;
            }
          });

// Print the first ten elements of each RDD generated in this DStream to the console

         wordCounts.print();

To start the processing after all transformations are set up:

Start the processing

          jssc.start();              // Start the computation
         jssc.awaitTermination();   // Wait for the computation to terminate
       
**MLlib**

MLlib is a part of Spark's ecosystem. MLlib is a machine learning framework its main goal is to make building a machine learning application easy, and scalable by learning from big data sets. MLlib consists of the following learning algorithms:

* Classification
* Dimensionality Reduction
* Clustering
* Regression
* Collaborative Filtering
* Recommendation
* Statistics
and many more...
For a full list of algorithms with their descriptions visit the following MLlib guide.

What if you want to go beyond these algorithms, then you will need what is called a ML Workflows.

What is ML Workflows?

The best way to explain ML Workflows is to go over a simple example. Lets say we have a large article that consists of many topics, this would be our data and we want to predict the topics using ML Workflow, here is a diagram of what we would have:

![](https://camo.githubusercontent.com/cafa6f28e05a3c4c4af27866a2c9b044426a8777/687474703a2f2f342e62702e626c6f6773706f742e636f6d2f2d57324879394948394c47632f56643631506c4f2d476f492f414141414141414141534d2f6e375531707261735547412f733634302f53637265656e25324253686f74253242323031352d30382d3236253242617425324231312e35382e3437253242504d2e706e67)

There are two main components for a ML workflow; Training, and Testing:

![](https://camo.githubusercontent.com/5c8b6a8117391066c9b43b29ad2d4eb8816ca843/687474703a2f2f312e62702e626c6f6773706f742e636f6d2f2d574148736b33737435374d2f5664363633425a6a4a79492f41414141414141414153672f69576d38773566656742512f733634302f53637265656e25324253686f74253242323031352d30382d3237253242617425324231322e32322e3439253242414d2e706e67)

Training will consist of RDDs that have the features which are texts in this case and has the predictions next to the features this information will be available for each row or instance.

Testing will have only the features present in the RDD, then will use the model from training to make predictions for example by reading keywords.

Training will have several components of its own; Load data, pull features, train model, and evaluate. Here is a diagram for demonstration:

![](https://camo.githubusercontent.com/aedcaf0898d8078271e4c45220287eaf8bbbbb5e/687474703a2f2f342e62702e626c6f6773706f742e636f6d2f2d434c4b423773486974336f2f5664374454756b394733492f41414141414141414153342f58525472782d656c592d512f733634302f53637265656e25324253686f74253242323031352d30382d3237253242617425324231322e35382e3433253242414d2e706e67)

Some of the problems that will be encountered using this ML Workflow:

You might need to work with a lot of RDDs and data types.
It might be easy to write the code for the ML Workflow but it will be hard to reuse it later, especially when you move into testing, it needs to handle all kinds of data.
In order to improve the final result you might need to play with each step's parameter in the ML Workflow based on the data you have.
To fix these problems, Spark came up with Pipelines. How does Pipelines make things easier?

**Pipelines**

Pipelines will make use of the DataFrames, which were explained previously but to recap, DataFrames are basically RDDs that have named columns and types (schema), from our example we can have one column as the predictions and an other as the texts, it also provides a Domain Specific Language (DSL) for example we can filter all the predictions that are equal to 1 (Microservices topic) from the table. This Pipeline feature will take care of the first problem stated above; dealing with a lot of RDDs and data types.

We can do Abstractions with Pipelines, trasforming one DataFrame into another, for example during the Pull Features step in ML Workflow we can transform the loaded data (texts, and predictions columns) into another DataFrame that has Texts, Predictions, and Features columns (Features will be made from the texts)

![](https://camo.githubusercontent.com/5525fa3960db80d3083c724118e306116cd7e73c/687474703a2f2f312e62702e626c6f6773706f742e636f6d2f2d4579567a384846426e57592f566551424a3856686337492f41414141414141414155382f64374b6e776f4c5068346b2f733634302f53637265656e25324253686f74253242323031352d30382d3331253242617425324231322e32332e3539253242414d2e706e67)

An Estimator can be used in the next step of the ML Workflow Train Model, what an estimator does is that it takes the features and predictions from a Dataframe and will make a prediction for us, matching a feature with a prediction.

In the final stage of the ML Workflow Evaluate, we can use an evaluator, this will take the final DataFrame and return a value. For example it can compare the prediction we got from the previous step with what it should be and then return how accurate it was.

Here is a diagram to summarize the process:

![](https://camo.githubusercontent.com/f6d4787040876b2c3e859e2f585cfc108471a33c/687474703a2f2f312e62702e626c6f6773706f742e636f6d2f2d4a5f676f3842416831654d2f5665414a786657653166492f414141414141414141546b2f74793245766177736a73632f733634302f53637265656e25324253686f74253242323031352d30382d3238253242617425324231322e31312e3239253242414d2e706e67)

In the training part at the top we load data, we get a DataFrame then we transform that data as mentioned before, then We use a model to estimate, in this case estimate a topic prediction. Now under training in that green box (transformer and estimator) will act as an estimator. If we go to testing we use the model from training to do the estimation, and the red block will act as a transformer, so now under testing instead of having new models to make predictions, it just gets data at the top and outputs the predictions at the bottom, no need for models to predict. This will take care of the second problem listed above.

Only one problem left from the three mentioned above, the parameter tuning problem, with Pipelines this can be taken care of by passing the pipeline to a CrossValidator which will take care of the parameter tuning automatically.

If we zoom into the green box under training (from above), this would be the Pipeline. This Pipeline consists of three parts Tokenizer, HashingTF, and LogisticRegression:

![](https://camo.githubusercontent.com/a3861f4f1ab709aeee4ad46c5c55f4233024832d/687474703a2f2f312e62702e626c6f6773706f742e636f6d2f2d307476556265777a714a4d2f566541535437736a7a6f492f41414141414141414154342f314378506a4563374653452f733634302f53637265656e25324253686f74253242323031352d30382d3238253242617425324231322e34382e3031253242414d2e706e67)

Tokenizer will take the text data from the DataFrame and break them into words, then HashingTF will take these words and transform them into vector feature a format that would be readable by LogisticRegression, and finally a model will be produced that can make predictions.

References

[1] http://spark.apache.org/docs/

[2] http://techblog.netflix.com/2015/03/can-spark-streaming-survive-chaos-monkey.html

[3] https://databricks.com/blog/2014/12/08/pearson-uses-spark-streaming-for-next-generation-adaptive-learning-platform.html

[4] https://www.infoq.com/articles/apache-spark-streaming
