# ChannelFuture\&ChannelPromise

### ChannelFuture

![](<../.gitbook/assets/image (23).png>)

ChannelFuture继承关系如上图，其作为netty提供的api的异步操作，如下几个例子

1，在写出“hello”后，关闭连接

```java
// response hello
final String BREAK_LINE = "\r\n" ;
String responseText =
        "HTTP/1.1 200" + BREAK_LINE +
                "Server: nginx" + BREAK_LINE +
                "Content-Type: text/plain; charset=utf-8" + BREAK_LINE + BREAK_LINE +
                "hello" ;
ChannelFuture channelFuture = ctx.channel().writeAndFlush(Unpooled.copiedBuffer(responseText, CharsetUtil.UTF_8));
// 在写出完成后关闭连接
channelFuture.addListener(new ChannelFutureListener() {
            @Override
            public void operationComplete(ChannelFuture future) throws Exception {
                future.channel().close();
            }
        });

```

2，在bind完成前等待

```java
ServerBootstrap b = ...
ChannelFuture f = b.bind(port);
f.sync(); // 等待服务器绑定完成
```

### ChannelPromise

![](<../.gitbook/assets/image (30).png>)

ChannelPromise继承关系如上图，Promise在Future的基础上多了setSuccess()、trySuccess()、setFailure()、tryFailure()这些方法，可以设置结果

示例：writeAndFlush加上监听在完成后打印写出的结果，在OutboundHandler中write触发的时候使用Promise设置为失败

```java
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
    try{
        ByteBuf buf = (ByteBuf) msg;
        ctx.channel().writeAndFlush(Unpooled.copiedBuffer("hello", CharsetUtil.UTF_8)).addListener(new ChannelFutureListener() {
            @Override
            public void operationComplete(ChannelFuture future) throws Exception {
                System.out.println(future.isSuccess()); // fail
            }
        });
    }finally {
        ReferenceCountUtil.release(msg);
    }
}

public class TestServerOutboundHandler extends ChannelOutboundHandlerAdapter {
    @Override
    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) throws Exception {
        ByteBuf buf = (ByteBuf) msg;
        System.out.printf("write msg：[%s]%n", buf.toString(CharsetUtil.UTF_8));
        promise.setFailure(new IllegalArgumentException("参数a错误"));
        ctx.write(msg, promise);
    }
}
```

