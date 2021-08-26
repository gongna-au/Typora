# Linux私房菜—首次登录

### 4.1 首次登陆系统

#### 4.1.1 首次登陆`CentOS 7.x`图形接口

- 只要文件名的开头是由小数点开始的，那么该文件名就不会在一般观察模式被显示出来！所以说，在 Linux 下面，隐藏文件并不是什么特殊的权限， 单纯是因为文件名命名的处理方式来搞定的！这样理解否？

- 如果你想要观察系统有多少不同的文件系统呢？那就看一下文件资源管理器左侧“设备”的项目下，有几个项目就是有几个设备啰！现在让我们来观察一下“计算机”内有什么数据吧！ 请按下他！然后观察一下如下的图示：

  **bin目录下的是：有链接文件的数据**

  **如果你的账号没有进入某个目录的权限，那么这个目录就会出现一个X的符号。**

#### 4.1.4 在终端接口登陆`linux`

```
CentOS Linux 7 （Core）
Kernel 3.10.0-229.el7.x86_64 on an x86_64
study login: dmtsai
Password: &lt;==这里输入你的密码
Last login: Fri May 29 11:55:05 on tty1 &lt;==上次登陆的情况
[dmtsai@study ~]$ _ &lt;==光标闪烁，等待你的指令输入
```

1. `CentOS` Linux 7 （Core）： 显示Linux distribution的名称（`CentOS`）与版本（7）；
2. . `Kernel 3.10.0-229.el7.x86_64 on an x86_64`： 显示 Linux 核心的版本为`3.10.0-229.el7.x86_64`， 且目前这部主机的硬件等级为`x86_64`。
3. study login: 那个study是你的主机名称。我们在第三章安装时有填写主机名称为：`study.centos.vbird`，主机名称的显示通常只取第一个小数点前的字母，所以就成为study啦！至于login:则是一支可以让我们登陆的程序
4. Password:：要你输入密码啰！ 请注意，在输入密码的时候，屏幕上面“不会显示任何的字样！”， 所以不要以为你的键盘坏掉去！很多初学者一开始到这里都会拼命的问！啊我的键盘怎么不能用
5. `Last login: Fri May 29 11:55:05 on tty1`： 当使用者登陆系统后，系统会列出上一次这个帐号登陆系统的时间与终端机名称！ 建议大家还是得要看看这个信息，是否真的是自己的登陆所致喔！
6. [`dmtsai`@study ~]$ _：这一行则是正确登陆之后才显示的讯息， 最左边的 `dmtsai` 显示的是“目前使用者的帐号”，而@之后接的 study 则是“主机名称”，至于最右边的~则指的是“目前所在的目录”，那个$则是我们常常讲的“提示字符”啦！

**那个 ~ 符号代表的是“使用者的主文件夹”的意思，他是个“变量！**举例来说，root的主文件夹在/root， 所以 ~ 就代表/root的意思。而`dmtsai`的主文件夹在`/home/dmtsai`， 所以如果你以`dmtsai`登陆时，他看到的 ~ 就会等于`/home/dmtsai`喔！

**至于提示字符方面，在Linux当中，默认root的提示字符为 # ，而一般身份使用者的提示字符为 $ 。**

那么如何离开系统呢？其实应该说“登出Linux”才对！

答：

```
[dmtsai@study ~]$ exit
```

就能够登出Linux了。但是请注意：“**离开系统并不是关机**. 基本上，Linux本身已经有相当多的工作在进行，你的登陆也仅是其中的一个“工作”而已， 所以当你离开时，这次这个登陆的工作就停止了，但此时Linux其他的工作是还是继续在进行的！

### 4.2 文字模式下指令的下达

其实我们都是通过“程序”在跟系统作沟通的，本章上面提到的窗口管理员或文字模式都是一组或一只程序在负责我们所想要完成的任务.文字模式登陆后所取得的程序被称为壳（Shell），这是因为这支程序负责最外面跟使用者（我们）沟通，所以才被戏称为壳程序！

**我敲这个指令的目的是想要完成什么任务？”，再看看输出的结果是否符合你的需求，这样才能学到东西！**

#### 4.2.1). 开始下达指令

- 一行指令中第一个输入的部分绝对是“指令（command）”或“可可执行文件案（例如批次脚本,script）”
- command 为指令的名称，例如变换工作目录的指令为 `cd` 等等
- 中刮号[]并不存在于实际的指令中，而加入选项设置时，通常选项前会带 - 号，例如 -h；有时候会使用选项的完整全名，则选项前带有 -- 符号，例如 --help；
- `. parameter1` `parameter2.`. 为依附在选项后面的参数，或者是 command 的参数；
- 指令, 选项, 参数等这几个咚咚中间以空格来区分，**不论空几格 shell 都视为一格**。所以空格是很重要的特殊字符！；
- 按下[Enter]按键后，该指令就立即执行。[Enter]按键代表着一行指令的开始启动
- 指令太长的时候，可以使用反斜线 （\） 来跳脱[Enter]符号，使指令连续到下一行。注意！反斜线后就立刻接特殊字符，才能跳脱
- . 其他：i. 在 Linux 系统中，英文大小写字母是不一样的。举例来说， `cd` 与 CD 并不同。

注意到上面的说明当中，**“第一个被输入的数据绝对是指令或者是可执行的文件”！**

```
$ ls -al ~
```

#### 4.2.2).基础指令的操作

显示日期与时间的指令： date

简单好用的计算机： `bc`

```
$ date +%Y/%m/%d
2021/08/25
那个“+%Y%m%d”就是date指令的一些参数功能
```

