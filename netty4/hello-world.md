# Hello world

### Netty版本

4.1.68.Final

### Hello world流程

1，启动服务端

2，客户端发送“client msg test!”

3，服务端读取客户端消息，并响应“server response hello”

4，客户端接收服务端响应，并关闭

### maven

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>me.ooi</groupId>
    <artifactId>test-netty4</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.6.2</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.0</version>
        </dependency>

        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.1.68.Final</version>
        </dependency>

        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.7.13</version>
        </dependency>


    </dependencies>

</project>
```

### 编写服务端

MsgServerHandler.java

```java
package me.ooi.testnetty4.helloworld;

import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFutureListener;
import io.netty.channel.ChannelHandler;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.util.CharsetUtil;
import io.netty.util.ReferenceCountUtil;

/**
 * @author jun.zhao
 */
@ChannelHandler.Sharable // 可多个Channel共享
public class MsgServerHandler extends ChannelInboundHandlerAdapter {

    // 客户端连接
    @Override
    public void channelActive(final ChannelHandlerContext ctx) {
        System.out.println("[server] client connected: " + ctx);
    }

    // 对于每个传入的消息都要调用
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        try{
            ByteBuf input = (ByteBuf) msg;
            System.out.printf("[server] receive msg：[%s]%n", input.toString(CharsetUtil.UTF_8));

            ctx.writeAndFlush(Unpooled.copiedBuffer("server response hello", CharsetUtil.UTF_8));
        }finally {
            ReferenceCountUtil.release(msg);
        }
    }

    // 通知ChannelInboundHandler最后一次对channelRead()的调用是当前批量读取中的最后一条消息
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        ctx.writeAndFlush(Unpooled.EMPTY_BUFFER).addListener(ChannelFutureListener.CLOSE); // flush并且关闭Channel
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace(); // 打印异常
        ctx.close(); // 关闭Channel
    }
}

```

MsgServer.java

```java
package me.ooi.testnetty4.helloworld;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;

/**
 * @author jun.zhao
 */
public class MsgServer {

    private final int port;
    public MsgServer(int port){
        this.port = port;
    }

    public void run() throws InterruptedException {

        System.out.println("[server] listen on: "+port);

        MsgServerHandler msgServerHandler = new MsgServerHandler();

        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class) // 使用NIO

                    // 当一个新的连接被接受时，
                    // 一个新的子 Channel 将会被创建，而 ChannelInitializer 将会把一个你的EchoServerHandler 的实例添加到该 Channel 的 ChannelPipeline 中
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        public void initChannel(SocketChannel ch) {
                            ch.pipeline().addLast(msgServerHandler);
                        }
                    });

            ChannelFuture f = b.bind(port)
                    .sync(); // 等待服务器绑定完成
            f.channel().closeFuture()
                    .sync(); // 等待Channel关闭完成
        } finally {
            // 关闭线程池并且释放所有的资源
            workerGroup.shutdownGracefully();
            bossGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws Exception {
        new MsgServer(8901).run();
    }
}

```

### 编写客户端

MsgClientHandler.java

```java
package me.ooi.testnetty4.helloworld;

import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.util.CharsetUtil;

/**
 * @author jun.zhao
 */
public class MsgClientHandler extends SimpleChannelInboundHandler<ByteBuf> {

    // 在到服务器的连接已经建立之后将被调用
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        ctx.writeAndFlush(Unpooled.copiedBuffer("client msg test!", CharsetUtil.UTF_8)); // 当被通知Channel是活跃的时候，发送一条消息
    }

    // 当从服务器接收到一条消息时被调用
//    每当接收数据时，都会调用这个方法。需要注意的是，由服务器发送的消息可能会被分块接收。
//    也就是说，如果服务器发送了 5 字节，那么不能保证这 5 字节会被一次性接收。即使是对于这么少量的数据，channelRead0()方法也可能会被调用两次，
//    第一次使用一个持有 3 字节的 ByteBuf（Netty 的字节容器），第二次使用一个持有 2 字节的 ByteBuf。
//    作为一个面向流的协议，TCP 保证了字节数组将会按照服务器发送它们的顺序被接收。
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) throws Exception {
        System.out.printf("[client] receive msg: [%s]%n", msg.toString(CharsetUtil.UTF_8));
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace(); // 打印异常
        ctx.close(); // 关闭Channel
    }

}

```

MsgClient.java

```java
package me.ooi.testnetty4.helloworld;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;

import java.util.concurrent.TimeUnit;

/**
 * @author jun.zhao
 */
public class MsgClient {

    private final String host;
    private final int port;
    public MsgClient(String host, int port) {
        this.host = host;
        this.port = port;
    }

    public void run() throws InterruptedException {

        System.out.printf("[client] connect to: %s:%s%n", host, port);

        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
                    .channel(NioSocketChannel.class) // 使用NIO

                    // 当一个新的连接被接受时，
                    // 一个新的子 Channel 将会被创建，而 ChannelInitializer 将会把一个你的EchoServerHandler 的实例添加到该 Channel 的 ChannelPipeline 中
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        public void initChannel(SocketChannel ch) {
                            ch.pipeline().addLast(new MsgClientHandler());
                        }
                    });

            ChannelFuture f = b.connect(host, port)
                    .sync(); // 等待服务器连接完成

            TimeUnit.SECONDS.sleep(3); // 等待服务端相应

            f.channel().closeFuture()
                    .sync(); // 等待Channel关闭完成
        } finally {
            group.shutdownGracefully(); // 关闭线程池并且释放所有的资源
        }
    }

    public static void main(String[] args) throws Exception {
        new MsgClient("localhost", 8901).run();
    }

}

```

### 控制台输出

先运行服务端

```shell
[server] listen on: 8901
```

再运行客户端

```
[client] connect to: localhost:8901
[client] receive msg: [server response hello]
```

再看服务端

```
[server] listen on: 8901
[server] client connected: ChannelHandlerContext(MsgServerHandler#0, [id: 0x388fa759, L:/127.0.0.1:8901 - R:/127.0.0.1:4422])
[server] receive msg：[client msg test!]
```
