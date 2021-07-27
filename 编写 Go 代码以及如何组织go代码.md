# 编写 Go 代码

## 1)如何组织go代码？

> Go 程序被组织成包。**包是被编译在同一个目录中的源文件的集合**

"**包是同一个目录下源文件的集合**"=="**一堆源文件集合在同一个目录下，这个目录就叫做包**"

"**在一个源文件中定义的函数的类型，方法，变量，常量对于同一个目录（包）下的其他源文件是可见的。**"

> 一个存储库包含一个或多个模块。一个模块是被一起发布有关围棋包的集合。

"**模块是包的集合**"=="**一堆包集合在同一个目录下，这个目录就是一个模块**"

Go 存储库通常只包含一个模块，位于存储库的根目录。一个名为`go.mod`there的文件声明了模块路径：模块中所有包的导入路径前缀，该模块在包含其`go.mod`文件的目录中包含包以及该目录的子目录，直到包含另一个`go.mod`文件（如果有）的下一个子目录 。

> 请注意，在构建代码之前，您无需将代码发布到远程存储库。模块可以在本地定义而不属于存储库。然而，组织你的代码就像你有一天会发布它是一个好习惯。

每个模块的路径不仅用作其包的导入路径前缀，而且还指示`go`命令应该在哪里下载它。例如，为了下载模块`golang.org/x/tools`，该`go`命令将查询由`https://golang.org/x/tools`（[在此处](https://golang.org/cmd/go/#hdr-Relative_import_paths)详细描述）指示的存储库 。

一个导入路径是用来导入包的字符串。包的导入路径是其模块路径与其在模块内的子目录相连。例如，模块`github.com/google/go-cmp`在目录中包含一个包`cmp/`。该包的导入路径是 `github.com/google/go-cmp/cmp`. 标准库中的包没有模块路径前缀。

## 2)步骤

要编译和运行一个简单的程序，首先选择一个**模块路径**（我们将使用 `example.com/user/hello`）并**创建一个`go.mod`声明它的文件**：



```
$ mkdir hello # 或者，如果它已经存在于版本控制中，则克隆它。
$ cd 你好
$ go mod init example.com/user/hello
go：创建新的 go.mod：模块 example.com/user/hello
$ cat go.mod
模块example.com/user/hello
```

Go 源文件中的第一条语句必须是 . 可执行命令必须始终使用 . `package name` `package main`

接下来，`hello.go`在该目录中创建一个包含以下 Go 代码的文件：

```
package main 

import "fmt" 

func main() { 
	fmt.Println("Hello, world.") 
}
```



现在您可以使用该`go`工具构建和安装该程序：

```
$ go install example.com/user/hello
$
```

此命令构建`hello`命令，生成可执行二进制文件。然后将该二进制文件安装为`$HOME/go/bin/hello`（或者，在 Windows 下，`%USERPROFILE%\go\bin\hello.exe`）。

安装目录由`GOPATH` 和`GOBIN` [环境变量控制](https://golang.org/cmd/go/#hdr-Environment_variables)。如果`GOBIN`设置，二进制文件将安装到该目录。如果`GOPATH`设置，二进制文件将安装到列表中`bin`第一个目录的子目录`GOPATH`。否则，二进制文件将安装到`bin`默认`GOPATH` (`$HOME/go`或`%USERPROFILE%\go`)的子目录中。



您可以使用该`go env`命令为将来的`go`命令可移植地设置环境变量的默认值：

```
$ go env -w GOBIN=/somewhere/else/bin 
$
```

要取消设置先前由 设置的变量`go env -w`，请使用`go env -u`：

```
$ go env -u GOBIN 
$
```

`go install`在包含当前工作目录的模块的上下文中应用之类的 命令。如果工作目录不在`example.com/user/hello`模块内，`go install`可能会失败。

为方便起见，`go`命令接受相对于工作目录的路径，如果没有给出其他路径，则默认为当前工作目录中的包。所以在我们的工作目录中，以下命令都是等价的：

```
$ go install example.com/user/hello

$ go install .

$ go install 
```

接下来，让我们运行该程序以确保其正常工作。为了更加方便，我们将安装目录添加到我们的目录中，`PATH`以便轻松运行二进制文件：

```
# Windows users should consult https://github.com/golang/go/wiki/SettingGOPATH
# for setting %PATH%.
$ export PATH=$PATH:$(dirname $(go list -f '{{.Target}}' .))
$ hello
Hello, world.
$
```

如果您使用的是源代码控制系统，现在是初始化存储库、添加文件和提交第一个更改的好时机。同样，这一步是可选的：您不需要使用源代码控制来编写 Go 代码。

```
$ git init
Initialized empty Git repository in /home/user/hello/.git/
$ git add go.mod hello.go
$ git commit -m "initial commit"
[master (root-commit) 0b4507d] initial commit
 1 file changed, 7 insertion(+)
 create mode 100644 go.mod hello.go
$
```

该`go`命令通过请求相应的 HTTPS URL 并读取嵌入在 HTML 响应中的元数据来定位包含给定模块路径的存储库（请参阅 参考资料 `go help importpath`）。许多托管服务已经为包含 Go 代码的存储库提供了元数据，因此让您的模块可供其他人使用的最简单方法通常是使其模块路径与存储库的 URL 匹配。

## 3)如何从你的模块导入包

让我们编写一个`morestrings`包并从`hello`程序中使用它。首先，为名为 的包创建一个目录`$HOME/hello/morestrings`，然后 `reverse.go`在该目录中创建一个 包含以下内容的文件：

```
// Package morestrings implements additional functions to manipulate UTF-8
// encoded strings, beyond what is provided in the standard "strings" package.
package morestrings

// ReverseRunes returns its argument string reversed rune-wise left to right.
func ReverseRunes(s string) string {
	r := []rune(s)
	for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
		r[i], r[j] = r[j], r[i]
	}
	return string(r)
}
```

> 因为我们的`ReverseRunes`函数以大写字母开头，所以它被[导出](https://golang.org/ref/spec#Exported_identifiers)，并且可以在导入我们`morestrings` 包的其他包中使用。
>
> 让我们测试包是否编译`go build`：

```
$ cd $HOME/hello/morestrings
$ go build
$
```

这不会产生输出文件。相反，它将编译的包保存在本地构建缓存中。

确认`morestrings`包构建完成后，让我们从`hello`程序中使用它。为此，请修改您的原始文件 `$HOME/hello/hello.go`以使用 morestrings 包：

```
package main 

import ( 
	"fmt" "example.com/user/hello/morestrings" 
) 
func main() { 
	fmt.Println(morestrings.ReverseRunes("!oG ,olleH")) 
}
```


安装`hello`程序：

```
$ go install example.com/user/hello
```

运行新版本的程序，您应该会看到一条新的反向消息：

```
$ hello
Hello, Go!
```

## 从远程模块导入包 

`mp/cmp`在您的程序中使用：

```
package main 

import ( 
	"fmt" 

	"example.com/user/hello/morestrings" 
	"github.com/google/go-cmp/cmp" 
) 

func main() { 
	fmt.Println(morestrings.ReverseRunes("!oG ,olleH ")) 
	fmt.Println(cmp.Diff("Hello World", "Hello Go")) 
}
```

**现在您依赖于外部模块，您需要下载该模块并将其版本记录在您的`go.mod`文件中。该`go mod tidy`命令为导入的包添加缺少的模块要求，并删除不再使用的模块的要求。**

```
$ go mod tidy
go: finding module for package github.com/google/go-cmp/cmp
go: found github.com/google/go-cmp/cmp in github.com/google/go-cmp v0.5.4
$ go install example.com/user/hello
$ hello
Hello, Go!
  string(
- 	"Hello World",
+ 	"Hello Go",
  )
$ cat go.mod
module example.com/user/hello

go 1.16

require github.com/google/go-cmp v0.5.4
$
```

模块依赖会自动下载到环境变量`pkg/mod` 指示的目录的子目录中`GOPATH`。给定版本模块的下载内容在该版本的所有其他模块之间共享`require`，因此该`go`命令将这些文件和目录标记为只读。要删除所有下载的模块，您可以将`-modcache`标志传递给`go clean`：

```
$ go clean -modcache
$

```

## 测试

Go 有一个由`go test` 命令和`testing`包组成的轻量级测试框架。

您可以通过创建一个名称以`_test.go` 包含以`TestXXX`signature 命名的函数的文件来编写测试`func (t *testing.T)`。测试框架运行每个这样的函数；如果函数调用失败函数，例如`t.Error`或 `t.Fail`，则认为测试失败。

`morestrings`通过创建`$HOME/hello/morestrings/reverse_test.go`包含以下 Go 代码的文件 向包 添加测试。

```
package morestrings 

import "testing" 

func TestReverseRunes(t *testing.T) { 
	case := []struct { 
		in, want string 
	}{ 
		{"Hello, world", "dlrow ,olleH"}, 
		{"Hello, 世界", "界世,olleH"}, 
		{"", ""}, 
	} 
	for _, c := range cases { 
		got := ReverseRunes(c.in) 
		if got != c.want { 
			t.Errorf("ReverseRunes( %q) == %q, 想要 %q", c.in, got, c.want) 
		} 
	} 
}
```

然后运行测试`go test`：

```
$ go test 
PASS 
ok example.com/user/morestrings 0.165s 
$
```

```
$ go test 
PASS 
ok example.com/user/morestrings 0.165s 
$
```