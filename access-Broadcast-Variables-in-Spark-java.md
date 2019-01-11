I need to process spark Broadcast variables using Java RDD API. This is my code what i have tried so far:

This is only sample code to check whether its works or not? In my case i need to work on two csvfiles.



    SparkConf conf = new SparkConf().setAppName("BroadcastVariable").setMaster("local");
          JavaSparkContext ctx = new JavaSparkContext(conf);
          Map<Integer,String> map = new HashMap<Integer,String>();
          map.put(1, "aa");
          map.put(2, "bb");
          map.put(9, "ccc");
          Broadcast<Map<Integer, String>> broadcastVar = ctx.broadcast(map);
          List<Integer> list = new ArrayList<Integer>();
          list.add(1);
          list.add(2);
          list.add(9);
          JavaRDD<Integer> listrdd = ctx.parallelize(list);
          JavaRDD<Object> mapr = listrdd.map(x -> broadcastVar.value());
          System.out.println(mapr.collect());
and its prints output like this:

     [{1=aa, 2=bb, 9=ccc}, {1=aa, 2=bb, 9=ccc}, {1=aa, 2=bb, 9=ccc}]

and my requirement is :

     [{aa, bb, ccc}]

Is it possible to do like in my required way?

Ans:
       JavaRDD<Object> mapr = listrdd.map(x -> broadcastVar.value().get(x));

