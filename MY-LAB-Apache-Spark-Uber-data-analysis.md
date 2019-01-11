First, we import the packages needed for Spark ML K-means and SQL.       
              
              import org.apache.spark._
              import org.apache.spark.sql.SQLContext
              import org.apache.spark.sql.functions._
              import org.apache.spark.sql.types._
              import org.apache.spark.sql._
              import org.apache.spark.ml.feature.VectorAssembler
              import org.apache.spark.ml.clustering.KMeans


We specify the schema with a Spark Structype (Please note that if you are using a notebook, then you do not have to create the SQLContext).


             val SQLContext = new SQLContext(sc)

             import sqlContext.implicits._
             import sqlContext._



            val schema = StructType(Array(
                           | StructField("dt",TimestampType,true),
                           | StructField("lat",DoubleType,true),
                           | StructField("lon",DoubleType,true),
                           | StructField("base",StringType,true)
                          | ))



Using Spark 2.0 and --packages com.databricks:spark-csv_2.10:1.5.0, we create a DataFrame from a CSV file data source and apply the schema. 

![](https://www.mapr.com/sites/default/files/otherpageimages/112816blog/7.png)

Create same in csv file and keep your local.


****Load Data****

                val df=spark.read.format("com.databricks.spark.csv").
                        option("header","false").schema(schema).
                        csv("///home/osboxes/Sparkdatafile/Uber-Jan-Feb-FOIL.csv")

               or


               val df1=spark.read.option("header","false")
                       .schema(schema)
                       .csv("///home/osboxes/Sparkdatafile/Uber-Jan-Feb-FOIL.csv") 



Data :+1: Download "Uber-Jan-Feb-FOIL.csv"

https://github.com/vaquarkhan/uber-tlc-foil-response

Show Schema

                    df.printSchema



DataFrame show() displays the first 20 rows:

                    df.cache
                    df.show


****Define Features Array****

In order for the features to be used by a machine learning algorithm, the features are transformed and put into Feature Vectors, which are vectors of numbers representing the value for each feature. Below, a VectorAssembler is used to transform and return a new DataFrame with all of the feature columns in a vector column.



           val featureCols=Array("lat","lon")
           val assembler = new VectorAssembler().setInputCols(featureCols)//.setOutputCols("features")

           val df2=assembler.transform(df)
           df2.show


![](https://www.mapr.com/sites/default/files/otherpageimages/112816blog/12.png)


Next, we create a KMeans object, set the parameters to define the number of clusters and the maximum number of iterations to determine the clusters, and then we fit the model to the input data.

![](https://www.mapr.com/sites/default/files/otherpageimages/112816blog/15.png)


             val Array(trainingData,testData) = df2.randomSplit(Array(0.7,0.3),5043)

             val kmeans= new KMeans().setK(8).setFeaturesCol("features").setPredictionCol("predication")

             val model = kmeans.fit(df2)

              println("Final Centers")
              model.clusterCenters.foreach(println) 


https://github.com/vaquarkhan/spark-ml-kmeans-uber/tree/master/data

https://www.mapr.com/blog/monitoring-real-time-uber-data-using-spark-machine-learning-streaming-and-kafka-api-part-1

Please save data as uber.csv


``







