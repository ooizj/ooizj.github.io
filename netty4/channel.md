# Channel

基本的 I/O 操作（bind()、connect()、read()和 write()）依赖于底层网络传输所提 供的原语。在基于 Java 的网络编程中，其基本的构造是 class Socket。Netty 的 Channel 接 口所提供的 API，大大地降低了直接使用 Socket 类的复杂性。此外，Channel 也是拥有许多 预定义的、专门化实现的广泛类层次结构的根，下面是一个简短的部分清单：&#x20;

&#x20;EmbeddedChannel；&#x20;

&#x20;LocalServerChannel；&#x20;

&#x20;NioDatagramChannel；&#x20;

NioSctpChannel；&#x20;

&#x20;NioSocketChannel。



