# httpd安装ssl模块

## httpd安装ssl模块

```text
yum install mod_ssl
```

安装完成后在“/etc/httpd/modules”目录可以看到名为“mod\_ssl.so”模块，  
在“/etc/httpd/conf.d”出现了“ssl.conf”的配置

## 添加方向代理测试一下

假设有个应用访问路径为 “[http://127.0.0.1:8877/fileserver/”](http://127.0.0.1:8877/fileserver/”)  
现在想用 “[http://127.0.0.1:8080/fileserver/”](http://127.0.0.1:8080/fileserver/”) 的地址去访问它，则可以添加反向代理来实现

编辑“/etc/httpd/conf.d/ssl.conf”，在“”节点添加反向代理信息

```text
<VirtualHost _default_:443>

    ....

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

## 重启httpd

```text
service httpd restart
```

检查443端口是否是httpd在监听（lsof未安装可以使用“yum install lsof”命令进行安装）

```text
lsof -i tcp:443
```cmd

如果防火墙没有开启443端口的权限，则使用下面的命令进行开启
```cmd
firewall-cmd --zone=public --add-port=443/tcp --permanent
```

## 测试是否能访问

在其他机器通过 “[https://ip/fileserver/”](https://ip/fileserver/”) 访问看看能否成功

