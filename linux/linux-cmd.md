# linux 常用命令

## 文件移动（or改名）：

```bash
mv 源文件（or目录，多个文件or目录用空格隔开） 目标文件（or目录）
```

## 创建目录

```bash
mkdir 目录名
```

## 查看系统磁盘使用情况

```bash
df -lh
df -m （按MB为单位）
```

## 查看系统内存使用情况

```bash
free -m
free -g
```

## 根据名称查看进程

```bash
ps -ef|grep 名称（如：tomcat/redis/java/...）
```

## 查找文件

```bash
find 目录 -name 文件名（可用通配符：“*”）
```

## 查看文件夹下有哪些文件

```bash
ls  
ls -a（显示所有文件，包含隐藏文件）
```

## 查看目录大小

```bash
du -h --max-depth=1
```

## 新增用户

```bash
useradd xiaoming
```

## 删除用户

```bash
userdel xiaoming  
userdel -r（也删除home） xiaoming
```

## 修改文件的或目录的所有者或组

```bash
chown -R（递归处理） 用户:组 文件或目录  
chown -R xiaoming:xxxgroup /opt/myapp  
chown -R xiaoming /opt/myapp
```

## 从一个电脑拷贝文件到另一个电脑

```bash
scp 本地文件 远程电脑用户名@远程电脑IP:远程电脑目录or文件
scp jdk-8u181-linux-x64.tar.gz root@192.168.0.2:/opt/es/
```

## 查看centos版本

```bash
cat /etc/redhat-release
```

## 解压缩

\*.tar.gz/\*.tgz

```bash
tar -zxvf 压缩文件名.tar.gz
# v 是否显示解压缩过程
```

## 查看占用某端口应用的PID

```bash
lsof -i tcp:端口
lsof -i tcp:10001
```

