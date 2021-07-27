# 函数签名和函数类型

别误会，我没有打算详细介绍函数。

在实际的开发中，自然会接触函数的用法。在写出优雅强大的函数之前，我们可以先调用标准库或第三方包里别人写好的函数，并从中学习。

要正确使用函数，我们需要查看文档，看懂函数签名和注释，有些还会有例子，就像看说明书。如果想学习实现，则要进一步看源码。

以经常用的 `fmt.Println` 为例。

可以在 [https://pkg.go.dev](https://pkg.go.dev/) 上搜索 `fmt` 包，找到 `Println` 这个函数，内容是这样的：

```
func Println(a ...interface{}) (n int, err error)
```

> Println formats using the default formats for its operands and writes to standard output. Spaces are always added between operands and a newline is appended. It returns the number of bytes written and any write error encountered.

Example Code:

```
package main

import (
	"fmt"
)

func main() {
	const name, age = "Kim", 22
	fmt.Println(name, "is", age, "years old.")

	// It is conventional not to worry about any
	// error returned by Println.

}
Kim is 22 years old.
```

> 注：pkg.go.dev 从 19 年起取代了 godoc.org 成为了 Go 语言的文档网站，上面不仅可以搜索到标准库，所有被缓存了的第三方 module 也都能搜到。（go module 默认会先向 proxy 请求第三方包，proxy 发现尚未缓存就会先获取缓存再返回。换言之，几乎所有公开的有人请求的 module 都可以搜到。）

------

函数签名、注释、例子还有例子的输出，是标准的文档构成。

> 注：文档里的实际上是函数原型（prototype），但要确认的主要是签名信息。

`Println` 不是讨论重点，注释和例子就不展开了。主要介绍一下函数签名

> 函数签名（function signature）定义了函数的输入（参数列表）和输出（返回值列表）。它本质上是函数开发者和调用者之间的契约，包含函数的关键信息：参数的类型、个数和顺序，返回值的类型、个数和顺序。调用者通过它了解调用时要提供什么，以及在调用完成后会得到什么。（当然，按签名调用还是有可能出现逻辑上的错误，开发者需要在注释中进一步说明注意事项。）函数名、参数名、返回值名可以出现在签名里也可以省略，**命名信息对签名来说并不重要** 。

**函数签名:**

```
(参数列表) (返回值列表)

//这就叫做一个函数签名，定义了函数的输入（也就是我们所说的参数列表），函数的输出（也就是我们所说的返回值列表）函数签名包含的是函数的重要信息，参数的类型，参数的个数，参数的顺序，返回值的类型，返回值的个数，返回值的顺序，命名信息对于函数签名来说不重要。

```

最简单的函数签名是这样的：`(参数列表) (返回值列表)`。

**函数类型（type）字面量：**

```
//签名信息前面加上 `func` 关键字就成了函数类型（type）字面量

func (参数列表) (返回值列表)

```

**函数原型（prototype）：**

```
签名信息前面加上 func 关键字就成了函数类型（type）字面量，再加上函数名就成了函数原型（prototype）

func 函数名 （参数列表）（返回值列表） 
```

**完整的函数**

```
再加上函数体 {/*代码实现*/} 就变成完整的函数
func 函数名 （参数列表）（返回值列表） {
	/*代码实现*/
} 

```

虽然函数签名是关键，但**命名能帮助我们区分函数**、**参数和返回值**，还能从**命名中推测用途， 所以很多函数签名其实是带着命名的类型字面量或函数原型的形式。**

> 很多函数签名其实是函数原型的形式

```
// 单纯的签名信息
(int, int) (int, error)
// 函数类型字面量，但不细究的话，也可以叫函数签名
func (int, int) (int, error)
// 函数原型，有时这个也叫函数签名
func Count(int, int) (int, error)
// 完整的函数
func Count(start int, end int) (int, error) {
    // 有引用到的参数需要命名。一般函数没有多余的参数，所以参数都是命名的。
    count := 0
    for i := start; i < end; i++ {
        // ...
        count++
    }
    return count, nil
}
```

Go 里面函数也是一种类型，**签名相同的函数就被认为是同一个类型**。下面的代码是合法的

> 在go语言里因为函数也是一种类型，所以函数签名相同的函数被认为是同一个类型。

```
var f func(a int, b int) (c int) = func(x int, y int) (z int) { return x + y }
var f2 func(int, int) int = f


//以上代码为合法的原因是：
因为函数真正的签名信息是(int, int) int，func 这个关键字，以及参数的命名，a, b, c, x, y, z,都可以省略，有没有命名，命名是否相同，不影响他们是同一个类型。（函数的参数名 x 和 y 在函数体没有引用时也可以省略，例如 func(int, int) int {return 0} 
```

无论是哪一种形式，关注的要点都是参数列表和返回值列表。知道以下几点规则，你就可以读懂函数签名：

- 跟其它 C 家族语言返回值类型在前、没有关键字不同（C 语言：`int myFunc(int a)`），Go 以关键字开头，函数名和参数列表在返回值列表前面。
- （顺序：关键字 - 函数名 - 参数列表 - 返回值列表。）
- 因为允许多返回值，参数和返回值都是列表。其中参数列表外面的括号不能省略，即使参数列表为空；而返回值列表如果为空或者只有一个匿名返回值，可以省略括号。
- （区分参数还是返回值：第一个括号里的是参数，右边剩下的是返回值。Go 没有类似 `void` 的关键字，没有返回值时，返回值部分直接为空。）
- 连续多个相同类型的命名参数或返回值，可以一起声明，`(a, b, c int, s string)` 等价于 `(a int, b int, c int, s string)` 。（要看懂这种写法，但不推荐这样写。这样写在增减参数和调整参数顺序时，容易出错，会把类型张冠李戴。）

#### 可变参数

Go 支持可变参数（variadic arguments）。具体声明形式是，在类型前面加上三个句点 `...`

**表示可以接受 0 到多个该类型的参数。例如 `Println` 的 `(a ...interface{})` 表示可以接受任意个空接口类型的值作为参数。**

> 注：空接口方法列表为空，意味着任意类型都满足空接口，任意类型都可以作为实参传递给函数。

需要注意的是：

- 是在类型前面加`...` 表示可以接受任意个该类型的参数
- 当接口的方法列表为空时表示：任意类型都可以满足该接口，也就是任意类型都可以作为实参传递给函数

```
// 可以没有参数，只输出一个换行符
fmt.Println()
// 可以 3 个 int 型字面量
fmt.Println(1, 2, 3)
// 不同类型混合着来
// 允许不同类型是因为用了空接口类型，数量可以为任意个才是可变参数的关键
fmt.Println("院子里有", 1, "棵枣树，另", 1, "棵也是枣树？", true)


//Println函数的原型如下所示：
func Println(a ...interface{}) (n int, err error)
正是因为...interface{}，表示Println（）1.可以接受任意个参数2.这任意个参数的类型为任意类型。


可变参数的关键是：参数可以为任意个
可变参数的高级用法就是`...interface{}`不但允许任意个参数而且允许任意个参数的类型为任意类型。

```

还需要注意的是：函数最多只能声明一个 **可变参数** ，而且只能是最后一个参数（可变参数放中间，后面的参数就很难对得上号了）。

> 可变参数实际上是一个语法糖，传给可变参数的一系列值被打包成了一个对应类型的切片，供函数内部引用。`Println` 的参数在函数内部相当于 `(a []interface{})` 。不过今天不讨论函数的实现，只讨论调用。这句话简直绝绝子！！！

也就是说：传给可变参数的一系列值，都被打包为一个对应类型的切片。相当于`[]interface{}`

```
举个例子：
funct Add (int a,...int){

}
相当于是：

func  Add （int a, []int ）{

}

```

1.有一个问题：既然可变参数实际上变成了一个切片，那么我正好有一个切片，我可以拿这个切片来作为实参吗？

答：不可以！**可变参数的本质是个数的"可变"**  个数的可变是什么意思呢？就是要求，你传入的实参是一个一个对应的类型的值，一个一个对应类型的值和一个切片这根本不是同一个概念好嘛？



2.那么问题又来了。既然传一个切片不行，那么我只能一个一个的传吗?(slice[0], slice[1],  slice[2],  slice[3], slice[4],)假如切片有100个元素，那么得传到猴年马月？

> 答：这时有另外一个语法糖，在实参后面加上`...` 就会产生类似 Python 解包（unpack）的效果。当然只是像，实际上是告诉函数：这是一个切片，可以直接赋复制给可变参数，并没有解包并打包的操作。

**`...` 的位置很容易搞混：可变参数（形参）的声明放在前面，给实参『解包』放在后面。**

> `...`放在形参前：表面函数有任意个该类型的参数
>
> `...`放在实参后：表面这个实参是个切片。可以把它的元素一个一个复制到函数的可变参数位置里



## 函数



在Go语言中，函数是第一类对象，我们可以将函数保持到变量中。函数主要有具名和匿名之分，包级函数一般都是具名函数，具名函数是匿名函数的一种特例。当然，Go语言中每个类型还可以有自己的方法，方法其实也是函数的一种。

```go
// 具名函数
func Add(a, b int) int {
    return a+b
}

// 匿名函数
var Add = func(a, b int) int {
    return a+b
}
```

Go语言中的函数可以有多个参数和多个返回值，参数和返回值都是以传值的方式和被调用者交换数据。在语法上，函数还支持可变数量的参数，可变数量的参数必须是最后出现的参数，可变数量的参数其实是一个切片类型的参数。

```go
// 多个参数和多个返回值
func Swap(a, b int) (int, int) {
    return b, a
}

// 可变数量的参数
// more 对应 []int 切片类型
func Sum(a int, more ...int) int {
    for _, v := range more {
        a += v
    }
    return a
}
```

当可变参数是一个空接口类型时，调用者是否解包可变参数会导致不同的结果：

```go
func main() {
    var a = []interface{}{123, "abc"}

    Print(a...) // 123 abc
    Print(a)    // [123 abc]
}

func Print(a ...interface{}) {
    fmt.Println(a...)
}
```

> ​	来深刻理解一下可变数量的参数其实是一个切片类型的参数和当可变参数是一个空接口类型时，调用者是否解包可变参数会导致不同的结果这两句话：
>
> 举个例子，现在某个函数有个某个类型可变参数，比如这里的
>
> `func Print(a ...interface{})`函数，你在传实参进去时，你如果是直接传个切片` Print(a)  `结果就是`[123 abc]`
>
> 但是你如果传`Print(a...) 结果就是 123 abc`



> 值得注意的是：当可变参数为普通类型（除了空接口类型），调用函数传实参进去时，不可以直接传切片，必须要在实参后面加上`...`让函数知道，把这个切片的所有的元素都复制到可变参数里面。
>
> 当可变参数为空接口类型时，你在调用函数传实参时，你可以传切片进去，不加`...`，相当于是未解包的参数，等价于直接调用`Print([]interface{}{123, "abc"})`
>
> 第一个`Print`调用时传入的参数是`a...`，等价于直接调用`Print(123, "abc")`

**函数的返回值的命名的意义：**

不仅函数的参数可以有名字，也可以给函数的返回值命名：

```go
func Find(m map[int]int, key int) (value int, ok bool) {
    value, ok = m[key]
    return
}
```

给函数的返回值命名意味着可以通过名字来修改返回值，也可以通过`defer`语句在`return`语句之后修改返回值。

```go
func Inc() (v int) {
    defer func(){ v++ } ()
    return 42
}
```

分析一波：我们通过`defer`语句延迟执行了一个匿名函数，因为这个匿名函数捕获了外部函数的局部变量v，这个函数我们一般叫做闭包。闭包的最大特点是，对于捕获的外部变量并不是传值的方式访问，而是通过引用的方式访问。

> 闭包这种引用方式存在的问题是：
>
> ```
> func main() {
>     for i := 0; i < 3; i++ {
>         defer func(){ println(i) } ()
>     }
> }
> // Output:
> // 3
> // 3
> // 3
> 
> ```
>
> 因为是闭包，在for循环迭代语句中，每个defer语句延迟执行的函数引用的都是同一个迭代变量`i`在循环结束后这个变量的值为3，因次最终输出的都是3



# 先来了解一下defer关键字

> ​	Go 语言的 `defer`关键字是用来在当前函数返回前执行传入的函数，也就是在最后执行传入的函数。这个最后怎么理解？在下文会说到。
>
> ​	应用场景：1.因为文件描述符我们一般都是在最后关闭的，所以我们常常用defer来执行关闭文件描述符的函数 	2.数据库的连接我们往往是最后关闭的，所以defer还可以用来关闭数据库连接的函数。  3.第三种场景就是用来解锁资源

### **使用 `defer` 的最常见场景是在函数调用结束后完成一些收尾工作：**

-  `defer` 中回滚数据库的事务：

  ```
  func createPost(db *gorm.DB) error {
      tx := db.Begin()
      defer tx.Rollback()
      
      if err := tx.Create(&Post{Author: "Draveness"}).Error; err != nil {
          return err
      }
      
      return tx.Commit().Error
  }
  
  //  defer 后面的函数tx.Rollback() 是在x.Commit() 之后再执行的
  这也就是defer关键字用来“在当前函数返回前执行传入的函数，也就是在最后执行传入的函数”这句话的理解
  
  在使用数据库事务时，我们可以使用上面的代码在创建事务后就立刻调用 Rollback 保证事务一定会回滚。哪怕事务真的执行成功了，那么调用 tx.Commit() 之后再执行 tx.Rollback() 也不会影响已经提交的事务。
  ```

  

### defer语句中遇到的问题

我们在 Go 语言中使用 `defer` 时会遇到两个常见问题，这里会介绍具体的场景并分析这两个现象背后的设计原理：

- 第一个问题是:`defer`关键字的调用‘‘时机’’，以及有多个defer关键字，他们的调用顺序是什么样的？
- 第二个问题是：`defer`关键字使用数据的方式？
  - 答：defer使用传值的方式传递参数时，是会先进行预计算的，会导致不符合预期的结果。

**作用域：**

向`defer`关键字传入的函数会在函数返回之前运行。

假设我们现在在for循环中。多次调用`defer`关键字：

```
func main() {
	for i := 0; i < 5; i++ {
		defer fmt.Println(i)
	}
}

$ go run main.go
4
3
2
1
0


如果还不理解，可以这么理解，先调用的`defer`距离main函数最后的大括号越近

所以这段代码等价于：
func main() {
	for i := 0; i < 5; i++ {
		defer fmt.Println(i)
	}
	
	
	fmt.Println(4)
	fmt.Println(3)
	fmt.Println(2)
	fmt.Println(1)
	fmt.Println(0)
}
凡事总有个先来后到，越先调用的离最后的大花括号越近
```

运行上述代码会倒序执行传入 `defer` 关键字的所有表达式，因为最后一次调用 `defer` 时传入了 `fmt.Println(4)`，所以这段代码会优先打印 4。我们可以通过下面这个简单例子强化对 `defer` 执行时机的理解：

```
func main() {
    {
        defer fmt.Println("defer runs")
        fmt.Println("block ends")
    }
    
    fmt.Println("main ends")
}

$ go run main.go
block ends
main ends
defer runs
```

> **从上述代码的输出我们会发现，defer 传入的函数不是在退出代码块的作用域时执行的，它只会在当前函数和方法返回之前被调用。**

**总结**：**defer语句执行的时机是；函数或者方法返回前执行的。不是什么代码块结束时执行的。**

**预计算参数** 

> go语言中所有的函数调用都是传值的，虽然defer是关键字，但是也继承了这个特性。也就是说defer的后面的函数调用是传值的，闭包是引用的。

利用这个特性我们可以用来计算main函数的运行时间。

```
func main() {
	startedAt := time.Now()
	defer fmt.Println(time.Since(startedAt))
	
	time.Sleep(time.Second)
}

$ go run main.go
0s
```

然而上述代码的运行结果并不符合我们的预期，这个现象背后的原因是什么呢？经过分析，我们会发现调用 `defer` 关键字会立刻拷贝函数中引用的外部参数，所以 `time.Since(startedAt)` 的结果不是在 `main` 函数退出之前计算的，而是在 `defer` 关键字调用时计算的，最终导致上述代码输出 0s。

**值得注意的是：1.`defer`关键字后面的函数也好，还是其他什么也罢，只要用到了外部的参数，都是把外部参数的值立即拷贝！！**

**立即拷贝外部参数的值有什么影响呢？这么说吧.....	**`defer`**后面的语句是在函数返回前执行，但是defer后面的语句有这么几个类型：1.纯语句型，（什么叫纯语句型呢？个人的理解就是这个语句是单纯的不是嵌套的）2.嵌套语句型	举个例子：**

```
defer fmt.Println(a)//这个a很单纯，就是一个数
但是！！有些语句就是不单纯的比如

defer fmt.Println(time.Since(startedAt))
//这个语句为什么不单纯呢？因为语句里面嵌套了语句，又因为`defer`后面的语句是在函数返回前执行，这里的“后面的语句”指的是`defer`后面最外层的语句！！！

导致的结果是：fmt.Println（）这个函数是在defer语句真正调用时执行，但是time.Since(startedAt)一旦函数获取到拷贝的形参值，他就执行。所以，在defer语句真正调用时 fmt.Println()里面的值早就是0了。
```



想要解决这个问题的方法非常简单，我们只需要向 `defer` 关键字传入匿名函数：

```
func main() {
	startedAt := time.Now()
	defer func() { fmt.Println(time.Since(startedAt)) }()
	
	time.Sleep(time.Second)
}

$ go run main.go
1s

```

**解决的思路是：延迟内部嵌套函数**`time.Since(startedAt)`的调用，具体怎么延迟？

**defer function分析：**先来分析一波

答案是：无论是go关键字还是defer关键字，在代码执行到他们时，编译器都要为它们后面的函数准备好函数调用的参数堆栈，要确定的参数值和参数的类型大小，这样一来就得去求值——求谁的值，对它们后面函数的参数进行求值。

```
//举个例子：
defer fmt.Println("result is",func() int { return i * 2 }())

//执行的步骤：
1.因为defer后面是fmt.Println这个函数，先对这个函数的参数进行求值，
fmt.Println有两个参数：第一个是"result is"这个字符串，第二个参数是
func() int { return i * 2 }()这个函数，第一个参数是字符串常量值，所以不用求值，第二个参数是函数，是个函数调用，对这个函数进行求值，求值时，i为1，所以Println的第二个参数的求值结果为2，于是上面的defer语句的使用就等价于：

defer fmt.Println("result is",2)
因此无论最终的i的值变成了多少，defer最终输出的都是result is 2
go关键字 后面的参数也是如此。之后在进入函数体，只不过是将函数执行的过程推迟到了调用方法退出之前了。


吐血总结！！！
	defer关键字和go关键字的执行原理都是一样的：关键字后面紧跟的最外层函数的执行是在调用方法退出之前，但是编译器会对这个函数的参数进行计算，（因为要先为关键字后面紧跟的最外层函数准备好函数调用的堆栈，所以要先知道函数参数的类型与大小）
```

为了解决这个问题，我们可以使用闭包函数：

```
//下面两段代码的结果都是一样的
package main

import "fmt"

func main() {
        var i int = 1

        defer func() {
                fmt.Println("result is", func() int { return i * 2 }())
        }()
        i++
}

Output：
result is 4
//
package main

import "fmt"

func main() {
	var i int = 1

	defer func() {
		fmt.Println("result is", i*2)
	}()
	i++
}

Output：
result is 4

```

> 这里我们看到，defer后面是一个不带任何参数的匿名函数，不带参数。自然对参数求值也是无值可求，等到main函数退出前，开始执行，此时i为2，所以最后结果是：result is 4



```
package main

import(
	"fmt"
	"time"
)
func main() {
	startedAt := time.Now()
	defer fmt.Println(time.Since(startedAt))
	
	time.Sleep(time.Second)
}
修改前！！
输出：0s
———————————————————————————————————————————————————————————
修改后！！
package main

import(
	"fmt"
	"time"
)
func main(){
	startedAt := time.Now()
	defer func(){
		fmt.Println(time.Since(startedAt))
}()
	
	time.Sleep(time.Second)
}

输出：1.000144091s
验证成功！

```



### defer method分析

> defer后面除了可以跟着普通函数调用外，还可以使用方法调用(method)

```
defer instance.Method(x,y)
```

> 对于方法调用的分析思路：
>
> 先把将method转换为function后，再分析。下面是举例：

```
package main

import (
    "fmt"
)

type field struct {
    name string
}

func (p *field) print() {
    fmt.Println(p.name)
}

func main() {
    data1 := []*field{{"one"}, {"two"}, {"three"}}
    for _, v := range data1 {
        defer v.print()
    }

    data2 := []field{{"four"}, {"five"}, {"six"}}
    for _, v := range data2 {
        defer v.print()
    }
}

输出：
six
six
six
three
two
one
转化前
————————————————————————————————————
转化后：
package main

import (
    "fmt"
)

type field struct {
    name string
}

func  print (p *field){
    fmt.Println(p.name)
}
func main() {
    data1 := []*field{{"one"}, {"two"}, {"three"}}
    for _, v := range data1 {
        defer print（v）
    }

    data2 := []field{{"four"}, {"five"}, {"six"}}
    for _, v := range data2 {
        defer print(&v)
    }
}

```

我们就利用defer的“参数实时求值”原理，对上面的代码作分析：

data1的三次迭代：defer的参数求值完后，defer print(v)在函数退出前的调用分别变成了：

- defer print(&field{“Three”})
- defer print(&field{“two”})
- defer print(&field{“one”})

data2的三次迭代，defer的参数求值完后，defer print(v)调用分别变成了：

- defer print(&v)
- defer print(&v)
- defer print(&v)

于是在main退出前，defer函数按defer被调用的顺序为：

```
print(&v)
print(&v)
print(&v)
print(&field{“three”})
print(&field{“two”})
print(&field{“one”})
```

而此刻：v中存储的值为field{“six”}，于是前三次print均输出”six”。

> 还有值得注意的是：在go语言中中的method（方法）本质上就是以method的receiver作为第一个参数的普通function（函数），即
>
> `instance.method(x,y)` <=> `function(instance, x,y)`

`instance.methond(x,y)=function(instance,x,y)`

```
type field struct {
    name string
}

func (p *field) print() {
    fmt.Println(p.name)
}

p.print()
//

type field struct {
    name string
}
func  print(p *field) {
    fmt.Println(p.name)
}

```

> 还有值得注意的一点是：因为data1中元素的类型是field指针，所以v被赋值之后就是不同元素的地址，每次调用print时，传入的参数v就是各个元素的地址。
>
> 但是data2因为data2中的元素类型是field（非指针类型），所以赋值后的v是元素的copy，每次传入的&v实际上是v的地址，

那就是v在整个for range过程只有一个，data2迭代完成之后，**v是元素”six”的copy**。

用go关键字 还是一样：

```
package main

import (
    "fmt"
    "time"
)

type field struct {
    name string
}

func (p *field) print() {
    fmt.Println(p.name)
}

func main() {
    data1 := []*field{{"one"}, {"two"}, {"three"}}
    for _, v := range data{
    	go v.print()
    
    }
    data2 := []field{{"four"}, {"five"}, {"six"}}
    for _, v := range data2 {
        go v.print()
    }
    time.Sleep(3 * time.Second)
}
    
    
    
输出：
one
two
three
six
six
six
    
```

现在修改一下源程序，让它能够输出

(“one”、”two”、”three”, “four”, “five”, “six”)

我们来改一下：只需将field method的receiver type由*field改为field即可。



```
package main

import (
    "fmt"
    "time"
)

type field struct {
    name string
}

func (p field) print() {
    fmt.Println(p.name)
}

func main() {

    data1 := []*field{{"one"}, {"two"}, {"three"}}
    for _, v := range data1 {
        go v.print()
    }

    data2 := []field{{"four"}, {"five"}, {"six"}}
    for _, v := range data2 {
        go v.print()
    }

    time.Sleep(3 * time.Second)
}


输出：
one
two
three
four
five
six

```



## defer的数据结构 

在介绍 `defer` 函数的执行过程与实现原理之前，我们首先来了解一下 `defer` 关键字在 Go 语言源代码中对应的数据结构

```
type _defer struct {
	siz       int32
	started   bool
	openDefer bool
	sp        uintptr
	pc        uintptr
	fn        *funcval
	_panic    *_panic
	link      *_defer
}
```

[`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体是延迟调用链表上的一个元素，所有的结构体都会通过 `link` 字段串联成链表。

![golang-defer-link](https://img.draveness.me/2020-01-19-15794017184603-golang-defer-link.png)



我们简单介绍一下 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体中的几个字段：

- `siz` 是参数和结果的内存大小；
- `sp` 和 `pc` 分别代表栈指针和调用方的程序计数器；
- `fn` 是 `defer` 关键字中传入的函数；
- `_panic` 是触发延迟调用的结构体，可能为空；
- `openDefer` 表示当前 `defer` 是否经过开放编码的优化；

除了上述的这些字段之外，[`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 中还包含一些垃圾回收机制使用的字段，这里为了减少理解的成本就都省去了

## 执行机制 

中间代码生成阶段的 [`cmd/compile/internal/gc.state.stmt`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.stmt) 会负责处理程序中的 `defer`，该函数会根据条件的不同，使用三种不同的机制处理该关键字：

```
func (s *state) stmt(n *Node) {
	...
	switch n.Op {
	case ODEFER:
		if s.hasOpenDefers {
			s.openDeferRecord(n.Left) // 开放编码
		} else {
			d := callDefer // 堆分配
			if n.Esc == EscNever {
				d = callDeferStack // 栈分配
			}
			s.callResult(n.Left, d)
		}
	}
}
```

堆分配、栈分配和开放编码是处理 `defer` 关键字的三种方法，早期的 Go 语言会在堆上分配 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体，不过该实现的性能较差，Go 语言在 1.13 中引入栈上分配的结构体，减少了 30% 的额外开销[1](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-defer/#fn:1)，并在 1.14 中引入了基于开放编码的 `defer`，使得该关键字的额外开销可以忽略不计[2](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-defer/#fn:2)，我们在一节中会分别介绍三种不同类型 `defer` 的设计与实现原理。



## 堆上分配

根据 [`cmd/compile/internal/gc.state.stmt`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.stmt) 方法对 `defer` 的处理我们可以看出，堆上分配的 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体是默认的兜底方案，当该方案被启用时，编译器会调用 [`cmd/compile/internal/gc.state.callResult`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.callResult) 和 [`cmd/compile/internal/gc.state.call`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.call)，这表示 `defer` 在编译器看来也是函数调用。

[`cmd/compile/internal/gc.state.call`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.call) 会负责为所有函数和方法调用生成中间代码，它的工作包括以下内容：

1. 获取需要执行的函数名、闭包指针、代码指针和函数调用的接收方；

2. 获取栈地址并将函数或者方法的参数写入栈中；

3. 使用 [`cmd/compile/internal/gc.state.newValue1A`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.newValue1A) 以及相关函数生成函数调用的中间代码；

4. 如果当前调用的函数是 `defer`，那么会单独生成相关的结束代码块；

5. 获取函数的返回值地址并结束当前调用；

   

   ```
   func (s *state) call(n *Node, k callKind, returnResultAddr bool) *ssa.Value {
   	...
   	var call *ssa.Value
   	if k == callDeferStack {
   		// 在栈上初始化 defer 结构体
   		...
   	} else {
   		...
   		switch {
   		case k == callDefer:
   			aux := ssa.StaticAuxCall(deferproc, ACArgs, ACResults)
   			call = s.newValue1A(ssa.OpStaticCall, types.TypeMem, aux, s.mem())
   		...
   		}
   		call.AuxInt = stksize
   	}
   	s.vars[&memVar] = call
   	...
   }
   ```



从上述代码中我们能看到，`defer` 关键字在运行期间会调用 [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc)，这个函数接收了参数的大小和闭包所在的地址两个参数。

编译器不仅将 `defer` 关键字都转换成 [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 函数，它还会通过以下三个步骤为所有调用 `defer` 的函数末尾插入 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 的函数调用：

1. [`cmd/compile/internal/gc.walkstmt`](https://draveness.me/golang/tree/cmd/compile/internal/gc.walkstmt) 在遇到 `ODEFER` 节点时会执行 `Curfn.Func.SetHasDefer(true)` 设置当前函数的 `hasdefer` 属性；
2. [`cmd/compile/internal/gc.buildssa`](https://draveness.me/golang/tree/cmd/compile/internal/gc.buildssa) 会执行 `s.hasdefer = fn.Func.HasDefer()` 更新 `state` 的 `hasdefer`；
3. [`cmd/compile/internal/gc.state.exit`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.exit) 会根据 `state` 的 `hasdefer` 在函数返回之前插入 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 的函数调用



```
func (s *state) exit() *ssa.Block {
	if s.hasdefer {
		...
		s.rtcall(Deferreturn, true, nil)
	}
	...
}
```



当运行时将 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 分配到堆上时，Go 语言的编译器不仅将 `defer` 转换成了 [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc)，还在所有调用 `defer` 的函数结尾插入了 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn)。上述两个运行时函数是 `defer` 关键字运行时机制的入口，它们分别承担了不同的工作：

- [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 负责创建新的延迟调用；
- [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 负责在函数调用结束时执行所有的延迟调用；

我们以上述两个函数为入口介绍 `defer` 关键字在运行时的执行过程与工作原理。

### 创建延迟调用 

[`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 会为 `defer` 创建一个新的 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体、设置它的函数指针 `fn`、程序计数器 `pc` 和栈指针 `sp` 并将相关的参数拷贝到相邻的内存空间中：

```
func deferproc(siz int32, fn *funcval) {
	sp := getcallersp()
	argp := uintptr(unsafe.Pointer(&fn)) + unsafe.Sizeof(fn)
	callerpc := getcallerpc()

	d := newdefer(siz)
	if d._panic != nil {
		throw("deferproc: d.panic != nil after newdefer")
	}
	d.fn = fn
	d.pc = callerpc
	d.sp = sp
	switch siz {
	case 0:
	case sys.PtrSize:
		*(*uintptr)(deferArgs(d)) = *(*uintptr)(unsafe.Pointer(argp))
	default:
		memmove(deferArgs(d), unsafe.Pointer(argp), uintptr(siz))
	}

	return0()
}
```



最后调用的 [`runtime.return0`](https://draveness.me/golang/tree/runtime.return0) 是唯一一个不会触发延迟调用的函数，它可以避免递归 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 的递归调用。

[`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 中 [`runtime.newdefer`](https://draveness.me/golang/tree/runtime.newdefer) 的作用是想尽办法获得 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体，这里包含三种路径：

1. 从调度器的延迟调用缓存池 `sched.deferpool` 中取出结构体并将该结构体追加到当前 Goroutine 的缓存池中；
2. 从 Goroutine 的延迟调用缓存池 `pp.deferpool` 中取出结构体；
3. 通过 [`runtime.mallocgc`](https://draveness.me/golang/tree/runtime.mallocgc) 在堆上创建一个新的结构体；

```
func newdefer(siz int32) *_defer {
	var d *_defer
	sc := deferclass(uintptr(siz))
	gp := getg()
	if sc < uintptr(len(p{}.deferpool)) {
		pp := gp.m.p.ptr()
		if len(pp.deferpool[sc]) == 0 && sched.deferpool[sc] != nil {
			for len(pp.deferpool[sc]) < cap(pp.deferpool[sc])/2 && sched.deferpool[sc] != nil {
				d := sched.deferpool[sc]
				sched.deferpool[sc] = d.link
				pp.deferpool[sc] = append(pp.deferpool[sc], d)
			}
		}
		if n := len(pp.deferpool[sc]); n > 0 {
			d = pp.deferpool[sc][n-1]
			pp.deferpool[sc][n-1] = nil
			pp.deferpool[sc] = pp.deferpool[sc][:n-1]
		}
	}
	if d == nil {
		total := roundupsize(totaldefersize(uintptr(siz)))
		d = (*_defer)(mallocgc(total, deferType, true))
	}
	d.siz = siz
	d.link = gp._defer
	gp._defer = d
	return d
}
```



无论使用哪种方式，只要获取到 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体，它都会被追加到所在 Goroutine `_defer` 链表的最前面。

![golang-new-defer](https://img.draveness.me/2020-01-19-15794017184614-golang-new-defer.png)



\

`defer` 关键字的插入顺序是从后向前的，而 `defer` 关键字执行是从前向后的，这也是为什么后调用的 `defer` 会优先执行。



### 执行延迟调用

```
func deferreturn(arg0 uintptr) {
	gp := getg()
	d := gp._defer
	if d == nil {
		return
	}
	sp := getcallersp()
	...

	switch d.siz {
	case 0:
	case sys.PtrSize:
		*(*uintptr)(unsafe.Pointer(&arg0)) = *(*uintptr)(deferArgs(d))
	default:
		memmove(unsafe.Pointer(&arg0), deferArgs(d), uintptr(d.siz))
	}
	fn := d.fn
	gp._defer = d.link
	freedefer(d)
	jmpdefer(fn, uintptr(unsafe.Pointer(&arg0)))
}
```



[`runtime.jmpdefer`](https://draveness.me/golang/tree/runtime.jmpdefer) 是一个用汇编语言实现的运行时函数，它的主要工作是跳转到 `defer` 所在的代码段并在执行结束之后跳转回 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn)。



```
TEXT runtime·jmpdefer(SB), NOSPLIT, $0-8
	MOVL	fv+0(FP), DX	// fn
	MOVL	argp+4(FP), BX	// caller sp
	LEAL	-4(BX), SP	// caller sp after CALL
#ifdef GOBUILDMODE_shared
	SUBL	$16, (SP)	// return to CALL again
#else
	SUBL	$5, (SP)	// return to CALL again
#endif
	MOVL	0(DX), BX
	JMP	BX	// but first run the deferred function
```



[`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 会多次判断当前 Goroutine 的 `_defer` 链表中是否有未执行的结构体，该函数只有在所有延迟函数都执行后才会返回。



##  栈上分配 

在默认情况下，我们可以看到 Go 语言中 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体都会在堆上分配，如果我们能够将部分结构体分配到栈上就可以节约内存分配带来的额外开销。

Go 语言团队在 1.13 中对 `defer` 关键字进行了优化，当该关键字在函数体中最多执行一次时，编译期间的 [`cmd/compile/internal/gc.state.call`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.call) 会将结构体分配到栈上并调用 [`runtime.deferprocStack`](https://draveness.me/golang/tree/runtime.deferprocStack)：

```
func (s *state) call(n *Node, k callKind) *ssa.Value {
	...
	var call *ssa.Value
	if k == callDeferStack {
		// 在栈上创建 _defer 结构体
		t := deferstruct(stksize)
		...

		ACArgs = append(ACArgs, ssa.Param{Type: types.Types[TUINTPTR], Offset: int32(Ctxt.FixedFrameSize())})
		aux := ssa.StaticAuxCall(deferprocStack, ACArgs, ACResults) // 调用 deferprocStack
		arg0 := s.constOffPtrSP(types.Types[TUINTPTR], Ctxt.FixedFrameSize())
		s.store(types.Types[TUINTPTR], arg0, addr)
		call = s.newValue1A(ssa.OpStaticCall, types.TypeMem, aux, s.mem())
		call.AuxInt = stksize
	} else {
		...
	}
	s.vars[&memVar] = call
	...
}
```



因为在编译期间我们已经创建了 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体，所以在运行期间 [`runtime.deferprocStack`](https://draveness.me/golang/tree/runtime.deferprocStack) 只需要设置一些未在编译期间初始化的字段，就可以将栈上的 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 追加到函数的链表上：

```
func deferprocStack(d *_defer) {
	gp := getg()
	d.started = false
	d.heap = false // 栈上分配的 _defer
	d.openDefer = false
	d.sp = getcallersp()
	d.pc = getcallerpc()
	d.framepc = 0
	d.varp = 0
	*(*uintptr)(unsafe.Pointer(&d._panic)) = 0
	*(*uintptr)(unsafe.Pointer(&d.fd)) = 0
	*(*uintptr)(unsafe.Pointer(&d.link)) = uintptr(unsafe.Pointer(gp._defer))
	*(*uintptr)(unsafe.Pointer(&gp._defer)) = uintptr(unsafe.Pointer(d))

	return0()
}
```



除了分配位置的不同，栈上分配和堆上分配的 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 并没有本质的不同，而该方法可以适用于绝大多数的场景，与堆上分配的 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 相比，该方法可以将 `defer` 关键字的额外开销降低 ~30%。



## 开放编码 

Go 语言在 1.14 中通过开放编码（Open Coded）实现 `defer` 关键字，该设计使用代码内联优化 `defer` 关键的额外开销并引入函数数据 `funcdata` 管理 `panic` 的调用[3](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-defer/#fn:3)，该优化可以将 `defer` 的调用开销从 1.13 版本的 ~35ns 降低至 ~6ns 左右：

```go
With normal (stack-allocated) defers only:         35.4  ns/op
With open-coded defers:                             5.6  ns/op
Cost of function call alone (remove defer keyword): 4.4  ns/op
```

Go

然而开放编码作为一种优化 `defer` 关键字的方法，它不是在所有的场景下都会开启的，开放编码只会在满足以下的条件时启用：

1. 函数的 `defer` 数量少于或者等于 8 个；
2. 函数的 `defer` 关键字不能在循环中执行；
3. 函数的 `return` 语句与 `defer` 语句的乘积小于或者等于 15 个；





### 启用优化

Go 语言会在编译期间就确定是否启用开放编码，在编译器生成中间代码之前，我们会使用 [`cmd/compile/internal/gc.walkstmt`](https://draveness.me/golang/tree/cmd/compile/internal/gc.walkstmt) 修改已经生成的抽象语法树，设置函数体上的 `OpenCodedDeferDisallowed` 属性：

```go
const maxOpenDefers = 8

func walkstmt(n *Node) *Node {
	switch n.Op {
	case ODEFER:
		Curfn.Func.SetHasDefer(true)
		Curfn.Func.numDefers++
		if Curfn.Func.numDefers > maxOpenDefers {
			Curfn.Func.SetOpenCodedDeferDisallowed(true)
		}
		if n.Esc != EscNever {
			Curfn.Func.SetOpenCodedDeferDisallowed(true)
		}
		fallthrough
	...
	}
}
```

Go

就像我们上面提到的，如果函数中 `defer` 关键字的数量多于 8 个或者 `defer` 关键字处于 `for` 循环中，那么我们在这里都会禁用开放编码优化，使用上两节提到的方法处理 `defer`。

在 SSA 中间代码生成阶段的 [`cmd/compile/internal/gc.buildssa`](https://draveness.me/golang/tree/cmd/compile/internal/gc.buildssa) 中，我们也能够看到启用开放编码优化的其他条件，也就是返回语句的数量与 `defer` 数量的乘积需要小于 15：

```go
func buildssa(fn *Node, worker int) *ssa.Func {
	...
	s.hasOpenDefers = s.hasdefer && !s.curfn.Func.OpenCodedDeferDisallowed()
	...
	if s.hasOpenDefers &&
		s.curfn.Func.numReturns*s.curfn.Func.numDefers > 15 {
		s.hasOpenDefers = false
	}
	...
}
```

Go

中间代码生成的这两个步骤会决定当前函数是否应该使用开放编码优化 `defer` 关键字，一旦确定使用开放编码，就会在编译期间初始化延迟比特和延迟记录。

### 延迟记录 

延迟比特和延迟记录是使用开放编码实现 `defer` 的两个最重要结构，一旦决定使用开放编码，[`cmd/compile/internal/gc.buildssa`](https://draveness.me/golang/tree/cmd/compile/internal/gc.buildssa) 会在编译期间在栈上初始化大小为 8 个比特的 `deferBits` 变量：

```go
func buildssa(fn *Node, worker int) *ssa.Func {
	...
	if s.hasOpenDefers {
		deferBitsTemp := tempAt(src.NoXPos, s.curfn, types.Types[TUINT8]) // 初始化延迟比特
		s.deferBitsTemp = deferBitsTemp
		startDeferBits := s.entryNewValue0(ssa.OpConst8, types.Types[TUINT8])
		s.vars[&deferBitsVar] = startDeferBits
		s.deferBitsAddr = s.addr(deferBitsTemp)
		s.store(types.Types[TUINT8], s.deferBitsAddr, startDeferBits)
		s.vars[&memVar] = s.newValue1Apos(ssa.OpVarLive, types.TypeMem, deferBitsTemp, s.mem(), false)
	}
}
```

Go

延迟比特中的每一个比特位都表示该位对应的 `defer` 关键字是否需要被执行，如下图所示，其中 8 个比特的倒数第二个比特在函数返回前被设置成了 1，那么该比特位对应的函数会在函数返回前执行：

![golang-defer-bits](https://img.draveness.me/2020-10-31-16041438704362/golang-defer-bits.png)

**图 5-12 延迟比特**

因为不是函数中所有的 `defer` 语句都会在函数返回前执行，如下所示的代码只会在 `if` 语句的条件为真时，其中的 `defer` 语句才会在结尾被执行[4](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-defer/#fn:4)：

```go
deferBits := 0 // 初始化 deferBits

_f1, _a1 := f1, a1  // 保存函数以及参数
deferBits |= 1 << 0 // 将 deferBits 最后一位置位 1

if condition {
    _f2, _a2 := f2, a2  // 保存函数以及参数
    deferBits |= 1 << 1 // 将 deferBits 倒数第二位置位 1
}
exit:

if deferBits & 1 << 1 != 0 {
    deferBits &^= 1 << 1
    _f2(a2)
}

if deferBits & 1 << 0 != 0 {
    deferBits &^= 1 << 0
    _f1(a1)
}
```

Go

延迟比特的作用就是标记哪些 `defer` 关键字在函数中被执行，这样在函数返回时可以根据对应 `deferBits` 的内容确定执行的函数，而正是因为 `deferBits` 的大小仅为 8 比特，所以该优化的启用条件为函数中的 `defer` 关键字少于 8 个。

上述伪代码展示了开放编码的实现原理，但是仍然缺少了一些细节，例如：传入 `defer` 关键字的函数和参数都会存储在如下所示的 [`cmd/compile/internal/gc.openDeferInfo`](https://draveness.me/golang/tree/cmd/compile/internal/gc.openDeferInfo) 结构体中：

```go
type openDeferInfo struct {
	n           *Node
	closure     *ssa.Value
	closureNode *Node
	rcvr        *ssa.Value
	rcvrNode    *Node
	argVals     []*ssa.Value
	argNodes    []*Node
}
```

Go

当编译器在调用 [`cmd/compile/internal/gc.buildssa`](https://draveness.me/golang/tree/cmd/compile/internal/gc.buildssa) 构建中间代码时会通过 [`cmd/compile/internal/gc.state.openDeferRecord`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.openDeferRecord) 方法在栈上构建结构体，该结构体的 `closure` 中存储着调用的函数，`rcvr` 中存储着方法的接收者，而最后的 `argVals` 中存储了函数的参数。

很多 `defer` 语句都可以在编译期间判断是否被执行，如果函数中的 `defer` 语句都会在编译期间确定，中间代码生成阶段就会直接调用 [`cmd/compile/internal/gc.state.openDeferExit`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.openDeferExit) 在函数返回前生成判断 `deferBits` 的代码，也就是上述伪代码中的后半部分。

不过当程序遇到运行时才能判断的条件语句时，我们仍然需要由运行时的 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 决定是否执行 `defer` 关键字：

```go
func deferreturn(arg0 uintptr) {
	gp := getg()
	d := gp._defer
	sp := getcallersp()
	if d.openDefer {
		runOpenDeferFrame(gp, d)
		gp._defer = d.link
		freedefer(d)
		return
	}
	...
}
```

Go

该函数为开放编码做了特殊的优化，运行时会调用 [`runtime.runOpenDeferFrame`](https://draveness.me/golang/tree/runtime.runOpenDeferFrame) 执行活跃的开放编码延迟函数，该函数会执行以下的工作：

1. 从 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体中读取 `deferBits`、函数 `defer` 数量等信息；
2. 在循环中依次读取函数的地址和参数信息并通过 `deferBits` 判断该函数是否需要被执行；
3. 调用 [`runtime.reflectcallSave`](https://draveness.me/golang/tree/runtime.reflectcallSave) 调用需要执行的 `defer` 函数；

```go
func runOpenDeferFrame(gp *g, d *_defer) bool {
	fd := d.fd

	...
	deferBitsOffset, fd := readvarintUnsafe(fd)
	nDefers, fd := readvarintUnsafe(fd)
	deferBits := *(*uint8)(unsafe.Pointer(d.varp - uintptr(deferBitsOffset)))

	for i := int(nDefers) - 1; i >= 0; i-- {
		var argWidth, closureOffset, nArgs uint32 // 读取函数的地址和参数信息
		argWidth, fd = readvarintUnsafe(fd)
		closureOffset, fd = readvarintUnsafe(fd)
		nArgs, fd = readvarintUnsafe(fd)
		if deferBits&(1<<i) == 0 {
			...
			continue
		}
		closure := *(**funcval)(unsafe.Pointer(d.varp - uintptr(closureOffset)))
		d.fn = closure

		...

		deferBits = deferBits &^ (1 << i)
		*(*uint8)(unsafe.Pointer(d.varp - uintptr(deferBitsOffset))) = deferBits
		p := d._panic
		reflectcallSave(p, unsafe.Pointer(closure), deferArgs, argWidth)
		if p != nil && p.aborted {
			break
		}
		d.fn = nil
		memclrNoHeapPointers(deferArgs, uintptr(argWidth))
		...
	}
	return done
}
```

Go

Go 语言的编译器为了支持开放编码在中间代码生成阶段做出了很多修改，我们在这里虽然省略了很多细节，但是也可以很好地展示 `defer` 关键字的实现原理。



## 小结 

`defer` 关键字的实现主要依靠编译器和运行时的协作，我们总结一下本节提到的三种机制：

- 堆上分配 · 
  - 编译期将 `defer` 关键字转换成 [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 并在调用 `defer` 关键字的函数返回之前插入 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn)；
  - 运行时调用 [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 会将一个新的 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构体追加到当前 Goroutine 的链表头；
  - 运行时调用 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 会从 Goroutine 的链表中取出 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 结构并依次执行；
- 栈上分配 · 
  - 当该关键字在函数体中最多执行一次时，编译期间的 [`cmd/compile/internal/gc.state.call`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.call) 会将结构体分配到栈上并调用 [`runtime.deferprocStack`](https://draveness.me/golang/tree/runtime.deferprocStack)；
- 开放编码 ·
  - 编译期间判断 `defer` 关键字、`return` 语句的个数确定是否开启开放编码优化；
  - 通过 `deferBits` 和 [`cmd/compile/internal/gc.openDeferInfo`](https://draveness.me/golang/tree/cmd/compile/internal/gc.openDeferInfo) 存储 `defer` 关键字的相关信息；
  - 如果 `defer` 关键字的执行可以在编译期间确定，会在函数返回前直接插入相应的代码，否则会由运行时的 [`runtime.deferreturn`](https://draveness.me/golang/tree/runtime.deferreturn) 处理；

我们在本节前面提到的两个现象在这里也可以解释清楚了：

- 后调用的defer函数会先执行：
  - 后调用的 `defer` 函数会被追加到 Goroutine `_defer` 链表的最前面；
  - 运行 [`runtime._defer`](https://draveness.me/golang/tree/runtime._defer) 时是从前到后依次执行；
- 函数的参数会被预先计算；
  - 调用 [`runtime.deferproc`](https://draveness.me/golang/tree/runtime.deferproc) 函数创建新的延迟调用时就会立刻拷贝函数的参数，函数的参数不会等到真正执行时计算；









****

#### **Method Type、Method Expression、Method Value**

```
1.
type T struct {
    a int
}

func (t T) Get() int       { return t.a }
func (t *T) Set(a int) int { t.a = a; return t.a }


//等价于
2.
type T struct {
    a int
}
func Get(t T) int       { return t.a }
func Set(t *T, a int) int { t.a = a; return t.a }


//使用方法：
方法的两种使用方式：一种是通过被声明的实例比如 var t T,这里的t就是实例。另外一种是通过类型var  t  T,这里的T就是类型

注意:通过实例使用时，按照方法定义的来
通过类型使用时，通过函数来，意味着要把方法与函数进行转化，而且函数需要的参数你必须要传进去。
第一种的使用方法：
var t T
t.Get()
t.Set(1)
除了这种使用方法，还可以是这种：
var t T
T.Get(t)
(*T).Set(&t,1)
通过以类型名调用方法的方式叫做Method Expression，类型T只能调用T的
Method Set中的方法；*T只能调用*T的Method Set中的方法！！这个和实例就截然不同。因此T.Get是合法的。但T.Set则不合法：T.Set(2) //invalid method expression T.Set (needs pointer receiver: (*T).Set)

值得注意的是，通过Method express来使用函数时，因为Method express自身类型就是一个普通function，可以作为右值赋值给函数类型的变量。
	f1 :=(*T).Set  函数类型：func (t *T, int)int
	f2 :=T.Get	   函数类型：func (t *T, int)int
	f1(&t, 3)
	fmt.Println(f2(t))




第二种的使用方法：
var t T
Get(t T)
Set(t *T, a int)




```

**Method Value**

Go中还定义了一种与Method有关的语法：如果一个表达式t具有静态类型T，M是T的Method Set中的一个方法，那么t.M即为**Method Value**。注意这里是t.M而不是T.M

```
	f3 := (&t).Set  //函数类型：func(int)int
    f3(4)
    f4 := t.Get//函数类型：func()int   
    fmt.Println(f4())

可以看出，Method value与Method Expression不同之处在于，Method value绑定了T对象实例，它的函数原型并不包含Method Expression函数原型中的第一个参数。完整例子参见：details-in-go/4/methodexpressionandmethodvalue.go。


```





# **range expression副本参与iteration**

```
package main

import (
	"fmt"
)

func arrayRangeExpression() {
	var a = [5]int{1, 2, 3, 4, 5}
	var r [5]int

	fmt.Println("a = ", a)

	for i, v := range a {
		if i == 0 {
			a[1] = 12
			a[2] = 13
		}
		r[i] = v
	}

	fmt.Println("r = ", r)
	fmt.Println("a = ", a)

}
func main() {
	arrayRangeExpression()
}

实际输出：
a =  [1 2 3 4 5]
r =  [1 2 3 4 5]
a =  [1 12 13 4 5]
期望输出：
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]




伪代码如下：
package main

import (
	"fmt"
)

func arrayRangeExpression() {
	var a = [5]int{1, 2, 3, 4, 5}
	var r [5]int

	fmt.Println("a = ", a)

	for i, v := range a' {//a' is copy from a
		if i == 0 {
			a[1] = 12
			a[2] = 13
		}
		r[i] = v
	}

	fmt.Println("r = ", r)
	fmt.Println("a = ", a)

}
func main() {
	arrayRangeExpression()
}



这是什么原因呢？

答：这就是所谓的range expression副本参与iteration，什么意思呢？就是在上面的例子中。真正参与循环的是数组a的副本,range后面跟的是--数组a的副本。循环里面却是修改真正的数组。
所以导致的结果是：v每次循环取到的都是副本中的元素，但是实际的数组已经被修改了。


Go中的数组在内部表示为连续的字节序列，虽然长度是Go数组类型的一部分，但长度并不包含的数组的内部表示中,数组的长度而是由编译器在编译期计算出来，这个例子中，对range表达式的拷贝，即对一个数组的拷贝，注意这里的a'是Go临时分配的连续的字节序列，与a完全不是同一块内存，无论a被如何修改，参与循环的是a'.并且a'保持不变 。
type Array struct {
	Elem  *Type //元素类型 element type
	Bound int64 //元素的个数 number of elements; <0 if unknown yet
}




在来看个例子：

package main

import (
	"fmt"
)
func pointerToArrayRangeExpression() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    fmt.Println("pointerToArrayRangeExpression result:")
    fmt.Println("a = ", a)

    for i, v := range &a {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }

        r[i] = v
    }

    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
    fmt.Println("")
}

func main() {
	pointerToArrayRangeExpression()
}

实际输出：
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]
期望输出：
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]


我们看到这次r数组的值与最终a被修改后的值一致了。这个例子中我们使用了*[5]int作为range表达式，其副本依旧是一个指向原数组 a的指针，因此后续所有循环中均是&a指向的原数组亲自参与的，因此v能从&a指向的原数组中取出a修改后的值。


idiomatic go建议我们尽可能的用slice替换掉array的使用，这里用slice能否实现预期的目标呢？我们来试试：

package main

import (
	"fmt"
)

func sliceRangeExpression() {
    var a = [5]int{1, 2, 3, 4, 5}
    var r [5]int

    fmt.Println("sliceRangeExpression result:")
    fmt.Println("a = ", a)

    for i, v := range a[:] {
        if i == 0 {
            a[1] = 12
            a[2] = 13
        }

        r[i] = v
    }

    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
    fmt.Println("")
}
func main() {
	 sliceRangeExpression()
}

实际输出：
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]
期望输出：
a =  [1 2 3 4 5]
r =  [1 12 13 4 5]
a =  [1 12 13 4 5]


显然用slice也能实现预期要求，我们可以分析一下slice是如何做到的。slice在go的内部表示为一个struct，由(*T, len, cap)组成，其中*T指向slice对应的underlying array的指针，len是slice当前长度，cap为slice的最大容量。当range进行expression复制时，它实际上复制的是一个 slice，也就是那个struct，副本struct中的*T依旧指向原slice对应的array
```

> 个人觉得对于切片的赋值，最本质的理解就是，切片的复制，就是切片的底层数据结构那个结构体的复制！！！

复制后的结构体里面(*T, len, cap)中 ，*  *T依旧指向原slice对应的array，所以对于切片的修改一方面对应的是数组，另一方面，range 后面得到的切片的副本，通过切片的`*T`找到的仍然是原来的数组。

slice与array还有一个不同点，就是其len在运行时可以被改变，而array的len是一个常量，不可改变。那么len变化的 slice对for range有何影响呢？我们继续看一个例子：

```
package main

import (
	"fmt"
)


func sliceLenChangeRangeExpression() {
    var a = []int{1, 2, 3, 4, 5}
    var r = make([]int, 0)

    fmt.Println("sliceLenChangeRangeExpression result:")
    fmt.Println("a = ", a)

    for i, v := range a {
        if i == 0 {
            a = append(a, 6, 7)
        }

        r = append(r, v)
    }

    fmt.Println("r = ", r)
    fmt.Println("a = ", a)
}

func main(){
	sliceLenChangeRangeExpression()
	
}
输出结果：

a =  [1 2 3 4 5]
r =  [1 2 3 4 5]
a =  [1 2 3 4 5 6 7]

在这个例子中，原slice a在for range过程中被附加了两个元素6和7，其len由5增加到7，但这对于r却没有产生影响。这里的原因就在于a的副本a'的内部表示struct中的 len字段并没有改变，依旧是5，因此for range只会循环5次，也就只获取a对应的underlying数组的前5个元素。

range的副本行为会带来一些性能上的消耗，尤其是当range expression的类型为数组时，range需要复制整个数组；而当range expression类型为pointer to array或slice时，这个消耗将小得多，仅仅需要复制一个指针或一个slice的内部表示（一个struct）即可。我们可以通过 benchmark test来看一下三种情况的消耗情况对比：

//details-in-go/5/arraybenchmark
go test -bench=.
testing: warning: no tests to run
PASS
BenchmarkArrayRangeLoop-4             20000000           116 ns/op
BenchmarkPointerToArrayRangeLoop-4    20000000            64.5 ns/op
BenchmarkSliceRangeLoop-4             20000000            70.9 ns/op

```

可以看到range expression类型为slice或pointer to array的性能相近，消耗都近乎是数组类型的1/2。

# **其他range expression类型**

对于range后面的其他表达式类型，比如string, map, channel，for range依旧会制作副本

### **【string】**

对string来说，由于string的内部表示为struct {*byte, len}

并且string本身是immutable的，因此其行为和消耗和slice expression类似。不过for range对于string来说，每次循环的单位是rune(code point的值)，而不是byte，index为迭代字符码点的第一个字节的position：

> range对于string循环的是rune(code point的值)而不是字节，而不是byte，index为迭代字符码点的第一个字节的position：

```
    var s = "中国人"

    for i, v := range s {
        fmt.Printf("%d %s 0x%x\n", i, string(v), v)
    }

输出结果：
0 中 0x4e2d
3 国 0x56fd
6 人 0x4eba
```

> 如果s中存在非法utf8字节序列，那么v将返回0xFFFD这个特殊值，并且在接下来一轮循环中，v将仅前进一个字节：
>
> ```
> //byte sequence of s: 0xe4 0xb8 0xad 0xe5 0x9b 0xbd 0xe4 0xba 0xba
>     var sl = []byte{0xe4, 0xb8, 0xad, 0xe5, 0x9b, 0xbd, 0xe4, 0xba, 0xba}
>     for _, v := range sl {
>         fmt.Printf("0x%x ", v)
>     }
>     fmt.Println("\n")
> 
>     sl[3] = 0xd0
>     sl[4] = 0xd6
>     sl[5] = 0xb9
> 
>     for i, v := range string(sl) {
>         fmt.Printf("%d %x\n", i, v)
>     }
> 
> 输出结果：
> 
> 0xe4 0xb8 0xad 0xe5 0x9b 0xbd 0xe4 0xba 0xba
> 
> 0 4e2d
> 3 fffd
> 4 5b9
> 6 4eba
> ```
>
> 

### **【map】**

对于map来说，map内部表示为一个指针，指针副本也指向真实map，因此for range操作均操作的是源map。

for range不保证每次迭代的元素次序，对于下面代码：

 

```
var m = map[string]int{
    "tony": 21,
    "tom": 22,
    "jim": 23,
  }

  for k, v := range m {
    fmt.Println(k, v)
  }
  
  
  输出结果可能是：

tom 22
jim 23
tony 21

也可能是：

tony 21
tom 22
jim 23

或其他可能。

```

> 对于map而言，for range 迭代不能保证每次迭代的元素的顺序。

一个简短的小结：

> strings  ：`struct {*byte  len}`
>
> map  :	`指针`
>
> 所以无论是切片，map还是string 副本都是复制了指针来进行操作。





### **如果map中的某项在循环到达前被在循环体中删除了，那么它将不会被iteration variable获取到**。

```

如果map中的某项在循环到达前被在循环体中删除了，那么它将不会被iteration variable获取到。
    counter := 0
    for k, v := range m {
        if counter == 0 {
            delete(m, "tony")
        }
        counter++
        fmt.Println(k, v)
    }
    fmt.Println("counter is ", counter)

反复运行多次，我们得到的两个结果：

tony 21
tom 22
jim 23
counter is  3

tom 22
jim 23
counter is  2

如果在循环体中新创建一个map元素项，那该项元素可能出现在后续循环中，也可能不出现：

    m["tony"] = 21
    counter = 0

    for k, v := range m {
        if counter == 0 {
            m["lucy"] = 24
        }
        counter++
        fmt.Println(k, v)
    }
    fmt.Println("counter is ", counter)

执行结果：

tony 21
tom 22
jim 23
lucy 24
counter is  4

or

tony 21
tom 22
jim 23
counter is  3
```

### **【channel】**

对于channel来说，channel内部表示为一个指针，channel的指针副本也指向真实channel。

for range**最终以阻塞读的方式阻塞在channel expression上**（即便是buffered channel，当channel中无数据时，for range也会阻塞在channel上），直到channel关闭：



```
 func main() {
  var c = make(chan int)
 	go func() {
    time.Sleep(time.Second * 3)
    c <- 1
    c <- 2
    c <- 3
    close(c)
  }()

  for v := range c {
    fmt.Println(v)
  }
}

运行结果：

1
2
3
```

### **select求值** 

golang引入的select为我们提供了一种在多个channel间实现“多路复用”的一种机制。select的运行机制这里不赘述，但select的case expression的求值顺序我们倒是要通过一个例子来了解一下：

```
func takeARecvChannel() chan int {
    fmt.Println("invoke takeARecvChannel")
    c := make(chan int)

    go func() {
        time.Sleep(3 * time.Second)
        c <- 1
    }()

    return c
}

func getAStorageArr() *[5]int {
    fmt.Println("invoke getAStorageArr")
    var a [5]int
    return &a
}

func takeASendChannel() chan int {
    fmt.Println("invoke takeASendChannel")
    return make(chan int)
}

func getANumToChannel() int {
    fmt.Println("invoke getANumToChannel")
    return 2
}

func main() {
    select {
    //recv channels
    case (getAStorageArr())[0] = <-takeARecvChannel():
        fmt.Println("recv something from a recv channel")

        //send channels
    case takeASendChannel() <- getANumToChannel():
        fmt.Println("send something to a send channel")
    }
}


运行结果:

$go run select.go
invoke takeARecvChannel
invoke takeASendChannel
invoke getANumToChannel

invoke getAStorageArr
recv something from a recv channel


```



1) select执行开始时，首先所有case expression的表达式都会被求值一遍，按语法先后次序。

```
invoke takeARecvChannel
invoke takeASendChannel
invoke getANumToChannel
```

例外的是`recv channel`的位于赋值等号左边的表达式（这里是：(`getAStorageArr())[0]`）不会被求值。



2) 如果选择要执行的case是一个`recv channel`，那么它的赋值等号左边的表达式会被求值：如例子中当`goroutine` `3s`后向`recvchan`写入一个int值后，`select`选择了`recv channel`执行，此时对=左侧的表达式 (`getAStorageArr()`)[0] 开始求值，输出“`invoke getAStorageArr`”。

### **panic的recover过程**

> Go没有提供“try-catch-finally”这样的异常处理设施，而仅仅提供了panic和recover，其中recover还要结合 defer使用。最初这也是被一些人诟病的点。但和错误码返回值一样，渐渐的大家似乎适应了这些，征讨之声渐稀，即便有也是排在“缺少generics” 之后了。

#### **【panicking】**

从结果可以看出：
  panic在zoo中发生，在zoo真正退出前，zoo中注册的defer函数会被逐一执行(FILO)，由于zoo defer中没有捕捉panic，因此panic被抛向其caller：bar。
  这时对于bar而言，其函数体中的zoo的调用就好像变成了panic调用似的，zoo有些类似于“黑客帝国3”中里奥被史密斯(panic)感 染似的，也变成了史密斯(panic)。panic在bar中扩展开来，bar中的defer也没有捕捉和recover panic，因此在bar中的defer func执行完毕后，panic继续抛给bar的caller: foo；
  这时对于foo而言，bar就变成了panic，同理，最终foo将panic抛给了main
  main与上述函数一样，没有recover，直接异常返回，导致进程异常退出。

#### **【recover】**

recover只有在defer函数中调用才能起到recover的作用，这样recover就和defer函数有了紧密联系。我们在zoo的defer函数中捕捉并recover这个panic：

```
func zoo() {
    defer func() {
        fmt.Println("zoo defer func1 invoked")
    }()

    defer func() {
        if x := recover(); x != nil {
            log.Printf("recover panic: %v in zoo recover defer func", x)
        }
    }()

    defer func() {
        fmt.Println("zoo defer func2 invoked")
    }()

    fmt.Println("zoo invoked")
    panic("zoo runtime exception")

}


这回的执行结果如下：

$go run recover.go
foo invoked
bar invoked
zoo invoked
zoo defer func2 invoked
2015/09/17 16:28:00 recover panic: zoo runtime exception in zoo recover defer func
zoo defer func1 invoked
do something after zoo in bar
bar defer func invoked
do something after bar in foo
foo defer func invoked

由于zoo在defer里恢复了panic，这样在zoo返回后，bar不会感知到任何异常，将按正常逻辑输出函数执行内容，比如：“do something after zoo in bar”,以此类推。

但若如果在zoo defer func中recover panic后，又raise another panic，那么zoo对于bar来说就又会变成panic了。

```

