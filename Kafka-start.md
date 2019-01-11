 

[https://youtu.be/GRPLRONVDWY](https://youtu.be/GRPLRONVDWY)

tar -xvf /home/osboxes/Downloads/kafka_2.10-0.10.1.0.tgz  -C /opt/Kafka/

/home/osboxes/Downloads/kafka_2.10-0.10.1.0.tgz


sudo tar -xvf kafka_2.10-0.10.1.0.tgz -C /opt/Kafka/

        sudo /opt/Kafka/kafka_2.10-0.10.1.0/bin/kafka-server-start.sh /opt/Kafka/kafka_2.10-0.10.1.0/config/server.properties --override property=value

        sudo /opt/Kafka/kafka_2.10-0.10.1.0/bin/kafka-server-start.sh /opt/Kafka/kafka_2.10-0.10.1.0/config/server.properties --override property=value


       sudo /opt/Kafka/kafka_2.10-0.10.1.0/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1  --partitions 1 --topic testing


**Command :+1: **

            `osboxes@osboxes:~$ cd /`
            `osboxes@osboxes:/$ cd opt`

             `osboxes@osboxes:/opt$ ls `
              `Kafka`

          
          osboxes@osboxes:/opt$ cd bin`
          bash: cd: bin: No such file or directory`

          `osboxes@osboxes:/opt$ ls`    
          `Kafka`
      
          `osboxes@osboxes:/opt$ cd Kafaka`
          `bash: cd: Kafaka: No such file or directory`

          `osboxes@osboxes:/opt$ ls `
           `Kafka`
          
          `osboxes@osboxes:/opt$ cd kafka`
           `bash: cd: kafka: No such file or directory`

           `osboxes@osboxes:/opt$ cd Kafka`
           `osboxes@osboxes:/opt/Kafka$ ls`

           `kafka-0.10.1.0-src  kafka_2.10-0.10.1.0`
     
            `osboxes@osboxes:/opt/Kafka$ cd bi`
            `bash: cd: bi: No such file or directory`
            
             `osboxes@osboxes:/opt/Kafka$ l`
             `kafka-0.10.1.0-src/  kafka_2.10-0.10.1.0/`
            
             `osboxes@osboxes:/opt/Kafka$ ls`
             `kafka-0.10.1.0-src  kafka_2.10-0.10.1.0`
             
              `osboxes@osboxes:/opt/Kafka$ cd kafka_2.10-0.10.1.0`
              `osboxes@osboxes:/opt/Kafka/kafka_2.10-0.10.1.0$ ls`
               
             'bin  config  libs  LICENSE  logs  NOTICE  site-docs`
              `osboxes@osboxes:/opt/Kafka/kafka_2.10-0.10.1.0$ cd bin`

               `sudo /opt/Kafka/kafka_2.10-0.10.1.0/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic testing`


       sudo /opt/Kafka/kafka_2.10-0.10.1.0/bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic testing --from-beginning




https://www.mapr.com/blog/getting-started-sample-programs-apache-kafka-09

