
### httpd2.4的安装
系统：centos7

### 安装
```cmd
#安装httpd
yum install httpd

#开机启动
systemctl enable httpd

#启动httpd
systemctl start httpd
```

### httpd常用命令

```cmd
#启动httpd
service httpd start

#停止httpd
service httpd stop

#重启httpd
service httpd restart
```


### httpd工作目录
```cmd
#主配置文件
/etc/httpd/conf/httpd.conf

#自定义配置文件的目录（在主配置文件中有引用conf.d/*.conf）
/etc/httpd/conf.d

#默认日志目录
/etc/httpd/logs

```

### 配置反向代理
假设有个应用访问路径为 “http://127.0.0.1:8877/fileserver/”  
现在想用 “http://127.0.0.1:8080/fileserver/” 的地址去访问它，则可以添加反向代理来实现  

在“/etc/httpd/conf.d”目录新增“my.conf”，文件内容如下  
```xml

#监听“8080”端口
Listen 8080

<VirtualHost *:8080>
    
    ProxyRequests Off
    <Proxy *>
    Order deny,allow
    Allow from all
    </Proxy>

    ProxyPreserveHost On
	
    ProxyPass /fileserver/ http://127.0.0.1:8877/fileserver/
    ProxyPassReverse /fileserver/ http://127.0.0.1:8877/fileserver/


</VirtualHost>

```
重启httpd使之生效
```cmd
service httpd restart
```

然后在其他机器通过 “http://ip:8080/fileserver/” 访问看看能否成功  
如果访问不到可以看看防火墙是否开启了8080端口访问权限  
```cmd
--查看防火墙状态
firewall-cmd --state

--添加tcp 8080端口访问权限
firewall-cmd --zone=public --add-port=8080/tcp --permanent

--重启防火墙
systemctl restart  firewalld
```

如果访问之后报“service unavailable”的错误，可以通过以下设置试试
```cmd
/usr/sbin/setsebool -P httpd_can_network_connect 1
```

