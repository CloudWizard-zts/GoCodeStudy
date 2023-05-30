# 13-流程控制for-range循环

> go语言中可以使用for-range遍历数组、切片、字符串、map及通道(channel)。通过for range遍历的返回值有以下规律：
> 1. 数组、切片、字符串返回**索引和值**
> 2. map返回**键和值**
> 3. 通道(channel)只返回通道内的值

`遍历数组`：
```go
import "fmt"

func main() {

    // go中的数组 [数组的长度]int{数组}
    var a = [...]int{1, 2, 3}
    for i, v := range a { // i和v分别代表键和值，range后面的就是对象，数组、切片等都可以
        fmt.Printf("%v:%v ", i, v)
    }
}

//执行结果:0:1 1:2 2:3  就是键值对,0对应1,1对应2,2对应3
```
`遍历切片`：
```go
package main

import "fmt"

func main() {

    // []中什么都不写就代表切片
    var s = []int{1, 2, 3, 4}
    for _, v := range s { // 这里直接使用_省略索引(下标),直接取值
        fmt.Printf("%v", v)
    }
}
```
`遍历map`：
```go
package main

import "fmt"

func main() {

    // key:value
    // 定义了一个map,key和value都是string
    m := make(map[string]string)
    m["name"] = "tom"
    m["age"] = "20"
    m["email"] = "tom@gmail.com"

    for key, value := range m {
        fmt.Printf("%v: %v\n", key, value)
    }
}

/* 执行结果: name: tom
            age: 20
            email: tom@gmail.com */
```
`打印字符串`：
```go
package main

import "fmt"

func main() {

    a := "好好学习"
    for _, v := range a {
        fmt.Printf("%c\n", v) // %c将一个整数格式化为对应的 Unicode 字符
    }
}
```