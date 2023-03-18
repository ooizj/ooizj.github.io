# windows使用ssh连接到github上传代码
使用ssh连接到github上传代码更稳定

## 生成密钥
在Git Bash输入生成密钥命令
```brash
ssh-keygen -t rsa -b 4096 -C "邮箱地址"
# 输出 Enter file in which to save the key (/c/Users/xx/.ssh/id_rsa): 需要输入保存的文件名（最好不要加扩展名）
# 输出 Enter passphrase (empty for no passphrase): 需要输入密码（密码是为了保证密钥被偷后还有一层保护）
# 输出 Enter same passphrase again: 需要再次输入密码
# 输出 The key fin... 以下省略
```
然后根据提示输入保存的文件名（最好不要加扩展名）和密码（密码是为了保证密钥被偷后多一层保护，要使用必须输入密码）  

## 将生成的密钥加到ssh-agent让其生效
在windows命令行工具里面输入以下命令
```brash
ssh-agent bash
ssh-add C:\Users\xx\.ssh\之前输入保存的文件名
```

## 测试是否生效
输入
```brash
ssh -T git@github.com
```
如果输出以下文本，则表示成功了
```brash
Hi xx! You've successfully authenticated, but GitHub does not provide shell access.
```

