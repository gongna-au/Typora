# Go语言配置管理神器——Viper

Viper能够为你执行下列操作：

1. 查找、加载和反序列化`JSON`、`TOML`、`YAML`、`HCL`、`INI`、`envfile`和`Java properties`格式的配置文件。****
2. **提供一种机制为你的不同配置选项设置默认值。**
3. **提供一种机制来通过命令行参数覆盖指定选项的值。**
4. **提供别名系统，以便在不破坏现有代码的情况下轻松重命名参数。**
5. **当用户提供了与默认值相同的命令行或配置文件时，可以很容易地分辨出它们之间的区别。**

- 显示调用`Set`设置值

- 命令行参数（flag）
- 环境变量
- 配置文件
- key/value存储
- 默认值

- 默认值

**重要：** 目前Viper配置的键（Key）是大小写不敏感的。目前正在讨论是否将这一选项设为可选。

## 把值存入Viper

> ### 建立默认值

一个好的配置系统应该支持默认值。键不需要默认值，但如果没有通过配置文件、环境变量、远程配置或命令行标志（flag）设置键，则默认值非常有用。

```
viper.SetDefault("ContentDir", "content")
viper.SetDefault("LayoutDir", "layouts")
viper.SetDefault("Taxonomies", map[string]string{"tag": "tags", "category": "categories"})
```

> ### 读取配置文件

Viper需要最少知道在哪里查找配置文件的配置。Viper支持`JSON`、`TOML`、`YAML`、`HCL`、`envfile`和`Java properties`格式的配置文件。Viper可以搜索多个路径，但目前**单个Viper实例只支持单个配置文件**。**Viper不默认任何配置搜索路径**，将默认决策留给应用程序。

下面是一个如何使用Viper搜索和读取配置文件的示例。不需要任何特定的路径，但是**至少应该提供一个配置文件预期出现的路径**

```
viper.SetConfigFile("./config.yaml")
viper.SetConfigName("config") // 配置文件名称(无扩展名)
```







# 使用 Viper 在你的 `Golang` 项目中配置环境变量

假设您有一个具有许多功能的应用程序，并且每个功能都需要访问数据库。您在每个功能中配置了所有数据库信息，如`DBURL`、`DBNAME`、`USERNAME`和`PASSWORD`

我将谈谈这样做的主要缺点之一：

**安全问题：**

- 您正在输入代码中的所有信息。现在，所有未经授权的人也可以访问数据库。
- 如果您使用的是代码版本控制工具，`git`那么一旦您推送代码，您的数据库的详细信息就会公开。

## 1.`os包`

`Golang` 提供了`os`package，一种配置和访问环境变量的简单方法。

```
设置环境变量,把 value放到key里面
os.Setenv(key, value)

要获取环境变量，拿到我们这个变量的值
value := os.Getenv(key)

```



使用**`os` 包**访问 `env` 文件的简单程序流程**：**



```
package main

import (
  "fmt"
  "os"
)

//使用 os 包获取已经设置的 env 变量
func envVariable(key string) string {

  //给变量env赋值  就是把值读到变量里面 
  os.Setenv(key, "dbase")

  // 得到变量的值
  return os.Getenv(key)
}

func main() {
  // os package
  value := envVariable("name")

  fmt.Printf("os package: %s = %s \n", "name", value)
}
```

# `GoDotEnv` 包

加载`.env`文件的最简单方法是使用`godotenv`package.json 。

在项目根目录下打开终端。

```
Install required package that will be added to go.mod 
go get github.com/joho/godotenv
```

`godotenv `提供了`Load `一种加载 `env` 文件的方法。

**在项目根目录下**新建一个**`env`**文件。



<img src="https://miro.medium.com/max/416/1*A2n_8190Bh9-aRIiPJx66A.png" alt="img" style="zoom:200%;" />

从 `GoDotEnv` 包访问环境文件的用法：

<img src="https://miro.medium.com/max/700/1*k_uC81Ky9UCfwu9hyOlnIA.png" alt="img" style="zoom:200%;" />



<img src="https://miro.medium.com/max/700/1*rMan9-xMCyWtIZjbdbt0sA.png" alt="img" style="zoom:200%;" />

# 3.毒蛇包

*Viper 🐍 是一个完整的 Go 应用程序配置解决方案，包括 12-Factor 应用程序。它旨在在应用程序中工作，可以处理所有类型的配置需求和格式。从 `JSON`、`TOML`、`YAML`、`HCL`、`envfile` 和 `Java` 属性配置文件中读取*

