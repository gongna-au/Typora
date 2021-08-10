# gin.HandlerFunc学习

> ```
>  func(c *gin.Context) {}
> ```
>
> 这个就是gin.HandlerFunc（处理函数）
>
> 具体要在里面做什么？当前端向我们索要信息时，我们需要把数据返回给前端。这个函数就是用来实现这个功能

具体怎么实现？

```
func main() {
    r := gin.Default()

    r.GET("/someJSON", func(c *gin.Context) {
    	//data就是我们要响应的数据，这里为了简化，我们采用初始化数据的方式，实际上的数据我们要从前端获得并把它绑定到我们需要的数据结构，可以是绑定到切片，也可以是绑定到结构体切片，也可以是map类型，具体看你要返回的数据是什么，对于唯一性数据，我们可以用一个结构体，但是如果要返回很多信息，我们可以是结构体切片。
        data := map[string]interface{}{
            "lang": "GO语言",
            "tag":  "<br>",
        }

        //调用函数c.AsciiJSON（）输出状态码和数据就结束了，具体的输出如下：
        //{"lang":"GO\u8bed\u8a00","tag":"\u003cbr\u003e"}
        //c.AsciiJSON可以生成只包含 ASCII 字符的 JSON 格式数据，对于非 ASCII 字符会进行转义。
        //
        c.AsciiJSON(http.StatusOK, data)
    })

    // 监听并在 0.0.0.0:8080 上启动服务
    r.Run(":8080")
}


```

再来看一个例子：

```
func main() {
    r := gin.Default()

    // 提供 unicode 实体
    r.GET("/json", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "html": "<b>Hello, world!</b>",
        })
    })

    // 提供字面字符
    r.GET("/purejson", func(c *gin.Context) {
        c.PureJSON(200, gin.H{
            "html": "<b>Hello, world!</b>",
        })
    })

    // 监听并在 0.0.0.0:8080 上启动服务
    r.Run(":8080")
}


```





# gin对各种请求中的参数的处理





## Get参数的获取

#### 1.从路由中获取参数

```
Get请求示例 ：
/getUserInfo?id=1
```

> 因为Get请求一般是在path中，也就是我们常说的路径参数Path

获取请求参数所用函数：

```
func (c *gin.Context) Query(key string) string
func (c *gin.Context) DefaultQuery(key string, defaultValue string) string
func (c *gin.Context) GetQuery(key string) (string, bool)


```

示例：

```
func Handler(c *gin.Context){
    //获取id参数，通过 Query 获取参数，返回string类型，没有则为空串
    idStr := c.Query("id")
    
    //获取 id 参数，通过 DefaultQuery 获取参数，返回string类型，没有则为defaultValue
    defaultValue := "xxx"
    idStr := c.DefaultQuery("id", defaultValue)
    
    //获取 id 参数，通过 GetQuery 获取参数，返回string类型，第二个参数，可判断是否存参数
    idStr, ok := c.GetQuery("id")
    if ok{
        fmt.Println("存在")
    }else{
        fmt.Println("不存在")
    }
}

```



Get请求示例：

```
/getUserInfo/:id/		--  /getUserInfo/1/
```

参数的处理:

```
idStr := c.Param("id")
var id int
var err error
if idStr!= "" {
        id, err = strconv.Atoi(idStr)
        if err != nil {
            ginUtil.SendBadRequest(c, "cannot parse: id")
            return
        }
    }

```

## Post参数的获取

#### 1.从表单中获取数据

> post请求，传输的数据是会在body里面的

> 获取Post请求参数的常用函数

```
func (c *gin.Context) PostForm(key string) string
func (c *gin.Context) DefaultPostForm(key string, defaultValuen string) string
func (c *gin.Context) GetPostForm(key string) (string, bool)

```

```
//示例：
func Hanlder(c *gin.Context){
    //从表单中获取，没有则为空""
    idStr := c.PostForm("id")
    
    //从表单中获取，没有则为默认值 "123"
    idStr := c.DefaultPostForm("id", "123")
    
    //从表单中获取，并且判断是否存在
    idStr, ok := c.GetPostForm("id")
    if !ok {
        // 参数不存在
    }
}

```

#### 2.请求体中的参数

```
body:
{
    "name":"stefan",
    "hobby":"soccer"
}
```

> 不管是GET、POST、PATCH、PUT请求，对于请求体中的参数处理基本相同**（DELETE如果想处理请求体中的数据，就要使用POST请求处理)**



解析Json参数常用的三种方式，具体视情况而定：

```
func (c *Context) BindJSON(obj interface{}) error {
    return c.MustBindWith(obj, binding.JSON)
}
//内部传递了一个binding.JSON来解析
• BindJson() 返回错误，并写400的状态码




func (c *Context) ShouldBindJSON(obj interface{}) error {
    return c.ShouldBindWith(obj, binding.JSON)
}

• ShouldBindJSON() 只会返回错误信息，不会写400的错误状态码


func (c *Context) ShouldBindWith(obj interface{}, b binding.Binding) error {
    return b.Bind(c.Request, obj)
}
• ShouldBindWith() 根据b的类型去绑定json


示例：
func method(c *gin.Context){
    type Req struct{
        Name string `json:"name" binding:"required"`
        Hobby string `json:"hobby" binding:"required"`
    }
    var r Req
    // 选择那种解析方式具体情况来定
    if err := c.BindJson(&r); err != nil {
        //处理错误
    }
    //通过 r.Name     r.Hobby 使用数据
}


```

## 模型绑定