- 某些指令有特殊的参数存在， 若输入错误的参数，则该指令会有错误讯息的提示，通过这个提示我们可以借以了解指令下达错误之处

  ```
  $ bc
  bc 1.07.1
  Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
  This is free software with ABSOLUTELY NO WARRANTY.
  For details type `warranty'. 
  1+3
  4
  scale=3
  1/3
  .333
  quit 
  ```

  

**在命令行界面里面下达指令时，会有两种主要的情况：**

- 一种是该指令会直接显示结果然后回到命令提示字符等待下一个指令的输入；

- 一种是进入到该指令的环境，直到结束该指令才回到命令提示字符的环境。

  如果是进入软件功能的环境（例如上面的`bc`软件），那么就得要使用该软件的结束指令。

#### 4.2.3). 重要的几个热键[Tab], `[ctrl]-c`, `[ctrl]-d`

- ​	[Tab]按键

  具有“命令补全”与“文件补齐”的功能

  ```
  $ ls -al ~/.bash[tab][tab]
  .bash_history  .bash_logout   .bashrc
  $ date --[tab]
  --date=       --help        --rfc-3339=   --utc         
  --debug       --iso-8601    --rfc-email   --version     
  --file=       --reference=  --set= 
  ```

  - [Tab] 接在一串指令的第一个字的后面，则为“命令补全”；
  - [Tab] 接在一串指令的第二个字以后时，则为“文件补齐”！

- `[Ctrl]-c` 按键

如果你在Linux下面输入了错误的指令或参数， 如果你想让当前的程序“停掉”的话，可以输入`：[Ctrl]与c`按键（先按着`[Ctrl]`不放，且再按下c按键，是组合按键）， 那就是中断目前程序的按键！

- `[Ctrl]-d` 按键

那么`[Ctrl]-d`是什么呢？就是`[Ctrl]与d`按键的组合啊！这个组合按键通常代表着： “键盘输入结束（End Of File, `EOF` 或 End Of Input）”的意思！ 另外，他也可以用来取代exit的输入呢！例
如你想要直接离开命令行，可以直接按下`[Ctrl]-d`就能够直接离开了（相当于输入exit啊！）

- [shift]+{`[PageUP]`|`[Page Down]`}按键

#### 4.2.4). 错误讯息的察看

```
$ DATE
bash: DATE: command not found... # 这里显示错误的讯息
Similar command is: 'date' # 这里竟然给你一个可能的解决方案耶！
```

上面那个bash:表示的是我们的Shell的名称,Linux的默认壳程序就是bash那么上面的例子说明了bash有错误，什么错误呢？bash告诉你：

```
DATE: command not found
```

- 这个指令不存在，因为该软件没有安装之故。解决方法就是安装该软件；
- 这个指令所在的目录目前的用户并没有将他加入指令搜寻路径中.
- 很简单！因为你打错字！

### 4.3 Linux系统的线上求助man page与info page

```
g[tab][tab]
Display all 210 possibilities? (y or n)
g++                           gnome-system-monitor
g++-10                        gnome-terminal
gamemoded                     gnome-terminal.real
gamemoderun                   gnome-terminal.wrapper
gamma4scanimage               gnome-text-editor
gapplication                  gnome-thumbnail-font
gatttool                      gnome-www-browser
gcalccmd                      go/
gcc                           gobject-query
gcc-10                        gofmt
gcc-ar                        goimports
gcc-ar-10                     gold
gcc-nm                        google-chrome
gcc-nm-10                     google-chrome-stable
gcc-ranlib                    gopls
gcc-ranlib-10                 gorename
gcore                         gpasswd
gcov                          gpg
gcov-10                       gpg-agent
gcov-dump                     gpgcompose
gcov-dump-10                  gpgconf
gcov-tool                     gpg-connect-agent
gcov-tool-10                  gpgparsemail
gcr-viewer                    gpgsm
gdb                           gpgsplit
--More--      
```

不需要背指令，那么我们如何知道每个指令的详细用法？还有，某些配置文件的内容到底是什么？ 这个可就不需要担心了！因为在Linux上开发的软件大多数都是自由软件/开源软件，而这些软件的开发者为了让大家能够了解指令的用法， 都会自行制作很多的文件，而这些文件也可以直接在线上就能够轻易的被使用者查询出来喔！很不赖吧！ 这根本就是“线上说明文档

#### 4.3.1). 指令的 --help 求助说明

事实上，几乎 Linux 上面的指令，在开发的时候，开发者就将可以使用的指令语法与参数写入指令操作过程中。

```
date --help
Usage: date [OPTION]... [+FORMAT] # 这里有基本语法
or: date [-u&#124;--utc&#124;--universal] [MMDDhhmm[[CC]YY][.ss]] # 这是设置时间的语法
Display the current time in the given FORMAT, or set the system date.
# 下面是主要的选项说明
Mandatory arguments to long options are mandatory for short options too.
-d, --date=STRING display time described by STRING, not 'now'
-f, --file=DATEFILE like --date once for each line of DATEFILE
....（中间省略）....
-u, --utc, --universal print or set Coordinated Universal Time （UTC）
--help 显示此求助说明并离开
--version 显示版本信息并离开
# 下面则是重要的格式 （FORMAT） 的主要项目
FORMAT controls the output. Interpreted sequences are:
%% a literal %
%a locale's abbreviated weekday name （e.g., Sun）
%A locale's full weekday name （e.g., Sunday）
....（中间省略）....
# 下面是几个重要的范例 （Example）
Examples:
Convert seconds since the epoch （1970-01-01 UTC） to a date
$ date --date='@2147483647'
....（下面省略）....


-----------------------------------------------------------


date --help
用法：date [选项]... [+格式]
　或：date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
以给定<格式>字符串的形式显示当前时间，或者设置系统日期。

必选参数对长短选项同时适用。
  -d, --date=字符串          显示给定<字符串>描述的时间，而非“当前时间”
      --debug                对解析的日期添加注释，
                              对不规范的使用方式进行警告，并输出警告信息到
                              标准错误
  -f, --file=日期文件        类似 --date；使用给定<日期文件>，一次处理一行
  -I[FMT], --iso-8601[=FMT]  以 ISO 8601 格式输出日期/时间。
                               FMT='date' 时只输出日期（默认行为），
                               当其为 'hours'、'minutes'、'seconds' 或 'ns' 时
                               则按照所指示的精确度显示日期和时间。
                               示例：2006-08-14T02:34:56-06:00
  -R, --rfc-email            以 RFC 5322 格式输出日期和时间。
                               例如：Mon, 14 Aug 2006 02:34:56 -0600
      --rfc-3339=格式        以 RFC 3339 格式输出日期/时间。
                               <格式>字符串可以是'date'、'seconds' 或 'ns'，
                               用于指定日期和时间显示的精确度。
                               例如：2006-08-14 02:34:56-06:00
  -r, --reference=文件       显示指定<文件>的最后修改时间
  -s, --set=字符串           按照给定<字符串>描述的时间来设置时间
  -u, --utc, --universal     按照协调世界时（UTC）显示或设置时间
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出

给定的格式FORMAT 控制着输出，解释序列如下：

  %%	一个文字的 %
  %a	当前locale 的星期名缩写(例如： 日，代表星期日)
  %A	当前locale 的星期名全称 (如：星期日)
  %b	当前locale 的月名缩写 (如：一，代表一月)
  %B	当前locale 的月名全称 (如：一月)
  %c	当前locale 的日期和时间 (如：2005年3月3日 星期四 23:05:25)
  %C	世纪；比如 %Y，通常为省略当前年份的后两位数字(例如：20)
  %d	按月计的日期(例如：01)
  %D	按月计的日期；等于%m/%d/%y
  %e	按月计的日期，添加空格，等于%_d
  %F   完整日期格式，等价于 %+4Y-%m-%d
  %g   ISO-8601 格式年份的最后两位（参见 %G）
  %G   ISO-8601 格式年份（参见 %V），一般只和 %V 结合使用
  %h	等于%b
  %H	小时(00-23)
  %I	小时(00-12)
  %j	按年计的日期(001-366)
  %k   小时，空格补充空白位（ 0..23）；与 %_H 相同
  %l   小时，空格补充空白位（ 1..12）；与 %_I 相同
  %m   月份（01..12）
  %M   分钟（00..59）
  %n   换行
  %N   纳秒（000000000..999999999）
  %p   当前地区时间设置中 AM 或 PM 的等效值（“上午”或“下午”），未知则为空
  %P   类似 %p，但使用小写
  %q   一年中的季度（1..4）
  %r   当前地区时间中十二小时制钟表时间（例如，下午 11时11分04秒）
  %R   24 小时制的时间和分钟；和 %H:%M 相同
  %s   1970-01-01 00:00:00 UTC 以来的秒数
  %S	秒(00-60)
  %t	输出制表符 Tab
  %T	时间，等于%H:%M:%S
  %u	星期，1 代表星期一
  %U	一年中的第几周，以周日为每星期第一天(00-53)
  %V	ISO-8601 格式规范下的一年中第几周，以周一为每星期第一天(01-53)
  %w	一星期中的第几日(0-6)，0 代表周一
  %W	一年中的第几周，以周一为每星期第一天(00-53)
  %x	当前locale 下的日期描述 (如：12/31/99)
  %X	当前locale 下的时间描述 (如：23:13:48)
  %y	年份最后两位数位 (00-99)
  %Y	年份
  %z +hhmm		数字时区(例如，-0400)
  %:z +hh:mm		数字时区(例如，-04:00)
  %::z +hh:mm:ss	数字时区(例如，-04:00:00)
  %:::z			数字时区带有必要的精度 (例如，-04，+05:30)
  %Z			按字母表排序的时区缩写 (例如，EDT)

默认情况下，日期的数字区域以0 填充。
以下可选标记可以跟在"%"后：

  -  （连字符）不填充该域
  _  （下划线）以空格填充
  0  （数字0）以 0 填充
  +  以零填充，并且在大于四个数位的未来年份之前放置 '+' 符号
  ^  如果可能，使用大写字母
  #  如果可能，使用相反的大小写

在任何标记之后还允许一个可选的域宽度指定，它是一个十进制数字。
作为一个可选的修饰声明，它可以是E，在可能的情况下使用本地环境关联的
表示方式；或者是O，在可能的情况下使用本地环境关联的数字符号。

示例：
将 epoch（1970-01-01 UTC）以来的秒数转换为日期
  $ date --date='@2147483647'

显示美国东海岸的当前时间（请使用 tzselect(1) 来查询 TZ 的具体数值）
  $ TZ='America/Los_Angeles' date

显示美国东海岸下一个星期五当地时间早上九点
  $ date --date='TZ="America/Los_Angeles" 09:00 next Fri'

GNU coreutils 在线帮助：<https://www.gnu.org/software/coreutils/>
请向 <http://translationproject.org/team/zh_CN.html> 报告任何翻译错误
完整文档 <https://www.gnu.org/software/coreutils/date>
或者在本地使用：info '(coreutils) date invocation'

```

**首先一开始是下达语法的方式 （Usage），这个 date 有两种基本语法**

- **一种是直接下达并且取得日期回传值**
- **另一种方式，则是加上 `MMDDhhmmCCYY` 的方式来设置日期时间**

基本上，如果是指令，那么通过这个简单的 --help 就可以很快速的取得你所需要的选项、参数的说明了！这很重要！

**在 `linux` 下面你需要学习“任务达成”的方式， 不用硬背指令参数**

- **值得注意的是：虽然 --help 已经相当好用，不过，通常 --help 用在协助你查询“你曾经用过的指令所具备的选项与参数”而已**
- **如果你要使用的是从来没有用过得指令，或者是你要查询的根本就不是指令，而是文件的“格式”时，那就得要通过 man page**

#### 4.3.2). man page

```
$ man date
DATE(1)                          User Commands                         DATE(1)

NAME	
       date - print or set the system date and time
       //这个指令的完整全名，如下所示为date且说明简单用途为设置与显示日期/时间

SYNOPSIS
//这个指令的基本语法如下所示
       date [OPTION]... [+FORMAT]
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]

