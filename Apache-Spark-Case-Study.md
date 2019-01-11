* http://hortonworks.com/blog/spark-data-science-case-study/
* http://projects.spring.io/spring-xd/
* http://sparkbigdata.com/tutorials/104-case-studies
* http://events.linuxfoundation.org/sites/events/files/slides/Boost%20real%20world%20appliations%20using%20Apache%20Spark_0.pdf
* https://aws.amazon.com/emr/details/spark/
* https://azure.microsoft.com/en-us/documentation/articles/hdinsight-apache-spark-eventhub-streaming/
* http://www.codeproject.com/Articles/1023037/Introduction-to-Apache-Spark


This piece of writing will present a real-life study case of Hadoop or Spark used in a unique approach to process large datasets such as Big Data.

     According to Spark.apache.org, (n.d.), Spark is a fast and general processing engine compatible with Hadoop data. It can run in Hadoop clusters through YARN or Spark's standalone mode. It is designed to perform both batch processing (similar to MapReduce) and new workloads like streaming, interactive queries, and machine learning.

 I. Case studies

      A Web search displays a real-life case study of “Apache Spark for Deep Learning: Two Case Studies” by Riggins (2016).

     Hadoop MapReduce and Apache Spark are two popular analytics software tools used by many companies. Both tools can complement each other or work together. For example, Spark can work on Hadoop Distributed File System (HDFS). Spark’s applications can run much 100 times faster than these run on Hadoop MapReduce because Spark uses RAM (in-memory) while Hadoop runs on the hard disk. Hadoop MapReduce has a more flexible, wide-ranged options but Spark can convert a big chunk of data into actionable information faster.

Trovit jumps from Hadoop MapReduce to Apache Spark
     Trovit is a classified ads engine uses HDFS by using many smaller servers to solve the storage problem with the huge amounts of data. However, when using Hadoop MapReduce on HDFS, developers and users experience the inflexible application programming interface or API and strictly on disk activities. Apache Spark offers a flexible and fast distributed processing framework. Developers can run MapReduce code in production on the Spark platform at high speed and ease of use. Trovit team and Spark innovates a set of libraries on top of the framework for rapid processing that is suitable on their resources. Today, Trovit uses both Hadoop and Spark combo in renewed flexibility in the language of the data, and the ability of parallel processing for effectivity and efficiency.

Faster distributed deep learning with Spark on AWS
     In the conference (PAPIs.io, 2016) on machine learning, Steenbergen (2016) presented the possibilities of distributed deep learning which can be image analysis, image generation, most famous, learning and playing Go by distributing training, computation over a Spark cluster. Spark allows users to next level of big data processing, innovating data science. Amazon Web Services’ EC2 has GPU instances that users can span on-demand for a dollar per spot instance about two to three times cheaper than other instances. A deep learning framework is Berkeley’s Caffe or Flickr. Users can run an existing cluster alongside other Spark jobs. Spark allows users to train multiple models at once and even leverage existing models in deep learning with Spark.

 II. Was Hadoop or Spark used in the amplest manner?

     With Big Data’s challenges of five V’s (Volume, Variety, Velocity, Veracity, and Value) (Goes, 2014), Hadoop, particularly Apache Spark, has gained popular demand in analytics. Along with other analytics software such as XML technology and R language, Hadoop and Spark have competed and complemented each other among many organizations. With all features of high speed, ease to use, generality, and ubiquity, Apache Spark, that

was built by more than 1000 developers from over 200 companies in 2009, seems to have the edge of dominance in processing large datasets in Hadoop applications (Spark.apache.org. n.d.). A wide range of organizations uses Spark as a primary analytics tool in Big Data. However, Hadoop, especially Spark, does not reach the amplest uses among organizations and enterprises because other analytics tools such as R Project and XML are also in the game of big data analytics (BDA). Notice that R is a powerful BDA tool (Minelli, Chambers, & Dhiraj, 2013). Perhaps, BDA users may know which analytics software tool (e.g., R or Spark) will be the winner of the most use in BDA in the amplest manner in the next five years.

 III. What benefits (if any) did Hadoop or Spark offer the industry?

     Hadoop, particularly Apache Spark, has many features that offer benefits to organizations in processing Big Data in the industries such as healthcare, banking, insurance education, security, safety, etc. (Kharbanda, 2015; Bharadwaj, 2015).

     - Spark has a high speed of execution in the analysis of huge datasets by using in-memory cache computing.

     - Spark offers ease of use with more than 80 high-level operators.

     - Hadoop and Spark have a set of libraries that can be built on top of existing libraries for parallel applications.

     - Hadoop and Spark can run everywhere. Even R code can run on Hadoop ecosystem.

     - Users can create their own functions to meet their needs in Spark for extensibility.

     - Hadoop and Spark that have a feature of automatic optimization leave developers to focus on semantics in their work.

     - Both Hadoop and Spark use the same platform for real-time and batch processing.

     - Apache streaming is suitable for many real-time analytics use cases.

     - It is highly configurable. Users can make it work well.

      Notice that a couple of disadvantages of Apache Spark are:

     - Spark consumes a lot of in-memory/cache at a high cost.

     - Spark is still an infant stage of development. It has more bugs than Hadoop does.

     In summary, Hadoop and Spark can store and process the large data sets. Two case studies were presented. It appeared that Hadoop and Spark have not reached the amplest use in industry, but they have the potential to become a winner in popular Hadoop applications. The features of Hadoop, particularly Spark, provided good benefits in BDA for many organizations. 

REFERENCES

Apache Software Foundation (2014). What is apache hadoop?  Retrieved November 08, 2015 from http://hadoop.apache.org/

Bharadwaj, S. (2015). What is the difference between Apache Spark and Apache Hadoop (Map-Reduce)? Retrieved July 18, 2016 from https://www.quora.com/What-is-the-difference-between-Apache-Spark-and-Apache-Hadoop-Map-Reduce.

Goes, P. B. (2014). Big Data and IS Research. MIS Quarterly, 38(3), iii-viii.

Kharbanda, A. (2015). What are the pros and cons of Apache Spark? Retrieved July 18, 2016 from https://www.quora.com/What-are-the-pros-and-cons-of-Apache-Spark

Minelli, M., Chambers, M., & Dhiraj, A. (2013). Big data, big analytics: emerging business intelligence and analytic trends for today's businesses. John Wiley & Sons.

Riggins, J. (2016). Apache spark for deep learning: two case studies. Retrieved July 18, 2016 from http://thenewstack.io/two-tales-big-data-framework-adoption-apache-spark/

Spark.apache.org. (n.d.). Lightning-fast cluster computing. Retrieved July 18, 2016 from http:// http://spark.apache.org/.

Steenbergen, V. (2016). Speed-up distributed deep learning with Spark on AWS. Retrieved July 18, 2016 from http://www.papis.io/connect-valencia-2016/talks/speed-up-distributed-deep-learning-with-spark-on-aws-vincent-van-steenbergen.

