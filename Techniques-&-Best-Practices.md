Techniques & Best Practices

Set spark.app.id when running on YARN. It enables monitoring. More details.
Follow best practices for Scala
Minimize network data shuffling by using shared variables, and favoring reduce operations (which reduce the data locally before shuffling across the network) over grouping (which shuffles all the data across the network)
Properly configure your temp work dir cleanups
in general tasks larger than about 20 KB are probably worth optimizing
This setting configures the serializer used for not only shuffling data between worker nodes but also when serializing RDDs to disk
If your objects are large, you may also need to increase the spark.kryoserializer.buffer config. This value needs to be large enough to hold the largest object you will serialize
A Spark Executor writes all its logs (including INFO, DEBUG) to stderr. The stdout is empty.
Monitoring Spark Apps
Web UI
Spark launches as embedded web-app by default to monitor a running application. this is launched on the Driver
It launches the monitoring app on 4040 by default.
If multiple apps are running simultaneously then they bind to successive ports
REST interface
Publish metrics to aggregation systems like Graphite, Ganglia and JMX
Scheduling
By default FIFO is used
Can be configured to use FAIR scheduler by changing spark.scheduler.mode. Spark assigns tasks between jobs in a “round robin” fashion in FAIR mode
