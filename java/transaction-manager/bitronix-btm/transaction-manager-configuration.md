# Transaction manager configuration

### Configuration

 BTM configuration settings are stored in a `Configuration` object. It can be obtained by calling `TransactionManagerServices.getConfiguration()`. All settings are documented in the javadoc and you should refer to it to know what can be configured.

BTM配置是存储在`Configuration`对象。它可以由`TransactionManagerServices.getConfiguration()`获取。所有的设置有javadoc，你应该参考它明白你可以配置哪些东西。

#### How to configure BTM

 The `Configuration` object is implemented with sensible default settings. For a first time user, all default settings are good enough. After the initial testing phase, you might want to change some settings. This can be done in two ways: via a properties configuration file or by setting values directly on the `Configuration` object.

`Configuration`对象有合理的默认设置。首次使用的话，所有设置默认就够了。在刚开始测试阶段，你或许响应改变一些配置。可以按下面两种方式来配置：通过一个properties文件；或者直接设置`Configuration`对象的值。

**The properties configuration file**

You can create a properties file in which you'll set some configuration settings. All the ones you omit will keep their default value.

你可以创建一个properties文件来设置一些属性。所有你没有明确指明的属性都保持默认值。

The file can be stored anywhere on the file system in which case you need to set the `bitronix.tm.configuration` system property to tell BTM where the file lies. This is generally done by adding a -D argument to the virtual machine's command line:

这个配置文件你可以存放到任何地方，你需要制定`bitronix.tm.configuration`这个system property来告诉BTM这个文件在哪。通常采用添加一个“-D”参数到JVM的启动命令行中。

```bash
    java -Dbitronix.tm.configuration=./my-btm-config.properties MyClass
```

Another way is to call the properties file `bitronix-default-config.properties` and store it at the root of your classpath.

另一种方式是将properties命名为`bitronix-default-config.properties`，然后放到根classpath。

The properties file is in the default format `key=value`. Ant-like references \(`${some.property.name}`\) to other properties or to system properties \(defined with -D on the command line\) are supported.

这个properties默认格式是`key=value`。Ant-like这样引用 \(`${some.property.name}`\) 其他属性或者system properties（使用“-D”命令行参数定义的）也是支持的。

**Setting values directly on the Configuration object**

 You can call any setter you want on the object you get from the call to `TransactionManagerServices.getConfiguration()`. This is convenient if you do not want to use the properties file to configure BTM but want to leverage - for instance - Spring instead. Since the Configuration object is a singleton, there is no need to pass it to any other object, BTM will pick it up at startup.

你可以调用从`TransactionManagerServices.getConfiguration()`获取的对象的任何想调用的setter。如果你不想用properties文件来配置BTM但是想用如spring，这样是很方便的。自从Configuration对象是单例的之后，就不需要通过其他对象，BTM将在启动时从它获取。

```java
Configuration conf = TransactionManagerServices.getConfiguration();
    conf.setServerId("jvm-1");
    conf.setLogPart1Filename("./tx-logs/part1.btm");
    conf.setLogPart2Filename("./tx-logs/part2.btm");
```

**Transaction Engine Settings**

These configurable properties are related to the transaction manager's core.

下面这些配置与实务管理的核心相关。

#### bitronix.tm.serverId

A stable ASCII string that must uniquely identify this TM instance. It must not exceed 51 characters or it will be truncated.

一个合适的ASCII字符串，必须唯一标识这个TM实例。它不能超过51个字符，否则会被截断。

<table>
  <thead>
    <tr>
      <th style="text-align:left">Configuration object property</th>
      <th style="text-align:left">serverId</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Default</td>
      <td style="text-align:left">
        <p>The machine&apos;s IP address but that&apos;s unsafe for production usage</p>
        <p>&#x670D;&#x52A1;&#x5668;IP&#xFF0C;&#x4F46;&#x662F;&#x8FD9;&#x6837;&#x505A;&#x5728;&#x751F;&#x4EA7;&#x73AF;&#x5883;&#x4E0D;&#x5B89;&#x5168;</p>
      </td>
    </tr>
  </tbody>
</table>#### bitronix.tm.2pc.async

Should two phase commit be executed asynchronously? Asynchronous two phase commit will improve 2PC execution time when there are many resources enlisted in transactions but can be very CPU intensive when used on JDK 1.4 without the java.util.concurrent backport implementation available on the classpath. It also makes debugging more complex.

