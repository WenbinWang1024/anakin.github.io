# 网络协议

## TCP/IP协议概述

1. 什么是协议？为什么要分层？
   1. 协议就是通信双方必须遵循的**规则**。
   2. 协议可以分为公有协议和私有协议。
   3. 分层之后，每一层分工明确，每一层使用下面一层的服务，同时向上一层提供服务。
2. TCP/IP模型和OSI参考模型
   1. OSI参考模型，七层：应用层，表示层，会话层，传输层，网络层，数据链路层，物理层。
   2. TCP/IP模型，四层：
      1. 应用层Application Layer：HTTP协议，FTP，SSH，TELNET协议。
      2. 传输层Transport Layer：TCP协议，UDP协议
      3. 网络层Internet Layer：IP协议，ICMP，IGMP
      4. 网络接口层Network Access Layer：ARP地址转换协议和，RARP反向地址转换协议。
   3. ![image-20200718111801256](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200718111801256.png)


