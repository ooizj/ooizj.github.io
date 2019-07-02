# linux 常用命令

## 文件移动（or改名）：

```text
mv 源文件（or目录，多个文件or目录用空格隔开） 目标文件（or目录）
```

## 创建目录

```text
mkdir 目录名
```

## 查看系统磁盘使用情况

```text
df -lh
df -m （按MB为单位）
```

## 查看系统内存使用情况

```text
free -m
free -g
```

## 根据名称查看进程

```text
ps -ef|grep 名称（如：tomcat/redis/java/...）
```

## 查找文件

```text
find 目录 -name 文件名（可用通配符：“*”）
```

## 查看文件夹下有哪些文件

```text
ls  
ls -a（显示所有文件，包含隐藏文件）
```

## 新增用户

```text
useradd xiaoming
```

## 删除用户

```text
userdel xiaoming  
userdel -r（也删除home） xiaoming
```

## 修改文件的或目录的所有者或组

```text
chown -R（递归处理） 用户:组 文件或目录  
chown -R xiaoming:xxxgroup /opt/myapp  
chown -R xiaoming /opt/myapp
```

## 从一个电脑拷贝文件到另一个电脑

```text
scp 本地文件 远程电脑用户名@远程电脑IP:远程电脑目录or文件
scp jdk-8u181-linux-x64.tar.gz root@192.168.0.2:/opt/es/
```

## 查看centos版本

```text
cat /etc/redhat-release
```

## 解压缩

\*.tar.gz/\*.tgz

```text
tar -zxvf 压缩文件名.tar.gz
# v 是否显示解压缩过程
```

