# go 语言指针

> Go语言为程序员提供了控制[数据结构](http://c.biancheng.net/data_structure/)指针的能力，但是，并不能进行指针运算。Go语言允许你控制特定集合的数据结构、分配的数量以及内存访问模式，这对于构建运行良好的系统是非常重要的。指针对于性能的影响不言而喻，如果你想要做系统编程、操作系统或者网络应用，指针更是不可或缺的一部分。

**指针（pointer）在Go语言中可以被拆分为两个核心概念：**

- 类型指针：允许对这个指针类型的数据进行修改，传递数据可以直接使用指针，而无需拷贝数据。类型指针不能进行偏移和运算
- 切片 ：由指向原始起始元素的指针，元素数量，容量组成

**Go语言的指针类型变量即拥有指针高效访问的特点，又不会发生指针偏移，从而避免了非法修改关键性数据的问题。同时，垃圾回收也比较容易对不会发生偏移的指针进行检索和回收**

**切片比原始指针具备更强大的特性，而且更为安全。切片在发生越界时，运行时会报出宕机，并打出堆栈，而原始指针只会崩溃。**

#### C/[C++](http://c.biancheng.net/cplus/)中的指针

说到 C/C++ 中的指针，会让许多人“谈虎色变”，尤其是对指针的偏移、运算和转换。

其实，指针是 C/C++ 语言拥有极高性能的根本所在，在操作大块数据和做偏移时即方便又便捷。因此，操作系统依然使用C语言及指针的特性进行编写。

C/C++ 中指针饱受诟病的根本原因是指针的运算和内存释放，C/C++ 语言中的裸指针可以自由偏移，甚至可以在某些情况下偏移进入操作系统的核心区域，我们的计算机操作系统经常需要更新、修复漏洞的本质，就是为解决指针越界访问所导致的“缓冲区溢出”的问题。

## 认识指针地址和指针类型

一个指针变量可以指向任何一个值的内存地址，它所指向的值的内存地址在 32 和 64 位机器上分别占用 4 或 8 个字节，占用字节的大小与所指向的值的大小无关。当一个指针被定义后没有分配到任何变量时，它的默认值为 nil。指针变量通常缩写为 ptr

每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用在变量名前面添加`&`操作符（前缀）来获取变量的内存地址（取地址操作），格式如下：

```
ptr = &v //v的类型是T
```

其中 v 代表被取地址的变量，变量 v 的地址使用变量 ptr 进行接收，ptr 的类型为`*T`，称做 T 的指针类型，`*`代表指针。

**变量、指针和地址三者的关系是，每个变量都拥有地址，指针的值就是地址。**

## 从指针获取指针指向的值

```
package main
import (
    "fmt"
)
func main() {
    // 准备一个字符串类型
    var house = "Malibu Point 10880, 90265"
    // 对字符串取地址, ptr类型为*string
    ptr := &house
    // 打印ptr的类型
    fmt.Printf("ptr type: %T\n", ptr)
    // 打印ptr的指针地址
    fmt.Printf("address: %p\n", ptr)
    // 对指针进行取值操作
    value := *ptr
    // 取值后的类型
    fmt.Printf("value type: %T\n", value)
    // 指针取值后就是指向变量的值
    fmt.Printf("value: %s\n", value)
}


ptr 类型是*string
value 类型是string
fmt.Printf("address: %p\n", ptr)打印出的是ptr自身的地址

运行结果：
ptr type: *string
address: 0xc0420401b0
value type: string
value: Malibu Point 10880, 90265
```

变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：

* 对变量进行取地址操作使用`&`操作符，可以获得这个变量的指针变量
* 指针变量的值是指针变量指向的原变量的地址
* 对指针变量进行取值操作使用`*`操作符，可以获得指针变量指向的原变量的值。

## 使用指针修改值

```
package main

import "fmt"
// 交换函数
func swap(a, b *int) {
    // 取a指针的值, 赋给临时变量t
    //*a出现在右边，所以它是取a指针指向的变量的值
    t := *a
    // 取b指针的值, 赋给a指针指向的变量
    //*a出现在左边，所以它是a指针指向的变量
    //*b出现在右边，所以它是b指针指向的变量的值
    *a = *b
    // 将a指针的值赋给b指针指向的变量
    //*b出现在左边，所以它是b指针指向的变量
    *b = t
}
func main() {
// 准备两个变量, 赋值1和2
    x, y := 1, 2
    // 交换变量值
    swap(&x, &y)
    // 输出变量值
    fmt.Println(x, y)
}
```

**`*`操作符作为右值时，意义是取指针的值，作为左值时，也就是放在赋值操作符的左边时，表示 a 指针指向的变量**

> **归根结底，*操作符的根本意义是，操作指针变量指向的变量，只不过是当操作在右边时，代表取指针变量指向的变量的值，操作在左边时，代表指针变量指向的变量**

## 使用指针变量获取命令行的输入信息

Go语言内置的 flag 包实现了对命令行参数的解析，flag 包使得开发命令行工具更为简单。

```
package main
// 导入系统包
import (
    "flag"
    "fmt"
)
// 定义命令行参数
var mode = flag.String("mode", "", "process mode")
func main() {
    // 解析命令行参数
    flag.Parse()
    // 输出命令行参数
    fmt.Println(*mode)
}
使用如下命令运行
go run main.go --mode=fast

输出结果
fast 

第 10 行，通过 flag.String，定义一个 mode 变量，这个变量的类型是 *string。后面 3 个参数分别如下：
参数名称：在命令行输入参数时，使用这个名称。
参数值的默认值：与 flag 所使用的函数创建变量类型对应，String 对应字符串、Int 对应整型、Bool 对应布尔型等。
参数说明：使用 -help 时，会出现在说明中。

```



# golang的拼接字符串方法

```go

package main
 
import "strings"
 
func main(){
	a := "hahaha"
	b := "hehehe"
	c := strings.Join([]string{a,b},",")
	println(c)

```

* 在已有字符串数组的场合，使用 `strings.Join()` 能有比较好的性能
* 在一些性能要求较高的场合，尽量使用 `buffer.WriteString()` 以获得更好的性能
* 较少字符串连接的场景下`strings.Join`性能最好，而且代码更简短清晰，可读性更好
* 如果需要拼接的不仅仅是字符串，还有数字之类的其他需求的话，可以考虑 `fmt.Sprintf`

### 字符串拼接在 golang 里面其实有很多种实现

#### 1. 直接使用运算符

```go
func BenchmarkAddStringWithOperator(b *testing.B) {
    hello := "hello"
    world := "world"
    for i := 0; i < b.N; i++ {
        _ = hello + "," + world
    }
}

func BenchmarkAddMoreStringWithOperator(b *testing.B) {
    hello := "hello"
    world := "world"
    for i := 0; i < b.N; i++ {
        var str string
        for i := 0; i < 100; i++ {
            str += hello + "," + world
        }
    }
}
```

golang 里面的字符串都是不可变的，每次运算都会产生一个新的字符串，所以会产生很多临时的无用的字符串，不仅没有用，还会给 gc 带来额外的负担，所以性能比较差

#### 2. fmt.Sprintf()

```go
func BenchmarkAddStringWithSprintf(b *testing.B) {
    hello := "hello"
    world := "world"
    for i := 0; i < b.N; i++ {
        _ = fmt.Sprintf("%s,%s", hello, world)
    }
}
```

内部使用 `[]byte` 实现，不像直接运算符这种会产生很多临时的字符串，但是内部的逻辑比较复杂，有很多额外的判断，还用到了 `interface`，所以性能也不是很好

#### 3. strings.Join()

```
func BenchmarkAddStringWithJoin(b *testing.B) {
    hello := "hello"
    world := "world"
    for i := 0; i < b.N; i++ {
        _ = strings.Join([]string{hello, world}, ",")
    }
}
```

join会先根据字符串数组的内容，计算出一个拼接之后的长度，然后申请对应大小的内存，一个一个字符串填入，在已有一个数组的情况下，这种效率会很高，但是本来没有，去构造这个数据的代价也不小

具体的使用代码：

```go
package main
import (
	"fmt"
	"strings"
)
func main() {
	
	//使用 join 函数，实现拼接字符串
	str1 := "Hello,"
	str2 := "World"
	var str = []string{str1, str2}
	strHaiCoder := strings.Join(str, "")
    //func Join(elems []string, sep string) string
    //Join将其第一个参数的元素连接起来以创建单个字符串。分隔符字符串sep放置在结果字符串中的元素之间
	fmt.Println("strHaiCoder =", strHaiCoder)
}
```

- 定义字符串变量 ，赋值为“Hello,”，接着又定义了一个字符串变量，赋值为 “World”。
- 根据字符串变量定义并初始化一个字符串数组，最后使用strings.Join()实现字符串的拼接

> 使用场景1--数组转化为字符串：我们有时候经常需要将一个数组或者List的各项通过分隔符连接成字符串。一般的实现逻辑是通过成员+分隔符连接，然后在结果截掉最后一个分隔符，先通过分隔符连接字符串，然后去掉分隔符。
>
> 使用场景2--字符串转化为数组`string.split()`
>
> `string.split()`的功能：
>
> 将切片 s 拆分为由 sep 分隔的所有子字符串，并返回这些分隔符之间的子字符串的切片。
>
> 如果 s 不包含 sep 并且 sep 不为空，则 Split 返回长度为 1 的切片，其唯一元素是 s。
>
> 如果 sep 为空，则 Split 在每个 UTF-8 序列之后拆分。如果 s 和 sep 都为空，则 Split 返回一个空切片。
>
> ```go 
> func Split(s, sep string ) [] string
> ```

深入学习链接地址https://golang.org/pkg/strings/#Join





#### 4. buffer.WriteString()

这个比较理想，可以当成可变字符使用，对内存的增长也有优化，如果能预估字符串的长度，还可以用 `buffer.Grow()` 接口来设置 capacity

```go
func BenchmarkAddStringWithBuffer(b *testing.B) {
    hello := "hello"
    world := "world"
    for i := 0; i < b.N; i++ {
        var buffer bytes.Buffer
        buffer.WriteString(hello)
        buffer.WriteString(",")
        buffer.WriteString(world)
        _ = buffer.String()
    }
}

func BenchmarkAddMoreStringWithBuffer(b *testing.B) {
    hello := "hello"
    world := "world"
    for i := 0; i < b.N; i++ {
        var buffer bytes.Buffer
        for i := 0; i < 100; i++ {
            buffer.WriteString(hello)
            buffer.WriteString(",")
            buffer.WriteString(world)
        }
        _ = buffer.String()
    }
}
```



具体代码示例：

```
package main
import (
	"bytes"
	"fmt"
)
func main() {
	
	//使用 buffer.WriteString 函数拼接字符串
	str1 := "Hello,"
	str2 := "HaiCoder"
	var bt bytes.Buffer
	bt.WriteString(str1)//先用 bytes.Buffer 的 WriteString 方法把字符串写入变量
	bt.WriteString(str2)
	strHaiCoder := bt.String()
	//再用bytes.Buffer 的 String 方法，把字符串 str1 和 字符串 str2 进行了拼接
	//函数原型:func (b *Buffer) String() string    
	//String 以字符串形式返回缓冲区未读部分的内容。如果 Buffer 是一个 nil 指针，它返回“<nil>”
	
	fmt.Println("strHaiCoder =", strHaiCoder)
}
```

- 首先，我们定义了一个字符串变量，赋值为 “Hello,”，定义了另一个字符串变量，赋值为 “HaiCoder”，接着又定义了一个 bytes.Buffer 类型的变量 bt。

- 我们使用 bytes.Buffer 的 WriteString 方法将变量 str1 和变量 str2 写入 bt 变量

- 最后，我们使用 bytes.Buffer 的 String 方法，实现了把字符串 str1 和 字符串 str2 进行了拼接

  



# 用Go 的 benchmark性能测试的工具

```
// fmt.Sprintf
func BenchmarkStringSprintf(b *testing.B) {
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        var str string
        for j := 0; j < numbers; j++ {
            str = fmt.Sprintf("%s%d", str, j)
        }
    }
    b.StopTimer()
}

// add
func BenchmarkStringAdd(b *testing.B) {
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        var str string
        for j := 0; j < numbers; j++ {
            str = str + string(j)
        }
    }
    b.StopTimer()
}

// bytes.Buffer
func BenchmarkStringBuffer(b *testing.B) {
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        var buffer bytes.Buffer
        for j := 0; j < numbers; j++ {
            buffer.WriteString(strconv.Itoa(j))
        }
        _ = buffer.String()
    }
    b.StopTimer()
}

// strings.Builder
func BenchmarkStringBuilder(b *testing.B) {
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        var builder strings.Builder
        for j := 0; j < numbers; j++ {
            builder.WriteString(strconv.Itoa(j))
        }
        _ = builder.String()
    }
    b.StopTimer()
}
```

运行结果：

```
lijun:benchmark/ $ go test -bench=.                                                           [10:01:20]
goos: darwin
goarch: amd64
pkg: class12/benchmark
BenchmarkStringSprintf-4              30          47358694 ns/op
BenchmarkStringAdd-4                  50          27664814 ns/op
BenchmarkStringBuffer-4            10000            184422 ns/op
BenchmarkStringBuilder-4           10000            157039 ns/op
PASS
ok      class12/benchmark       6.350s
lijun:benchmark/ $
```

比较结果：

`strings.Builder > bytes.Buffer > string add > fmt.Sprintf`

##### 先看 Sprintf



```go
// Sprintf formats according to a format specifier and returns the resulting string.
func Sprintf(format string, a ...interface{}) string {
    p := newPrinter()
    p.doPrintf(format, a)
    s := string(p.buf)
    p.free()
    return s
}
```

这是 fmt.Sprintf 的源码，我们可以看到内部会通过 newPrinter 创建一个新对象 p，点进去看一下 newPrinter 这个函数



```go
// newPrinter allocates a new pp struct or grabs a cached one.
func newPrinter() *pp {
    p := ppFree.Get().(*pp)
    p.panicking = false
    p.erroring = false
    p.fmt.init(&p.buf)
    return p
}
```

它会从系统的临时对象池中那 pp 这个对象，关于临时对象池（sync.Pool)，下次有机会再探讨。这里可以知道， Sprintf 会从临时对象池中获取一个 *pp 的指针，然后再做一些格式化的操作，doPrintf 代码就不贴了，格式化后的底层字节会放到 []byte 这个切片里面，最后再 string 转换成字符串返回，并且释放掉 p 对象。
 整个过程：创建对象 - 格式化操作 - string化 - 释放对象

