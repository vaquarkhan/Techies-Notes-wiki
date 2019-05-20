* http://blog.cloudera.com/blog/2013/07/guide-to-using-apache-hbase-ports/
* https://phoenix.apache.org/language/datatypes.html#timestamp_type
* https://phoenix.apache.org/secondary_indexing.html
* https://phoenix.apache.org/tuning_guide.html


REFERENCES

Best single source Worth getting to best get up to speed with HBase!

HBase: The Definitive Guide 

HBase overviews

* http://hortonworks.com/webinar/hbase-for-mission-critical-applications/ 

* https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_data-access/content/ch05.html 

* Row key design (subsecond responses require proper keys, which can be composite) 

* https://community.hortonworks.com/questions/101309/hbase-schema-design-for-complex-data.html

* https://www.youtube.com/watch?v=_HLoH_PgrLk

Acid Semantics (HBase locks at the row level)

* https://hbase.apache.org/acid-semantics.html 

**Java API**

* http://hbase.apache.org/book.html#jdo 

* http://www.baeldung.com/hbase 

**Shell API**

* http://hbase.apache.org/book.html#shell 

* Bulk Loading Data

* https://community.hortonworks.com/articles/4942/import-csv-data-into-hbase-using-importtsv.html

* https://hbase.apache.org/book.html#importtsv (sections 145.11 - 145.12)

**Phoenix SQL interface**

* https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html

* https://www.slideshare.net/Hadoop_Summit/w-145p230-ataylorv2 

**The following are excellent refs to get skills on composite keys with Phoenix. (Please still send queries though).**

* https://www.slideshare.net/Hadoop_Summit/w-145p230-ataylorv2 

* https://community.hortonworks.com/articles/61705/art-of-phoenix-secondary-indexes.html 

**This will help you in determining datatypes for your table:** 

* https://phoenix.apache.org/language/datatypes.html 

Not sure where you are in loading data but this ref may help:

*  https://phoenix.apache.org/bulk_dataload.html



-----------------------------------------------------------------------

**POC Steps:**

Phase 1: Hello World
* •Get HBase running and configured with Ambari default selections
* •Understand the Shell and Java APIs in simple Hello World ways
* •Understand HBase architecture (see refs below)
* •Particularly understand key design and wide tables (see refs below)

**Decision point: Phoenix**

* •Read about Phoenix (ref below) which is a SQL interface to HBase
* •It is not required for your solution, since HBase is consumed by microservices. Use of HBase native API is simpler solution.
* •but .. Phoenix presents a SQL interface that may be more familiar and preferable to developers than Java HBase API
* •if users ever query HBase directly, Phoenix is preferable (SQL from a SQL client)
* •it is worth evaluating its potential use

**Phase 2: POC**

* •design your keys against actual data and query patterns
* •load actual datasets (key is first column of dataset) Note: for large volumes of initial load (>50-100GB), consider bulk load as per below
* •consider multiple tables, each with same data but different key design (each table and key design is for specific query pattern)
*  use CLI API to do sanity tests
* •integrate native Java API (or Phoenix JDBC) in microservices framework for final integration
* •optimize HBase configs if needed (see refs)

**Phase 3: **

* Load data Hive to Hbase using Hive SQL


------------------------------------------------------

**Long Key vs Secondry index :+1: **

Every column value is associated with the key itself so long keys result in higher storage on disk and in memory than shorter keys.  During insert, larger keys will take up more space in the memstore which means it will be flushed and the resulting files compacted more frequently than with shorter keys.  Overall, longer keys will occupy more space in hdfs.

