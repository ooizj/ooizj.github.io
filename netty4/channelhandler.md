# ChannelHandler

从应用程序开发人员的角度来看，Netty 的主要组件是 ChannelHandler，它充当了所有 处理入站和出站数据的应用程序逻辑的容器。这是可行的，因为 ChannelHandler 的方法是 由网络事件（其中术语“事件”的使用非常广泛）触发的。事实上，ChannelHandler 可专 门用于几乎任何类型的动作，例如将数据从一种格式转换为另外一种格式，或者处理转换过程 中所抛出的异常。 举例来说，ChannelInboundHandler 是一个你将会经常实现的子接口。这种类型的 ChannelHandler 接收入站事件和数据，这些数据随后将会被你的应用程序的业务逻辑所处 理。当你要给连接的客户端发送响应时，也可以从 ChannelInboundHandler 冲刷数据。你 的应用程序的业务逻辑通常驻留在一个或者多个 ChannelInboundHandler 中。

