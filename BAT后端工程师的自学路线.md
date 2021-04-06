# 从小白到BAT后端工程师的自学路线

## 1. 前言

遇事不决就Google

## 2.学习方向和学习方式

**语言（C、C++、Go、Python等）**

> 另外还有一些很有意思的方向，可以作为进阶学习，对于大部分岗位的面试而言，只是加分项，而非必选项.  

   **必备**

+ 语言（C、C++、Go、Python等）

+ 数据结构和算法（一切的基石）

+ 操作系统（操作系统理论知识、Linux中怎么实现）

+ 计算机网络（网络分层、TCP协议）

+ 系统编程和网络编程（Linux/Unix的系统编程接口和网络编程模型）

  **进阶**

  + 分布式系统（感兴趣可以学习，分布式存储、计算等，加分项）

  + 编译原理（很大的魅力，感兴趣可以学，耗时间，加分项）

  + Functional Programming（同上）

  + 云原生、容器、服务编排等（Docker、Kubernetes什么的，加分项）

    

    **主要的学习方式有：**

    + 看书（买书不要吝啬）
    + 名校课程（MIT、CMU有很多不错的courses）
    + 阅读源码（熟练运用google、github）
    + 刷题（LeetCode）
    + 自己实现一遍
    + 知乎等平台（参与讨论，会发现很多自己没有思考到的东西）

    

> https://link.zhihu.com/?target=https%3A//b-ok.cc/

> 一个英文书籍的检索网站

## 3.学习道路

> https://link.zhihu.com/?target=https%3A//item.jd.com/11917487.html
>
> 入门，C语言，推荐书籍：《C Primer Plus 》，另外有两本可以作为辅助读物：《编码》和《计算科学概论》，这两本书可以翻一翻，没兴趣也可以不读。
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/4822685/
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/6862061/

 

> 学完了C语言之后，就有能力去学习数据结构了，推荐《数据结构与算法分析（C语言描述）》，尽量把上面的数据结构和算法都自己实现一遍。这个时候最好学会了使用命令行，然后代码可以托管在Github。
>
> 
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/1139426/



>然后，可以学习一下主要的语言C++，推荐《C++ Primer》。另外，学习一下脚本语言也有一些用处，推荐Python，书籍《Python基础教程》，了解下基本语法就好。学习Python之后，可以尝试去做一些比较有意思的项目，例如写个爬虫，这样可以培养自己的编程兴趣，也挺有成就感，写爬虫可以在网上搜一些教程，在学习的过程中，也可以对html、http协议等有一些了解。
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/25708312/)
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/25708312/
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/27667375/





> 算法仍是重中之重，学习完C++后，可以着重学习这方面，推荐《算法（第四版）》和《算法导论》，这两本书都是很经典的算法方面的书籍，前者难度相对较低，后者因为有很多算法复杂度分析，会让人觉得读起来很困难。我的建议是，算法4需要通读，并且用C++来实现上面的算法（书上都是用Java实现的），然后算法导论看完前面17章即可（到贪心那章），算法复杂度分析的部分可以尝试去看看，如果实在讨厌公式推导也可以跳过，但是要能够掌握推导一些常见算法的复杂度。
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/19952400/
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/20432061/



