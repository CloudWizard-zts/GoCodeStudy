# 56-go并发编程之通道channel

go提供了一种称为通道的机制，用于在goroutine(协程)之间**共享数据**。当你作为goroutine执行并发活动时，需要在goroutine之间共享资源或数据，通道充当goroutine之间的管道并提供一种机制来保证同步交换。

需要在声明通道时指定**数据类型**，我们可以通过共享内置、命名、结构和引用类型的值和指针。数据在通道上传递：在任何给定时间只有一个goroutine可以访问数据项：因此按照设计不会发生数据竞争。

根据数据交换的行为，有两种类型的通道：无缓冲通道和缓冲通道。无缓冲通道用于执行goroutine之间的同步通信，而缓冲通道用于执行异步通信。无缓冲通道保证在发送和接收发送的瞬间执行两个goroutine之间的交换。缓冲通道没有这样的保证。

简单来说，有两个协程，协程A发送一个馒头给协程B，无缓冲通道处理时，如果协程B不把馒头拿走，会产生堵塞，协程A就不会继续发送，是同步；有缓冲通道则是，可以协程A一次存5个馒头，协程B拿走一个继续存一个，是一个异步执行

**通道由make函数创建，该函数指定chan关键字和通道的元素类型**

## 创建无缓冲通道和缓冲通道的代码块：

`语法`：

```go
Unbuffered := make(chan int)  // 整型无缓冲通道
buffered := make(chan int, 10) // 整型有缓冲通道
```

使用内置函数`make`创建无缓冲和缓冲通道。`make`的第一个参数需要关键字`chan`，然后是通道允许交换的数据类型。

## 将值发送到通道的代码块需要使用<-运算符

`语法`：

```go
goroutine1 := make(chan string, 5) // 字符串缓冲通道
goroutine1 <- "hello" // 通过通道发送字符串
```

## 从通道接收代码块

```go
data :=  <-goroutine1  //  从通道接收字符串
```

`<-`运算符附加到通道变量(goroutine1)的左侧，以接收来自通道的值

## 通道的发送和接收特性

1. 对于同一个通道，发生操作之间是互斥的，接收操作之间也是互斥的。
2. 发送操作和接收操作中对元素值的处理都是不可分割的。
3. 发送操作在完全完成之前会被阻塞。接收操作也是如此。

## 实例

```go
package main

import (
    "fmt"
    "math/rand"
    "time"
)

// 创建一个int通道
var values = make(chan int)

func seed() {
    // 使用当前时间的纳秒级别作随机数种子
    rand.New(rand.NewSource(time.Now().UnixNano()))
    value := rand.Intn(10) // 生成一个范围是0-9的随机整数
    fmt.Printf("seed: %v\n", value)
    values <- value // 将value的随机数发送到通道中
}

func main() {

    // defer 延时函数
    // close 函数用于关闭通道
    // 这个意思是在main函数结束前关闭values通道
    defer close(values)
    go seed()
    fmt.Println("wait...")
    value := <-values // 将通道values接收到的值赋给value
    fmt.Printf("receive: %v\n", value)
    fmt.Println("end...")
}
```
