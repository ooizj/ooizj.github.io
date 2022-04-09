# ChannelPipeline

ChannelPipeline是ChannelHandler的容器，一个ChannelPipeline包含多个ChannelHandler，ChannelHandler包含在ChannelHandlerContext中，如图

![](<../.gitbook/assets/image (19) (1).png>)

![](<../.gitbook/assets/image (23) (1).png>)

ChannelPipeline中的ChannelHandlerContext是按链式存储的，ChannelPipeline包含了自动创建的HeadChannelHandlerContext和TailChannelHandlerContext分别作为链的头和尾。向ChannelPipeline添加一个ChannelHandler的时候，会创建一个新的ChannelHandlerContext加入到链中，ChannelHandler包含在ChannelHandlerContext中，如图

![](<../.gitbook/assets/image (25).png>)

