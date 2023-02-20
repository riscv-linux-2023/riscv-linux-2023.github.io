---
layout: post
title:  "《UNIX网络编程 卷1 套接字联网API(第3版)》读书笔记"
date:   2023-02-14 11:08:59 +0800
categories: net book
---

## OSI

![osi](/assets/images/unp/osi.png)

## 传输层

![tcp3](/assets/images/unp/tcp3.jpg)

![tcp4](/assets/images/unp/tcp4.jpg)

![tcp state](/assets/images/unp/tcp_state.jpg)

![tcp-exchange](/assets/images/unp/tcp-exchange.png)

![port](/assets/images/unp/port.png)

![client](/assets/images/unp/client.png)

![tcp_buffer](/assets/images/unp/tcp_buffer.png)

![protocol](/assets/images/unp/protocol.png)

## socket 编程

```
struct sockaddr_in {
    short int sin_family;           // 地址族，始终设置为 AF_INET
    unsigned short int sin_port;    // 端口号，以网络字节序表示
    struct in_addr sin_addr;        // IPv4 地址，以网络字节序表示
    unsigned char sin_zero[8];      // 未使用的字段，通常设置为全零
};

struct in_addr {
    unsigned long int s_addr;       // IPv4 地址，以网络字节序表示
};
```

```
struct sockaddr {
    unsigned short sa_family; // 地址族，如 AF_INET 表示 IPv4 地址族
    char sa_data[14]; // 地址信息
};
```

函数

```
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

![socket_addr](/assets/images/unp/socket_addr.png)

## TCP

![socket_tcp](/assets/images/unp/socket_tcp.png)

![socket_family_type](/assets/images/unp/socket_family_type.png)

## TCP client / server

![tcp_client](/assets/images/unp/tcp_client.png)

![tcp_server](/assets/images/unp/tcp_server.png)

## I/O 复用

![blocking_io](/assets/images/unp/blocking_io.png)

![nonblocking_io](/assets/images/unp/nonblocking_io.png)

![io_multiplexing](/assets/images/unp/io_multiplexing.png)

![sig_io](/assets/images/unp/sig_io.png)

![asynchronous_io](/assets/images/unp/asynchronous_io.png)

![io](/assets/images/unp/io.png)

## UDP

![udp](/assets/images/unp/udp.png)

```c
#include <sys/types.h>
#include <sys/socket.h>

ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,
    struct sockaddr *src_addr, socklen_t *addrlen);

ssize_t sendto(int sockfd, const void *buf, size_t len, int flags,
    const struct sockaddr *dest_addr, socklen_t addrlen);
```

![udp_client](/assets/images/unp/udp_client.png)

![udp_server](/assets/images/unp/udp_server.png)

## 高级 UDP

可靠性：
1. 超时和重传：用于处理丢失的数据报
    1. 请求丢失
    2. 应答丢失
    3. RTO 太小
2. 序列号：供客户验证一个应答是否匹配相应的请求

RTO: 重传超时（retransmission timeout）

![timer](/assets/images/unp/timer.png)

## 信号驱动 IO

* 大部分左侧
* NTP 网络时间协议，英文名称：Network Time Protocol 使用右侧

![udp_sigio](/assets/images/unp/udp_sigio.png)
