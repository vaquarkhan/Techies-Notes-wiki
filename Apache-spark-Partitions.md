**Properties of Partitions:**

* Partitions never span multiple machines i.e., tuples in the same partition are guaranteed to be on same machine.

* Each machine in the cluster contain one or more partitions

* The number of partitions to use is configurable .By default , it equals the total number of cores on all executor nodes.

**Default no of partitions **

Example if Machine have 4 core and 6 worker node means default no of partitions is 4*6 =24

-------------------------------------------------------

Partitioning is possible only on pair RDD as partition working only key.  



-------------------------------------------------------


          case class CFFPurchase(customerId:Int,destination:String,price:Double)

          val purchases =List(CFFPurchase(100,"Geneva",22.25),
                    CFFPurchase(300,"Zurich",42.25),
                    CFFPurchase(100,"Firebourg",12.40),
                    CFFPurchase(200,"st.Fallen",8.20),
                    CFFPurchase(100,"Lucerne",31.80),
                    CFFPurchase(300,"Basel",16.20))

         val purchasesRDD =sc.parallelize(purchases,3)

         val purchesePerMonth= purchasesRDD.map(p=>(p.customerId,(1,p.price))).reduceByKey((v1,v2)=>
        (v1._1+v2._1,v1._2+v2._2)).collect()
                     

-------------------------------------------------------

Hash partitioning :

Hash partitioning distribute data as er hashcode equlaay distributed.
p=k.hashCode() % numPartitions

Then , all tuples in the same partition p are sent to the achin hosting p

Range Partitioning :

1) an ordering of keys
2) a set of sorted ranges of key

Note : tuples with keys in the same range apper on the same machine 


-------------------------------------------------------
Example -how to choose partition

8, 96, 240,400,101,800 

patition 4

hashCode()  n.hashCode()==n)

p=k.hashCode % numPartition

k%4 


* Partition 0: [8,96,240,400,800]
* Partition 1: [401]
* Partition 2:
* Partition 3:

The results is a very ubalanced distribution which hurts performance.


Now Range Partition

Assumptions:
(a) keys non negative ,
(b) 800 is biggest key in the RDD

Set of ranges :[1,200],[201,400],[401,600],[601,800]

* Partition 0 : [8,96]
* Partition 1: [240,400]
* Partition 2:[401]
* Partition 3:[800]

The resuts of partioning is much more balance

![](https://3.bp.blogspot.com/-TZ13FtDfaGs/WdBHwQSvtbI/AAAAAAAACYA/Bian3-R2Or4ULR2Nzs49LpT09LIv_I4NQCLcBGAs/s320/Untitled.png)



![](https://1.bp.blogspot.com/-kns3W9VSUj0/WdBJGte3ySI/AAAAAAAACYM/wbSrh1TKjJ8a9zU_I0aCbFxN15SxlcLoACLcBGAs/s320/Untitled.png)


![](https://1.bp.blogspot.com/-_oj4ywXkxKk/WdBQW8K-nXI/AAAAAAAACYc/TuOYDRNoBegGHXkA9vRDW0vLwbRKY97AQCLcBGAs/s320/Untitled.png)

-------------------------------------------------------------


* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-Partition

* https://github.com/vaquarkhan/vaquarkhan/wiki/Apache-Spark-handling-of-empty-partitions

* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Spark-Performance-Improving--With-Partitioning

* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-Tuning-and-Debugging

* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-programming-guide-notes-1

* https://github.com/vaquarkhan/vk-wiki-notes/wiki/high-performance-spark---join-and-partition

* https://github.com/vaquarkhan/vaquarkhan/wiki/How-to-calculate-node-and-executors-memory-in-Apache-Spark

* https://github.com/vaquarkhan/vk-wiki-notes/wiki/Apache-Spark-DAG
