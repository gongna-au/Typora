# 使用 Go 和 Gin 开发 RESTful API

> Gin 简化了与构建 Web 应用程序（包括 Web 服务）相关的许多编码任务。所以我们可以使用 Gin 来进行1.路由请求、2.检索请求详细信息 3.编组 JSON 获取响应。



## 1.设计 API 

我们先来构建一个API，这个API是用来进行对一个销售专辑的商店的访问（这里的访问，慢慢体会）。

所以我们需要提供一个端点，通过这个端点，客户端可以通过这个端点，为用户获取专辑信息，或者可以为用户添加专辑。

这个端点是什么，我们先来举个例子：



/albums

- `GET`- Get a list of all albums, returned as JSON.
- `POST` – Add a new album from request data sent as JSON.

/albums/:id

- `GET` – Get an album by its ID, returning the album data as JSON.

Next, you'll create a folder for your code.



以上就是我们创建的端点。

接下来就是创建文件夹并开始编写代码

## 2.创建文件夹

- 使用命令提示符为您的代码创建一个名为 web-service-gin 的目录。

```
$ mkdir web-service-gin
$ cd web-service-gin
```

- 创建一个模块，在其中管理我们的依赖项。运行 go mod init 命令，为其*指定代码所在模块的路径*。

>  go mod init 最开始就是为了指定代码所在的模块的路径。

```
$ go mod init example.com/web-service-gin
go: creating new go.mod: module example.com/web-service-gin
```

