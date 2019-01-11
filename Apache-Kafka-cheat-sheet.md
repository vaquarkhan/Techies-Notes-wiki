

# Topics

Creating a New Topic
```
kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic my-topic
```
Verify the topic
```
kafka-topics --list --zookeeper localhost:2181
```
Adding Partitions
```
kafka-topics --zookeeper localhost:2181 --alter --topic my-topic --partitions 16
```
Deleting a Topic
```
kafka-topics --zookeeper localhost:2181 --delete --topic my-topic
```
Listing All Topics in a Cluster
```
kafka-topics --zookeeper localhost:2181 --list
```
Describing Topic Details
```
kafka-topics --zookeeper localhost:2181/kafka-cluster --describe
```
Show Under-replicated Partitions for topics
```
kafka-topics --zookeeper localhost:2181/kafka-cluster --describe --under-replicated-partitions
```

# Producers
Produce messages standard input
```
kafka-console-producer --broker-list localhost:9092 --topic my-topic
```
Produce messages file
```
kafka-console-producer --broker-list localhost:9092 --topic test < messages.txt
```
Produce Avro messages
```
kafka-avro-console-producer --broker-list localhost:9092 --topic my.Topic --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"f1","type":"string"}]}' --property schema.registry.url=http://localhost:8081
```
And enter a few values from the console:
```
{"f1": "value1"}
```

# Consumers

## Consume messages

Start a consumer from the beginning of the log
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic my-topic --from-beginning
```
Consume 1 message
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic my-topic  --max-messages 1
```

Consume 1 message from `__consumer_offsets`
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic __consumer_offsets --formatter 'kafka.coordinator.GroupMetadataManager$OffsetsMessageFormatter' --max-messages 1
```

Consume, specify consumer group:
```
kafka-console-consumer --topic my-topic --new-consumer --bootstrap-server localhost:9092 --consumer-property group.id=my-group
```

## Consume Avro messages
```
kafka-avro-console-consumer --topic position-reports --new-consumer --bootstrap-server localhost:9092 --from-beginning --property schema.registry.url=localhost:8081 --max-messages 10
```

```
kafka-avro-console-consumer --topic position-reports --new-consumer --bootstrap-server localhost:9092 --from-beginning --property schema.registry.url=localhost:8081
```

## Consumers admin operations

List Groups
```
kafka-consumer-groups --new-consumer --list --bootstrap-server localhost:9092
```
Describe Groups
```
kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group testgroup
```

# Config
Set the retention for the topic
```
kafka-configs --zookeeper localhost:2181 --alter --entity-type topics --entity-name my-topic --add-config retention.ms=3600000
``` 
Show all configuration overrides for a topic
```
kafka-configs --zookeeper localhost:2181 --describe --entity-type topics --entity-name my-topic
```
Delete a configuration override for retention.ms for a topic 
```
kafka-configs --zookeeper localhost:2181 --alter --entity-type topics --entity-name my-topic --delete-config retention.ms 
```

# Performance

Producer
```
kafka-producer-perf-test --topic position-reports --throughput 10000 --record-size 300 --num-records 20000 --producer-props bootstrap.servers="localhost:9092"
```

# ACLs
```
kafka-acls --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:Bob --consumer --topic topicA --group groupA
```

```
kafka-acls --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:Bob --producer --topic topicA
```
List the ACLs
```
 kafka-acls --authorizer-properties zookeeper.connect=localhost:2181 --list --topic topicA
```

# Zookeeper 
Enter zookeepr shell:
```
zookeeper-shell localhost:2182 ls /
```

--------------------------------------------------------------------------------------------


- http://kafka.apache.org/documentation.html

- http://ronnieroller.com/kafka/cheat-sheet#describe-configs-for-a-topic

- https://abdulhadoop.wordpress.com/2017/11/06/kafka-command-cheat-sheet/

- https://martin.atlassian.net/wiki/spaces/lestermartin/pages/230916100/Kafka+Cheat+Sheet?preview=%2F230916100%2F231276546%2FKafka+Sizing.pdf

- https://community.hortonworks.com/articles/80813/kafka-best-practices-1.html

- https://community.hortonworks.com/articles/109848/how-to-view-the-oldest-message-in-a-kafka-topic.html


--------------------------------------------------------------------------------------------
**How to view the oldest message in a Kafka topic using console consumer script.**
**Article**
In a non-Kerberized environment, for Kafka console consumer command displays all messages, perform the following:

Note:
Here, <broker_host> is Kafka broker hostname, <zk_host> is zookeeper hostname and Kafka port will be 6667:

Using Old consumer API (prior to HDP 2.5.x), run the following:

      bin/kafka-console-consumer.sh --zookeeper <zk_host>:2181 --topic test --from-beginning

Using New consumer API ( From HDP 2.5.x onwards), run the following:

     bin/kafka-console-consumer.sh --bootstrap-server <broker_host>:6667 --topic test --from-beginnin

To view a specific number of message in a Kafka topic, use the --max-messages option. To view the oldest message, run the console consumer with --from-beginning and --max-messages 1:

Using Old consumer API ( Prior to HDP 2.5.x):


      bin/kafka-console-consumer.sh --zookeeper <zk_host>:2181 --topic test --from-beginning 
     --max-messages 1

Using New consumer API ( From HDP 2.5.x onwards):

      bin/kafka-console-consumer.sh --bootstrap-server <broker_host>:6667 --topic test --from-beginning 
      --max-messages 