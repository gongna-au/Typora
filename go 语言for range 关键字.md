# go 语言for range 关键字

循环是所有编程语言都有的控制结构，除了使用经典的三段式循环之外，Go 语言还引入了另一个关键字 `range` 帮助我们快速遍历数组、切片、哈希表以及 Channel 等集合类型。本节将深入分析 Go 语言的两种循环，也就是 for 循环和 for-range 循环，我们会分析这两种循环的运行时结构以及它们的实现原理。

**for 循环能够将代码中的数据和逻辑分离，让同一份代码能够多次复用相同的处理逻辑**。我们先来看一下 Go 语言 for 循环对应的汇编代码，下面是一段经典的三段式循环的代码，我们将它编译成汇编指令：

> **for 循环能够将代码中的数据和逻辑分离，让同一份代码能够多次复用相同的处理逻辑**。

```go
package main

func main() {
	for i := 0; i < 10; i++ {
		println(i)
	}
}

"".main STEXT size=98 args=0x0 locals=0x18
	00000 (main.go:3)	TEXT	"".main(SB), $24-0
	...
	00029 (main.go:3)	XORL	AX, AX                   ;; i := 0
	00031 (main.go:4)	JMP	75
	00033 (main.go:4)	MOVQ	AX, "".i+8(SP)
	00038 (main.go:5)	CALL	runtime.printlock(SB)
	00043 (main.go:5)	MOVQ	"".i+8(SP), AX
	00048 (main.go:5)	MOVQ	AX, (SP)
	00052 (main.go:5)	CALL	runtime.printint(SB)
	00057 (main.go:5)	CALL	runtime.printnl(SB)
	00062 (main.go:5)	CALL	runtime.printunlock(SB)
	00067 (main.go:4)	MOVQ	"".i+8(SP), AX
	00072 (main.go:4)	INCQ	AX                       ;; i++
	00075 (main.go:4)	CMPQ	AX, $10                  ;; 比较变量 i 和 10
	00079 (main.go:4)	JLT	33                           ;; 跳转到 33 行如果 i < 10
	...
```

这里将上述汇编指令的执行过程分成三个部分进行分析：

1. 0029 ~ 0031 行负责循环的初始化；

   1. 对寄存器 `AX` 中的变量 `i` 进行初始化并执行 `JMP 75` 指令跳转到 0075 行；

2. 0075 ~ 0079 行负责检查循环的终止条件，将寄存器中存储的数据`i`

    与 10 比较；

   1. `JLT 33` 命令会在变量的值小于 10 时跳转到 0033 行执行循环主体；
   2. `JLT 33` 命令会在变量的值大于 10 时跳出循环体执行下面的代码；

3. 0033 ~ 0072 行是循环内部的语句；

   1. 通过多个汇编指令打印变量中的内容；
   2. `INCQ AX` 指令会将变量加一，然后再与 10 进行比较，回到第二步；

```
package main

func main() {
	arr := []int{1, 2, 3}
	for i, _ := range arr {
		println(i)
	}
}
```

在汇编语言中，无论是经典的 for 循环还是 for-range 循环都会使用 `JMP` 等命令跳回循环体的开始位置复用代码。从不同循环具有相同的汇编代码可以猜到，使用 for-range 的控制结构最终也会被 Go 语言编译器转换成普通的 for 循环，后面的分析也会印证这一点。



## 使用for range 循环存在的问题

- ### **循环永动机**

> 问题1：如过我们在循环数组的同时修改数组的元素，会不会得到一个永远都不会停止的循环呢？

```
func main() {
	arr := []int{1, 2, 3}
	for _, v := range arr {
		arr = append(arr, v)
	}
	fmt.Println(arr)
}

$ go run main.go
1 2 3 1 2 3
```

上述代码的输出意味着循环只遍历了原始切片中的三个元素，我们在遍历切片时追加的元素不会增加循环的执行次数，所以循环最终还是停了下来。

> 也就是说以上代码等价于：
>
> ```
> func main() {
> 	arr := []int{1, 2, 3}
> 	for _, v := range arr' {//arr'是arr的副本
> 		arr = append(arr, v)
> 	}
> 	fmt.Println(arr)
> }
> 
> $ go run main.go
> 1 2 3 1 2 3
> ```
>
> 

- ### 神奇指针

