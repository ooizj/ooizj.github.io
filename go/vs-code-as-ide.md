# 用vs code作为go的开发环境

### 1，安装go

下载地址：[https://golang.org/dl/](https://golang.org/dl/) 一步步安装即可

安装完成后，在cmd输入go env可以查看gopath、goroot等环境信息

![](../.gitbook/assets/image%20%2815%29.png)

### 1，为VS Code安装go插件

View-&gt;Extensions，搜索go，找到并安装

![](../.gitbook/assets/image%20%2813%29.png)

![](../.gitbook/assets/image%20%2810%29.png)

### 3，安装以下模块

{% hint style="info" %}
对于“golang.org”开头的可能会超时，可以通过下面的命令设置代理
{% endhint %}

设置代理（不需要的跳过）

```bash
# 启用 Go Modules 功能
go env -w GO111MODULE=on

# 配置 GOPROXY 环境变量，以下三选一

# 1. 七牛 CDN
go env -w  GOPROXY=https://goproxy.cn,direct

# 2. 阿里云
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/,direct

# 3. 官方
go env -w  GOPROXY=https://goproxy.io,direct
```

需要安装的模块

```bash
go get -u golang.org/x/tools/cmd/goimports
go get -u golang.org/x/tools/cmd/gorename
go get -u github.com/sqs/goreturns
go get -u github.com/mdempsky/gocode
go get -u github.com/alecthomas/gometalinter
go get -u github.com/mgechev/revive
go get -u github.com/golangci/golangci-lint/cmd/golangci-lint
go get -u github.com/zmb3/gogetdoc
go get -u github.com/zmb3/goaddimport
go get -u github.com/rogpeppe/godef
go get -u golang.org/x/tools/cmd/guru
go get -u github.com/fatih/gomodifytags
go get -u github.com/tpng/gopkgs
go get -u github.com/ramya-rao-a/go-outline
```

![](../.gitbook/assets/image%20%2816%29.png)

### 4，编写HelloWorld

新建文件夹，并用VSCode打开它，打开Terminal（Vew-&gt;Terminal），输入以下命令

![](../.gitbook/assets/image%20%2817%29.png)

```bash
go mod init 模块名（如：hello）
```

新建 hello.go

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

打开左侧的Run Tab页，点击Run and Debug，第一次运行go程序可能会安装一些模块，根据提示操作即可

![](../.gitbook/assets/image%20%2814%29.png)

![](../.gitbook/assets/image%20%2811%29.png)

### 5，编译成exe

在Terminal输入go build即可

