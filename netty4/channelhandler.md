# ChannelHandler

ChannelHandler作为编写应用程序时处理数据的核心组件。

有ChannelInboundHandler和ChannelOutboundHandler两种。

ChannelInboundHandler中的方法用于响应事件（如：有数据了且设置了自动读或提交了读取任务，则会调用read()；有新的客户端连接则会调用channelActive()）；Decoder是将入站消息解码，是实现了ChannelInboundHandler。

ChannelOutboundHandler中的方法通常是自己的代码发起的调用（如：Channel.read()，Channel.write()）；Encoder是将出站消息编码，是实现了ChannelOutboundHandler。

在读写数据时在ChannelHandler之间的流转如图

![](<../.gitbook/assets/image (20) (1).png>)

### ChannelHandler示例

#### 示例流程

服务端的ChannelHandler依次为：TestServerInboundHandler1, TestServerOutboundHandler2, TestServerInboundHandler2, TestServerOutboundHandler1

客户端的ChannelHandler依次为：TestClientInboundHandler1,TestClientOutboundHandler2,TestClientInboundHandler2,TestClientOutboundHandler1

客户端连接到服务端后，发送“client say hello”的消息到服务端，并设置autoRead为false，并在3秒后读取服务端消息，服务端收到客户端发来的“client say hello”消息后向客户端响应“server say hello”的消息。

#### 代码

TestServer1.java

```java
package me.ooi.testnetty4.testchannelhandler;

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
public class TestServer1 {

    private final int port;
    public TestServer1(int port){
        this.port = port;
    }

    public void run() throws InterruptedException {

        System.out.println("[server] listen on: "+port);

        TestServerInboundHandler1 testServerInboundHandler1 = new TestServerInboundHandler1();
        TestServerInboundHandler2 testServerInboundHandler2 = new TestServerInboundHandler2();
        TestServerOutboundHandler1 testServerOutboundHandler1 = new TestServerOutboundHandler1();
        TestServerOutboundHandler2 testServerOutboundHandler2 = new TestServerOutboundHandler2();

        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class) // 使用NIO

                    // 当一个新的连接被接受时，
                    // 一个新的子 Channel 将会被创建，而 ChannelInitializer 将会把一个你的xxxHandler 的实例添加到该 Channel 的 ChannelPipeline 中
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        public void initChannel(SocketChannel ch) {
                            ch.pipeline().addLast(testServerInboundHandler1, testServerOutboundHandler2, testServerInboundHandler2, testServerOutboundHandler1);
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
        new TestServer1(8901).run();
    }

}

```

TestServerInboundHandler1.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.buffer.ByteBuf;
import io.netty.channel.*;
import io.netty.util.CharsetUtil;

/**
 * @author jun.zhao
 */
@ChannelHandler.Sharable // 可多个Channel共享
public class TestServerInboundHandler1 extends ChannelInboundHandlerAdapter {

    @Override
    public void channelActive(final ChannelHandlerContext ctx) {
        System.out.println("[server] client connected: " + ctx);
    }

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        ByteBuf buf = (ByteBuf) msg;
        System.out.printf("[server] receive msg：[%s]%n", buf.toString(CharsetUtil.UTF_8));
        ctx.fireChannelRead(msg);
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace(); // 打印异常
        ctx.close(); // 关闭Channel
    }
}
```

TestServerInboundHandler2.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.util.CharsetUtil;
import io.netty.util.ReferenceCountUtil;

/**
 * @author jun.zhao
 */
@ChannelHandler.Sharable // 可多个Channel共享
public class TestServerInboundHandler2 extends ChannelInboundHandlerAdapter {

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        try{
            ByteBuf buf = (ByteBuf) msg;
            System.out.printf("[SERVER] receive msg：[%s]%n", buf.toString(CharsetUtil.UTF_8));

//            ctx.writeAndFlush(Unpooled.copiedBuffer("server say hello", CharsetUtil.UTF_8)); // 当前handler前端的outbound handler会触发write
            ctx.channel().writeAndFlush(Unpooled.copiedBuffer("server say hello", CharsetUtil.UTF_8)); // 所有的outbound handler会触发write

        }finally {
            ReferenceCountUtil.release(msg);
        }
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace(); // 打印异常
        ctx.close(); // 关闭Channel
    }
}

```

TestServerOutboundHandler1.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandler;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelOutboundHandlerAdapter;
import io.netty.channel.ChannelPromise;
import io.netty.util.CharsetUtil;

/**
 * @author jun.zhao
 */
