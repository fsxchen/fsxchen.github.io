---
title: Unix-Network(3)-基本socket
date: 2016-7-17 20:46
categories:
	- Read
tags:
	- network program
---
# 基本socket

## `socket`函数

```
SYNOPSIS
     #include <sys/socket.h>

     int
     socket(int domain, int type, int protocol);
```

family: 协议地址族

`AF_xxx`:表示地址族
`PF_xxx`:表示协议族
基本上都是采用的`AF_xxx`


## `connect`函数
```
SYNOPSIS
     #include <sys/types.h>
     #include <sys/socket.h>

     int
     connect(int socket, const struct sockaddr *address,
         socklen_t address_len);
```

如果是TCP套接字，调用`connect`将发起三次握手，建立成功或出错返回，出错的情况如下：

* 若TCP客户端没有收到SYN分节的响应，则返回`TIMEDOUT`错误。举例来说，调用`connect`函数时，发送一个`SYN`包后若无响应则等待6s后再发一个，若无响应则再等待24s。若总共75s时仍未收到则出现本错误。

* 若对客户端的｀SYN｀的响应是`RST`,则表明该服务器主机在我们指定的端口没有进程在等待与之连接。这是一种硬错误，客户端一旦收到`RST`，则马上返回`ECONNREFUSED`错误。

    **产生`RST`的三个条件：一是目的地为某端口的`SYN`到达服务器，可是服务器端口并未开放；二是TCP想取消一个已有连接；三是TCP接收到一个根本不存在的连接上的分节。**
* 若客户端发出的`RST`在中间的某个路由器引发了一个“destination unreachable”的ICMP错误，则认为这是一种软错误。客户机内核保存该消息，然后就像情况一一样继续发送，到规定时间（有的是75s）仍未响应，则把保存的消息作为`EHOSTUNREACH`或`ENETUNREACH`返回给进程。  

**端口扫描原理分析**
NMAP端口扫描
TCP扫描的原理主要是三次握手。这种扫描也称为SYN扫描。Scanner向主机发起SYN请求。如果服务器回复SYN、ACK，这说明此端口开放。如果回复TCP、RST，表示端口未开放。如果没有回复，那么就可能没有这个服务器，或者数据包被防火墙丢弃。

UDP的扫描原理：由于UDP没有三次握手，所以UDP的扫描原理不一样。同样在Scanner发起一次UDP请求。如果服务器回复ICMP的端口不可达。则说明该服务器的此端口关闭。如果没有回应，这说明：一、服务器开放该端口。二、服务器主机不存在。三、被防火墙等设备丢弃。

ZMAP端口扫描


## `bind`函数
```
SYNOPSIS
     #include <sys/socket.h>

     int
     bind(int socket, const struct sockaddr *address, socklen_t address_len);
```
第二个参数是一个指向特定于协议的地址结构的指针，第三个为该地址结构的长度。
返回的常见错误`EADDRINUSE`，地址已经被使用。
## `listen`函数
```
#include <sys/socket.h>

     int
     listen(int socket, int backlog);
```

为了理解`bloglog`参数，我们必须认识到内核为任何一个给定的监听的套结字维护两个队列：

* 1) 未完成连接队列，每个这样的`SYN`分节对应其中一项:已由某个客户端发出并到达服务器，而服务器正在等待完成相应的三次握手过程。这些套接字处于`SYN_RCVD`状态。

* 2）已完成连接队列，每个已完成`TCP`三路握手过程的客户端对应其中一项。这些套结字处于`ESTABLISHED`状态。

两队列之和不操过blacklog。

## `accept`函数

```
SYNOPSIS
     #include <sys/socket.h>
     int
     accept(int socket, struct sockaddr *restrict address, socklen_t *restrict address_len);
```
参数`cliaddr`和`addrlen`用来返回已连接的对端进程的协议地址，调用前，我们将由`*addrlen`所引用的整数值置为有`cliaddr`所指的套接字地址结构的长度，返回时，该整数值即为内核存放该套接字地址结构内的确切长度。所以`address_len`是一个值结果参数。

如果`accept`成功，那么其返回值是有内核自动生成的一个全新的描述符。

如果我们队客户协议地址不感兴趣，可以把后面两个参数均设置成空指针。


## `fork`和`exec`函数

## 并发服务器
一个最简单的并发服务器
```
for(;;) {
    connfd = accept(listen, ...);
    if ((pid = fork()) == 0) {
        close(listenfd);
        doit(connfd);
        close(connfd);
    }
    close(connfd);
}
```

对一个套接字调用colse方法，会导致内核发送一个`FIN`包，但是父进程调用了`close(connfd)`,并没有发送`FIN`.
因为每个文件或套接字都由一个引用计数，引用计数放在文件表项中维护。


## `getsockname`和`getpeername`函数
