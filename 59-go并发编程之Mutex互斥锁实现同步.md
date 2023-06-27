# 59-go并发编程之Mutex互斥锁实现同步
除了使用channel实现同步之外，还可以使用mutex互斥锁的方式实现同步。
互斥锁（Mutex）是一种并发控制原语，用于保护共享资源的访问。互斥锁提供了一种机制，确保同一时间只有一个goroutine可以进入临界区（被锁定的代码段），从而避免数据竞态（Data Race）和非预期行为的发生。

当一个goroutine获取到互斥锁时（使用Lock()方法），它可以继续执行临界区的代码。其他尝试获取该互斥锁的goroutine会被阻塞，直到该互斥锁被释放。一旦goroutine完成了对共享资源的操作，它会通过调用Unlock()方法来释放互斥锁，这样其他被阻塞的goroutine就有机会获取互斥锁并访问临界区。

互斥锁提供了一种排他性访问的方式，在同一时间只允许一个goroutine访问共享资源，从而避免了并发读写导致的数据不一致性问题。互斥锁是一种简单有效的并发控制机制，但需要注意避免死锁情况的发生，即多个goroutine互相等待对方释放锁而无法继续执行的情况。因此，在使用互斥锁时，需要仔细设计和编写代码，确保正确地获取和释放锁的顺序。

`先来回顾一下使用WaitGroup实现同步的方式`：
```go
package main

import (
    "fmt"
    "sync"
)

var i int = 100

var wg sync.WaitGroup

func add() {
    defer wg.Done()
    i += 1
    fmt.Printf("i++: %d\n", i)
}

func sub() {
    defer wg.Done()
    i -= 1
    fmt.Printf("i--: %d\n", i)
}

func main() {
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go add()
        wg.Add(1)
        go sub()
    }

    wg.Wait()

    fmt.Printf("end i: %d\n", i)
}
```
`虽然我们使用了WaitGroup的方法使得主函数main会等待所有goroutine执行结束后才会执行，但是i是一个全局变量，并发操作下存在竞争条件(race condition)。多个goroutine修改同一个变量i，就可以导致多个goroutine执行的顺序不正确，例如，在某个goroutine执行i+=1的时候，另一个goroutine已经执行i-=1的操作了，最终结果就会偏离预期。`

`我们在上面代码的基础上，加上互斥锁`
```go
package main

import (
    "fmt"
    "sync"
)

var i int = 100

var wg sync.WaitGroup

var lock sync.Mutex

func add() {
    defer wg.Done()
    lock.Lock() // .Lock 上锁
    i += 1
    fmt.Printf("i++: %d\n", i)
    lock.Unlock()  // .Unlock 解锁
}

func sub() {
    lock.Lock() // 这里放在了defer前面，add放在了后面，并不影响，因为我们只是想对i的操作进行限制
    defer wg.Done()
    i -= 1
    fmt.Printf("i--: %d\n", i)
    lock.Unlock()
}

func main() {
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go add()
        wg.Add(1)
        go sub()
    }

    wg.Wait()

    fmt.Printf("end i: %d\n", i)
}
```
`注意，这里add和sub函数都需要加互斥锁，因为add和sub是以goroutine的方式并发执行的，例如sub函数不加互斥锁，那么sub函数就可能在add函数做出修改之前访问变量i，就会导致竞态条件，导致最终结果不正确。`