# go中的方法



## 1).方法的定义

```
func (recevier type) methodName(参数列表) (返回值列表){
    方法体
    return 返回值
}
```

## 2).方法的使用

- 方法需要个定义的类型进行绑定

  ```
  type Person struct {
   Name string
  }
  func (p Person)testMothod(){ //绑定P类型
   fmt.Println("Name =",p.Name)
  }
  
  func main()  {
  //声明类型并初始化
   person := Person{Name:"jack"}
   person.testMothod()
  }
  ```

- go语言中的方法是作用在指定的数据类型上面的，也就是说是和指定的数据类型进行绑定。也就是说自定义的类型是都可以有方法的。不仅仅是`type number int ` `number `就可以绑定自己的方法。

- 方法**只能通过定义的类型**的变量来调用，**不能直接调用**，不能拿其他类型的变量调用。也就是说：你通过 `type 类型名  int/struct{}/[]string`  定义了什么类型，然后去拿类型名去定义变量，`var 变量名  类型名` ，然后再拿变量去调用。

  ```
  type Person struct {
   Name string
  }
  
  type Car struct {
   Price int
  }
  
  func (p Person)testMothod(){
   fmt.Println("Name =",p.Name)
  }
  func main(){
  	var p Person={Name:"jack"}
  	p.testMothod()//正确调用
  	//错误调用
      car := Car{Price:5000000}
      car.testMothod() //只能使用Person类型调用，使用Car调用就会报错
  
  }
  
  ```

  

### 3).方法和函数的调用方式的区别

-  函数调用方式：函数名（实参列表）
-  方法调用方式：变量.方法名(实参列表)

```
package main
import (
"fmt"
)
type Person struct {
    Name string
}
//函数
func testFunc(p Person){
    fmt.Println("函数中的Name = ",p.Name)
}

//方法
func (p Person) testMethod(){
    fmt.Println("方法中的Name = ",p.Name)
}


func main(){
    //函数调用方式
    person := Person{Name:"Jack"}
    testFunc(person)
    //方法调用方式
    person.testMethod()
}

```

### 4).方法和函数对于接收数据处理的不同

- 普通函数，函数的参数是什么类型，你就需要传什么类型的参数进去。

```
type Person struct {
    Name string
}

func testFunc01(p Person){
    fmt.Println("函数中的Name = ",p.Name)
}

func testFunc01(p Person){
    fmt.Println("函数中的Name = ",p.Name)
}

func main(){
    //函数调用
    person := Person{Name:"Jack"}
    testFunc01(person)
    testFunc02(&person)  //这里如果是testFunc02(person) 这样调用就会报错
}
```

- **对于方法而言：**接收者为值类型时，可以直接使用指针类型的变量调用方法。接收者为指针类型时，可以直接使用值类型的变量调用方法。

  ```
  type Person struct {
      Name string
  }
  func (p Person) testMethod01(){
      fmt.Println("方法中的Name = ",p.Name)
  }
  func (p *Person) testMethod02(){
      fmt.Println("方法中的Name = ",p.Name)
  }
  
  func main(){
      //方法调用 
      person := Person{Name:"Jack"}
       person.testMethod01()
       (&person).testMethod01()
       //以上两者的调用方式都是一样的。都是值拷贝
       person.testMethod02()
       (&person).testMethod02()
       //这里 person.testMethod02() 和 (&person).testMethod02() 调用都是一样的，都是地址拷贝
  }
  ```

  小结：**不管调用形式如何，决定是值拷贝还是地址拷贝，看方法与哪个类型进行绑定。如果是和值类型，比如（p Person）,则是值拷贝，如果和指针类型，比如（p *Person）则是地址拷贝**

# **什么是面向对象？**

```
package main

import (
   "fmt"  
)

/* 定义结构体 */
type Circle struct {
  radius float64
}

func main() {
  var c1 Circle
  c1.radius = 10.00
  fmt.Println("圆的面积 = ", c1.getArea())
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
  //c.radius 即为 Circle 类型对象中的属性
  return 3.14 * c.radius * c.radius
}

```

> 我理解的对象是一个具体类型变量，这个变量（对象）可以调用很多方法。方法是具体类型的变量的方法

调用方法的具有具体类型的变量是对象

比如我们说定义一个类型：`type A struct{name string }`，然后实例化一个变量，`var  a  A ={ name:"dfsf"}`  `a.name `是变量的属性，我们也把这个叫做**对象的属性**  

**方法的好处是：把某个类型与方法绑定之后，我们随时随地实例化该类型的变量就可以使用这个方法**(也就是在这里我们说：person 这个类型具有了String方法)

