# go 语言的继承

Go语言提供了继承，但是采用了组合的语法，我们将其称为匿名组合，例如：

## 匿名组合

```

type Base struct {
    name string
}
 
func (base *Base) Set(myname string) {
    base.name = myname
}
 
func (base *Base) Get() string {
    return base.name
}
 
type Derived struct {
    Base //Derived类型继承了Base类型，并改写了Get()方法，在Derived对象（实例化的变量）调用Set()方法，会加载基类对应的方法；而调用Get()方法时，加载派生类改写的方法
    age int 
}
 
func (derived *Derived) Get() (nm string, ag int) {
    return derived.name, derived.age
}
 
 
func main() {
    b := &amp;Derived{}
 
    b.Set("sina")
    fmt.Println(b.Get())
}

```

但是： Base 类型与Derived类型如果包含相同的成员名

```

type Base struct {
    name string
    age int
}
 
func (base *Base) Set(myname string, myage int) {
    base.name = myname
    base.age = myage
}
 
type Derived struct {
    Base
    name string
}
 
func main() {
    b := &Derived{}
 
    b.Set("sina", 30)
    fmt.Println("b.name =",b.name, "\tb.Base.name =", b.Base.name)
    fmt.Println("b.age =",b.age, "\tb.Base.age =", b.Base.age)
}
//
// b.name =    b.Base.name = sina
// b.age = 30  b.Base.age = 30
```

这个例子，其实也是容易理解的。因为派生类（Derived）并没有Set方法，所以他会继承Base()的Set方法。