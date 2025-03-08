---
title: 复盘一次SYN_SENT网络问题定位
date: 2025-03-02 21:59:48
tags:
 - Debug
 - 网络通信
---

## 背景

最近在做一个项目，使用一个公司内部开发的消息通信模块遇到了一个问题，就是进行本地socket通信，连接消息服务进程监听的一个端口时发现TCP连接始终建立不了，netstat查看端口始终处于SYN_SENT状态，当时花了好几天才最终定位出原因，是因为这个端口在iptables中设置了规则，只允许特定UID的进程能连接这个端口，由于没加规则，导致socket通信始终建立不起来。

经此一事，就反思后续这类问题该如何去定位，由于问题原因是由iptables导致的，所以在寻找有没有手段去分析数据包是否被拦截。

## 问题场景还原

### 问题场景描述

1、有A、B两个进程，B进程监听端口PortB

2、iptables设置了`INPUT`规则，任何发往端口PortB的数据包都会被丢弃（DROP）

3、A进程调用`connect`接口连接 127.0.0.1 的端口PortB，netstat命令查看端口PortB的连接，状态始终为SYN_SENT

### 问题场景构造

1、分别构造一个client和server程序，client连接server特定端口，这里从网上找了一个python写的client-server程序 [tcp_sixteen.py](https://github.com/brandon-rhodes/fopnp/blob/m/py3/chapter03/tcp_sixteen.py)，程序默认连接1060端口。

1）启动server，执行

```shell
python3 tcp_sixteen.py server 127.0.0.1
```

2）启动client，执行

```shell
python3 tcp_sixteen.py client 127.0.0.1
```

client会向server发送一个消息，server收到消息后会返回一个响应。server端输出如下：

![](server1.png)

client端输出如下：

![](client1.png)

由图可知，client端 `(127.0.0.1, 41292)` 连接server端 `(127.0.0.1, 1060)`。client端的端口是任意的，server端的端口固定是1060

2、新增一条iptables的filter表`INPUT`规则，拦截传给端口1060的数据包，执行命令

```shell
sudo iptables -A INPUT -p tcp --dport 1060 -j DROP
```

可以查看当前filter表的规则

```shell
sudo iptables -t filter -L -v -n --line-numbers
```

已新增一条规则：丢弃（DROP）所有发往1060端口的数据包

![](iptables1.png)

3、再一次执行client，向server发送消息，此时由于iptables规则，client发送消息超时

![](client_timeout.png)

使用`netstat`查看端口1060的连接情况，发现有个连接`127.0.0.1:59346 -> 127.0.0.1:1060`一直处于SYN_SENT状态，说明iptables规则生效了，已还原问题场景

```shell
sudo netstat -tunalp | grep :1060
```

![](netstat1.png)

## 问题定位

### 预备知识

由于涉及到iptables使用，需要了解关键概念：表Table、链Chain，还有使用iptables的`TRACE`功能，需要设置并查看跟踪日志。这些预备知识可以参考下面文章进行学习：

1. [ArchLinux Wiki: iptables](https://wiki.archlinux.org/title/Iptables)
2. [Chapter 6. Traversing of tables and chains](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html#TRAVERSINGOFTABLES)
3. [鸟哥私房菜：7.2.2 iptables的表格（table）与链（chain）](https://linux.vbird.org/linux_server/rocky9/0180firewall.php#7.2.2)
4. [数据包如何游走于 Iptables 规则之间？](https://www.bilibili.com/opus/830723460482203664)

### IPTables Trace

要定位是否由iptables设置的规则拦截导致的，这里需要用到iptables的`TRACE`功能。要使用`TRACE`功能，首先需要了解一个数据包从client发往server，需要经过iptables哪些表和链

搬一张ArchLinux Wiki的[iptables](https://wiki.archlinux.org/title/Iptables)教程中的图

![](tables_traverse.jpg)

由上图可以看到，本节点进程A发数据包给同节点的进程B，需要经过的表和链如下：

1、进程A发出数据包（从图中 Local Process 节点开始）

1) Local Process
2) Routing Decision
3) raw表OUTPUT链
4) mangle表OUTPUT链
5) nat表OUTPUT链
6) filter表OUTPUT链
7) Routing Decision
8) mangle表POSTROUTING链
9) nat表POSTROUTING链
10) NETWORK