gin提供了模型绑定，方便参数的规范化，简单来说，模型绑定就是把参数解析出来，放在你定义好的结构体里面。模型绑定的好处如下

- 规范化数据
- 能够将string数据解析为你希望的类型，比如 `uint32`
- 能够使用参数验证器

### 最常使用的模型绑定方法

gin 对模型绑定出错的处理分了两个大类：

- `Bind*`方法，以及`MustBindWith`方法 出错会将返回code置为`400`
- `ShouldBind*` 方法，出错不会设置返回code，可以自己控制返回的code，一般来说，直接调 `ShouldBind`方法就行了，它会自动判断 `Content-Type` 选择相应的绑定

#### Query Param 绑定（这种就是把从路径中获取到的参数进行绑定）

请求为 `curl 'localhost:8080/send?a=haha&b=123'`, go代码如下

```
g.GET("/send", func(ctx *gin.Context) {
    type Param struct {
        A string `form:"a" binding:"required"`
        B int    `form:"b" binding:"required"`
    }

    param := new(Param)
    if err := ctx.ShouldBind(param); err != nil {
        ctx.JSON(400, gin.H{
            "err": err.Error(),
        })
        return
    }

    ctx.JSON(200, gin.H{
        "Content-Type": ctx.ContentType(),
        "a":            param.A,
        "b":            param.B,
    })
})

// output: {"Content-Type":"","a":"haha","b":123}
```

#### Query 与 Form Param 同时绑定

请求为 `curl -XPOST 'localhost:8080/send?a=haha' -d "b=2&c=3"`, go代码如下

```
g.POST("/send", func(ctx *gin.Context) {
    type Param struct {
        A string `form:"a" binding:"required"`
        B int    `form:"b" binding:"required"`
        C int    `form:"c" binding:"required"`
    }

    param := new(Param)
    if err := ctx.ShouldBind(param); err != nil {
        ctx.JSON(400, gin.H{
            "err": err.Error(),
        })
        return
    }

    ctx.JSON(200, gin.H{
        "a": param.A,
        "b": param.B,
        "c": param.C,
    })
})

// output: {"a":"haha","b":2,"c":3}
```

可以看到，Query 和 Form 参数都是用的 `form` 这个tag

#### Path 路径参数绑定

上面看到了，Query 和 Form 是可以绑定到一个结构体当中，但是路径参数就只能单独进行绑定了（如果不需要使用参数验证，则直接用 `ctx.Param(key)`方法即可），需要单独绑定到一个结构体当中, 使用`ctx.ShouldBindUri()` 这个方法进行绑定。

请求为 `curl 'localhost:8080/send/haha'`, go代码如下



**强制绑定**

- `func (c *Context) MustBindWith(obj interface{}, b binding.Binding) error` 通用的强制绑定方法，出错则置返回code为400，一般不直接用此方法
- `func (c *Context) Bind(obj interface{}) error` 调用 `MustBindWith` 自动根据请求类型来判断绑定
- `func (c *Context) BindHeader(obj interface{}) error` 调用 `MustBindWith` 绑定请求头，tag使用`header`
- `func (c *Context) BindJSON(obj interface{}) error` 调用 `MustBindWith` 绑定json，tag使用`json`
- `func (c *Context) BindQuery(obj interface{}) error` 调用 `MustBindWith` 绑定 Query Param，tag使用`form`
- `func (c *Context) BindUri(obj interface{}) error` 调用 `MustBindWith` 绑定Path路径参数，tag使用`uri`
- `func (c *Context) BindXML(obj interface{}) error` 调用 `MustBindWith` 绑定xml，tag使用`xml`
- `func (c *Context) BindYAML(obj interface{}) error` 调用 `MustBindWith` 绑定yaml，tag使用`yaml`





> 这里说一个需要注意的问题，如果是数据存储于 `Body` 里面的，gin是封装的`标准库`的http，而 Body 是`io.ReadCloser` 类型的，只能读取一次，之后就关闭，内容**只允许读一次**，也就是说，上述的 Bind 凡是读 Body 的，都不能再读第二次，这个可以用其他办法解决，这里暂且只说一个，那就是
> `func (c *Context) ShouldBindBodyWith(obj interface{}, bb binding.BindingBody) (err error)` 方法，这个方法允许调用多次，因为它将内容暂时存在了 `gin.Context` 当中，比如绑定json如下代码所示:
> `ctx.ShouldBindBodyWith(&objA, binding.JSON)`









# **接口类型的方法集**



```
type I interface{
	F()
    G()
}
type T struct{


} 
func (T) F(){}
func (T) G(){}
func (T) H(){}
var i I=T{}
// error: i.H undefined (type I has no field or method H)
}


上面的例子中，不允许调用 T 类型的变量 i 上的方法。仅有来自接口的接口类型值方法属于接口类型的方法集。（方法集是静态的，当分配不同类型的值时不会改变）。 要调用 H 方法，需要先使用类型断言:
t, ok := i.(T)
if !ok {
    log.Fatal("Type assertion failed")
}
t.H()

```



# 非接口类型的方法集



```go
type T struct{}
func (T) F() {}
func (T) G() {}

type U struct{}
func (U) H() {}
func main() {
    t := T{}
    t.F()
    t.G()
    t.H() // error: t.H undefined (type T has no field or method H)
}

```



当处理 T 类型的指针时, 它的方法集由接收器为 T 或 *T 的方法组成:

```
type T struct{}
func (T) F() {}
func (T) G() {}
func (*T) H() {}
func main() {
    t := &T{}
    t.F()
    t.G()
    t.H()
}
```

