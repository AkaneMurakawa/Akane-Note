# Dubbo

文档：https://dubbo.apache.org/zh/docs/v2.7/user/



## 架构变化

https://dubbo.apache.org/zh/docs/v2.7/user/preface/background/

SOA 架构: Service-Oriented Architecture

## RPC

RPC：远程过程调用协议（**R**emote **P**rocedure Call Protocol），它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。

原来的几种RPC框架：DCOM，CORBA，RMI（Java）等

- RMI——Remote Method Invoke：调用远程的方法。“方法”一般是附属于某个对象上的，所以通常RMI指对在远程的计算机上的某个对象，进行其方法函数的调用。
- RPC——Remote Procedure Call：远程过程调用。指的是对网络上另外一个计算机上的，某段特定的函数代码的调用。



## RPC和HTTP对比

### 传输协议

- RPC：可以基于TCP协议，也可以基于HTTP协议
- HTTP：基于HTTP协议

### 传输效率

- RPC：使用自定义的TCP协议，可以让请求报文体积更小，或者使用HTTP2协议，也可以很好的减少报文的体积，提高传输效率

- HTTP：如果是基于HTTP1.1的协议，请求中会包含很多无用的内容，如果是基于HTTP2.0，那么简单的封装以下是可以作为一个RPC来使用的，这时标准RPC框架更多的是服务治理

  

### 性能消耗

- RPC：可以基于thrift实现高效的二进制传输
- HTTP：大部分是通过json来实现的，字节大小和序列化耗时都比thrift要更消耗性能



### 负载均衡

- RPC：基本都自带了负载均衡策略
- HTTP：需要配置Nginx，HAProxy来实现



### 服务治理

- RPC：能做到自动通知，不影响上游
- HTTP：需要事先通知，修改Nginx/HAProxy配置



### 总结

RPC主要用于公司内部的服务调用，性能消耗低，传输效率高，服务治理方便。HTTP主要用于对外的异构环境，浏览器接口调用，APP接口调用，第三方接口调用等。



## Apache Dubbo

>  Apache Dubbo is a high-performance, java based open source RPC framework.

![dubbo-architucture](https://dubbo.apache.org/imgs/user/dubbo-architecture.jpg)



**节点角色说明**

| 节点        | 角色说明                               |
| ----------- | -------------------------------------- |
| `Provider`  | 暴露服务的服务提供方                   |
| `Consumer`  | 调用远程服务的服务消费方               |
| `Registry`  | 服务注册与发现的注册中心               |
| `Monitor`   | 统计服务的调用次数和调用时间的监控中心 |
| `Container` | 服务运行容器                           |

**调用关系说明**

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

Dubbo 架构具有以下几个特点，分别是连通性、健壮性、伸缩性、以及向未来架构的升级性。



### 协议

#### Dubbo协议(官方推荐协议)

优点：

采用NIO复用单一长连接，并使用线程池并发处理请求，减少握手和加大并发效率，性能较好（推荐使用） 

缺点：

大文件上传时,可能出现问题(不使用Dubbo文件上传)

### 注册中心

####  Zookeeper(官方推荐)

优点:

支持分布式.很多周边产品.

缺点: 

受限于Zookeeper软件的稳定性.Zookeeper专门分布式辅助软件,稳定较优