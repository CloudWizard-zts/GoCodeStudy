# 60-go并发编程之channel的遍历

## 方法一：for循环+if判断
```go
package main

import "fmt"

// 定义一个int类型通道
var c = make(chan int)

func main() {

    // 启动一个匿名函数的子协程
    go func() {
        for i := 0; i < 2; i++ {
            c <- i
        }
    }() // 小括号表示自己调用自己

    for i := 0; i < 3; i++ {
        r := <-c
        fmt.Printf("r: %v\n", r)
    }
}
```
`执行结果`：
可以看到，我们在通道中存入了2个，但是读的时候，读了3次，所以前两次读是正确的，最后一次报错了。
```go
r: 0
r: 1
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan receive]:
main.main()
        C:/awesomeProject/hello.go:18 +0x48
```
`解决这个问题，我们可以在通道存完毕后，关闭这个通道`：
```go
    go func() {
        for i := 0; i < 2; i++ {
            c <- i
        }
        close(c) // 使用close表示关闭channel
    }() // 小括号表示自己调用自己
```
`运行结果`：
```go
r: 0
r: 1
r: 0
```
没有任何问题
for循环的方式还有一种写法，如下(推荐使用for range的方式)：
```go
package main

import "fmt"

// 定义一个int类型通道
var c = make(chan int)

func main() {

    // 启动一个匿名函数的子协程
    go func() {
        for i := 0; i < 2; i++ {
            c <- i
        }
        close(c)
    }() // 小括号表示自己调用自己

    for {
        v, ok := <-c
        if ok { // 当ok为true的时候输出v
            fmt.Printf("r: %v\n", v)
        } else { // 否则退出
            break
        }
    }
}
```

## 方法二： for range
```go
package main

import "fmt"

// 定义一个int类型通道
var c = make(chan int)

func main() {

    // 启动一个匿名函数的子协程
    go func() {
        for i := 0; i < 2; i++ {
            c <- i
        }
        close(c)
    }() // 小括号表示自己调用自己

    for v := range c {
        fmt.Printf("r: %v\n", v)
    }
}
```