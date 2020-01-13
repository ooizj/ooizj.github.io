# spring-integration-kafka-1.3.0.RELEASE

source [https://github.com/spring-projects/spring-integration-kafka/tree/v1.3.0.RELEASE](https://github.com/spring-projects/spring-integration-kafka/tree/v1.3.0.RELEASE)

## Spring Integration Kafka Adapter

The _Spring Integration Kafka Adapter_ provides client components for Apache Kafka. Apache Kafka is a distributed publish-subscribe messaging system that is designed for high throughput \(terabytes of data\) and low latency \(miliseconds\). For more information on Kafka and its design goals, see [Kafka main page](http://kafka.apache.org/).

spring集成kafka为其提供客户端组件。kafka是一个为了高通量（TB级）低延迟（毫秒级）设计的分布式publish-subscribe消息系统。关于kafka的更多信息参考[Kafka main page](http://kafka.apache.org/)。

In particular, Spring Integration Kafka provides:

spring集成kafka提供：

* A consumer client library based on the Simple Consumer API with the following features:
* 一个consumer客户端基于“Simple Consumer API”具有下面的属性：
  * Dependency injection-friendly `ConnectionFactory` for infrastructure management;
  * 依赖injection-friendly`ConnectionFactory` 基础管理；
  * `KafkaTemplate` for broker read operations, following the general Spring Template model, with the ability of reading from specific partitions and offsets;
  * `KafkaTemplate`用于broker读取操作，遵循spring模板设计，从指定partitions和offsets读取能力；
  * Message-driven `KafkaMessageListenerContainer` with support for:
  * Message-driven `KafkaMessageListenerContainer` 支持：
    * Listening to specific partitions and starting offsets;
    * 监听指定partitions和offsets；
    * Customizing offset management via the `OffsetManager` abstraction, with the ability of choosing between various offset storage and update strategies;
    * 自定义offset管理通过`OffsetManager`抽象，具有在各种offset存储和更新策略之前选择的功能；
    * Manual acknowledgment of offsets for asynchronous operation;
    * 手动确认offsets以进行异步操作；
* Inbound and outbound channel adapters for Spring Integration
* spring集成了inbound和outbound channel adapters

### Quick Start

See the [Spring Integration kafka Sample](https://github.com/spring-projects/spring-integration-samples/tree/master/basic/kafka) for a simple Spring Boot application that sends and receives messages.

发送和接收消息的简单spring boot应用[Spring Integration kafka Sample](https://github.com/spring-projects/spring-integration-samples/tree/master/basic/kafka)。

### Checking out and building

Currently Spring Integration Kafka adapter is built against Kafka 0.8.2.1 that is backed by Scala 2.10.4.

In order to build the project:

当前spring集成kafka是构建基于由Scala 2.10.4支持的Kafka 0.8.2.1。

构建项目：

```text
./gradlew build
```

In order to install this into your local maven cache:

安装到本地maven缓存：

```text
./gradlew install
```

Spring Integration Kafka project currently supports the following components. Please keep in mind that this is very early stage in development and do not yet fully make use of all the features that Kafka provides.

spring集成kafka项目当前支持下面的组件。请记住这是开发早期还没有完全用上kafka的所有功能。

* Outbound Channel Adapter
* Outbound Channel Adapter
* Message Driven Channel Adapter based on the simple consumer API
* Message Driven Channel Adapter基于“simple consumer API”
* Inbound Channel Adapter based on the High level consumer API
* Inbound Channel Adapter基于“High level consumer API”

### Outbound Channel Adapter:

The Outbound channel adapter is used to publish messages from a Spring Integration channel to Kafka. The channel is defined in the application context and then wired in the application that sends messages to Kafka. After that, sender applications can publish to Kafka via Spring Integration messages, which are internally converted to Kafka messages by the outbound channel adapter, as follows: the payload of the Spring Integration message will be used to populate the payload of the Kafka message, and the `kafka_messageKey` header of the Spring Integration message will be used to populate the key of the Kafka message.

The Outbound channel adapter是用于从spring集成通道发送消息到kafka。通道是定义在application context。之后，发送应用可以通过spring Integration消息发送到kafka，这些spring Integration消息在内部由outbound channel adapter转换到kafka消息，如下：Spring Integration消息的payload将被用作填充kafka消息的payload，spring Integration消息的`kafka_messageKey`头将被用于填充kafka消息的key。

The target topic and partition for publishing the message can be customized through the `kafka_topic` and `kafka_partitionId` headers, respectively.

用于发送消息的目标topic和partition可以被分别通过`kafka_topic`和`kafka_partitionId`头自定义。

Here's an example for sending a message with an arbitrary payload and the String `"key"` as value on the `test` topic.  
这里有个发送消息的例子，一个任意的payload、主题`test`、key的`"key"`。

```java
final MessageChannel channel = ctx.getBean("inputToKafka", MessageChannel.class);

    channel.send(
            MessageBuilder.withPayload(payload)
                    .setHeader(KafkaHeaders.MESSAGE_KEY, "key")  // Note: the header was `messageKey` in earlier versions
                    .setHeader(KafkaHeaders.TOPIC, "test")       // Note: the header was `topic` in earlier versions
                    .build()
            );
```

In addition, the `<int-kafka:outbound-channel-adapter>` provides the ability to extract the key, target topic, and target partition by applying SpEL expressions on the outbound message. To that end, it supports the mutually exclusive pairs of attributes `topic`/`topic-expression`, `message-key`/`message-key-expression`, and `partition-id`/`partition-id-expression`, to allow the specification of `topic`,`message-key` and `partition-id` respectively as static values on the adapter, or to dynamically evaluate their values at runtime against the request message.

此外`<int-kafka:outbound-channel-adapter>`提供在outbound message由使用SpEL表达式获取key、目标topic和目标partition的能力。为此，它支持`topic`/`topic-expression`, `message-key`/`message-key-expression`, 和`partition-id`/`partition-id-expression`属性互斥对儿，允许`topic`,`message-key` 和`partition-id`分别指定为适配器上的静态值，或者在运行时根据请求消息动态执行这些值。

**Important. The `KafkaHeaders` interface contains constants used for interacting with headers. The `messageKey` and `topic` default headers now require a `kafka_` prefix. When migrating from an earlier version that used the old headers, you need to specify `message-key-expression="headers.messageKey"` and `topic-expression="headers.topic"` on the `<int-kafka:outbound-channel-adapter>`, or simply change the headers upstream to the new headers from `KafkaHeaders` using a `<header-enricher>` or `MessageBuilder`. Or, of course, configure them on the adapter if you are using constant values.**

**注意。`KafkaHeaders`接口包含用于交互的头部常量。`messageKey` 和`topic`默认头现在要求一个`kafka_`前缀。当从较早版本迁移使用旧的头，你需要指定`message-key-expression="headers.messageKey"` 和`topic-expression="headers.topic"` on the `<int-kafka:outbound-channel-adapter>`，或者仅使用`<header-enricher>`或`MessageBuilder`将`KafkaHeaders`变更为新头。或者，当然，你可以使用常量在适配器上配置它们。**

Here is how kafka outbound channel adapter is configured:

这是kafka outbound channel adapter如何配置：

```markup
<int-kafka:outbound-channel-adapter id="kafkaOutboundChannelAdapter"
                                        kafka-producer-context-ref="kafkaProducerContext"
                                        auto-startup="false"
                                        channel="inputToKafka"
                                        topic="foo"
                                        message-key-expression="header.messageKey">
        <int:poller fixed-delay="1000" time-unit="MILLISECONDS" receive-timeout="0" task-executor="taskExecutor"/>
    </int-kafka:outbound-channel-adapter>
```

The key aspect in this configuration is the producer-context-ref. The producer context contains all the producer configurations for the topics that this adapter is expected to handle. The adapter will subscribe to a channel and any message sent to that channel will be handled by this adapter. You can also configure a poller depending on the type of the channel used. For example, in the above configuration, we use a queue based channel and thus a poller is configured with a task executor. If no messages are available in the queue it will timeout immediately because of the receive-timeout configuration. Then it will poll again with a delay of 1 second.

配置中的producer-context-ref。producer context包含所有为了topics的producer配置，适配器将订阅一个通道和任何消息发送给将被这个适配器处理的通道。你也可以配置一个poller取决于使用的通道类型。如，以上配置，我们使用一个基于queue的通道，因此poller被配置了一个task executor。如果queue中没有了可用消息，将因为receive-timeout的属性配置超时。然后将在一秒后重试。

The producer context is at the heart of the kafka outbound adapter. Here is an example of how it is configured.

producer context是kafka outbound adapter的核心。这里有个如何配置的例子。

```markup
  <int-kafka:producer-context id="kafkaProducerContext">
        <int-kafka:producer-configurations>
            <int-kafka:producer-configuration broker-list="localhost:9092"
                       key-class-type="java.lang.String"
                       value-class-type="java.lang.String"
                       topic="test1"
                       value-serializer="kafkaSerializer"
                       key-serializer="kafkaSerializer"
                       compression-type="none"/>
            <int-kafka:producer-configuration broker-list="localhost:9092"
                       topic="test2"
                       compression-type="none"/>
            <int-kafka:producer-configuration broker-list="localhost:9092"
                        topic="regextopic.*"
                        compression-type="gzip"/>
        </int-kafka:producer-configurations>
    </int-kafka:producer-context>
```

There are a few things going on here. So, lets go one by one. First of all, producer context is simply a holder of, as the name indicates, a context for the Kafka producer. It contains one ore more producer configurations. Each producer configuration is ultimately gets translated into a Kafka native producer. Each producer configuration is per topic based right now. If you go by the above example, there are two producers generated from this configuration - one for topic named test1 and another for test2. Each producer can take the following:

这里有些事情。因此，让我们一步步看。首先，producer context是简单的一个holder，就像它的名字，一个Kafka producer的context。它包含一个或多个producer配置。每个producer配置最终会对应到kafka原生producer。每个producer配置是基于每个topic。如果你按照上面的例子，这里有两个从这个配置生成的producer-一个topic名为“test1”另一个topic名为“test2”。每个producer可以配置下面的属性：

```text
broker-list				List of comma separated brokers that this producer connects to
                producer连接到的brokers，多个用逗号分隔，如localhost:9092
topic					Topic name or Java regex pattern of topic name
                topic名字或者正则表达式形式的topic名字
compression-type		Compression method to be used. Supported compression types are `none`, `gzip` and `snappy`. Default is `none`.
                被使用的压缩方法。支持的压缩类型是`none`、`gzip`和`snappy`。默认是`none`。
value-serializer			Serializer to be used for encoding messages.
                用于消息编码的Serializer
key-serializer				Serializer to be used for encoding the partition key
                用于编码partition key的Serializer 
key-class-type			Type of the key class. This will be ignored if no key-encoder is provided
                key class。如果没有key-encoder提供，这个将被忽略
value-class-type		Type of the value class. This will be ignored if no value-encoder is provided.
                value class。如果没有value-encoder提供，这个将被忽略
partitioner				Custom implementation of a Kafka Partitioner interface.
                自定义Kafka Partitioner interface实现
batch-bytes		Number of bytes to batch at the producer. If async is false, then this has no effect.
                producer批量处理的数据大小。如果async是false，则这个就没有效果了。
```

The `value-serializer` and `key-serializer` are referring to other spring beans. They are essentially implementations of an interface provided by Kafka, the Serializer interface. Here is an example of configuring an encoder.

`value-serializer` 和`key-serializer` 是引用其他spring beans。他是kafka提供的Serializer接口的实现。这是配置一个encoder的例子。

```markup
	<bean id="kafkaSerializer" class="com.acme.KryoSerializer"/>
```

Spring Integration Kafka allows the reuse of pre-0.8.2 `Encoder`s. To do so, the attributes `key-encoder` and `value-encoder` can be used instead of `key-serializer` and `value-serializer` respectively. For either the key or value, you can configure either a `Serializer` or an `Encoder` but not both.

spring Intergration kafka允许pre-0.8.2`Encoder`的重用。这样做，属性`key-encoder` 和`value-encoder`可以分别被`key-serializer` 和`value-serializer`替换。但是`Serializer` 或者`Encoder`不能同时配置。

Spring Integration Kafka provides Apache Avro backed encoders out of the box, as this is a popular choice for serialization in the big data spectrum. If no encoders are specified as beans, the default encoders provided by Kafka will be used. On that not, if the encoder is configured only for the message and not for the key, the same encoder will be used for both. These are standard Kafka behaviors. Spring Integration Kafka adapter does simply enforce those behaviours. Kafka default encoder expects the data to come as byte arrays and it is a no-op encoder, i.e. it just takes the byte array as it is. When default encoders are used, there are two ways a message can be sent. Either, the sender of the message to the channel can simply put byte arrays as message key and payload. Or, the key and value can be sent as Java Serializable object. In the latter case, the Kafka adapter will automatically convert them to byte arrays before sending to Kafka broker. If the encoders are default and the objects sent are not serializable, then that would cause an error. By providing explicit encoders it is totally up to the developer to configure how the objects are serialized. In that case, the objects may or may not implement the Serializable interface.

spring Intergration kafka 提供Apache Avro（Apache Avro™ is a data serialization system）encoder开箱即用，因为这是大数据序列号的流行的做法。如果没有特殊的encoder bean，kafka提供的默认encoder将被使用。否则，encoder为了消息被配置而不是key，两者都使用相同的encoder。这些是标准的kafka做法。spring Intergration kafka adapter按这些做法做。kafka默认的encoder希望数据项以字节数组一样来，它是一个没有任何操作的encoder，它仅仅需要字节数组。当默认的encoder被使用，这里有两种发送消息方式。消息发送通道可以只放字节数组作为消息key和payload。key和value可以作为Java Serializable object被发送。在后一种情况，kafka adapter在发送它们到kafka broker之前自动转换到字节数组。如果encoder是默认的，且发送对象没有序列化，就会产生错误。通过提供显示encoder，取决于开发人员配置对象如何被序列化。在那种情况下，对象可以实现Serializable接口也可以不实现。

A bit more on the Avro support. There are two flavors of Avro encoders provided, one based on the Avro ReflectDatum and the other based on SpecificDatum. The encoding using reflection is fairly simple as you only have to configure your POJO or other class types along with the XML. Here is an example.

关于更多Avro支持。这里是Avro encoder提供两种风格，一种基于Avro ReflectDatum，另一种基于Avro SpecificDatum。编码使用反射是相当简单，你仅必须配置你的POJO或者其它class类型。这是个例子。

```markup
<bean id="kafkaEncoder" class="org.springframework.integration.kafka.serializer.avro.AvroReflectDatumBackedKafkaEncoder">
        <constructor-arg value="java.lang.String" />
    </bean>
```

Reflection based encoding may not be appropriate for large scale systems and Avro's SpecificDatum based encoders can be a better fit. In this case, you can generate a specific Avro object \(a glorified POJO\) from a schema definition. The generated object will store the schema as well. In order to do this, you need to generate the Avro object separately though. There are both maven and gradle plugins available to do code generation automatically. You have to provide the avdl or avsc files to specify your schema. Once you take care of these steps, you can simply configure a specific datum based Avro encoder \(see the first example above\) and pass along the fully qualified class name of the generated Avro object for which you want to encode instances. The samples project has examples of using both of these encoders.

基于反射的编码方式或许对于大型系统不好，基于Avro's SpecificDatum的encoder更合适些。在这种情况下，你可以从schema定义生成一个特别的Avro对象（a glorified POJO）。生成对象也将保存schema。为此，你需要单独生成Avro对象，maven和gradle都有能自动生成这些代码的插件。你可以简单的配置一个具体的日期基于Avro encoder（参考上面第一个例子）传递生成Avro对象的全类名为了你要编码的实例。样例工程有所有这些encoder的例子。

Encoding String for key and value is a very common use case and Kafka provides a StringEncoder out of the box. It takes a Kafka specific VerifiableProperties object along with its constructor that wraps a regular Java.util.Properties object. The StringEncoder is great when writing a direct Java client that talks to Kafka. However, when using Spring Integration Kafka adapter, it introduces unnecessary steps to create these properties objects. Therefore, we provide a wrapper class for this same StringEncoder as part of the SI kafka support, which makes using it from Spring a bit easier. You can inject any properties to it in the Spring way. Kafka StringEncoder looks at a specific property for the type of encoding scheme used. In the wrapper bean provided, this property can simply be injected as a value without constructing any other objects. Spring Integration provided StringEncoder is available in the package org.springframework.integration.kafka.serializer.common.StringEncoder. The avro support for serialization is also available in a package called avro under serializer.

为key和value进行字符串编码是很常用的，kafka提供了一个StringEncoder拆开即用。它需要一个kafka特定VerifiableProperties对象，以及包装了一个Java.util.Properties对象的构造方法。当编写一个直接与kafka通信的客户端时StringEncoder是很好的。然而，当用spring Intergration kafka adapter，它引入了不必要的步骤去创建这些属性对象。因此，我们提供了一个包装类StringEncoder来作为支持kafka的一部分，这样在spring中使用它更简单些，通过提供的包装bean你可以注入任何属性到它里面，这个属性可以简单的作为值被注入，而不需要构造任何其他对象。spring Intergration提供的在org.springframework.integration.kafka.serializer.common.StringEncoder。avro序列化的支持也已经可用，在serializer下名为avro的包中。 

**Tuning Producer Properties**

Kafka Producer API provides several \[Producer Configs\] \([http://kafka.apache.org/documentation.html\#producerconfigs](http://kafka.apache.org/documentation.html#producerconfigs)\) to fine-tune producers. To specify those properties, `producer-context` element supports optional `producer-properties` attribute that can reference the Spring properties bean. These properties will be applied to all Producer Configurations within the producer context. For example:

Kafka Producer API 提供一些\[Producer Configs\] \([http://kafka.apache.org/documentation.html\#producerconfigs](http://kafka.apache.org/documentation.html#producerconfigs)\)配置producer。声明的属性，`producer-context`元素支持可选`producer-properties` 属性，可以引用spring properties bean。这些属性将由producer context被应用到所有producer配置。如下例子：

```markup
<bean id="producerProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">
        <property name="properties">
          <props>
            <prop key="topic.metadata.refresh.interval.ms">3600000</prop>
                    <prop key="message.send.max.retries">5</prop>
                    <prop key="send.buffer.bytes">5242880</prop>
          </props>
        </property>
    </bean>

    <int-kafka:producer-context id="kafkaProducerContext" producer-properties="producerProperties">
        <int-kafka:producer-configurations>
            <int-kafka:producer-configuration ... > ... </int-kafka:producer-configuration>
            <int-kafka:producer-configuration ... > ... </int-kafka:producer-configuration>
            ...
        <int-kafka:producer-configurations>
    </int-kafka:producer-context>
```

### Inbound Channel Adapter:

The Inbound channel adapter is used to consume messages from Kafka. These messages will be placed into a channel as Spring Integration specific Messages. Kafka provides two types of consumer API's primarily. One is called the High Level Consumer and the other is the Simple Consumer. High Level consumer is pretty complex inside. Nonetheless, for the client, using the high level API is straightforward. Although easy to use, High level consumer does not provide any offset management. So, if you want to rewind and re-fetch messages, it is not possible to do so using the High Level Consumer API. Offsets are managed by the Zookeeper internally in the High Level Consumer. If your use case does not require any offset management or re-reading messages from the same consumer, then high level consumer is a perfect fit. Spring Integration Kafka inbound channel adapter currently supports only the High Level Consumer. Here are the details of configuring one.

Inbound channel adapter被用作从kafka消费消息。这些消息将被作为spring Intergration特别的消息放到一个通道。kafka主要提供两种consumer API。一个叫做High Level Consumer，另一个叫做Simple Consumer。High Level consumer内部相当复杂。尽管如此，在客户端，使用high level API更直接。虽然容易用，High level consumer不提供任何offset管理。因此如果你想要倒回去重新获取消息，就是不可能做到的了。offset被Zookeeper内部管理在High Level Consumer。如果你不需要任何offset管理或者重新从同一consumer读取消息，那么high level consumer是合适的。spring Intergration Kafka inbound channel adapter当前支持仅High Level Consumer。这里是一个配置详情。

```markup
<int-kafka:inbound-channel-adapter id="kafkaInboundChannelAdapter"
                                           kafka-consumer-context-ref="consumerContext"
                                           auto-startup="false"
                                           channel="inputFromKafka">
            <int:poller fixed-delay="10" time-unit="MILLISECONDS" max-messages-per-poll="5"/>
    </int-kafka:inbound-channel-adapter>
```

Since this inbound channel adapter uses a Polling Channel under the hood, it must be configured with a Poller. A notable difference between the poller configured with this inbound adapter and other pollers used in Spring Integration is that the receive-timeout specified on this poller does not have any effect. The reason for this is because of the way Kafka implements iterators on the consumer stream. It is using a BlockingQueue internally and thus it would wait indefinitely. Instead of interrupting the underlying thread, we are leveraging a direct Kafka support for consumer time out. It is configured on the consumer context. Everything else is pretty much the same as in a regular inbound adapter. Any message that it receives will be sent to the channel configured with it.

自从inbound channel adapter在后台使用一个轮询Channel，它就必须配置一个Poller。在inbound adapter下面配置的poller与其他在spring集成中用到的poller的明显区别是这里的receive-timeout属性是无效的。这是因为kafka在consumer stream实现iterators的方法是使用了一个内部的BlockingQueue，那样会造成一直等待。而不是中断线程，我们直接使用kafka提供的consumer time out。它是配置在consumer context。其他与inbound adapter基本相同。任何它收到的消息都将发送到配置的通道。

Inbound Kafka Adapter must specify a kafka-consumer-context-ref element and here is how it is configured:

Inbound Kafka Adapter必须指定一个kafka-consumer-context-ref元素，这里有个配置例子：

```markup
<int-kafka:consumer-context id="consumerContext"
                                   consumer-timeout="4000"
                                   zookeeper-connect="zookeeperConnect">
           <int-kafka:consumer-configurations>
               <int-kafka:consumer-configuration group-id="default"
                       value-decoder="valueDecoder"
                       key-decoder="valueDecoder"
                       max-messages="5000">
                   <int-kafka:topic id="test1" streams="4"/>
                   <int-kafka:topic id="test2" streams="4"/>
               </int-kafka:consumer-configuration>
               <int-kafka:consumer-configuration group-id="default3"
                        value-decoder="kafkaSpecificDecoder"
                        key-decoder="kafkaReflectionDecoder"
                        max-messages="10">
                   <int-kafka:topic-filter pattern="regextopic.*" streams="4" exclude="false"/>
               </int-kafka:consumer-configuration>
           </int-kafka:consumer-configurations>
   </int-kafka:consumer-context>
```

`consumer-configuration` supports consuming from specific topic using a `topic` child element or from multiple topics matching a topic regex using `topic-filter` child element. `topic-filter` supports both whitelist and blacklist filter based on `exclude` attribute.

`consumer-configuration` 支持用`topic`子节点指定消费的topic，或用正则表达式`topic-filter`子节点多个topic。`topic-filter` 白名单和黑名单都支持，用`exclude`属性即可。

Consumer context requires a reference to a zookeeper-connect which dictates all the zookeeper specific configuration details. Here is how a zookeeper-connect is configured.

Consumer context要求一个到zookeeper-connect的引用，zookeeper-connect决定了zookeeper配置信息。下面有个配置例子。

```markup
 <int-kafka:zookeeper-connect id="zookeeperConnect" zk-connect="localhost:2181" zk-connection-timeout="6000"
                        zk-session-timeout="6000"
                        zk-sync-time="2000" />
```

zk-connect attribute is where you would specify the zookeeper connection. All the other attributes get translated into their zookeeper counter-part attributes by the consumer.

zk-connect属性是用来指定zookeeper的连接。所有其他属性会转换到zookeeper属性。

In the above consumer context, you can also specify a consumer-timeout value which would be used to timeout the consumer in case of no messages to consume. This timeout would be applicable to all the streams \(threads\) in the consumer. The default value for this in Kafka is -1 which would make it wait indefinitely. However, Sping Integration overrides it to be 5 seconds by default in order to make sure that no threads are blocking indefinitely in the lifecycle of the application and thereby giving them a chance to free up any resources or locks that they hold. It is recommended to override this value so as to meet any specific use case requirements. By providing a reasonable consumer-timeout on the context and a fixed-delay value on the poller, this inbound adapter is capable of simulating a message driven behaviour.

在consumer context，你可以用conumser-timeout指定在没有消息的情况下超时时间。这个超时将应用到consumer中所有的stream（threads）。kafka中对应的默认值是“-1”，这回是的一直等待。而然，spring Intergration默认会将其改为“5秒”，为了避免在应用生命周期中有线程一直阻塞，好让它们有机会释放资源或者锁住它们持有的资源。推荐根据使用要求来修改这个值。在poller提供合理的consumer-timer和fixed-delay，这个inbound adapter有模拟消息驱动那样的能力。

consumer context takes consumer-configurations which are at the core of the inbound adapter. It is a group of one or more consumer-configuration elements which consists of a consumer group dictated by the group-id. Each consumer-configuration can be configured with one or more kafka-topics.

consumer context采用位于inbound adapter核心的consumer-configurations。它是一组一个或多个consumer-configuration元素，那是一个由group-id决定的consumer group组成的。每个consumer-configuration可以配置一个或多个kafka-topic。

In the above example provided, we have a single consumer-configuration that consumes messages from two topics each having 4 streams. These streams are fundamentally equivalent to the number of partitions that a topic is configured with in the producer. For instance, if you configure your topic with 4 partitions, then the maximum number of streams that you may have in the consumer is also 4. Any more than this would be a no-op. If you have less number of streams than the available partitions, then messages from multiple partitions will be sent to available streams. Therefore, it is a good practice to limit the number of streams for a topic in the consumer configuration to the number of partitions configured for the topic. There may be situations in which a partition may be gone during runtime and in that case the stream receiving data from the partition will simply timeout and whenever this partition comes back, it would start read data from it again.

上面提供的例子，我们有一个单独consumer-configuration，从两个topic消费消息有4个stream。这些stream基本等于producer中主题配置partition的数量。例如，如果你配置你的topic为4个partition，则consumer中stream的最大数量也是4，不能更多了。如果你stream数量少于可用partition，则来自多个partition的消息将被发送到可用stream。因此，最好限制在consumer配置topic的stream数量为topic配置的partition数量。可能的情况是一个partition在运行时没了，此时从该partition接收数据的stream将只是超时，这个分区又被创建了的话又会读取数据。

Consumer configuration can also be configured with optional decoders for key and value. The default decoders provided by Kafka are basically no-ops and would consume as byte arrays. If you provide a custom encoder for key/value in the producer, then it is recommended to provide corresponding decoders for the consumer. As discussed already in the outbound adapter, Spring Integration Kafka adapter gives Apache Avro based data serialization components out of the box. You can use any serialization component for this purpose as long as you implement the required encoder/decoder interfaces from Kafka. As with the Avro encoder support, decoders provided also implement Reflection and Specific datum based de-serialization. Here is how you would configure kafka decoder beans that is Avro backed.

consumer配置也可以由可选的key decoder和value decoder配置。默认的decoder由kafka提供的基本上无操作，将像字节数组一样消费。如果你提供了一个在producer自定义key/value encoder，则它为consumer推荐提供相应的decoder。讨论过的outbound adapter，spring Intergration kafka adapter提供基于apache Avro数据序列化组件开箱即用。你可以用任意序列化组件来实现这个功能，只要你实现了kafka encoder/decoder接口。与Avro encoder支持一样，decoder也提供了实现基于反射和特定数据的反序列化。这里是Avro支持的你可以如何配置kafka decoder bean。

### Message Driven Channel Adapter:

The `KafkaMessageDrivenChannelAdapter` \(`<int-kafka:message-driven-channel-adapter>`\) uses the Kafka `SimpleConsumer`\([https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+SimpleConsumer+Example](https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+SimpleConsumer+Example)\) internally. Although it is called 'simple', the API and usage is not so simple. To simplify the configuration and provide a higher-level API based on Spring Integration concepts, the `KafkaMessageListenerContainer` has been introduced. It supports 'leader election' with `org.springframework.integration.kafka.core.ConnectionFactory` and 'offset management' with `org.springframework.integration.kafka.listener.OffsetManager` abstractions. The `DefaultConnectionFactory` requires `org.springframework.integration.kafka.core.Configuration` for Kafka. `ZookeeperConfiguration` and `BrokerAddressListConfiguration` are presented as configuration options.

`KafkaMessageDrivenChannelAdapter` \(`<int-kafka:message-driven-channel-adapter>`\) 使用Kafka内部 `SimpleConsumer`\([https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+SimpleConsumer+Example](https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+SimpleConsumer+Example)\) . 也叫 'simple', API和用法却不简单。基于spring Intergration精简配置和提供higher-level API，`KafkaMessageListenerContainer` 被介绍. 它通过 `org.springframework.integration.kafka.core.ConnectionFactory`支持 'leader election'，通过抽象的`org.springframework.integration.kafka.listener.OffsetManager` 支持'offset management' 。`DefaultConnectionFactory`需要Kafka的`org.springframework.integration.kafka.core.Configuration` 。`ZookeeperConfiguration` 和`BrokerAddressListConfiguration` 给出了配置选项。

`KafkaMessageDrivenChannelAdapter` 实现`MessageProducer`,读取一个带有`Metadata`的`KafkaMessage` ，像spring Integration消息发送到提供的 `MessageChannel`。`MessageDrivenChannelAdapter`配置需要`KafkaMessageListenerContainer` 或`ConnectionFactory` 和`topics` 对儿。基于java典型配置如下:

```java
@Bean
public Configuration zkConfiguration() {
   return new ZookeeperConfiguration(new ZookeeperConnect());
}

@Bean
public ConnectionFactory kafkaConnectionFactory() {
   return new DefaultConnectionFactory(zkConfiguration());
}

@Bean
public MessageProducer kafkaMessageDrivenChannelAdapter() {
   KafkaMessageDrivenChannelAdapter adapter = new KafkaMessageDrivenChannelAdapter(
   					new KafkaMessageListenerContainer(kafkaConnectionFactory(), "topic1", "topic2")
   					);
   adapter.setOutputChannel(inputChannel());
   return adapter;
}
```

As a variant, the `KafkaMessageListenerContainer` can accept `org.springframework.integration.kafka.core.Partition` array argument to specify topics and their partitions pair.

作为参数，`KafkaMessageListenerContainer`可以接受`org.springframework.integration.kafka.core.Partition`数组参数指明topic和partition对儿。

The xml configuration variant is typical too:

xml配置也很典型：

```markup
<int-kafka:message-driven-channel-adapter
			id="adapter"
			channel="output"
			connection-factory="connectionFactory"
			key-decoder="decoder"
			payload-decoder="decoder"
			offset-manager="offsetManager"
			max-fetch="100"
			topics="${kafka.test.topic}"/>
```

Where `offsetManager` is a bean that is an implementation of `org.springframework.integration.kafka.listener.OffsetManager`. The default implementation is `MetadataStoreOffsetManager`, which is based on the `MetadataStore` to store and fetch `offsets` under the key based on the provided `Partition` and preconfigured `consumerId` option. The `KafkaMessageListenerContainer` takes care about `offsets` management during its internal process. Another implementation is `KafkaTopicOffsetManager` to free application from any other external system like Redis for the `MetadataStoreOffsetManager`.

`org.springframework.integration.kafka.listener.OffsetManager`默认实现是`MetadataStoreOffsetManager`，它基于`MetadataStore`存储和获取`offsets`基于提供`Partition`和预配置`consumerId` 选项。`KafkaMessageListenerContainer` 在内部处理负责`offsets`管理。另一个实现是`KafkaTopicOffsetManager` 可将应用程序从Redis等任何其他内部系统中释放出来，用于`MetadataStoreOffsetManager`。

The `KafkaMessageListenerContainer` can be configured with `concurrency` to run several internal `QueueingMessageListenerInvoker` concurrent fetch tasks.

`KafkaMessageListenerContainer` 可以被`concurrency`配置运行一些内部`QueueingMessageListenerInvoker` 并发提取任务。

Refer to the `KafkaMessageDrivenChannelAdapter` and `KafkaMessageListenerContainer` JavaDocs for more information.

参考`KafkaMessageDrivenChannelAdapter` 和`KafkaMessageListenerContainer` JavaDocs。

Using Avro Specific support:

Avro具体支持：

```markup
<bean id="kafkaDecoder" class="org.springframework.integration.kafka.serializer.avro.AvroSpecificDatumBackedKafkaDecoder">
           <constructor-arg value="com.domain.AvroGeneratedSpecificRecord" />
   </bean>
```

Using Reflection support:

使用反射支持：

```markup
<bean id="kafkaDecoder" class="org.springframework.integration.kafka.serializer.avro.AvroReflectDatumBackedKafkaDecoder">
           <constructor-arg value="java.lang.String" />
   </bean>
```

Another important attribute for the consumer-configuration is the max-messages. Please note that this is different from the max-messages-per-poll configured on the inbound adapter element. There it means the number of times the receive method called on the adapter. The max-messages on consumer configuration is different. When you use Kafka for ingesting messages, it usually means an influx of large amount of data constantly. Because of this, each time a receive is invoked on the adapter, you would basically get a collection of messages. The maximum number of messages to retrieve for a topic in each execution of the receive is what configured through the max-messages attribute on the consumer-configuration. Basically, if the use case is to receive a constant stream of large number of data, simply specifying a consumer-timeout alone would not be enough. You would also need to specify the max number of messages to receive.

consumer-configuration另外一个重要属性是max-messages。请注意这个不同于inbound adapter节点的max-message-per-poll属性，max-message-per-poll是adapter上调用receive方法的次数的。consumer配置的max-messages是不同的。当你使用kafka获取消息，通常意味着大量数据不断的涌入。因此，adapter上的接收方法没调用一次，你将获取一消息集合。在执行接收的时候一个topic接收到消息的最大数量是通过consumer-configuration上的max-messages配置的。如果用来接收大量数据的固定stream，简单指定一个单独consumer-timeout就足够了。你也需要指定接收消息的最大数量。

The type of the payload of the Message returned by the adapter is the following:

adapter的返回消息payload类型是：

```java
Map<String, Map<Integer, List<Object>>>
```

It is a java.util.Map that contains the topic string consumed as the key and another Map as the value. The inner map's key is the stream \(partition\) number and value is a list of message payloads. The reason for this complex return type is due to the way Kafka orders the messages. In the high level consumer, all the messages received in a single stream for a single partition are guaranteed to be in order. For example, if I have a topic named test configured with 4 partitions and I have 4 corresponding streams in the consumer, then I would receive data in all the consumer streams in the same order as they were put in the corresponding partitions. This is another reason to set the number of consumer streams for a topic same as the number of broker partitions configured for that topic. Lets say that the number of streams are less than the number of partitions. Then, normally, there is no guarantee for any order other than just the fact that a single stream will contain messages from multiple partitions and the messages from a given single partition received will still be kept contiguously. By that time probably there is no way to find out which set of messages came from which partition. By providing this complex map that contains the partition information for the topic, we make sure that the order sent by the producer is preserved even if the number of streams used was less than the number of broker partitions.

它是java.util.Map包含topic作为key，一个Map作为value。内部的Map的key是stream（partition）的数量，value是消息payload集合。之所以返回类型这么复杂是因为kafka对于消息的排序。在high level consumer，所有在一个stream接收到的消息对于一个partition是有序的。例如，如果我有一个名为test的topic，4个partition，在consumer有4个对于的stream，所有的consumer stream接收数据的顺序将和放入的顺序一致。这是主题的consumer stream数量和broker partition数量一致的另一个原因。咋们再来说说stream数量少于partition数量的情况，单独stream包含从多个partition获取的消息这里没有任何其它保证，但是从单独partition接收的消息将还是保持连续。到了那个时候，大概哪里没有方法找出消息来自来个partition。提供包含主题partition信息的复杂map，我们确定使用的stream的数量少于broker partition的数量，producer也保持发送顺序。

A downstream component which receives the data from the inbound adapter can cast the SI payload to the above Map.

从inbound adapter接收数据下端组件可以转换SI payload到上面的map。

If your use case does not require ordering of messages during consumption, then you can easily pass this payload to a standard SI transformer and just get a full dump of the actual payload sent by Kafka.

如果你在消费的时候不要求消息的顺序，则你可以容易通过转换payload到标准SI transformer，仅获取实际kafka发送的payload。

**Tuning Consumer Properties**

 Kafka Consumer API provides several \[Consumer Configs\] \([http://kafka.apache.org/documentation.html\#consumerconfigs](http://kafka.apache.org/documentation.html#consumerconfigs)\) to fine tune consumers. To specify those properties, `consumer-context` element supports optional `consumer-properties` attribute that can reference the spring properties bean. This properties will be applied to all Consumer Configurations within the consumer context. For Eg:

Kafka Consumer API 提供 \[Consumer配置\] \([http://kafka.apache.org/documentation.html\#consumerconfigs](http://kafka.apache.org/documentation.html#consumerconfigs)\) 。指定那些熟悉，`consumer-context` 元素支持可选的`consumer-properties`属性可以引用spring properties bean。属性将被通过consumer context应用到所有的consumer配置。例子：

```markup
<bean id="consumerProperties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">
        <property name="properties">
            <props>
                <prop key="auto.offset.reset">smallest</prop>
                <prop key="socket.receive.buffer.bytes">10485760</prop> <!-- 10M -->
                <prop key="fetch.message.max.bytes">5242880</prop>
                <prop key="auto.commit.interval.ms">1000</prop>
            </props>
        </property>
    </bean>

    <int-kafka:consumer-context id="consumerContext"
            consumer-timeout="1000"
            zookeeper-connect="zookeeperConnect" consumer-properties="consumerProperties">
        <int-kafka:consumer-configurations>
            <int-kafka:consumer-configuration ... > ... </int-kafka:consumer-configuration>
            <int-kafka:consumer-configuration ... > ... </int-kafka:consumer-configuration>
            ...
        </int-kafka:consumer-configurations>
    </int-kafka:producer-context>
```







