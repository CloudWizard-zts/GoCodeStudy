# 54-go包管理工具go module

go module是golang1.11新加的特性，用来管理模块中**包的依赖关系**

## go mod使用方法

* 初始化模块
  `go mod init <项目模块名称>`
* 依赖关系处理，根据go.mod文件
  `go mod tidy`
* 将依赖包复制到项目下的vendor目录
  `go mod vendor`
  如果包被屏蔽(墙)，可以使用这个命令，随后使用go build -mod=vendor编译
* 显示依赖关系
  `go list -m all`
* 显示详细依赖关系
  `go list -m -json all`
* 下载依赖
  `go mod download [path@version]`
  path@version是非必写的

## 实例演示：

`先在dao.go中编写代码`：

```go
package dao

import "fmt"

// 函数名开头大写表示是公开的
func All() {
    fmt.Println("我是dao包中的All函数...")
}
```

`打开终端`：

```go
// 执行命令
go mod init mygolang(文件夹名称)
```

![](vx_images/162795014259983.png)

`然后我们继续在终端进行操作`：

```go
cd到dao这个包下
然后执行go build
go build 之后他就自动编译到缓存里面去了
```

![](vx_images/181735214257587.png)

`接着我们在main.go中调用这个包`：

```go
package main

// 会自动补全包所在的位置
import "mygolang/dao"

func main() {
    // 调用dao包中的All函数
    dao.All()
}
```

`执行后可以发现没有任何问题`
![](vx_images/571985414255089.png)

## 实例演示2：

我们还想再创建一个客户的包
![](vx_images/118735814236330.png)

```go
继续回到终端，cd到custom文件夹
执行go build
```

![](vx_images/421700915253906.png)

```go
// 在main.go中调用custom包
package main

import (
    "mygolang/custom"
    "mygolang/dao"
)

func main() {
    dao.All()
    custom.Custom()
}
```

`输出没有问题`
![](vx_images/323571115247452.png)

## 实例演示3：

如果在同一个包中有多个项目，如：
![](vx_images/66951315240586.png)

我们并不需要重复执行go build
直接编写完代码，回到main.go调用即可

输出没有任何问题：
![](vx_images/533071315231116.png)

## 使用在线包：

如果我们想使用gin包，我们需要先下载
打开终端执行命令
`go get -u github.com/gin-gonic/gin`

这个下载了非常多的依赖
![](vx_images/102623515233620.png)

可以看到，已经将包和依赖加入go.mod文件了，因为没有使用，所以是灰色
![](vx_images/518433515242567.png)

我们直接使用gin提供的实例代码：

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "pong",
        })
    })
    r.Run() // listen and serve on 0.0.0.0:8080
}
```

运行后没有问题
![](vx_images/279424715235452.png)

浏览器访问也没有任何问题
![](vx_images/432834715236061.png)

使用了这个包，go.mod中就变彩色了
![](vx_images/174734915245229.png)