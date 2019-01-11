* https://databricks.com/blog/2015/03/20/using-mongodb-with-spark.html
* https://github.com/mongodb/mongo-spark
* https://docs.mongodb.com/spark-connector/v1.1/getting-started/
* http://www.slideshare.net/NorbertoLeite/spark-and-mongodb
* https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=17&cad=rja&uact=8&ved=0ahUKEwip6IuHp7HRAhUD24MKHTovD8w4ChAWCEcwBg&url=http%3A%2F%2Ftngconsultores.com%2Fkw%2Fpluginfile.php%2F53%2Fmod_glossary%2Fattachment%2F825%2Fmongodb_spark.pdf&usg=AFQjCNGh2sTjYyRfI_XxJOG5hzTMWOlTLw&sig2=TeJ6GVHgQI61Ae-CCg7crQ
* https://www.mongodb.com/blog/post/tutorial-for-operationalizing-spark-with-mongodb
* http://www.focusedsupport.com/blog/analytics-using-spark-and-mongo/



**Reading mongodump bson file from Spark in scala using mongo-hadoop**

I couldn't find a complete Scala version using mongo-hadoop v1.3.1 to read a mongodump bson file, so here's one I prepared earlier: 

      val bsonData = sc.newAPIHadoopFile("file:///your/file.bson",  classOf[com.mongodb.hadoop.BSONFileInputFormat].asSubclass(classOf[org.apache.hadoop.mapreduce.lib.input.FileInputFormat[Object, org.bson.BSONObject]]),
classOf[Object], 
classOf[org.bson.BSONObject])

 
Note that (for v1.3.1) we need to subclass com.mongodb.hadoop.BSONFileInputFormat to avoid this compilation error: "inferred type arguments do not conform to method newAPIHadoopFile's type parameter bounds".  This isn't required if reading from Mongo directly using com.mongodb.hadoop.MongoInputFormat.

Also, you can pass a Configuration object as a final parameter if you need to set any specific conf values.

For more bson examples see here: https://github.com/mongodb/mongo-hadoop/blob/master/BSON_README.md

For Java examples see here: http://crcsmnky.github.io/2014/07/13/mongodb-spark-input/