If all of this is true, your impact from long keys will be felt at the initial load and not thereafter.  
In this case, you should use bulk load via Map-Reduce [https://phoenix.apache.org/bulk_dataload.html](https://phoenix.apache.org/bulk_dataload.html) and test in development.  To be safe, bulk load your data in batches and use salting.  Also be sure to use DATA_BLOCK_ENCODING=‘FAST_DIFF’.  See this reference: [https://phoenix.apache.org/tuning_guide.html](https://phoenix.apache.org/tuning_guide.html)



------------------------------------------------------
**Primary and secondry index **

* https://community.hortonworks.com/articles/61705/art-of-phoenix-secondary-indexes.html
* https://community.hortonworks.com/questions/46769/is-there-a-limitation-on-a-number-of-secondary-ind.html
* https://phoenix.apache.org/secondary_indexing.html
------------------------------------------------------
Code example:

* https://github.com/petetian/hbase-web-service
* https://github.com/lessc0de/rest-phoenix
* https://community.hortonworks.com/articles/56642/creating-a-spring-boot-java-8-microservice-to-read.html
* http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_data-access/content/ch_using-phoenix.html
* https://community.hortonworks.com/articles/19016/connect-to-phoenix-hbase-using-dbvisualizer.html
* https://netkiller.github.io/java/spring/boot/phoenix.html



REFERENCES

Best single source Worth getting to best get up to speed with HBase! HBase: The Definitive Guide

HBase overviews http://hortonworks.com/webinar/hbase-for-mission-critical-applications/ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_data-access/content/ch05.html

Row key design (subsecond responses require proper keys, which can be composite) https://community.hortonworks.com/questions/101309/hbase-schema-design-for-complex-data.html https://www.youtube.com/watch?v=_HLoH_PgrLk

Acid Semantics (HBase locks at the row level) https://hbase.apache.org/acid-semantics.html

Java API http://hbase.apache.org/book.html#jdo http://www.baeldung.com/hbase

Shell API http://hbase.apache.org/book.html#shell

Bulk Loading Data https://community.hortonworks.com/articles/4942/import-csv-data-into-hbase-using-importtsv.html https://hbase.apache.org/book.html#importtsv (sections 145.11 - 145.12)

Phoenix SQL interface https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html https://www.slideshare.net/Hadoop_Summit/w-145p230-ataylorv2

----------------------------------------------------------------
Example
----------------------------------------------------------------

   package com.khan.vaquar;

    import java.math.BigDecimal;
    import java.sql.ResultSet;
    import java.sql.SQLException;
    import java.util.List;
    import java.util.Set;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.jdbc.core.RowMapper;
    import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
    import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
    import org.springframework.stereotype.Repository;
    import com.khan.vaquar.domain.TestPOJO;

   @Repository
   public class HbasePhonixRepository {
	
	@Value("${phoenix.table-name}")
	private String tableName;
	
	@Autowired
	private NamedParameterJdbcTemplate phoenixJdbcTemplate;
	
	public List<PosAccount> findRecords(String str, Set<String> dates) {
		
		String sql = "SELECT * FROM " + this.tableName + " WHERE CURRENT_ACCOUNT_NBR = :str AND DATE IN (:dates)";
		MapSqlParameterSource parameters = new MapSqlParameterSource();
		parameters.addValue("str", str);
		parameters.addValue("dates", dates);
		
		return this.phoenixJdbcTemplate.query(sql, parameters, new MYMapper());
	}
	
	public static final class MYMapper implements RowMapper<PosAccount> {

		@Override
	    public TestPOJO mapRow(ResultSet rs, int rowNum) throws SQLException {
	    	TestPOJO testPOJO = new TestPOJO();
			
	    	testPOJO.setA(rs.getString("A_NBR"));
	    	testPOJO.setT(rs.getString("T_ID"));
	    				
			return testPOJO;
	    }	
	    
	}
	
}

-------------------
Dependency 
-------------------
	<dependency>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		
		<dependency>
    		<groupId>org.apache.hbase</groupId>
    		<artifactId>hbase-client</artifactId>
    		<version>1.4.0</version>
    		<exclusions>
    			<exclusion>
					<groupId>log4j</groupId>
					<artifactId>log4j</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-log4j12</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		
		<dependency>
    		<groupId>org.apache.phoenix</groupId>
    		<artifactId>phoenix-core</artifactId>
    		<!-- This is the version of our Phoenix jar install -->
   			<version>4.7.0-HBase-1.1</version>
   			<exclusions>
   				<exclusion>
					<groupId>org.apache.hbase</groupId>
    				<artifactId>hbase-client</artifactId>
				</exclusion>
   				<exclusion>
					<groupId>log4j</groupId>
					<artifactId>log4j</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-log4j12</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.mortbay.jetty</groupId>
					<artifactId>servlet-api-2.5</artifactId>
				</exclusion>
				<exclusion>
					<artifactId>servlet-api</artifactId>
					<groupId>javax.servlet</groupId>
				</exclusion>
				<exclusion>
					<groupId>jdk.tools</groupId>
					<artifactId>jdk.tools</artifactId>
				</exclusion>
				<exclusion>
					<groupId>sqlline</groupId>
    				<artifactId>sqlline</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		
		<!--  Maven repo is missing sqlline jar for 1.1.8 for phoenix-core, so include alt version manually -->
		<dependency>
    		<groupId>sqlline</groupId>
    		<artifactId>sqlline</artifactId>
    		<version>1.3.0</version>
		</dependency>
		
------------
Command
------------

upsert into test1 values (1,'Hello');
upsert into test1 values (2,'World');


Create table:

create 'customer', {NAME=>'addr'}, {NAME=>'order'}

Create 't2',{'NAME=addr'},{'NAME=order;}

put 'customers', 'jsmith', 'addr:city', 'nashville'
----------------------------------------------------------
Create Table: insert data
----------------------------------------------------------
create 'asteroids', 'object', 'craft'

put 'asteroids', 'row1', 'object:location', '124212'
put 'asteroids', 'row2', 'object:location', '124212'
put 'asteroids', 'row3', 'object:location', '124213'
put 'asteroids', 'row4', 'object:location', '124214'

put 'asteroids', 'row1', 'object:location', '124215'

----------------------------------------------------------
connecting Phoenix using following commands on Hadoop node

•	 cd /usr/hdp/2.5.3.0-37/phoenix/bin

•	./sqlline-thin.py <ServerName>:<PORT>

-------------------------------------------------------
http://hadooptutorial.info/apache-phoenix-hbase-an-sql-layer-on-hbase/

https://www.youtube.com/watch?v=_HLoH_PgrLk
		

https://stackoverflow.com/questions/46331734/how-to-mask-columns-using-spark-2

https://www.youtube.com/watch?v=_HLoH_PgrLk


https://github.com/tspannhw/phoenix

https://community.hortonworks.com/articles/56642/creating-a-spring-boot-java-8-microservice-to-read.html

https://phoenix.apache.org/faq.html#I_want_to_get_started_Is_there_a_Phoenix_Hello_World
