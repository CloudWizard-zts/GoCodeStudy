# 57-go并发编程之WaitGroup实现同步

简单来说就是两个协程之间相互等待，协程A没有执行完，协程B会等待协程A
`实例演示`：
查看添加`WaitGroup`和不添加`WaitGroup`d的区别
```go
// 不添加WaitGroup
package main  
  
import "fmt"  
  
func showMsg(s int) {  
	fmt.Printf("s: %v\n", s)  
}  
  
func main() {  
  
	for i := 0; i < 10; i++ {  
		// 启动一个协程来执行  
		go showMsg(i)  
}  
  
// 主协程  
	fmt.Println("end...")  
}
```
```go
执行结果来看，并不能执行完全部协程，主协程就退出了
```
`加入WaitGroup`：
```go
package main

import (
    "fmt"
    "sync"
)

var wp sync.WaitGroup

func showMsg(s int) {
    // wp.Add(-1) 使用.Done，其实就是Add(-1)，可以点击Done进去查看
    // 加defer是 当fmt语句执行结束才调用wp.Done
    defer wp.Done()
    fmt.Printf("s: %v\n", s)
}

func main() {

    for i := 0; i < 10; i++ {
        // 启动一个协程来执行
        go showMsg(i)
        wp.Add(1) //
    }

    // Wait会生成一个阻塞，在计数器归零时解除阻塞继续执行主协程
    wp.Wait()
    // 主协程
    fmt.Println("end...")
}
```