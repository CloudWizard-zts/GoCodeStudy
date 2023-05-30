# 14-流程控制关键字break
`跳出for循环`：
```go
package main

import "fmt"

func main() {
    for i := 0; i < 10; i++ {
        fmt.Println(i)
        if i == 5 { // 当i等于5时,跳出循环
            break
        }
    }
}
```
`switch语句`：
```go
package main

import "fmt"

func main() {
    i := 2
    switch i {
    case 1:
        fmt.Println("1")
    case 2:
        fmt.Println("2")
        break
        fallthrough // 前面使用了dreak，即便这里用了fallthrough关键字，也不会穿透到下一个case
    case 3:
        fmt.Println("3")
    }
}

```
`跳转到标签`：
```go
package main

import "fmt"

func main() {

MYLABEL:
    for i := 0; i <= 10; i++ {
        fmt.Println(i)
        if i == 5 {
            break MYLABEL
        }
    }
}

/*
MYLABEL 是一个标签，它被用于 for 循环之前。在 for 循环内部，当 i 的值等于 5 时，执行了 break MYLABEL 语句，表示跳出了标签所在的代码块。由于标签位于 for 循环之前，因此跳转到该标签后会直接结束整个 for 循环。
标签并不是 Go 语言推荐的用法，通常情况下可以通过良好的代码设计和结构，避免使用标签。
*/
```