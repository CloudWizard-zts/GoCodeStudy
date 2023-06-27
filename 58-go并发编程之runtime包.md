# go并发编程之runtime包
runtime包里面定义了一些协程管理相关的api

## runtime.Gosched()
让出cpu时间片，重新等待安排任务
```go
package main

import (
    "fmt"
    "runtime"
)

func show(s string) {
    for i := 0; i < 2; i++ {
        fmt.Println(s)
    }
}

func main() {

    go show("java") // 启动一个子协程

    for i := 0; i < 5; i++ {
        // 每次执行时，我都让出来，等待其他协程执行结束我在执行
        // 如果不写，那么主函数一开始就运行结束了，show子协程就直接被kill了
        runtime.Gosched() // 注释掉看结果
        fmt.Println("golang")
    }
}
```

## runtime.Goexit()
退出当前协程
```go
package main

import (
    "fmt"
    "runtime"
    "time"
)

func show() {
    for i := 0; i < 10; i++ {
        fmt.Printf("i: %v\n", i)
        if i >= 5 {
            runtime.Goexit()
        }
    }
}

func main() {
    go show()

    // time.Second 等同与 const Second Duration = 1000 * Millisecond
    // 等待 1000 毫秒
    // 目的是让main主函数不要立即结束，否则子协程就被kill了
    time.Sleep(time.Second)
}
```
## runtime.NumCpu
获取cpu的核心数
```go
package main

import (
    "fmt"
    "runtime"
)

func main() {
    fmt.Printf("Cpu核心数: %v\n", runtime.NumCPU())
}
```
`运行结果`：
```go
Cpu核心数: 8
```

## runtime.GOMAXPROCS
设置最大cpu核心数
`go默认是使用多核心，所以执行以下代码会发现a，b交替打印`
```go
package main

import (
    "fmt"
    "time"
)

func a() {
    for i := 0; i < 1000; i++ {
        fmt.Printf("a: %d\n", i)
    }
}

func b() {
    for i := 0; i < 1000; i++ {
        fmt.Printf("b: %d\n", i)
    }
}

func main() {
    go a()
    go b()

    time.Sleep(time.Millisecond * 1000000)
}
```
`使用runtime.GOMAXPROCS`:
```go
package main

import (
    "fmt"
    "runtime"
    "time"
)

func a() {
    for i := 0; i < 1000; i++ {
        fmt.Printf("a: %d\n", i)
    }
}

func b() {
    for i := 0; i < 1000; i++ {
        fmt.Printf("b: %d\n", i)
    }
}

func main() {
    runtime.GOMAXPROCS(1)
    go a()
    go b()

    time.Sleep(time.Millisecond * 1000)
}
```

