https://github.com/apache/spark/pull/16000

here is my test code base on v1.6.0
###################################
diff --git a/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/JacksonMessageWriter.scala b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/JacksonMessageWriter.scala
new file mode 100644
index 0000000..690e2d8
--- /dev/null
+++ b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/JacksonMessageWriter.scala
@@ -0,0 +1,68 @@
+package org.apache.spark.streaming.status.api.v1
+
+import java.io.OutputStream
+import java.lang.annotation.Annotation
+import java.lang.reflect.Type
+import java.text.SimpleDateFormat
+import java.util.{Calendar, SimpleTimeZone}
+import javax.ws.rs.Produces
+import javax.ws.rs.core.{MediaType, MultivaluedMap}
+import javax.ws.rs.ext.{MessageBodyWriter, Provider}
+
+import com.fasterxml.jackson.annotation.JsonInclude
+import com.fasterxml.jackson.databind.{ObjectMapper, SerializationFeature}
+
+@Provider
+@Produces(Array(MediaType.APPLICATION_JSON))
+private[v1] class JacksonMessageWriter extends MessageBodyWriter[Object]{
+
+  val mapper = new ObjectMapper() {
+    override def writeValueAsString(t: Any): String = {
+      super.writeValueAsString(t)
+    }
+  }
+ mapper.registerModule(com.fasterxml.jackson.module.scala.DefaultScalaModule)
+  mapper.enable(SerializationFeature.INDENT_OUTPUT)
+  mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL)
+  mapper.setDateFormat(JacksonMessageWriter.makeISODateFormat)
+
+  override def isWriteable(
+      aClass: Class[_],
+      `type`: Type,
+      annotations: Array[Annotation],
+      mediaType: MediaType): Boolean = {
+      true
+  }
+
+  override def writeTo(
+      t: Object,
+      aClass: Class[_],
+      `type`: Type,
+      annotations: Array[Annotation],
+      mediaType: MediaType,
+      multivaluedMap: MultivaluedMap[String, AnyRef],
+      outputStream: OutputStream): Unit = {
+    t match {
+      //case ErrorWrapper(err) => outputStream.write(err.getBytes("utf-8"))
+      case _ => mapper.writeValue(outputStream, t)
+    }
+  }
+
+  override def getSize(
+      t: Object,
+      aClass: Class[_],
+      `type`: Type,
+      annotations: Array[Annotation],
+      mediaType: MediaType): Long = {
+    -1L
+  }
+}
+
+private[spark] object JacksonMessageWriter {
+  def makeISODateFormat: SimpleDateFormat = {
+    val iso8601 = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'GMT'")
+    val cal = Calendar.getInstance(new SimpleTimeZone(0, "GMT"))
+    iso8601.setCalendar(cal)
+    iso8601
+  }
+}
diff --git a/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/StreamingApiRootResource.scala b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/StreamingApiRootResource.scala
new file mode 100644
index 0000000..f4e43dd
--- /dev/null
+++ b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/StreamingApiRootResource.scala
@@ -0,0 +1,74 @@
+package org.apache.spark.streaming.status.api.v1
+
+import org.apache.spark.status.api.v1.UIRoot
+import org.eclipse.jetty.server.handler.ContextHandler
+import org.eclipse.jetty.servlet.ServletContextHandler
+import org.eclipse.jetty.servlet.ServletHolder
+
+import com.sun.jersey.spi.container.servlet.ServletContainer
+
+import javax.servlet.ServletContext
+import javax.ws.rs.Path
+import javax.ws.rs.Produces
+import javax.ws.rs.core.Context
+import org.apache.spark.streaming.ui.StreamingJobProgressListener
+
+
+@Path("/v1")
+private[v1] class StreamingApiRootResource extends UIRootFromServletContext{
+
+  @Path("streaminginfo")
+  def getStreamingInfo(): StreamingInfoResource = {
+    new StreamingInfoResource(uiRoot,listener)
+  }
+
+}
+
+private[spark] object StreamingApiRootResource {
+
+  def getServletHandler(uiRoot: UIRoot, listener:StreamingJobProgressListener): ServletContextHandler = {
+
+    val jerseyContext = new ServletContextHandler(ServletContextHandler.NO_SESSIONS)
+    jerseyContext.setContextPath("/streamingapi")
+    val holder: ServletHolder = new ServletHolder(classOf[ServletContainer])
+ holder.setInitParameter("com.sun.jersey.config.property.resourceConfigClass",
+      "com.sun.jersey.api.core.PackagesResourceConfig")
+ holder.setInitParameter("com.sun.jersey.config.property.packages",
+      "org.apache.spark.streaming.status.api.v1")
+ //holder.setInitParameter(ResourceConfig.PROPERTY_CONTAINER_REQUEST_FILTERS,
+    //  classOf[SecurityFilter].getCanonicalName)
+    UIRootFromServletContext.setUiRoot(jerseyContext, uiRoot)
+    UIRootFromServletContext.setListener(jerseyContext, listener)
+    jerseyContext.addServlet(holder, "/*")
+    jerseyContext
+  }
+}
+
+private[v1] object UIRootFromServletContext {
+
+  private val attribute = getClass.getCanonicalName
+
+  def setListener(contextHandler:ContextHandler, listener: StreamingJobProgressListener):Unit={
+   contextHandler.setAttribute(attribute+"_listener", listener)
+  }
+
+  def getListener(context:ServletContext):StreamingJobProgressListener={
+ context.getAttribute(attribute+"_listener").asInstanceOf[StreamingJobProgressListener]
+  }
+
+  def setUiRoot(contextHandler: ContextHandler, uiRoot: UIRoot): Unit = {
+    contextHandler.setAttribute(attribute, uiRoot)
+  }
+
+  def getUiRoot(context: ServletContext): UIRoot = {
+    context.getAttribute(attribute).asInstanceOf[UIRoot]
+  }
+}
+
+private[v1] trait UIRootFromServletContext {
+  @Context
+  var servletContext: ServletContext = _
+
+  def uiRoot: UIRoot = UIRootFromServletContext.getUiRoot(servletContext)
+  def listener: StreamingJobProgressListener = UIRootFromServletContext.getListener(servletContext)
+}
diff --git a/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/StreamingInfoResource.scala b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/StreamingInfoResource.scala
new file mode 100644
index 0000000..d5fc11b
--- /dev/null
+++ b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/StreamingInfoResource.scala
@@ -0,0 +1,22 @@
+package org.apache.spark.streaming.status.api.v1
+
+import org.apache.spark.status.api.v1.SimpleDateParam
+import org.apache.spark.status.api.v1.UIRoot
+
+import javax.ws.rs.GET
+import javax.ws.rs.Produces
+import javax.ws.rs.core.MediaType
+import org.apache.spark.streaming.StreamingContext
+import org.apache.spark.streaming.ui.StreamingJobProgressListener
+
+@Produces(Array(MediaType.APPLICATION_JSON))
+private[v1] class StreamingInfoResource(uiRoot: UIRoot, listener: StreamingJobProgressListener){
+
+  @GET
+  def streamingInfo()
+  :Iterator[StreamingInfo]={
+    var v = listener.numTotalCompletedBatches
+    Iterator(new StreamingInfo("testname",v))
+
+  }
+}
\ No newline at end of file
diff --git a/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/api.scala b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/api.scala
new file mode 100644
index 0000000..958dd41
--- /dev/null
+++ b/streaming/src/main/scala/org/apache/spark/streaming/status/api/v1/api.scala
@@ -0,0 +1,6 @@
+package org.apache.spark.streaming.status.api.v1
+
+class StreamingInfo private[streaming](
+    val name:String,
+    val completedBatchCount:Long)
+
\ No newline at end of file
diff --git a/streaming/src/main/scala/org/apache/spark/streaming/ui/StreamingTab.scala b/streaming/src/main/scala/org/apache/spark/streaming/ui/StreamingTab.scala
index bc53f2a..877abf4 100644
--- a/streaming/src/main/scala/org/apache/spark/streaming/ui/StreamingTab.scala
+++ b/streaming/src/main/scala/org/apache/spark/streaming/ui/StreamingTab.scala
@@ -22,6 +22,7 @@ import org.apache.spark.streaming.StreamingContext
 import org.apache.spark.ui.{SparkUI, SparkUITab}

 import StreamingTab._
+import org.apache.spark.streaming.status.api.v1.StreamingApiRootResource

 /**
  * Spark Web UI tab that shows statistics of a streaming job.
@@ -39,6 +40,9 @@ private[spark] class StreamingTab(val ssc: StreamingContext)
   ssc.sc.addSparkListener(listener)
   attachPage(new StreamingPage(this))
   attachPage(new BatchPage(this))
+
+  //register streaming api
+ parent.attachHandler(StreamingApiRootResource.getServletHandler(parent,listener));

   def attach() {
     getSparkUI(ssc).attachTab(this)
