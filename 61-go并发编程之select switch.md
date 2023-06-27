# 61-go并发编程之select switch
1. selects是go中的一个控制结构，类似`switch`语句，用于处理异步IO操作，`select`会监听case语句中channel的读写操作，当case中channel读写操作为非阻塞状态(即能读能写)时，将触发相应的动作。
> select中的语句必须是一个channel操作
select中的default子句总是可运行的
2. 如果有多个`case`都可以运行，`select`会随机公平的选出一个执行，其他不会执行
3. 如果没有可运行的`case`语句，且有`default`语句，那么就会执行`default`的动作
4. 如果没有可运行的`case`语句，且没有`default`语句，`select`将会阻塞，直到某个`case`通信可运行

## 实例
```go
package main

import (
    "fmt"
    "time"
)

var chanInt = make(chan int)
var chanString = make(chan string)

func main() {

    go func() {
        chanInt <- 100
        chanString <- "hello"
    }()

    select {
    case r := <-chanInt:
        fmt.Printf("chanInt: %v\n", r)
    case r := <-chanString:
        fmt.Printf("chanString: %v\n", r)
    default:
        fmt.Println("default...")
    }

    time.Sleep(time.Second)
}
```
这段代码实际上是有bug的，它的输出会一直都是default，由于channel是无缓冲的，即使发送操作已经完成，但接收操作需要等待主函数的`select`语法开始执行才能进行，而在这之前，`select`语句已经完成，所以对应了特性3：如果没有可运行的`case`语句，且有`default`语句，那么就会执行`default`的动作。

`更改后代码`：
```go
package main

import (
    "fmt"
    "time"
)

var chanInt = make(chan int)
var chanString = make(chan string)

func main() {

    go func() {
        chanInt <- 100
        chanString <- "hello"
        close(chanInt) // 关闭通道
        close(chanString) // 关闭通道
    }()

    for {
        select {
        case r := <-chanInt:
            fmt.Printf("chanInt: %v\n", r)
        case r := <-chanString:
            fmt.Printf("chanString: %v\n", r)
        default:
            fmt.Println("default...")
        }
        time.Sleep(time.Second)
    }
}
```
这段代码使用了for循环，因此for会一直循环`select`语句，r就可以接收到来自chanInt和chanString的值，因为我们已经关闭了通道，所以在循环两次接收完chanInt和chanString的值之后，就会出发特性3：如果没有可运行的`case`语句，且有`default`语句，那么就会执行`default`的动作


