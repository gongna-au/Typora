## go的值语义和引用语义

> go的值语义与引用语义的本质真正在于——赋值

```
b = a
b.Modify()
```

**当b的修改不会改变a的值时——说明：该类型是值类型**

**当b的修改会改变a的值时——说明：该类型是引用类型**

**Go语言中的大多数类型都基于值语义，包括：**

- **基本类型，如`byte、int、bool、float32、string`等；**
- **复合类型，如`arry、struct、pointer`等；**

类型是值类型说明什么？——说明：你把这个类型的变量赋值给一个新的变量，新的变量的修改不会改变原变量。

简化为：

```
b=a
b.Modify()
```

## 数组的值语义和引用语义

> 通过一个函数传递数组是引用语义。
>
> 在结构体中定义数据变量又是值语义。

```
var a = [3] int{1,2,3}
var b = a
b[1]++
fmt.Println(a, b)   // [1 2 3] [1 3 3]

从结果看，b=a赋值语句是数组内容的完整复制，要想表达引用，需要用指针
```

从结果看数组的赋值是值语义。

```
var a = [3] int{1,2,3}
var b = &a　　　　// 引用语义
b[1]++
fmt.Println(a, b)   // [1 3 3] [1 3 3]
```

## 接口

> Interface 是一组抽象方法（未具体实现的方法/仅包含方法名参数返回值的方法）的集合，如果实现了 interface 中的所有方法，即该类/对象就实现了该接口。

也就是说，接口是方法的集合，里面的方法**是未具体实现的方法**，（仅仅包含方法名字和参数返回值），当一个类型实现了接口里所有的方法，这个类型就实现了这个接口。这个变量就实现了这个接口，这个对象就实现了这个接口。

> Interface 可以被任意对象实现（因为任意的实例化的变量都可以去实现接口里所有的方法），一个类型/对象也可以实现多个 interface；interface的变量可以是持有任意实现该interface类型的对象。
>
>  

```

package main

    import "fmt"

    type Human struct {
        name string
        age int
        phone string
    }

    type Student struct {
        Human //匿名字段
        school string
        loan float32
    }

    type Employee struct {
        Human //匿名字段
        company string
        money float32
    }

    //Human实现SayHi方法
    func (h Human) SayHi() {
        fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
    }

    //Human实现Sing方法
    func (h Human) Sing(lyrics string) {
        fmt.Println("La la la la...", lyrics)
    }

    //Employee重载Human的SayHi方法
    func (e Employee) SayHi() {
        fmt.Printf("Hi, I am %s, I work at %s. Call me on %s\n", e.name,
            e.company, e.phone)
        }

    // Interface Men被Human,Student和Employee实现
    // 因为这三个类型都实现了这两个方法
    type Men interface {
        SayHi()
        Sing(lyrics string)
    }

    func main() {
        mike := Student{Human{"Mike", 25, "222-222-XXX"}, "MIT", 0.00}
        paul := Student{Human{"Paul", 26, "111-222-XXX"}, "Harvard", 100}
        sam := Employee{Human{"Sam", 36, "444-222-XXX"}, "Golang Inc.", 1000}
        tom := Employee{Human{"Tom", 37, "222-444-XXX"}, "Things Ltd.", 5000}

        //定义Men类型的变量i
        var i Men

        //i能存储Student
        i = mike　　　　
        fmt.Println("This is Mike, a Student:")
        i.SayHi()
        i.Sing("November rain")

        //i也能存储Employee
        i = tom
        fmt.Println("This is tom, an Employee:")
        i.SayHi()
        i.Sing("Born to be wild")

        //定义了slice Men
        fmt.Println("Let's use a slice of Men and see what happens")
        x := make([]Men, 3)
        //这三个都是不同类型的元素，但是他们实现了interface同一个接口
        x[0], x[1], x[2] = paul, sam, mike

        for _, value := range x{
            value.SayHi()
        }
    }
```





```
//自己写一个大类型——首先是人

    type Human struct {
        name string		//人有姓名
        age int			//人有年龄
        phone string	//人有电话
    }
//学生肯定是人，学生会继承人的属性
    type Student struct {
        Human //匿名字段  //在这里学生会继承人的属性
        school string	// 学生有学校
        loan float32	// 学生贷款
    }
//雇员肯定是人，雇员会继承人的属性
    type Employee struct {
        Human //匿名字段	//在这里雇员会继承人的属性
        company string		// 雇员有公司
        money float32		// 雇员有工资
    }
---------------------------------------------

//人可以打招呼，所以我们让人实现打招呼

  //Human实现SayHi方法
    func (h Human) SayHi() {
        fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
    } 
    
    
//人可以唱歌，所以我们让人实现唱歌   

   //Human实现Sing方法
    func (h Human) Sing(lyrics string) {
        fmt.Println("La la la la...", lyrics)
    }
    
//Employee重载Human的SayHi方法
    func (e Employee) SayHi() {
        fmt.Printf("Hi, I am %s, I work at %s. Call me on %s\n", e.name,
            e.company, e.phone)
        } 
        // e.name,e.phone是人的属性，这也是我们为什么要继承人，为什么要改写SayHi方法，因为e.company的属性，不是人所有的，而是 e所有。
  
  
  
  因为Student和Employee都继承了父类，也就是可以实现父类的SayHi方法和Sing方法,所以Human，Student，Employee都实现了SayHi方法，Sing方法，把Sing方法和Sing方法我们当做一个方法集=（接口）取名为Man
  
  得到了
type Man interface{
	SayHi（）
	Sing(lyrics string)

} 
func main() {
        mike := Student{Human{"Mike", 25, "222-222-XXX"}, "MIT", 0.00}
        paul := Student{Human{"Paul", 26, "111-222-XXX"}, "Harvard", 100}
        sam := Employee{Human{"Sam", 36, "444-222-XXX"}, "Golang Inc.", 1000}
        tom := Employee{Human{"Tom", 37, "222-444-XXX"}, "Things Ltd.", 5000}

	//定义Men类型（接口类型）的变量i
        var i Men
   //我们定义类型的变量可以赋值给接口变量，这个时候接口变量的类型与被复制变量的类型一致
   //i能存储Student
        i = mike　　　　
        fmt.Println("This is Mike, a Student:")
        i.SayHi()
        i.Sing("November rain")

        //i也能存储Employee
        i = tom
        fmt.Println("This is tom, an Employee:")
        i.SayHi()
        i.Sing("Born to be wild")

        //定义了slice Men
        fmt.Println("Let's use a slice of Men and see what happens")
        x := make([]Men, 3)
        //这三个都是不同类型的元素，但是他们实现了interface同一个接口
        x[0], x[1], x[2] = paul, sam, mike
        //接口类型的变量被赋值后，它的类型与被赋值变量的类型一致

        for _, value := range x{
            value.SayHi()
        }
        

    接口（集合）类型的变量实现很多方法，
    具体类型的变量实现具体的方法
```

