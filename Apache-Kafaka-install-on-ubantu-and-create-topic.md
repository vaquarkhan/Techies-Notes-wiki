Apache Kafka

Apache Kafka is a real time, fault tolerant, scalable messaging system for moving data in real time. It's a good candidate for use cases like capturing user activity on websites, logs, stock ticker data, and instrumentation data.

Kafka works like a distributed database and is based on a partitioned and replicated low latency commit log. When we post a message to Kafka, it's replicated to different servers in the cluster and at the same time it’s also committed to disk.

**[Apache Kafka](http://kafka.apache.org/documentation.html)**includes client API as well as a data transfer framework called Kafka Connect.

**[Kafka Clients](http://kafka.apache.org/documentation.html#api):** Kafka includes Java clients (for both message producers and consumers). We will use the Java producer client API in our sample application.

**[Kafka Connect:](http://kafka.apache.org/documentation.html#connect) **Kafka also includes Kafka Connect, which is a framework for streaming data between Apache Kafka and external data systems to support the data pipelines in organizations. It includes import and export connectors to move data sets into and out of Kafka. Kafka Connect program can run as a standalone process or as a distributed service and supports REST interface to submit the connectors to Kafka Connect cluster using a REST API.


**How to install**


https://devops.profitbricks.com/tutorials/install-and-configure-apache-kafka-on-ubuntu-1604-1/

https://kafka.apache.org/quickstart

**Kafka Cheat Sheet**

 
       $ kafka-topics.sh --describe --zookeeper localhost:2181 --topic beacon

    Topic:beacon    PartitionCount:6    ReplicationFactor:1 Configs:
    Topic: beacon   Partition: 0    Leader: 1   Replicas: 1 Isr: 1
    Topic: beacon   Partition: 1    Leader: 1   Replicas: 1 Isr: 1

**Add Partitions to a Topic**

        $ kafka-topics.sh --alter --zookeeper localhost:2181 --topic beacon --partitions 3

WARNING: If partitions are increased for a topic that has a key, the partition logic or ordering of the messages will be affected
Adding partitions succeeded!

**Delete Topic**

       $ kafka-run-class.sh kafka.admin.DeleteTopicCommand --zookeeper localhost:2181 --topic test

kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic file_acquire_complete
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic job_result
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic trigger_match
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic event_result
$ kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic job_result

**Created topic "job_result".**

         $ kafka-topics.sh --list --zookeeper localhost:2181

**event_result**
file_acquire_complete
job_result
**trigger_match**

       $ kafka-console-producer.sh --broker-list localhost:9092 --topic test
       $ kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning

* https://kafka.apache.org/quickstart
* http://events.linuxfoundation.org/sites/events/files/slides/The%20Best%20of%20Apache%20Kafka%20Architecture.pdf
* http://events.linuxfoundation.org/sites/events/files/slides/developing.realtime.data_.pipelines.with_.apache.kafka_.pdf
* https://cwiki.apache.org/confluence/display/KAFKA/Kafka+Command+Line+and+Related+Improvements

Tutorial :+1: 
* https://www.tutorialspoint.com/apache_kafka/
* http://blog.cloudera.com/blog/2014/09/apache-kafka-for-beginners/
* http://hortonworks.com/apache/kafka/
* http://www.michael-noll.com/blog/2014/08/18/apache-kafka-training-deck-and-tutorial/
* http://www.javaworld.com/article/3060078/big-data/big-data-messaging-with-kafka-part-1.html





