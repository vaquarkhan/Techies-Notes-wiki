We all write perfect Apache Spark code and everything runs perfectly all the time, right?we know that working with large datasets is hardly ever that easy – there is inevitably some data point that will expose any corner cases with your code.

Use count() to call actions on intermediary RDDs/Dataframes .

While it’s great that Spark follows a lazy computation model so it doesn’t compute anything until necessary, the downside is that when you do get an error, it may not be clear exactly where the error in your code appeared. Therefore, you’ll want to factor your code such that you can store intermediary RDDs / Dataframes as a variable. When debugging, you should call count() on your RDDs / Dataframes to see what stage your error occurred. This is a useful tip not just for errors, but even for optimizing the performance of your Spark jobs. It will allow you to measure the running time of each individual stage and optimize them.

Working around bad input.

When working with large datasets, you will have bad input that is malformed or not as you would expect it. I recommend being proactive about deciding for your use case, whether you can drop any bad input, or you want to try fixing and recovering, or otherwise investigating why your input data is bad.

A filter command is a great way to get only your good input points or your bad input data (If you want to look into that more and debug). If you want to fix your input data or to drop it if you cannot, then using a flatMap() operation is a great way to accomplish that.

Understanding how to debug with the Spark UI

Generally, I find the Spark UI intuitive to use. The only thing I see is that sometimes if a job fails, users will only look at the error that is thrown up to the cell in the console. When you have a Spark stage with a ton of tasks, if even a single task consistently fails, your whole job will fail. So, I advise drilling all the way down to the task page, sorting your page by the status, and examining the “Errors” column for the tasks that have failed. You’ll get a detailed error message there



Scale up Spark jobs slowly for really large datasets

If you have a really large dataset to analyze and run into errors, you may want to try debugging/testing on a portion of your dataset first. And then when you get that running smoothly, go back to the full dataset. Having a smaller dataset makes it quick to reproduce any errors, understand the characteristics of your dataset during various stages of your data pipeline, and more. Note – you can definitely run into more problems when you run the larger dataset – the hope is just that if you can reproduce the error at a smaller scale, it’s easier for you to fix than if you needed the full dataset.

Examine the partitioning for your dataset.

While Spark chooses good reasonable defaults for your data, if your Spark job runs out of memory or runs slowly, bad partitioning could be at fault. For me, I start with trying different partitioning sizes to see how they affect your job.

If your dataset is large, you can try repartitioning to a larger number to allow more parallelism on your job. A good indication of this is if in the Spark UI – you don’t have a lot of tasks, but each task is very slow to complete.

On the other hand, if you don’t have that much data and you have a ton of partitions, the overhead of the having too many partitions can also cause your job to be slow. You can repartition to a smaller number, but coalesce may be faster since that will try to combine partitions on the same machines rather than shuffle your data around again.

If you are using Spark SQL, you can set the partition for shuffle steps by setting spark.sql.shuffle.partitions.


http://spark.apache.org/docs/latest/api/scala/index.html