DESCRIPTION
//详细说明刚刚语法谈到的选项与参数的用法
       Display the current time in the given FORMAT, or set the system date.

       Mandatory  arguments  to  long  options are mandatory for short options
       too.

       -d, --date=STRING
              display time described by STRING, not 'now'

       --debug
              annotate the parsed date, and warn about questionable  usage  to
              stderr

       -f, --file=DATEFILE
...
//找到了！下面就是格式化输出的详细数据！
       FORMAT controls the output.  Interpreted sequences are:

       %%     a literal %

       %a     locale's abbreviated weekday name (e.g., Sun)

       %A     locale's full weekday name (e.g., Sunday)

       %b     locale's abbreviated month name (e.g., Jan)

       %B     locale's full month name (e.g., January)

       %c     locale's date and time (e.g., Thu Mar  3 23:05:25 2005)

       %C     century; like %Y, except omit last two digits (e.g., 20)

       %d     day of month (e.g., 01)
	%D     date; same as %m/%d/%y

       %e     day of month, space padded; same as %_d

       %F     full date; like %+4Y-%m-%d

       %g     last two digits of year of ISO week number (see %G)

       %G     year of ISO week number (see %V); normally useful only with %V

       %h     same as %b

       %H     hour (00..23)

       %I     hour (01..12)

       %j     day of year (001..366)

       %k     hour, space padded ( 0..23); same as %_H
       
       
