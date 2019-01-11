**Spark GraphX to detect influence in corporate networks**

**Getting the Data**
http://www.theyrule.net/

Data is an effort to provide some public transparency as to the list of powerful board members.

Disclaimer: This is very old data ( from 2004 ) , which is stale and it serves hereby just as illustration. However It can be easily applied and adapted to any hierarchical social network. The data comes as SQL inserts and describing how to create or import into database is out of scope. After importing into MySQL the schemas look like that:


`mysql> describe us2004_companies;`

   `+-------------+--------------+------+-----+---------+----------------+`
   `| Field       | Type         | Null | Key | Default | Extra          |`
   `+-------------+--------------+------+-----+---------+----------------+`
   `| id          | int(11)      | NO   | PRI | NULL    | auto_increment |`
   `| companyname | varchar(128) | NO   |     |         |                |`
   `| bod         | varchar(255) | NO   |     |         |                |`
   `| url         | varchar(255) | NO   |     |         |                |`
   `| objects     | mediumtext   | NO   |     | NULL    |                |`
   `| BOD_URL     | varchar(255) | NO   |     |         |                |`
   `| symbol      | varchar(50)  | NO   |     |         |                |`
    `+-------------+--------------+------+-----+---------+----------------+`

`7 rows in set (0.01 sec)`

`mysql> describe us2004_directors;`

`+--------------+--------------+------+-----+---------+----------------+`
`| Field        | Type         | Null | Key | Default | Extra          |`
`+--------------+--------------+------+-----+---------+----------------+`
`| id           | int(11)      | NO   | PRI | NULL    | auto_increment |`
`| firstname    | varchar(128) | NO   |     |         |                |`
`| lastname     | varchar(128) | NO   |     |         |                |`
`| gender       | char(1)      | NO   |     | m       |                |`
`| boards       | varchar(255) | NO   |     |         |                |`
`| url          | varchar(255) | NO   |     |         |                |`
`| institutions | varchar(255) | NO   |     |         |                |`
`+--------------+--------------+------+-----+---------+----------------+`

`7 rows in set (0.00 sec)`


** Loading MySQL data into Spark Dataframes**
 
Start Spark Shell.Make sure that you include the relevant JDBC driver in the classpath.In this case.I had used mysql jdbc driver: 

           $SPARK_HOME/bin/spark-shell --jars mysql-connector-java-5.1.27.jar 

After your spark shell start import the following: 


`import org.apache.spark.graphx._`
`import org.apache.spark.rdd.RDD`
`import scala.util.MurmurHash`
`import org.apache.spark.{SparkConf, SparkContext}`

`val sqlContext = new org.apache.spark.sql.SQLContext(sc)`
`import sqlContext.implicits._ `
Next we create DataFrame using jdbc specifics as defined in Spark Scala API: 

`def jdbc(url: String, table: String, predicates: Array[String],`

connectionProperties: Properties): DataFrame
Construct a DataFrame representing the database table accessible
via JDBC URL url named table using connection properties.

The best way is to pass a Map for each table with necessary connection details as follows: 

