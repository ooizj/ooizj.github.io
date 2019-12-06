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

如果供应商的XADataSource的实现支持transactions interleaving，则将这个参数设置为false。默认值为false。

**disabled**

 This parameter controls whether this DataSource configuration is disabled or enabled. Default value: `false`.

这个参数决定是否这个数据源配置被禁用或启用。默认值为false。

**enableJdbc4ConnectionTest**

 If your JDBC driver supports JDBC4, this method of testing the connection is likely much more efficient than using the testQuery parameter. In the case of a testQuery, the query must be sent to the DB server, parsed, and executed before the connection can be used. JDBC4 exposes a method by which a driver can make its own determination of connectivity \(possibly whether the socket is still connected, etc.\). Default value: `false`.

如果你的JDBC驱动支持JDBC4，这个测试连接的方法可能比使用testQuery这个参数更有效，在连接被使用之前，这个查询语句必须被发送到数据库解析和执行。JDBC4有数据库驱动暴露了一个方法可以自行判断连通性（可能是是否socket还连接着，等等）。默认值为false。

**ignoreRecoveryFailures**

 Should recovery errors be ignored? **Ignoring recovery errors jeopardizes the failed transactions atomicity so only set this parameter to true when you know what you're doing.** This is mostly useful in a development environment. Default value: `false`.

是否忽略recovery错误？忽略recovery错误不利于失败事物原子性，因此只可在你知道你在做什么的时候设置这个参数为true。这个通常在开发环境很有用。默认值为false。

**isolationLevel**

 Set the default isolation level. All of the four standard `READ_COMMITTED`, `READ_UNCOMMITTED`, `REPEATABLE_READ` and `SERIALIZABLE` values are supported. Default value: `READ_COMMITTED`

设置默认的isolation level。4个标准的`READ_COMMITTED`, `READ_UNCOMMITTED`, `REPEATABLE_READ` 和`SERIALIZABLE`值都是支持的。默认值为`READ_COMMITTED`。

**maxIdleTime**

When the pool is above the minPoolSize, this parameter controls how long \(in seconds\) an idle connection will stay in the pool before being retired. However, even if a connection is idle for longer than maxIdleTime, the connection will not be retired if it would bring the pool below the minPoolSize. Default value: 60.

当连接池中连接个数多于minPoolSize时，这个参数决定一个空闲的连接在请离前可以呆在连接池的时间（秒），当连接池中连接个数小于等于minPoolSize时，即使一个连接空闲超过了maxIdleTime，这个连接也不会被请离。默认值为60。

**maxLifeTime**

This parameter controls how long \(in seconds\) a connection is allowed to live in the pool regardless of the minPoolSize parameter value. If a connection exceeds this time, and it is not in use, it will be retired from the pool. If the retirement of a connection causes the pool to dip below the minPoolSize, it will be immediately replaced with a new connection. This setting can be used to avoid unexpected disconnect due to database-side connection timeout. It is also useful to avoid leaks and release resources held on the database-side for open connections. Default value: 0.

这个参数决定一个连接被允许在连接池存在的时间（秒）不会受minPoolSize影响。如果一个连接超出了这个时间，且没有在被使用，它将被请离连接池。如果一个连接被请离造成连接池中连接个数小于minPoolSize，则将会用新的连接替换上。这个设置可以被用于避免连接过期数据库端连接超时。也可以用于避免连接泄露和释放数据库端打开的连接资源。默认值为0。

**minPoolSize**

The minimum number of active connections the pool will try to keep in the pool. In an idle system, the number of active connections will trend toward this value until it is reached. Connections that are retired and bring the pool below this value will be replenished until this minimum pool size is reached. Default value: 0.

这个参数设置连接池活跃的最小连接个数。在系统空闲时，活跃的连接数将会逐渐趋于这个个数，直到达到。连接退出连接池带来的连接个数小于这个值时，将会补充直到达到这个值。默认值为0。

**maxPoolSize**

 The maximum number of active connections the pool will allow in the pool. If this maximum is reached, a request for a connection from the pool will block the calling thread until a connection is available or the **acquisitionTimeout** is reached. **This value should be overridden.** Default value: 0.

