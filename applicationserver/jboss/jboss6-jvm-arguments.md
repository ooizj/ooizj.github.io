# jboss6修改jvm参数

下面以jboss6修改最大堆内存参数为例

## 方式一

添加环境变量JAVA\_OPTS的方式进行修改（会影响到所有的jboss应用）

```text
JAVA_OPTS="-Xms1303m -Xmx2048m -XX:MaxPermSize=256m"
```

## 方式二

直接修改JBOSS\_HOME/bin/standalone.conf文件的方式（会影响的同一jboss内的其它应用）  
找到类似如下部分直接修改“Xmx”

```text
if [ "x$JAVA_OPTS" = "x" ]; then
   JAVA_OPTS="-Xms1303m -Xmx2048m -XX:MaxPermSize=100m -Djava.net.preferIPv4Stack=true"
   JAVA_OPTS="$JAVA_OPTS -Djboss.modules.system.pkgs=$JBOSS_MODULES_SYSTEM_PKGS -Djava.awt.headless=true"
   JAVA_OPTS="$JAVA_OPTS -Djboss.modules.policy-permissions=true"
else
   echo "JAVA_OPTS already set in environment; overriding default settings with values: $JAVA_OPTS"
fi
```

## 方式三

在环境变量中新增一个变量， 然后在需要修改最大堆内存的应用的启动脚本中修改该变量， 修改JBOSS\_HOME/bin/standalone.conf文件根据该变量判断如何设置最大堆内存等参数  
如果不想影响到其他应用的设置，可以采用此种方式。

1，新增环境变量“CUSTOMER\_JAVA\_OPTS”

```text
# 编辑环境变量
vim /etc/profile
```

```text
export CUSTOMER_JAVA_OPTS=""
```

```text
# 刷新环境变量
source /etc/profile
```

2，修改JBOSS\_HOME/bin/standalone.conf文件

```text
# 将此文件中需要修改的jvm参数放到“LOCAL_JAVA_OPTS”变量中
LOCAL_JAVA_OPTS="-Xms1303m -Xmx1303m -XX:MaxPermSize=256m"

# 修改JAVA_OPTS当“CUSTOMER_JAVA_OPTS”的值不为空时用“CUSTOMER_JAVA_OPTS”的值替代“LOCAL_JAVA_OPTS”部分
if [ "x$JAVA_OPTS" = "x" ]; then
   if [ "x$CUSTOMER_JAVA_OPTS" != "x" ]; then
      LOCAL_JAVA_OPTS="$CUSTOMER_JAVA_OPTS"
   fi
#   JAVA_OPTS="-Xms1303m -Xmx1303m -XX:MaxPermSize=256m -Djava.net.preferIPv4Stack=true"
   JAVA_OPTS="$LOCAL_JAVA_OPTS -Djava.net.preferIPv4Stack=true"
   JAVA_OPTS="$JAVA_OPTS -Djboss.modules.system.pkgs=$JBOSS_MODULES_SYSTEM_PKGS -Djava.awt.headless=true"
   JAVA_OPTS="$JAVA_OPTS -Djboss.modules.policy-permissions=true"
else
   echo "JAVA_OPTS already set in environment; overriding default settings with values: $JAVA_OPTS"
fi
```

3，修改应用启动脚本

```text
CUSTOMER_JAVA_OPTS="-Xmx3072m -XX:MaxPermSize=512m"
# 启动应用
#如 nohup bin/standalone.sh -Djboss.server.base.dir=xx/jboss_home/app1 -Djboss.socket.binding.port-offset=1 &
```

