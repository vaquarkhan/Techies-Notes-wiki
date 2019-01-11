I am trying to perform a POC between Kafka 0.10 and Spark 2.0.2. Currently I
am running into an issue, where only one executor ("kafka consumer") is
reading from the topic. Which is causing performance to be really poor. I
have tried adding "--num-executors 8" both in the script to execute the jar
and in my java code. Here is the code below. Please let me know if I am
missing something or there is a way to increase the number of consumers to
connect to kafka.


Thanks,
Gabe

<code>
Map<String, Object> kafkaParams = new HashMap<>();
                kafkaParams.put( "bootstrap.servers", "server:9092" );
                kafkaParams.put( "key.deserializer", StringDeserializer.class );
                kafkaParams.put( "value.deserializer", StringDeserializer.class );
                kafkaParams.put( "group.id", "spark-aggregation" );
                kafkaParams.put( "auto.offset.reset", "earliest" );
                kafkaParams.put( "request.timeout.ms", "305000" );
                kafkaParams.put( "heartbeat.interval.ms", "85000" );
                kafkaParams.put( "session.timeout.ms", "90000" );

                Collection<String> topics = Arrays.asList( "Topic" );

                SparkConf sparkConf = new SparkConf().setMaster( "spark://server:7077" )
                                .setAppName( "aggregation" ).set( "spark.submit.deployMode", "cluster" )
                                .set( "spark.executor.instances", "16" );

                JavaStreamingContext javaStreamingContext = new JavaStreamingContext(
sparkConf, new Duration( 5000 ) );

                //Creates connect to the Stream.....
                final JavaInputDStream<ConsumerRecord&lt;String, String>> stream =
KafkaUtils.createDirectStream(
                                javaStreamingContext, LocationStrategies.PreferConsistent(),
                                ConsumerStrategies.<String, String> Subscribe( topics, kafkaParams ) );

                //JavaPairDStream<String, String> unifiedStream =
javaStreamingContext.union(kafkaStreams.get(0), kafkaStreams.subList(1,
kafkaStreams.size()));

                JavaDStream<String> records = stream.map( new
Function<ConsumerRecord&lt;String, String>, String>() {

                        private static final long serialVersionUID = 1L;

                        @Override
                        /**
                         * Pulling key from the stream and creating the aggregation key.
                         */
                        public String call( ConsumerRecord<String, String> record ) {


                                return record.key();

                        }
                } );

                JavaPairDStream<String, Integer> pairs = records.mapToPair( new
PairFunction<String, String, Integer>() {

                        private static final long serialVersionUID = 1L;

                        @Override
                        /**
                         * Creating new tuple to perform calculations on.
                         */
                        public Tuple2<String, Integer> call( String s ) {

                                return new Tuple2<>( s, 1 );
                        }
                } );

                JavaPairDStream<String, Integer> counts = pairs.reduceByKey( new
Function2<Integer, Integer, Integer>() {

                        private static final long serialVersionUID = 1L;

                        @Override
                        /**
                         * perform counts...
                         */
                        public Integer call( Integer i1, Integer i2 ) {

                                return i1 + i2;
                        }
                } );

                stream.foreachRDD( new VoidFunction<JavaRDD&lt;ConsumerRecord&lt;String,
String>>>() {

                        /**
                        *
                        */
                        private static final long serialVersionUID = 1L;

                        @Override
                        public void call( JavaRDD<ConsumerRecord&lt;String, String>> rdd ) {

                                OffsetRange[] offsetRanges = ( (HasOffsetRanges) rdd.rdd()
).offsetRanges();

                                // some time later, after outputs have completed
                                ( (CanCommitOffsets) stream.inputDStream() ).commitAsync( offsetRanges
);
                        }
                } );
</code>







* http://spark.apache.org/docs/latest/streaming-kafka-integration.html
* https://www.confluent.io/blog/stream-data-platform-2/
* http://www.bluedata.com/blog/2016/02/real-time-data-pipelines-spark-kafka-cassandra-on-docker/
* https://rahuldausa.wordpress.com/2014/10/15/real-time-analytics-with-apache-kafka-and-spark/
