 am working on building a custom ML pipeline-model / estimator to impute
> missing values, e.g. I want to fill with last good known value.
> Using a window function is slow / will put the data into a single
> partition.
> I built some sample code to use the RDD API however, it some None / null
> problems with empty partitions.
>
> How should this be implemented properly to handle such empty partitions?
> http://stackoverflow.com/questions/41474175/spark-mappartitionswithindex-handling-empty-partitions



http://www.spark.tc/


 https://gist.github.com/geoHeil/6a23d18ccec085d486165089f9f430f2 for me
 that is crashing in either line 51 or 58. Holden described the problem