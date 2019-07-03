# kafka客户端代码示例

### kafka客户端代码示例

kafka支持的客户端很多，主流的java、python、node.js等都是支持的，restfull-api也是有的  
下面介绍一下java编写客户端的示例

注：假设kafka-server的ip为：192.168.1.1；访问端口为9092

#### 1，添加maven依赖

创建maven工程，并添加kafka-clients依赖

```markup
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.1.0</version>
</dependency>
```

#### 2，编写consumer

创建consumer.properties，所有配置的含义可以到[Consumer Configs](https://kafka.apache.org/documentation/#consumerconfigs)中查看

```text
auto.commit.interval.ms = 5000
auto.offset.reset = latest
bootstrap.servers = 192.168.1.1:9092
check.crcs = true
client.dns.lookup = default
connections.max.idle.ms = 540000
default.api.timeout.ms = 60000
enable.auto.commit = true
exclude.internal.topics = true
fetch.max.bytes = 52428800
fetch.max.wait.ms = 500
fetch.min.bytes = 1
group.id = group1
heartbeat.interval.ms = 3000
internal.leave.group.on.close = true
isolation.level = read_uncommitted
key.deserializer = org.apache.kafka.common.serialization.StringDeserializer
max.partition.fetch.bytes = 1048576
max.poll.interval.ms = 300000
max.poll.records = 500
metadata.max.age.ms = 300000
metrics.num.samples = 2
metrics.recording.level = INFO
metrics.sample.window.ms = 30000
receive.buffer.bytes = 65536
reconnect.backoff.max.ms = 1000
reconnect.backoff.ms = 50
request.timeout.ms = 30000
retry.backoff.ms = 100
security.protocol = PLAINTEXT
send.buffer.bytes = 131072
session.timeout.ms = 10000
value.deserializer = org.apache.kafka.common.serialization.StringDeserializer
```

编写consumer代码，参考[官方示例](https://kafka.apache.org/21/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html)

```java
public void testConsumer() throws IOException {
    final String TOPIC = "mytest1" ; 
    Properties props = new Properties() ; 
    props.load(KafkaConsumerTestor.class.getResourceAsStream("/consumer.properties"));
    KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
    consumer.subscribe(Arrays.asList(TOPIC));

    Runtime.getRuntime().addShutdownHook(new Thread(()->{
        consumer.close();
    }));

    //轮询消息
    while (true) {
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10)); //最多等10s 
        if( records != null ){
            log.info("本次获取到的消息数量："+records.count());
            for (ConsumerRecord<String, String> record : records) {
                log.info(Thread.currentThread().getId()+":收到消息:"+record.value()) ; 
            }
        }
    }
}
```

#### 3，编写producer

创建producer.properties，所有配置的含义可以到[Producer Configs](https://kafka.apache.org/documentation/#producerconfigs)中查看

```text
acks = 1
batch.size = 100
bootstrap.servers = 192.168.1.1:9092
buffer.memory = 33554432
client.dns.lookup = default
compression.type = none
connections.max.idle.ms = 540000
delivery.timeout.ms = 120000
enable.idempotence = false
key.serializer = org.apache.kafka.common.serialization.StringSerializer
linger.ms = 0
max.block.ms = 60000
max.in.flight.requests.per.connection = 5
max.request.size = 1048576
metadata.max.age.ms = 300000
metrics.num.samples = 2
metrics.recording.level = INFO
metrics.sample.window.ms = 30000
partitioner.class = org.apache.kafka.clients.producer.internals.DefaultPartitioner
receive.buffer.bytes = 32768
reconnect.backoff.max.ms = 1000
reconnect.backoff.ms = 50
request.timeout.ms = 30000
retries = 2147483647
retry.backoff.ms = 100
security.protocol = PLAINTEXT
send.buffer.bytes = 131072
value.serializer = org.apache.kafka.common.serialization.StringSerializer
```

编写producer代码，参考[官方示例](https://kafka.apache.org/21/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html)

```java
public void testProducer() throws IOException {
    final String TOPIC = "mytest1";
    Properties props = new Properties();
    props.load(TestKafkaProducer.class.getResourceAsStream("/producer.properties"));
    Producer<String, String> producer = new KafkaProducer<>(props);

    log.info("发送开始");
    final int MAX_COUNT = 1_000_000;
    for (int i = 0; i < MAX_COUNT; i++) {
        producer.send(new ProducerRecord<String, String>(TOPIC, "msg-" + i));
    }
    log.info("发送结束");

    producer.close();
}
```

