# ChannelPipeline

ChannelPipeline是ChannelHandler的容器，一个ChannelPipeline包含多个ChannelHandler，如下图所示

![](<../.gitbook/assets/image (22).png>)

ChannelPipeline中包含HeadChannelHandlerContext和TailChannelHandlerContext分别作为头和尾。向ChannelPipeline添加一个ChannelHandler的时候，会创建一个新的ChannelHandlerContext加入到链中，ChannelHandler包含在ChannelHandlerContext中，如下图所示

![](<../.gitbook/assets/image (24).png>)

