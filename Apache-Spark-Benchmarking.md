https://github.com/apache/spark/tree/master/sql/core/src/test/scala/org/apache/spark/sql/execution/benchmark

https://yahooeng.tumblr.com/post/135321837876/benchmarking-streaming-computation-engines-at



https://www.dezyre.com/article/apache-flink-vs-spark-will-one-overtake-the-other/282


1. The  talk by Dongwon Kim from POSTECH at the Flink Forward conference on October 13, 23015 provides the most up-to-date performance of Flink vs. Spark vs. Tez: A comparative performance evaluation of Flink, Dongwon Kim, POSTECH.  http://www.slideshare.net/FlinkForward/dongwon-kim-a-comparative-performance-evaluation-of-flink

2. Apache Flink outperforms Apache Spark in processing machine learning & graph algorithms and relational queries but not in batch processing! The results were published in the proceedings of the 18th International Conference, Business Information Systems 2015, Poznań, Poland, June 24-26, 2015. Thanks to our friend Google, Chapter 3: Evaluating New Approaches of Big Data Analytics Frameworks by Norman Spangenberg, Martin Roth and Bogdan Franczyk is available for preview at http://goo.gl/WocQci  on pages 28-37. 

3.  Some first numbers on performance of streaming jobs with Apache Flink here from the Apache Software Foundation: http://flink.apache.org/features.html , lots more are here: http://data-artisans.com/high-throughput-low-latency-and-exactly-once-stream-processing-with-apache-flink/

4. TeraSort on Hadoop Map Reduce, Tez, Apache Spark and Apache Flink. Guess who had best performance? ApacheFlink! http://goo.gl/yBS6ZC 

5. Hash join on Tez, Spark, and Flink. Guess who had best performance? ApacheFlink! http://goo.gl/a0d6RR

6. Grep log for 3 terms in a 25 node cluster while scaling data size from 100GB to 1 TB Flink was faster with its more efficient pipelining http://de.slideshare.net/KostasTzoumas/apache-flink-api-runtime-and-project-roadmap/35

7.  There is some work started by some Flink contributors to create some performance scripts for Flink, Spark, and MapReduce here: There is Apache Flink: Performance and Testing  No results published yet. 

8. Big Data Benchmark: Geotemporal Clustering  http://blog.mgm-tp.com/2016/01/big-data-benchmark-geotemporal-clustering/ Matthias Kricke and Paul Röwer, January 15, 2016

9. Iterative processing http://de.slideshare.net/KostasTzoumas/apache-flink-api-runtime-and-project-roadmap/42 Flink is faster than Spark for iterative processing. 

10. The institute of Computing Technology, Chinese Academy of Sciences released a benchmark for big data systems called BigDataBench, which has become an industry-standard big data benchmark in China. http://prof.ict.ac.cn/BigDataBench/

They are planning to support Flink in their benchmark! This could provide a set of workloads on different domains and an objective comparison with systems such as Spark and Hadoop.

11. 'Plataformas de Big Data: Spark, Storm e Flink' Article in Portuguese by Jean Bez from Universidade Federal do Rio Grande do Sul  published on July 15, 2015 shows 2 examples in which Flink outperforms Spark by  factor of 2 and 7 respectively. https://www.researchgate.net/publication/280052790_Plataformas_de_Big_Data_Spark_Storm_e_Flink

12. Big Data Performance Engineering Examples from Hadoop, Pig, HBase, Flink and Spark  http://www.bigsynapse.com/addressing-big-data-performance Posted on August 16, 2015 by Sameer Wadkar

13. Benchmarking Streaming Computation Engines at Yahoo!   http://yahooeng.tumblr.com/post/135321837876/benchmarking-streaming-computation-engines-at  December 16, 2015 Code at github: https://github.com/yahoo/streaming-benchmarks

14. Evaluating cloud frameworks on genomic applications http://ieeexplore.ieee.org/xpl/login.jsp?tp=&arnumber=7363756
Michele Bertoni, Stefano Ceri, Abdurrahman Kaitoua, Pietro Pinoli 2015 IEEE International Conference on Big Data, 193-202

15. Batch processing comparison - Apache Spark vs. Apache Flink  Apache Flink vs Apache Spark - Reproducible experiments on cloud  Slides: http://www.slideshare.net/shelan1/apache-flink-vs-apache-spark-reproducible-experiments-on-cloud Blog: http://shelan.org/blog/2016/01/31/reproducible-experiment-to-compare-apache-spark-and-apache-flink-batch-processing/ Report: http://www.scribd.com/doc/297923938/Apache-Spark-vs-Apache-Flink-Reproducible-Experiments-on-cloud

16. Spark versus Flink: Understanding Performance in Big Data Analytics Frameworks   https://hal.inria.fr/hal-01347638/file/clusterFS.pdf

17.  Apache Flink: Distributed Stream Data Processing, K.M.J. Jacobs, CERN, Geneva, Switzerland. https://cds.cern.ch/record/2208322/files/report.pdf

18. Functional Comparison and Performance Evaluation of Streaming Frameworks. October 6, 2016 http://www.slideshare.net/HuafengWang/functional-comparison-and-performance-evaluation-of-streaming-frameworks.