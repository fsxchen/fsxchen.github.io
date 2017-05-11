---
title: Unix-Network(2)-地址以及字符串操作函数
date: 2016-7-17 20:46
categories:
	- Read
tags:
	- Misc
---
# 套接字地址结构

## IPV4套接字地址结构

```
struct in_addr {
    in_addr_t s_addr;
};

struct sockaddr_in {
    uint8_t         sin_len;
    sa_family_t     sin_family;
    in_port_t       sin_port;
    struct in_addr  sin_addr;

    char            sin_zero[8];
};
```

## 通用套接字

```
```

## 字节排序函数
大端机和小端机
```
uint16_t htons(uint16_t host16bitvalue);
uint32_t htonl(uint32_t host32bitvalue);

uint16_t htonl(uint16_t net16bitvalue);
uint32_t ntohl(uint32_t net32bitvalue);
```

## 网络字节序和主机字节序

## 字节操纵函数

Berkeley的函数
```
#include <strings.h>
void bzero(void *dest, size_t nbytes);
void bcopy(const void *src, void *dest, size_t nbytes);
int bcmp(const void *ptrl, const void *ptr2, size_t nbytes);

```

ANSI C的函数

```
void *memset(void *dest, int c, size_t len);
void *memcpy(void *dest, const void *src, size_t nbytes);
int memcmp(const void *ptrl, const void *ptr2, size_t nbytes);
```

## 两组地址转换函数

```
int inet_aton(const char *strptr, struct in_addr *addrptr);
in_addr_t inet_addr(const char *strptr);
char *inet_ntoa(struct in_addr inaddr);
```

## readn, writen和readline函数
字节流套接字（例如`TCP`套接字）上的`read`和`write`函数所表现的行为不同于通常的文件I/O。字节流套接字上调用`read`或`write`输入或输出的字节数可能比请求的数量少，然而这不是出错的状态。这个现象的原因在于内核中用于套接字的缓冲区可能已经达到了极限。
