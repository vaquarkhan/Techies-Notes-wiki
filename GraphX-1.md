* https://www.youtube.com/watch?v=mKEn9C5bRck
* https://databricks.com/blog/2016/03/03/introducing-graphframes.html
* https://stanford.edu/~rezab/nips2014workshop/slides/ankur.pdf
* https://www.edureka.co/blog/spark-graphx/


**Graph and its representations**

Graph is a data structure that consists of following two components:

![](https://www.mathsisfun.com/geometry/images/vertex-edge-face.svg)

![](https://cdn.edureka.co/blog/wp-content/uploads/2017/05/Graph-Concepts-Spark-GraphX-Tutorial-Edureka.png)

1. A finite set of vertices also called as nodes.
2. A finite set of ordered pair of the form (u, v) called as edge. The pair is ordered because (u, v) is not same as (v, u) in case of directed graph(di-graph). The pair of form (u, v) indicates that there is an edge from vertex u to vertex v. The edges may contain weight/value/cost.

![](http://image.slidesharecdn.com/walldisplay3dshapes-140407062304-phpapp01/95/wall-display-3dshapes-3-638.jpg)

Graphs are used to represent many real life applications: Graphs are used to represent networks. The networks may include paths in a city or telephone network or circuit network. Graphs are also used in social networks like linkedIn, facebook. For example, in facebook, each person is represented with a vertex(or node). Each node is a structure and contains information like person id, name, gender and locale. See this for more applications of graph.

![](https://4.bp.blogspot.com/-A6Yd01RvYM8/WdRPf0lFrKI/AAAAAAAACZI/GTdDkctYzKImgJ6qiVQkwqWol_FUx26IACLcBGAs/s320/Capture.PNG)


![](https://4.bp.blogspot.com/--Sf9jx5yq94/WdRP_d4t3BI/AAAAAAAACZQ/x_E_5KPSt7YbGZrqb_S40NRuRMOtS7LKgCLcBGAs/s640/Capture.PNG)



* A directed graph is a graph where the edges have a direction associated with them. An example of a directed graph is a Twitter follower. User Bob can follow user Carol without implying that user Carol follows user Bob.

* regular graph is a graph where each vertex has the same number of edges. An example of a regular graph is Facebook friends. If Bob is a friend of Carol, then Carol is also a friend of Bob.

-----------------------------------------------

GraphX extends the Spark RDD abstraction by introducing the Resilient Distributed Property Graph: a directed multigraph with properties attached to each vertex and edge. To support graph computation, GraphX exposes a set of fundamental operators (e.g., subgraph, joinVertices, and mapReduceTriplets) as well as an optimized variant of the Pregel API. In addition, GraphX includes a growing collection of graph algorithms and builders to simplify graph analytics tasks.


![](http://ampcamp.berkeley.edu/big-data-mini-course/img/social_graph.png)

Social network with users and their ages modeled as vertices and likes modeled as directed edges,We begin by creating the property graph from arrays of vertices and edges. 


     import org.apache.spark.graphx._
     import org.apache.spark.rdd.RDD

      val vertexArray = Array(
            (1L, ("Alice", 28)),
            (2L, ("Bob", 27)),
            (3L, ("Charlie", 65)),
            (4L, ("David", 42)),
            (5L, ("Ed", 55)),
            (6L, ("Fran", 50))
           )
        val edgeArray = Array(
            Edge(2L, 1L, 7),
            Edge(2L, 4L, 2),
            Edge(3L, 2L, 4),
            Edge(3L, 6L, 3),
            Edge(4L, 1L, 1),
            Edge(5L, 2L, 2),
            Edge(5L, 3L, 8),
            Edge(5L, 6L, 3)
             )


Here we use the Edge class. Edges have a srcId and a dstId corresponding to the source and destination vertex identifiers. In addition, the Edge class has an attr member which stores the edge property (in this case the number of likes).


**Creating a Graph:**


![](https://3.bp.blogspot.com/-ZDO-brJ8CUc/WdRUySkn0RI/AAAAAAAACZg/A4K9X9X43ncOvW8SPbCAW0cIMeWLvyA6wCLcBGAs/s320/Capture.PNG)


    type VertexId=Long

    val vertices: RDD[(VertexId,String)]= sc.parallelize(List((1L,"Alice"),(2L,"Bob"),(3L,"Charlie")))



      class Edge[ED](
      val srcId:VertexId,
      val dstId:VertexId,
      val attr:ED)


     import org.apache.spark.graphx._
     import org.apache.spark.graphx.Edge


     val edges:RDD[Edge[String]] = sc.parallelize(List(Edge(1L,2L,"coworker"),Edge(2L,3L,"friend")))
     val graph = Graph(vertices,edges)

------------------------------------------

**Graph Operations:**


Next : https://github.com/vaquarkhan/vk-wiki-notes/wiki/GraphX-2