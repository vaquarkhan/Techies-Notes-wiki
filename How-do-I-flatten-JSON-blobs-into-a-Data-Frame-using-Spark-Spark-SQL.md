I would like to flatten JSON blobs into a Data Frame using Spark/Spark SQl inside Spark-Shell.

val df = spark.sql("select body from test limit 3"); // body is a json encoded blob column

val df2 = df.select(df("body").cast(StringType).as("body"))



when I do 

df2.show // shows the 3 rows



body

------------------------------------

{"k1": "v1", "k2": "v2" }

{"k3": "v3"}

{"k4": "v4", "k5": "v5", "k6": "v6"}

-------------------------------------


Now say I have billion of these rows/records but at most there will 5 different json schemas for all billion rows. Now how do I flatten such that I get a data frame in the format below? Should I use df.forEach or df.forEachPartition or df.explode or df.flatMap? How do I make sure I am not creating a billion data frames and trying to union all of them or something more inefficient. It will be great if I could see a code sample. Also since this might have Nil I wonder if they would take up any space? I initially tried it with UDF's and I keep getting some exception saying "Any" Type is not supported and so on. So an example on how to do this would help!



"K1" | "K2" | "K3" | "K4" | "K5" | "K6"
---------------------------------------
"V1" | "V2" |
            | "V3" |
                   | "V4" | "V5" | "V6"
Needless to say, V1, V2, V3...V6 can be any type (String, boolean, integer, Map...). Also open to any new ideas.





This seem to work 

    import org.apache.spark.sql._
     val rdd = df2.rdd.map { case Row(j: String) => j }
    spark.read.json(rdd).show()

However I wonder if this any inefficiency here ? since I have to apply this function for billion rows.

Michael Armbrust
Nov 21 (1 day ago)

to kant, user 
In Spark 2.1 we've added a from_json function that I think will do what you want.
