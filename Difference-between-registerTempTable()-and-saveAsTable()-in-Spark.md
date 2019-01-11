**registerTempTable()**

registerTempTable() creates an in-memory table that is scoped to the cluster in which it was created. The data is stored using Hive's highly-optimized, in-memory columnar format.

This is important for dashboards as dashboards running in a different cluster (ie. the single Dashboard Cluster) will not have access to the temp tables registered in another cluster.

Re-registering a temp table of the same name (using overwrite=true) but with new data causes an atomic memory pointer switch so the new data is seemlessly updated and immediately accessble for querying (ie. from a Dashboard).

**saveAsTable()**

saveAsTable() creates a permanent, physical table stored in S3 using the Parquet format. This table is accessible to all clusters including the dashboard cluster. The table metadata including the location of the file(s) is stored within the Hive metastore.

Re-creating a permanent table of the same name (using overwrite=true) but with new data causes the old data to be deleted and the new data to be saved in the same underlying file on S3. This may lead to moments when the data is not available due to S3's eventual consistency model. There are on-going improvements to reduce this down time