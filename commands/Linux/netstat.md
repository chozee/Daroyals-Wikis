netstat

# 从应用角度看netstat的作用

## 查看端口
```shell
# 列出所有端口
netstat -a | more

#列出所有tcp端口
netstat -at
netstat -au    #列出所有udp端口   

#显示TCP或UDP端口的统计信息
netstat -st 
netstat -su   

# 查看端口是否打开
netstat -ntulp |grep 80 
netstat -an | grep 3306 

netstat -ntlp   //查看当前所有tcp端口·

#显示所有端口的统计信息
netstat -s
```


## PID相关
#输出中显示PID和进程名称
netstat -pt

## 查看进程、程序访问了哪些地址
```shell
ps -ef | grep your_application_name OR others
netstat -anp | grep pid
```


## 查看一个IP：PORT被哪个程序访问了
```shell
netstat -anp | grep 127.3.2.1:5566
ps -ef | grep pid
```



## 通过应用程序找端口
```
#找出程序运行的端口
netstat -ap | grep ssh
```

## 通过端口找进程或应用程序
```shell
# 找出运行在指定端口的进程
netstat -an | grep ':80'
# 通过端口找进程ID
netstat -anp|grep 8081 | grep LISTEN|awk '{printf $7}'|cut -d/ -f1

```


## 网卡信息 路由
```
#显示网络接口列表
netstat -i

#显示详细信息,类似于ifconfig
netstat -ie

#输出中不显示主机，端口和用户名
netstat -an

#每隔一秒输出网络信息
netstat -c

#显示系统不支持的地址族 (Address Families)
netstat --verbose

#显示核心路由信息
netstat -r

```


##  监听相关 监听状态的 Sockets
```shell
netstat -l        #只显示监听端口
netstat -lt       #只列出所有监听 tcp 端口
netstat -lu       #只列出所有监听 udp 端口
netstat -lx       #只列出所有监听 UNIX 端口

```

## 统计信息什么用?? todo
```shell
netstat -s    #显示所有端口的统计信息
netstat -st   #显示TCP端口的统计信息
netstat -su   #显示UDP端口的统计信息
# 查看连接某服务端口最多的的IP地址
netstat -ntu | grep :80 | awk '{print $5}' | cut -d: -f1 | awk '{++ip[$1]} END {for(i in ip) print ip[i],"\t",i}' | sort -nr

```

# 显示字段含义

## 活动的Internet网络连接 (TCP, UDP, raw)
> Proto
套接字使用的协议。 

> Recv-Q
连接此套接字的用户程序未拷贝的字节数。 

> Send-Q
远程主机未确认的字节数。 

> Local Address
套接字的本地地址(本地主机名)和端口号。除非给定-n --numeric ( -n ) 选项，否则套接字地址按标准主机名(FQDN)进行解析，而端口号则转换到相应的服务名。 

> Foreign Address
套接字的远程地址(远程主机名)和端口号。 Analogous to "Local Address." 

> State
套接字的状态。因为在RAW协议中没有状态，而且UDP也不用状态信息，所以此行留空。通常它为以下几个值之一
>> * ESTABLISHED套接字有一个有效连接。
>> * SYN_SENT 套接字尝试建立一个连接。
>> * SYN_RECV 从网络上收到一个连接请求。
>> * FIN_WAIT1 套接字已关闭，连接正在断开。
>> * FIN_WAIT2 连接已关闭，套接字等待远程方中止。
>> * TIME_WAIT 在关闭之后，套接字等待处理仍然在网络中的分组
>> * CLOSED 套接字未用。
>> * CLOSE_WAIT 远程方已关闭，等待套接字关闭。
>> * LAST_ACK 远程方中止，套接字已关闭。等待确认。
>> * LISTEN套接字监听进来的连接。如果不设置 --listening (-l) 或者 --all (-a) 选项，将不显示出来这些连接。
>> * CLOSING套接字都已关闭，而还未把所有数据发出。
>> * UNKNOWN套接字状态未知。


> User 套接字属主的名称或UID。 

