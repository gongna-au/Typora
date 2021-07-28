# 访问关系数据库



## 1)为您的代码创建一个文件夹

首先，为您将编写的代码创建一个文件夹。

1. 打开命令提示符并切换到您的主目录。

   在 Linux 或 Mac 上：

   ```
   $ cd
   ```

   在 Windows 上：

   ```
   C:\> cd %HOMEPATH%
   ```

   在本教程的其余部分，我们将显示 $ 作为提示。我们使用的命令也适用于 Windows。

2. 在命令提示符下，为您的代码创建一个名为 data-access 的目录。

   ```
   $ mkdir data-access
   $ cd data-access
   ```

3. 创建一个模块，您可以在其中管理将在本教程中添加的依赖项。

   运行该`go mod init`命令，为其提供新代码的模块路径。

   ```
   $ go mod init example.com/data-access
   go: creating new go.mod: module example.com/data-access
   ```

   此命令创建一个 go.mod 文件，您添加的依赖项将在其中列出以进行跟踪。有关更多信息，请务必参阅 [管理依赖项](https://golang.org/doc/modules/managing-dependencies)。

接下来，您将创建一个数据库。

## 2)设置数据库

我们将创建一个数据库，其中包含有关黑胶唱片上的老式爵士乐录音的数据

此处的代码使用[MySQL CLI](https://dev.mysql.com/doc/refman/8.0/en/mysql.html)，但大多数 DBMS 都有自己的具有类似功能的 CLI。

步骤如下：

1. 打开一个新的命令提示符。

2. 在命令行中，登录到您的 数据库，如下面的 MySQL 示例所示。

   ```
   $ mysql -u root（用户） -p
   Enter password:
   
   mysql> 
   
   $ mysql -u用户 -p密码
   例如
   $ mysql -ugongna2 -p123456
   
   
   mysql> 
   
   mysql> 
   ```

3. 在`mysql`命令提示符下，创建一个数据库。

   ```
   mysql> create database recordings;
   ```

4. 更改为您刚刚创建的数据库，以便您可以添加表。

   ```
   mysql> use recordings;
   Database changed
   ```

   

5. 在文本编辑器的 data-access 文件夹中，创建一个名为 create-tables.sql 的文件来保存用于添加表的 SQL 脚本。

6. 到文件中，粘贴以下 SQL 代码，然后保存文件。

   ```
   DROP TABLE IF EXISTS album;
   CREATE TABLE album (
     id         INT AUTO_INCREMENT NOT NULL,
     title      VARCHAR(128) NOT NULL,
     artist     VARCHAR(255) NOT NULL,
     price      DECIMAL(5,2) NOT NULL,
     PRIMARY KEY (`id`)
   );
   
   INSERT INTO album 
     (title, artist, price) 
   VALUES 
     ('Blue Train', 'John Coltrane', 56.99),
     ('Giant Steps', 'John Coltrane', 63.99),
     ('Jeru', 'Gerry Mulligan', 17.99),
     ('Sarah Vaughan', 'Sarah Vaughan', 34.98);
     
     
     //在此 SQL 代码中，您：
     	- 删除（删除）名为 的表`album`。如果您想从该表重新开始，首先执行此命令可以让您稍后更轻松地重新运行脚本。
   
   	- 创建一个`album`四列的表：`title`，`artist`，和`price`。每行的`id`值由 DBMS 自动创建。
   	- 添加带有值的三行。
   
   
   ```

   - 删除（删除）名为 的表`album`。如果您想从该表重新开始，首先执行此命令可以让您稍后更轻松地重新运行脚本。

   - 创建一个`album`四列的表：`title`，`artist`，和`price`。每行的`id`值由 DBMS 自动创建。
   - 添加带有值的三行。

7. 在`mysql`命令提示符下，运行您刚刚创建的脚本。

   您将使用`source`以下形式的命令：

   ```
   mysql> source /path/to/create-tables.sql
   ```

8. 在 DBMS 命令提示符下，使用`SELECT`语句来验证您是否已成功创建包含数据的表。

   ```
   mysql> select * from album;
   +----+---------------+----------------+-------+
   | id | title         | artist         | price |
   +----+---------------+----------------+-------+
   |  1 | Blue Train    | John Coltrane  | 56.99 |
   |  2 | Giant Steps   | John Coltrane  | 63.99 |
   |  3 | Jeru          | Gerry Mulligan | 17.99 |
   |  4 | Sarah Vaughan | Sarah Vaughan  | 34.98 |
   +----+---------------+----------------+-------+
   4 rows in set (0.00 sec)
   ```

## 3)查找并导入数据库驱动程序

> 找到并导入一个数据库驱动程序，该驱动程序会将您通过`database/sql`包中的函数发出的请求转换为数据库能够理解的请求

**''驱动程序''的功能：我们调用**`database/sql`**包中的函数发出的请求会被驱动程序转化为数据库能够理解的请求**。

```
import (
	"database/sql"
	"time"

	_ "github.com/go-sql-driver/mysql"
)

// ...

db, err := sql.Open("mysql", "user:password@/dbname")
if err != nil {
	panic(err)
}
// See "Important settings" section.
db.SetConnMaxLifetime(time.Minute * 3)
db.SetMaxOpenConns(10)
db.SetMaxIdleConns(10)
-----------------------------------------------------------

//db.SetConnMaxLifetime()在 MySQL 服务器、操作系统或其他中间件关闭连接之前，需要确保驱动程序安全地关闭连接。由于一些中间件会在 5 分钟后关闭空闲连接，我们建议超时时间小于 5 分钟。此设置也有助于负载平衡和更改系统变量。

//db.SetMaxOpenConns()强烈建议限制应用程序使用的连接数。没有推荐的限制数量，因为它取决于应用程序和 MySQL 服务器。

//db.SetMaxIdleConns()建议设置为相同db.SetMaxOpenConns()。当它小于 时SetMaxOpenConns()，连接可以比您预期的更频繁地打开和关闭。空闲连接可以通过 关闭db.SetConnMaxLifetime()。如果你想更快地关闭空闲连接，你可以db.SetConnMaxIdleTime()从 Go 1.15 开始使用。

DSN（数据源名称）:

数据源名称具有通用格式，例如PEAR DB使用它，但没有类型前缀（用方括号标记的可选部分）：
[username[:password]@][protocol[(address)]]/dbname[?param1=value1&...&paramN=valueN]

最完整形式的 DSN:
username:password@protocol(address)/dbname?param=value

除 databasename 外，所有值都是可选的。所以最小的 DSN 是：
/dbname

如果您不想预先选择数据库，请将其dbname留空：
/
这与空的 DSN 字符串具有相同的效果：

或者使用Config.FormatDSN可用于通过填充结构来创建 DSN 字符串。

Password：
密码可以由任何字符组成。逃避是没有必要的。
Protocol：
有关可用网络的详细信息，请参阅net.Dial。一般来说，您应该使用 Unix 域套接字（如果可用）和 TCP 否则以获得最佳性能。
address:
对于 TCP 和 UDP 网络，地址的形式为host[:port]. 如果port省略，将使用默认端口。如果host是文字 IPv6 地址，则必须将其括在方括号中。函数net.JoinHostPort和net.SplitHostPort以这种形式操作地址。
对于 Unix 域套接字，地址是 MySQL-Server-socket 的绝对路径，例如/var/run/mysqld/mysqld.sock或/tmp/mysql.sock。
param:
参数区分大小写！
请注意，任何true, TRUE,True或1都被接受来代表真正的布尔值。不足为奇的是，假可以被指定为任何的：false，FALSE，False或0。
allowAllFiles
Type:           bool
Valid Values:   true, false
Default:        false
allowAllFiles=true禁用文件许可名单LOAD DATA LOCAL INFILE并允许所有文件。 可能没有安全感！

allowCleartextPasswords
Type:           bool
Valid Values:   true, false
Default:        false
allowCleartextPasswords=true如果帐户需要，允许使用明文客户端插件，例如使用PAM 身份验证插件定义的插件。在某些配置中，以明文形式发送密码可能是一个安全问题。为避免密码可能被截获时出现问题，客户端应使用保护密码的方法连接到 MySQL 服务器。可能性包括TLS/SSL、IPsec 或专用网络。

allowNativePasswords
Type:           bool
Valid Values:   true, false
Default:        true
allowNativePasswords=false 不允许使用 MySQL 本机密码方法。

allowOldPasswords
Type:           bool
Valid Values:   true, false
Default:        false
allowOldPasswords=true允许使用不安全的旧密码方法。这应该避免，但在某些情况下是必要的。另请参阅old_passwords 维基页面。


charset
Type:           string
Valid Values:   <name>
Default:        none
设置用于客户端-服务器交互的字符集 ( "SET NAMES <value>")。如果设置了多个字符集（用逗号分隔），如果设置字符集失败，则使用以下字符集。例如，这可以支持utf8mb4（在 MySQL 5.5.3 中引入）并回退到utf8旧服务器 ( charset=utf8mb4,utf8)。charset不鼓励使用该参数，因为它会向服务器发出额外的查询。除非您需要回退行为，否则请collation改用。

checkConnLiveness
Type:           bool
Valid Values:   true, false
Default:        true
在支持的平台上，从连接池中检索的连接在使用前会被检查活跃度。如果检查失败，则相应的连接被标记为错误，并使用另一个连接重试查询。 checkConnLiveness=false禁用此连接的活性检查。


collation
Type:           string
Valid Values:   <name>
Default:        utf8mb4_general_ci
设置用于连接时客户端-服务器交互的排序规则。与 相比charset，collation不会发出额外的查询。如果指定的排序规则在目标服务器上不可用，则连接将失败。
服务器的有效字符集列表可使用 检索SHOW COLLATION。
utf8mb4_general_ciMySQL 5.5 支持默认排序规则 ( )。utf8_general_ci对于较旧的 MySQL，您应该使用较旧的排序规则（例如）。
不能使用字符集“ucs2”、“utf16”、“utf16le”和“utf32”的排序规则（ref）。

clientFoundRows
Type:           bool
Valid Values:   true, false
Default:        false
clientFoundRows=true 导致 UPDATE 返回匹配行数而不是更改的行数。


columnsWithAlias
Type:           bool
Valid Values:   true, false
Default:        false
当columnsWithAlias为真时，调用sql.Rows.Columns()将返回表别名和用点分隔的列名。例如：
SELECT u.id FROM users as u
将返回u.id而不仅仅是idif columnsWithAlias=true。


interpolateParams
Type:           bool
Valid Values:   true, false
Default:        false
如果interpolateParams为 true，则?调用db.Query()和db.Exec()中的占位符 ( )将插入到具有给定参数的单个查询字符串中。这减少了往返次数，因为驱动程序必须准备一个语句，使用给定的参数执行它并再次使用interpolateParams=false.

multiStatements
Type:           bool
Valid Values:   true, false
Default:        false
允许在一个查询中使用多个语句。虽然这允许批量查询，但也大大增加了 SQL 注入的风险。只返回第一个查询的结果，所有其他结果都被静默丢弃。当multiStatements使用时，?参数必须仅在第一个语句中使用。

parseTime
Type:           bool
Valid Values:   true, false
Default:        false
parseTime=true改变的输出类型DATE和DATETIME值，以time.Time代替[]byte/string 日期或日期时间等0000-00-00 00:00:00被转换成零值time.Time。

readTimeout
Type:           duration
Default:        0
I/O 读取超时。该值必须是带有单位后缀（"ms"、"s"、"m"、"h"）的十进制数，例如"30s"、"0.5m"或"1m30s"。


rejectReadOnly
Type:           bool
Valid Values:   true, false
Default:        false


rejectReadOnly=true导致驱动程序拒绝只读连接。这是针对自动故障转移期间可能出现的竞争条件，其中 mysql 客户端在故障转移后连接到只读副本。

请注意，这应该是一种相当罕见的情况，因为自动故障转移通常在主服务器关闭时发生，并且竞争条件不应该发生，除非它在故障转移开始后立即恢复联机。另一方面，当发生这种情况时，MySQL 应用程序可能会卡在只读连接上，直到重新启动。然而，重现相当容易，例如，在 AWS Aurora 的 MySQL 兼容集群上使用手动故障转移。

如果您不依赖只读事务来拒绝不应该发生的写入，则在某些 MySQL 提供程序（例如 AWS Aurora）上设置此选项对于故障转移更安全。

请注意，可以为read-only服务器返回 ERROR 1290 ，此选项将导致对该错误进行重试。然而，相同的错误编号用于其他一些情况。read-only在启用此选项时，您应该确保您的应用程序永远不会导致 ERROR 1290，除了mode。



```

### 补充：连接池和超时

连接池由 Go 的 database/sql 包管理。有关如何配置池的大小和连接多久细节留在游泳池看到`*DB.SetMaxOpenConns`，`*DB.SetMaxIdleConns`和`*DB.SetConnMaxLifetime`将在[数据库/ SQL文档](https://golang.org/pkg/database/sql/)。读，写，和拨号为每个单独的连接超时被配置成与`DSN`参数[`readTimeout`](https://github.com/go-sql-driver/mysql/#readtimeout)，[`writeTimeout`](https://github.com/go-sql-driver/mysql/#writetimeout)以及[`timeout`](https://github.com/go-sql-driver/mysql/#timeout)分别。

### 补充：`ColumnType` 支持

此驱动程序支持Go 1.8 中引入的[`ColumnType`接口](https://golang.org/pkg/database/sql/#ColumnType)[`ColumnType.Length()`](https://golang.org/pkg/database/sql/#ColumnType.Length)，但目前不支持的 。所有未签名的数据库类型名称将返回`UNSIGNED `带有`INT`, `TINYINT`, `SMALLINT`, `BIGINT`。

## `context.Context` 支持

Go 1.8 添加了`database/sql`对`context.Context`. 此驱动程序通过上下文支持查询超时和取消。有关更多详细信息，请参阅[database/sql 包中的上下文支持](https://golang.org/doc/go1.8#database_sql)。

### 补充：`LOAD DATA LOCAL INFILE` 支持

```
import "github.com/go-sql-driver/mysql"
```



文件必须通过注册`mysql.RegisterLocalFile(filepath)`（推荐）明确允许，或者必须使用 DSN 参数停用许可名单检查`allowAllFiles=true`（[*可能不安全！*](http://dev.mysql.com/doc/refman/5.7/en/load-data-local.html)）。

要使用`io.Reader`处理程序函数，必须注册`mysql.RegisterReaderHandler(name, handler)`其返回 a`io.Reader`或`io.ReadCloser`。然后，Reader 可与文件路径一起使用`Reader::<name>`。为不同的处理程序以及`DeregisterReaderHandler`当您不再需要时选择不同的名称。

### 补充：`time.Time` 支持

MySQL`DATE`和`DATETIME`值的默认内部输出类型是`[]byte`允许您将值扫描到程序中的`[]byte`,`string`或`sql.RawBytes`变量中。

但是，许多人希望将 MySQL`DATE`和`DATETIME`值扫描到`time.Time`变量中，这在 Go to`DATE`和`DATETIME`MySQL 中是逻辑等价的。您可以通过使用 DSN 参数将内部输出类型从 更改为`[]byte`来实现。您可以使用DSN 参数设置默认[位置](https://golang.org/pkg/time/#Location)。`time.Time``parseTime=true`[`time.Time`](https://golang.org/pkg/time/#Location)`loc`

### 引入：导入数据库驱动程序并开始我们的代码

> 在您的浏览器中，访问[SQLDrivers](https://github.com/golang/go/wiki/SQLDrivers) wiki 页面以确定您可以使用的驱动程序。
>
> 使用页面上的列表来确定您将使用的驱动程序。为了在本教程中访问 MySQL，您将使用 [Go-MySQL-Driver](https://github.com/go-sql-driver/mysql/)。

**需要注意的几点:**

- 注意驱动程序的包名称 - 此处为`github.com/go-sql-driver/mysql`.

- 使用您的文本编辑器，创建一个用于编写 Go 代码的文件，并将该文件保存为 main.go 文件，保存在您之前创建的数据访问目录中

- 在 main.go 中，粘贴以下代码以导入驱动程序包。

  ```
  package main
  
  import "github.com/go-sql-driver/mysql"
  ```

- 在以上这个代码中，我们将：

  - 将我们的代码添加到`main`包中，以便我们可以独立执行它。
  - 导入 MySQL 驱动程序`github.com/go-sql-driver/mysql`

导入驱动程序后，您将开始编写代码来访问数据库。

## 获取数据库句柄并连接

> 现在可以编写一些 Go 代码，让我们可以使用数据库句柄访问数据库。
>
> 我们将使用指向`sql.DB`结构的指针，它表示对特定数据库的访问。

"**使用指向`sql.DB`的指针，代表我们对特定数据库访问**"

### 1)编写代码

1. 在 main.go 中，在`import`我们刚刚添加的代码下方，粘贴以下 Go 代码以创建数据库句柄。

   ```
   var db *sql.DB
   
   func main(){
   	// 捕获连接属性
   	//调用mysql.Config（）函数
   	//或者使用Config.FormatDSN可用于通过填充结构来创建 DSN 字符串
   	// Capture connection properties.
   	cfg := mysql.Config{
           User:   os.Getenv("DBUSER"),
           Passwd: os.Getenv("DBPASS"),
           Net:    "tcp",
           Addr:   "127.0.0.1:3306",
           DBName: "recordings",
       }
       
       // 获取数据库句柄。
       // Get a database handle.
       	var err error
   		db,err=sql.Open("mysql", cfg.FormatDSN())
   		//定义的var db *sql.DB，在这里获得数据库句柄（指向数据库的指针？），拿了这个指针就可以去操作数据库了。
   		if err != nil {
           log.Fatal(err)
       } 
       
   	  pingErr := db.Ping()
   	  //等我了解db.Ping()之后在再补充
   	  if pingErr != nil {
           log.Fatal(pingErr)
       }
       
       fmt.Println("Connected!")
   
   }
   
   ```

   在这段代码中：

   - 我们声明了一个变量db，类型为[`*sql.DB`](https://pkg.go.dev/database/sql#DB) 这个就是我们的数据库句柄。

     在这里我们制作一个全局变量来简化这个例子，但是在实际的开发中，我们经常会避免使用全局变量，而是把变量传递给需要的函数，或者把变量包装在函数/结构中。

   - 使用 MySQL 驱动程序的[`Config`](https://pkg.go.dev/github.com/go-sql-driver/mysql#Config) -- 和类型的[`FormatDSN`](https://pkg.go.dev/github.com/go-sql-driver/mysql#Config.FormatDSN) -- 来收集连接属性并将它们格式化为连接字符串的 DSN。

     该`Config`结构使代码比连接字符串更易于阅读。

   - 调用[`sql.Open`](https://pkg.go.dev/database/sql#Open) 以初始化`db`变量，传递 `FormatDSN`.

   - 检查来自`sql.Open`的错误。例如，如果我们的数据库连接细节格式不正确，它可能会失败。使用使用检查是很有必要的。

     为了简化处理来自`sql.Open`错误的代码，我们可以使用`log.Fatal`可以优雅的结束执行并且打印错误。在实际生产中我们可能用更加优雅的代码来进行错误的处理。

   - 在这里我们还调用了`pingErr := db.Ping()`这个是为了确认我们连接到数据库正常。在程序运行的时候，调用`sql.Open`可能不会立即进行连接到数据库。这个取决与驱动程序。为了解决这个问题，我们使用了`pingErr := db.Ping()`来确认 `database/sql`包可以在需要时连接

   - 检查来自 `Ping`的错误,以防连接失败

   - 如果`Ping`连接成功，则打印一条消息。

2.在 main.go 文件的顶部附近，就在包声明的下方，导入支持刚刚编写的代码所需的包。

```
package main

import (
    "database/sql"
    "fmt"
    "log"
    "os"

    "github.com/go-sql-driver/mysql"
)
```

3.保存main.go

### 2)运行代码

1.开始将 MySQL 驱动程序模块作为依赖项进行跟踪。

使用[`go get`](https://golang.org/cmd/go/#hdr-Add_dependencies_to_current_module_and_install_them) 将 github.com/go-sql-driver/mysql 模块添加为您自己的模块的依赖项。使用点参数表示“获取当前目录中代码的依赖项”。

Go 下载了这个依赖项，因为你`import` 在上一步中将它添加到了声明中。有关依赖项跟踪的更多信息，请参阅[添加依赖项](https://golang.org/doc/modules/managing-dependencies#adding_dependency)。

2.在命令提示符下，设置Go 程序使用的`DBUSER`和`DBPASS`环境变量。

在 Linux 或 Mac 上：

```
$ export DBUSER=username
$ export DBPASS=password
```

在 Windows 上：

```
C:\Users\you\data-access> set DBUSER=username
C:\Users\you\data-access> set DBPASS=password
```

3.从包含 main.go 的目录中的命令行，通过键入`go run`一个点参数来运行代码，以表示“在当前目录中运行包”。

```
$ go run .
Connected!
```



# 在数据库中查询多行数据

> 主要思路是：对于可能返回多行的 `SQL` 语句，我们可以使用包`database/sql`中的`Query`方法，然后遍历它返回的行。（在后文中我们将在查询单行部分中了解如何查询 [单行](https://golang.org/doc/tutorial/database-access#single_row)。）

### 1)编写代码

1.在` main.go `的正上方`func main`，粘贴以下`Album`结构体的定义。您将**使用它来保存从查询返回的行数据。**=="**使用结构体来保存数据**"

```
type Album struct {
    ID     int64
    Title  string
    Artist string
    Price  float32
}
```

2.在 下方`func main`，粘贴以下`albumsByArtist`函数以查询数据库。

```
// albumsByArtist queries for albums that have the specified artist name.
func albumsByArtist(name string) ([]Album, error) {
    // An albums slice to hold data from returned rows.
    // 一个相册切片，用于保存返回行的数据。
    var albums []Album

    rows, err := db.Query("SELECT * FROM album WHERE artist = ?", name)
    //db.Query()从数据库中执行SQL语句并获取数据
    //rows是得到的返回的多条记录
    //值得注意的是：虽然rows是得到的多条数据，但是这多条数据完不完整就不一定了，所以后面我们要使用rows.Err()来判断得到的数据是否完整！！
    if err != nil {
        return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)
    }
    defer rows.Close()
    // Loop through rows, using Scan to assign column data to struct fields.
    //循环遍历行，使用 Scan 将列数据分配给结构字段。
    //通过循环不断声明一个结构体，把数据存到这个结构体，然后把结构体的字段调用append(albums, alb)追加到结构体切片
    for rows.Next() {
        var alb Album
        if err := rows.Scan(&alb.ID, &alb.Title, &alb.Artist, &alb.Price); err != nil {
            return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)
        }
        //在循环内，每次循环都要进行检查：`Scan`将列值扫描到结构体字段中是否存在错误
        albums = append(albums, alb)
        //在循环内，将新的被写入了数据的结构体-alb append（附加）到albums结构体切片。
    }
    
    if err := rows.Err(); err != nil {
        return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)
    }
    //在循环之后，还要使用rows.Err()这是为什么？
   	//答：如果因为如果查询本身失败，那么返回的rows就不完整，这个时候，你需要对这个潜在的错误进行处理。）
    return albums, nil
}
```

**在上面这段代码中，我们：**

- `var albums []Album`首先声明了一个结构体类型的切片，这个切片是用来保存返回的行的数据。结构体的字段名称和类型对应的是数据库的字段名称和类型。

- **`db.Query`是用来执行`SQL`语句——**`SELECT * FROM album WHERE artist = ?`指定到数据库中查询特定艺术家姓名的专辑。

  `Query`的第一个参数是`SQL`语句。在第一个参数之后，我们可以传递零值或者多个任何类型的参数。这些为`SQL`语句指定了要查询数据的地方。通过把`SQL`语句和参数分开（*而不是将它们放在一起*，*例如*：`fmt.Sprintf()`,我们可以使用`database/sql`包发送与`SQL`文本分开的值，从而消除`SQL`的注入风险。）

- `defer rows.Close()`延迟关闭返回行数据（rows）以便于在函数退出时释放它所有的任何资源。

- `for rows.Next()`循环遍历返回的行`，rows.Scan()`用于将每一列的列值分配给`Album`结构体的字段。

  `Scan`获取指向 *列值*的指针列表，列值将被写入其中。在这里我们可以使用指针，使用&运算符把创建的结构体的变量的字段传入到`Scan`中 `Scan`通过指针往结构体字段写入数据，更新了结构体的字段。

  ```
  func (rs *Rows) Scan(dest ...interface{}) error
  //Scan 将当前行中的列复制到 dest 指向的值中,dest 中的值数必须与 Rows 中的列数相同。
  //Scan 将从数据库中读取的列转换为以下常见的 Go 类型和 sql 包提供的特殊类型：
  *string 
  *[]byte 
  *int, *int8, *int16, *int32, *int64 
  *uint, *uint8, *uint16, *uint32, *uint64 
  *bool 
  *float32, *float64 
  *interface{} 
  *RawBytes 
  *Rows (游标值）
  任何类型的实现扫描仪（见扫描仪文档)
  
  虽然 Scan 将从数字数据库列扫描的所有数字字符串化为 *string，但会检查数字类型的扫描是否溢出。例如，值为 300 的 float64 或值为“300”的字符串可以扫描到 uint16，但不能扫描到 uint8，尽管 float64(255) 或“255”可以扫描到 uint8。一个例外是，将某些 float64 数字扫描到字符串可能会在字符串化时丢失信息。通常，将浮点列扫描到 *float64 中。
  如果 dest 参数的类型为 *[]byte，则 Scan 会在该参数中保存相应数据的副本。副本归调用者所有，可以修改和无限期保留。可以通过使用 *RawBytes 类型的参数来避免复制；请参阅 RawBytes 的文档以了解其使用限制。
  如果参数的类型为 *interface{}，则 Scan 会复制底层驱动程序提供的值而不进行转换。当从 []byte 类型的源值扫描到 *interface{} 时，会生成切片的副本并且调用者拥有结果。
  time.Time 类型的源值可以扫描为 *time.Time、*interface{}、*string 或 *[]byte 类型的值。转换为后两者时，使用的是 time.RFC3339Nano。
  
  可以将 bool 类型的源值扫描为 *bool、*interface{}、*string、*[]byte 或 *RawBytes 类型。
  
  为了扫描到 *bool，源可能是真、假、1、0 或 strconv.ParseBool 可解析的字符串输入。
  
  Scan 还可以将从查询返回的游标（例如“select cursor(select * from my_table) from dual”）转换为可以从中扫描的 *Rows 值。如果父 *Rows 关闭，则父选择查询将关闭任何游标 *Rows。
  
  ```

- 在循环内，每次循环都要进行检查：`Scan`将列值扫描到结构体字段中是否存在错误。

- 在循环内，将新的获取到数据的结构体——alb不断的附到`albums`结构体切片。

- 在循环之后，使用`rows.Err`是用来检查执行查询语句之后返回的rows是否完整（因为如果查询本身失败，那么返回的rows就不完整，这个时候，你需要对这个潜在的错误进行处理。）

  处理的方式是：`return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)`返回`nil`，打印错误。

3.更新我们的main函数，并调用函数`albumsByArtist`.

在 末尾`func main`，添加以下代码:

```
albums, err := albumsByArtist("John Coltrane")
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Albums found: %v\n", albums)
```

在新代码中，我们现在：

- 调用`albumsByArtist`我们写的函数，将其返回值分配给一个新`albums`变量。
- 分配给新的变量是为了查看结果，所以在最后打印了`fmt.Printf("Albums found: %v\n", albums)`

4.运行代码

```
$ go run .
Connected!
Albums found: [{1 Blue Train John Coltrane 56.99} {2 Giant Steps John Coltrane 63.99}]
```



# 在数据库中查询单行

> 对于我们以及明确知道的，执行`SQL`查询语句之后，*<u>最多</u>*（因为还有可能查不到.......这种情况就需要我们对err的类型做更加精确的判断，判断是查不到还是执行`SQL`语句发生了错误）。返回一条数据的的情况来说，我们一般使用—— `QueryRow`这比使用`Query`更加简单，因为免去了循环多行数据。

### 1)编写代码

1.在 下`albumsByArtist`，粘贴以下`albumByID`函数

```
// albumByID queries for the album with the specified ID.
// albumByID 查询指定 ID 的专辑。
func albumByID(id int64) (Album, error) {
    // An album to hold data from the returned row.
    //一个结构体，用于保存返回行中的数据。
    var alb Album

    row := db.QueryRow("SELECT * FROM album WHERE id = ?", id)
    //db.QueryRow不返回错误，它的错误是由`Rows.Scan`返回任何查询错误。
    
    //执行SQL语句，获取的返回数据
    //把获取的数据扫描到结构体字段，在扫描时如果发生了错误，获取错误类型，如果本来就没获取到数据，在扫描时必定会发生错误，这个时候错误类型就可以与sql.ErrNoRows进行判断，如果等于sql.ErrNoRows，那么我们给前端发挥信息，或者我们在本地打印这条信息。如果不等于sql.ErrNoRows，那么可能是发生了其他的错误，这个时候我们还是要返回给前端这条错误消息，或者我们在本地打印这条错误消息。
if err := row.Scan(&alb.ID, &alb.Title, &alb.Artist, &alb.Price); err != nil {
        if err == sql.ErrNoRows {
            return alb, fmt.Errorf("albumsById %d: no such album", id)
        }
        return alb, fmt.Errorf("albumsById %d: %v", id, err)
    }
    return alb, nil
}
```

- 使用[`DB.QueryRow`](https://pkg.go.dev/database/sql#DB.QueryRow) 执行`SELECT`到查询语句具有指定ID的专辑

  它返回一个`sql.Row`. 为了简化调用代码（您的代码！），`QueryRow`不返回错误。相反，它会安排`sql.ErrNoRows`从`Rows.Scan`以后返回任何查询错误（例如）

- 使用[`Row.Scan`](https://pkg.go.dev/database/sql#Row.Scan)列值复制到结构体的相应字段。

- 检查来自Scan的错误

  特殊错误`sql.ErrNoRows`表明查询未返回任何行。通常，该错误值得用更具体的文本替换，例如此处的“没有这样的专辑”

  

2.更新`main`调用`albumByID`。

在 末尾`func main`，添加以下代码。

```
// Hard-code ID 2 here to test the query.
alb, err := albumByID(2)
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Album found: %v\n", alb)

在新代码中，您现在：

调用albumByID您添加的函数。

打印返回的相册 ID。
```

3.运行代码

从包含 `main.go 的`目录中的命令行，运行代码。

```
$ go run .
Connected!
Albums found: [{1 Blue Train John Coltrane 56.99} {2 Giant Steps John Coltrane 63.99}]
Album found: {2 Giant Steps John Coltrane 63.99}
```



# 往数据库添加信息

> 我们在已经了解了如何使用`Query`和`QueryRow`使用返回数据的 `SQL` 语句。要执行*不*返回数据的`SQL` 语句，请使用`Exec`.

### 1)编写代码

1. 在 下方`albumByID`，粘贴以下`addAlbum`函数以在数据库中插入新专辑，然后保存 `main.go。`

   ```
   // addAlbum adds the specified album to the database,
   // addAlbum 用于将指定的专辑添加到数据库中， 
   // returning the album ID of the new entry
   // 返回新专辑的专辑 ID，如果是和前端进行交互，我们往往是给前端返回成功的状态码。而不是返回新专辑的ID
   //函数需要一个 Album类型的参数alb来作为往数据库插入的数据
   //在这里是给前端返回了一个我们插入参数的数据之后，专辑在数据库中的ID
   func addAlbum(alb Album) (int64, error) {
       result, err := db.Exec("INSERT INTO album (title, artist, price) VALUES (?, ?, ?)", alb.Title, alb.Artist, alb.Price)
       if err != nil {
           return 0, fmt.Errorf("addAlbum: %v", err)
       }
       id, err := result.LastInsertId()
       if err != nil {
           return 0, fmt.Errorf("addAlbum: %v", err)
       }
       return id, nil
   }
   
   ```

- 在这里我们使用`db.Exec`来往数据库里插入信息

  和`Query`很类似的是：`Exec`采用 `SQL` 语句后跟 `SQL 语句的参数值`。

- `db.Exec`在执行插入语句时，会返回错误类型。我们继而对错误进行判断。如果往数据库插入数据发生了错误，我们就返回0，并且打印错误值。

- 如果插入数据成功，我们尝试检索我们刚刚插入的数据的行 ID [`Result.LastInsertId`](https://pkg.go.dev/database/sql#Result.LastInsertId)。

  这么做是为了看看我们在检索我们我们插入数据的ID时是否发生了错误。

2.更新`main`以调用新`addAlbum`函数

```
albID, err := addAlbum(Album{
    Title:  "The Modern Sound of Betty Carter",
    Artist: "Betty Carter",
    Price:  49.99,
})
if err != nil {
    log.Fatal(err)
}
fmt.Printf("ID of added album: %v\n", albID)
```

在新代码中，您现在：

- 调用`addAlbum`新专辑，将您要添加的专辑的 ID 分配给`albID`变量。

3.运行代码

从包含 `main.go` 的目录中的命令行，运行代码

```
$ go run .
Connected!
Albums found: [{1 Blue Train John Coltrane 56.99} {2 Giant Steps John Coltrane 63.99}]
Album found: {2 Giant Steps John Coltrane 63.99}
ID of added album: 5
```

# 完成代码

**以下是我们编写的完整的代码。**

```
package main

import (
    "database/sql"
    "fmt"
    "log"
    "os"

    "github.com/go-sql-driver/mysql"
)

var db *sql.DB

type Album struct {
    ID     int64
    Title  string
    Artist string
    Price  float32
}

func main() {
    // Capture connection properties.
    cfg := mysql.Config{
        User:   os.Getenv("DBUSER"),
        Passwd: os.Getenv("DBPASS"),
        Net:    "tcp",
        Addr:   "127.0.0.1:3306",
        DBName: "recordings",
    }
    // Get a database handle.
    var err error
    db, err = sql.Open("mysql", cfg.FormatDSN())
    if err != nil {
        log.Fatal(err)
    }

    pingErr := db.Ping()
    if pingErr != nil {
        log.Fatal(pingErr)
    }
    fmt.Println("Connected!")

    albums, err := albumsByArtist("John Coltrane")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Albums found: %v\n", albums)

    // Hard-code ID 2 here to test the query.
    alb, err := albumByID(2)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Album found: %v\n", alb)

    albID, err := addAlbum(Album{
        Title:  "The Modern Sound of Betty Carter",
        Artist: "Betty Carter",
        Price:  49.99,
    })
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("ID of added album: %v\n", albID)
}

// albumsByArtist queries for albums that have the specified artist name.
func albumsByArtist(name string) ([]Album, error) {
    // An albums slice to hold data from returned rows.
    var albums []Album

    rows, err := db.Query("SELECT * FROM album WHERE artist = ?", name)
    if err != nil {
        return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)
    }
    defer rows.Close()
    // Loop through rows, using Scan to assign column data to struct fields.
    for rows.Next() {
        var alb Album
        if err := rows.Scan(&alb.ID, &alb.Title, &alb.Artist, &alb.Price); err != nil {
            return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)
        }
        albums = append(albums, alb)
    }
    if err := rows.Err(); err != nil {
        return nil, fmt.Errorf("albumsByArtist %q: %v", name, err)
    }
    return albums, nil
}

// albumByID queries for the album with the specified ID.
func albumByID(id int64) (Album, error) {
    // An album to hold data from the returned row.
    var alb Album

    row := db.QueryRow("SELECT * FROM album WHERE id = ?", id)
    if err := row.Scan(&alb.ID, &alb.Title, &alb.Artist, &alb.Price); err != nil {
        if err == sql.ErrNoRows {
            return alb, fmt.Errorf("albumsById %d: no such album", id)
        }
        return alb, fmt.Errorf("albumsById %d: %v", id, err)
    }
    return alb, nil
}

// addAlbum adds the specified album to the database,
// returning the album ID of the new entry
func addAlbum(alb Album) (int64, error) {
    result, err := db.Exec("INSERT INTO album (title, artist, price) VALUES (?, ?, ?)", alb.Title, alb.Artist, alb.Price)
    if err != nil {
        return 0, fmt.Errorf("addAlbum: %v", err)
    }
    id, err := result.LastInsertId()
    if err != nil {
        return 0, fmt.Errorf("addAlbum: %v", err)
    }
    return id, nil
}
```

