http://backtobazics.com/big-data/spark/apache-spark-groupby-example/


I have read 5 columns from parquet into data frame. My queries on the parquet table is of below type:

    val df1 = sqlContext.sql(select col1,col2,count(*) from table groupby col1,col2)
    val df2 = sqlContext.sql(select col1,col3,count(*) from table  groupby col1,col3)
    val df3 = sqlContext.sql(select col1,col4,count(*) from table  groupby col1,col4)
    val df4 = sqlContext.sql(select col1,col5,count(*) from table  groupby col1,col5)

And then i require to union the results from df1 to df4 into a single df.


So basically, only the second column is changing, Is there any efficient way to write the above queries  in Spark-Sql instead of writing 4 different queries(OR in loop) and doing union to get the result.


-----------------------------
If you only need the group by in the same hierarchy logic, then you can group by at the lowest level, and cache it, then use the cached DF to derive to the higher level, so Spark will only scan the originally table once, and reuse the cache in the following.

      val df_base =  sqlContext.sql("select col1,col2,col3,col4,col5, count(*) from table
     groupby  col1,col2,col3,col4,col5").cache
     df_base.registerTempTable("df_base")
    val df1 = sqlContext.sql("select col1, col2, count(*) from df_base group by col1, col2")
    val df2 = // similar logic
