# Http

# 什么是Internet，HTTP和HTTPS？6分钟阅读

上 九月7，2018

现在，互联网已成为每个人生活中不可或缺的一部分，它可以帮助您在线上进行事情，而无需在杂货店或超市排队，只需用手机单击几下，您就可以轻松地购买东西。在探究什么是HTTP之前，我们应该首先考虑什么是Internet。

互联网实际上只是WAN（广域网）和LAN（局域网）的集合。

**局域网**–单个建筑物或附近建筑物中的计算机网络

**WAN** –一种网络，计算机设备在物理上彼此距离较远，通常跨城市，州甚至大洲。

而且，如果您要连接到Internet，则可以从Internet服务提供商（**ISP**）获得帮助，该公司将负责为您提供**路由器**-计算机中的设备或软件，该**路由器**或设备可以确定下一个网络点。数据包应转发。或者，您可以轻松地通过WLAN（无线局域网）访问互联网，并且通常被称为**Wi-Fi**或WiFi，这是一项基于**IEEE 802.11**标准的设备通过接入点**POP**进行无线无线局域网的技术。（存在点）也称为**热点**。

学习互联网的基本概念，例如TCP / IP，OSI模型，IP，子网，DHCP，FTP，POP3，SMTP，是至关重要的，但是在这篇有限的文章中，我们将仅深入了解HTTP及其周围的一些概念。所有其他这些将在其他文章中进行解释。

## [![img](https://learntocodetogether.com/wp-content/uploads/2018/09/HTTP.jpg)](https://learntocodetogether.com/wp-content/uploads/2018/09/HTTP.jpg)

## 什么是HTTP？

在Internet的旧时代，文件是使用FPT（文件传输协议）和其他一些旧协议在两台或更多台不同的计算机之间传输的，这确实很乏味且效率低下。这就是为什么在1990年的一个美好的日子里，英国的研究人员和科学家提出了万维网（**WWW**）的思想，其主要思想是Web浏览器和Web服务器，而HTTP是一个至关重要的协议，负责Web浏览器和Web服务器可以彼此“交谈”。



**HTTP**是超文本传输协议的缩写，科学上来说，超文本传输协议是用于分布式，协作和**超媒体**信息系统的应用程序协议。**HTTP**是**万维网**数据通信的基础。

您可以通过在搜索栏上键入内容来想象它是什么，点击回车后，浏览器将通过HTTP协议向网络服务器发送请求。Web服务器将收到此请求，并将结果返回到Web浏览器。

