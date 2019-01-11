osboxes@osboxes:/$ sudo wget https://www.apache.org/dyn/closer.cgi?path=/kafka/0.10.1.1/kafka_2.10-0.10.1.1.tgz

    /home/osboxes/
    sudo mkdir /home/osboxes/Kafka
    cd /home/osboxes/Kafka



     sudo tar -xvf /home/osboxes/Downloads/kafka_2.10-0.10.1.1.tgz  -C /home/osboxes/Kafka

     sudo bin/kafka-server-start.sh config/server.properties
-------------------------------------------------------------------------------------------------
Compile the code using :

      mvn compile assembly:single

-----------------------------------------------------------------------------------------------------------------------
Kafka Commands 

     sudo fuser -k 2181/tcp

--zookepper runing
     
      sudo service zookeeper status

--Zookeper mode       

    echo srvr | nc localhost 2181 | grep Mode #(From 3.3.0 onwards)
-----------------------------------------------------------------------------------------------------------------------
--Start ServerS
    
      sudo bin/zookeeper-server-start.sh config/zookeeper.properties

--Create Topic

       bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic myTopic

--List of topic

      bin/kafka-topics.sh --list --zookeeper localhost:2181

--Write in topic

     bin/kafka-console-producer.sh --broker-list localhost:9092 --topic myTopic

--Reading topic data

     bin/kafka-console-consumer.sh --zookeeper localhost:2181 --from-beginning --topic myTopic

--Run Java code

    java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*”:. SimpleProducer <topic-name>

    java -cp /home/osboxes/Sparkdatafile/kafka-java-example-0.0.1-SNAPSHOT.jar  :. ProducerTest fast-messages

------------------------Running jar
  
    --http://howtoprogram.xyz/2016/05/02/apache-kafka-0-9-java-client-api-example/

     java -cp /home/osboxes/Sparkdatafile/kafka-java-example-0.0.1-SNAPSHOT.jar  com.howtoprogram.kafka.ProducerTest

--Copy jar

       sudo  mv /home/osboxes/Sparkdatafile/kafka-java-example-0.0.1-SNAPSHOT.jar /opt/Kafka/kafka_2.10-0.10.1.0/libs
       sudo mv /home/osboxes/Downloads/kafka-java-example-0.0.1-SNAPSHOT.jar /home/osboxes/Kafka/kafka_2.10-0.10.1.1

       sudo mv /home/osboxes/Downloads/KafkaAPIClient-1.0-SNAPSHOT.jar /home/osboxes/Kafka/kafka_2.10-0.10.1.1/libs

--run jar in unix

      java -cp /opt/Kafka/kafka_2.10-0.10.1.0/libs/* :/opt/Kafka/kafka_2.10-0.10.1.0/libs/kafka-java-example-0.0.1-SNAPSHOT.jar  com.howtoprogram.kafka.ProducerTest


    java -cp /opt/Kafka/kafka_2.10-0.10.1.0/libs/* :/opt/Kafka/kafka_2.10-0.10.1.0/libs/kafka-java-example-0.0.1-SNAPSHOT.jar  com.howtoprogram.kafka.ConsumerTest


   mv /home/osboxes/Downloads/argparse4j-0.5.0.jar /opt/Kafka/kafka_2.10-0.10.1.0/libs


     java -cp .:/opt/Kafka/kafka_2.10-0.10.1.0/libs/* :/opt/Kafka/kafka_2.10-0.10.1.0/libs/argparse4j-0.5.0.jar :/opt/Kafka/kafka_2.10-0.10.1.0/libs/kafka-java-example-0.0.1-SNAPSHOT.jar  com.howtoprogram.kafka.ProducerTest



      export CLASSPATH=$CLASSPATH:/opt/Kafka/kafka_2.10-0.10.1.0/libs/argparse4j-0.5.0.jar:.
      export CLASSPATH=$CLASSPATH:/opt/Kafka/kafka_2.10-0.10.1.0/libs/*:.

-----------------------------------------------------------------------------------------------------------------------
 First setup the consumer configuration
-----------------------------------------------------------------------------------------------------------------------


        Properties props = new Properties();
        props.put("zookeeper.connect", "host:2181");
        props.put("group.id", "mygroupid1");
        props.put("zookeeper.session.timeout.ms", "413");
        props.put("zookeeper.sync.time.ms", "203");
        props.put("auto.commit.interval.ms", "1000");
        ConsumerConfig cf = new ConsumerConfig(props) ;
-----------------------------------------------------------------------------------------------------------------------
Runing jar for development:

-----------------------------------------------------------------------------------------------------------------------
      java -cp .:/home/osboxes/Kafka/kafka_2.10-0.10.1.1/libs/*:/home/osboxes/Kafka/kafka_2.10-0.10.1.1/libs/KafkaAPIClient-1.0-SNAPSHOT.jar  com.spnotes.kafka.simple.Producer test


      java -cp .:/home/osboxes/Kafka/kafka_2.10-0.10.1.1/libs/*:/home/osboxes/Kafka/kafka_2.10-0.10.1.1/libs/KafkaAPIClient-1.0-SNAPSHOT.jar  com.spnotes.kafka.simple.Consumer test group1



* [https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart)
* [http://stackoverflow.com/questions/41410151/apache-kafka-exception-org-apache-kafka-clients-consumer-kafkaconsumer-subscri?noredirect=1#comment70029694_41410151](http://stackoverflow.com/questions/41410151/apache-kafka-exception-org-apache-kafka-clients-consumer-kafkaconsumer-subscri?noredirect=1#comment70029694_41410151)