**GraphX Basics and Operations**

GraphX is based on two specialized RDD implementations that are optimized for graphs. 
The VertexRDD[VD] is a specialized implementation of RDD[(VertexId, VD)], where the VertexID type is an instance of Long and is required for every vertex, while the VD can be any other type of data that is associated with the vertex, and is called the vertex attribute. The VertexRDD[A] extends RDD[(VertexID, A)] and adds the additional constraint that each VertexID occurs only once. Moreover,VertexRDD[A] represents a set of vertices each with an attribute of type A. 
The EdgeRDD[ED] is a specialized implementation of RDD[Edge[ED]], where Edge is a case class that contains two VertexId values and an edge attribute of type ED.The EdgeRDD[ED], which extends RDD[Edge[ED]] organizes the edges in blocks partitioned using one of the various partitioning strategies defined in PartitionStrategy. Within each partition, edge attributes and adjacency structure, are stored separately enabling maximum reuse when changing attribute values.

Both the VertexRDD and the EdgeRDD have internal indices within each partition of the data that is designed to facilitate fast joins and attribute updates.

Rather than splitting graphs along edges, GraphX partitions the graph along vertices which can reduce both the communication and storage overhead. Logically, this corresponds to assigning edges to machines and allowing vertices to span multiple machines.

**Deduplication**

GraphX API automatically converts the input duplicated vertices into unique VertexRDDs.Note that if there are duplicate entries in the EdgeRDD for a given pair of vertices, the Graph API will not deduplicate them: GraphX allows us to create multigraphs,


**Connected Components**
          val connectedComponentGraph: Graph[VertexId, Int] =
          topicGraph.connectedComponents()
          connectedComponents.vertices should contains (vertex, componentid)

The connected components algorithm labels each connected component of the graph with the ID of its lowest-numbered vertex.
To see the names of the topics associated with these smaller components, we’ll need to join the VertexRDD for the connected components graph with the vertices from our original concept graph by VertexRDD's innerJoin transformation.

       val nameCID = topicGraph.vertices.
        innerJoin(connectedComponentGraph.vertices) {
          (topicId, name, componentId) => (name, componentId)
}

**EdgeTriplet**

The triplet view logically joins the vertex and edge properties yielding an RDD[EdgeTriplet[VD, ED]] containing instances of the EdgeTriplet class. The EdgeTriplet class extends the Edge class by adding the srcAttr and dstAttr members which contain the source and destination properties respectively.  src.id, dst.id, src.attr, e.attr, dst.attr
We can use the triplet view of a graph to render a collection of strings describing relationships between users.

     val facts: RDD[String] = graph.triplets.map(triplet =>
        triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1)


**Property Operators**

Like the RDD map operator, the property graph contains the following:

     class Graph[VD, ED] {
      def mapVertices[VD2](map: (VertexId, VD) => VD2): Graph[VD2, ED]
      def mapEdges[ED2](map: Edge[ED] => ED2): Graph[VD, ED2]
      def mapTriplets[ED2](map: EdgeTriplet[VD, ED] => ED2): Graph[VD, ED2] 
       }

Each of these operators yields a new graph with the vertex or edge properties modified by the user defined map function.
Note that in each case the graph structure is unaffected. This is a key feature of these operators which allows the resulting graph to reuse the structural indices of the original graph. The following snippets are logically equivalent, but the first one does not preserve the structural indices and would not benefit from the GraphX system optimizations:



        val newVertices = graph.vertices.map { case (id, attr) => (id, mapUdf(id, attr)) }
        val newGraph = Graph(newVertices, graph.edges)
        Instead, use mapVertices to preserve the indices:
        val newGraph = graph.mapVertices((id, attr) => mapUdf(id, attr))

These operators are often used to initialize the graph for a particular computation or project away unnecessary properties. 

The subgraph operator takes vertex and edge predicates and returns the graph containing only the vertices that satisfy the vertex predicate (evaluate to true) and edges that satisfy the edge predicate and connect vertices that satisfy the vertex predicate. The subgraph operator can be used in number of situations to restrict the graph to the vertices and edges of interest or eliminate broken links. The subgraph operator defaults to true if the vertex or edge predicates are not provided.


             def subgraph(epred: EdgeTriplet[VD,ED] => Boolean,
               vpred: (VertexId, VD) => Boolean): Graph[VD, ED]

               val validGraph = graph.subgraph(vpred = (id, attr) => attr._2 != "Missing")

**Join Operators**
The joinVertices operator joins the vertices with the input RDD and returns a new graph with the vertex properties obtained by applying the user defined map function to the result of the joined vertices. Vertices without a matching value in the RDD retain their original value.
The more general outerJoinVertices behaves similarly to joinVertices except that the user defined map function is applied to all vertices and can change the vertex property type. Because not all vertices may have a matching value in the input RDD the map function takes an Option type. 


      val outDegrees: VertexRDD[Int] = graph.outDegrees
      val degreeGraph = graph.outerJoinVertices(outDegrees) { (id, oldAttr, outDegOpt) =>
         outDegOpt match {
         case Some(outDeg) => outDeg
           case None => 0 // No outDegree means zero outDegree
                         }
           }

**Aggregation Operators**

The core aggregation operation in GraphX is aggregateMessages.This operator applies a user defined sendMsg function to each edge triplet in the graph and then uses the mergeMsg function to aggregate those messages at their destination vertex.

         class Graph[VD, ED] {
                def aggregateMessages[Msg: ClassTag](
                sendMsg: EdgeContext[VD, ED, Msg] => Unit,
                mergeMsg: (Msg, Msg) => Msg,
                tripletFields: TripletFields = TripletFields.All): VertexRDD[Msg]
             }
The user defined sendMsg function takes an EdgeContext, which exposes the source and destination attributes along with the edge attribute and functions (sendToSrc, and sendToDst) to send messages to the source and destination attributes. Think of sendMsg as the map function in map-reduce. 

The user defined mergeMsg function takes two messages destined to the same vertex and yields a single message. Think of mergeMsg as the reduce function in map-reduce. 

In addition, aggregateMessages takes an optional tripletsFields which indicates what data is accessed in the EdgeContext. The possible options for the tripletsFields are defined in TripletFields and the default value is TripletFields.All. ThetripletFields argument can be used to notify GraphX that only part of the EdgeContext will be needed allowing GraphX to select an optimized join strategy. For example if we are computing the average age of the followers of each user we would only require the source field and so we would use TripletFields.Src to indicate that we only require the source field.


            val olderFollowers: VertexRDD[(Int, Double)] = graph.aggregateMessages[(Int, Double)](
                 triplet => { // Map Function
           if (triplet.srcAttr > triplet.dstAttr) {
             // Send message to destination vertex containing counter and age
               triplet.sendToDst(1, triplet.srcAttr)
             } },
            // Add counter and age
            (a, b) => (a._1 + b._1, a._2 + b._2) // Reduce Function
           )
Reference:
http://spark.apache.org/docs/latest/graphx-programming-guide.html