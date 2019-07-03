# kafka2.1服务端安装

注：内存太小的机器可能无法运行kafka

## 1，下载kafka

kafka目前最新版本为kafka\_2.12-2.2.0，官方文档的示例使用的是kafka\_2.11-2.1.0  
在[kafka官网](https://kafka.apache.org/downloads)下载kafka\_2.11-2.1.0并解压

## 2，设置jdk（若环境变量的java版本为1.8则跳过此步骤）

根据[官网介绍](http://kafka.apache.org/documentation/#upgrade_200_notable)kafka2.0.0起支持的jdk最低版本为1.8

* Support for Java 7 has been dropped, Java 8 is now the minimum version required.

修改bin/kafka-run-class.sh，在第一行添加“JAVA\_HOME”和“JRE\_HOME”，如下

```text
JAVA_HOME="jdk1.8目录"
JRE_HOME="jre1.8目录"
```

## 3，启动zookeeper

linux

```text
bin/zookeeper-server-start.sh config/zookeeper.properties
```

windows

```text
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
```

## 4，启动kafka-server

如果需要其他机器访问kafka-server，需要修改“config/server.properties”的“advertised.listeners”属性，如下

```text
advertised.listeners=PLAINTEXT://ip:端口
#如：advertised.listeners=PLAINTEXT://192.168.1.1:9092
```

linux

```text
bin/kafka-server-start.sh config/server.properties
```

windows

```text
.\bin\windows\kafka-server-start.bat .\config\server.properties
```

## 测试

注：下面介绍的命令为linux下的命令，windows的命令在“bin\windows”，linux命令和windowns命令名称相同只是目录不同

创建一个topic：“test”

```text
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

查看topic列表

```text
bin/kafka-topics.sh --list --zookeeper localhost:2181
```

发送测试消息

```text
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

查看消息

```text
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

