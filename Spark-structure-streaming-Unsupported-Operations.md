There are a few DataFrame/Dataset operations that are not supported with streaming DataFrames/Datasets. Some of them are as follows.

Multiple streaming aggregations (i.e. a chain of aggregations on a streaming DF) are not yet supported on streaming Datasets.

Limit and take first N rows are not supported on streaming Datasets.

Distinct operations on streaming Datasets are not supported.

Sorting operations are supported on streaming Datasets only after an aggregation and in Complete Output Mode.

Outer joins between a streaming and a static Datasets are conditionally supported.

Full outer join with a streaming Dataset is not supported

Left outer join with a streaming Dataset on the right is not supported

Right outer join with a streaming Dataset on the left is not supported

Any kind of joins between two streaming Datasets is not yet supported.

In addition, there are some Dataset methods that will not work on streaming Datasets. They are actions that will immediately run queries and return results, which does not make sense on a streaming Dataset. Rather, those functionalities can be done by explicitly starting a streaming query (see the next section regarding that).

count() - Cannot return a single count from a streaming Dataset. Instead, use ds.groupBy().count() which returns a streaming Dataset containing a running count.

foreach() - Instead use ds.writeStream.foreach(...) (see next section).

show() - Instead use the console sink (see next section).

If you try any of these operations, you will see an AnalysisException like “operation XYZ is not supported with streaming DataFrames/Datasets”. While some of them may be supported in future releases of Spark, there are others which are fundamentally hard to implement on streaming data efficiently. For example, sorting on the input stream is not supported, as it requires keeping track of all the data received in the stream. This is therefore fundamentally hard to execute efficiently.


http://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#unsupported-operations