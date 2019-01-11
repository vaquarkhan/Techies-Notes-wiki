
- [A Gentle Introduction to Apache Spark on Databricks](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/346304/2168141618055043/484361/latest.html)
- [Apache Spark on Databricks for Data Scientists](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/346304/2168141618055194/484361/latest.html)
- [Apache Spark on Databricks for Data Engineers](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/346304/2168141618055109/484361/latest.html)



* https://www.youtube.com/watch?v=ssPBlqiRJGY


# **The Data Interfaces**

**The Dataset:**
The Dataset is Apache Spark's newest distributed collection and can be considered a combination of DataFrames and RDDs. It provides the typed interface that is available in RDDs while providing a lot of conveniences of DataFrames. It will be the core abstraction going forward.

**The DataFrame:**
The DataFrame is collection of distributed  <code>Row</code> types. These provide a flexible interface and are similar in concept to the DataFrames you may be familiar with in python (pandas) as well as in the R language.

**The RDD (Resilient Distributed Dataset):**
Apache Spark's first abstraction was the RDD or Resilient Distributed Dataset. Essentially it is an interface to a sequence of data objects that consist of one or more types that are located across a variety of machines in a cluster. RDD's can be created in a variety of ways and are the "lowest level" API available to the user. While this is the original data structure made available, new users should focus on Datasets as those will be supersets of the current RDD functionality.


**Transformation :**

![](http://training.databricks.com/databricks_guide/gentle_introduction/trans_and_actions.png)



* https://dzone.com/refcardz/apache-spark

* https://dzone.com/storage/assets/3151-rd204-010d-spark_0.pdf

* http://spark.apache.org/docs/latest/programming-guide.html

* http://stackoverflow.com/questions/32808053/spark-shell-command-lines

* http://blog.brakmic.com/data-science-for-losers-part-3-scala-apache-spark/

* http://alvinalexander.com/scala/analyzing-apache-access-logs-files-spark-scala

* http://www.robertomarchetto.com/spark_java_maven_example

* https://www.cs.duke.edu/courses/fall15/compsci290.1/TA_Material/jungkang/how_to_run_spark_app.pdf

* http://blog.madhukaraphatak.com/pipe-in-spark/

* http://www.openkb.info/2015/01/scala-on-spark-cheatsheet.html

* http://blog.jaceklaskowski.pl/2015/07/20/real-time-data-processing-using-apache-kafka-and-spark-streaming.html

* http://www.markhneedham.com/blog/2014/11/16/spark-parse-csv-file-and-group-by-column-value/

* http://snowplowanalytics.com/blog/2015/06/10/spark-streaming-example-project-0.1.0-released/

* http://eugenezhulenev.com/blog/2014/10/18/run-tests-in-standalone-spark-cluster/

* http://homepage.cs.latrobe.edu.au/zhe/files/SparkAPIMaster.pdf

* http://ampcamp.berkeley.edu/5/exercises/data-exploration-using-spark-sql.html

* https://spark-summit.org/2014/wp-content/uploads/2014/06/Testing-Spark-Best-Practices-Anupama-Shetty-Neil-Marshall.pdf

* https://dzone.com/articles/analytics-with-apache-spark-tutorial-part-2-spark

* https://www.linkedin.com/today/author/0_10raswup_UGlp5PDu0cSIp?trk=prof-sm