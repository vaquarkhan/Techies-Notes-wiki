Java serialization: By default, Spark serializes objects using Java’s ObjectOutputStream framework, and can work with any class you create that implements java.io.Serializable. You can also control the performance of your serialization more closely by extending java.io.Externalizable.

Java serialization is flexible but often quite slow, and leads to large serialized formats for many classes.

·       Kryo serialization: Spark can also use the Kryo library (version 2) to serialize objects more quickly. Kryo is significantly faster and more compact than Java serialization (often as much as 10x.

You can switch to using Kryo by initializing your job with a SparkConf and calling conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer"). 

 This setting configures the serializer used for not only shuffling data between worker nodes but also when serializing RDDs to disk.

You can switch to using Kryo by initializing your job with a SparkConf object.

      conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")

This setting configures the serializer used for not only shuffling data between worker nodes but also when serializing RDDs to disk. Another requirement for Kryo serializer is to register the classes in advance for best performance. If the classes are not registered, then the kryo would store the full class name with each object (instead of mapping with an ID), which can lead to wasted resource.

       conf.set("spark.kryo.registrator", “com.art.spark.AvroKyroRegistrator");