让我们来看看代码

```
viper.SetConfigName("config") → 提供配置文件名。
viper.SetConfigPath(".") → 提供 viper 需要搜索配置文件的路径。
viper.AutomaticEnv() → 告诉 viper 查看环境变量。
viper.ReadInConfig() → 读取所有配置变量。
viper.Get("variable_name") → 首先从环境配置中返回“变量名”变量的值，如果不可用，则从配置文件中读取。
viper.SetDefault("database.dbname", "test_db") → 如果未在配置文件或环境变量中设置，则为给定变量“database.dbname”设置默认值。
```

**让我们开始配置部分：**

1) 让我们在`util`包中创建一个新文件`config.go`。`Config`在这个文件中声明一个新的类型结构。这个`Config`结构体将保存我们从文件或环境变量中读取的应用程序的所有配置变量。

<img src="https://miro.medium.com/max/439/1*vBfjWnDRgw1poSLA_Vt2Rw.png" alt="img" style="zoom:200%;" />



2) 定义好储存数据的结构体之后，接下来的问题就是：如何获取到我们需要的值，并把这些值放到我们建立的结构体之中。为了获取变量的值并将它们存储在这个结构中，我们需要使用 Viper 的解组功能。**Viper 在[底层](https://github.com/mitchellh/mapstructure)使用[`mapstructure`](https://github.com/mitchellh/mapstructure)包来解组值**，因此我们使用`mapstructure`标签来指定每个配置字段的名称。

> 我们必须使用 . `env`文件<img src="https://miro.medium.com/max/700/1*R9Bqqv5-Le7GgobGWGY0sg.png" alt="img" style="zoom:200%;" />

```
func LoadConfig(path string )(config Config ,err error){
	
	viper.AddConfigPath(path)//Viper要查找的配置文件的路径
	viper.SetConfigName("app") //Viper要查找的配置文件的名字,无扩展
	viper.SetConfigFile("yml")//Viper要查找的配置文件的格式（后缀），可以是JSON，也可以是XML
	viper.AutomaticEnv()//在Viper查找到文件之后，做的就只是从文件中读取值。但是viper.AutomaticEnv()做到的不仅仅是这些，它先从环境中读取值，如果读取到的值和配置文件中读到的值重复，那么环境中读到的值将覆盖在文件中读到的值。
	err=viper.ReadInConfig()//然后才开始从配置文件中读取值
	if err!=nil{
		return 
	}
	//如果正常读取到值，要做的就是把读到的值放到我们定义好的结构体中
	
	err=viper.Unmarshal(&config)
	
	return 
}

```

- 首先，我们调用`viper.AddConfigPath()`告诉 Viper 配置文件的位置。在这种情况下，位置由输入路径参数给出。

- 接下来，我们调用`viper.SetConfigName()`告诉 Viper 查找具有特定名称的配置文件。我们的配置文件是`app.env`，所以它的名字是`app`.

- 我们还`env`通过调用`viper.SetConfigFile()`并传入`env`. 如果需要，您也可以在此处使用 `JSON、`XML 或任何其他格式，只需确保您的配置文件具有正确的格式和扩展名。(写一些函数对扩展名进行判断)

- 现在，除了从文件中读取配置之外，我们还希望 viper 从环境变量中读取值。所以我们调用`viper.AutomaticEnv()`告诉 viper 自动覆盖它从配置文件中读取的值，如果它们存在，则使用相应的环境变量的值。

- 之后，我们调用`viper.ReadInConfig()`开始读取配置值。如果`error`不是， nil ，那么我们只需返回它。

- 否则，我们调用`viper.Unmarshal()`将值解组到目标`config`对象中。最后，只要返回`config`对象和发生的任何错误。

- 至此，`load config`功能基本就完成了。现在我们可以在`main.go`文件中使用它。

- > 没有参数的 `return` 语句返回各个返回变量的当前值。这种用法被称作“裸”返回。

**在此之前让我们建立数据库连接：**

<img src="https://miro.medium.com/max/700/1*XYfz6VegpsbpC-jcmnOOkg.png" alt="img" style="zoom:200%;" />

然后在`main()`函数中，我们调用`util.LoadConfig()`并传入`"."`这里，意思是当前文件夹，因为我们的配置文件`app.env`和这个`main.go`文件在同一个位置。

