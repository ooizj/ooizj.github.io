# JDBC pools configuration

BTM XA datasources can be created via some java code or via a BTM-specific tool called the Resource Loader. You are free to choose the method you prefer, there is absolutely no difference between them.

BTM XA datasources可以通过java代码或者BTM的Resource Loader来创建。

#### DataSource Properties

**acquireIncrement**

 This parameter controls how many connections are filled into the pool when the pool is empty but the **maxPoolSize** has not been reached. If there aren't enough connections in the pool to fulfill a request, new connections will be created, by increments of **acquireIncrement** at a time. Default value: 1.

这个参数决定当连接池已空且未达到**maxPoolSize**时多少连接被放入连接池。如果连接池没有足够的连接满足请求的时候，将会根据**acquireIncrement**决定创建多少个新的连接。默认值为1。

**acquisitionInterval**

This parameter controls how long \(in seconds\) the pool waits between attempts to create new connections. This time in included within in the acquisitionTimeout. Default value: 1.

这个参数决定连接池可以等待两次创建新连接的间隔时间（秒）。这个时间包含了acquisitionTimeout。默认值为1。

**acquisitionTimeout**

This parameter controls how long \(in seconds\) a request for a connection from the pool will block before being aborted \(and an exception thrown\). Default value: 30.

这个参数决定一个从连接池获取连接在中断（伴随着抛出异常）的之前最多等待的时间（秒）。默认值为30

**allowLocalTransactions**

 This parameter is used to adjust whether or not you want to be able to run SQL statements outside of XA transactions scope. Defaults value: `false`.

这个参数被用来设置是否你想要允许在事物外执行sql。默认值为false

**applyTransactionTimeout**

 Should the transaction timeout be passed to the resource via `XAResource.setTransactionTimeout()`? Default value: `false`.

是否通过`XAResource.setTransactionTimeout()`将事物超时时间传递给resource。默认值为false。

**automaticEnlistingEnabled**

 This parameter controls whether connections from the PoolingDataSource are automatically enlisted/delisted into the XA transactions. If this is set to `false` then have to enlist XAResource objects manually into the Transaction objects for them to participate in XA transactions. Default value: `true`.

这个参数决定是否来自于PoolingDataSource的连接是自动进/出XA事物。如果设置为`false`则必须手动将XAResource对象注册到事物来参加XA事物。默认值为true。

**className**

 The name of the JDBC driver's `XADataSource` implementation class.

实现了`XADataSource`的JDBC驱动类名。

**deferConnectionRelease**

 Set this parameter to false if the vendor's XADataSource implementation supports transactions interleaving. Default value: `false`

如果实现XADataSource供应商将这个参数设置为false