2、发往进程B的数据包（从图中最顶端的节点 NETWORK开始）

1) NETWORK
2) raw表PREROUTING链
3) mangle表PREROUTING链
4) nat表PREROUTING链
5) Routing Decision
6) mangle表INPUT链
7) filter表INPUT链
8) Local Process

`TRACE`功能只能在iptables的raw表中使用，需要在raw表的链中添加规则，跟踪特定数据包，输出其经过的表和链到日志中，通过分析日志可以知道哪些规则拦截了数据包

可以看出，本节点进程A和B间的TCP通信会经过raw表的OUTPUT、PREROUTING链，可以在其中一个新增trace规则进行数据包跟踪

### Trace Packet

在raw表OUTPUT链中新增目的端口1060的trace规则，执行以下命令

```shell
sudo iptables -t raw -A OUTPUT -p tcp --dport 1060 -j TRACE
```

再次执行client，向server发消息，使用`netstat`查看端口1060连接情况

![](netstat2.png)

client尝试从端口55714发送TCP请求给端口1060，但是都没收到server的ACK。这时可以查看内核日志，grep下trace关键字

```shell
sudo dmesg | grep -i trace
```

trace规则抓到日志如下

```
// 第一次TCP请求
[ 7579.448153] TRACE: raw:OUTPUT:policy:2 IN= OUT=lo SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48009 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7182C20000000001030307) UID=1000 GID=1000
[ 7579.448160] TRACE: filter:OUTPUT:policy:1 IN= OUT=lo SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48009 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7182C20000000001030307) UID=1000 GID=1000
[ 7579.448167] TRACE: raw:PREROUTING:policy:1 IN=lo OUT= MAC=00:00:00:00:00:00:00:00:00:00:00:00:08:00 SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48009 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7182C20000000001030307)
[ 7579.448169] TRACE: filter:INPUT:rule:1 IN=lo OUT= MAC=00:00:00:00:00:00:00:00:00:00:00:00:08:00 SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48009 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7182C20000000001030307)

// 第二次TCP请求
[ 7580.449767] TRACE: raw:OUTPUT:policy:2 IN= OUT=lo SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48010 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7186AC0000000001030307) UID=1000 GID=1000
[ 7580.449955] TRACE: filter:OUTPUT:policy:1 IN= OUT=lo SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48010 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7186AC0000000001030307) UID=1000 GID=1000
[ 7580.450133] TRACE: raw:PREROUTING:policy:1 IN=lo OUT= MAC=00:00:00:00:00:00:00:00:00:00:00:00:08:00 SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48010 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7186AC0000000001030307)
[ 7580.450137] TRACE: filter:INPUT:rule:1 IN=lo OUT= MAC=00:00:00:00:00:00:00:00:00:00:00:00:08:00 SRC=127.0.0.1 DST=127.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=48010 DF PROTO=TCP SPT=55714 DPT=1060 SEQ=3916344369 ACK=0 WINDOW=65495 RES=0x00 SYN URGP=0 OPT (0204FFD70402080A3E7186AC0000000001030307)

// ..... 多次重复TCP请求
```

日志中关键字段：

1. 源端口Source Port：关键字`SPT=`
2. 目的端口Destination Port：关键字`DPT=`
3. 经过的规则：关键字`TRACE: xxx表:xxx链:<policy|rule>:规则ID`

从第一次TCP请求日志可以获取到以下信息：

1. TCP请求源端口为55714，目的端口为1060
2. 经过的规则顺序为：

    1) raw表OUTPUT链policy 2
    2) filter表OUTPUT链policy 1
    3) raw表PREROUTING链policy 1
    4) filter表INPUT链**rule 1**

可以分析出从端口55714发送的TCP请求最终只走到filter表INPUT链的规则1，说明这个规则可能导致TCP请求未发到server端。通过iptables命令查看这个规则：

```
sudo iptables -t filter -L -v -n --line-numbers
```

![](iptables2.png)

规则1刚好就是之前新增的拦截规则，至此可以得出结论是由于filter表的INPUT规则导致TCP请求未成功发送
