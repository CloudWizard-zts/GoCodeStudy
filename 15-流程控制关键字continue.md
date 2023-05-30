# 15-流程控制关键字continue

> continue只能用在循环中，在go中只能用在for循环中，它可以终止本次循环，进行下一次循环。在continue语言后添加标签时，表示开始标签对应的循环

`输出1到10之间的偶数`：
```go
package main

import "fmt"

func main() {

    for i := 0; i <= 10; i++ {
        if i%2 == 0 {
            fmt.Println(i)
        } else {
            continue // 当i的余数不为0时，终止本次循环，开始下一轮
        }
    }
}
```
`跳转到标签`：
```go
package main

import "fmt"

func main() {

    for i := 0; i < 4; i++ {
    MYLABEL:
        for j := 0; j < 4; j++ {
            if i == 2 && j == 2 {
                continue MYLABEL // 当i=2并且j=2时，终止此次循环吗，并跳转到MYLABEL标签处继续循环，所以执行后没有2,2的结果
            }
            fmt.Printf("%v,%v\n", i, j)
        }
    }
}
```
