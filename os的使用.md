# os的使用

```
// go 标准库 os.File
package main

import (
    "fmt"
    "os"
    "time"
)

func main() {
    // 获取当前目录
    dir, err := os.Getwd()
    fmt.Println(dir, err)
    
* 新建文件的目录 
  file := dir + "/new"
    
    var fh *os.File
* 这个目录文件是否存在
	fi, _ := os.Stat(file)
    if fi == nil {
    
*   得到这个文件 
		fh, _ = os.Create(file) // 文件不存在就创建
    } else {
        fh, _ = os.OpenFile(file, os.O_RDWR, 0666) // 文件存在就打开
    }

*   要写的字符串
	w := []byte("hello go language" + time.Now().String())
	
* 往文件中写入字符串
    n, err := fh.Write(w)
    fmt.Println(n, err)

* 设置下次读写位置
    ret, err := fh.Seek(0, 0)
    fmt.Println("当前文件指针位置", ret, err)

    b := make([]byte, 128)
    n, err = fh.Read(b)
    fmt.Println(n, err, string(b))
* 关闭文件
    fh.Close()
}
3. FileInfo 结构体
FileInfo用来描述一个文件对象

结构体定义:

type FileInfo interface {
    Name() string       // base name of the file
    Size() int64        // length in bytes for regular files; system-dependent for others
    Mode() FileMode     // file mode bits
    ModTime() time.Time // modification time
    IsDir() bool        // abbreviation for Mode().IsDir()
    Sys() interface{}   // underlying data source (can return nil)
}



```

func Stat(name string) (fi FileInfo, err error) // Stat 返回描述文件的FileInfo。如果指定的文件对象是一个符号链接，返回的FileInfo描述该符号链接指向的文件的信息，本函数会尝试跳转该链接
func Lstat(name string) (fi FileInfo, err error) // Lstat 返回描述文件对象的FileInfo。如果指定的文件对象是一个符号链接，返回的FileInfo描述该符号链接的信息，本函数不会试图跳转该链接。
使用示例:

```
fi, _ := os.Stat(file)
if fi == nil {
    fh, _ = os.Create(file) // 文件不存在就创建
} else {
    fh, _ = os.OpenFile(file, os.O_RDWR, 0666) // 文件存在就打开
}
```

