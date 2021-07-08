# netstat使用示例

1)列出所有端口
netstat -anp

2)列出所有 tcp 端口
netstat -antp

3)列出所有 udp 端口
netstat -anup

4)只显示所有监听端口
netstat -lnp

5)只列出所有监听 tcp 端口
netstat -ltnp

6)只列出所有监听 udp 端口
netstat -lunp

7)只列出所有监听 UNIX 端口
netstat -lxnp

8)找出程序运行的端口
netstat -anp | grep ssh

NOTE:并不是所有的进程都能找到，没有权限的会不显示，使用 root 权限查看所有的信息。

10)找出运行在指定端口的进程
netstat -anp | grep ‘:3306’

11)持续输出 netstat 信息(每隔一秒输出网络信息)
netstat -cnp

12)显示所有端口的统计信息
netstat -s