@ChannelHandler.Sharable
public class TestServerOutboundHandler1 extends ChannelOutboundHandlerAdapter {

    @Override
    public void read(ChannelHandlerContext ctx) throws Exception {
        System.out.println("[client] read");
        ctx.read();
    }

    @Override
    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) throws Exception {
        ByteBuf buf = (ByteBuf) msg;
        System.out.printf("[server] write msg：[%s]%n", buf.toString(CharsetUtil.UTF_8));
        ctx.write(msg, promise);
    }

}

```

TestServerOutboundHandler2.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandler;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelOutboundHandlerAdapter;
import io.netty.channel.ChannelPromise;
import io.netty.util.CharsetUtil;

/**
 * @author jun.zhao
 */
@ChannelHandler.Sharable
public class TestServerOutboundHandler2 extends ChannelOutboundHandlerAdapter {

    @Override
    public void read(ChannelHandlerContext ctx) throws Exception {
        System.out.println("[CLIENT] read");
        ctx.read();
    }

    @Override
    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) throws Exception {
        ByteBuf buf = (ByteBuf) msg;
        System.out.printf("[SERVER] write msg：[%s]%n", buf.toString(CharsetUtil.UTF_8));
        ctx.write(msg, promise);
    }

}
```



TestClient1.java

```java
package me.ooi.testnetty4.testchannelhandler;

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
public class TestClient1 {

    private final String host;
    private final int port;
    public TestClient1(String host, int port) {
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
                    // 一个新的子 Channel 将会被创建，而 ChannelInitializer 将会把一个你的xxxHandler 的实例添加到该 Channel 的 ChannelPipeline 中
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        public void initChannel(SocketChannel ch) {
                            ch.pipeline()
                                    .addLast("in1", new TestClientInboundHandler1(false))
                                    .addLast("out2", new TestClientOutboundHandler2())
                                    .addLast("in2", new TestClientInboundHandler2())
                                    .addLast("out1", new TestClientOutboundHandler1());
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
        new TestClient1("localhost", 8901).run();
    }

}

```

TestClientInboundHandler1.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.util.CharsetUtil;

/**
 * @author jun.zhao
 */
public class TestClientInboundHandler1 extends SimpleChannelInboundHandler<ByteBuf> {

    public TestClientInboundHandler1(boolean autoRelease) {
        super(autoRelease);
    }

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) throws Exception {
        System.out.printf("[client] receive msg: [%s]%n", msg.toString(CharsetUtil.UTF_8));
        ctx.fireChannelRead(msg);
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace(); // 打印异常
        ctx.close(); // 关闭Channel
    }

}

```

TestClientInboundHandler2.java

```java
package me.ooi.testnetty4.testchannelhandler;

import cn.hutool.core.thread.ThreadUtil;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.util.CharsetUtil;

import java.util.concurrent.TimeUnit;

/**
 * @author jun.zhao
 */
public class TestClientInboundHandler2 extends SimpleChannelInboundHandler<ByteBuf> {

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {

        // 设置不自动读取服务端消息
        ctx.channel().config().setAutoRead(false);

        // 向服务端发送hello
        ctx.writeAndFlush(Unpooled.copiedBuffer("client say hello", CharsetUtil.UTF_8));

        // 等3秒后触发读取
        System.out.println("等3秒后触发读取");
        ThreadUtil.execute(()->{
            try {
                TimeUnit.SECONDS.sleep(3);
                ctx.read(); // 当前handler前端的outbound handler会触发read
//                ctx.channel().read(); // 所有的outbound handler会触发read
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
    }

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, ByteBuf msg) throws Exception {
        System.out.printf("[CLIENT] receive msg: [%s]%n", msg.toString(CharsetUtil.UTF_8));
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace(); // 打印异常
        ctx.close(); // 关闭Channel
    }

}

```

TestClientOutboundHandler1.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelOutboundHandlerAdapter;

/**
 * @author jun.zhao
 */
public class TestClientOutboundHandler1 extends ChannelOutboundHandlerAdapter {

    @Override
    public void read(ChannelHandlerContext ctx) throws Exception {
        System.out.println("[client] read");
        ctx.read();
    }
}

```

TestClientOutboundHandler2.java

```java
package me.ooi.testnetty4.testchannelhandler;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelOutboundHandlerAdapter;

/**
 * @author jun.zhao
 */
public class TestClientOutboundHandler2 extends ChannelOutboundHandlerAdapter {

    @Override
    public void read(ChannelHandlerContext ctx) throws Exception {
        System.out.println("[CLIENT] read");
        ctx.read();
    }

}

```

