> https://github.com/golang/go/tree/master/src/container/ring

在调用Ring.Do时，会依次将每个节点的Value当做参数调用这个函数，实际上这是策略方法的应用，通过传递不同的函数，可以在同一个ring上实现多种不同的操作。下面展示一个简单的遍历打印程序。

```go
package main

import (
    "container/ring"
    "fmt"
)

func main() {
    r := ring.New(10)

    for i := 0; i < 10; i++ {
        r.Value = i
        r = r.Next()
    }

    sum := SumInt{}
    r.Do(func(i interface{}) {
        fmt.Println(i)
    })
}
```

除了简单的无状态程序外，也可以通过结构体保存状态，例如下面是一个对ring上值求和的程序。

```go
package main

import (
    "container/ring"
    "fmt"
)

type SumInt struct {
    Value int
}

func (s *SumInt) add(i interface{}) {
    s.Value += i.(int)
}

func main() {
    r := ring.New(10)

    for i := 0; i < 10; i++ {
        r.Value = i
        r = r.Next()
    }

    sum := SumInt{}
    r.Do(sum.add)
    fmt.Println(sum.Value)
}
```