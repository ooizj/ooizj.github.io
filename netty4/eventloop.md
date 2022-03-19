# EventLoop

EventLoop 定义了 Netty 的核心抽象，用于处理连接的生命周期中所发生的事件。我们将 在第 7 章中结合 Netty 的线程处理模型的上下文对 EventLoop 进行详细的讨论。目前，图 3-1 在高层次上说明了 Channel、EventLoop、Thread 以及 EventLoopGroup 之间的关系。

![](../.gitbook/assets/image-097.png)

这些关系是：

一个 EventLoopGroup 包含一个或者多个 EventLoop；&#x20;

&#x20;一个 EventLoop 在它的生命周期内只和一个 Thread 绑定；&#x20;

&#x20;所有由 EventLoop 处理的 I/O 事件都将在它专有的 Thread 上被处理；&#x20;

&#x20;一个 Channel 在它的生命周期内只注册于一个 EventLoop；&#x20;

&#x20;一个 EventLoop 可能会被分配给一个或多个 Channel。 注意，在这种设计中，一个给定 Channel 的 I/O 操作都是由相同的 Thread 执行的，实际 上消除了对于同步的需要。
