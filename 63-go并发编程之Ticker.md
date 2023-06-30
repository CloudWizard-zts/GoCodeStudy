# 63-go并发编程之Ticker
Timer只执行一次，Ticker可以周期的执行
`实例`：
```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ticker := time.NewTicker(time.Second)

    for _ = range ticker.C {
        fmt.Printf("当前时间: %v\n", <-ticker.C)
    }
}
```
就会一直输出当前时间
如果想要停止，可以加一段循环：
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    timer := time.NewTicker(time.Second)

    counters := 0
    for _ = range timer.C {
        counters++
        if counters >= 5 {
            timer.Stop()  // 结束
            break  // 结束循环
        }
        fmt.Printf("当前时间: %v\n", <-timer.C)
    }

}
```

`
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    ticker := time.NewTicker(time.Second)

    chatInt := make(chan int)

    // channel写操作，每隔一秒钟随机的朝chatInt中写入1，2，3
    go func() {
        for _ = range ticker.C {
            select {
            case chatInt <- 1:
            case chatInt <- 2:
            case chatInt <- 3:
            }
        }
    }()

    // channel接收操作
    sum := 0
    for v := range chatInt {
        fmt.Printf("收到: %d\n", v)
        sum += v 
        if sum >= 10 { // 当接收的值大于等于10的时候，停止循环
            break
        }
    }

}
```