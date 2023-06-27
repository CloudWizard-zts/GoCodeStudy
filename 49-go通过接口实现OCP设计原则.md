# 49-go通过接口实现OCP设计原则
面向对象的可复用设计的第一块基石，便是所谓的‘开-闭’原则，(Open-Closed Principle,常缩写为OCP)，虽然go不是面向对象语言，但是也可以模拟实现这个原则。
什么是开闭原则：对**扩展**是开放的，对**修改**是关闭的。

![](vx_images/159542011230666.png)

## OCP设计原则实例
下面通过一个人养宠物的例子，来解释OCP设计原则
`定义一个宠物接口Pet`：
```go
type Pet interface {
    eat()
    sleep()
}
```
该接口有吃和睡两个方法
`定义一个Dog结构体`：
```go
type Dog struct{}
```
`Dog实现接口方法`：
```go
func (d Dog) eat() {
    fmt.Println("dog eat ...")
}
func (d Dog) sleep() {
    fmt.Println("dog sleep ...")
}
```
`定义一个Cat结构体`：
```go
type Cat struct{}
```
`Cat实现接口方法`：
```go
func (c Cat) eat() {
    fmt.Println("cat eat ...")
}
func (c Cat) sleep() {
    fmt.Println("cat sleep ...")
}
```
`定义一个Person结构体`：
```go
type Person struct{}
```
`为Person添加一个养宠物的方法`：
```go
func (p Person) care(pet Pet) {
    p.eat()
    p.sleep()
}
```
`最后测试一下`：
```go
func main() {
    dog := Dog{}
    cat := Cat{}
    per := Person{}
    
    per.care(dog)
    per.care(cat)
}
```
`运行结果`：
```go
// 通过Person直接管理了dog和cat的eat、sleep方法
dog eat ...
dog sleep ...
cat eat ...
cat sleep ...
```

`完整源代码`：
```go
package main

import "fmt"

type Pet interface {
    eat()
    sleep()
}

type Dog struct{}
type Cat struct{}

// Dog实现Pet接口
func (d Dog) eat() {
    fmt.Println("dog eat ...")
}
func (d Dog) sleep() {
    fmt.Println("dog sleep ...")
}

// Cat实现Pet接口
func (c Cat) eat() {
    fmt.Println("cat eat ...")
}
func (c Cat) sleep() {
    fmt.Println("cat sleep ...")
}

type Person struct{}

// pet 既可以传递Dog又可以传递Cat
func (Person) care(pet Pet) {
    pet.eat()
    pet.sleep()
}

func main() {
    dog := Dog{}
    cat := Cat{}
    per := Person{}
    per.care(dog)
    per.care(cat)
}
```