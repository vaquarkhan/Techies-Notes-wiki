Check the Monitoring and Instrumentation API: http://spark.apache.org/docs/latest/monitoring.html




Try hitting:  http://<server-url>:18080/api/v1

Then hit /applications.

That should give you a list of running spark jobs on a given server.


Don't have a Spark cluster up to verify this, but try port 8080.  

http://spark-master-ip:8080/api/v1/applications.


