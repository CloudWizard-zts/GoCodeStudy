# 33go defer语句
go语言中的`defer`语句会将其后面跟随的语句进行**延迟**处理。在`defer`归属的函数即将返回时，将延迟处理的语句按`defer`定义的**逆序**进行执行，也就是说，先被`defer`的语句最后执行，最后被`defer`的语句，最先被执行。

defer特性
1. 关键字`defer`用于注册延迟调用
2. 这些调用直到`return`前才被执行。因此用来做资源清理
3. 多个`defer`语句，按先进后出的方式执行
4. `defer`语句中的变量，在`defer`声明时就决定了

defer用途
1. 关闭文件句柄
2. 锁资源释放
3. 数据库连接释放

`go语言defer语句实例：`
查看执行顺序
```go
package main

import "fmt"

func main() {
    // 正常顺序依次向下输出
    // defer的特性先进后出，所以Starting、end输出结束后，最后的step3最先输出，先进的step1最后输出
    fmt.Println("Starting")
    defer fmt.Println("step1")
    defer fmt.Println("step2")
    defer fmt.Println("step3")
    fmt.Println("end")
}
```
`执行结果`：
```go
Starting
end
step3
step2
step1
```