Short Description:

TDD for Big Data. How to test before you deploy to massive distributed clusters.
Article

Most code for current big data projects and for the code you are going to write is going to be JVM based (Java and Scala mostly). There is certainly a ton of R, Python, Shell and other languages. For this tutorial we will focus on JVM tools.

The great thing about that is that Java and Scala Static Code Analysis Tools will work for analyzing your code. JUnit test are great for testing the basic code and making sure you isolate out functionality from Hadoop and Spark specific interfacing.

General Java Tools for Testing

http://junit.org/
http://checkstyle.sourceforge.net/
http://pmd.github.io/pmd-5.4.2/pmd-java/rules/index.html
Testing Hadoop (A Great Overview)

https://github.com/mfjohnson/HadoopTesting
https://www.infoq.com/articles/HadoopMRUnit
Example:

I have a Hive UDF written in Java that I can test via Junit to ensure that the main functionality works.

(See: UtilTest)

import static org.junit.Assert.assertEquals;
import org.junit.Test;	
       /**
	 * Test method for 
	 * {@link com.dataflowdeveloper.deprofaner.ProfanityRemover#fillWithCharacter(
         * int, java.lang.String)}.
	 */
	@Test
	public void testFillWithCharacterIntString() {
		assertEquals("XXXXX", Util.fillWithCharacter(5, "X") );				
	}
As you can see this is just a plain old JUnit Test, but it's one step in the process to make sure you can test your code before it is deployed. Also Jenkins and other CI tools are great at running JUnits are part of their continuous build and integration process.

A great way to test your application is with a small Hadoop cluster or simulated one. Testing against a Sandbox downloaded on your laptop is a great way as well.

Testing Integration with a Mini-Cluster

https://github.com/hortonworks/mini-dev-cluster
https://github.com/sakserv/hadoop-mini-clusters
Testing Hbase Applications

Artem Ervits has a great article on Hbase Unit Testing.
https://community.hortonworks.com/repos/15674/variety-of-hbase-unit-testing-utilities.html
https://github.com/dbist/HBaseUnitTest
Testing Apache NiFi Processors

http://docs.hortonworks.com/HDPDocuments/HDF1/HDF-1.2.0.1/bk_DeveloperGuide/content/instantiate-testrunner.html
http://www.nifi.rocks/developing-a-custom-apache-nifi-processor-unit-tests-partI/
Testing Apache NiFi Scripts

https://github.com/mattyb149/nifi-script-tester
http://funnifi.blogspot.com/2016/06/testing-executescript-processor-scripts.html
Testing Oozie

https://oozie.apache.org/docs/4.2.0/ENG_MiniOozie.html
Testing Hive Scripts

https://cwiki.apache.org/confluence/display/Hive/Unit+Testing+Hive+SQL
http://hakunamapdata.com/beetest-a-simple-utility-for-testing-apache-hive-scripts-locally-for-non-java-developers/
https://github.com/klarna/HiveRunner
https://github.com/edwardcapriolo/hive_test
http://finraos.github.io/HiveQLUnit/
Testing Hive UDF

http://blog.matthewrathbone.com/2013/08/10/guide-to-writing-hive-udfs.html
https://cwiki.apache.org/confluence/display/Hive/PluginDeveloperKit
Using org.apache.hive.pdk.HivePdkUnitTest and org.apache.hive.pdk.HivePdkUnitTests in your Hive plugin so that it will be included in unit tests.

Testing Pig Scripts

http://pig.apache.org/docs/r0.8.1/pigunit.html
http://www.slideshare.net/Skillspeed/hdfs-and-big-data-tdd-using-pig-unit-webinar
http://www.slideshare.net/SwissHUG/practical-pig-and-pig-unit-michael-noll-july-2012
Testing Apache Spark Applications

http://www.jesse-anderson.com/2016/04/unit-testing-spark-with-java/
https://github.com/holdenk/spark-testing-base
http://www.slideshare.net/hkarau/effective-testing-for-spark-programs-strata-ny-2015
https://developer.ibm.com/hadoop/2016/03/07/testing-your-apache-spark-code-with-junit-4-0-and-intellij/
http://www.slideshare.net/knoldus/unit-testing-of-spark-applications
Testing Apache Storm Applications

Debugging an Apache Storm Topology
https://github.com/xumingming/storm-lib/blob/master/src/jvm/storm/TestingApiDemo.java