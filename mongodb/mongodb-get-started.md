# mongoDB get started

## 1，安装

官方install文档

{% embed url="https://docs.mongodb.com/manual/administration/install-community/" caption="" %}

以下以Mac安装为例，和linux以及windows区别不大

### 下载安装包

{% embed url="https://www.mongodb.com/try/download/community" %}

### 解压缩

```bash
tar -zxvf mongodb-macos-x86_64-5.0.tgz
```

### 拷贝bin目录下的文件到/usr/local/bin或者创建符号链接/usr/local/bin

```bash
# 拷贝
sudo cp /path/to/the/mongodb-directory/bin/* /usr/local/bin/

# 创建符号链接
sudo ln -s  /path/to/the/mongodb-directory/bin/* /usr/local/bin/
```

## 2，启动

```text
mongod --dbpath /usr/local/var/mongodb --logpath /usr/local/var/log/mongodb/mongo.log --fork
```

## 3，安装客户端

MongoDB Compass是MongoDB官方出品的客户端，现在已经可以免费下载使用

{% embed url="https://www.mongodb.com/try/download/compass" %}

打开客户端后输入连接信息（没有设置用户名密码端口等则默认就行）

```text
mongodb://localhost:27017
```



