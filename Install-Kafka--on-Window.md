These are the steps I followed to run kafka on Windows

* Install Zookeeper first (I downloaded v3.3.6) zookeeper-3.3.6.tar.gz
* Extract Zookeeper and run this command in powershell/cmd \zookeeper-3.3.6\bin> .\zkServer.cmd Now this should up a Zookeeper instance on localhost:2181
* Download Kafka binary version (I downloaded v0.10.0.1)kafka_2.10-0.10.0.1.tgz
* Extract Kafka, time to modify some configs
* Inside Kafka extraction you can find .\config\server.properties
* In .\config\server.properties replace log.dirs=c:/kafka/kafka-logs
* Note: Make sure to create those folders in relevant paths
* Happy news: Now Kafka ships with windows .bat scripts, You can find these files inside ./bin/windows folder
* Start powershell/cmd and run this command to start Kafka broker  .\bin\windows\kafka-server-start.bat .\config\server.properties
* DONE!, Now you have a running Zookeeper instance and a Kafka broker.


**Zookeepr install**
https://ihong5.wordpress.com/2014/07/07/how-to-install-and-setup-apache-zookeeper-standalone-windows/