DATE STRING
//--date 的格式说明！
       The --date=STRING is a mostly free format human  readable  date  string
       such  as  "Sun, 29 Feb 2004 16:21:42 -0800" or "2004-02-29 16:21:42" or
       even "next Thursday".  A date string may contain items indicating  cal‐
       endar  date,  time of day, time zone, day of week, relative time, rela‐
       tive date, and numbers.  An empty string indicates the beginning of the
       day.   The date string format is more complex than is easily documented
       here but is fully described in the info documentation.

AUTHOR
       Written by David MacKenzie.

REPORTING BUGS
       GNU coreutils online help: <https://www.gnu.org/software/coreutils/>
       Report any translation bugs to <https://translationproject.org/team/>

COPYRIGHT
//受到著作权法的保护！用的就是 GPL 了！
       Copyright © 2020 Free Software Foundation, Inc.   License  GPLv3+:  GNU
       GPL version 3 or later <https://gnu.org/licenses/gpl.html>.
       This  is  free  software:  you  are free to change and redistribute it.


```

- **进入man指令的功能后，你可以按下“空白键”往下翻页，可以按下“ q ”按键来离开man的环境。 **

- **“DATE（1）”，DATE我们知道是指令的名称， 那么（1）代表什么呢？他代表的是“一般使用者可使用的指令”的意思！**

  | 代号 | 代表内容                                                     |
  | ---- | ------------------------------------------------------------ |
  | 1    | 使用者在shell环境中可以操作的指令或可可执行文件              |
  | 2    | 系统核心可调用的函数与工具等                                 |
  | 3    | 一些常用的函数（function）与函数库（library），大部分为C的函数库`（libc`） |
  | 4    | 设备文件的说明，通常在`/dev`下的文件                         |
  | 5    | 配置文件或者是某些文件的格式                                 |
  | 6    | 游戏（games）                                                |
  | 7    | 惯例与协定等，例如Linux文件系统、网络协定、ASCII code等等的说明 |
  | 8    | 系统管理员可用的管理指令                                     |
  | 9    | 跟kernel有关的文件                                           |

如果你下达了“man null”时，会出现的第一行是：“NULL（4）”，对照一下上面的数字意义， 原来null这个玩意儿竟然是一个“设备文件”呢！

-  上表中的1, 5, 8这三个号码特别重要。1--使用者在shell环境中可以操作的指令。或者可以执行的可执行文件。
- 5--配置文件或者某些文件的格式
- 8--系统管理员可以使用的管理命令

基本上，man page大致分成下面这几个部分

| 代号        | 内容说明                                                     |
| ----------- | ------------------------------------------------------------ |
| NAME        | 简短的指令、数据名称说明                                     |
| SYNOPSIS    | 简短的指令下达语法（syntax）简介                             |
| DESCRIPTION | 较为完整的说明，这部分最好仔细看看！                         |
| OPTIONS     | 针对 SYNOPSIS 部分中，有列举的所有可用的选项说明             |
| COMMANDS    | 当这个程序（软件）在执行的时候，可以在此程序（软件）中下达的指令 |
| FILES       | 这个程序或数据所使用或参考或链接到的某些文件                 |
| SEE ALSO    | 可以参考的，跟这个指令或数据有相关的其他说明！               |
| EXAMPLE     | 一些可以参考的范例                                           |

- 先察看NAME的项目，约略看一下这个数据的意思；
- 再详看一下DESCRIPTION，这个部分会提到很多相关的数据与使用时机，从这个地方可以学到很多小细节呢；
- 是查询关于OPTIONS的部分了！ 可以知道每个选项的意义，这样就可以下达比较细部的指令内容呢！
- 上面的SEE ALSO就告知我们还可以利用“`info coreutils date`”来进一步查阅
- 当你按下“/”之后，光标就会移动到屏幕的最下面一行， 并等待你输入搜寻的字串了

| 按键        | 进行工作                                                     |
| ----------- | ------------------------------------------------------------ |
| 空白键      | 向下翻一页                                                   |
| [Page Down] | 向下翻一页                                                   |
| [Page Up]   | 向上翻一页                                                   |
| [Home]      | 去到第一页                                                   |
| [End]       | 去到最后一页                                                 |
| /string     | 向“下”搜寻 string 这个字串，如果要搜寻 `vbird` 的话，就输入 `/vbird` |
| ?string     | 向“上”搜寻 string 这个字串                                   |
| n, N        | 用 / 或 ? 来搜寻字串时，可以用 n 来继续下一个搜寻 （不论是 / 或 ?） ，可以利用 N 来进行“反向”搜寻。举例来说，我以 /`vbird` 搜寻 `vbird` 字串那么 可以 n 继续往下查询，用 N 往上查询。若以 `?vbird` 向上查询 `vbird` 字串， 那我可以用 n 继续“向上”查询，用 N 反向查询 |

既然有man page，自然就是因为有一些文件数据，那么这些man page的数据 放在哪里呢,不同的distribution通常可能有点差异性，不过，通常是放
在`/usr/share/man`这个目录里头，然而，我们可以通过修改他的man page搜寻路径来改善

搜寻特定指令/文件的man page说明文档，在某些情况下，你可能知道要使用某些特定的指令或者是修改某些特定的配置文件，但是偏
偏忘记了该指令的完整名称。 有些时候则是你只记得该指令的部分关键字。这个时候你要如何查出来你所想要知道的man page呢？

```
$ man -f man
man （1） - an interface to the on-line reference manuals
man （1p） - display system documentation
man （7） - macros to format man pages
```

使用 -f 这个选项就可以取得更多与man相关的信息，而上面这个结果当中也有提示了 （数字） 的内容， 举例来说，第三行的“ man （7） ”表示有个man （7）的说明文档存在喔！但是却有个man （1）存在啊！ 那当我们下达“ man man ”的时候，到底是找到哪一个说明文档呢？

你可以自行将上面两个指令输入一次看看，就知道，两个指令输出的结果是不同的。 那个1, 7就是分别取出在man page里面关于1与7相关数据的文件文件啰！ 好了，那么万一我真的忘记了下达数字，只有输入“ man man ”时，那么取出的数据到底是1还是7啊？ 这个就跟搜寻的顺序有关了。搜寻的顺序是记录在`/etc/man_db.conf` 这个配置文件当中， 先搜寻到的那个说明文档，就会先被显示出来！ 一般来说，通常会先找到数字较小的那个啦！因为排序的关系啊！所以， man man 会跟 man 1 man 结果相同！

- 左边部分：指令（或文件）以及该指令所代表的意义（就是那个数字）；
- 右边部分：这个指令的简易说明，例如上述的“-macros to format man pages”

但如果我想要找的是“关键字”呢？也就是说，我想要同时找上面说的两个地方的内容，只要该内容有关键字存在， 不需要完全相同的指令（或文件）就能够找到时，该怎么办？请看下个范例啰！

```
man -k man
fallocate （2） - manipulate file space
zshall （1） - the Z shell meta-man page
....（中间省略）....
yum-config-manager （1） - manage yum configuration options and yum repositories
yum-groups-manager （1） - create and edit yum's group metadata
yum-utils （1） - tools for manipulating repositories and extended package managemen
```

这个是利用关键字将说明文档里面只要含有man那个字眼的（不
见得是完整字串） 就将他取出来.

```
$ whatis [指令或者是数据] &lt;==相当于 man -f [指令或者是数据]
$ apropos [指令或者是数据] &lt;==相当于 man -k [指令或者是数据]
```

总结：

**怎么找到所需要的指令呢？**

**答：举例来说，打印的相关指令，仅记得 `lp` （line print）而
已。那我就由 `man lp` 开始，去找相关的说明，， 然后，再以 `lp`[tab][tab] 找到任何以 `lp` 为开头的指令，找到我认为可能有点相关的指令后，先以 --help 去查基本的用法， 若有需要再以
man 去查询指令的用法！**

#### 4.3.3). info page

在所有的Unix Like系统当中，都可以利用 man 来查询指令或者是相关文件的用法； 但是，在Linux里面则又额外提供了一种线上求助的方法，那就是利用info这个好用的家伙啦！

基本上，info与man的用途其实差不多，都是用来查询指令的用法或者是文件的格式。但是与man page一口气输出一堆信息不同的是，info page则是将文件数据拆成一个一个的段落，每个段落用自己的页面来撰写， 并且在各个页面中还有类似网页的“超链接”来跳到各不同的页面中，每个独立的页面也被称为一个节点（node）。 所以，你可以将info page想成是文字模
式的网页显示数据啦！

**不过你要查询的目标数据的说明文档必须要以info的格式来写成才能够使用info的特殊功能**,而这个支持info指令的文件默认是放置在`/usr/share/info/`这个目录当中的。举例来说，info这个指令的说明文档有写成info格式，所以，你使用“ info info ”可以得到如下的画面：

```
$ info info
File: info.info, Node: Top, Next: Getting Started, Up: （dir）
Info: An Introduction
*********************
The GNU Project distributes most of its on-line manuals in the "Info
format", which you read using an "Info reader". You are probably using
an Info reader to read this now.
....（中间省略）....
If you are new to the Info reader and want to learn how to use it,
type the command 'h' now. It brings you to a programmed instruction
sequence. # 这一段在说明，按下 h 可以有简易的指令说明！很好用！
....（中间省略）....
* Menu:
* Getting Started:: Getting started using an Info reader.
* Advanced:: Advanced Info commands.
* Expert Info:: Info commands for experts.
* Index:: An index of topics, commands, and variables.
--zz-Info: （info.info.gz）Top, 52 lines --Bot---------------------------------------
```

- File：代表这个info page的数据是来自`info.info`文件所提供的；
- Node：代表目前的这个页面是属于Top节点。 意思是`info.info`内含有很多信息，而Top仅是`info.info`文件内的一个节点内容而已；
- Next：下一个节点的名称为Getting Started，你也可以按“N”到下个节点去；
- Up：回到上一层的节点总揽画面，你也可以按下“U”回到上一层；
- `Prev`：前一个节点。但由于Top是`info.info`的第一个节点，所以上面没有前一个节点的信息

在各链接之间还可以具有类似“超链接”的快速按钮，可以通过[tab]键在各个超链接间移动。 也可以使用U,P,N来在各个阶层与相关链接中显示！非常的不错用啦！ 至于在info page当中可以使用的按键，可以整理成下面这样，事实上，你也可以在 info page 中按下 h 喔！

| 按键        | 进行工作                                     |
| ----------- | -------------------------------------------- |
| 空白键      | 向下翻一页                                   |
| [Page Down] | 向下翻一页                                   |
| [Page Up]   | 向上翻一页                                   |
| [tab]       | 在node 之间移动，有node的地方，通常会以*显示 |
| [Enter]     | 当光标在node上面时可以进入该node             |
| b           | 移动光标到该 info 画面当中的第一个 node 处   |
| e           | 移动光标到该 info 画面当中的最后一个 node 处 |
| n           | 前往下一个 node 处                           |
| p           | 前往上一个 node 处                           |
| u           | u 向上移动一层                               |
| s（/）      | 在 info page 当中进行搜寻                    |
| h, ?        | 显示求助菜单                                 |
| q           | 结束这次的 info page                         |

#### 4.3.4). 其他有用的文件（documents）

刚刚前面说，一般而言，指令或者软件制作者，都会将自己的指令或者是软件的说明制作成“线上说明文档”！但是，毕竟不是每个咚咚都需要做成线上说明文档的，还有相当多的说明需要额外的文件！

此时，这个所谓的 How-To（如何做的意思）就很重要啦！还有，某些软
件不只告诉你“如何做”，还会有一些相关的原理会说明。

那么这些说明文档要摆在哪里呢？哈哈！就是摆在`/usr/share/doc`这个目目录下。

- `/usr/share/doc/grub2-tools-2.02`

另外，很多原版软件释出的时候，都会有一些安装须知、预计工作事项、未来工作规划等等的东西，还有包括可安装的程序等， 这些文件也都放置在 `/usr/share/doc`，而且`/usr/share/doc`这个目录下的数据主要是以套件（packages）为主的， 例如 `nano` 这个软件的相关信息在`usr/share/doc/nano-xxx（`那个`xxx`表示版本的意思！）。

- 在终端机模式中，如果你知道某个指令，但却忘记了相关选项与参数，请先善用 --help的功能来查询相关信息
- 当有任何你不知道的指令或文件格式这种玩意儿，但是你想要了解他，请赶快使用man或者是info来查询！
- 而如果你想要架设一些其他的服务，或想要利用一整组软件来达成某项功能时，请赶快到`/usr/share/doc` 下面查一查有没有该服务的说明文档喔！

### 4.4 超简单文书编辑器： `nano`

```
  

   

                                   [ 新文件 ]
