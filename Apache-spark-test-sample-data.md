https://github.com/datasets?page=2

http://hadoopilluminated.com/hadoop_illuminated/Public_Bigdata_Sets.html



Here are some public data sets I have gathered over time

* 1. http://wiki.gephi.org/index.php/Datasets
* 1. Download large data for Hadoop
* 1. http://datamob.org/datasets
* 1. http://konect.uni-koblenz.de/
* 1. http://snap.stanford.edu/data/
* 1. http://archive.ics.uci.edu/ml/
* 1. https://bitly.com/bundles/hmason/1
* 1. http://www.inside-r.org/howto/finding-data-internet
* http://goo.gl/Jecp6
* http://ftp3.ncdc.noaa.gov/pub/data/noaa/1990/
* http://data.cityofsantacruz.com/
* http://bitly.com/bundles/hmason/1
* https://aws.amazon.com/public-datasets/

* http://myjourneythroughhadoop.blogspot.in/2013/07/how-to-download-weather-data-for-your.html



**Copy a remote dataset from the internet to DBFS in my Spark cluster?**

You can use wget to pull the file down to your Driver, then copy this file to your cluster in Scala or Python using dbutils.cp() as follows:
import sys.process._

"wget -P /tmp http://vincentarelbundock.github.io/Rdatasets/csv/datasets/iris.csv" !!

val localpath="file:/tmp/iris.csv"

dbutils.fs.mkdirs("dbfs:/datasets/")

dbutils.fs.cp(localpath, "dbfs:/datasets/")

display(dbutils.fs.ls("dbfs:/datasets/iris.csv"))

Note that the local file is referenced using file:/ and the distributed dbfs file is referenced using dbfs:/.