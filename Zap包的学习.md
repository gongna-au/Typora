# Zap包的学习

**我们可以定制zap的输出内容格式。**

**在定制之前，我们先来看看zap的内部结构：**

![img](https://pic2.zhimg.com/80/v2-a94beabaa278fde197a9bf4e8ff8250d_1440w.jpg)

> Create logger——创建记录器
>
> logger factory——记录器工厂
>
> Encoder creation——编码器创建
>
> Output stream creation——输出流创建
>
> Create  a core——创建一个核心（a core registers the encoder 
> outputs and the minimum level  of messages to be recoded 一个核心注册编码器,输出和要重新编码的消息的最低级别）
>
> Apply custom options——应用自定义选项
>
> Add fields ,hooks,etc——添加字段、钩子等
>
> log message ——日志消息
>
> Check the message level——检查消息级别(compare with the minimum level required by each core)与每个核心所需的最低级别进行比较
>
> Create an entry object——创建入口对象(get from a sync Pool)从同步池中获取
>
> Register the cores in it——在其中注册内核（each core agreed to record the message is embedded）每个内核同意记录消息被嵌入
>
> Encode and write the entry——编码并写入条目（use the registered encoders and write in output streams recoded in the factory）使用已注册的编码器并写入在工厂中重新编码的输出流
>
> Release the entry——释放条目（put back to the sync Pool）放回同步池



**zap是由创建logger与写log两个关键过程组成**,其中zap的核心是名为`zapcore.Core`抽象，Core是zap定义的一个log接口，正如其名，围绕着这个Core，zap提供上层log对象以及相应的方法(`zap.Logger`就组合了`zapcore.Core`)，开发者同样可以基于该接口定制自己的log包



我们一般通过`zapcore.NewCore`函数创建一个实现了`zapcore.Core`的实例，`NewCore`接收三个参数，也是Core的主要组成部分，它们如下图：

```text
                            															┌───────────────┐
                                 │               │
                                 │               │
                      ┌─────────►│     Encoder   │
                      │          │      编码器    │
                      │          │               │
                      │          └───────────────┘
┌────────────────┐    │
│                ├────┘
│                │               ┌───────────────┐
│                │               │               │
│      Core      ├──────────────►│  WriteSyncer  │
│                │               │  写同步器 	  │
│                ├─────┐         │               │
└────────────────┘     │         └───────────────┘
                       │
                       │
                       │         ┌───────────────┐
                       │         │               │
                       └────────►│  LevelEnabler │
                                 │   电平使能器    │
                                 │               │
                                 └───────────────┘
```

- Encoder是日志消息的编码器；**编码器(如何写入日志**)
- `WriteSyncer`是支持Sync方法的`io.Writer`，含义是日志输出的地方，我们可以很方便的通过`zap.AddSync`将一个`io.Writer`转换为支持Sync方法的`WriteSyncer；`（**指定日志将写到哪里去**）
- `LevelEnabler`*则是日志级别相关的参数*（**哪种级别的日志将被写入**。）

由此我们看到要定制日志的输出格式，我们的重点是Encoder(日志消息的编码器)。从大类别上分，zap内置了两类编码器，一个`ConsoleEncoder`，另一个是`JSONEncoder。` `ConsoleEncoder`更适合人类阅读，而`JSONEncoder`更适合机器处理。

zap提供的两个最常用创建Logger的函数：`NewProduction`和`NewDevelopment`则分别使用了`JSONEncoder`和`ConsoleEncoder`。两个编码器默认输出的内容对比如下：

```
// ConsoleEncoder（NewDevelopment创建)
2021-07-11T09:39:04.418+0800 INFO zap/testzap2.go:12 failed to fetch URL {"url": "localhost:8080", "attempt": 3, "backoff": "1s"}

// JSONEncoder (NewProduction创建)
{"level":"info","ts":1625968332.269727,"caller":"zap/testzap1.go:12","msg":"failed to fetch URL","url":"localhost:8080","attempt":3,"backoff":1}
```

我们可以看到两者差异巨大！`ConsoleEncoder`输出的内容跟适合我们阅读，而`JSONEncoder`输出的结构化日志更适合机器/程序处理。前面我们说了，我们封装的log包不是要做通用log包，我们无需同时支持这两大类Encoder，于是我们在上面的示例选择采用的`JSONEncoder`：

```
//示例——摘自知乎
core := zapcore.NewCore(
  zapcore.NewJSONEncoder(cfg.EncoderConfig),
  zapcore.AddSync(writer),
  zapcore.Level(level),
 )
 --------------------------------------------------------
//我们自己写的示例：
//先写zapcore.NewJSONEncoder()的参数
encoderConfig:=zapcore.EncoderConfig{
		TimeKey:        "time",
		LevelKey:       "level",
		NameKey:        "logger",
		CallerKey:      "linenum",
		MessageKey:     "msg",
		StacktraceKey:  "stacktrace",
		LineEnding:     zapcore.DefaultLineEnding,
		EncodeLevel:    zapcore.LowercaseLevelEncoder, 
		// 小写编码器
		EncodeTime:     zapcore.ISO8601TimeEncoder,     
		// ISO8601 UTC 时间格式
		EncodeDuration: zapcore.SecondsDurationEncoder, 
		EncodeCaller:   zapcore.FullCallerEncoder,      
		// 全路径编码器
		EncodeName:     zapcore.FullNameEncoder,
}

1.第一步日志分割（用到lumberjack.Logger）
	写zapcore.NewMultiWriteSyncer()的第二个参数hook

		// 日志分割
		hook := lumberjack.Logger{
		Filename:   "./logs/status.log", // 日志文件路径 ,默认 os.TempDir()
		MaxSize:    128, // 每个日志文件保存的最大尺寸 默认 100M
		MaxBackups: 5,   // 日志文件最多保存多少个备份,默认不限
		MaxAge:     30,   // 文件最多保存多少天,默认不限
		Compress:   true, // 是否压缩,默认不压缩
	}
	
2.设置日志级别
		// 设置日志级别
		atomicLevel := zap.NewAtomicLevel()
		atomicLevel.SetLevel(zap.InfoLevel)
    
3.新建一个core

	// 新建一个core
	core := zapcore.NewCore(
		// 编码器配置
		zapcore.NewJSONEncoder(encoderConfig),   
		// 打印到控制台和文件
		zapcore.NewMultiWriteSyncer(zapcore.AddSync(os.Stdout), zapcore.AddSync(&hook)),
		// 日志级别
		atomicLevel, 
	)
	
	
	
4.开启开发模式，进行堆栈跟踪
	// 开启开发模式，堆栈跟踪
    caller := zap.AddCaller()
    // 开启文件及行号
	development := zap.Development()
    // 设置初始化字段,如：添加一个服务器名称
    filed := zap.Fields(zap.String("serviceName", "server"))
    // 构造日志
	logger = zap.New(core, caller, development, filed)
	logger.Info("DefaultLogger init success")



5.整合以上代码到一个函数中初始化日志
	func init() {
		hook := lumberjack.Logger{
            Filename:   "./logs/status.log", // 日志文件路径
            MaxSize:    128,                 // 每个日志文件保存的最大尺寸 单位：M
            MaxBackups: 5,                   // 日志文件最多保存多少个备份
            MaxAge:     30,                  // 文件最多保存多少天
            Compress:   true,                // 是否压缩
		}
        encoderConfig := zapcore.EncoderConfig{
            TimeKey:        "time",
            LevelKey:       "level",
            NameKey:        "logger",
            CallerKey:      "linenum",
            MessageKey:     "msg",
            StacktraceKey:  "stacktrace",
            LineEnding:     zapcore.DefaultLineEnding,
            EncodeLevel:    zapcore.LowercaseLevelEncoder,  // 小写编码器
            EncodeTime:     zapcore.ISO8601TimeEncoder,     // ISO8601 UTC 时间格式
            EncodeDuration: zapcore.SecondsDurationEncoder, //
            EncodeCaller:   zapcore.FullCallerEncoder,      // 全路径编码器
            EncodeName:     zapcore.FullNameEncoder,
        }
        // 设置日志级别
        atomicLevel := zap.NewAtomicLevel()
        atomicLevel.SetLevel(zap.InfoLevel)
        // 新建一个ZapCore
        core := zapcore.NewCore(
        	// 编码器配置
            zapcore.NewJSONEncoder(encoderConfig),                                         			   // 打印到控制台和文件
           zapcore.NewMultiWriteSyncer(zapcore.AddSync(os.Stdout),zapcore.AddSync(&hook)), 
            // 日志级别
            atomicLevel, 
        )
		// 开启开发模式，堆栈跟踪
		caller := zap.AddCaller()
		// 开启文件及行号
		development := zap.Development()
		// 设置初始化字段,如：添加一个服务器名称
		filed := zap.Fields(zap.String("serviceName", "server"))
		// 构造日志
		logger = zap.New(core, caller, development, filed)
		
		
		
		
}


6.再写一些函数用来日志打印信息主要是调用logger.Info、logger.Fatal、logger.Debug、logger.Error。
// info level log
func Info(msg string, fields ...zap.Field) {
	logger.Info(msg, fields...)
}

// fatal level log
func Fatal(msg string, fields ...zap.Field) {
	logger.Fatal(msg, fields...)
}

// debug level log
func Debug(msg string, fields ...zap.Field) {
	logger.Debug(msg, fields...)
}

// error level log
func Error(msg string, fields ...zap.Field) {
	logger.Error(msg, fields...)
}


7.在程序结束时关闭日志Sync 是将内存中的数据保存到本地磁盘
func SyncLogger() {
	logger.Sync()
	//Sync 调用底层 Core 的 Sync 方法，刷新任何缓冲的日志条目。应用程序应注意在退出前调用 Sync。
}






7.在main函数中使用

func main(){
	 // 历史记录日志名字为：./logs/status.log，服务重新启动，日志会追加，不会删除
	 InitLogger()
	 defer SyncLogger()
	 // 强结构形式
	 logger.Info("test",
        zap.String("string", "string"),
        zap.Int("int", 3),
        zap.Duration("time", time.Second),
     )
        // 必须 key-value 结构形式 性能下降一点
    	logger.Sugar().Infow("test-",
        	"string", "string",
        	"int", 1,
        	"time", time.Second,
   

    	)
	 
	 
	 

}

```

> 使用 lumberjack 进行日志切割归档，因为 zap 本身不支持切割归档日志文件，为了添加日志切割归档功能，我们将使用第三方库 lumberjack 来实现。将 lumberjack 加入 zap logger `Lumberjack Logger` 采用以下属性作为输入:

> 		Filename : 日志文件的位置；
> 		MaxSize ：在进行切割之前，日志文件的最大大小（以MB为单位）；
> 		MaxBackups ：保留旧文件的最大个数；
> 		MaxAges ：保留旧文件的最大天数；
> 		Compress ：是否压缩/归档旧文件




 