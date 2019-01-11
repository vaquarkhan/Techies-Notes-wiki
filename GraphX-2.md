* https://bcourses.berkeley.edu/courses/1267848/pages/lab-11
* http://ampcamp.berkeley.edu/5/exercises/graph-analytics-with-graphx.html

![](https://cdn.edureka.co/blog/wp-content/uploads/2017/05/GraphX-Example-Spark-GraphX-Tutorial-Edureka.png)

Looking at the graph, we can extract information about the people (vertices) and the relations between them (edges). The graph here represents the Twitter users and whom they follow on Twitter. For e.g. Bob follows Davide and Alice on Twitter.

     import org.apache.spark._
     import org.apache.spark.rdd.RDD
     import org.apache.spark.util.IntParam
     import org.apache.spark.graphx._
     import org.apache.spark.graphx.Edge
     import org.apache.spark.graphx.util.GraphGenerators



      val vertexArray = Array(
      (1L, ("Alice", 28)),
      (2L, ("Bob", 27)),
      (3L, ("Charlie", 65)),
      (4L, ("David", 42)),
      (5L, ("Ed", 55)),
      (6L, ("Fran", 50)))

     val edgeArray = Array(
      Edge(2L, 1L, 7),
      Edge(2L, 4L, 2),
      Edge(3L, 2L, 4),
      Edge(3L, 6L, 3),
      Edge(4L, 1L, 1),
      Edge(5L, 2L, 2),
      Edge(5L, 3L, 8),
      Edge(5L, 6L, 3))

    var vertexRDD: RDD[(Long, (String, Int))] = sc.parallelize(vertexArray)

    var edgeRDD: RDD[Edge[Int]] = sc.parallelize(edgeArray)

    var graph: Graph[(String, Int), Int] = Graph(vertexRDD, edgeRDD)


//Displaying Vertices: Further, we will now display all the names and ages of the users (vertices).


       graph.vertices.filter { case (id, (name, age)) => age > 30 }
             .collect.foreach { case (id, (name, age)) => println(s"$name is $age")}

Results 


     Charlie is 65
     David is 42
     Ed is 55
     Fran is 50

     vertexArray: Array[(Long, (String, Int))] = Array((1,(Alice,28)), (2,(Bob,27)), (3,(Charlie,65)), (4,(David,42)), (5,
      (Ed,55)), (6,(Fran,50)))

     edgeArray: Array[org.apache.spark.graphx.Edge[Int]] = Array(Edge(2,1,7), Edge(2,4,2), Edge(3,2,4), Edge(3,6,3), 
     Edge(4,1,1), Edge(5,2,2), Edge(5,3,8), Edge(5,6,3))



Displaying Edges: Let us look at which person likes whom on Twitter.


       for (triplet <- graph.triplets.collect){
       println(s"${triplet.srcAttr._1} likes ${triplet.dstAttr._1}")
       }

Results 

    Bob likes Alice
    Bob likes David
    Charlie likes Bob
    Charlie likes Fran
    David likes Alice
    Ed likes Bob
    Ed likes Charlie
    Ed likes Fran


Number of followers: Every user in our graph has a different number of followers. Let us look at all the followers for every user.



       // Defining a class to more clearly model the user property
       case class User(name: String, age: Int, inDeg: Int, outDeg: Int)
       // Creating a user Graph
       val initialUserGraph: Graph[User, Int] = graph.mapVertices{ case (id, (name, age)) => User(name, age, 0, 0) }
 
      // Filling in the degree information
       val userGraph = initialUserGraph.outerJoinVertices(initialUserGraph.inDegrees) {
      case (id, u, inDegOpt) => User(u.name, u.age, inDegOpt.getOrElse(0), u.outDeg)
     }.outerJoinVertices(initialUserGraph.outDegrees) {
     case (id, u, outDegOpt) => User(u.name, u.age, u.inDeg, outDegOpt.getOrElse(0))
     }

      for ((id, property) <- userGraph.vertices.collect) {
      println(s"User $id is called ${property.name} and is liked by ${property.inDeg} people.")
     }

Results 

      User 1 is called Alice and is liked by 2 people.
      User 2 is called Bob and is liked by 2 people.
      User 3 is called Charlie and is liked by 1 people.
      User 4 is called David and is liked by 1 people.
      User 5 is called Ed and is liked by 0 people.
      User 6 is called Fran and is liked by 2 people.


Oldest Followers: We can also sort the followers by their characteristics. Let us find the oldest followers of each user by age.


      // Finding the oldest follower for each user
      val oldestFollower: VertexRDD[(String, Int)] = userGraph.aggregateMessages[(String, Int)](
      // For each edge send a message to the destination vertex with the attribute of the source vertex
      edge => Iterator((edge.dstId, (edge.srcAttr.name, edge.srcAttr.age))),
       // To combine messages take the message for the older follower
      (a, b) => if (a._2 > b._2) a else b
      )
      userGraph.vertices.leftJoin(oldestFollower) { (id, user, optOldestFollower) =>
      optOldestFollower match {
       case None => s"${user.name} does not have any followers."
       case Some((name, age)) => s"${name} is the oldest follower of ${user.name}."
       }
       }.foreach { case (id, str) => println(str) }

Result

TODO 




Next     https://github.com/vaquarkhan/vk-wiki-notes/wiki/GraphX-3