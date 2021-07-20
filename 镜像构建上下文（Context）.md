0vp(argv[2], &argv[2]);
errExit("execvp");
}Docker 构建镜像

### 镜像构建上下文（Context）

```
docker build` 命令最后有一个 `.
```

`.` 表示当前目录，而 `Dockerfile` 就在当前目录，因此不少初学者以为这个路径是在指定 `Dockerfile` 所在路径，这么理解其实是不准确的。如果对应上面的命令格式，你可能会发现，这是在指定 **上下文路径**。那么什么是上下文呢？

**首先我们要理解 `docker build` 的工作原理。Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具。Docker 的引擎提供了一组 REST API，被称为 [Docker Remote API](https://docs.docker.com/develop/sdk/)，而如 `docker` 命令这样的客户端工具，则是通过这组 API 与 Docker 引擎交互，从而完成各种功能。因此，虽然表面上我们好像是在本机执行各种 `docker` 功能，但实际上，一切都是使用的远程调用形式在服务端（Docker 引擎）完成。也因为这种 C/S 设计，让我们操作远程服务器的 Docker 引擎变得轻而易举。**





> 根据docker build 命令构建镜像，其实是在Docker的“后端”进行构建的。一切构建的操作都是在于Docker的后端进行通讯。

当我们进行镜像构建的时候，并非所有定制都会通过 `RUN` 指令完成，经常会需要将一些本地文件复制进镜像，比如通过 `COPY` 指令、`ADD` 指令等。而 `docker build` 命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎中构建的。那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢

 当我们用Docker构建镜像时，我们需要指定构建镜像上下文的路径，docker build 命令得知这个路径以后，会把这个路径下的所有内容打包，然后上传给Docker的后端--"也就是引擎"

当Docker引擎收到这个上下文的包之后，展开就会获得构建镜像所需要的一切文件。

如果在 `Dockerfile` 中这么写

```
COPY ./package.json /app/
```

这并不是要复制执行 `docker build` 命令所在的目录下的 `package.json`，也不是复制 `Dockerfile` 所在目录下的 `package.json`，而是复制 **上下文（context）** 目录下的 `package.json`。

因此，`COPY` 这类指令中的源文件的路径都是*相对路径*。这也是初学者经常会问的为什么 `COPY ../package.json /app` 或者 `COPY /opt/xxxx /app` 无法工作的原因，因为这些路径已经超出了上下文的范围，Docker 引擎无法获得这些位置的文件。如果真的需要那些文件，应该将它们复制到上下文目录中去

现在就可以理解刚才的命令 `docker build -t nginx:v3 .` 中的这个 `.`，实际上是在指定上下文的目录，`docker build` 命令会将该目录下的内容打包交给 Docker 引擎以帮助构建镜像。

如果观察 `docker build` 输出，我们其实已经看到了这个发送上下文的过程：

```
$ docker build -t nginx:v3 .

Sending build context to Docker daemon 2.048 kB

......
```

理解构建上下文对于镜像构建是很重要的，避免犯一些不应该的错误。比如有些初学者在发现 `COPY /opt/xxxx /app` 不工作后，于是干脆将 `Dockerfile` 放到了硬盘根目录去构建，结果发现 `docker build` 执行后，在发送一个几十 GB 的东西，极为缓慢而且很容易构建失败。那是因为这种做法是在让 `docker build` 打包整个硬盘，这显然是使用错误。

一般来说，应该会将 `Dockerfile` 置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用 `.gitignore` 一样的语法写一个 `.dockerignore`，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的。

那么为什么会有人误以为 `.` 是指定 `Dockerfile` 所在目录呢？这是因为在默认情况下，如果不额外指定 `Dockerfile` 的话，会将上下文目录下的名为 `Dockerfile` 的文件作为 Dockerfile。

这只是默认行为，实际上 `Dockerfile` 的文件名并不要求必须为 `Dockerfile`，而且并不要求必须位于上下文目录中，比如可以用 `-f ../Dockerfile.php` 参数指定某个文件作为 `Dockerfile`。

当然，一般大家习惯性的会使用默认的文件名 `Dockerfile`，以及会将其置于镜像构建上下文目录中。



```
# $env:DOCKER_BUILDKIT=0

# export DOCKER_BUILDKIT=0



$ docker build -t hello-world https://github.com/docker-library/hello-world.git#master:amd64/hello-world



Step 1/3 : FROM scratch

 --->

Step 2/3 : COPY hello /

 ---> ac779757d46e

Step 3/3 : CMD ["/hello"]

 ---> Running in d2a513a760ed

Removing intermediate container d2a513a760ed

 ---> 038ad4142d2b

Successfully built 038ad4142d2b
```



这行命令指定了构建所需的 Git repo，并且指定分支为 `master`，构建目录为 `/amd64/hello-world/`，然后 Docker 就会自己去 `git clone` 这个项目、切换到指定分支、并进入到指定目录后开始构建。

如果所给出的 URL 不是个 Git repo，而是个 `tar` 压缩包，那么 Docker 引擎会下载这个包，并自动解压缩，以其作为上下文，开始构建。

```
$ docker build http://server/context.tar.gz
```

## 从标准输入中读取 Dockerfile 进行构建



```
docker build - < Dockerfile
cat Dockerfile | docker build -

```

**如果标准输入传入的是文本文件，则将其视为 `Dockerfile`，并开始构建。这种形式由于直接从标准输入中读取 Dockerfile 的内容，它没有上下文，因此不可以像其他方法那样可以将本地文件 `COPY` 进镜像之类的事情。**

## 从标准输入中读取上下文压缩包进行构建



```
$ docker build - < context.tar.gz
```

如果发现标准输入的文件格式是 `gzip`、`bzip2` 以及 `xz` 的话，将会使其为上下文压缩包，直接将其展开，将里面视为上下文，并开始构建。

> **一个进程的每种 Linux Namespace,都在它对应的 /proc/[进程号]/ns 下有一个对**
> **应的虚拟文件,并且链接到一个真实的 Namespace 文件上**。

有了这样一个可以“hold 住”所有 Linux Namespace 的文件,我们就可以对 Namespace 做
一些很有意义事情了,比如:加入到一个已经存在的 Namespace 当中。

而这个操作所依赖的,乃是一个名叫 setns() 的 Linux 系统调用。它的调用方法,我可以用如下
一段小程序为你说明:

```
#define _GNU_SOURCE
#include <fcntl.h>
#include <sched.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#define errExit(msg) do { perror(msg); exit(EXIT_FAILURE);} while (0)
int main(int argc, char *argv[]) {
int fd;
fd = open(argv[1], O_RDONLY);
if (setns(fd, 0) == -1) {
errExit("setns");
}
execvp(argv[2], &argv[2]);
errExit("execvp");
}
```


**这段代码功能非常简单:它一共接收两个参数,第一个参数是 argv[1],即当前进程要加入的
Namespace 文件的路径,比如 /proc/25686/ns/net;而第二个参数,则是你要在这个
Namespace 里运行的进程,比如 /bin/bash**

这段代码的的核心操作,则是通过 open() 系统调用打开了指定的 Namespace 文件,并把这个
文件的描述符 fd 交给 setns() 使用。在 setns() 执行后,当前进程就加入了这个文件对应的
Linux Namespace 当中了。

```
$ gcc -o set_ns set_ns.c
$ ./set_ns /proc/25686/ns/net /bin/bash
$ ifconfig
eth0
Link encap:Ethernet
HWaddr 02:42:ac:11:00:02
inet addr:172.17.0.2
Bcast:0.0.0.0
Mask:255.255.0.0
inet6 addr: fe80::42:acff:fe11:2/64 Scope:Link
UP BROADCAST RUNNING MULTICAST
MTU:1500
Metric:1
RX packets:12 errors:0 dropped:0 overruns:0 frame:0
TX packets:10 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:0
RX bytes:976 (976.0 B)
lo
TX bytes:796 (796.0 B)
Link encap:Local Loopback
inet addr:127.0.0.1
Mask:255.0.0.0
inet6 addr: ::1/128 Scope:Host
UP LOOPBACK RUNNING
MTU:65536
Metric:1
RX packets:0 errors:0 dropped:0 overruns:0 frame:0
TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:0 (0.0 B)
TX bytes:0 (0.0 B)

```



	正如上所示,当我们执行 ifconfig 命令查看网络设备时,我会发现能看到的网卡“变少”了:
只有两个。而我的宿主机则至少有四个网卡。这是怎么回事呢?
实际上,在 setns() 之后我看到的这两个网卡,正是我在前面启动的 Docker 容器里的网卡。也
就是说,我新创建的这个 /bin/bash 进程,由于加入了该容器进程(PID=25686)的 Network
Namepace,它看到的网络设备与这个容器里是一样的,即:/bin/bash 进程的网络设备视
图,也被修改了。
	而一旦一个进程加入到了另一个 Namespace 当中,在宿主机的 Namespace 文件上,也会有
所体现。在宿主机上,你可以用 ps 指令找到这个 set_ns 程序执行的 /bin/bash 进程,其真实的 PID 是28499:

```
# 在宿主机上
ps aux | grep /bin/bash
root
28499
0.0
0.0 19944
3612 pts/0
S
14:15
0:00 /bin/bash
```

```
这时,如果按照前面介绍过的方法,查看一下这个 PID=28499 的进程的 Namespace,你就会
发现这样一个事实:
$ ls -l /proc/28499/ns/net
lrwxrwxrwx 1 root root 0 Aug 13 14:18 /proc/28499/ns/net -> net:[4026532281]
$ ls -l
/proc/25686/ns/net
lrwxrwxrwx 1 root root 0 Aug 13 14:05 /proc/25686/ns/net -> net:[4026532281]

在 /proc/[PID]/ns/net 目录下,这个 PID=28499 进程,与我们前面的 Docker 容器进程
(PID=25686)指向的 Network Namespace 文件完全一样。这说明这两个进程,共享了这个
名叫 net:[4026532281] 的 Network Namespace

```

**此外,Docker 还专门提供了一个参数,可以让你启动一个容器并“加入”到另一个容器的
Network Namespace 里,这个参数就是 -net,比如:$ docker run -it --net container:4ddf4638572d busybox ifconfig**

这样,我们新启动的这个容器,就会直接加入到 ID=4ddf4638572d 的容器,也就是我们前面
的创建的 Python 应用容器(PID=25686)的 Network Namespace 中。所以,这里 ifconfig
返回的网卡信息,跟我前面那个小程序返回的结果一模一样,你也可以尝试一下。

而如果我指定–net=host,就意味着这个容器不会为进程启用 Network Namespace。这就意
味着,这个容器拆除了 Network Namespace 的“隔离墙”,所以,它会和宿主机上的其他普
通进程一样,直接共享宿主机的网络栈。这就为容器直接操作和使用宿主机网络提供了一个渠
道。



## docker commit

docker commit,实际上就是在容器运行起来后,把最上层的“可读写层”,加上原先容器镜
像的只读层,打包组成了一个新的镜像。当然,下面这些只读层在宿主机上是共享的,不会占用
额外的空间。

而由于使用了联合文件系统,你在容器里对镜像 rootfs 所做的任何修改,都会被操作系统先复
制到这个可读写层,然后再修改。这就是所谓的:Copy-on-Write。

有了新的镜像,我们就可以把它推送到 Docker Hub 上了:

```
$ docker push geektime/helloworld:v2
```

我在企业内部,能不能也搭建一个跟 Docker Hub 类似的镜像上传
系统呢?
当然可以,这个统一存放镜像的系统,就叫作 Docker Registry。感兴趣的话,你可以查看
Docker 的官方文档,以及VMware 的 Harbor 项目。

前面我已经介绍过,容器技术使用了 rootfs 机制和 Mount Namespace,构建出了一个同宿主
机完全隔离开的文件系统环境。这时候,我们就需要考虑这样两个问题:
1. 容器里进程新建的文件,怎么才能让宿主机获取到?
2. 宿主机上的文件和目录,怎么才能让容器里的进程访问到?

这正是 Docker Volume 要解决的问题:Volume 机制,允许你将宿主机上指定的目录或者文
件,挂载到容器里面进行读取和修改操作。

在 Docker 项目里,它支持两种 Volume 声明方式,可以把宿主机目录挂载进容器的 /test 目
录当中:

```
$ docker run -v /test ...
$ docker run -v /home:/test ...
```

而这两种声明方式的本质,实际上是相同的:都是把一个宿主机的目录挂载进了容器的 /test 目
录。



* 在第一种情况下,由于你并没有显示声明宿主机目录,那么 Docker 就会默认在宿主机
  上创建一个临时目录 /var/lib/docker/volumes/[VOLUME_ID]/_data,然后把它挂载到容器的/test 目录上。

* 第二种情况下,Docker 就直接把宿主机的 /home 目录挂载到容器的 /test
  目录上。

  

**那么,Docker 又是如何做到把一个宿主机上的目录或者文件,挂载到容器里面去呢?难道又是Mount Namespace 的黑科技吗?实际上,并不需要这么麻烦。
在《白话容器基础(三):深入理解容器镜像》的分享中,我已经介绍过,当容器进程被创建之
后,尽管开启了 Mount Namespace,但是在它执行 chroot(或者 pivot_root)之前,容器进
程一直可以看到宿主机上的整个文件系统**

**而宿主机上的文件系统,也自然包括了我们要使用的容器镜像。这个镜像的各个层,保存在
/var/lib/docker/aufs/diff 目录下,在容器进程启动后,它们会被联合挂载在
/var/lib/docker/aufs/mnt/ 目录中,这样容器所需的 rootfs 就准备好了。**

**所以,我们只需要在 rootfs 准备好之后,在执行 chroot 之前,把 Volume 指定的宿主机目录
(比如 /home 目录),挂载到指定的容器目录(比如 /test 目录)在宿主机上对应的目录(即
/var/lib/docker/aufs/mnt/[可读写层 ID]/test)上,这个 Volume 的挂载工作就完成了**

**更重要的是,由于执行这个挂载操作时,“容器进程”已经创建了,也就意味着此时 Mount
Namespace 已经开启了。所以,这个挂载事件只在这个容器里可见**你在宿主机上,是看不见
容器内部的这个挂载点的。这就保证了容器的隔离性不会被 Volume 打破。

> 注意:这里提到的 " 容器进程 ",是 Docker 创建的一个容器初始化进程
> (dockerinit),而不是应用进程 (ENTRYPOINT + CMD)。dockerinit 会负责完成
> 根目录的准备、挂载设备和目录、配置 hostname 等一系列需要在容器内进行的
> 初始化操作。最后,它通过 execv() 系统调用,让应用进程取代自己,成为容器里
> 的 PID=1 的进程。



在Linux 内核中，绑定挂载实际上是一个 inode 替换的过程。在Linux 操作系统中,inode 可以理解为存放文件内容的“对象”,而 dentry,也叫目录项,就是访问这个 inode 所使用的“指针”

这句话的意思是说：在Linux里面目录其实是一个指向文件的指针，当我们把一个目录挂载在另外一个目录上其实就是相当于，让这两个（目录）指针指向了同一个文件，当我们对新挂载的目录进行操作时 ，其实就是相当于对这个目录新指向的文件资源进行操作。但是当我们进行umount操作之后，这个新挂载的指针又会重新指向它原来指向的文件。

所以,在一个正确的时机,进行一次绑定挂载,Docker 就可以成功地将一个宿主机上的目录或
文件,不动声色地挂载到容器中，这样,进程在容器里对这个 /test 目录进行的所有操作,都实际发生在宿主机的对应目录(比如,/home,或者 /var/lib/docker/volumes/[VOLUME_ID]/_data)里,而不会影响容器镜像的内容。

> mount --bind /home /test,会将 /home 挂载到 /test 上,也就是把后者指向前者的文件资源。对后者的操作就是对前者的操作。

所以说，当我们对把宿主机上的文件挂载在容器里某个目录下，对容器里这个目录的操作，就等价于对宿主机文件的操作。这样,进程在容器里对这个 /test 目录进行的所有操作,都实际发生在宿主机的对应目录(比如,/home,或者 /var/lib/docker/volumes/[VOLUME_ID]/_data)里,而**不会影响容器镜像**的内容。打破了次元壁。

**那么,这个 /test 目录里的内容,既然挂载在容器 rootfs 的可读写层,它会不会被 docker
commit 提交掉呢?也不会。容器的镜像操作,比如 docker commit,都是发生在宿主
机空间的。而由于 Mount Namespace 的隔离作用,宿主机并不知道这个绑定挂载的存在。所以,在宿主机看来,容器中可读写层的 /test 目录(/var/lib/docker/aufs/mnt/[可读写层
ID]/test),始终是空的。**

不过,由于 Docker 一开始还是要创建 /test 这个目录作为挂载点,所以执行了 docker
commit 之后,你会发现新产生的镜像里,会多出来一个空的 /test 目录。毕竟,新建目录操
作,又不是挂载操作,Mount Namespace 对它可起不到“障眼法”的作用。

![image-20210707194048883](/home/gongna/snap/typora/39/.config/Typora/typora-user-images/image-20210707194048883.png)

**我们可以看到,Kubernetes 项目的架构,跟它的原型项目 Borg 非常类似,都由 Master 和 Node两种节点组成,而这两种角色分别对应着控制节点和计算节点。其中,控制节点,即 Master 节点,由三个紧密协作的独立组件组合而成,它们分别是负责 API 服
务的 kube-apiserver、负责调度的 kube-scheduler,以及负责容器编排的 kube-controller-manager。整个集群的持久化数据,则由 kube-apiserver 处理后保存在 Ectd 中。而计算节点上最核心的部分,则是一个叫作 kubelet 的组件。**

**在 Kubernetes 项目中,kubelet 主要负责同容器运行时(比如 Docker 项目)打交道。而这个交互所依赖的,是一个称作 CRI(Container Runtime Interface)的远程调用接口,这个接口定义了容器运行时的各项核心操作,比如:启动一个容器需要的所有参数**。

**Kubernetes 项目并不关心你部署的是什么容器运行时、使用的什么技术实现,只要你
的这个容器运行时能够运行标准的容器镜像,它就可以通过实现 CRI 接入到 Kubernetes 项目当中**

**而具体的容器运行时,比如 Docker 项目,则一般通过 OCI 这个容器运行时规范同底层的 Linux 操作系统进行交互,即:把 CRI 请求翻译成对 Linux 操作系统的调用(操作 Linux Namespace 和Cgroups 等)。**

**此外,kubelet 还通过 gRPC 协议同一个叫作 Device Plugin 的插件进行交互。这个插件,是Kubernetes 项目用来管理 GPU 等宿主机物理设备的主要组件,也是基于 Kubernetes 项目进行机器学习训练、高性能作业支持等工作必须关注的功能**

而kubelet 的另一个重要功能,则是调用网络插件和存储插件为容器配置网络和持久化存储

这两个插件与 kubelet 进行交互的接口,分别是 CNI(Container Networking Interface)和
CSI(Container Storage Interface)。



那么,Borg 对于 Kubernetes 项目的指导作用又体现在哪里呢?

**Master 节点从一开始,Kubernetes 项目就没有像同时期的各种“容器云”项目那
样,把 Docker 作为整个架构的核心,而仅仅把它作为最底层的一个容器运行时实现。**

> 运行在大规模集群中的各种任务之间,实际上存在着各种各样的关系。这些关系的处理,才是作业编排和管理系统最困难的地方。

这种任务与任务之间的关系,在我们平常的各种技术场景中随处可见。比如,一个 Web 应
用与数据库之间的访问关系,一个负载均衡器和它的后端服务之间的代理关系,一个门户应用与授权组件之间的调用关系。

更进一步地说,同属于一个服务单位的不同功能之间,也完全可能存在这样的关系。比如,一个
Web 应用与日志搜集组件之间的文件交换关系。

**你会经常发现很多功能并不相关的应用被一股脑儿地部署在同一台虚拟机中,只是因为它们之间偶尔会互相发起几个 HTTP 请求。**

**更常见的情况则是,一个应用被部署在虚拟机里之后,你还得手动维护很多跟它协作的守护进程(Daemon),用来处理它的日志搜集、灾难恢复、数据备份等辅助工作。**

> 但容器技术出现以后,你就不难发现,在“功能单位”的划分上,容器有着独一无二的“细粒
> 度”优势:毕竟容器的本质,只是一个进程而已。

也就是说,只要你愿意,那些原先拥挤在同一个虚拟机里的各个应用、组件、守护进程,都可以被
分别做成镜像,然后运行在一个个专属的容器中。它们之间互不干涉,拥有各自的资源配额,可以
被调度在整个集群里的任何一台机器上。而这,正是一个 PaaS 系统最理想的工作状态,也是所
谓“微服务”思想得以落地的先决条件。

当然,如果只做到“封装微服务、调度单容器”这一层次,Docker Swarm 项目就已经绰绰有余
了。如果再加上 Compose 项目,你甚至还具备了处理一些简单依赖关系的能力,比如:一
个“Web 容器”和它要访问的数据库“DB 容器”。

在 Compose 项目中,你可以为这样的两个容器定义一个“link”,而 Docker 项目则会负责维护
这个“link”关系,其具体做法是:Docker 会在 Web 容器中,将 DB 容器的 IP 地址、端口等信息
以环境变量的方式注入进去,供应用进程使用,比如:

```
DB_NAME=/web/db
DB_PORT=tcp://172.17.0.5:5432
DB_PORT_5432_TCP=tcp://172.17.0.5:5432
DB_PORT_5432_TCP_PROTO=tcp
DB_PORT_5432_TCP_PORT=5432
DB_PORT_5432_TCP_ADDR=172.17.0.5
```

而当 DB 容器发生变化时(比如,镜像更新,被迁移到其他宿主机上等等),这些环境变量的值会
由 Docker 项目自动更新。这就是平台项目自动地处理容器间关系的典型例子

**可是,如果我们现在的需求是,要求这个项目能够处理前面提到的所有类型的关系,甚至还要能够**
**支持未来可能出现的更多种类的关系呢?**

**这时,“link”这种单独针对一种案例设计的解决方案就太过简单了。如果你做过架构方面的工作,
就会深有感触:一旦要追求项目的普适性,那就一定要从顶层开始做好设计。
所以,Kubernetes 项目最主要的设计思想是,从更宏观的角度,以统一的方式来定义任务之间的各种关系,并且为将来支持更多种类的关系留有余地。**

> Kubernetes 项目对容器间的“访问”进行了分类,首先总结出了一类非常常见的“紧密交
> 互”的关系,即:这些应用之间需要非常频繁的交互和访问;又或者,它们会直接通过本地文件进行信息交换。
>
> **在常规环境下,这些应用往往会被直接部署在同一台机器上,通过 Localhost 通信,通过本地磁盘目录交换文件。而在 Kubernetes 项目中,这些容器则会被划分为一个“Pod”,Pod 里的容器共享同一个 Network Namespace、同一组数据卷,从而达到高效率交换信息的目的**

Web 应用与数据库之间的访问关系,Kubernetes 项目则提
供了一种叫作“Service”的服务。像这样的两个应用,往往故意不部署在同一台机器上,这样即使
Web 应用所在的机器宕机了,数据库也完全不受影响。可是,我们知道,对于一个容器来说,它的
IP 地址等信息不是固定的,那么 Web 应用又怎么找到数据库容器的 Pod 呢?

> 所以,Kubernetes 项目的做法是给 Pod 绑定一个 Service 服务,而 Service 服务声明的 IP 地址等信息是“终生不变”的。这个Service 服务的主要作用,就是作为 Pod 的代理入口(Portal),从而代替 Pod 对外暴露一个固定的网络地址。

这样,对于 Web 应用的 Pod 来说,它需要关心的就是数据库 Pod 的 Service 信息。不难想象,
Service 后端真正代理的 Pod 的 IP 地址、端口等信息的自动更新、维护,则是 Kubernetes 项目的职责。

![image-20210707195952369](/home/gongna/snap/typora/39/.config/Typora/typora-user-images/image-20210707195952369.png)

按照这幅图的线索,我们从容器这个最基础的概念出发

* 首先遇到了容器间“紧密协作”关系的难题,于是就扩展到了 Pod;有了 Pod 之后,我们希望能一次启动多个应用的实例,这样就需要Deployment 这个 Pod 的多实例管理器;
* 而有了这样一组相同的 Pod 后,我们又需要通过一个固定的 IP 地址和端口以负载均衡的方式访问它,于是就有了 Service。
* 可是,如果现在两个不同 Pod 之间不仅有“访问关系”,还要求在发起时加上授权信息。最典型的例子就是 Web 应用对数据库访问时需要 Credential(数据库的用户名和密码)信息。那么,在Kubernetes 中这样的关系又如何处理呢?Kubernetes 项目提供了一种叫作 Secret 的对象,它其实是一个保存在 Etcd 里的键值对数据。这样,你把 Credential 信息以 Secret 的方式存在 Etcd 里,Kubernetes 就会在你指定的 Pod(比如,Web 应用的 Pod)启动时,自动把 Secret 里的数据以 Volume 的方式挂载到容器里。这样,这个 Web 应用就可以访问数据库了。

**除了应用与应用之间的关系外,应用运行的形态是影响“如何容器化这个应用”的第二个重要因
素。**

**为此,Kubernetes 定义了新的、基于 Pod 改进后的对象。比如 Job,用来描述一次性运行的Pod(比如,大数据任务);再比如 DaemonSet,用来描述每个宿主机上必须且只能运行一个副本的守护进程服务;又比如 CronJob,则用于描述定时任务等等。**

**如此种种,正是 Kubernetes 项目定义容器间关系和形态的主要方法**

Kubernetes 项目中,我们所推崇的使用方法是：

* 首先,通过一个“编排对象”,比如 Pod、Job、CronJob 等,来描述你试图管理的应用

* 然后,再为它定义一些“服务对象”,比如 Service、Secret、Horizontal Pod Autoscaler(自
  动水平扩展器)等。这些对象,会负责具体的平台级功能

* 声明式 API”。这种 API 对应的“编排对象”和“服务对象”,都是Kubernetes 项目中的 API 对象(API Object)。

  

  

  **最后,我来回答一个更直接的问题:Kubernetes 项目如何启动一个容器化任务呢?
  比如,我现在已经制作好了一个 Nginx 容器镜像,希望让平台帮我启动这个镜像。并且,我要求平台帮我运行两个完全相同的 Nginx 副本,以负载均衡的方式共同对外提供服务。**

* 如果是自己 DIY 的话,可能需要启动两台虚拟机,分别安装两个 Nginx,然后使用 keepalived为这两个虚拟机做一个虚拟 IP
* 而如果使用 Kubernetes 项目呢?你需要做的则是编写如下这样一个 YAML 文件(比如名叫
  nginx-deployment.yaml)

```
apiVersion: apps/v1
kind: Deployment
metadata:
	name: nginx-deployment
	labels:
	app: nginx
spec:
	replicas: 2
	selector:
		matchLabels:
			app: nginx
template:
	metadata:
		labels:
			app: nginx
spec:
	containers:
	- name: nginx
		image: nginx:1.7.9
		ports:
		- containerPort: 80

然后执行
$ kubectl create -f nginx-deployment.yaml
```

**这样,两个完全相同的 Nginx 容器副本就被启动了。
不过,这么看来,做同样一件事情,Kubernetes 用户要做的工作也不少嘛。
别急,在后续的讲解中,我会陆续介绍 Kubernetes 项目这种“声明式 API”的种种好处,以及基
于它实现的强大的编排能力。
拭目以待吧。
总结

* 容器其实可以分为两个部分:容器运行时和容器镜像。 Kubernetes 项目的架构以及它如何使用“声明式 API”来描述容器化业务和容器间关系的设计思想。
  实际上,过去很多的集群管理项目(比如 Yarn、Mesos,以及 Swarm)所擅长的,都是把一个容器,按照某种规则,放置在某个最佳节点上运行起来。这种功能,我们称为“调度”。
*  Kubernetes 项目所擅长的,是按照用户的意愿和整个系统的规则,完全自动化地处理好容器之间的各种关系。这种功能,就是我们经常听到的一个概念:编排。所以说,Kubernetes 项目的本质,是为用户提供一个具有普遍意义的容器编排工具。不过,更重要的是,Kubernetes 项目为用户提供的不仅限于一个工具。它真正的价值,乃在于提供
  了一套基于容器构建分布式系统的基础依赖。

# kubeadm 的工作原理

**因为kubelet 是 Kubernetes 项目用来操作 Docker 等容器运行时的核心
组件。可是,除了跟容器运行时打交道外,kubelet 在配置容器网络、管理容器数据卷时,都需要直接操作宿主机**，如果想通过镜像化Kubeenetes来进行部署，那么kubelet 的容器化是关键。

**而如果现在 kubelet 本身就运行在一个容器里,那么直接操作宿主机就会变得很麻烦。对于网络配置来说还好,kubelet 容器可以通过不开启 Network Namespace(即 Docker 的 host network模式)的方式,直接共享宿主机的网络栈。可是,要让 kubelet 隔着容器的 Mount Namespace和文件系统,操作宿主机的文件系统,就有点儿困难了。**

比如,如果用户想要使用 NFS 做容器的持久化数据卷,那么 kubelet 就需要在容器进行绑定挂载
前,在宿主机的指定目录上,先挂载 NFS 的远程目录

可是,这时候问题来了。由于现在 kubelet 是运行在容器里的,这就意味着它要做的这个“mount-F nfs”命令,被隔离在了一个单独的 Mount Namespace 中。即,kubelet 做的挂载操作,不能被“传播”到宿主机上。

对于这个问题,有人说,可以使用 setns() 系统调用,在宿主机的 Mount Namespace 中执行这些
挂载操作;也有人说,应该让 Docker 支持一个–mnt=host 的参数。
但是,到目前为止,在容器里运行 kubelet,依然没有很好的解决办法,我也不推荐你用容器去部
署 Kubernetes 项目。

正因为如此,kubeadm 选择了一种妥协方案:

> 把 kubelet 直接运行在宿主机上,然后使用容器部署其他的 Kubernetes 组件 

所以,你使用 kubeadm 的第一步,是在机器上手动安装 kubeadm、kubelet 和 kubectl 这三个
二进制文件。当然,kubeadm 的作者已经为各个发行版的 Linux 准备好了安装包,所以你只需要
执行:

```
$ apt-get install kubeadm
```

就可以了。
接下来,你就可以使用“kubeadm init”部署 Master 节点了。

kubeadm init 的工作流程
当你执行 kubeadm init 指令后,kubeadm 首先要做的,是一系列的检查工作,以确定这台机器可
以用来部署 Kubernetes。这一步检查,我们称为“Preflight Checks”,它可以为你省掉很多后续
的麻烦。

* Linux 内核的版本必须是否是 3.10 以上?
* Linux Cgroups 模块是否可用?
* 机器的 hostname 是否标准?在 Kubernetes 项目里,机器的名字以及一切存储在 Etcd 中的
* API 对象,都必须使用标准的 DNS 命名(RFC 1123)。
* 用户安装的 kubeadm 和 kubelet 的版本是否匹配?
* 机器上是不是已经安装了 Kubernetes 的二进制文件?
* Kubernetes 的工作端口 10250/10251/10252 端口是不是已经被占用?
  ip、mount 等 Linux 指令是否存在?
* Docker 是否已经安装?

在通过了 Preflight Checks 之后,kubeadm 要为你做的,是生成 Kubernetes 对外提供服务所需的各种证书和对应的目录。



在通过了 Preflight Checks 之后,kubeadm 要为你做的,是生成 Kubernetes 对外提供服务所需的各种证书和对应的目录。
Kubernetes 对外提供服务时,除非专门开启“不安全模式”,否则都要通过 HTTPS 才能访问
kube-apiserver。这就需要为 Kubernetes 集群配置好证书文件。
kubeadm 为 Kubernetes 项目生成的证书文件都放在 Master 节点的 /etc/kubernetes/pki 目录下。在这个目录下,最主要的证书文件是 ca.crt 和对应的私钥 ca.key。

此外,用户使用 kubectl 获取容器日志等 streaming 操作时,需要通过 kube-apiserver 向
kubelet 发起请求,这个连接也必须是安全的。kubeadm 为这一步生成的是 apiserver-kubelet-
client.crt 文件,对应的私钥是 apiserver-kubelet-client.key。

此外,用户使用 kubectl 获取容器日志等 streaming 操作时,需要通过 kube-apiserver 向
kubelet 发起请求,这个连接也必须是安全的。kubeadm 为这一步生成的是 apiserver-kubelet-client.crt 文件,对应的私钥是 apiserver-kubelet-client.key。

除此之外,Kubernetes 集群中还有 Aggregate APIServer 等特性,也需要用到专门的证书,这里
我就不再一一列举了。需要指出的是,你可以选择不让 kubeadm 为你生成这些证书,而是拷贝现有的证书到如下证书的目录里:

```
/etc/kubernetes/pki/ca.{crt,key}
```

**这时,kubeadm 就会跳过证书生成的步骤,把它完全交给用户处理。
证书生成后,kubeadm 接下来会为其他组件生成访问 kube-apiserver 所需的配置文件。这些文件的路径是:/etc/kubernetes/xxx.conf:**

```
ls /etc/kubernetes/
admin.conf
controller-manager.conf
kubelet.conf
scheduler.conf
```

# pod的使用

* 容器健康检查和恢复机制

> 为 Pod 里的容器定义一个健康检查“探针”(Probe)。这样,
> kubelet 就会根据这个 Probe 的返回值决定这个容器的状态,而不是直接以容器进行是否运行(来
> 自 Docker 返回的信息)作为依据。这种机制,是生产环境中保证应用健康存活的重要手段。

```
apiVersion: v1
kind: Pod
metadata:
labels:
test: liveness
name: test-liveness-exec
spec:
containers:
- name: liveness
image: busybox
args:
- /bin/sh
- -c
- touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
// 在temp 的目录下创建一个健康文件

livenessProbe:
// 健康检查

exec:
command:
- cat
- /tmp/healthy
//执行指定的命令
查看该目录下的健康文件，如果该文件存在就返回0值，让pod知道，容器不仅启动，而且健康
initialDelaySeconds: 5
periodSeconds: 5


```

restartPolicy 和 Pod 里容器的状态,以及 Pod 状态
的对应关系,总结了非常复杂的一大堆情况。实际上,你根本不需要死记硬背这些对应关系,只要
记住如下两个基本的设计原理即可:

* 只要 Pod 的 restartPolicy 指定的策略允许重启异常的容器(比如:Always),那么这个 Pod
  就会保持 Running 状态,并进行容器重启。否则,Pod 就会进入 Failed 状态
* 对于包含多个容器的 Pod,只有它里面所有的容器都进入异常状态后,Pod 才会进入 Failed 状态。在此之前,Pod 都是 Running 状态。此时,Pod 的 READY 字段会显示正常容器的个数
  

所以,假如一个 Pod 里只有一个容器,然后这个容器异常退出了。那么,只有estartPolicy=Never 时,这个 Pod 才会进入 Failed 状态。而其他情况下,由于 Kubernetes 都可以重启这个容器,所以 Pod 的状态保持 Running 不变。

而如果这个 Pod 有多个容器,仅有一个容器异常退出,它就始终保持 Running 状态,哪怕即使
restartPolicy=Never。只有当所有容器也异常退出之后,这个 Pod 才会进入 Failed 状态。

### pod的readinessProbe字段

> readinessProbe 检查结果的成功与否,决定的这个 Pod 是不是能被通
> 过 Service 的方式访问到,而并不影响 Pod 的生命周期

```
apiVersion: settings.k8s.io/v1alpha1
kind: PodPreset
metadata:
	name: allow-database
spec:
	selector:
		matchLabels:
			role: frontend
env:
	- name: DB_PORT        //DB_PORT 这个环境变量
	value: "6379"
volumeMounts:              //容器 Volume 的挂载目录
	- mountPath: /cache
	name: cache-volume
volumes:
- name: cache-vol
emptyDir: {}
```



**Pod API 对象的各个字段**

**Kubernetes“一切皆对象”的设计思想:比如应
用是 Pod 对象,应用的配置是 ConfigMap 对象,应用要访问的密码则是 Secret 对象。**

> PodPreset 这样专门用来对 Pod 进行批量化、自动化修改的工具对
> 象

# kubernetes的编排原理——“控制器”模型



> Pod 这个看似复杂的 API 对象,实际上就是对容器的进一步抽象和
> 封装而已。



> “容器”镜像虽然好用,但是容器这样一个“沙盒”的概念,对于描述应用来说,还是太过简单了。这就好比,集装箱固然好用,但是如果它四面都光秃秃的,吊车还怎么把这个集装箱吊起来并摆放好呢?所以,Pod 对象,其实就是容器的升级版。它对容器进行了组合,添加了更多的属性和字段。这就好比给集装箱四面安装了吊环,使得 Kubernetes 这架“吊车”,可以更轻松地操作它。

**Kubernetes 操作这些“集装箱”的逻辑**就是控制器在完成

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
	matchLabels:
		app: nginx
replicas: 2
template:
  metadata:
	labels:
		app: nginx
spec:
	containers:
	- name: nginx
	image: nginx:1.7.9
	ports:
	- containerPort: 80
```



```
$ cd kubernetes/pkg/controller/
$ ls -d */
deployment/ 	job/ 			podautoscaler/
cloud/ 			disruption/ 	namespace/
replicaset/ 	serviceaccount/ volume/
cronjob/ 		garbagecollector/ nodelifecycle/
```



**这个目录下面的每一个控制器,都以独有的方式负责某种编排功能。而我们的 Deployment,正是这些控制器中的一种。实际上,这些控制器之所以被统一放在 pkg/controller 目录下,就是因为它们都遵循 Kubernetes项目中的一个通用编排模式,即:控制循环(control loop)。**

****

```
for {
实际状态 := 获取集群中对象 X 的实际状态(Actual State)
期望状态 := 获取集群中对象 X 的期望状态(Desired State)
	if 实际状态 == 期望状态{
		什么都不做
	} else {
	执行编排动作,将实际状态调整为期望状态
	}
}
```

* 实际状态往往来自于 Kubernetes 集群本身。

  比如,kubelet 通过心跳汇报的容器状态和节点状态,或者监控系统中保存的应用监控数据,或者控制器主动收集的它自己感兴趣的信息,这些都是常见的实际状态的来源。

* 期望状态,一般来自于用户提交的 YAML 文件

  

具体的实现如下：

* 1.Deployment 控制器从 Etcd 中获取到所有携带了“app: nginx”标签的 Pod,然后统计它们的数量,这就是实际状态
* 2.Deployment 对象的 Replicas 字段的值就是期望状态;
* 3.Deployment 控制器将两个状态做比较,然后根据比较结果,确定是创建 Pod,还是删除已有的 Pod(具体如何操作 Pod 对象,我会在下一篇文章详细介绍)。

**一个 Kubernetes 对象的主要编排逻辑,实际上是在第三步的“对比”阶段完成的。
这个操作,通常被叫作调谐(Reconcile)。这个调谐的过程,则被称作“Reconcile Loop”(调
谐循环)或者“Sync Loop”(同步循环)。调谐的最终结果,往往都是对被控制对象的某种写操作增加 Pod,删除已有的 Pod,或者更新 Pod 的某个字段。这也是 Kubernetes 项目“面向
API 对象编程”的一个直观体现。**

![](/home/gongna/图片/2021-07-17_20-26.png)

**很多不同类型的容器编排功能,比如 StatefulSet、DaemonSet 等等,它们无一例外地都有这样一个甚至多个控制器的存在,并遵循控制循环(control loop)的流程,完成各自的编排逻辑。实际上,跟 Deployment 相似,这些控制循环最后的执行结果,要么就是创建、更新一些 Pod(或者其他的 API 对象、资源),要么就是删除一些已经存在的 Pod(或者其他的 API 对象、资源)。但也正是在这个统一的编排框架下,不同的控制器可以在具体执行过程中,设计不同的业务逻辑,从而达到不同的编排效果。**

![](/home/gongna/图片/2021-07-17_20-30.png)

**一个定义了 replicas=3 的 Deployment,与它的ReplicaSet,以及 Pod 的关系,实际上是一种“层层控制”的关系。**
	其中,ReplicaSet 负责通过“控制器模式”,保证系统中 Pod 的个数永远等于指定的个数(比
如,3 个)。这也正是 Deployment 只允许容器的 restartPolicy=Always 的主要原因:只有在
容器能保证自己始终是 Running 状态的前提下,ReplicaSet 调整 Pod 的个数才有意义。
		而在此基础上,Deployment 同样通过“控制器模式”,来操作 ReplicaSet 的个数和属性,进
而实现“水平扩展 / 收缩”和“滚动更新”这两个编排动作。
		其中,“水平扩展 / 收缩”非常容易实现,Deployment Controller 只需要修改它所控制的
ReplicaSet 的 Pod 副本个数就可以了。
		比如,把这个值从 3 改成 4,那么 Deployment 所对应的 ReplicaSet,就会根据修改后的值自动创建一个新的 Pod。这就是“水平扩展”了;“水平收缩”则反之。而用户想要执行这个操作的指令也非常简单,就是 kubectl scale

```
$ kubectl scale deployment nginx-deployment --replicas=4
deployment.apps/nginx-deployment scaled
```

