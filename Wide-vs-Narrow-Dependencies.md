Compositions on RDDs are represented as a lineage graph ; a Directed Asyclic Graph 9DAG) representing the compositions done on the RDD.

**Narrow Dependencies :**

Each partion of the parent RDD is used by at most one partition of the child RDD.

FAST ! No shuffle necessary.Optimizations like pipelining possible.

Ex-  map,filter,union,narrow dependency join


**Wide Dependencies :**

Each partion of the parents RDD may be dpendend on by multiple child partions

Slow ! Requies  all or some data to be shuffled over the network.


Ex GroupByKey, input not co partitions Join


![](https://4.bp.blogspot.com/-YGgWG0dOd1w/WdBrr-UeclI/AAAAAAAACYs/JpGnsvQuGrEVgWtDPyT4pFUf4BmFFNW_ACLcBGAs/s320/Untitled.png)

– Narrow dependency: RDD operations like map, union, filter can operate on a single partition and map the data of that partition to resulting single partition. These kind of operations which maps data from one to one partition are referred as Narrow operations. Narrow operations doesn’t required to distribute the data across the partitions.

– Wide dependency: RDD operations like groupByKey, distinct, join may require to map the data across the partitions in new RDD. These kind of operations which maps data from one to many partitions are referred as Wide operations


![](https://d26dzxoao6i3hh.cloudfront.net/items/1m0S1m2V0k302D45252Y/Image%202014-04-03%20at%2010.24.16%20nachm..png?v=7bf2cf00)

The scheduler will examine the type of dependencies and group the narrow dependency RDD into a unit of processing called a stage.  Wide dependencies will span across consecutive stages within the execution and require the number of partition of the child RDD to be explicitly specified.


![](http://2.bp.blogspot.com/-5sDP78mSdlw/Ur3szYz1HpI/AAAAAAAABCo/Aak2Xn7TmnI/s400/p2.png)



A typical execution sequence is as follows ...
RDD is created originally from external data sources (e.g. HDFS, Local file ... etc)
RDD undergoes a sequence of TRANSFORMATION (e.g. map, flatMap, filter, groupBy, join), each provide a different RDD that feed into the next transformation.
Finally the last step is an ACTION (e.g. count, collect, save, take), which convert the last RDD into an output to external data sources
The above sequence of processing is called a lineage (outcome of the topological sort of the DAG).  Each RDD produced within the lineage is immutable.  In fact, unless if it is cached, it is used only once to feed the next transformation to produce the next RDD and finally produce some action output.

In a classical distributed system, fault resilience is achieved by replicating data across different machines together with a active monitoring system.  In case of any machine crashes, there is always another copy of data residing in a different machine from where recovery can take place. 

Fault resiliency in Spark takes a different approach.  First of all, as a large scale compute cluster, Spark is not meant to be a large scale data cluster at all.  Spark makes two assumptions of its workload.
The processing time is finite (although the longer it takes, the cost of recovery after fault will be higher)
Data persistence is the responsibility of external data sources, which keeps the data stable within the duration of processing.
Spark has made a tradeoff decision that in case of any data lost during the execution, it will re-execute the previous steps to recover the lost data.  However, this doesn't mean everything done so far is discarded and we need to start from scratch at the beginning.  We just need to re-executed the corresponding partition in the parent RDD which is responsible for generating the lost partitions, in case of narrow dependencies, this resolved to the same machine. 

Notice that the re-execution of lost partition is exactly the same as the lazy evaluation of the DAG, which starts from the leaf node of the DAG, tracing back the dependencies on what parent RDD is needed and then eventually track all the way to the source node.  Recomputing the lost partition is done is a similar way, but taking partition as an extra piece of information to determine which parent RDD partition is needed.

However, re-execution across wide dependencies can touch a lot of parent RDD across multiple machines and may cause re-execution of everything. To mitigate this, Spark persist the intermediate data output from a Map phase before it shuffle them to different machines executing the reduce phase.  In case of machine crash, the re-execution (from another surviving machine) just need to trace back to fetch the intermediate data from the corresponding partition of the mapper's persisted output.  Spark also provide a checkpoint API to explicitly persist intermediate RDD so re-execution (when crash) doesn't need to trace all the way back to the beginning.  In future, Spark will perform check-pointing automatically by figuring out a good balance between the latency of recovery and the overhead of check-pointing based on statistical result.


