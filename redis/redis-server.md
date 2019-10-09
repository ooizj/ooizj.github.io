---
description: redis服务端安装和运行
---

# redis-server

### redis下载

redis目前最新稳定版本为5.0.5，这里使用的是redis4.x的版本，下载地址：[http://download.redis.io/releases/redis-4.0.14.tar.gz](http://download.redis.io/releases/redis-4.0.14.tar.gz)

### redis编译

```bash
# 解压
tar -zxvf redis-4.0.14.tar.gz

# 进入解压后的目录
cd redis-4.0.14

# 编译
# 如果没有make命令，如果是ubantu，可以使用“apt-get install build-essential”安装make
make
```

如果编译出现类似“fatal error: jemalloc/jemalloc.h: No such file or directory”可以按以下步骤尝试解决（[https://dzone.com/articles/building-redis-from-source-on-ubuntu-server-1804](https://dzone.com/articles/building-redis-from-source-on-ubuntu-server-1804)）

```bash
cd deps
make hiredis jemalloc linenoise lua geohash-int
cd ../
make
```

### redis启动

需要用到的文件有&lt;redis\_home&gt;/src/redis-cli、&lt;redis\_home&gt;/src/redis-server、&lt;redis\_home&gt;/redis.conf、&lt;redis\_home&gt;/sentinel.conf，将这些文件拷贝到任意目录都可以使用。

```bash
# 简单启动
redis-server redis.conf
```

