# **"再"学Git**

> 先吐个槽：
>
> git的命令好难记......
> 这么多命令要记死人啊!!
> **尼玛, 一会这个参数在前, 一会那个参数在后, 根本记不住啊, 现在记住, 过一会又忘了...**
> 真的不好理解, 就不能使用统一的格式和参数吗.
> 文档也说不清楚啊.
> 或者看不懂啊.
> 怪我菜喽......                                                                                                                                                                这里插播一条ZMC学长的著名名言：“菜是原罪”
> 最让人奔溃的是有时候同一个需求可以用好几种命令解决而且有的命令还有别名                                                                        我在网上找到的一些五画八门的命令，竟然都能达成目的，难以找到规律。

那俺为什么还要分享Git呢？是因为 孩子因为请教帅气ZJQ的学长时，学长说：“ 之前教你的Git知识全还给我了？好家伙，那你下周分享Git吧 ”  我的内心: " ........"

### 1.复盘Git的工作原理

Git 主要的目的是通过操纵下面这三棵树来以更加连续的状态记录项目的快照。

我们进入到一个新目录，其中有一个文件。 我们称其为该文件的 v1 版本，将它 标记为蓝色。运行 git init，这会创建一个 Git 仓库，其中的 HEAD 引用指向未创建的分支（master 还不存在）。我们创建一个 file1.txt，那么现在仓库的情况是这样的：