是否应该异步执行two phase commit。在许多资源在事物中时，异步two phase commit将增加2PC执行时间，但是在JDK1.4且classpath中没有java.util.concurrent backport实现时，可以充分使用CPU。

| Configuration object property | asynchronous2Pc |
| :--- | :--- |
| Default | false |

#### bitronix.tm.2pc.warnAboutZeroResourceTransactions

Should transactions executed without a single enlisted resource result in a warning or not? Most of the time transactions executed with no enlisted resource reflect a bug or a mis-configuration somewhere.

是否事物执行时没有资源的时候发出警告。大多数情况，事物执行时没有资源反映出有bug或者某个地方配置错误。

| Configuration object property | warnAboutZeroResourceTransaction |
| :--- | :--- |
| Default | true |

#### bitronix.tm.2pc.debugZeroResourceTransactions

Should creation and commit call stacks of transactions executed without a single enlisted resource tracked and logged or not? This is a companion to warnAboutZeroResourceTransaction where the transaction creation and commit call stacks could help you identify the culprit code.

是否在没有追踪和记录单个加入的资源时执行创建和提交事物的调用栈。这个和warnAboutZeroResourceTransaction组合使用，事物创建和提交调用栈可以帮助你定定位到错误的代码位置。

| Configuration object property | debugZeroResourceTransaction |
| :--- | :--- |
| Default | false |

#### bitronix.tm.disableJmx

The transaction manager registers objects in the JMX registry by default if available. Set this to true to never register JMX objects.

如果JMX可用，事物管理注册对象默认会注册到JMX。若设置这个属性为true，则不会注册到JMX。

| Configuration object property | disableJmx |
| :--- | :--- |
| Default | false |

#### bitronix.tm.jndi.userTransactionName

The name under which the transaction manager will be bound in the internal JNDI provider.

事物管理器将会被绑定到内部JNDI提供商的名字。

| Configuration object property | jndiUserTransactionName |
| :--- | :--- |
| Default | java:comp/UserTransaction |

#### **bitronix.tm.allowMultipleLrc**

Should the transaction manager allow multiple LRC resources to be enlisted into the same transaction? Having multiple LRC resources participate in a transaction gives up the recovery guarantee but sometimes is useful in development mode.

是否事物管理器允许多个LRC资源被加入同一个事物。有多个LRC资源参加事物时放弃保证恢复，但是有的时候在开发者模式下很有用。

| Configuration object property | allowMultipleLrc |
| :--- | :--- |
| Default | false |

**bitronix.tm.currentNodeOnlyRecovery**

Set this to true if you run multiple instances of the transaction manager on the same JMS and JDBC resources to avoid the recovery process to try to recover transactions started by another node.

如果你在相同的JMS和JDBC资源运行多个事物管理的实例，则设置这个属性为true，以避免恢复进程尝试恢复其它节点启动的事物。

| Configuration object property | currentNodeOnlyRecovery |
| :--- | :--- |
| Default | true |

**Journal Settings**

These configurable properties are related to the disk journal used to record recovery information.

这个可配置的属性与disk journal有关，用于记录恢复信息。

**bitronix.tm.journal**

Set the journal to be used to record transaction logs. This can be any of disk, null or a class name. The disk journal is a classic implementation using two fixed-size files and disk forces, the null journal just allows one to disable logging. This can be useful to run tests. Do not use the null journal on production as without transaction logs, atomicity cannot be guaranteed.

设置journal来用作记录事物日志。这个可以是磁盘的任何地方、null或者类名。disk journal是一个经典的实现，使用两个固定大小的文件和disk forces，null journal允许禁用日志。这个可以用作测试。不要在生产环境使用null journal，因为没有事物日志，原子性不能被保证。

| Configuration object property | journal |
| :--- | :--- |
| Default | disk |

**bitronix.tm.journal.disk.logPart1Filename**

Journal fragment file 1.

| Configuration object property | logPart1Filename |
| :--- | :--- |
| Default | btm1.tlog |

**bitronix.tm.journal.disk.logPart2Filename**

Journal fragment file 1.

| Configuration object property | logPart2Filename |
| :--- | :--- |
| Default | btm2.tlog |

**bitronix.tm.journal.disk.forcedWriteEnabled**