^G 帮助      ^O 写入      ^W 搜索      ^K 剪切      ^T 执行命令  ^C 位置
^X 离开      ^R 读档      ^\ 替换      ^U 粘贴      ^J 对齐      ^_ 跳行
```

^代表的是`[ctrl]`的意思

- `[ctrl]-G`：取得线上说明（help），很有用的！

- `[ctrl]-X`：离开`naon`软件，若有修改过文件会提示是否需要储存喔！

- `[ctrl]-O`：储存盘案，若你有权限的话就能够储存盘案了；

- `[ctrl]-R`：从其他文件读入数据，可以将某个文件的内容贴在本文件中；

- `[ctrl]-W：`搜寻字串，这个也是很有帮助

- `[ctrl]-C`：说明目前光标所在处的行数与列数等信息；

- `[ctrl]-_：`可以直接输入行号，让光标快速移动到该行；

- `[alt]-Y：`校正语法功能打开或关闭（按一下开、再按一下关）

- `[alt]-M`：可以支持鼠标来移动光标的功能

  比较常见的功能是这些，如果你想要取得更完整的说明，可以在`nano`的画面中按下`[ctrl]-G`或者是`[F1]`按键， 就能够显示出完整的`naon`内指令说明了

如果是单纯的想要储存而已，直接按下[enter]即可储存后离开`nano`程序。不过上表中最下面还有两行，我们知道指数符号代表`[crtl]`， 那个M是代表什么呢？其实就是[alt]啰！其实`nano`也不需要记太多指令啦！只要知道怎么进入`nano`、怎么离开，怎么搜寻字串即可。 未来我们还
会学习更有趣的vi呢！

### 4.5 正确的关机方法

在 Linux 下面，由于每个程序 （或者说是服务） 都是在在背景下执行的，因此，在你看不到的屏幕背后其实可能有相当多人同时在你的主机上面工作， 例如浏览网页啦、传送信件啦以 FTP 传送文件啦等等的，如果你直接
按下电源开关来关机时， 则其他人的数据可能就此中断！那可就伤脑筋了！
此外，最大的问题是，若不正常关机，则可能造成文件系统的毁损 （因为来不及将数据回写到文件中，所以有些服务的文件会有问题！）。所以正常情况下，要关机时需要注意下面几件事:

- who  --看看目前谁在线上  `netstat -a`  -看看网络的连线状态
- `ps -aux` --看看背景执行的程序

- 将数据同步写入硬盘中的指令： sync
- 惯用的关机指令： shutdown
- 重新开机，关机： reboot, halt, `poweroff`

 由于Linux系统的关机/重新开机是很重大的系统运行，因此只有root才能够进行例如shutdown, reboot等指令。 不过在某些distributions当中，例如我们这里谈到的`CentOS`系统，他允许你在本机前的`tty1~tty7`当中（无论是文字界面或图形界面）， 可以用一般帐号来关机或重新开机！但某些distributions则在你要关机时，他会要你输入root的密码

- 数据同步写入磁盘： sync

所有的数据都得要被读入内存后才能够被CPU所处理，但是数据又常常需要由内存写回硬盘当中。由于硬盘的速度太慢（相对于内存来说），如果常常让数据在内存与硬盘中来回写入/读出，系统的性能就不会太好。

因此在Linux系统中，为了加快数据的读取速度，所以在默认的情况中， 某些已经载入内存中的数据将不会直接被写回硬盘，而是先暂存在内存当中，如此一来， 如果一个数据被你重复的改写，那么由于他尚未被写入硬盘中，因此可以直接由内存当中读取出来， 在速度上一定是快上相当多的！

不过，如此一来也造成些许的困扰，那就是万一你的系统因为某些特殊情况造成不正常关机（例如停电或者是不小心踢到power）时，由于数据尚未被写入硬盘当中，哇！所以就会造成数据的更新不正常啦！ 那要怎么办呢？这个时候就需要sync这个指令来进行数据的写入动作
啦！ 直接在命令行下输入sync，那么在内存中尚未被更新的数据，就会被写入硬盘中！所以，这个指令在系统关机或重新开机之前， 很重要喔！最好多执行几次！

**虽然目前的 shutdown/reboot/halt 等等指令均已经在关机前进行了 sync 这个工具的调用。**

常见的变成root用户的指令

```
 $ su
 Password:
 [root@study~]# sync