此命令创建一个 go.mod 文件，您添加的依赖项将在其中列出以进行跟踪。有关更多信息，请务必参阅 [管理依赖项](https://golang.org/doc/modules/managing-dependencies)。



**接下来，我们就要设计用于处理数据的数据结构。**

## 3.创建数据

我们一般是api 与数据库进行交互，但是最开始尝试的时候，为了简单，我们可以把数据存在内存里，然后与API进行交互。但是！！值得注意的是：把数据存在内存，意味着停止服务器时，专辑集（数据）都会丢失，然后在启动时重新创建。

#### 1)编写代码

​	1.使用我们文本编辑器，在 web-service 目录中创建一个名为 main.go 的文件。我们将在这个文件中编写 Go 代码。

​	2.在 main.go 文件的顶部，粘贴以下包声明。

```
	package main
```

​	3.在包声明下方，粘贴以下`album`结构声明 。您将使用它在内存（数据库）中存储专辑数据。

​		还有不要忘记——结构标记！！

​		什么是结构标记呢？例如json:"artist" 这句话就指定了当结构的内容**序列化为JSON**时，字段的名称应该叫什么。我们在结构体中指定了Price  字段，通过后面的 `json:"price"`，使得结构体的内容序列化为JSON时，`Price`就变成了`price`

​		值得注意的是：如果没有结构标记，那么JSON将会使用结构体的大写字段名称，这种一般不常见，为了和前端的兄弟交互起来方便，还是使用结构标记，变成小写。

```
// album represents data about a record album.
type album struct {
    ID     string  `json:"id"`
    Title  string  `json:"title"`
    Artist string  `json:"artist"`
    Price  float64 `json:"price"`
}
```

​	4.在您刚刚添加的结构声明下方，粘贴以下`album`结构片段， 其中包含一些我们用于开始的数据（这里不涉及与数据库的交互）

```
// albums slice to seed record album data.
var albums = []album{
    {ID: "1", Title: "Blue Train", Artist: "John Coltrane", Price: 56.99},
    {ID: "2", Title: "Jeru", Artist: "Gerry Mulligan", Price: 17.99},
    {ID: "3", Title: "Sarah Vaughan and Clifford Brown", Artist: "Sarah Vaughan", Price: 39.99},
}

//注意数据与类型的匹配，记得我最开始写时就经常写错，把时间写成了string 真正的时间应该是data类型，造成postman测试报错
```



接下来，就是编写代码来实现我们的第一个端点

#### 2)编写处理程序来返回所有的项目

当客户端在 发出`GET`请求时`GET /albums`，前端需要你将数据以 JSON 形式返回。

所以，你将这么写：

- 先写我们响应给前端的逻辑
- 然后把前端的请求路径映射到我们响应的逻辑

注意，代码执行时恰恰与这个相反，代码执行时，一般是：

- 前端的请求路径映射到我们响应的逻辑

- 执行逻辑

  

1.**编写代码**

---1在上一节中添加的结构体代码下，粘贴以下代码以获取专辑列表：

```
func getAlbums(c *gin.Context) {//定义一个函数，函数需要的参数是
    c.IndentedJSON(http.StatusOK, albums)
}

//这段代码就是通过getAlbums函数从album结构切片创建 JSON ，将 JSON 写入响应。

//简单的说就是：从数据创建JSON,然后把JSON写入响应

//func (*Context) IndentedJSON 

//func (c *Context) IndentedJSON(code int, obj interface{})
//IndentedJSON 将给定的结构序列化为漂亮的 JSON（缩进 + 结束线）到响应正文中。它还将 Content-Type 设置为“application/json”。警告：我们建议仅将其用于开发目的，因为打印漂亮的 JSON 会消耗更多 CPU 和带宽。改用 Context.JSON() 。

//func (*Context) JSON 

//func (c * Context ) JSON(code int , obj interface{})
//JSON 将给定的结构作为 JSON 序列化到响应正文中。它还将 Content-Type 设置为“application/json”。



```

- 编写一个带`gin.Context` 参数的函数,这里的`getAlbums`请注意，我们可以为该函数指定任何名称——Gin 和 Go 都不需要特定的函数名称格式。

  `gin.Context`是Gin最重要的部分。它携带请求详细信息、验证和序列化 JSON 等。（尽管名称相似，但这与 Go 的内置 [`context`](https://golang.org/pkg/context/)包不同。）

  ```
  -------这个是gin.Context------
  type Context struct {
  	Request *http.Request
  	Writer  ResponseWriter
  
  	Params Params
  
  	// Keys is a key/value pair exclusively for the context of each request.
  	// Keys 是专用于每个请求上下文的键/值对
  	Keys map[string]interface{}
  
  	// Errors is a list of errors attached to all the handlers/middlewares who used this context.
  	// Errors 是附加到所有使用此上下文的处理程序/中间件的错误列表。
  	Errors errorMsgs
  
  	// Accepted defines a list of manually accepted formats for content negotiation.
  	// Accepted 定义了手动接受的内容协商格式列表。
  	Accepted []string
  	// contains filtered or unexported fields
  	// 包含过滤或未导出的字段
  }
  ```

  上下文是 gin 中最重要的部分。例如，它允许我们在中间件之间传递变量、管理流程、验证请求的 JSON 并呈现 JSON 响应。

- 调用[`Context.IndentedJSON`](https://pkg.go.dev/github.com/gin-gonic/gin#Context.IndentedJSON) 以将结构序列化为 JSON 并将其添加到响应中。该函数的第一个参数是我们要送给客户端的 HTTP 状态代码。在这里，[`StatusOK`](https://pkg.go.dev/net/http#StatusOK) 从`net/http`包中传递常量以指示`200 OK`

  请注意，我们可以替换 为发送更紧凑的 JSON，调用`Context.IndentedJSON`替换为调用 [`Context.JSON`](https://pkg.go.dev/github.com/gin-gonic/gin#Context.JSON)。在实践中，缩进形式在调试时更容易使用，并且大小差异通常很小。



--2.在 main.go 的顶部附近，就在`albums`切片声明的下方，粘贴下面的代码以将处理程序函数分配给端点路径。

```
func main() {
    router := gin.Default()
    router.GET("/albums", getAlbums)

    router.Run("localhost:8080")
}
```

`router.GET("/albums", getAlbums)`这会建立一个关联，在该关联中`getAlbums`处理对`/albums`端点路径的请求 。

- 值得注意的是：使用了 Default.

- 使用该[`GET`](https://pkg.go.dev/github.com/gin-gonic/gin#RouterGroup.GET) 函数将`GET`HTTP 方法和`/albums`路径与处理程序函数相关联。

  > 也就是说：通过router.GET("/albums", getAlbums)这一句代码，把前端的请求方法——GET，与我们设置的端点——`/albums`路径，还有这个路径下的处理逻辑——getAlbums三者联系起来

值得注意的一点是：我们传递的是——getAlbums，不是getAlbums（），前者意味着传递函数的功能，后者意味着传递函数的结果

- 使用该[`Run`](https://pkg.go.dev/github.com/gin-gonic/gin#Engine.Run) 功能将路由器连接到一个`http.Server`并启动服务器。

--3 在 main.go 的顶部附近，就在包声明的下方，导入支持刚刚编写的代码所需的包。

第一行代码应该是这样的；

```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)
```

--4 保存main.go



#### 3)运行代码

1.开始将 Gin 模块作为依赖项进行跟踪

在命令行中，使用[`go get`](https://golang.org/cmd/go/#hdr-Add_dependencies_to_current_module_and_install_them) 将 github.com/gin-gonic/gin 模块添加为您的模块的依赖项。使用点参数表示“获取当前目录中代码的依赖项”。

```
$ go get .
go get: added github.com/gin-gonic/gin v1.7.2
```

解决并下载此依赖项以满足`import` 您在上一步中添加的声明。

2.从包含 main.go 的目录中的命令行，运行代码。使用点参数表示“在当前目录中运行代码”。

```
$ go run .
```

代码运行后，您就有了一个正在运行的 HTTP 服务器，您可以向其发送请求。

3.新的命令行窗口中，用于`curl`向正在运行的 Web 服务发出请求。

```
$ curl http://localhost:8080/albums

```

该命令应显示您为服务提供种子的数据。

```
[
        {
                "id": "1",
                "title": "Blue Train",
                "artist": "John Coltrane",
                "price": 56.99
        },
        {
                "id": "2",
                "title": "Jeru",
                "artist": "Gerry Mulligan",
                "price": 17.99
        },
        {
                "id": "3",
                "title": "Sarah Vaughan and Clifford Brown",
                "artist": "Sarah Vaughan",
                "price": 39.99
        }
]
```

现在我们已经启动了一个 API！在下一部分中，您将使用代码创建另一个端点来处理`POST`添加项目的请求。

# 编写一个处理程序来添加一个新项目

- 当客户端`POST`在 处发出请求时`/albums`，我们希望将请求正文中描述的专辑添加到现有专辑数据中。

  为此，我们将编写以下内容：

  - 将新专辑添加到现有列表的逻辑。
  - 将`POST`请求路由映射到我们的逻辑的一些代码

请注意，这与它们在运行时的执行方式相反，但我们首先添加依赖项，然后添加依赖它们的代码。

## 编写代码

### 1).添加代码以将专辑数据添加到专辑列表。

在`import`语句之后的某处粘贴以下代码。（文件的末尾是这段代码的好地方，但 Go 不强制你声明函数的顺序。）

```
// postAlbums adds an album from JSON received in the request body.
func postAlbums(c *gin.Context) {
    var newAlbum album

    // Call BindJSON to bind the received JSON to
    // newAlbum.
    //调用 BindJSON 将接收到的 JSON 绑定到newAlbum
    if err := c.BindJSON(&newAlbum); err != nil {
        return
    }

    // Add the new album to the slice.
    albums = append(albums, newAlbum)
    c.IndentedJSON(http.StatusCreated, newAlbum)
}
```

- 使用[`Context.BindJSON`](https://pkg.go.dev/github.com/gin-gonic/gin#Context.BindJSON) 将接受到的数据绑定到了结构体`newAlbum`。
- `album`将把 JSON 初始化的结构newAlbum附加到`albums` 切片。
- `201`向响应添加状态代码，以及表示您添加的专辑的 JSON。

### 2)更改我们的的`main`函数，使其包含该`router.POST`函数，如下所示：

```
func main() {
    router := gin.Default()
    router.GET("/albums", getAlbums)
    router.POST("/albums", postAlbums)

    router.Run("localhost:8080")
}
```

- `POST`将`/albums`路径中的方法与`postAlbums` 函数相关联

  > 使用 Gin，我们可以把处理程序与 HTTP 方法和路径组合相关联。通过这种方式，我们可以根据客户端使用的方法单独路由发送到单个路径的请求。

### 3)运行代码

1. 如果服务器从上一节开始仍在运行，请停止它。

2. 从包含 main.go 的目录中的命令行，运行代码。

   ```
   $ go run .
   ```

3. 从不同的命令行窗口，用于`curl`向正在运行的 Web 服务发出请求。

   ```
   $ curl http://localhost:8080/albums \
       --include \
       --header "Content-Type: application/json" \
       --request "POST" \
       --data '{"id": "4","title": "The Modern Sound of Betty Carter","artist": "Betty Carter","price": 49.99}'
   ```

   该命令应显示添加专辑的标题和 JSON。

   ```
   HTTP/1.1 201 Created
   Content-Type: application/json; charset=utf-8
   Date: Wed, 02 Jun 2021 00:34:12 GMT
   Content-Length: 116
   
   {
       "id": "4",
       "title": "The Modern Sound of Betty Carter",
       "artist": "Betty Carter",
       "price": 49.99
   }
   ```

4. 与上一节一样，使用`curl`检索专辑的完整列表，您可以使用它来确认新专辑已添加。

   ```
   $ curl http://localhost:8080/albums \
       --header "Content-Type: application/json" \
       --request "GET"
   ```

   该命令应显示专辑列表。

   ```
   [
           {
                   "id": "1",
                   "title": "Blue Train",
                   "artist": "John Coltrane",
                   "price": 56.99
           },
           {
                   "id": "2",
                   "title": "Jeru",
                   "artist": "Gerry Mulligan",
                   "price": 17.99
           },
           {
                   "id": "3",
                   "title": "Sarah Vaughan and Clifford Brown",
                   "artist": "Sarah Vaughan",
                   "price": 39.99
           },
           {
                   "id": "4",
                   "title": "The Modern Sound of Betty Carter",
                   "artist": "Betty Carter",
                   "price": 49.99
           }
   ]
   ```

   在下一部分中，您将添加代码来处理`GET`特定项目的 a。

## 编写处理程序以返回特定项目

当客户端向 发出请求时`GET /albums/[id]`，您希望返回 ID 与`id`path 参数匹配的相册。

为此，您将：

- 添加逻辑以检索请求的专辑。
- 将路径映射到逻辑。

## 编写代码

1.在`postAlbums`您在上一节中添加的函数下方，粘贴以下代码以检索特定专辑。

该`getAlbumByID`函数将提取请求路径中的ID，然后定位匹配的专辑。

```
// getAlbumByID locates the album whose ID value matches the id
// getAlbumByID 查找 ID 值与 id 匹配的相册
// parameter sent by the client, then returns that album as a response.
//客户端发送的参数，然后返回该专辑作为响应。
func getAlbumByID(c *gin.Context) {
    id := c.Param("id")

    // Loop over the list of albums, looking for
    // 遍历专辑列表，寻找
    // an album whose ID value matches the parameter.
    // ID 值与参数匹配的专辑。
    for _, a := range albums {
        if a.ID == id {
            c.IndentedJSON(http.StatusOK, a)
            return
        }
    }
    c.IndentedJSON(http.StatusNotFound, gin.H{"message": "album not found"})
}
```

以上的这段代码中：

- `c.Param("id")`用于从 URL[`Context.Param`](https://pkg.go.dev/github.com/gin-gonic/gin#Context.Param) 检索`id`路径参数。当您将此处理程序映射到路径时，您将在路径中包含参数的占位符。

- 循环`album`切片中的结构，查找`ID` 字段值与`id`参数值匹配的结构。如果找到，则将该`album`结构序列化为JSON 并将其作为带有`200 OK` HTTP 代码的响应返回。

  如上所述，现实世界的服务可能会使用数据库查询来执行此查找。

- 如果找不到专辑`404`，[`http.StatusNotFound`](https://pkg.go.dev/net/http#StatusNotFound)则返回 HTTP错误。

```
常量：
const (
 	MethodGet = "GET"
 	MethodHead = "HEAD"
 	MethodPost = "POST"
 	MethodPut = "PUT"
 	MethodPatch = "PATCH" // RFC 5789
 	MethodDelete = "DELETE"
 	MethodConnect = "CONNECT"
 	MethodOptions = "OPTIONS"
 	MethodTrace = "TRACE "
 )
常量：
StatusNotFound = 404 // RFC 7231，6.5.4
StatusOK = 200 // RFC 7231，6.3。 1
StatusContinue = 100 // RFC 7231，6.2.1
StatusSwitchingProtocols = 101 // RFC 7231，6.2.2
StatusProcessing = 102 // RFC 2518，10.1
StatusEarlyHints = 103 // RFC 8297

StatusOK = 200 // RFC 7231，6.3。 1
StatusCreated = 201 // RFC 7231 , 6.3.2
StatusAccepted = 202 // RFC 7231 , 6.3.3
StatusNonAuthoritativeInfo = 203 // RFC 7231 , 6.3.4
StatusNoContent = 204 // RFC 7231，6.3.5
StatusResetContent = 205 // RFC 7231，6.3.6
StatusPartialContent = 206 // RFC 7233，4.1
StatusMultiStatus = 207 // RFC 4918，11.1
StatusAlreadyReported = 208 // RFC 5842，7.1
StatusIMUsed = 226 // RFC 3229 , 10.4.1

StatusMultipleChoices = 300 // RFC 7231 , 6.4.1
StatusMovedPermanently = 301 // RFC 7231 , 6.4.2
StatusFound = 302 // RFC 7231，6.4.3
StatusSeeOther = 303 // RFC 7231，6.4.4
StatusNotModified = 304 // RFC 7232，4.1
StatusUseProxy = 305 // RFC 7231，6.4.5

StatusTemporaryRedirect = 307 // RFC 7231， 6.4.7
StatusPermanentRedirect = 308 // RFC 7538 , 3

StatusBadRequest = 400 // RFC 7231 , 6.5.1
StatusUnauthorized = 401 // RFC 7235 , 3.1
StatusPaymentRequired = 402 // RFC 7231，6.5.2
StatusForbidden = 403 // RFC 7231，6.5.3
StatusNotFound = 404 // RFC 7231，6.5.4
StatusMethodNotAllowed = 405 // RFC 7231，6.5.5
StatusNotAcceptable = 406 // RFC 7231 , 6.5.6
StatusProxyAuthRequired = 407 // RFC 7235 , 3.2
StatusRequestTimeout = 408 // RFC 7231 , 6.5.7
StatusConflict = 409 //RFC 7231，6.5.8
StatusGone = 410 // RFC 7231，6.5.9
StatusLengthRequired = 411 // RFC 7231，6.5.10
StatusPreconditionFailed = 412 // RFC 7232，4.2
StatusRequestEntityTooLarge = 413 // RFC 7231，6.5.11
StatusRequestURITooLong = 414 // RFC 7231 ,6.5.12 StatusUnsupportedMediaType = 415 // RFC 7231 , 6.5.13
StatusRequestedRangeNotSatisfiable = 416 // RFC 7233 , 4.4
StatusExpectationFailed = 417 // RFC 7231，6.5.14
StatusTeapot = 418 // RFC 7168，2.3.3
StatusMisdirectedRequest = 421 // RFC 7540，9.1.2
StatusUnprocessableEntity = 422 // RFC 4918，11.2
StatusLocked = 423 // RFC 4918， 11.3
StatusFailedDependency = 424 // RFC 4918 , 11.4
StatusTooEarly = 425 // RFC 8470 , 5.2. 
StatusUpgradeRequired = 426 //RFC 7231，6.5.15
StatusPreconditionRequired = 428 // RFC 6585，3个
StatusTooManyRequests = 429 // RFC 6585，4
StatusRequestHeaderFieldsTooLarge = 431 // RFC 6585，5个
StatusUnavailableForLegalReasons = 451 // RFC 7725，3

StatusInternalServerError = 500 // RFC 7231， 6.6.1
StatusNotImplemented = 501 // RFC 7231 , 6.6.2
StatusBadGateway = 502 // RFC 7231 , 6.6.3
StatusServiceUnavailable = 503 // RFC 7231，6.6.4
StatusGatewayTimeout = 504 // RFC 7231，6.6.5
StatusHTTPVersionNotSupported = 505 // RFC 7231，6.6.6
StatusVariantAlsoNegotiates = 506 // RFC 2295，8.1
StatusInsufficientStorage = 507 // RFC 4918， 11.5
StatusLoopDetected = 508 // RFC 5842 , 7.2
StatusNotExtended = 510 // RFC 2774 , 7
StatusNetworkAuthenticationRequired = 511 //RFC 6585 , 6
 )

```

2.最后，更改您的`main`使其包含对 的新调用`router.GET`，路径现在为`/albums/:id`，如以下示例所示。

```
func main() {
    router := gin.Default()
    router.GET("/albums", getAlbums)
    router.GET("/albums/:id", getAlbumByID)
    router.POST("/albums", postAlbums)

    router.Run("localhost:8080")
}
```

- 将`/albums/:id`路径与`getAlbumByID`函数关联。在 Gin 中，路径中项目前面的冒号表示该项目是路径参数。

  > `:id`在`id`前面加了冒号，表示该变量为参数

## 运行代码

1. 如果服务器从上一节开始仍在运行，请停止它。

2. 从包含 main.go 的目录中的命令行，运行代码以启动服务器。

   ```
   $ go run .
   ```

3. 从不同的命令行窗口，用于`curl`向正在运行的 Web 服务发出请求。

   ```
   $ curl http://localhost:8080/albums/2
   ```

该命令应显示您使用其 ID 的专辑的 JSON。如果未找到专辑，您将收到带有错误消息的 JSON。

```
{
        "id": "2",
        "title": "Jeru",
        "artist": "Gerry Mulligan",
        "price": 17.99
}
```