![img](https://learntocodetogether.com/wp-content/uploads/2018/09/http_process_explained.jpg)

## HTTPS

**HTTPS** （*HTTP安全*）是[HTTP](https://developer.mozilla.org/en-US/docs/Glossary/HTTP) 协议的加密版本 。它通常使用 [SSL](https://developer.mozilla.org/en-US/docs/Glossary/SSL)（安全套接字层）或 [TLS](https://developer.mozilla.org/en-US/docs/Glossary/TLS)（传输层安全性）来加密客户端和服务器之间的所有通信。这种安全的连接使客户端能够与服务器安全地交换敏感数据，例如用于银行活动或在线购物。实际上，HTTPS现在无处不在，甚至在我的博客中也是如此！如果您正在浏览的网站未加密，则不应键入任何敏感信息。您可以通过观察并单击搜索栏的左上角来检查它是HTTPS还是HTTP。

![img](https://learntocodetogether.com/wp-content/uploads/2018/09/68747470733a2f2f7072616275646468612e6d652f77702d636f6e74656e742f75706c6f6164732f323031362f30312f6874747073312e706e67.png)HTTPS（安全）

![img](https://learntocodetogether.com/wp-content/uploads/2018/09/Screen-Shot-2019-06-21-at-05.47.30.png)HTTP（不安全）

您也可以扩展此部分，以查看有关您当前所在的网站的证书，该网站使用的Cookie的更多信息。

## HTTP缓存

通过重新使用以前获取的资源，可以显着提高网站和应用程序的性能。Web缓存减少了延迟和网络流量，因此减少了显示资源表示形式所需的时间。通过使用HTTP缓存，网站变得更加敏感。

Web缓存是用于临时存储Web文档（例如HTML页面和图像）以减少服务器延迟的信息技术。



## HTTP COOKIE

一个 HTTP cookie的 （网络Cookie，浏览器cookie）是**一小片数据的一个服务器发送到用户的网络浏览器**。**浏览器可以存储它，并将其与下一个请求一起发送回同一服务器**。 通常，**它用于判断两个请求是否来自同一浏览器**-例如，使用户保持登录状态。它记住[无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#HTTP_is_stateless_but_not_sessionless) HTTP协议的有状态信息 。

## HTTP标头

HTTP标头允许客户端和服务器将附加信息与请求或响应一起传递。HTTP标头由不区分大小写的名称组成，后跟冒号' `:`'，然后是其值（无换行符）。该值之前的前导空格将被忽略。

## HTTP请求方法

- `GET`

``该 `GET` 方法请求表示指定资源。使用的请求 `GET` 应仅检索数据。

- `POST`

``该 `POST` 方法用于将实体提交给指定的资源，通常会导致状态更改或对服务器产生副作用。

还有其他几种方法，但是现在，我们应该重点关注上面的2种常见方法。

# 什么是Internet，HTTP和HTTPS？6分钟阅读

上 九月7，2018

现在，互联网已成为每个人生活中不可或缺的一部分，它可以帮助您在线上进行事情，而无需在杂货店或超市排队，只需用手机单击几下，您就可以轻松地购买东西。在探究什么是HTTP之前，我们应该首先考虑什么是Internet。

互联网实际上只是WAN（广域网）和LAN（局域网）的集合。

**局域网**–单个建筑物或附近建筑物中的计算机网络

**WAN** –一种网络，计算机设备在物理上彼此距离较远，通常跨城市，州甚至大洲。

而且，如果您要连接到Internet，则可以从Internet服务提供商（**ISP**）获得帮助，该公司将负责为您提供**路由器**-计算机中的设备或软件，该**路由器**或设备可以确定下一个网络点。数据包应转发。或者，您可以轻松地通过WLAN（无线局域网）访问互联网，并且通常被称为**Wi-Fi**或WiFi，这是一项基于**IEEE 802.11**标准的设备通过接入点**POP**进行无线无线局域网的技术。（存在点）也称为**热点**。

学习互联网的基本概念，例如TCP / IP，OSI模型，IP，子网，DHCP，FTP，POP3，SMTP，是至关重要的，但是在这篇有限的文章中，我们将仅深入了解HTTP及其周围的一些概念。所有其他这些将在其他文章中进行解释。

## [![img](https://learntocodetogether.com/wp-content/uploads/2018/09/HTTP.jpg)](https://learntocodetogether.com/wp-content/uploads/2018/09/HTTP.jpg)

## 什么是HTTP？

在Internet的旧时代，文件是使用FPT（文件传输协议）和其他一些旧协议在两台或更多台不同的计算机之间传输的，这确实很乏味且效率低下。这就是为什么在1990年的一个美好的日子里，英国的研究人员和科学家提出了万维网（**WWW**）的思想，其主要思想是Web浏览器和Web服务器，而HTTP是一个至关重要的协议，负责Web浏览器和Web服务器可以彼此“交谈”。



**HTTP**是超文本传输协议的缩写，科学上来说，超文本传输协议是用于分布式，协作和**超媒体**信息系统的应用程序协议。**HTTP**是**万维网**数据通信的基础。

您可以通过在搜索栏上键入内容来想象它是什么，点击回车后，浏览器将通过HTTP协议向网络服务器发送请求。Web服务器将收到此请求，并将结果返回到Web浏览器。

![img](https://learntocodetogether.com/wp-content/uploads/2018/09/http_process_explained.jpg)

## HTTPS

**HTTPS** （*HTTP安全*）是[HTTP](https://developer.mozilla.org/en-US/docs/Glossary/HTTP) 协议的加密版本 。它通常使用 [SSL](https://developer.mozilla.org/en-US/docs/Glossary/SSL)（安全套接字层）或 [TLS](https://developer.mozilla.org/en-US/docs/Glossary/TLS)（传输层安全性）来加密客户端和服务器之间的所有通信。这种安全的连接使客户端能够与服务器安全地交换敏感数据，例如用于银行活动或在线购物。实际上，HTTPS现在无处不在，甚至在我的博客中也是如此！如果您正在浏览的网站未加密，则不应键入任何敏感信息。您可以通过观察并单击搜索栏的左上角来检查它是HTTPS还是HTTP。

![img](https://learntocodetogether.com/wp-content/uploads/2018/09/68747470733a2f2f7072616275646468612e6d652f77702d636f6e74656e742f75706c6f6164732f323031362f30312f6874747073312e706e67.png)HTTPS（安全）

![img](https://learntocodetogether.com/wp-content/uploads/2018/09/Screen-Shot-2019-06-21-at-05.47.30.png)HTTP（不安全）

您也可以扩展此部分，以查看有关您当前所在的网站的证书，该网站使用的Cookie的更多信息。

## HTTP缓存

通过重新使用以前获取的资源，可以显着提高网站和应用程序的性能。Web缓存减少了延迟和网络流量，因此减少了显示资源表示形式所需的时间。通过使用HTTP缓存，网站变得更加敏感。

Web缓存是用于临时存储Web文档（例如HTML页面和图像）以减少服务器延迟的信息技术。



## HTTP COOKIE

一个 HTTP cookie的 （网络Cookie，浏览器cookie）是一小片数据的一个服务器发送到用户的网络浏览器。浏览器可以存储它，并将其与下一个请求一起发送回同一服务器。 通常，它用于判断两个请求是否来自同一浏览器-例如，使用户保持登录状态。它记住[无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#HTTP_is_stateless_but_not_sessionless) HTTP协议的有状态信息 。

## HTTP标头

HTTP标头允许客户端和服务器将附加信息与请求或响应一起传递。HTTP标头由不区分大小写的名称组成，后跟冒号' `:`'，然后是其值（无换行符）。该值之前的前导空格将被忽略。

## HTTP请求方法

- `GET`

``该 `GET` 方法请求表示指定资源。使用的请求 `GET` 应仅检索数据。

- `POST`

``该 `POST` 方法用于将实体提交给指定的资源，通常会导致状态更改或对服务器产生副作用。

还有其他几种方法，但是现在，我们应该重点关注上面的2种常见方法。

## HTTP响应状态码

HTTP响应状态代码指示特定的 [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP) 请求是否已成功完成。响应分为五类：信息响应，成功响应，重定向，客户端错误和服务器错误。

#### 常用方法

- 200 OK
- 创建了201
- 400错误的要求
- 401禁止
- 找不到404
- 405不允许的方法
- 422无法处理的实体

## 跨域资源共享（CORS）

跨域资源共享（[CORS](https://developer.mozilla.org/en-US/docs/Glossary/CORS)）是一种机制，它使用附加的 [HTTP](https://developer.mozilla.org/en-US/docs/Glossary/HTTP) 标头来告诉浏览器，使运行在一个来源（域）上的Web应用程序有权访问来自另一个来源的服务器中的选定资源。 Web应用程序**请求** 其来源（域，协议和端口）与其自身来源不同的资源时，会发出 **跨域HTTP请求**。



一个跨域请求的例子：一个Web应用程序的前端JavaScript代码从供应 `http://domain-a.com` 用途 [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 作出了要求 `http://api.domain-b.com/data.json`。

## HTTP消息

HTTP消息是服务器和客户端之间如何交换数据的方式。有两种类型的消息： *请求* 客户端发送到触发服务器上的动作，并且 *响应*，从服务器的答案。