这个参数设置连接池活跃的最大连接个数。如果达到这个值，从连接池请求连接将阻塞调用者所在线程，直到有可用连接或达到**acquisitionTimeout**。这个参数必须设置。默认值为0。

**preparedStatementCacheSize**

 Controls how many Prepared Statements are cached \(per connection\) by BTM. A value of `0` means that statement caching is disabled. Default value: 0.

这个参数决定被BTM缓存Prepared Statements的个数。为0意味着statement缓存被禁用。默认值为0。

**shareTransactionConnections**

 By default, whenever a thread requests a connection from the DataSource, BTM will issue a new connection. All connections issued are bound into the same transaction context. Depending on the design of the user's application, this behavior can result in a large number of connections to the database -- and in the case of a database such as PostgreSQL, which uses one process per-connection this places a fairly heavy burden on the database. Setting this option to true will enable a thread-associated connection cache. With this option enabled, no matter how many times a thread requests a connection from the DataSource, BTM will return a single connection. Because connections can be shared within the context of a transaction, this provides a more efficient use of connection resources. A positive benefit of a single connection per thread is that the prepared statement cache \(which is per-connection\) is also made more efficient. Lastly, another benefit is that because connections are shared within the same thread, the overhead of establishing and testing a new connection to the database is avoided, which significantly improves the performance of some access patterns. Of course, BTM will still ensure correctness whenever this parameter is set to true. **While the default value of this property is `false` for backward compatibility, the recommended setting is `true`.** Default value: `false`.

默认情况，无论何时一个线程从数据源请求一个连接，BTM都将给出一个新的连接。所有的给出的连接被绑定到相同的事物上下文。依赖于用户应用的设计，可以产生一个巨大数量的数据库连接--像是PostgreSQL这样的数据库，使用一个处理过程一个连接，这点对数据库造成很大的负担。设置这个参数为true将打开一个thread-associated的连接缓存。打开后，不用在乎一个线程从数据源请求一个连接多少次，BTM将返回一个单独的连接。因为在事物上下文中连接可以被共享，这种方式提供了更有效的连接资源的用处。一个连接对应一个线程的好处是prepared statement缓存（per-connection）也变得更有效率。最后，其它，另一个好处是因为在同一个线程连接是共享的，建立和测试的一个新连接到数据库的开销被避免了，显著的提高了一些访问方式的性能。当然，BTM将仍然保证正确性不论是否这个参数被设置为true。虽然为了向后兼容默认值是false，推荐的值是true。默认值为false。

**testQuery**

This parameters sets the SQL statement that is used to test whether a connection is still alive before returning it from the connection pool.

这个参数设置被用于测试一个连接在返回前是否可用的SQL statement。

**twoPcOrderingPosition**

This parameter controls the position of this resource during the 2PC protocol execution. This is required if you want to guarantee that a resource commits before another one. Default value: 0.

这个参数决定这个在2PC protocol（[https://zh.wikipedia.org/wiki/%E4%BA%8C%E9%98%B6%E6%AE%B5%E6%8F%90%E4%BA%A4](https://zh.wikipedia.org/wiki/%E4%BA%8C%E9%98%B6%E6%AE%B5%E6%8F%90%E4%BA%A4)）执行的资源位置。如果你想要保证一个资源提交先于另一个，那么这个是必须的。默认值为0。

**uniqueName**

Each datasource must be assigned a unique name. This is required for distributed crash recovery.

每一个数据库对应的必须被分配一个唯一的名字。这个是必须的，为了分布式崩溃恢复。

**useTmJoin**

 Set this parameter to `false` if the vendor's XADataSource implementation does not implement XAResource.isSameRM\(\) properly. Refer to the JdbcXaSupportEvaluation page to see if your database needs it. Default value: `true`.

如果提供商的XADataSource实现类没有实现XAResource.isSameRM\(\)这个方法，则设置这个参数为false。参考JdbcXaSupportEvaluation页面以查看你的数据库是否需要这样做。默认值为true。















