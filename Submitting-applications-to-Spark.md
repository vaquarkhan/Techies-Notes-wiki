From http://stackoverflow.com/questions/34391977/spark-submit-does-automatically-uploadthe-
jar-to-cluster

This seems to be a common problem, here's what my research into this has turned up. In practice it seems that a lot of people ask this question here and on other forums, so I would think the default behavior is that Spark's driver does NOT push jars around a cluster, just the classpath. The point of confusion, that I along with other newcomers commonly suffer from is this:
The driver does NOT push your jars to the cluster. The master in the cluster DOES push your jars to the workers. In theory. I see various things in the docs for Spark that seem to contradict the idea that if you pass an assembly jar and/or dependencies to spark-submit with --jars, that these jars are pushed out to the cluster. For example, on the plus side, "When using spark-submit, the application jar along with any jars included with the --jars
option will be automatically transferred to the cluster. " That's from 

http://spark.apache.org/docs/latest/submitting-applications.html#advanced-dependencymanagement 

So far so good right? But that is only talking about once your master has the jars, it can push them to workers.
There's a line on the docs for spark-submit, for the main parameter application-jar. Quoting another answer I wrote:
"I assume they mean most people will get the classpath out through a driver config option. I know most of the docs for spark-submit make it look like the script handles moving your code around the cluster, but it only moves the classpath around for you. The driver does not load the jars to the master. For example in this line from Launching Applications with sparksubmit explicitly says you have to move the jars yourself or make them "globally available":

application-jar: Path to a bundled jar including your application and all dependencies. The URL must be globally visible inside of your cluster, for instance, an

    hdfs:// path or a file://

path that is present on all nodes." From an answer I wrote on Spark workers unable to find JAR on EC2 cluster So I suspect we are seeing a "bug" in the docs. I would explicitly put the jars on each node, or "globally available" via NFS or HDFS, for another reason from the docs:

From Advanced Dependency Management, it seems to present the best of both worlds, but also a great reason for manually pushing your jars out to all nodes: local: - a URI starting with local:/ is expected to exist as a local file on each worker node. 

This means that no network IO will be incurred, and works well for large files/JARs that are pushed to each worker, or shared via NFS, GlusterFS, etc. Bottom line, I am coming to realize that to truly understand what Spark is doing, we need to bypass the docs and read the source code. Even the Java docs don't list all the parameters for things like