https://gist.github.com/rajkrrsingh/47fd073674eb5e2977c017acdb538d23


Example:


`package com.vk.example1`

`import org.apache.spark.SparkContext`
`import org.apache.log4j._`
`import org.apache.spark.sql.SQLContext`
`import org.apache.spark.sql.functions._`
`import org.apache.spark.sql.types.IntegerType`
`import org.apache.spark.sql.types.StringType`


`object Test11 {`
  
  `def main(rgs:Array[String]){`
`//`
    `Logger.getLogger("org").setLevel(Level.ERROR)`
    
    `val sc = new SparkContext("local[*]","Test10")`
    
   
    `val sqlContext = new SQLContext(sc)`
    `val input = sqlContext.read.text("../ScalaSparkProject/src/ml-100k/u.data")`
    `//input.show()`
  
   `//----------------------------------------------------------------------------------------------------------------------`
  `// Split logic 1`
  `//----------------------------------------------------------------------------------------------------------------------`
    
   `val movieDF= input.withColumn("UserId", split(col("value"), "\\W+").getItem(0).cast(IntegerType))`
                     `.withColumn("MovieId", split(col("value"), "\\W+").getItem(1).cast(IntegerType))`
                     `.withColumn("Rating", split(col("value"), "\\W+").getItem(2).cast(IntegerType))`
                     `.withColumn("Timestamp", split(col("value"), "\\W+").getItem(3).cast(IntegerType))`
                     `.drop("value")`

  
   `movieDF.show()`
   
   `movieDF.printSchema()`
  `//----------------------------------------------------------------------------------------------------------------------`
  `// Split logic 2`
  `//----------------------------------------------------------------------------------------------------------------------`
    `import sqlContext.implicits._`
   
    `val movieDF1= input.withColumn("value", split($"value", "\\W+")).select(`
                       `$"value".getItem(0).as("UserId"),`
                       `$"value".getItem(1).as("MovieId"),`
                       `$"value".getItem(2).as("Rating"),`
                       `$"value".getItem(3).as("Timestamp")`
`).drop("value")`

`//movieDF1.show()`
`//----------------------------------------------------------------------------------------------------------------------  `
     
`val MovieIdDF=movieDF.select($"Rating",$"MovieId").groupBy("MovieId").count()`

`val newMovieIdDF=MovieIdDF.sort($"count".desc)`

`println("----------------------------------Most populer Movie id-----------------------------------------------------") `

`newMovieIdDF.show()`
 
`println("----------------------------------Most populer Movie name-----------------------------------------------------") `

`//loading file name file`

 `val movieName = sqlContext.read.text("../ScalaSparkProject/src/ml-100k/u.item")`
 
  `val movieNameDF= movieName.withColumn("id", split(col("value"), "\\|").getItem(0).cast(IntegerType))`
                     `.withColumn("name", split(col("value"), "\\|").getItem(1).cast(StringType))`
                     `.drop("value")`
 
 `movieNameDF.show(50,false)`
 `//brodcast `
`val brodcastMovieDF= sc.broadcast(movieNameDF)`
 
 `println("----------------------------------join-----------------------------------------------------") `
 `import org.apache.spark.sql.functions.broadcast`

 
`val finalDF= movieDF.join(brodcastMovieDF.value,movieDF.col("MovieId")===movieNameDF.col("id"))`

`val printFinalDF=finalDF.select($"Rating",$"MovieId",$"name")`
`printFinalDF.show(120,false)`

  `}`
  
`}`