##### 接下来看 string

string 是在 Go 里面是一个不可变类型，所以下面的代码



```go
str = str + str2
```

每次都会创建一个新的 string 类型的值，然后重新赋值给 str 这个变量，相比于上面的 Sprintf 主要少了格式化这个过程，所以在性能上肯定要优于 Sprintf

##### bytes.Buffer

我们看一下 builder 的 String() 函数源码



```go
// String returns the contents of the unread portion of the buffer
// as a string. If the Buffer is a nil pointer, it returns "<nil>".
//
// To build strings more efficiently, see the strings.Builder type.
func (b *Buffer) String() string {
    if b == nil {
        // Special case, useful in debugging.
        return "<nil>"
    }
    return string(b.buf[b.off:])
}
```

字符串的底层结构是一个 []byte 的字节序列，而 Buffer 是直接获取未读取的 []byte序列，在转成 string 返回，少了重复创建对象这个步骤。
 b.buf 是 []byte 切片
 b.off 是已读取的字节位置

##### strings.Builder



```go
// String returns the accumulated string.
func (b *Builder) String() string {
    return *(*string)(unsafe.Pointer(&b.buf))
}
```

`strings.Builder` 直接通过指针来操作了，在效率上更进一步。

#### 总结

通过源码来分析，还是比较清晰明了的，但是限于我自身的水平，对于源码的解读并不都是特别深入，这里也是给大家做出一个参考。关于最后的通过转换成指针来返回字符串的操作，我也就是知道转成指针效率会高，但是关于为什么，也都是模棱两可（是因为直接通过操作内存地址吗）。总之关于基础性、底层的东西还是要多多学习。



[![  ](https://cdn2.jianshu.io/assets/default_avatar/3-9a2bcc21a5d89e21dafc73b39dc5f582.jpg)]()

