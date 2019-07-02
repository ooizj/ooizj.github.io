# linux防火墙设置

### linux防火墙设置

```text
--查看防火墙状态
firewall-cmd --state

--添加tcp 80端口访问权限
firewall-cmd --zone=public --add-port=80/tcp --permanent
--zone #作用域
--add-port=80/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

--重启防火墙
systemctl restart  firewalld
```

