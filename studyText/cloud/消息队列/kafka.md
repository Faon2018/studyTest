# kafka

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

   

