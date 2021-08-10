# gin学习笔记——路由

> Gin是一个基于golang的net包实现的网络框架

```

1.	func main() {
2.		// Disable Console Color
3.		// gin.DisableConsoleColor()
4.
5.		// Creates a gin router with default middleware:
6.		// logger and recovery (crash-free) middleware
7.		router := gin.Default()
8.
9.		router.GET("/someGet", getting)
10.		router.POST("/somePost", posting)
11.		router.PUT("/somePut", putting)
12.		router.DELETE("/someDelete", deleting)
13.		router.PATCH("/somePatch", patching)
14.		router.HEAD("/someHead", head)
15.		router.OPTIONS("/someOptions", options)
16
17.		// By default it serves on :8080 unless a
18.		// PORT environment variable was defined.
19.		router.Run()
20.		// router.Run(":3000") for a hard coded port
}

```

> 这种写法非常的优雅。第7行新建了一个路由器；第9~15行定义了路由规则；第19行启动该路由器。如此整个服务就跑起来了。<u>向http://host/someGet发送Get请求将由getting方法处理</u>,<u>向http://host/somePost发送Post请求将由posting方法处理</u>

**我们开始分析路由器是怎么将请求和处理方法(handler)关联起来的:**

> 第7行创建的对象叫做路由器（router），但是其底层名称却是“引擎”（Engine）
>
> ```
> 
> // Default returns an Engine instance with the Logger and Recovery middleware already attached.
> 
> //router := gin.Default()下面这段代码就是gin.Default()的底层实现，返回给我们一个引擎，我们拿着这个引擎就可以把Get、Post、Put等Http的协议与我们具体的处理方法关联起来，这样访问http://host/somePost就会由posting方法处理。
> func Default() *Engine {
> 	debugPrintWARNINGDefault()
> 	engine := New()
> 	engine.Use(Logger(), Recovery())
> 	return engine
> }
> //router.GET("/user/:name", func(c *gin.Context))我们可以看到后面的函数就是我们的处理函数，类型是gin.HandlerFunc
> func main() {
>     router := gin.Default()
> 
>     // 此 handler 将匹配 /user/john 但不会匹配 /user/ 或者 /user
>     router.GET("/user/:name", func(c *gin.Context) {
>         name := c.Param("name")
>         c.String(http.StatusOK, "Hello %s", name)
>     })
> 
> }
> 
> //所以我们完全可以模仿自己这个写一个（路由）的函数 ，在这个函数里面我们我们把我们的引擎（路由）全局变量传进去，然后在里面实现路由与方法的绑定。同时我们还可以把我们写的自定义的中间件函数传进去。
> 
> Load 函数需要我们传一个以及定义好的引擎进去，然后把我们的处理函数传进去，在main函数中调用该函数就可以实现访问与处理函数的关联，然后返回一个引擎，让引擎再跑起来。
> 
> 
> func Load(g *gin.Engine, mw ...gin.HandlerFunc) *gin.Engine {
> 	// middleware
> 	g.Use(gin.Recovery())
> 	g.Use(middleware.NoCache)
> 	g.Use(middleware.Options)
> 	g.Use(middleware.Secure)
> 	g.Use(mw...)
> 	// 404 Handler.
> 	g.NoRoute(func(c *gin.Context) {
> 		c.String(http.StatusNotFound, "The incorrect API route.")
> 	})
> 	g.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))
> 	// router
> 	authRouter := g.Group("/api/v1/auth")
> 	{
> 		authRouter.POST("/register", auth.Register)
> 		authRouter.POST("/login", auth.Login)
> 	}
> 
> 	userRouter := g.Group("/api/v1/user")
> 	userRouter.Use(middleware.AuthMiddleware)
> 	{
> 		userRouter.GET("/detail/:id", user.GetUserInfo)
> 		userRouter.PUT("", user.UpdateUserInfo)
> 	}
> 
> 	ideaRouter := g.Group("/api/v1/idea")
> 	ideaRouter.Use(middleware.AuthMiddleware)
> 	{
> 		// idea
> 		ideaRouter.POST("", idea.CreateIdea)
> 		ideaRouter.DELETE("detail/:id", idea.DeleteIdea)
> 		ideaRouter.GET("/list", idea.GetIdeaList)
> 		ideaRouter.GET("/detail/:id", idea.GetIdea)
> 
> 		ideaRouter.GET("/liked", idea.GetIdeaLikedList)
> 
> 		// comment
> 		ideaRouter.POST("/detail/:id/comment", idea.CreateComment)
> 		ideaRouter.DELETE("/detail/:id/comment/:comment_id", idea.DeleteComment)
> 		ideaRouter.GET("/detail/:id/comment", idea.GetCommentList)
> 
> 		// like
> 		ideaRouter.PUT("/detail/:id/like", idea.UpdateIdeaLike)
> 		ideaRouter.PUT("/comment/:id/like", idea.UpdateCommentLike)
> 	}
> 
> 	collectionRouter := g.Group("/api/v1/collection")
> 	collectionRouter.Use(middleware.AuthMiddleware)
> 	{
> 		collectionRouter.GET("", collection.GetCollection)
> 		collectionRouter.POST("", collection.CreateCollection)
> 		collectionRouter.DELETE("", collection.DeleteCollection)
> 	}
> 
> 	searchRouter := g.Group("/api/v1/search")
> 	searchRouter.Use(middleware.AuthMiddleware)
> 	{
> 		searchRouter.GET("", search.GetSearchResult)
> 		searchRouter.DELETE("", search.DeleteHistory)
> 		searchRouter.GET("/histories", search.GetHistories)
> 	}
> 
> 	// 举报
> 	reportRouter := g.Group("/api/v1/report")
> 	reportRouter.Use(middleware.AuthMiddleware)
> 	{
> 		reportRouter.POST("", report.CreateReport)
> 	}
> 
> 	// 消息
> 	messageRouter := g.Group("api/v1/message")
> 	messageRouter.Use(middleware.AuthMiddleware)
> 	{
> 		messageRouter.GET("/tip", message.GetMessageTip)
> 		messageRouter.PUT("/readall", message.UpdateMessageReadAll)
> 		messageRouter.GET("/like", message.GetMessageForLike)
> 		messageRouter.GET("/comment", message.GetMessageForComment)
> 
> 		// 系统通知，先不做
> 		// messageRouter.GET("/notice",message.GetMessageForNotice)
> 	}
> 
> 	// The health check handlers
> 	svcd := g.Group("/sd")
> 	{
> 		svcd.GET("/health", sd.HealthCheck)
> 		svcd.GET("/disk", sd.DiskCheck)
> 		svcd.GET("/cpu", sd.CPUCheck)
> 		svcd.GET("/ram", sd.RAMCheck)
> 	}
> 
> 	return g
> }
> 
> 
> 
> 
> 然后看看我们在main函数中是怎么做的
> 
> func main() {
> 	//使用gin.new()函数创建一个引擎
> 	g := gin.New()
> 	//调用我们的Load函数来把关联路由与方法
> 	//因为我们写的Load函数是在router包里，所以我们在main函数中导入相应的包后，调用方式：router.Load(参数)
> 	g=router.Load(g,middleware.Logging(),middleware.RequestId())
> 	//然后再让router跑起来
> 	g.Run("8080")
> 	
> 	
> 
> }
> 
> 
> 
> ```
>
> 

