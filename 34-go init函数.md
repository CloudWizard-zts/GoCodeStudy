# 34-go init函数
go中有一个特殊的函数`init`函数，先与`main`函数执行，实现包级别的一些**初始化**操作

init函数的主要特点
* init函数先于main函数**自动执行**，不能被其他函数调用
* init函数没有输入参数、返回值；
* 每个包可以有多个init函数
* **包的每个源文件也可以有多个init函数**，这点比较特殊
* 同一个包的init执行顺序，golang没有明确定义，编程时要注意程序不要依赖这个执行顺序
* 不同的init函数按照包导入的依赖关系决定执行顺序

golang初始化顺序：
变量初始化-->init()-->main()
`实例`：
```go
package main

import "fmt"

func init() {
    fmt.Println("init...")
}

func main() {
    // 没有调用init函数，init函数会自动执行，并且在main函数前面执行
    fmt.Println("main...")
}
```
`实例2`：
```go
package main

import "fmt"

func init() {
    fmt.Println("init...")
}

var i = initVar()

func initVar() int {
    fmt.Println("initVar...")
    return 100
}

func main() {
    
    fmt.Println("main...")
}
```
```go
执行结果：
// 符合初始化顺序，先初始化变量i，然后init函数，最后main函数
initVar...
init...
main...
```
`如果有多个init函数，谁在上面谁先执行，但一般不写多个init函数`：
```go
package main

import "fmt"

func init() {
    fmt.Println("init2...")
}

func init() {
    fmt.Println("init...")
}

func main() {

}
```