```

**事实上sync也可以被一般帐号使用喔！只不过一般帐号使用者所更新的硬盘数据就仅有自己的数据，不像root可以更新整个系统中的数据了**

- 惯用的关机指令： shutdown
- 由于Linux的关机是那么重要的工作，因此除了你是在主机前面以实体终端机 `（tty1~tty7）` 来登陆系统时， 不论用什么身份都能够关机之外，若你是使用远端管理工具（如通过`pietty`使用ssh服务来从其他计算机登陆主机）， 那关机就只有root有权力而已喔！

我们较常使用的是shutdown这个指令，而这个指令会通知系统
内的各个程序 （processes），并且将通知系统中的一些服务来关闭。shutdown可以达成如下的工作：

- 可以自由选择关机模式：是要关机或重新开机均可；
- 可以设置关机时间: 可以设置成现在立刻关机, 也可以设置某一个特定的时间才关机。
- 可以自订关机讯息：在关机之前，可以将自己设置的讯息传送给线上 user 。
- 可以仅发出警告讯息：有时有可能你要进行一些测试，而不想让其他的使用者干扰，或者是明白的告诉使用者某段时间要注意一下！这个时候可以使用 shutdown 来吓一吓使用者，但却不是真的要关机啦！

```
[root@study ~]# /sbin/shutdown [-krhc] [时间] [警告讯息]
选项与参数：
-k ： 不要真的关机，只是发送警告讯息出去！
-r ： 在将系统的服务停掉之后就重新开机（常用）
-h ： 将系统的服务停掉后，立即关机。 （常用）
-c ： 取消已经在进行的 shutdown 指令内容。
时间 ： 指定系统关机的时间！时间的范例下面会说明。若没有这个项目，则默认 1 分钟后自动进行。
范例：
[root@study ~]# /sbin/shutdown -h 10 'I will shutdown after 10 mins'
Broadcast message from root@study.centos.vbird （Tue 2015-06-02 10:51:34 CST）:
I will shutdown after 10 mins
The system is going down for power-off at Tue 2015-06-02 11:01:34 CST!
```

在执行 shutdown 之后，系统告诉大家，这部机器会在十分钟后关机！并且会将讯息显示在目前登陆者的屏幕前方！ 你可以输入“ shutdown -c ”来取消这次的关机指令。而如果你什么参数都没有加，单纯执行 shutdown 之后， 系统默认会在 1 分钟后进行“关机”的动作喔！我们也提供几个常见的时间参数给你参考

- 重新开机，关机： reboot, halt, `poweroff`

一般在重新开机时，都会下达如下的指令:

```
# sync
# reboot
```

在默认的情况下，这几个指令都会完成一样的工作！（全部的动作都是去调用 `systemctl` 这个重要的管理命令！） 所以，你只要记得其中一个就好了！重点是，你自己习惯即可！

- 实际使用管理工具 `systemctl`关机

```
# systemctl [指令]
指令项目包括如下：
halt 进入系统停止的模式，屏幕可能会保留一些讯息，这与你的电源管理模式有关
poweroff 进入系统关机模式，直接关机没有提供电力喔！
reboot 直接重新开机
suspend 进入休眠模式


