## go的推迟、恐慌和恢复

> 一个**defer语句**推动一个函数调用到列表中。**保存的调用列表在周围函数返回后执行。**

### **使用场景：Defer语句通常用于~~简化~~执行各种清理操作的函数**

举个例子：

```
func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }

    written, err = io.Copy(dst, src)
    dst.Close()
    src.Close()
    return
}
//这有效，但有一个错误。如果对 os.Create 的调用失败，该函数将返回而不关闭源文件,但是可以通过在第二个return语句之前调用src.Close 来解决，但是如果函数更加的复杂，那么这个问题将不会轻易的被注意到，更加优雅的做法是，打开文件之后，我们在第一个return语句之后，（因为一旦返回，证明打开失败，就不需要关闭文件了）执行defer src.Close()来延迟关闭文件，它将会在第二个os.Create()语句失败之后，第二个语句return 语句返回之后执行关闭。
//这也验证了那句话：一个defer语句推动一个函数调用的列表,保存的函数调用的列表，会在（周围）函数返回之后执行！！！这个周围二字你要慢慢体会，很精辟！！

func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }
    defer src.Close()

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }
    defer dst.Close()

    return io.Copy(dst, src)
}


Defer 语句允许我们在打开每个文件后立即考虑关闭它，保证无论函数中有多少个 return 语句，文件都将被关闭。
```

### **defer 语句的行为是直接且可预测的。有三个简单的规则：**

#### 1.*在计算 defer 语句时计算延迟函数的参数。*

```
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
```



#### 2.*延迟的函数调用在周围函数返回后以后进先出的顺序执行*。

```
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
此函数打印“3210”：
```



#### 3.*延迟函数可以读取并分配给返回函数的命名返回值。*

```
func c() (i int) {
    defer func() { i++ }()
    return 1
}
//在此示例中，延迟函数 在周围函数返回后增加返回值 i 。因此，此函数返回 2
```

**这样方便修改函数的错误返回值；我们很快就会看到一个例子。**



### **Panic**是一个内置函数，它停止普通的控制流并开始*恐慌*

> 也就是说：当一个函数F内部调用panic时，这个函数F的执行将停止，但是F中任何的延迟执行的函数将正常执行，然后F返回给它的调用者。对于调用者而言，F的行为就像是调用panic.这个过程继续向上堆栈，直到当前的`goroutine`中所有的函数返回。此时程序崩溃。恐慌可以通过直接调用恐慌来启动。他们可能是由运行时的错误引起：例如数组访问越界。



### **Recover**是一个内置函数，可以重新控制恐慌的 `goroutine`

> 值得注意的是：Recover只在延迟调用的函数中有用，这个是为什么呢？
>
> 因为对于正常执行期间，调用recovery函数只会返回一个nil并且没有其他影响。但是如果当前的`goroutine `处于恐慌时，调用`recovery`,`recovery `将捕获给予`goroutine `恐慌的值，并且恢复正常执行。
>
> 以下是一个演示恐慌和延迟机制的例子：

```
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
        //r := recover()就是捕获引起恐慌的值，在根据这个值的空与否来进行进一步的操作。
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}



func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}



猜测输出：
Calling g.
Printing in g", 0
Printing in g", 1
Printing in g", 2
Printing in g", 3
Panicking!
Returned normally from g.
Defer in g", 3
Defer in g", 2
Defer in g", 1
Defer in g", 0
Returned normally from f
Recovered in f 4

//判断错误是因为：延迟执行的打印语句
defer func() {
        if r := recover(); r != nil {
        //r := recover()就是捕获引起恐慌的值，在根据这个值的空与否来进行进一步的操作。
            fmt.Println("Recovered in f", r)
        }
    }()
    个人认为这些语句是在f 里面，所以要在main函数返回后执行所以要在Returned normally from f.这个语句之后，目前还未得到解决。





实际输出：

Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
Recovered in f 4
Returned normally from f.

如果我们从 f 中删除延迟函数，恐慌不会恢复并到达 goroutine 调用堆栈的顶部，终止程序。这个修改后的程序为：
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}



func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}



这个修改后的程序将输出：

Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
panic: 4
panic PC=0x2a9cd8
[stack trace omitted]

与我们捕获恐慌数据不同的是：我们看到，当我们不调用recover来捕获引起的数据时 ，程序就会奔溃，不会继续往下执行，并且会抛出引起恐慌的数据，和错误提示，让主函数停下来。
```



有关**panic**和**recovery**的真实示例，请参阅Go 标准库中的[json 包](https://golang.org/pkg/encoding/json/)。它使用一组递归函数对接口进行编码。如果在遍历值时发生错误，则调用 panic 将堆栈展开到顶级函数调用，该函数调用从 panic 中恢复并返回适当的错误值（参见 encodeState 类型的 'error' 和 'marshal' 方法在[encode.go 中](https://golang.org/src/pkg/encoding/json/encode.go)）。

Go 库中的约定是，即使包在内部使用 panic，其外部 API 仍会显示明确的错误返回值。

### *defer 的其他用途*——释放互斥锁

```
mu.Lock()
defer mu.Unlock()

//个人觉得在并发编程那一章这个defer关键字释放互斥锁的功能还是很强大的！！！

```

### *defer 的其他用途*——打印页脚

```
printHeader()
defer printFooter()
```





### 吐血总结：

> defer 语句为控制流提供了一种不寻常而且强大的机制。

