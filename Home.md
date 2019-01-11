Welcome to the Spark-Notes wiki!

## Install Apache Spark (OSX)

```shell
$ brew install apache-spark
```


## Run the Spark python shell

A python shell with a preconfigured SparkContext (available as `sc`). It is
single node, in fact it seems to ignore `--num-executors`.

```shell
$ pyspark
$ IPYTHON=1 pyspark
$ PYSPARK_DRIVER_PYTHON=/path/to/python pyspark
$ PYSPARK_DRIVER_PYTHON=/path/to/python IPYTHON=1 pyspark
```


If required, explicitly state which python executable to use:

* IPYTHON will cause an embedded ipython (if available on PATH) to be started.

* PYSPARK_DRIVER_PYTHON can be used to explicitly specify which python
executable to be used. It can also be used to point to ipython directly.

The GUI is available at http://10.1.0.111:4040


## Configuration

See https://spark.apache.org/docs/latest/configuration.html

To pass Spark properties to pyspark

```shell
$ PYSPARK_DRIVER_PYTHON=/path/to/python pyspark --conf 'user.country=GB user.name=rob'
```

Multiple `--conf` params can be passed.

It is sometimes useful to see exactly what command is executed when starting
the pyspark shell. To do so, use `SPARK_PRINT_LAUNCH_COMMAND=1`

```shell
$ SPARK_PRINT_LAUNCH_COMMAND=1 pyspark
```


## Loading data from S3

Set environment variables to configure AWS authentication `AWS_ACCESS_KEY_ID`
and `AWS_SECRET_ACCESS_KEY`.

Load files with `s3n://` prefix.

```python
>>> rdd = sc.textFile('s3n://bucket/data.txt')
```

Using SparkSQL we can load json-per-line files thus

```python
>>> dataframe = sqlContext.jsonFile('s3n://bucket/data.txt')
```

*However*, we have seen unexpected behaviour with in-memory caching of the
data frame loading directly like this. A workaround is to load the file to an
RDD and construct a JSON data frame from that intermediate RDD.

```python
>>> rdd = sc.textFile('s3n://bucket/data.txt')
>>> rdd.cache()
>>> dataframe = sqlContext.jsonRDD(rdd)
```

### Glob patterns

Supports any glob pattern that hadoop supports.

| Pattern       | Description                                                                                                                                                   |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ?             | Matches any single character.                                                                                                                                 |
| *             | Matches zero or more characters.                                                                                                                              |
| [abc]         | Matches a single character from character set {a,b,c}.                                                                                                        |
| [a-b]         | Matches a single character from the character range {a...b}. Note that character a must be lexicographically less than or equal to character b.               |
| [^a]          | Matches a single character that is not from character set or range {a}. Note that the ^ character must occur immediately to the right of the opening bracket. |
| \c            | Removes (escapes) any special meaning of character c.                                                                                                         |
| {ab,cd}       | Matches a string from the string set {ab, cd}                                                                                                                 |
| {ab,c{de,fh}} | Matches a string from the string set {ab, cde, cfh}                                                                                                           |

NOTE: Be careful with prefixes/paths. We discovered that `.parquet` in an S3 prefix terminates recursive file lookup, presumably because it expects that string at the end of the path.

## Loading CSV

There is no built-in support for loading csv files. Instead, load the file as
a text file and map a parsing function to each line. There are numerous ways
to do this. Something like:

```python
>>> import csv
>>> headers = ['col_a', 'col_b', 'col_c']
>>> sc.textFile(filepath).map(
    lambda l: csv.DictReader([l], fieldnames=headers).next())
```

If performance is critical, rumour has it that `pandas.io.parsers.read_csv` is
much faster. This is untested but may offer some interesting benefits.

```python
>>> d = pandas.io.parsers.read_csv(filepath, names=headers)
>>> df = sqlContext.createDataFrame(d)
```

