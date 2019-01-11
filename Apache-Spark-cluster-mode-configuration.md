keep Spark driver node or master node separate than HDFS master node. 

HDFS master node (Namenode) has all metadata about HDFS files (data blocks and replicas). The reason keeping Namenode separate is that Namenode keeps all metadata in memory and needs to respond to all new requests to locate the data blocks and metadata updates. So it is usually heavily loaded, now come to Spark driver node is for application context and Spark master node is for resource allocation. 


http://spark.apache.org/docs/latest/hardware-provisioning.html
http://spark.apache.org/docs/latest/configuration.html#dynamic-allocation
