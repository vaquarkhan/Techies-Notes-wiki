That feature is coming in 2.1.0. We have added watermarking, that will track the event time of the data and accordingly close old windows, output its corresponding aggregate and then drop its corresponding state. But in that case, you will have to use append mode, and aggregated data of a particular window will be evicted only when the windows is closed. You will be able to control the threshold on how long to wait for late, out-of-order data before closing a window. 

you can read the design doc in the corresponding JIRA -

  https://issues.apache.org/jira/browse/SPARK-18124
