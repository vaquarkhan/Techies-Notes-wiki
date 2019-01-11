Problems

Problem 1: The following is data of few users. It cotains four columns (userid, date, item1 and item2). Find each user's latest day's records?

Input

val data="""
user date      item1 item2
1    2015-12-01 14  5.6
1    2015-12-01 10  0.6
1    2015-12-02 8   9.4
1    2015-12-02 90  1.3
2    2015-12-01 30  0.3
2    2015-12-01 89  1.2
2    2015-12-30 70  1.9
2    2015-12-31 20  2.5
3    2015-12-01 19  9.3
3    2015-12-01 40  2.3
3    2015-12-02 13  1.4
3    2015-12-02 50  1.0
3    2015-12-02 19  7.8
"""
Expected output

For user 1, the latest date is 2015-12-02 and he has two records for that particular date.
For user 2, the latest date is 2015-12-31 and he has two records for that particular date.
For user 3, the latest date is 2015-12-02 and he has three records for that particular date.
Problem 2: From the tweet data set here, find the following (This is my own solution version of excellent article: Getting started with Spark in practice)

all the tweets by user
how many tweets each user has
all the persons mentioned on tweets
Count how many times each person is mentioned
Find the 10 most mentioned persons
Find all the hashtags mentioned on a tweet
Count how many times each hashtag is mentioned
Find the 10 most popular Hashtags
Input sample

{
   "id":"572692378957430785",
   "user":"Srkian_nishu :)",
   "text":"@always_nidhi @YouTube no i dnt understand bt i loved the music nd their dance awesome all the song of this mve is rocking",
   "place":"Orissa",
   "country":"India"
}
{
   "id":"572575240615796737",
   "user":"TagineDiningGlobal",
   "text":"@OnlyDancers Bellydancing this Friday with live music call 646-373-6265 http://t.co/BxZLdiIVM0",
   "place":"Manhattan",
   "country":"United States"
}
Problem 3: Demonstrate data virtualization capabilities of SparkSQL by joining data across different data stores i.e. rdbms, parquet, avro



Solutions

Please create a pull request to provide more elegant solutions. Specify the improvements.
Solution 1

//Step 1. Prepare dataset

val input = """user date      item1 item2
1    2015-12-01 14  5.6
1    2015-12-01 10  0.6
1    2015-12-02 8   9.4
1    2015-12-02 90  1.3
2    2015-12-01 30  0.3
2    2015-12-01 89  1.2
2    2015-12-30 70  1.9
2    2015-12-31 20  2.5
3    2015-12-01 19  9.3
3    2015-12-01 40  2.3
3    2015-12-02 13  1.4
3    2015-12-02 50  1.0
3    2015-12-02 19  7.8
"""

val inputLines=sc.parallelize(input.split("\\r?\\n"))
//filter the header row
val data=inputLines.filter(l=> !l.startsWith("user") )
data.foreach(println)

//Step 2. Find the latest date of each user

val keyByUser=data.map(line => { val a = line.split("\\s+"); ( a(0), line ) })
//For each user, find his latest date
val latestByUser = keyByUser.reduceByKey( (x,y) => if(x.split("\\s+")(1) > y.split("\\s+")(1)) x else y )
latestByUser.foreach(println)

//Step 3. Join the original data with the latest date to get the result

val latestKeyedByUserAndDate = latestByUser.map( x => (x._1 + ":"+x._2.split("\\s+")(1), x._2))
val originalKeyedByUserAndDate = data.map(line => { val a = line.split("\\s+"); ( a(0) +":"+a(1), line ) })
val result=latestKeyedByUserAndDate.join(originalKeyedByUserAndDate)
result.foreach(println)

//Step 4. Transform the result into the format you desire

def createCombiner(v:(String,String)):List[(String,String)] = List[(String,String)](v)
def mergeValue(acc:List[(String,String)], value:(String,String)) : List[(String,String)] = value :: acc
def mergeCombiners(acc1:List[(String,String)], acc2:List[(String,String)]) : List[(String,String)] = acc2 ::: acc1
//use combineByKey
val transformedResult=result.mapValues(l=> { val a=l._2.split(" +"); (a(2),a(3)) } ).combineByKey(createCombiner,mergeValue,mergeCombiners)
transformedResult.foreach(println)
Solution 2

Setup
A single tweet looks as below
{
   "id":"572692378957430785",
   "user":"Srkian_nishu :)",
   "text":"@always_nidhi @YouTube no i dnt understand bt i loved the music nd their dance awesome all the song of this mve is rocking",
   "place":"Orissa",
   "country":"India"
}
Let us create a case class to represent each tweet
case class Tweet(id:String, user:String, text:String, place:String, country:String)
Transform the file into this RDD[Tweet].
For this we use Jackson library which comes bundled with Apache Spark already. We will not use object binding feature (mapper.readValue()) due to this issue. Instead, we will use TreeModel API (mapper.readTree)
/**
    Create a holder object with a transient lazy field to hold the ObjectMapper. 
    This way, the ObjectMapper will be instantiated at the destination and we can share the instance.

    NOTE: This is just to show the functionality. Ideally we would like to use mapPartitions
**/
object Holder extends Serializable {
  import com.fasterxml.jackson.databind.{DeserializationFeature, ObjectMapper}
  import com.fasterxml.jackson.module.scala.DefaultScalaModule

  @transient lazy val mapper = new ObjectMapper()
  mapper.registerModule(DefaultScalaModule)
  mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
}

val tweets=sc.textFile("file:///c:/_home/reduced-tweets.json").map( line => {
  val t = Holder.mapper.readTree(line)
  Tweet(t.path("id").asText, t.path("user").asText, t.path("text").asText, t.path("place").asText, t.path("country").asText)
})
all the tweets by user

how many tweets each user has

all the persons mentioned on tweets

Count how many times each person is mentioned

Find the 10 most mentioned persons

Find all the hashtags mentioned on a tweet

Count how many times each hashtag is mentioned

Find the 10 most popular Hashtags
