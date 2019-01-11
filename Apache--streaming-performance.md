am having trouble with streaming performance. My main problem is how to do a sliding window calculation where the ratio between the window size and the step size is relatively large (hundreds) without recalculating everything all the time.

I created a simple example of what I am aiming at with what I have so far which is detailed in http://stackoverflow.com/questions/41266956/apache-spark-streaming-performance

I was hoping someone can point me to what I am doing wrong.

Thanks,

----------
From what I understand looking at the code in stackoverflow, I think you are "simulating" the streaming version of your calculation incorrectly. You are repeatedly unioning batch dataframes to simulate streaming and then applying aggregation on the unioned DF. That will not going to compute aggregates incrementally, it will just process the whole data every time. So the oldest batch DF again and again, causing an increasing resource usage. Thats not how streaming works, so this is not simulating the right thing.

With Structured Streaming's streaming dataframes, it is actually done incrementally. The best way to try that is to generate a file per "bucket", and then create a streaming dataframe on the files such that they are one by one. See this notebook for the maxFilesPerTrigger option in http://cdn2.hubspot.net/hubfs/438089/notebooks/spark2.0/Structured%20Streaming%20using%20Scala%20DataFrames%20API.html 
This would process each file one by one, maintain internal state to continuous update the aggregates and never require reprocessing the old data.

Hope this helps