Are logs forced to disk? \*\*Do not set to false on production since without disk force, atomicity cannot be guaranteed.\*\*

是否强制写入日志到磁盘？\*\*在生产环境不要设置为false，因为没有disk force，原子性不能被保证。\*\*

| Configuration object property | forcedWriteEnabled |
| :--- | :--- |
| Default | true |

**bitronix.tm.journal.disk.maxLogSize**

Maximum size in megabytes of the journal fragments. Larger logs allow transactions to stay longer in-doubt but the TM pauses longer when a fragment is full.

journal fragments的最大大小（MB）。较大的日志允许事物保存更久in-doubt，但是当一个fragment满了的时候，TM暂停更久。

| Configuration object property | maxLogSize |
| :--- | :--- |
| Default | 2 |

**bitronix.tm.journal.disk.filterLogStatus**

Should only mandatory logs be written? Enabling this parameter lowers space usage of the fragments but makes debugging more complex.

是否仅mandatory logs被强制写入？启用这个参数减少fragments的空间使用率，但是使得调试更复杂。

| Configuration object property | filterLogStatus |
| :--- | :--- |
| Default | false |

**bitronix.tm.journal.disk.skipCorruptedLogs**

Should corrupted transactions log entries be skipped? Use only at last resort when all you have to recover is a pair of corrupted files.

是否跳过损坏的事物日志。只有在你必须恢复一对损坏的文件时使用。

| Configuration object property | skipCorruptedLogs |
| :--- | :--- |
| Default | false |

**bitronix.tm.conservativeJournaling**

Should transactions be appended to the log in a single-threaded fashion similar to BTM 2.x? Setting conservative journaling can have a large impact on performance and should only be set to workaround a bug in the new BTM 3.x parallel log appending support.

是否应该用类似BTM 2.x单线程的方式将日志加入事物？设置conservative journaling很影响性能，应该仅被设置用来解决在新的BTM 3.x并行添加的bug。

| Configuration object property | conservativeJournaling |
| :--- | :--- |
| Default | false |

**Timers Settings**

The transaction manager heavily relies on timeouts. All of them can be configured.

事物管理器各种超时配置都可以被设置。

**bitronix.tm.timer.defaultTransactionTimeout**

Default transaction timeout in seconds.

默认的事物超时时间（秒）

| Configuration object property | defaultTransactionTimeout |
| :--- | :--- |
| Default | 60 |

**bitronix.tm.timer.gracefulShutdownInterval**

Maximum amount of seconds the TM will wait for transactions to get done before aborting them at shutdown time.

TM在关闭时，在终止事物前等待事物完成的最大时间（秒）。

| Configuration object property | gracefulShutdownInterval |
| :--- | :--- |
| Default | 60 |

**bitronix.tm.timer.backgroundRecoveryIntervalSeconds**

Interval in seconds at which to run the recovery process in the background. Cannot be disabled.

后台运行的恢复进程间隔时间（秒）。不可以禁用。

| Configuration object property | backgroundRecoveryIntervalSeconds |
| :--- | :--- |
| Default | 60 |

**Resource Loader Settings**

 The resource loader loads and configures XA resources using configuration stored in a properties file. See the [Resource Loader](https://github.com/bitronix/btm/wiki/Resource-loader-configuration) page for more details.

resource loader加载和配置XA资源使用的配置保存在properties文件中，参考[Resource Loader](https://github.com/bitronix/btm/wiki/Resource-loader-configuration)页面了解更多。

**bitronix.tm.resource.configuration**

Resource Loader configuration file name.

Resource Loader配置文件名称。

| onfiguration object property | resourceConfigurationFilename |
| :--- | :--- |
| Default | none \(optional\) |

**Connection Pools Settings**

JDBC and JMS connection pools configuration are discussed in details in the [JDBC pools configuration](https://github.com/bitronix/btm/wiki/JDBC-pools-configuration) and the JMS pools configuration pages. Alternatively you can use the [Resource Loader](https://github.com/bitronix/btm/wiki/Resource-loader-configuration) instead.

JDBC和JMS连接池配置参考[JDBC pools configuration](https://github.com/bitronix/btm/wiki/JDBC-pools-configuration) 和JMS pools配置页面（没看到）。或者用 [Resource Loader](https://github.com/bitronix/btm/wiki/Resource-loader-configuration) 替代。