```
type person struct {
	name string
}

func (p person) String() string{
	return "the person name is "+p.name
}

//调用的方法非常简单，使用类型的变量进行调用即可，类型变量和方法之前是一个.操作符，表示要调用这个类型变量的某个方法的意思。
```

### 值接收者和指针接收者决定了——对变量的修改会不会改变原变量

#### 1)如果是值接收者，调用方法时——其实是值的拷贝在调用方法

```
func main() {
	p:=person{name:"张三"}
	p.modify() //值接收者，修改无效
	//等价于p'.modify()
	fmt.Println(p.String())
}

type person struct {
	name string
}

func (p person) String() string{
	return "the person name is "+p.name
}

func (p person) modify(){
	p.name = "李四"
}
```

#### 2)如果是指针接收者，调用方法时——其实是原来的变量的在调用方法

```
func main() {
	p:=person{name:"张三"}
	p.modify() //指针接收者，修改有效
	fmt.Println(p.String())
}

type person struct {
	name string
}

func (p person) String() string{
	return "the person name is "+p.name
}

func (p *person) modify(){
	p.name = "李四"
}
```





```
type Person struct {
    Name string
}
func (p Person) testMethod01(){
    fmt.Println("方法中的Name = ",p.Name)
}
func (p *Person) testMethod02(){
    fmt.Println("方法中的Name = ",p.Name)
}

func main(){
    //方法调用 
    person := Person{Name:"Jack"}
     person.testMethod01()
     (&person).testMethod01()
     //以上两者的调用方式都是一样的。都是值拷贝
     //也就是上面都等价于person'.testMethod01() (&person').testMethod01()
     
     person.testMethod02()
     (&person).testMethod02()
     //这里 person.testMethod02() 和 (&person).testMethod02() 调用都是一样的，都是地址拷贝
     //也就是都等价于person.testMethod02()  是原来的变量 (&person).testMethod02()
     
}
```

**在调用方法的时候，传递的接收者本质上都是副本，只不过一个是这个值副本，一是指向这个值指针的副本。指针具有指向原有值的特性，所以修改了指针指向的值，也就修改了原有的值。我们可以简单的理解为值接收者使用的是值的副本来调用方法，而指针接收者使用实际的值来调用方法。**

> 我们在调用方法时其实都是传递的是副本，只不过，你定义接收者的方式不同，决定了你传的是我们实例化的变量的副本，还是指向变量的指针的副本，指针的副本，你操作会改变原来的值。变量的副本完全是脱离原来的变量。**相当于**使用值接受者，是在使用副本，使用指针接收者是在使用原来的变量。

在上面的例子中，有没有发现，我们在调用指针接收者方法的时候，使用的也是一个值的变量，并不是一个指针，如果我们使用下面的也是可以的。

```
p:=person{name:"张三"}
(&p).modify() //指针接收者，修改有效
这个是因为：
这样也是可以的。如果我们没有这么强制使用指针进行调用，
Go的编译器自动会帮我们取指针，以满足接收者的要求。
```

**同样的，如果是一个值接收者的方法，使用指针也是可以调用的，Go编译器自动会解引用，以满足接收者的要求，比如例子中定义的`String()`方法，也可以这么调用：**

```
p:=person{name:"张三"}
fmt.Println((&p).String())
```



#### 总结：

> 决定你是要使用值接受者还是指针接收者，你只需要思考：
>
> 我在对该类型的变量进行操作时，是要改变该变量，（主要看:后续是在使用原来的变量，还是在使用改变后的变量）还是创建一个新的值作为返回。这个就可以觉得我们采用值传递还是指针传递。

#### 补充：好好利用go的多值返回

> Go语言支持函数方法的多值返回，也就说我们定义的函数方法可以返回多个值，比如标准库里的很多方法，都是返回两个值，第一个是函数需要返回的值，第二个是出错时返回的错误信息，这种的好处，我们的出错异常信息再也不用像Java一样使用一个Exception这么重的方式表示了，非常简洁。这个要求我们在定义函数时，思考我们需要返回什么值，在这个值之外在添加一个出错信息。

> 1. 如果接收器是map，func或chan，不要使用指针类型的，应该直接传值；
> 2. 如果接收器是slice并且该方法不重新切片或重新分配切片，不要使用指针类型的；
> 3. 如果该方法需要改变接收器，则接收器必须是指针；
> 4. 如果接收器是包含sync.Mutex或类似同步字段的结构体，则接收器必须是指针类型以避免副本的失效；
> 5. 如果接收器是比较大的结构体或数组，则指针类型的接收器更佳高效；
> 6. 如果接收器是结构体，数组或切片，且其内部元素指向可变的指针时，使用指针类型的接收器更容易理解；
> 7. 如果接收器是一个小型数组或结构体等简单类型（例如int或string）且没有可变字段和指针，使用值类型的更合理。