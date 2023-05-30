# 24-map的遍历
> 一般都使用**for range**的方式进行遍历，得到key和value值

`1.遍历key`：
```go
package main

import "fmt"

func main() {

    var m1 = map[string]string{"name": "test", "email": "google.com", "age": "20"}
    for key := range m1 {
        fmt.Printf("key: %s\n", key)
    }
}
```
`执行结果`：
```go
key: name
key: email
key: age
```
`2.遍历value和key`：
```go
package main

import "fmt"

func main() {

    var m1 = map[string]string{"name": "test", "email": "google.com", "age": "20"}

    for k, v := range m1 {
        fmt.Printf("key: %s, value: %s\n", k, v)
    }
}
```
`执行结果`：
```go
key: name, value: test
key: email, value: google.com
key: age, value: 20
```
