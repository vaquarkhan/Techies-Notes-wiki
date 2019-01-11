**Spark Core: sc.textFile vs sc.WholeTextFiles**

While loading a RDD from source data, there are two choices which look similar.

    val text= sc.textFile("hdfs://aa1/data/src_data/stage/test_files/collection_vk/current_snapshot/*")
  
    val text1= sc.wholeTextFiles("hdfs://aa1/data/src_data/stage/test_files/collection_vk/current_snapshot/*")

**sc.textFile**
SparkContext’s TextFile method, i.e., sc.textFile in Spark Shell, creates a RDD with each line as an element. If there are 10 files in current_snapshotfolder, 10 partitions will be created. You can verify the number of partitions by:

     text.partitions.length

textFile(String path, int minPartitions)
Read a text file from HDFS, a local file system (available on all nodes), or any Hadoop-supported file system URI, and return it as an RDD of Strings.

**sc.wholeTextFiles**

SparkContext’s whole text files method, i.e., sc.wholeTextFiles in Spark Shell, creates a PairRDD with the key being the file name with a path. It’s a full path like “hdfs://aa1/data/src_data/stage/test_files/collection_vk/current_snapshot/*”. The value is the whole content of file in String. Here the number of partitions will be 1 or more depending upon how many executor cores you have.

wholeTextFiles(String path, int minPartitions)
Read a directory of text files from HDFS, a local file system (available on all nodes), or any Hadoop-supported file system URI.