> PID/Program name
以斜线分隔的处理套接字程序的PID及进程名。 --program 使此栏目被显示。你需要 superuser 权限来查看不是你拥有的套接字的信息。对IPX套接字还无法获得此信息。 

> Timer
todo 

活动的UNIX域套接字

## 套接字（活动的UNIX域套接字）
注意Unix域套接字与TCP相关的字段取值是不一样的。要注意

> Proto
套接字所用的协议(通常是unix)。 

> RefCnt
使用数量(也就是通过此套接字连接的进程数)。 

> Flags
>> 1. `ACC` - SO_ACCEPTCONN：表示套接字已经开始监听连接请求。对于未连接的套接字，如果相应的进程正在等待连接请求，则显示此标志。
>> 2. `W` - SO_WAITDATA：表示套接字正在等待接收数据。当有数据到达套接字时，进程会收到通知。
>> 3. `N` - SO_NOSPACE：表示套接字缓冲区已满，无法容纳更多数据。发送方需要等待直到接收方清空缓冲区。


> Type
套接字使用的一些类型：
>> STREAM：流套接字，它提供了可靠的、双向的、基于连接的字节流通信。这是 Unix 域套接字中最常见的类型，类似于 TCP 套接字。

>> DGRAM：数据报套接字，它提供了无连接的、不可靠的消息传递服务。类似于 UDP 套接字，但在 Unix 域中。

>> SEQPACKET：顺序分组套接字，它提供了可靠的、双向的、基于连接的消息传递服务。与流套接字类似，但是消息边界得到了保留。

>> RAW：原始套接字，这种类型的套接字提供了对底层通信协议的直接访问，用于实现新协议或访问非标准特性。

>> RDM（Reliably Delivered Message，可靠传递消息）是一种套接字类型，它提供了可靠的、无连接的消息传递服务。RDM 类型套接字保证消息按照发送顺序正确传递，但与 SEQPACKET 不同，它不是基于连接的。在实际应用中，RDM 套接字类型并不常见，主要用于特定场景下的可靠消息传输。了解 RDM 类型套接字有助于分析其通信模式和可靠性特性。然而，需要注意的是，RDM 类型主要用于某些特定的协议，而不是在 Unix 域套接字中广泛使用的

>> PACKET：这种类型的套接字用于与数据链路层通信。PACKET 套接字允许你直接与网络设备进行通信，绕过传输层和网络层协议。它们通常用于网络诊断、监控和实现新协议等。需要注意的是，PACKET 套接字不是 Unix 域套接字，而是用于网络套接字。

>> UNKNOWN：这表示套接字类型未知或无法识别。在 netstat 输出中看到 UNKNOWN 类型的套接字时，可能是因为该套接字尚未完全初始化或存在问题。UNKNOWN 类型可能需要进一步调查，以确保系统中的网络连接和进程正常运行。

在使用 netstat 命令时，Type 列可以帮助你了解 Unix 域套接字的类型。结合其他列（如本地和远程地址、状态、进程等），你可以更好地理解系统上的 Unix 域套接字活动


> State
>> FREE：套接字未分配。

>> LISTENING：套接字正在监听一个连接请求。

>> CONNECTING：套接字正要建立连接。

>> CONNECTED：套接字已连接。

>> DISCONNECTING：套接字已断开。

>> (empty)：套接字未连接。


查找请求数请20个IP（常用于查找攻来源）
```shell
netstat -anlp|grep 80|grep tcp|awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -nr|head -n20

netstat -ant |awk '/:80/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A[i],i}' |sort -rn|head -n20
```

## 用tcpdump嗅探80端口的访问看看谁最高

```shell
tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -nr |head -20
```

查找较多time_wait连接
```shell
netstat -n|grep TIME_WAIT|awk '{print $5}'|sort|uniq -c|sort -rn|head -n20
```

找查较多的SYN连接
```shell
netstat -an | grep SYN | awk '{print $5}' | awk -F: '{print $1}' | sort | uniq -c | sort -nr | more
```

Reference
https://wangchujiang.com/linux-command/c/netstat.html

https://www.jianshu.com/p/4d5df145268e

http://linux.51yip.com/search/netstat
