
At high level, when any action is called on the RDD, Spark creates the DAG and submits it to the DAG scheduler.

The DAG scheduler divides operators into stages of tasks. A stage is comprised of tasks based on partitions of the input data. The DAG scheduler pipelines operators together. For e.g. Many map operators can be scheduled in a single stage. The final result of a DAG scheduler is a set of stages.
The Stages are passed on to the Task Scheduler.The task scheduler launches tasks via cluster manager (Spark Standalone/Yarn/Mesos). The task scheduler doesn't know about dependencies of the stages.
The Worker executes the tasks on the Slave.
Let's come to how Spark builds the DAG.

At high level, there are two transformations that can be applied onto the RDDs, namely narrow transformation and wide transformation. Wide transformations basically result in stage boundaries.

Narrow transformation - doesn't require the data to be shuffled across the partitions. for example, Map, filter etc..

wide transformation - requires the data to be shuffled for example, reduceByKey etc..

Let's take an example of counting how many log messages appear at each level of severity,

Following is the log file that starts with the severity level,

    INFO I'm Info message
    WARN I'm a Warn message
    INFO I'm another Info message
and create the following scala code to extract the same,

    val input = sc.textFile("log.txt")
    val splitedLines = input.map(line => line.split(" "))
                        .map(words => (words(0), 1))
                        .reduceByKey{(a,b) => a + b}


This sequence of commands implicitly defines a DAG of RDD objects (RDD lineage) that will be used later when an action is called. Each RDD maintains a pointer to one or more parents along with the metadata about what type of relationship it has with the parent. For example, when we call val b = a.map() on a RDD, the RDD b keeps a reference to its parent a, that's a lineage.

To display the lineage of an RDD, Spark provides a debug method toDebugString(). For example executing toDebugString() on the splitedLines RDD, will output the following:

    (2) ShuffledRDD[6] at reduceByKey at <console>:25 []
    +-(2) MapPartitionsRDD[5] at map at <console>:24 []
    |  MapPartitionsRDD[4] at map at <console>:23 []
    |  log.txt MapPartitionsRDD[1] at textFile at <console>:21 []
    |  log.txt HadoopRDD[0] at textFile at <console>:21 []
The first line (from the bottom) shows the input RDD. We created this RDD by calling sc.textFile(). Below is the more diagrammatic view of the DAG graph created from the given RDD.

**RDD DAG graph**

Once the DAG is build, the Spark scheduler creates a physical execution plan. As mentioned above, the DAG scheduler splits the graph into multiple stages, the stages are created based on the transformations. The narrow transformations will be grouped (pipe-lined) together into a single stage. So for our example, Spark will create two stage execution as follows:

**Stages**

The DAG scheduler will then submit the stages into the task scheduler. The number of tasks submitted depends on the number of partitions present in the textFile. Fox example consider we have 4 partitions in this example, then there will be 4 set of tasks created and submitted in parallel provided there are enough slaves/cores. Below diagram illustrates this in more detail:

https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/spark-dagscheduler.html


**Task execustion**

* [http://stackoverflow.com/questions/25836316/where-to-learn-how-dag-works-under-the-covers-in-rdd](http://stackoverflow.com/questions/25836316/where-to-learn-how-dag-works-under-the-covers-in-rdd)

* https://stackoverflow.com/questions/25836316/how-dag-works-under-the-covers-in-rdd

* [https://www.quora.com/As-it-is-mentioned-Apache-Spark-follows-a-DAG-Directed-Acyclic-Graph-execution-engine-for-execution-What-is-the-whole-concept-about-it-and-the-overall-architecture-of-the-Spark](https://www.quora.com/As-it-is-mentioned-Apache-Spark-follows-a-DAG-Directed-Acyclic-Graph-execution-engine-for-execution-What-is-the-whole-concept-about-it-and-the-overall-architecture-of-the-Spark)