# 50-go模拟OOP的属性和方法
go没有面向对象的概念，也没有封装的概念，但是可以通过结构体`struct`和函数**绑定**来实现OOP的属性和方法等特性。通过接收者receiver就变成了**方法**

以下就是通过go语法概念来实现OOP思想中的封装概念
例如，想要定义一个Person类，有name和age属性，有eat/sleep/work方法
```go
package main

import "fmt"

// 定义Person结构体
type Person struct {
    name string
    age  int
}

type care interface {
    eat()
    sleep()
    work()
}

func (p Person) eat() {
    fmt.Println("eat ...")
}

func (p Person) sleep() {
    fmt.Println("sleep ...")
}

func (p Person) work() {
    fmt.Println("work...")
}
func main() {
    per := Person{
        name: "tom",
        age:  20,
    }
    fmt.Println(per)
    // 通过结构体来调用，说明它们是绑定在一起的
    per.eat()
    per.sleep()
    per.work()
}
```