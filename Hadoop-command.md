HDFS:
  NameNode
  su hdfs
  /usr/lib/hadoop/bin/hadoop-daemon.sh --config /etc/hadoop/conf/ start namenode
  su mapred
  /usr/lib/hadoop/bin/hadoop-daemon.sh --config /etc/hadoop/conf/ start jobtracker
  
  Secondary NameNode
  /usr/lib/hadoop/bin/hadoop-daemon.sh --config /etc/hadoop/conf/ start secondarynamenode
  
  DataNodes-All
  su - hdfs -c  "/usr/lib/hadoop/bin/hadoop-daemon.sh --config /etc/hadoop/conf/ start datanode"

  hdfs getconf -confKey dfs.client.retry.policy.enabled
  

PIG:
  su hdfs
  pig -l /tmp/pig.log /tmp/id.pig

HIVE/Hcatalog:
  su hive
    nohup /usr/bin/hive --service metastore > /home/hive/logs/hive.out 2> /home/hive/logs/hive.log 
  /usr/lib/hive/bin/hiveserver2 -hiveconf hive.metastore.uris=" " >//home/hive/logs/hiveserver2.out 2> //home/hive/logs/hiveserver2.log & 
  
WEBHCat:
  su hcat
  /usr/lib/hcatalog/sbin/webhcat_server.sh start

WebHDFS:
 http://artemis1.td.teradata.com:50070/webhdfs/v1/user/oozie/share/lib/oozie/json-simple-1.1.jar?op=GETFILESTATUS

Zookeeper: All zk nodes
  su zookeeper
  /usr/lib/zookeeper/bin/zkServer.sh start /etc/zookeeper/conf/zoo.cfg

HBASE: 
  su - hbase -c "/usr/lib/hbase/bin/hbase-daemon.sh --config /etc/hbase/conf start master"
  su - hbase -c "/usr/lib/hbase/bin/hbase-daemon.sh --config /etc/hbase/conf start master"

Oozie:
  su oozie
  /usr/lib/oozie/bin/oozie-start.sh
  Verify
  Server: http://{oozie.full.hostname}:11000/oozie 
  Client: oozie admin -oozie http://$oozie.full.hostname:11000/oozie -status 
  
sqoop:
  sqoop version

Ganglia:
  Server: 
    service httpd restart
    etc/init.d/hdp-gmetad start
    #HDPSlaves
    0|grep GMOND_STARTED
    #HDPNameNode
    curl telnet://localhost:8661|grep GMOND_STARTED
    curl http://localhost:50070/jmx
    #/HDPJobTracker
    curl telnet://localhost:8662|grep GMOND_STARTED
    #HDPHBaseMaster
    curl telnet://localhost:8663|grep GMOND_STARTED
  All:
  /etc/init.d/hdp-gmond start
  Verify:
  http://$my.ganglia.server.hostname/ganglia

Nagios:
  /etc/init.d/nagios start
  Verify:
  http://$nagios.server/nagios

HA:
su - hdfs -c "hdfs haadmin -failover nn2 nn1"

TERASORT:
hdfs dfs -mkdir -p benchmarks/terasort
hadoop jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=72 -Dmapred.reduce.tasks=36  1000000 benchmarks/terasort/input
hadoop jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=72 -Dmapred.reduce.tasks=36 benchmarks/terasort/input benchmarks/terasort/output
hdfs dfs -rm -R -skipTrash benchmarks/terasort
 @vaquarkhan
  
         
Write  Preview

Leave a comment