val compMap = Map ("url" ->"jdbc:mysql://localhost:3306/sec",
`"driver"->"com.mysql.jdbc.Driver",`
`"dbtable"->"us2004_companies" , `
`"user" ->"DBUSER" , `
`"password"->"MYSQL PSWD")`
`val compDF=sqlContext.read.format("jdbc").options(compMap).load()`

Out of the created Dataframe we need to select only columns of interest: 

`val comps=compDF.select(compDF("id"),`
`compDF("companyname"),compDF("bod"))`
`The result is `
`comps: org.apache.spark.sql.DataFrame = `
`[id: int, companyname: string, bod: string]`


**Transforming the Data**

When working with dataframes it is good idea to create custom classes to deal with data transformation and manipulation. In this case I had created two such classes: Director and Company and three auxiliary methods to facilitate mapping. Here Murmurhash library is used to generate unique integer out of the provided String.


**case class Company **

`(companyid:Int,companyName:String,boardMember:Int)`
`case class Director (id:Int,Name:String,memberOf:Int)`
`def toCompany(id:Int,name:String,bod:String):Company={Company(id.toInt,name,bod.toInt)}`
`def toDirector(id:Int,name:String,bod:String):Director={Director(id.toInt,name,bod.trim.toInt)}`
`def toEdge(name:String,company:String):Edge[Int]={ Edge( MurmurHash.stringHash( company), `
`MurmurHash.stringHash( name) , MurmurHash.stringHash( name) ) }`


After that we apply chain of transformations to the Dataframe comps: 

`val compRDD1=comps.map(t=>`
`(t.getAs[Int]("id"),`
`t.getAs[String]("companyname"),t.getAs[String]("bod")`
`.split(",")))`
`//compRDD1: org.apache.spark.rdd.RDD[(Int, String, Array[String])]`

`val compMapDF=compRDD1.flatMap(comp=>`
`{comp._3.map(toCompany(comp._1,comp._2,_))})`
`.toDF`
`//"flatMap" creates compMapDF: org.apache.spark.rdd.RDD[Company]`
`//and "toDF" creates:`
`//compMapDF: org.apache.spark.sql.DataFrame: `
`//[companyid: int, companyName: string, boardMember: int]`


What is going on in the above code? We need to be aware of several things when delaing with dataframes. One is that anytime we use "map/flatMap" , the transformation returns an RDD (Returns a new RDD by applying a function to all rows of this DataFrame.) and therfore the need to use toDF call to transfer back to DataFrame. Another is the way access fields/columns of DataFrame : DataFrame consists of spark.sql.Row object and when accessing it we need to explicitly convert it to the expected type. We do the same steps for the us2004_directors table 

`val options = Map ("url" ->"jdbc:mysql://localhost:3306/sec",`
`"driver"->"com.mysql.jdbc.Driver",`
`"dbtable"->"us2004_directors" ,`
`"user" ->"USER" ,`
`"password"->"PSWD")`
`val dirDF=sqlContext.read.format("jdbc").options(options).load()`
`val dirnames=dirDF.select(dirDF("id"),dirDF("firstname"),`
`dirDF("lastname"),dirDF("boards"))`
`val directorsRDD =dirnames.map(t=>(t.getAs[Int]("id"),`
`t.getAs[String]("lastname")+" "+t.getAs[String]("firstname"),`
`t.getAs[String]("boards").split(",") )  )`
`.filter(_._3.length>1)`
`val dirMapDF=directorsRDD.`
`flatMap(dir=>{dir._3.map(toDirector(dir._1,dir._2,_))})`
`.toDF`


After we had created the above dataframes, we will register them as tables so that we can run queries on them. First we will combine data using inner join in a very similiar fashion as a typical SQL join query as follows:

`compMapDF.registerTempTable("companies")`
`dirMapDF.registerTempTable("directors")`
`val joined = `
`sqlContext.sql("SELECT distinct Name ,companyName,memberOf `
`FROM companies c ,  directors d `
`where d.memberOf=c.companyid")`
`We will also need to do some cleanup and remove duplicates so that we work with clean data: `
`val companiessdistinct=joined.select(joined("companyName")).distinct`
`val namesdistinct=joined.select(joined("Name")).distinct`
`val edges=joined.map(rec=>`
`(toEdge(rec.getAs[String]("Name") , `
`rec.getAs[String]("companyName") ) ) )`


In GraphX each Node is represented by unique numerical value - VertexId, followed by some other tuple data, typically String.Here we need to convert the above into unique numbers so that we can represent them as VertexRDDs. We utilize Scala's MurmurHash library to do that, as follows:

`val dirVertices:RDD[(VertexId, String)]=`
`namesdistinct.map(`
`v=>`
`(MurmurHash.stringHash(v.getAs[String]("Name")),`
`v.getAs[String]("Name")))`

`val compVertices:RDD[(VertexId, String)]=`
`companiessdistinct.map(`
`v=>`
`(MurmurHash.stringHash(v.getAs[String]("companyName")),`
`v.getAs[String]("companyName")))`

Next we combine them so that we have all vertices: 

       val allVertices=dirVertices.union(compVertices)

Now we are ready to build the graph. In Spark Graph constructor takes some default vertex which has to match the Tuple type of other vertices ( in our case String): 

`val default= ("NoName")`
`val graph = Graph(allVertices,edges, default)`
`graph.persist()`

Now we can run some analyticss and obtain individuals with many incoming connection - i.e "popularity" : 
val influential=graph.inDegrees.join(allVertices).

`sortBy(_._2._1, ascending=false).`
`take(1)`
`println("POPULAR:"+influential.mkString(","))`
`The result is something like: `
`influential:Array[(org.apache.spark.graphx.VertexId,(Int, String))]=`
`Array((56914485,(8,Gray, III William H.)))`
`POPULAR:(56914485,(8,Gray, III William H.))`


However popularity does not always mean influence. To measure real impact or clout of Node in a connected Graph, two brainy guys - Larry Page and Sergey Brin back in the day came up with an algorithm called "PageRank". We can apply the mechanism of PafeRank to find which board member has the biggest "clout" based on calculated PageRank score. To use PageRank in Spark one need to specify tolerance which actually represents the convergence. We had used "0.0001"

val ranks = graph.pageRank(0.0001).vertices
This calculation might take a while depending on how big the graph is. Once the result is obtained we can use it to join with the population of all vertices and sort in descending order to obtain the "biggest" influencer: 

`val ranksDirectors = ranks.join(allVertices).`
`sortBy(_._2._1,ascending=false).map(_._2._2)`
`scala> ranksDirectors.take(1)`
`res5: Array[String] = Array(Ward Jackie M.)`
