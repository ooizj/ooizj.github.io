# Channel

Channel接口基本的 I/O 操作（bind()、connect()、read()和 write()），大大地降低了直接使用 Socket 类的复杂性。

### 常用的Channel

#### &#x20;EmbeddedChannel

常用于测试写的handler、decoder、encoder。

#### ServerSocketChannel(e.g. NioServerSocketChannel)

服务端用于**accept**事件处理，accept之后会创建child channel（**SocketChannel(e.g. NioSocketChannel)**），这个新建的child channel负责和客户端的数据交互。

#### SocketChannel(e.g. NioSocketChannel)

服务端accept之后创建的，或则客户端用于和服务端数据交互的Channel。

### ChannelHandler在Channel中的包含关系如图

![](<../.gitbook/assets/image (28).png>)

### Channel.write()

当调用Channel.write()的时候，会调用最后一个OutboundHandler.write()，如果最后一个OutboundHandler.write()方法中有调用ChannelHandlerContext.write()，会调用倒数第二个OutboundHandler.write()，前面的OutboundHandler也一样；否则不会经过其他OutboundHandler。

当调用ChannelHandlerContext.write()的时候除了开始是从该ChannelHandlerContext的ChannelHandler的前一个OutboundHandler.write()开始，其他和Channel.write()一样，如图

![](<../.gitbook/assets/image (27).png>)

![](<../.gitbook/assets/image (26).png>)

![](<../.gitbook/assets/image (21).png>)

### Channel.read()

当调用Channel.read()的时候，会调用最后一个OutboundHandler.read()，如果最后一个OutboundHandler.read()方法中有调用ChannelHandlerContext.read()，会调用倒数第二个OutboundHandler.read()，前面的OutboundHandler也一样；否则不会经过其他OutboundHandler。

走到最后会提交一个read任务到EventLoop，有数据的时候EventLoop会调用第一个InboundHandler.read()，如果第一个InboundHandler.read()方法中有调用ChannelHandlerContext.read()，会调用第二个InboundHandler.read()，后面的InboundHandler也一样；否则不会经过其他InboundHandler。如图

![](<../.gitbook/assets/image (29).png>)

![](<../.gitbook/assets/image (22).png>)

![](<../.gitbook/assets/image (20) (1).png>)





