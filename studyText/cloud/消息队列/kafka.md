# kafkahttps://blog.csdn.net/taoy86/article/details/84335929

一个分布式的流平台。

1. kafka作为一个集群运行在一个或多个服务器上。

2. kafka集群存储的消息是以topic为类别记录的。

3. 每个消息（也叫记录record，我习惯叫消息）是由一个key，一个value和时间戳构成。

   ### kafka有四个核心API：

   - 应用程序使用 [Producer API]() 发布消息到1个或多个topic（主题）中。
   - 应用程序使用 [Consumer API]() 来订阅一个或多个topic，并处理产生的消息。
   - 应用程序使用 [Streams API]() 充当一个流处理器，从1个或多个topic消费输入流，并生产一个输出流到1个或多个输出topic，有效地将输入流转换到输出流。
   - [Connector API]() 可构建或运行可重用的生产者或消费者，将topic连接到现有的应用程序或数据系统。例如，连接到关系数据库的连接器可以捕获表的每个变更

   Client和Server之间的通讯，是通过一条简单、高性能并且和开发语言无关的[TCP协议](https://kafka.apache.org/protocol.html)。并且该协议保持与老版本的兼容。Kafka提供了Java Client（客户端）

   ## 基本概念

   #### Topic

   Kafka将消息分门别类，每一类的消息称之为一个主题（Topic）。

   #### Producer

   发布消息的对象称之为主题生产者（Kafka topic producer）

   #### Consumer

   订阅消息并处理发布的消息的对象称之为主题消费者（consumers）

   #### Broker

   已发布的消息保存在一组服务器中，称之为Kafka集群。集群中的每一个服务器都是一个代理（Broker）。 消费者可以订阅一个或多个主题（topic），并从Broker拉数据，从而消费这些已发布的消息。

   Topic是发布的消息的类别名，一个topic可以有零个，一个或多个消费者订阅该主题的消息。

   对于每个topic，Kafka集群都会维护一个分区log，就像下图中所示：

   ![screenshot](../../images/KmCudlf7DsaAVF0WAABMe0J0lv4158.png)

   每一个分区都是一个顺序的、不可变的消息队列， 并且可以持续的添加。分区中的消息都被分了一个序列号，称之为偏移量(offset)，在每个分区中此偏移量都是唯一的。

   Kafka集群保持所有的消息，直到它们过期（无论消息是否被消费）。实际上消费者所持有的仅有的元数据就是这个offset（偏移量），也就是说offset由消费者来控制：正常情况当消费者消费消息的时候，偏移量也线性的的增加。但是实际偏移量由消费者控制，消费者可以将偏移量重置为更早的位置，重新读取消息。可以看到这种设计对消费者来说操作自如，一个消费者的操作不会影响其它消费者对此log的处理。

   Kafka中采用分区的设计有几个目的。一是可以处理更多的消息，不受单台服务器的限制。Topic拥有多个分区意味着它可以不受限的处理更多的数据。第二，分区可以作为并行处理的单元。

   为了更好的做负载均衡，Kafka尽量将所有的Partition均匀分配到整个集群上。一个典型的部署方式是一个Topic的Partition数量大于Broker的数量。同时为了提高Kafka的容错能力，也需要将同一个Partition的Replica尽量分散到不同的机器。实际上，如果所有的Replica都在同一个Broker上，那一旦该Broker宕机，该Partition的所有Replica都无法工作，也就达不到HA的效果。同时，如果某个Broker宕机了，需要保证它上面的负载可以被均匀的分配到其它幸存的所有Broker上。
   　　Kafka分配Replica的算法如下：

   1. 将所有Broker（假设共n个Broker）和待分配的Partition排序
   2. 将第i个Partition分配到第（i mod n）个Broker上
   3. 将第i个Partition的第j个Replica分配到第（(i + j) mod n）个Broker上

   ### 分布式(Distribution)

   Log的分区被分布到集群中的多个服务器上。每个服务器处理它分到的分区。 根据配置每个分区还可以复制到其它服务器作为备份容错。 每个分区有一个leader，零或多个follower。Leader处理此分区的所有的读写请求，而follower被动的复制数据。如果leader宕机，其它的一个follower会被推举为新的leader。 一台服务器可能同时是一个分区的leader，另一个分区的follower。 这样可以平衡负载，避免所有的请求都只让一台或者某几台服务器处理。

   ### Propagate消息

   　　Producer在发布消息到某个Partition时，先通过 Metadata （通过 Broker 获取并且缓存在 Producer 内） 找到该 Partition 的Leader，然后无论该Topic的Replication Factor为多少（也即该Partition有多少个Replica），Producer只将该消息发送到该Partition的Leader。Leader会将该消息写入其本地Log。每个Follower都从Leader pull数据。这种方式上，Follower存储的数据顺序与Leader保持一致。Follower在收到该消息并写入其Log后，向Leader发送ACK。一旦Leader收到了ISR中的所有Replica的ACK，该消息就被认为已经commit了，Leader将增加HW并且向Producer发送ACK。
   为了提高性能，每个Follower在接收到数据后就立马向Leader发送ACK，而非等到数据写入Log中。因此，对于已经commit的消息，Kafka只能保证它被存于多个Replica的内存中，而不能保证它们被持久化到磁盘中，也就不能完全保证异常发生后该条消息一定能被Consumer消费。但考虑到这种场景非常少见，可以认为这种方式在性能和数据持久化上做了一个比较好的平衡。在将来的版本中，Kafka会考虑提供更高的持久性。
   Consumer读消息也是从Leader读取，只有被commit过的消息（offset低于HW的消息）才会暴露给Consumer。

   ### Geo-Replication(异地数据同步技术)

   Kafka MirrorMaker为群集提供`geo-replication`支持。借助`MirrorMaker`，消息可以跨多个数据中心或云区域进行复制。 您可以在active/passive场景中用于备份和恢复; 或者在active/passive方案中将数据置于更接近用户的位置，或数据本地化。

   ### 生产者(Producers)

   生产者往某个Topic上发布消息。生产者也负责选择发布到Topic上的哪一个分区。最简单的方式从分区列表中轮流选择。也可以根据某种算法依照权重选择分区。开发者负责如何选择分区的算法。

   ### 消费者(Consumers)

   通常来讲，消息模型可以分为两种， 队列和发布-订阅式。 队列的处理方式是 一组消费者从服务器读取消息，一条消息只有其中的一个消费者来处理。在发布-订阅模型中，消息被广播给所有的消费者，接收到消息的消费者都可以处理此消息。Kafka为这两种模型提供了单一的消费者抽象模型： 消费者组 （consumer group）。 消费者用一个消费者组名标记自己。 一个发布在Topic上消息被分发给此消费者组中的一个消费者。 假如所有的消费者都在一个组中，那么这就变成了queue模型。 假如所有的消费者都在不同的组中，那么就完全变成了发布-订阅模型。 更通用的， 我们可以创建一些消费者组作为逻辑上的订阅者。每个组包含数目不等的消费者， 一个组内多个消费者可以用来扩展性能和容错。



```
docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2 --name kafka  -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=47.115.34.249:2181/kafka  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT:47.115.34.249:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092  -v /etc/localtime:/etc/localtime wurstmeister/kafka
```