systemctl reboot 重新开机
systemctl poweroff 直接关机
```

**总结：**

- 间接断电会造成Linux系统的伤害，所以一个Linux要是作为服务器，最好给它加上不断电的系统来提供稳定的电力

- 不要用Root直接登录用户

- 默认情况下，Linux提供`tty1~tty6`的终端界面。

  在UNIX的术语中，最简单的回答是：
  **终端是文本的输入与输出的环境=tty，控制台其实就是物理的终端（物理的文本的输入与输出的环境。）**而`shell`的本质： **就是命令行解释器**。

  shell，作为用户登录系统时看到的主界面，它主要的用处就是启动别的程序。（我不确定原来的比喻义是指shell是用户的home环境，还是说shell是其他程序在里面跑的环境）。在UNIX圈里，shell 特指命令行shell（并且围绕这这样的描述：  **“输入你想要运行的应用的名字，后面跟着文件或者其他的、应用程序应该据此作出调整的东西（翻译：就是参数啦） ，并按回车键。**”） 其他类型的环境并不使用“shell”这个词，比如说窗口系统涉及了”窗口管理器“(window managers) 和”桌面环境“(desktop environments)，但并不使用”shell“这个词。
  UNIX shell有很多，比较流行的交互式shell包括bash（大多数Linux发行版的默认选择） **`zsh`**（重视功能和可定制性） 还有fish（以简单为主旨）。命令行shell包括了用来组合命令的流程控制结构。除了在交互式提示符下输入命令外，用户还可以编写脚本,大多数的shell都有一套共同的语法。

  终端与shell的区别：

  **输入：终端把键盘的操作转化为控制序列，shell把控制序列转化为命令。在行编辑中，输入历史与输入补全是由shell提供的。**

  终端有时会在自己的行编辑命令中提供历史与输入补全的功能，并且在命令执行时提供line发送到shell中

  **输出**：shell发出指令：展示XXX,把光标移到XXX，终端去响应这些命令。

  命令提示符是纯粹的shell概念。

  应用程序之间复制是由终端提供的，通常用鼠标，或者组合键，比如`Ctrl+SHift+V` 或Shift+Insert。而作业控制——（在后台启动程序，并且管理这些程序）主要是由shell执行：“由shell执行，但是是由终端来处理组合键的，比如`Ctrl+C` 杀死前台程序，或者`Ctrl+Z`终止任务运行”

- 线上说明系统有man及info两个常见的指令；
- **man page说明后面的数字中，1代表一般帐号可用指令，8代表系统管理员常用指令，5代表系统配置文件格式；**
- info page可将一份说明文档拆成多个节点（node）显示，并具有类似超链接的功能，增加易读性
- 系统需正确的关机比较不容易损坏，可使用`shutdown, poweroff`等指令关机。

**Physical console / Virtual console / Terminal 的说明为何？**

**答：实体控制台：实体的屏幕、键盘、鼠标等界面，让你可以使用该配备来操作系统的环境，就称为实体控制台 （Physical console）**

**虚拟控制台：：由系统衍生出的虚拟控制台，你可以通过该虚拟控制台搭配你自己系统的实体配备，来操作远端系统的环境。每个虚拟控制台都是独立运行的。**简单来说就是：虚拟控制台就是你电脑的操作系统虚拟出一个控制台，实际你还是通过你的实体电脑，来控制远程的电脑，这就叫做虚拟控制台。

**终端机：你可以使用该界面获得一个可以操作系统的shell环境。**

**一般来说：把可以与操作系统进行互动的环境，我们叫做terminal**

- 我以文字模式登录Linux时，可以使用几个终端机接口？

  答：一共六个，`tty1~tty6`切换的方式为 `Crtl + Alt + [F1]~[F6]`

- 我想要知道 date 如何使用，应该如何查询？

  答：man date 、info date 、更加详细的还可以在usr/share/doc里面找到。

- 我想要在今天的 `1:30` 让系统自己关机

  答：

  ```
  shutdown -h 1:30
  ```

- 如果我 Linux 的 X Window 突然发生问题而挂掉，但 Linux 本身还是好好的，那么我可以按下哪三个按键来让 X window 重新启动？

  答：`[crtl]+[alt]+[backspace]`

- 若以 X-Window 为默认的登陆方式，那请问如何进入 Virtual console 呢？可以按下 [Ctrl]

  答：`[Alt] + [F2] ~ [F6]` 进入 Virtual console （ 共六个 ）； 而按下 `[Ctrl] + [Alt] + [F1]` 可回到X-Window 的 desktop 中

- **简单说明在 bash shell 的环境下， [tab] 按键的用途？[Tab] 按键可做为命令补齐或文件补齐的功能，与所接的指令位置有关。**
- Linux 提供相当多的线上查询，称为 man page，请问，我如何知道系统上有多少关于`passwd` 的说明？又，可以使用其他的程序来取代man 的这个功能吗？可以利用 `man -f passwd` 来查询，另外，如果有提供 info 的文件数据时 （在 `/usr/share/info/` 目录中） ，则能够利用 `info passwd` 来查询

- man page 显示的内容的文件是放置在哪些目录中？放置在 `/usr/share/man/` 与等中

- 我想知道目前系统有多少指令是以 `bz` 为开头的，可以怎么作？

  答： `bz[tab][tab]`