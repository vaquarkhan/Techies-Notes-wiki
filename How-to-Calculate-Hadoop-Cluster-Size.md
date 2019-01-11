This is a formula to estimate Hadoop storage (H): 

H=c*r*S/(1-i) 

where: 
* c = average compression ratio. It depends on the type of compression used (Snappy, LZOP, ...) and size of the data. When no compression is used, c=1. 

* r = replication factor. It is usually 3 in a production cluster. 

* S = size of data to be moved to Hadoop. This could be a combination of historical data and incremental data. The incremental data can be daily for example and projected over a period of time (3 years for example). 

* i = intermediate factor. It is usually 1/3 or 1/4. Hadoop's working space dedicated to storing intermediate results of Map phases. 

Example: With no compression i.e. c=1, a replication factor of 3, an intermediate factor of .25=1/4 
H= 1*3*S/(1-1/4)=3*S/(3/4)=4*S 
With the assumptions above, the Hadoop storage is estimated to be 4 times the size of the initial data size. 

**2. This is the formula to estimate the number of data nodes (n): **
n= H/d = c*r*S/(1-i)*d 

where d= disk space available per node. All other parameters remain the same as in 1. 

Example: If 8TB is the available disk space per node (10 disks with 1 TB , 2 disk for operating system etc were excluded.). Assuming initial data size is 600 TB. n= 600/8=75 data nodes needed


------------------------------------------------------------------------------------------------------------

* http://hadoopmag.com/how-to-plan-capacity-for-hadoop-cluster/

* https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_cluster-planning-guide/bk_cluster-planning-guide-20150721.pdf


* http://eprints.bournemouth.ac.uk/22861/1/Scaling_beyond_one_rack_and_sizing_of_Hadoop_platform.pdf

* http://files.cloudera.com/pdf/Dell_Cloudera_Solution_for_Apache_Hadoop_Reference_Architecture.pdf