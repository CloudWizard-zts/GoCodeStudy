# 62-go并发编程之Timer
Timer顾名思义，就是定时器的意思，可以实现一些定时操作，器内部也是可以通过channel来实现的。

## Timer有关等待
```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // 定义等待2秒
    timer := time.NewTimer(time.Second * 2)
    fmt.Printf("当前时间: %v\n", time.Now()) // time.Now()用于获取当前时间
    t1 := <-timer.C  // 阻塞的，直到时间到了，.C是表示channel的首字母，实际上这是一个通道
    fmt.Printf("t1: %v\n", t1)
}
```

`如果只是想单纯的等待，可以使用time.Sleep来实现`：
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    fmt.Printf("当前时间: %v\n", time.Now())
    timer := time.NewTimer(time.Second * 2)
    fmt.Printf("time now: %v\n", time.Now())
    <-timer.C // 可以发现，now2的时间是前面两个执行完后，等待两秒才执行的
    fmt.Printf("time now2: %v\n", time.Now())
    fmt.Println("-----------------------------")
    fmt.Printf("当前时间: %v\n", time.Now())
    fmt.Printf("time now: %v\n", time.Now())
    time.Sleep(2 * time.Second) // 使用Sleep和上面效果是一样的
    fmt.Printf("time now2: %v\n", time.Now())
}
```
`或者使用After`
```go
package main

import (
    "fmt"
    "time"
)

func main() {
    fmt.Printf("time now: %v\n", time.Now())
    <-time.After(time.Second * 2)
    fmt.Printf("time now1: %v\n", time.Now())
}

/*
文档可以看到：
After实际上就是Time的通道
func After(d Duration) <-chan Time {
	return NewTimer(d).C
}
*/
```

## Stop阻塞
`先看一下正常运行的效果`：
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    timer := time.NewTimer(time.Second)
    go func() {
        <-timer.C
        fmt.Printf("func...\n")
    }()

    time.Sleep(3 * time.Second)
    fmt.Println("main end...")
}
```
```运行结果：
func...
end...
```
`加入stop后`：
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    timer := time.NewTimer(time.Second)
    go func() {
        <-timer.C
        fmt.Printf("func...\n")
    }()

    s := timer.Stop()
    if s {
        fmt.Println("stop...")
    }

    time.Sleep(3 * time.Second)
    fmt.Println("main end...")
}
```
```go
运行结果：
stop...
main end...
```
可以看到，并没有执行func...
Stop的作用是让其他线程的timer停止了，但是main主线程不会停止
`Stop也可以不赋值使用`：
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    timer := time.NewTimer(time.Second)
    go func() {
        <-timer.C
        fmt.Printf("func...\n")
    }()

    timer.Stop()
    fmt.Println("stop...")

    time.Sleep(3 * time.Second)
    fmt.Println("main end...")
}
```

## Reset重新设置时间
```go
package main

import (
    "fmt"
    "time"
)

func main() {

    fmt.Printf("当前时间: %v\n", time.Now())
    // 设置5s
    timer := time.NewTimer(5 * time.Second)
    timer.Reset(2 * time.Second) // 使用Reset重新设置为2秒
    <-timer.C // 调用
    fmt.Printf("time: %v\n", time.Now())
}
```