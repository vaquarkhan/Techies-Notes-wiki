**1. Event Processing Model**

- Spark: Mini-batch Model. It cuts event stream into small batches, and process each batch in sequence. Each batch and event processing latency could be several seconds to minutes.

- Flink: Streaming Model. It handles event stream in realtime, and process each event immediately when it arrives. The event processing latency will be very small and could be milliseconds.

**2. Data Shuffling Mode**

- Spark: Pull Mode. It saves shuffled data into disk at the end of each stage, which is the minimal compute unit without data shuffling. Then next stage reads those data from previously saved nodes.

- Flink: Push Mode. It shuffles data and sends them to the other nodes for further processing. Again, this push mode will reduce latency in event processing.

**3. Window Function**

- Spark: Basic Support. Spark RDD and Structured Streaming support basic window functions like sliding window, but do not support session window. The Structured Stream does not support custom event eviction yet.

- Flink: Advanced Support. Flink has well designed window function implementation, and supports both sliding window and session window. It is very close to Google's DataFlow Model. It also supports custom event eviction using Evictor.

**4. State Storage**

- Spark: In Memory with Disk Checkpoint. Spark saves state in its RDD structure, and checkpoints data periodically into disk. Users could not choose different storages.

- Flink: Pluggable State Storage. Flink has more flexible design with state storage. It is pluggable with three system provided backends: MemoryStateBackend, FsStateBackend and RocksDBStateBackend.

**5. Job Upgrade**

- Spark: Not Support Upgrade/Resume. Spark's checkpoint is binding with the job DAG (Directed Acyclic Graph). Old checkpoint will not be compatible with new job after its code or DAG gets updated. So people need to do a fresh start wheneven they change Spark job code, which usually means resetting Kafka offset to either oldest and latest (if using Kafka).

- Flink: Support Upgrade/Resume. Flink allows people to upgrade their job code and resume running from the "savepoint" when possible. The reason is Flink's savepoint and job are not strictly correlated.


**6. Maturity**

- Spark: More Mature. Spark has been widely used and tested. Its RDD implementation is proved to work well in terms of scalability and fault tolerance.

- Flink: Less Mature. Flink is still relative new comparing with Spark. It may still need quite some work regarding fault tolerance and edge cases.

In summary, Flink has lower latency and advanced window function implementation, while Spark has mature implementation and wider usage.

If you have to choose from one of them, the suggestion is to check your realtime requirement and developer resource. If you really need milliseconds level latency, and have strong developer team, Flink will be your choice, otherwise Spark is recommended.