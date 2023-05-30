# 16-流程控制关键字goto

> goto语句通过标签进行代码间的**无条件跳转**。goto语句可以在快速跳出循环、避免重复退出上有一定的帮助。go语言中使用goto语句能简化一些代码的实现过程。例如双层嵌套的for循环要退出时

`跳转到指定标签`：
```go
package main

import "fmt"

func main() {

    i := 1
    if i >= 2 {
        fmt.Println("Please")
    } else {
        goto END
    }
END:
    fmt.Println("end...")
}

// 执行结果为 end... 通过goto直接跳转到END标签
```
`跳出双重循环`：
```go
package main

import "fmt"

func main() {
    for i := 0; i < 10; i++ {
        for j := 0; j < 10; j++ {
            if i == 2 && j == 2 {
                goto END
            }
            fmt.Printf("%v,%v\n", i, j)
        }
    }
END:
    fmt.Println("END...")
}

// 执行结果：当循环到2.2时，满足if条件，goto跳转到END标签，打印END...
```