There is a third-party [package](http://spark-packages.org/package/databricks/spark-csv)
to add CSV support to `.load` and `.save`. It can be loaded by passing `--packages`
to pyspark.

```shell
$ pyspark --packages com.databricks:spark-csv_2.11:1.0.3
```

```python
>>> df = sqlContext.load(
        path="s3n://data.api/metadata/merchants.tsv.gz"
        source="com.databricks.spark.csv",
        delimiter="\t",
        header="true")
>>> df.take(1)
```

According to the [docs](https://github.com/databricks/spark-csv) a jar of the
package can be built for deployment to a cluster.


## Loading data from SQL DB

Since Spark 1.3 it is possible to load a table or SELECT statement into a data
frame. Do so judiciously as we have not yet determined precisely how it loads
data and what performance implications it may (or may not) have.

A jar of an appropriate JDBC driver is required. Load it my setting
`--driver-library-path` and `-driver-class-path`.

NOTE: A mysql jdbc driver seems to be pre-installed on EMR at `/home/hadoop/spark/classpath/emr/mysql-connector-java-5.1.30.jar`.

```shell
$ pyspark --driver-library-path '../mysql-connector-java-5.1.35.jar' --driver-class-path '../mysql-connector-java-5.1.35.jar'
```

Note that the docs suggest using `SPARK_CLASSPATH` env var. This works but is
deprecated.

Create a `SQLContext`

```python
>>> from pyspark.sql import SQLContext
>>> sqlctx = SQLContext(sc)
>>> df = sqlctx.load(
        source='jdbc',
        driver='com.mysql.jdbc.Driver',
        url='jdbc:mysql://host.net/mugic?user=user&password=password',
        dbtable='table_or_select')
>>> df.take(2)
```

## Viewing the Spark GUI


### Post 4.0 AMI

1. Establish a SOCKS proxy to the master node.
1. Use this proxy in your browser of choice.
1. Connect to the Hadoop UI on port 8088, and navigate to the Spark app.

```shell
$ aws emr socks --cluster-id j-XXXXXXXXX --key-pair-file ~/.ssh/emr.pem
$ /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --proxy-server="socks5://localhost:8157" \
  --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost" \
  --user-data-dir=/tmp/

```

### Pre 4.0 AMI

Go to the Hadoop web UI at {host}:9026, take the ApplicationMaster link (right
hand side) and replace the internal IP with the hostname.

Hostname for the master node can be found in the management console (Master
public DNS).

Within the UI urls are constructed using `SPARK_PUBLIC_DNS`, which can be set
on the master node.


## Silencing the logs

By default spark logs are printed to stdout. To reduce the verbosity change
the log level in the log4j.properties file.

Look for `log4j.rootCategory=INFO, console` and change `INFO` to `WARN`

The file can (probably) be found at `~/spark-x.x.x/conf/log4j.properties`, or
`/usr/local/Cellar/apache-spark/x.x.x/libexec/conf/log4j.properties` on osx if
you installed with homebrew.


## On EMR

* Spark env vars can be set in `/home/hadoop/spark/conf/spark-env.sh`,
  commonly in a bootstrap step.
* `Spark 1.3.1` requires `/tmp/spark-events` but it does not exist in `AMI 3.8.0`.
  Create it in the bootstrap step.

### Run commands on EMR nodes

* To get the IPs of all worker nodes:

```
hdfs dfsadmin -report | grep ^Name | cut -f2 -d: | cut -f2 -d' '
```

* To run a command on all nodes, use pssh:

```
hdfs dfsadmin -report | grep ^Name | cut -f2 -d: | cut -f2 -d' ' > hosts.txt
pssh -h hosts.txt -l hadoop -x "-oStrictHostKeyChecking=no" -o output.txt '{CMD}'
```

## HDFS

* Often useful to use [S3DistCp](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/UsingEMR_s3distcp.html) to copy data to HDFS on the cluster.
* Hadoop, and HDFS can be configured in a [bootstrap step](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-plan-bootstrap.html).
* HDFS commands can be executed on the cli with `hdfs`.
* Cluster disk usage: `hdfs dfs -df -h`
* To move a local file into HDFS: `hdfs dfs -copyFromLocal /local /dst`
* To view HDFS config: `hdfs getconf -confKey [key]`
* HDFS site config is at `/home/hadoop/conf/hdfs-site.xml`


# Useful Links

## AWS, EC2, EMR

* http://tech.just-eat.com/2014/02/13/tips-for-using-ec2-spot-instances/
* [Running Spark on EC2](https://spark.apache.org/docs/latest/ec2-scripts.html)
* [EMR Spark bootstrap scripts from AWS](https://github.com/awslabs/emr-bootstrap-actions/tree/master/spark)

## Spark

* https://spark.apache.org/docs/latest/
* [Spark Configuration](https://spark.apache.org/docs/latest/configuration.html)
* http://blog.cloudera.com/blog/2015/03/how-to-tune-your-apache-spark-jobs-part-2/
* https://databricks.com/blog/2015/04/24/recent-performance-improvements-in-apache-spark-sql-python-dataframes-and-more.html
* [Advanced Spark](http://ampcamp.berkeley.edu/wp-content/uploads/2012/06/matei-zaharia-amp-camp-2012-advanced-spark.pdf)
* [Mastering Spark](https://jaceklaskowski.gitbooks.io/mastering-apache-spark/content/)

## Spark at Skimlinks
* [Common errors and solutions](https://docs.google.com/document/d/1m-x812gquyk0hpriYwgVnUo30SKDkWF-kDUZE9hu-Nw/edit)