![img](https://camo.githubusercontent.com/1c18122948d4a058db78e04c775498e716895273038e5a03104a514a3deb15de/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383039333132363039382e706e67)

此时，只有工作目录有内容。**现在我们想要提交这个文件，所以用 git add 来获取工作目录中的内容，并将其复制到索引区中**。

![image-20200618093242914](https://camo.githubusercontent.com/bac432f175f0e72a85685f7ac9b22f9ec00c53bcb0b949dd2d11a0008f3d4b18/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383039333234323931342e706e67)

**接着运行 git commit**，它会取得索引中的内容并将它保存为一个永久的快照，然后创建一个指向该快照的提交对象，最后更新 master 来指向本次提交。



![image-20200618093605400](https://camo.githubusercontent.com/24856b533a4f51a0579a9daf331884626f8397ff230a1824ce3f0310da58e5c8/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383039333630353430302e706e67)

现在我们想要对文件进行修改然后提交它。 我们将会经历同样的过程；首先在工作目录中修改文件。 我们称其 为该文件的 v2 版本，并将它标记为红色。

![image-20200618093724948](https://camo.githubusercontent.com/3c9f1e20378d3b4eb19c11a8e0350fc7964c7f643891fdba7290108915fb2974/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383039333732343934382e706e67)

如果现在运行 **git status**，我们会看到文件显示在 “Changes not staged for commit,” “尚未暂存以备提交的变更”![image-20210401191757674](/home/gongna/.config/Typora/typora-user-images/image-20210401191757674.png)

下面并被标记为红 色，因为该条目在索引与工作目录之间存在不同。

 接着我们运行 **git add** 来将它暂存到索引中

![image-20200618093847326](https://camo.githubusercontent.com/3c3465626fb97e34a5c42af5a0fdd7584d1a51b92701c6e13de40ac2f4c12b64/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383039333834373332362e706e67)

再git status显示：“要提交的变更”

![image-20210401191823048](/home/gongna/.config/Typora/typora-user-images/image-20210401191823048.png)

最后git commit 完成提交,此时仓库区 、暂存区、工作区三个地方的最新的文件 40 位长的 16 进制 sha1编号一致。切换分支或克隆的过程也类似。 当**检出(checkout命令)**一个分支时，它会修改 HEAD 指向新的分支引用，**将索引填充为该次提交的快照**，然后将索引的内容复制到工作目录中。

### 2.**官方文档**和**自带命令行帮助**（使用 git --help）

> 这里不得不夸``` man git ```   这个指令了，就是一个活脱脱的Linux本地帮助系统.

### 3.关注提交信息命令

> 一次 git commit 形成了一次提交历史，使用一个非常长的哈希码表示，形式是一串字母加数字，这个哈希码根据文件的大小，创建提交的时间，提交者信息等等综合生成，几乎不会有哈希冲突。即便是不同的人在不同的电脑上提交，生成的哈希码也是不同的，所以可以保证多人开发的时候的全局唯一性。

#### `git commit `

**如果使用 git commit 而不带 -m 参数，那么会进入详细提交信息的编辑页面，默认的编辑器在 MacOS 和 Linux 下是 Vim 编辑器，第一行是提交信息，第二行是空行，（包含）第三行往下是详细说明信息，如果本次提交需要附加的信息特别多，应该在编辑器里详细写明。**

- 加快 Reviewing Code 的过程
- 帮助我们写好 release note
- 5 年后帮你快速想起来某个分支，tag 或者 commit 增加了什么功能，改变了哪些代码
- 让其他的开发者在运行 `git blame` 的时候想跪谢
- 总之一个好的提交信息，会帮助我们提高项目的整体质量

一个简单的示例（ ZeroGravity的登录功能文件提交时写的信息）：

```
Redirect user to the requested page after login 登录后将用户重定向到请求的页面 

http://39.102.42.156:8080/api/v1/auth/api/v1/login 请求资源的URL

Users were being redirected to the home page after login, which is less
useful than redirecting to the page they had originally requested before
being redirected to the login form.登录后将用户重定向到主页，这要少一些
比重定向到他们之前最初请求的页面有用
被重定向到登录表单

* Store requested path in a session variable将请求的路径存储在会话变量中
* Redirect to the stored location after successfully logging in the user成功登录用户后重定向到存储位置 

```

基本要求：

- 第一行应该少于 50 个字。 **随后是一个空行** 第一行题目也可以写成：`Fix issue #8976`
- 喜欢用 vim 的哥们把下面这行代码加入 *.vimrc* 文件中，来检查拼写和自动折行

```
autocmd Filetype gitcommit setlocal spell textwidth=72
```

- 永远不在 `git commit` 上增加 `-m <msg>` 或 `--message=<msg>` 参数，而单独写提交信息

一个不好的例子 `git commit -m "Fix login bug"   没错说的就是我.....

* 提交信息中包含一个简短的故事，能让别人更容易理解你的项目
* 注释最好包含一个连接指向我们项目的 issue/story/card。一个完整的连接比一个 issue numbers 更好

### 4.关注追溯文件历史的命令

#### `git blame` 

追溯一个指定文件的历史修改记录

意义：查看自己或同伴都干了什么

高级用法：

直接输入`git blame -h`会有一大推的命令选项和解释,感兴趣可以自己去看

![image-20210401201249799](/home/gongna/.config/Typora/typora-user-images/image-20210401201249799.png)

![image-20210401201916857](/home/gongna/.config/Typora/typora-user-images/image-20210401201916857.png)

#### `git log` 

git  log 查看一个文件的提交历史, 也可以查看整个仓库的的提交历史。

毫不夸张的说， git log 的命令是我们使用最高频的命令之一。然而 git log 本身输出的格式不是我们想要的，如图所示：

```
commit b8b2b4985abf4c132abbfea3c5ffcf23e97db9ef (HEAD -> master)
Author: notfreshmbp <1195056983@qq.com>
Date:   Fri Jun 26 19:35:51 2020 +0800

    add 5.txt

    This is a test message to show people that this is the detailed message.

commit 4e7f6e354179c19e5adfff6515a2982f732c02e2
Author: notfreshmbp <1195056983@qq.com>
Date:   Thu Jun 18 10:42:32 2020 +0800

    update 1.txt
```

当我们想要查看更加详细的、可视化的信息的时候，我们可以在终端执行如下操作：

```
git config --global alias.l "log --color --graph --all --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s%Creset %Cgreen(%cd) %C(bold blue)<%an>%Creset' --abbrev-commit --date='format:%Y-    %m-%d %H:%M:%S'"
```

这条命令为 git log 配置了一个别名，附带了很多的参数，我们可以使用 git l 查看效果：![image-20210401222941359](/home/gongna/.config/Typora/typora-user-images/image-20210401222941359.png)

git log 还支持 `grep` 命令查询提交信息（只支持提交信息查询），使用 `git log *--all --grep=KEY_WORD` 可以搜索某些提交一个简单的例子：`git log --grep'fix:swagger'`![image-20210401223453161](/home/gongna/.config/Typora/typora-user-images/image-20210401223453161.png)



| 符号    | 含义                               |
| ------- | ---------------------------------- |
| .       | 匹配任意单个字符（不包括\n）       |
| ^       | 匹配行首                           |
| $       | 匹配行尾                           |
| [ ]     | 匹配中括号内的单个字符             |
| [^ ]    | 匹配非中括号内的单个字符           |
| *       | 星号之前的字符出现0次或多次        |
| \{n\}   | 前一个字符刚好出现n次              |
| \{n,\}  | 前一个字符至少出现n次              |
| \{n,m\} | 前一个字符至少出现n次，至多出现m次 |

### 5.关注分支的命令

什么是分支？分支, 就是一个区域, 有起点, 有终点, 起点一个 `CommitA`, 终点 `CommitB`.

![image-20200618095257616](https://camo.githubusercontent.com/8b62c7324d833d696f8764312787e2403e9698a8bf5a8afe56bc82a26e733cc4/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383039353235373631362e706e67)

上图是分支的工作原理，

#### `git checkout 分支名` 

**切换分支的双标**：

**新增的文件**：没有被保存到仓库区，无论怎么切换分支，都是可以的，

**修改的文件**：但是我们在工作区修改了一个已经存在与仓库区的文件A，那么我们在切换分支前，应该提交或者暂存（stash命令）修改的内容。否则就会切换失败。失败原因是切换分支会导致修改被覆盖。

### 6.关注撤销提交的命令

####       `git reset`

![image-20200618100418746](https://camo.githubusercontent.com/613a9bc9a76dbdc26215f2bbb9c8822035e60ad6163d5d9e6303d8a26c1ee719/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383130303431383734362e706e67)

`git reset `的工作原理

**第 1 步：移动 HEAD** 

reset 做的第一件事是移动 HEAD 。reset 移动HEAD 指向的分支。 这意味着如果 HEAD 指向 master 分支（例如，你正在 master 分支上）通过`git reset HEAD^ login.go`意为回退login.go 文件的上一个版本，此时就会变成这样

![image-20200618101407284](https://camo.githubusercontent.com/203f1479854c8e71171cefcf7a2942deb51a23949480ea649c6be24283fd882a/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383130313430373238342e706e67)

`git reset --soft HEAD`   soft用来回退到某个版本

`git reset HEAD^ `     回退所有内容到上一个版本

`git reset HEAD^login.go` 回退login.go 文件的版本到上一个版本

**第 2步：更新索引（--mixed）**

首先什么是Git索引？

Git索引是一个在我们的工作目录和项目仓库间的暂存区(staging area). 有了它, 你可以把许多内容的修改一起提交(commit). 如果你创建了一个提交(commit), 那么提交的是当前索引(index)里的内容, 而不是工作目录中的内容.

因此只要执行了`git add`，就算工作目录中的文件被误删除，也不会引起文件的丢失，因为文件已经被存入Git对象，并使用索引进行定位。

那问题又来了，什么是Git对象？好问题！！！

那先来了解一下Git的对象模型叭.......

**仓库结构**

git 分布式的一个重要体现是 git 在本地是有一个完整的 git 仓库也就是 .git 文件目录，(**在Ubuntu系统下这个.git文件通常是被隐藏的，可以通过`ls -a`**这个命令查看)通过这个仓库，git 就可以完全离线化操作。在这个本地化的仓库中存储了 git 所有的模型对象。下面是 git 仓库的 tree 和相关说明：

![image.png](https://pic4.zhimg.com/80/v2-e75b2f39b6f7720b15bbf5fd6fe2928b_720w.png)

我们可以通过`git ls-files --stage`命令看到仓库中每一个文件及其所对应的文件对象，也可以直接通过查看二进制索引文件的方式来了解更多信息（尽管过于硬核......不过你要是能看的懂就当我从未说过这句话）

git 主要有四个对象，分别是 Blob，Tree， Commit， Tag 他们都用 SHA-1 进行命名。

**「blob 对象」**：接下来，了解一下什么是 blob 对象：binary large object，字面意思很容易理解，就是一个目标对象，其实在 Git 中，就是一个文件经过压缩后的内容。也就是说，在 Git 中，每个文件都是一个 blob 对象，也就是说，利用这个 blob 对象可以还原出原始的文件。记住这一点，每次你 add 以后，git 就会对你修改了的文件生成一个 blob 对象，也就是说，不仅仅每个文件都是一个 blob 对象，文件的不同版本也会是一个 blob 对象。

**「tree对象」**：然后是 tree 对象。顾名思义，就是一个树形结构图。在 Git 中，不会保存每个文件夹（也可以理解为目录，虽然在 Git 中它们并没有上下级的关系），但是为了记录文件之间的关系，会用 tree 对象来表示这种关系。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/6xW2D94qBjHSA4tEw7tLls92qyAnLGG2iaqH1xvXurxTibCFsQOYLv5cKjSqibgb4JeFvpgssPXeVWic96bv7iaudkQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**「commit 对象」**：commit 这个命令我们一定很熟悉，但是它是什么呢？如何产生作用？奥秘来自于 commit 对象本身。每个 commit 对象本身包含了这些信息：

- 某个 tree 对象；
- 本次 commit 的时间；
- 本次 commit 的作者；
- 本次 commit 的信息。

这里我们可以提起一个概念（或许在很多 Git 教程中都提到了，但是你没有完全理解），**「Git的工作原理是创建文件快照，而不是像其他VCS那样增加差异。」**

其中的奥秘就在这个 commit 对象的内容，它指向的 tree 对象一般来说，就是一个根文件的状态，通过 tree 对象本身再指向其它 tree 对象或者 blob 对象，就保存了当前所有文件的状态。也就是通过一个 commit 对象，我们就保存了一份当前文件系统的快照。

此外，还需要了解到一个细节，除了第一个 commit 对象外，每个 commit 对象中本身还会指向前一个 commit 对象。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/6xW2D94qBjHSA4tEw7tLls92qyAnLGG2mtQibB5WFicl5FUclh91q6YR7mXULTCGsAyv9v0DIv2IKyuxPDlibAW1g/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**「tag 对象」**：tag 对象就比较简单了，具体 git tag 的命令我们就不去介绍了，本质上就是对当前 commit 添加一些描述信息，所以我们也可以很容易的去理解它的原理，就是指向一个 commit 对象，此外再附加一些执行 git tag 命令时我们添加的那些备注信息，备注人，时间等等。







~~索引文件存储在`.git/index`中，`.git`目录存储有该仓库所有的文件，而其他目录只是根据该目录所重建出的镜像。`.git/index`是一个二进制文件，如果直接使用文本编辑器打开会发现全是乱码。在Linux/MacOS中，我们可以使用`hexdump`来以十六进制方式显示该文件：~~对不起跑偏了......

```bash
#  hexdump -C .git/index
00000000  44 49 52 43 00 00 00 02  00 00 00 34 5d 45 6b 72  |DIRC.......4]Ekr|
00000010  00 00 00 00 5d 45 6b 72  00 00 00 00 00 00 00 00  |....]Ekr........|
00000020  04 51 c4 fe 00 00 81 a4  00 00 01 f5 00 00 00 14  |.Q..............|
00000030  00 00 00 78 d7 17 f9 f6  5d 8c 51 af e0 74 d2 f9  |...x....].Q..t..|
00000040  c7 99 e3 43 6a d1 72 fd  80 0a 2e 67 69 74 69 67  |...Cj.r....gitig|
00000050  6e 6f 72 65 00 00 00 00  00 00 00 00 5c 16 88 81  |nore........\...|
00000060  00 00 00 00 5b bd a4 c6  00 00 00 00 01 00 00 04  |....[...........|
00000070  00 0e 74 81 00 00 81 a4  00 00 01 f5 00 00 00 14  |..t.............|
00000080  00 00 02 dd 7a 4e b6 57  21 37 f4 60 11 ca 25 6a  |....zN.W!7.`..%j|
00000090  ba 51 4c b7 18 78 f7 3a  00 0c 43 48 41 4e 47 45  |.QL..x.:..CHANGE|
000000a0  4c 4f 47 2e 6d 64 00 00  00 00 00 00 5c 16 88 7e  |LOG.md......\..~|
000000b0  00 00 00 00 5b f5 4c bf  00 00 00 00 01 00 00 04  |....[.L.........|
......
```



这一部分包含了关于`.gitignore`文件的元数据，足够我们分析。

当我们在运行 git commit 时，Git 会创建一个新的提交，并移动 HEAD 所指向的分支来使其指向该提交。 当我们将它 reset 回HEAD~（HEAD 的父结点）时，其实就是把该分支移动回原来的位置，而不会改变索引和工作目录。现在我们可以更新索引并再次运行 git commit 来完成 git commit --amend 所要做的事情了，那么问题来了：我仓库区发生了变化我回到原来的版本，那么我暂存区呢？我暂存区呢？我原来暂存区的东西呢？东西呢？东西呢？

答案是：使用**git reset --mixed HEAD**  **Git会用仓库区的HEAD指针指向的版本内容覆盖暂存区**我们回到了所有 git add 和 git commit 的命令执行之前。使用**git reset --soft HEAD** 回退到git commit之前，此时处在暂存区。（即执行git add 命令后）

**第 3 步：更新工作目录（--hard）**

![image-20200618102225932](https://camo.githubusercontent.com/565d83d210db52a965866c3600dd8cbd4f280df71b73017e284a142255a2abae/68747470733a2f2f7479706f72612d313235363939313738312e636f732e61702d6265696a696e672e6d7971636c6f75642e636f6d2f755069632f696d6167652d32303230303631383130323232353933322e706e67)

**--hard 标记是 reset 命令唯一的危险用法**，它也是 Git 会真正地销毁数据的仅有的几个操作之一。其他任何形式的 reset 调用都可以轻松撤消，但是 --hard 选项不能，因为它强制覆盖了工作目录中的文件。在这种特殊情况下，我们的 Git 数据库中的一个提交内还留有该文件的 v3 版本，我们可以通过 reflog 来找回它。**但是若该文件还未提交，Git 仍会覆盖它从而导致无法恢复**。三者的改变全都丢失，即代码的修改内容丢失

### 7.关注分支合并的命令

#### `fast forward`

![img](https://pic2.zhimg.com/v2-0a0431c992211561f14ee66f1cf0ea89_b.webp)

举个例子，分支 master 含有 c1, c2, c3,共3个提交， 分支 dev 含有 c1, c2,c3, c4 共4个提交，即 dev完全包含 master，如果使用 git merge dev master, 意思就是让 master 合并 dev 分支了里的内容。merge 完成后，master也有了 c1，c2, c3,c4 共4个分支，而dev没有任何变化。这种合并叫做 fast forward, 可以理解为“快速追上”的策略。

#### `No-fast-foward (—no-ff)`

![img](https://pic3.zhimg.com/v2-d5be0dfa20f8a7c57f99f2b48b521bda_b.webp)

再举个例子，分支 master 含有 c1, c2, c3,共3个提交， 分支 dev 含有 c1, c2, c4  共3个提交，即 dev 分支有包含 master 分支没有的内容，如果使用 git merge dev master, 意思就是让 master 合并 dev 分支了里的内容。merge 完成后，master也有了 c1，c2, c3, c5, , 共4个分支，c5里**<u>包含</u>**着 c4 提交的内容，而dev没有任何变化。这种合并为合并了分叉内容，会自动创建一个提交，例如c5分支。

#### `git rebase`

![image-20210402110308986](/home/gongna/.config/Typora/typora-user-images/image-20210402110308986.png)

![img](https://pic2.zhimg.com/v2-6b8427b4baf6cdfb08b852ab1cdb4941_b.webp)

可将一个分支的修改融入到另一个分支的方式是执行` git rebase`。

`git rebase `会将当前分支的提交复制到指定的分支之上。如上图,现在我们在 dev 分支上获取了 master 分支上的所有修改。

变基与合并有一个重大的区别：Git 不会尝试确定要保留或不保留哪些文件。我们执行 `rebase` 的分支总是含有我们想要保留的最新近的修改！这样我们不会遇到任何合并冲突，而且可以保留一个漂亮的、线性的 Git 历史记录。

打个比方, 分支 master 包含 C1,C2,C3, 分支 branchX 包含 C2, C4, C5.
采用普通的merge策略, 会新建一个提交C6, 让C3, C5都指向C6. C6的内容包含master和branchX都有的内容.
采用rebase, 命令格式为 git rebase BASE_BRANCH TO_REPLAY_BRANCH
在这里使用 `git rebase master branchX`就是把 C4-C5在C3后续上, 然后branchX指针指向C5, master位置不变, 也就是说分支的指向位置都没变, 只不过提交历史的形状变了.
如果没有冲突顺利合并, 如果有冲突解决冲突.
如果 只写git rebase BASE_BRANCH, 那么git就会假定当前分支就是TO_REPLAY_BRANCH.

总结: 什么是rebase? 变基, 变谁的基? 就拿刚才一个具体的命令来说吧, 当前分支是branchX, 执行`git rebase master-- branchX`, 或者`git rebase master`, 我们这么理解, 变得就是branchX的基, 基, 英语base, 我们不妨理解为起点. 重新修改branchX的起点, 之前branchX的起点是C2, 现在我们让它的起点改为C3.

第一种场景:
如果我想手动的自己处理分支, 比如远程仓库已经更新, 我执行如下操作: step1: git fetch origin
step2: git rebase origin/master master :这句话的意思是以origin/master 为base, 把master放到origin/master的末端 这样就可以把提交历史捋直了, 否则就会出现一次merge操作. 上面两句等同于 git pull --rebase: 把本地的放到远程的上面, 让本地的提交包括远程的提交.

> 有一种错误的rebase, 在step2, 执行 git rebase master origin/master, 因为origin/master是不可以移动的, git会自动拷贝一个提交历史放到master的前面, 这样一来, 尽管内容是相同的, 可是本地的分支包含了远程分支没有的提交历史, 中间夹了几个本地提交的分支, 就会导致无法推送. 感兴趣的不妨试一下.

#### 





### 8.关注暂时保存的命令

### `git stash` 

`git stash`的每个存储单元在工作仓库中是通过`commit`的形式由树结构展示的.它的根节点是`HEAD`所在的提交.以下图标来表示`stash`和`branch`的关系.

![img](https://upload-images.jianshu.io/upload_images/2291019-1781521bbb6e05a9.png?imageMogr2/auto-orient/strip|imageView2/2)

`H`是`HEAD`提交.`I`是存储单元的提交.`W`是工作树中的提交.通过几个实际的示例来了解一下`git stash`的强大.

**开发到一半,同步远端代码**

当我们开发进行到一半,但是代码还不想进行提交 ,然后需要同步去关联远端代码时.如果我们本地的代码和远端代码没有冲突时,可以直接通过`git pull`解决.但是如果可能发生冲突怎么办.直接`git pull`会拒绝覆盖当前的修改.

**工作流被打断,需要先做别的需求**

当开发进行到一半,老板过来跟你说"线上有个bug,你现在给我改好,不然扣你鸡腿".当然,你可以开一个新的分支,把当前代码提交过去,回头再merge,具体代码如下

```bash
繁琐的工作流示例
# ... hack hack hack ...
 git checkout -b my_wip
 git commit -a -m "WIP"
 git checkout master
 edit emergency fix
 git commit -a -m "Fix in a hurry"
 git checkout my_wip
 git reset --soft HEAD^
# ... continue hacking ...
```

**通过  git stash  简化流程**

```csharp
正确姿势
# ... hack hack hack ...
 git stash        //保存开发到一半的代码
 edit emergency fix
 git commit -a -m "Fix in a hurry"
 git stash pop   //将代码追加到最新的提交之后
# ... continue hacking ...
```

这次的分享就这些了.....蟹蟹大家的聆听~