**Flight Data Analysis using Spark GraphX**

* https://www.javacodegeeks.com/2016/03/get-started-using-apache-spark-graphx-scala.html

Problem Statement: To analyze Real-Time Flight data using Spark GraphX, provide near real-time computation results and visualize the results using Google Data Studio.

**Use Case – Computations to be done:**

* Compute the total number of flight routes
* Compute and sort the longest flight routes
* Display the airport with the highest degree vertex
* List the most important airports according to PageRank
* List the routes with the lowest flight costs

We will use Spark GraphX for the above computations and visualize the results using Google Data Studio.

**[Download dataset](https://drive.google.com/file/d/0B7Yoht-ttAeuaWdGZkRsSkVkN00/view)**


![](https://cdn.edureka.co/blog/wp-content/uploads/2017/05/Flow-Diagram-Spark-GraphX-Edureka.gif)


The property graph isa directed multigraph which can have multiple edges in parallel. Every edge and vertex has user defined properties associated with it. The parallel edges allow multiple relationships between the same vertices.


![](https://www.javacodegeeks.com/wp-content/uploads/2016/03/image01_flight-relationship.png)


As a starting simple example, we will analyze three flights. For each flight, we have the following information:

        Originating Airport	Destination Airport	Distance
        SFO	                ORD	                1800 miles
        ORD	                DFW	                800 miles
        DFW	                SFO              	1400 miles



![](https://www.javacodegeeks.com/wp-content/uploads/2016/03/image04_3-vertex-relationship.png)




///FileStore/tables/2ko1cy131507093159673/USA_Flight_Datset___Spark_Tutorial___Edureka-c5240.csv


      import org.apache.spark._
      import org.apache.spark.rdd.RDD
      import org.apache.spark.graphx._



     // create vertices RDD with ID and Name
     val vertices=Array((1L, ("SFO")),(2L, ("ORD")),(3L,("DFW")))
     val vRDD= sc.parallelize(vertices)
     vRDD.take(1)
     // Array((1,SFO)) 
     // Defining a default vertex called nowhere
      val nowhere = "nowhere"


      // create routes RDD with srcid, destid, distance
      val edges = Array(Edge(1L,2L,1800),Edge(2L,3L,800),Edge(3L,1L,1400))
      val eRDD= sc.parallelize(edges) 
      eRDD.take(2)
     // Array(Edge(1,2,1800), Edge(2,3,800))



       // define the graph
        val graph = Graph(vRDD,eRDD, nowhere)
       // graph vertices
       graph.vertices.collect.foreach(println)
      // (2,ORD)
      // (1,SFO)
      // (3,DFW) 
      // graph edges
      graph.edges.collect.foreach(println) 
     // Edge(1,2,1800)
     // Edge(2,3,800)
    // Edge(3,1,1400)

-------------------------------------------------------------

      //1. How many airports are there?
      val numairports = graph.numVertices

      //2. How many routes are there?
      val numroutes = graph.numEdges

      //3. which routes > 1000 miles distance?
     // routes > 1000 miles distance?
      graph.edges.filter { case Edge(src, dst, prop) => prop > 1000 }.collect.foreach(println)
      // Edge(1,2,1800)
      // Edge(3,1,1400)

      //4. The EdgeTriplet class extends the Edge class by adding the srcAttr and dstAttr members which contain the source 
    and destination properties, respectively.
    // triplets
    graph.triplets.take(3).foreach(println)
    //((1,SFO),(2,ORD),1800)
    //((2,ORD),(3,DFW),800)
    //((3,DFW),(1,SFO),1400)

    //5. Sort and print out the longest distance routes
    // print out longest routes
     graph.triplets.sortBy(_.attr, ascending=false).map(triplet =>
     "Distance " + triplet.attr.toString + " from " + triplet.srcAttr + " to " + triplet.dstAttr + 
     ".").collect.foreach(println) 
     //Distance 1800 from SFO to ORD.
     //Distance 1400 from DFW to SFO.
     //Distance 800 from ORD to DFW.