```
func main() {
	arr := []int{1, 2, 3}
	newArr := []*int{}
	for _, v := range arr {
		newArr = append(newArr, &v)
	}
	for _, v := range newArr {
		fmt.Println(*v)
	}
}


//以上的代码是我们在循环数组时，把获取到的数组元素的地址保存到另外一个数组时遇到的奇怪的问题，因为它的输出是这个样子的：
$ go run main.go
3 3 3

```

正确的做法是：

```
func main() {
	arr := []int{1, 2, 3}
	newArr := []*int{}
	for _, v := range arr {
		newArr = append(newArr, &arr[i])//使用 &arr[i] 替代 &v
	}
	for _, v := range newArr {
		fmt.Println(*v)
	}
}
```

**这个是为甚么呢？**



**原因:**



**小结：**



- ### 遍历清空数组

```
func main() {
	arr := []int{1, 2, 3}
	for i, _ := range arr {
		arr[i] = 0
	}
}

当我们想要在 Go 语言中清空一个切片或者哈希时，一般都会使用以上的方法将切片中的元素置零。

```

> **依次遍历切片和哈希看起来是非常耗费性能的**，**因为数组、切片和哈希占用的内存空间都是连续的**，所以最快的方法是直接清空这片内存中的内容，当我们编译上述代码时会得到以下的汇编指令：
>
> ```
> "".main STEXT size=93 args=0x0 locals=0x30
> 	0x0000 00000 (main.go:3)	TEXT	"".main(SB), $48-0
> 	...
> 	0x001d 00029 (main.go:4)	MOVQ	"".statictmp_0(SB), AX
> 	0x0024 00036 (main.go:4)	MOVQ	AX, ""..autotmp_3+16(SP)
> 	0x0029 00041 (main.go:4)	MOVUPS	"".statictmp_0+8(SB), X0
> 	0x0030 00048 (main.go:4)	MOVUPS	X0, ""..autotmp_3+24(SP)
> 	0x0035 00053 (main.go:5)	PCDATA	$2, $1
> 	0x0035 00053 (main.go:5)	LEAQ	""..autotmp_3+16(SP), AX
> 	0x003a 00058 (main.go:5)	PCDATA	$2, $0
> 	0x003a 00058 (main.go:5)	MOVQ	AX, (SP)
> 	0x003e 00062 (main.go:5)	MOVQ	$24, 8(SP)
> 	0x0047 00071 (main.go:5)	CALL	runtime.memclrNoHeapPointers(SB)
> 	...
> ```
>
> 从生成的汇编代码我们可以看出，编译器会直接使用 [`runtime.memclrNoHeapPointers`](https://draveness.me/golang/tree/runtime.memclrNoHeapPointers) 清空切片中的数据，这也是我们在下面的小节会介绍的内容。

也就是说：通过遍历切片或者哈希的方式来清空切片或者哈希是非常损耗性能的，更加好的办法是——通过直接清空切片或者哈希那块的内存的内容。



**具体的做法是：**

Go 语言会直接使用 [`runtime.memclrNoHeapPointers`](https://draveness.me/golang/tree/runtime.memclrNoHeapPointers) 或者 [`runtime.memclrHasPointers`](https://draveness.me/golang/tree/runtime.memclrHasPointers) 



```

```



- ### **随机遍历**

```
func main() {
	hash := map[string]int{
		"1": 1,
		"2": 2,
		"3": 3,
	}
	for k, v := range hash {
		println(k, v)
	}
}

当我们在 Go 语言中使用 range 遍历哈希表时，往往都会使用如下的代码结构，但是这段代码在每次运行时都会打印出不同的结果。
两次运行上述代码可能会得到不同的结果，第一次会打印 2 3 1，第二次会打印 1 2 3，如果我们运行的次数足够多，最后会得到几种不同的遍历顺序。


$ go run main.go
2 2
3 3
1 1

$ go run main.go
1 1
2 2
3 3

Go 语言在运行时为哈希表的遍历引入了不确定性，也是告诉所有 Go 语言的使用者，程序不要依赖于哈希表的稳定遍历，我们在下面的小节会介绍在遍历的过程是如何引入不确定性的。
```

 **也就是说：不要企图通过遍历哈希表得到一个确定的输出结果！！**



- ### 经典循环

Go 语言中的经典循环在编译器看来是一个 `OFOR` 类型的节点，这个节点由以下四个部分组成：

1. 初始化循环的 `Ninit`；
2. 循环的继续条件 `Left`；
3. 循环体结束时执行的 `Right`；
4. 循环体 `NBody`：

在生成 SSA 中间代码的阶段，[`cmd/compile/internal/gc.state.stmt`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.stmt) 方法在发现传入的节点类型是 `OFOR` 时会执行以下的代码块，这段代码会将循环中的代码分成不同的块：

```
func (s *state) stmt(n *Node) {
	switch n.Op {
	case OFOR, OFORUNTIL:
		bCond, bBody, bIncr, bEnd := ...

		b := s.endBlock()
		b.AddEdgeTo(bCond)
		s.startBlock(bCond)
		s.condBranch(n.Left, bBody, bEnd, 1)

		s.startBlock(bBody)
		s.stmtList(n.Nbody)

		b.AddEdgeTo(bIncr)
		s.startBlock(bIncr)
		s.stmt(n.Right)
		b.AddEdgeTo(bCond)
		s.startBlock(bEnd)
	}
}
```

一个常见的 for 循环代码会被 [`cmd/compile/internal/gc.state.stmt`](https://draveness.me/golang/tree/cmd/compile/internal/gc.state.stmt) 转换成下面的控制结构，该结构中包含了 4 个不同的块，这些代码块之间的连接表示汇编语言中的跳转关系，与我们理解的 for 循环控制结构没有太多的差别。

![golang-for-loop-ssa](https://img.draveness.me/2020-01-17-15792766877627-golang-for-loop-ssa.png)

**图 Go 语言循环生成的 SSA 代码**

[机器码生成](https://draveness.me/golang/docs/part1-prerequisite/ch02-compile/golang-machinecode/)阶段会将这些代码块转换成机器码，以及指定 CPU 架构上运行的机器语言，就是我们在前面编译得到的汇编指令。

- ## 范围循环

与简单的经典循环相比，范围循环在 Go 语言中更常见、实现也更复杂。这种循环同时使用 `for` 和 `range` 两个关键字，编译器会在编译期间将所有 for-range 循环变成经典循环。从编译器的视角来看，就是将 `ORANGE` 类型的节点转换成 `OFOR` 节点:

![Golang-For-Range-Loop](https://img.draveness.me/2020-01-17-15792766926441-Golang-For-Range-Loop.png)

 **范围循环、普通循环和 SSA**

节点类型的转换过程都发生在中间代码生成阶段，所有的 for-range 循环都会被 [`cmd/compile/internal/gc.walkrange`](https://draveness.me/golang/tree/cmd/compile/internal/gc.walkrange) 转换成不包含复杂结构、只包含基本表达式的语句。接下来，我们按照循环遍历的元素类型依次介绍遍历数组和切片、哈希表、字符串以及管道时的过程。

### 数组和切片

对于数组和切片来说，Go 语言有三种不同的遍历方式，这三种不同的遍历方式分别对应着代码中的不同条件，它们会在 [`cmd/compile/internal/gc.walkrange`](https://draveness.me/golang/tree/cmd/compile/internal/gc.walkrange) 函数中转换成不同的控制逻辑，我们会分成几种情况分析该函数的逻辑：

1. 分析遍历数组和切片清空元素的情况；
2. 分析使用 `for range a {}` 遍历数组和切片，不关心索引和数据的情况；
3. 分析使用 `for i := range a {}` 遍历数组和切片，只关心索引的情况；
4. 分析使用 `for i, elem := range a {}` 遍历数组和切片，关心索引和数据的情况；

```
func walkrange(n *Node) *Node {
	switch t.Etype {
	case TARRAY, TSLICE:
		if arrayClear(n, v1, v2, a) {
			return n
		}
```

[`cmd/compile/internal/gc.arrayClear`](https://draveness.me/golang/tree/cmd/compile/internal/gc.arrayClear) 是一个非常有趣的优化，它会优化 Go 语言遍历数组或者切片并删除全部元素的逻辑：

```go
// 原代码
for i := range a {
	a[i] = zero
}

// 优化后
if len(a) != 0 {
	hp = &a[0]
	hn = len(a)*sizeof(elem(a))
	memclrNoHeapPointers(hp, hn)
	i = len(a) - 1
}
```

相比于依次清除数组或者切片中的数据，Go 语言会直接使用 [`runtime.memclrNoHeapPointers`](https://draveness.me/golang/tree/runtime.memclrNoHeapPointers) 或者 [`runtime.memclrHasPointers`](https://draveness.me/golang/tree/runtime.memclrHasPointers) 清除目标数组内存空间中的全部数据，并在执行完成后更新遍历数组的索引，这也印证了我们在遍历清空数组一节中观察到的现象。

线处理了这种特殊的情况之后，我们可以回到 `ORANGE` 节点的处理过程了。这里会设置 for 循环的 `Left` 和 `Right` 字段，也就是终止条件和循环体每次执行结束后运行的代码：

```
	ha := a

		hv1 := temp(types.Types[TINT])
		hn := temp(types.Types[TINT])

		init = append(init, nod(OAS, hv1, nil))
		init = append(init, nod(OAS, hn, nod(OLEN, ha, nil)))

		n.Left = nod(OLT, hv1, hn)
		n.Right = nod(OAS, hv1, nod(OADD, hv1, nodintconst(1)))

		if v1 == nil {
			break
		}
```

如果循环是 `for range a {}`，那么就满足了上述代码中的条件 `v1 == nil`，即循环不关心数组的索引和数据，这种循环会被编译器转换成如下形式：

```
ha := a
hv1 := 0
hn := len(ha)
v1 := hv1
for ; hv1 < hn; hv1++ {
    ...
}

```

这是 `ORANGE` 结构在编译期间被转换的最简单形式，由于原代码不需要获取数组的索引和元素，只需要使用数组或者切片的数量执行对应次数的循环，所以会生成一个最简单的 for 循环。



如果我们在遍历数组时需要使用索引 `for i := range a {}`，那么编译器会继续会执行下面的代码：

```
if v2 == nil {
			body = []*Node{nod(OAS, v1, hv1)}
			break
		}
```

`v2 == nil` 意味着调用方不关心数组的元素，只关心遍历数组使用的索引。它会将 `for i := range a {}` 转换成下面的逻辑，与第一种循环相比，这种循环在循环体中添加了 `v1 := hv1` 语句，传递遍历数组时的索引：

```
ha := a
hv1 := 0
hn := len(ha)
v1 := hv1
for ; hv1 < hn; hv1++ {
    v1 = hv1
    ...
}
```

上面两种情况虽然也是使用 range 会经常遇到的情况，但是同时去遍历索引和元素也很常见。处理这种情况会使用下面这段的代码：

```
tmp := nod(OINDEX, ha, hv1)
		tmp.SetBounded(true)
		a := nod(OAS2, nil, nil)
		a.List.Set2(v1, v2)
		a.Rlist.Set2(hv1, tmp)
		body = []*Node{a}
	}
	n.Ninit.Append(init...)
	n.Nbody.Prepend(body...)

	return n
}
```

这段代码处理的使用者同时关心索引和切片的情况。它不仅会在循环体中插入更新索引的语句，还会插入赋值操作让循环体内部的代码能够访问数组中的元素：

```
ha := a   //复制副本的过程
hv1 := 0
hn := len(ha)
v1 := hv1
v2 := nil  //用新的变量来储蓄副本的元素
for ; hv1 < hn; hv1++ {
    tmp := ha[hv1] //使用副本的元素，把副本的元素给同一个temp，然后temp又给了V2，所以说每次range循环得到的元素都被储存在了一个临时变量里面。 
    v1, v2 = hv1, tmp
    ...
}
```

对于所有的 range 循环，**Go 语言都会在编译期将原切片或者数组赋值给一个新变量 `ha`，在赋值的过程中就发生了拷贝**，**而我们又通过 `len` 关键字预先获取了切片的长度**，所以在循环中追加新的元素也不会改变循环执行的次数，这也就解释了循环永动机一节提到的现象。

> 而遇到这种同时遍历索引和元素的 range 循环时，Go 语言会额外创建一个新的 `v2` 变量存储切片中的元素，**循环中使用的这个变量 v2 会在每一次迭代被重新赋值而覆盖，赋值时也会触发拷贝**。

```
func main() {
	arr := []int{1, 2, 3}
	newArr := []*int{}
	for i, _ := range arr {
		newArr = append(newArr, &arr[i])
	}
	for _, v := range newArr {
		fmt.Println(*v)
	}
}
```

因为在循环中获取返回变量的地址都完全相同，所以会发生神奇的指针一节中的现象。原因就是：**复制副本的过程每次range循环得到的元素都被储存在了一个临时变量里面，而这个临时变量经过迭代之后就会被更新。而你的Print（）函数是根据地址找的数据，所以最后打印出来的数据就是最后一次的数据，因为之前的数据都被覆盖了。**

```
ha := a   
//复制副本的过程每次range循环得到的元素都被储存在了一个临时变量里面，而这个临时变量经过迭代之后就会被更新。而你的Print（）函数是根据地址找的数据，所以最后打印出来的数据就是最后一次的数据，因为之前的数据都被覆盖了。
hv1 := 0
hn := len(ha)
v1 := hv1
v2 := nil  //用新的变量来储蓄副本的元素
for ; hv1 < hn; hv1++ {
    tmp := ha[hv1] //使用副本的元素，把副本的元素给同一个temp，然后temp又给了V2，所以说每次range循环得到的元素都被储存在了一个临时变量里面。 
    v1, v2 = hv1, tmp
    ...
}
```

因此当我们想要访问数组中元素所在的地址时，不应该直接获取 range 返回的变量地址 `&v2`，而应该使用 `&a[index]` 这种形式。

### 哈希表

在遍历哈希表时，编译器会使用 [`runtime.mapiterinit`](https://draveness.me/golang/tree/runtime.mapiterinit) 和 [`runtime.mapiternext`](https://draveness.me/golang/tree/runtime.mapiternext) 两个运行时函数重写原始的 for-range 循环：

```go
ha := a
hit := hiter(n.Type)
th := hit.Type
mapiterinit(typename(t), ha, &hit)
for ; hit.key != nil; mapiternext(&hit) {
    key := *hit.key
    val := *hit.val
}
```

上述代码是展开 `for key, val := range hash {}` 后的结果，在 [`cmd/compile/internal/gc.walkrange`](https://draveness.me/golang/tree/cmd/compile/internal/gc.walkrange) 处理 `TMAP` 节点时，编译器会根据 range 返回值的数量在循环体中插入需要的赋值语句：

![golang-range-map](https://img.draveness.me/2020-01-17-15792766877639-golang-range-map.png)

####  **不同方式遍历哈希插入的语句**

这三种不同的情况分别向循环体插入了不同的赋值语句。遍历哈希表时会使用 [`runtime.mapiterinit`](https://draveness.me/golang/tree/runtime.mapiterinit) 函数初始化遍历开始的元素：

```
func mapiterinit(t *maptype, h *hmap, it *hiter) {
	it.t = t
	it.h = h
	it.B = h.B
	it.buckets = h.buckets

	r := uintptr(fastrand())
	it.startBucket = r & bucketMask(h.B)
	it.offset = uint8(r >> h.B & (bucketCnt - 1))
	it.bucket = it.startBucket
	mapiternext(it)
}
```

该函数会初始化 [`runtime.hiter`](https://draveness.me/golang/tree/runtime.hiter) 结构体中的字段，并通过 [`runtime.fastrand`](https://draveness.me/golang/tree/runtime.fastrand) 生成一个随机数帮助我们随机选择一个遍历桶的起始位置。Go 团队在设计哈希表的遍历时就不想让使用者依赖固定的遍历顺序，所以引入了随机数保证遍历的随机性。

遍历哈希会使用 [`runtime.mapiternext`](https://draveness.me/golang/tree/runtime.mapiternext)，我们在这里简化了很多逻辑，省去了一些边界条件以及哈希表扩容时的兼容操作，这里只需要关注处理遍历逻辑的核心代码，我们会将该函数分成桶的选择和桶内元素的遍历两部分，首先是桶的选择过程：

```go
func mapiternext(it *hiter) {
	h := it.h
	t := it.t
	bucket := it.bucket
	b := it.bptr
	i := it.i
	alg := t.key.alg

next:
	if b == nil {
		if bucket == it.startBucket && it.wrapped {
			it.key = nil
			it.value = nil
			return
		}
		b = (*bmap)(add(it.buckets, bucket*uintptr(t.bucketsize)))
		bucket++
		if bucket == bucketShift(it.B) {
			bucket = 0
			it.wrapped = true
		}
		i = 0
	}
```

这段代码主要有两个作用：

1. 在待遍历的桶为空时，选择需要遍历的新桶；
2. 在不存在待遍历的桶时。返回 `(nil, nil)` 键值对并中止遍历；

[`runtime.mapiternext`](https://draveness.me/golang/tree/runtime.mapiternext) 剩余代码的作用是从桶中找到下一个遍历的元素，在大多数情况下都会直接操作内存获取目标键值的内存地址，不过如果哈希表处于扩容期间就会调用 [`runtime.mapaccessK`](https://draveness.me/golang/tree/runtime.mapaccessK) 获取键值对：

```go
	for ; i < bucketCnt; i++ {
		offi := (i + it.offset) & (bucketCnt - 1)
		k := add(unsafe.Pointer(b), dataOffset+uintptr(offi)*uintptr(t.keysize))
		v := add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+uintptr(offi)*uintptr(t.valuesize))
		if (b.tophash[offi] != evacuatedX && b.tophash[offi] != evacuatedY) ||
			!(t.reflexivekey() || alg.equal(k, k)) {
			it.key = k
			it.value = v
		} else {
			rk, rv := mapaccessK(t, h, k)
			it.key = rk
			it.value = rv
		}
		it.bucket = bucket
		it.i = i + 1
		return
	}
	b = b.overflow(t)
	i = 0
	goto next
}
```

当上述函数已经遍历了正常桶后，会通过 [`runtime.bmap.overflow`](https://draveness.me/golang/tree/runtime.bmap.overflow) 遍历哈希中的溢出桶。

![golang-range-map-and-buckets](https://img.draveness.me/2020-01-17-15792766877646-golang-range-map-and-buckets.png)

#### **哈希表的遍历过程**

简单总结一下哈希表遍历的顺序，首先会选出一个绿色的正常桶开始遍历，随后遍历所有黄色的溢出桶，最后依次按照索引顺序遍历哈希表中其他的桶，直到所有的桶都被遍历完成。

### 字符串 [#](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-for-range/#字符串)

遍历字符串的过程与数组、切片和哈希表非常相似，只是在遍历时会获取字符串中索引对应的字节并将字节转换成 `rune`。我们在遍历字符串时拿到的值都是 `rune` 类型的变量，`for i, r := range s {}` 的结构都会被转换成如下所示的形式：

```go
ha := s
for hv1 := 0; hv1 < len(ha); {
    hv1t := hv1
    hv2 := rune(ha[hv1])
    if hv2 < utf8.RuneSelf {
        hv1++
    } else {
        hv2, hv1 = decoderune(ha, hv1)
    }
    v1, v2 = hv1t, hv2
}
```

在前面的字符串一节中我们曾经介绍过字符串是一个只读的字节数组切片，所以范围循环在编译期间生成的框架与切片非常类似，只是细节有一些不同。

使用下标访问字符串中的元素时得到的就是字节，但是这段代码会将当前的字节转换成 `rune` 类型。如果当前的 `rune` 是 ASCII 的，那么只会占用一个字节长度，每次循环体运行之后只需要将索引加一，但是如果当前 `rune` 占用了多个字节就会使用 [`runtime.decoderune`](https://draveness.me/golang/tree/runtime.decoderune) 函数解码，具体的过程就不在这里

详细介绍了。

### 通道 

使用 range 遍历 Channel 也是比较常见的做法，一个形如 `for v := range ch {}` 的语句最终会被转换成如下的格式：

```go
ha := a
hv1, hb := <-ha
for ; hb != false; hv1, hb = <-ha {
    v1 := hv1
    hv1 = nil
    ...
}
```

这里的代码可能与编译器生成的稍微有一些出入，但是结构和效果是完全相同的。该循环会使用 `<-ch` 从管道中取出等待处理的值，这个操作会调用 [`runtime.chanrecv2`](https://draveness.me/golang/tree/runtime.chanrecv2) 并阻塞当前的协程，当 [`runtime.chanrecv2`](https://draveness.me/golang/tree/runtime.chanrecv2) 返回时会根据布尔值 `hb` 判断当前的值是否存在：

- 如果不存在当前值，意味着当前的管道已经被关闭；
- 如果存在当前值，会为 `v1` 赋值并清除 `hv1` 变量中的数据，然后重新陷入阻塞等待新数据；

这一节介绍的两个关键字 `for` 和 `range` 都是我们在学习和使用 Go 语言中无法绕开的，通过分析和研究它们的底层原理，让我们对实现细节有了更清楚的认识，包括 Go 语言遍历数组和切片时会复用变量、哈希表的随机遍历原理以及底层的一些优化，这都能帮助我们更好地理解和使用 Go 语言。