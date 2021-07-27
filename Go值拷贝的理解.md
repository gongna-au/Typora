# Go值拷贝的理解



> In a function call, the function value and arguments are evaluated in the usual order. After they are evaluated, the parameters of the call are passed by value to the function and the called function begins execution.

官方文档已经明确说明：Go里边函数传参只有值传递一种方式: 值传递
那么为什么还会有有关Go的值拷贝的思考？

```
package main

import (
    "fmt"
)

func main() {
    arr := [5]int{0, 1, 2, 3, 4}
    s := arr[1:]
    changeSlice(s)
    fmt.Println(s)
    fmt.Println(arr)
}

func changeSlice(arr []int) {
    for i := range arr {
        arr[i] = 10
    }
}


Output:
[10 10 10 10]
[0 10 10 10 10]
```

如果Go是值拷贝的，那么我修改了函数 `changeSlice` 里面的`slice s` 的值，为什么main函数里面的`slice`和 `array`也被修改了![preview](https://segmentfault.com/img/remote/1460000020086648?w=1256&h=946/view)

以上图为例，a 是初始变量，b 是引用变量(Go中并不存在)，p 是指针变量,![img](https://segmentfault.com/img/remote/1460000020086649?w=896&h=498)

在这里变量a被拷贝后，地址发生了变化，地址上存储的是原先地址存储的值 10 变量p被拷贝后，地址发生了变化，地址上存储的还是原先地址存储的值 ）0X001, 然后按照这个地址去查找，找到的是 0X001 上面存储的值

所以，当你去修改拷贝后的*p的值，其实修改的还是0X001地址上的值，而不是 拷贝后a的值

> 怎么理解呢？就是对于切片的底层数据而言，其中三个 要素。类型，容量，指针，指针是用来干嘛的，就是指向数组啊，所以说，不论你怎么拷贝切片，切片的指针都是指向原来的数组，当你修改切片的值时，你其实是在修改数组的值！！！！

**slice在实现的时候，其实是对array的映射，也就是说slice存对应的是原array的地址，就类似于p与a的关系，那么整个slice拷贝后，拷贝后的slice中存储的还是array的地址，去修改拷贝后的slice，其实跟修改slice，和原array是一样的**

**上面这句话很很很重要，要记住的是：	1.切片是对数组的映射，相当于是数组a和指向a的指针	2.不论对切片的拷贝是什么样的？切片的底层数据的指针都是指向数组的。去修改切片的值就是修改数组的值。**





#### 总结：

> go 值的拷贝都是值拷贝，只是切片中储存的是原数组的地址，“切片是对数组的引用” 每得到的一个切片都是一个指向数组的指针，当你企图修改切片的值，就是在修改数组的值。内在的逻辑是：一个切片就是你一个指向数组的指针，你通过切片去修改数组，然后在引用数组，自始至终，你都是在引用数组，不存在你切片里面存了你所引用的数组的数据！！！

![img](https://segmentfault.com/img/remote/1460000020086649?w=896&h=498)

> Go的拷贝都是值拷贝，只是slice中存储的是原array的地址，所以在拷贝的时候，其实是把地址拷贝的新的slice，那么此时修改slice的时候，还是根据slice中存储的地址，找到要修改的内容

