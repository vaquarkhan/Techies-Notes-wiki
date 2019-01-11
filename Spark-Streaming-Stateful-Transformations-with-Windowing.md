In streaming if we have a use case to track data across batches then we need state-ful DStreams.

For example we may track a user’s interaction in a website during the user session or we may track a particular twitter hashtag across time and see which users across the globe is talking about it.

Types of state-ful transformation.

State-ful DStreams are of two types – window based tracking and full session tracking.

For stateful tracking all incoming data should be transformed to key-value pairs such that the key states can be tracked across batches. This is a precondition.

Further we should also enable checkpointing, a concept which we will discuss in the later blogs.

> Window based tracking

In window based tracking the incoming batches are grouped in time intervals, i.e. group batches every ‘x’ seconds. Further computations on these batches are done using slide intervals.

For example if the window interval = 3 secs and slide interval = 2 secs, then all incoming data will be grouped in batches every 3 seconds and the computations on these batches will happen every 2 seconds. Alternatively we can say, do computations every 2 seconds on the batches that arrived in the last 3 seconds.

spark-streaming-dstream-window
In the above diagram we see that the incoming batches are grouped every 3 units of time (window interval) and the computations are done every 2 units of time (slide interval).
Note: Unlike Apache Flink, Apache Spark does not have a concept of tumbling window, all windows are sliding.

API

A popular API for window based transformations is

PairDStreamFunctions.reduceByKeyAndWindow.

There are several overloaded versions of this API, let’s see the one that has the most number of parameters. After this explanation the rest of the overloaded versions of this API should be self explanatory.

API-spark-streaming

Returns: The transformed DStream[(K, V)]

reduceFunc: The associative reduce function.

invReduceFunc: The inverse of the above reduce function. This is required for efficient computing of incoming and outgoing batches. With the help of this function the value of the batches which are outgoing is deducted from the accumulated value of the above reduce function. For example, if we are computing the sum of the incoming values for the respective keys then for the outgoing batches we will subtract the values for the respective keys (provided they are present in the current batch else ignore).

windowDuration: Units of time for grouping the batches, this should be a multiple of the batch interval.

slideDuration: Units of time for computation, this should be a multiple of the batch interval.partitioner: The partitioner to use for storing the resulting DStream. For more information on partitioning read this.

filterFunc: Function to filter out expired key-value pairs, i.e. for example if we don’t get an update for a key for sometime we may wish to remove it.