**要继承是为了：使用父类的属性或者方法，为什么要改写父类的方法，是为了，用我自己的属性。通过父类无法使用我自己的属性**

我们写一个接口的逻辑是：

我们**定义了几个**结构体**类型**，这些**类型**之间存在继承关系，这些 **类型实现了相同的方法**，然后这些相同方法的集合就是接口类型，接口类型可以储存我们之前定义的几个类型。

```
定义了：
    类型1 	  			类型2                 类型3
    |      					|					|
    |						|					|
    |						|					|
    |						|					|
    |						|					|
   方法1，方法2   		方法1，方法2      	 方法1，方法2
   \					  	|						/
   	\						|					  /
   	 \						|					/
   	  \						|				  /
   	   \					|				/
   		\					|
   		   					|
   	  	  				
   						新类型
   						接口类型
   我们得到新类型——接口类型之后并不能直接使用
   我们要得到接口类型的变量，var i Man
   类型1 类型2 类型3的变量可以赋值给接口类型的变量
   也就是说
   
   
```

****

**我们得到新类型——接口类型之后并不能直接使用， 我们要得到接口类型的变量，`var i Man`类型1 类型2 类型3的变量可以赋值给接口类型的变量，这句话的意思是说：接口类型的变量可以保存别的类型（结构体，数组，切片，string ，int）的变量**

> 接口变量保存“别的”变量（对象）。

### 空接口

空interface(interface{})不包含任何的method，正因为如此，所有的类型都实现了空interface。空interface对于描述起不到任何的作用(因为它不包含任何的method），但是空interface在我们需要存储任意类型的数值的时候相当有用，因为它可以存储任意类型的数值。它有点类似于C语言的void*类型。

```
定义一个空接口,任何类型你要怎么定义
type a interface {
	
}
var b a 
var b interface{}
	var i int = 5
    s := "Hello world"
    b=i
    b=s
//b 可以存储任何类型的值，任何类型的值给它都不会出错。



var b  interface{
 	method1()
 	method2()
 }
 

```

**interface的变量里面可以存储任意类型的数值,那么我们怎么知道这个变量实际保存了哪个类型的变量（对象）**目前常用的有两种方法：`switch`测试、`Comma-ok`断言。

switch测试如下：

```
type Element interface{}
type List [] Element

type Person struct {
    name string
    age int 
}

//打印
func (p Person) String() string {
    return "(name: " + p.name + " - age: "+strconv.Itoa(p.age)+ " years)"
}

func main() {
    list := make(List, 3)
    list[0] = 1 //an int 
    list[1] = "Hello" //a string
    list[2] = Person{"Dennis", 70} 

    for index, element := range list{
        switch value := element.(type) {
            case int:
                fmt.Printf("list[%d] is an int and its value is %d\n", index, value)
            case string:
                fmt.Printf("list[%d] is a string and its value is %s\n", index, value)
            case Person:
                fmt.Printf("list[%d] is a Person and its value is %s\n", index, value)
            default:
                fmt.Println("list[%d] is of a different type", index)
        }   
    }   
}


```

# GO语言`Comma-ok`断言

`Comma-ok`断言的语法是：`value, ok := element.(T)`。element必须是接口类型的变量，T是普通类型。如果断言失败，ok为false，否则ok为true并且value为变量的值。来看个例子：

```
func main() {
    list := make(List, 3)
    list[0] = 1 // an int
    list[1] = "Hello" // a string
    list[2] = Person{"Dennis", 70}
for index, element := range list{
	
	if value,ok:=element.(int);ok{
	fmt.Printf("list[%d] is an int and its value is %d\n", index, value)
	}else if value,ok:=element.(int);ok{
	fmt.Printf("list[%d] is a string and its value is %s\n", index, value)
	}else if value,ok:=element.((Person);ok{
	fmt.Printf("list[%d] is a Person and its value is %s\n", index, value)
	}else {
     fmt.Printf("list[%d] is of a different type\n", index)
    }
	
}
    
    
```

### 嵌入接口

**正如`struct`类型可以包含一个匿名字段，interface也可以嵌套另外一个接口。**

**如果一个`interface1`作为`interface2`的一个嵌入字段，那么`interface2隐式`的包含了`interface1`里面的method。**