>首推《深入理解计算机系统》，这本书涵盖了从一个bit到一个网络应用的系统知识，初学者会有很多概念都难以理解，建议是尝试检索相关内容去理解，实在学不会就先跳过，继续后面的内容。读书过程中，也不需要一定按照其安排的章节顺序，可以先挑自己喜欢的读，例如为了理解计算机中的cache，就去读第六章。这本书是CMU CS15213 课程（[Introduction to Computer Systems](https://link.zhihu.com/?target=https%3A//www.cs.cmu.edu/~213/)）的配套书籍，该课程相关的资源也可以用来辅助学习，另外，最精髓的就在于配套的Lab了，难度不低，这个一定要做，如果做的很困难，也要坚持去做，完成后会发现自己的能力有很大的提升。这本书也不用一口气读完，有些章节不太感兴趣，或者读不下去的话，可以留着，等后面知识更加丰富了再回头来看（例如网络那章，我看的是很难理解一些概念，后来学习了计算机网络和网络编程后，就发现读起来比较简单了）。



>首推《深入理解计算机系统》，这本书涵盖了从一个bit到一个网络应用的系统知识，初学者会有很多概念都难以理解，建议是尝试检索相关内容去理解，实在学不会就先跳过，继续后面的内容。读书过程中，也不需要一定按照其安排的章节顺序，可以先挑自己喜欢的读，例如为了理解计算机中的cache，就去读第六章。这本书是CMU CS15213 课程（[Introduction to Computer Systems](https://link.zhihu.com/?target=https%3A//www.cs.cmu.edu/~213/)）的配套书籍，该课程相关的资源也可以用来辅助学习，另外，最精髓的就在于配套的Lab了，难度不低，这个一定要做，如果做的很困难，也要坚持去做，完成后会发现自己的能力有很大的提升。这本书也不用一口气读完，有些章节不太感兴趣，或者读不下去的话，可以留着，等后面知识更加丰富了再回头来看（例如网络那章，我看的是很难理解一些概念，后来学习了计算机网络和网络编程后，就发现读起来比较简单了）。



> 学习完了上面内容后，就要进入系统的世界了。首推《深入理解计算机系统》，这本书涵盖了从一个bit到一个网络应用的系统知识，初学者会有很多概念都难以理解，建议是尝试检索相关内容去理解，实在学不会就先跳过，继续后面的内容。读书过程中，也不需要一定按照其安排的章节顺序，可以先挑自己喜欢的读，例如为了理解计算机中的cache，就去读第六章。这本书是CMU CS15213 课程（[Introduction to Computer Systems](https://link.zhihu.com/?target=https%3A//www.cs.cmu.edu/~213/)）的配套书籍，该课程相关的资源也可以用来辅助学习，另外，最精髓的就在于配套的Lab了，难度不低，这个一定要做，如果做的很困难，也要坚持去做，完成后会发现自己的能力有很大的提升。这本书也不用一口气读完，有些章节不太感兴趣，或者读不下去的话，可以留着，等后面知识更加丰富了再回头来看（例如网络那章，我看的是很难理解一些概念，后来学习了计算机网络和网络编程后，就发现读起来比较简单了）。
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/26912767/



>再就是十分重要的**操作系统**的学习了。这方面建议先学习理论，然后再去实践。理论方向推荐OSTEP（网上有免费pdf），中文版是《操作系统导论》，建议通读。学习过程中，如果对很多概念很有兴趣，想知道Linux是怎么实现的，可以看《Linux内核设计与实现》和《[深入理解LINUX内核](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/2287506/)》，当然这种书不必通读，挑自己感兴趣的章节看就好了
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/33463930/
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/6097773/
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/2287506/



> 如果时间充裕，可以尝试自己去实现一个操作系统，这方面推荐书籍《操作系统真象还原》，或者课程MIT6.828：[6.S081 / Fall 2019](https://link.zhihu.com/?target=https%3A//pdos.csail.mit.edu/6.828/2019/index.html)，两者二选一吧。
>
> https://link.zhihu.com/?target=https%3A//pdos.csail.mit.edu/6.828/2019/index.html



>然后就是计算机网络的学习，理论先看《计算机网络：自顶向下》，可以只看前面三章，即应用层和传输层，做下每章后面的lab。再就是对TCP/IP协议的学习，推荐《TCP/IP详解：卷1》，也是为了节省时间，可以只看TCP相关的章节。这本书英文版是第一版，中文版是第二版，个人是先看的英文第一版，然后再看第二版，挑了新加入的内容看。学习的时候，建议用wireshark来抓包，感受一下这些协议是怎么工作在这互联世界的网络中的，会很有趣的。
>
>https://link.zhihu.com/?target=https%3A//book.douban.com/subject/26176870/

> 
>
> 前面学习了操作系统和网络是怎么工作的，然后就需要学习怎么通过编程来调用操作系统的接口，来进行系统编程和网络编程。这方面比较经典的是《Unix环境高级编程》和《Unix网络编程》，这两本书都比较古老，比如现在网络模型中常用的epoll就没有介绍，因为成书早，那个时候Linux内核还没有实现epoll，可惜书的作者早逝，不能更新了。但是也很经典，需要搭配《Linux/Unix编程手册》。当然，这方面的书籍也不是要通读，只需要精读部分章节就好了。
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/25900403/
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/26434583/
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/25809330/

> 至此，应该能满足大厂实习面试的要求了，可以去投一投简历了。然后需要丰富一下自己的项目经历，多做一些工程。学习了网络编程后，可以学习《Linux多线程服务端编程》来学习一些C++在实际中的应用，一些常用的小东西（double-buffer啥的），并且加深自己并发编程的能力，相信在前面的基础学习后，能够很好地理解锁，而且对锁的实现，也会了然于胸。然后，这本书还介绍了现在常用的网络IO模型，自己可以实现一种，做一个网络库，并基于此实现一个http server。另外，还可以阅读该书配套代码，muduo，一方面可以看一看优秀的C++代码应该是怎样的，还可以查漏补缺自己的C++知识。
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/20471211/

> 到这个时候，差不多就是一台offer收割机了。为了应付面试，可以看一看《剑指offer》，免得被套路题给难倒，然后多看看面经。
>
> https://link.zhihu.com/?target=https%3A//book.douban.com/subject/25910559/