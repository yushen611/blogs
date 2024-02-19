---
title: 'go语言中的消息队列(MQ)'
date: 2023-03-26 01:43:16
tags: [go]
published: true
hideInList: true
feature: 
isTop: false
---

<meta name="referrer" content="no-referrer"/>

# 一、概述

## 消息队列的概念

### **定义**

> 消息队列是一种**通信模式**，它用于**在不同的应用程序之间传递消息**。它通常是在**分布式系统中使用的一种组件**，可以实现不同应用程序之间的**解耦**，从而提高系统的可伸缩性和可靠性。
>
> 在消息队列中，**消息是以异步的方式进行传递的**。发送者将消息发送到队列中，而接收者则从队列中获取消息。消息队列允许多个接收者同时从同一个队列中获取消息，并且可以支持多种消息传递模式，如点对点和发布/订阅模式。
>
> 消息队列还具有可靠性和持久性的特点。它可以确保消息的传递是有序的，而且即使在系统中断或故障的情况下，消息也不会丢失。

**类比理解**：

例子1

> 假设我们有一个在线商店，有很多顾客下单购买商品，而我们需要把商品快递送到顾客手中。这个过程可以类比为一个应用程序向另一个应用程序发送消息。
>
> 如果我们按照传统的方式，每个订单都直接从商店直接派送出去，那么我们可能会面临一些问题。首先，我们需要等待一个订单处理完成后才能开始处理下一个订单，这样会导致我们的速度变慢。其次，如果我们需要处理大量的订单，可能会因为系统负载过高而导致我们的系统崩溃。
>
> 这时，我们可以使用消息队列来解决这些问题。**我们可以把订单看作是消息，而把快递员看作是接收者，把仓库看作是队列**。当有新的订单下达时，我们可以把它放到仓库里面。这样，快递员就可以从仓库里面拿出订单，然后开始派送，而不需要等待商店处理完每一个订单。
>
> 使用消息队列的好处是，它可以让我们把请求和处理分离开来，从而实现系统的解耦和可伸缩性。同时，消息队列还可以确保消息的可靠传递，即使系统出现问题，也不会丢失重要的消息。

例子2：

> 假设你是一个销售手机的公司，你需要处理大量的订单，包括下单、支付、发货等流程。如果你直接使用同步的方式来处理订单，可能会面临以下问题：
>
> 1. 需要等待一个订单的处理完成后才能处理下一个订单，可能会导致系统响应变慢。
> 2. 如果有大量订单同时到来，可能会因为系统负载过高而导致系统崩溃。
> 3. 如果有一些订单处理失败，需要重新处理，可能会导致订单丢失或者重复处理。
>
> 这时候，你可以引入消息队列来解决这些问题。具体地，你可以将每一个订单看作是一个消息，将消息放到消息队列中，然后由另一个应用程序异步地从队列中获取消息并进行处理。
>
> 举个例子，当顾客下单时，你可以将订单信息放到消息队列中。然后，另一个应用程序可以从消息队列中获取订单信息，进行支付和发货等处理。这样，**订单处理的速度就不再受到一个订单处理完成的影响，而是可以根据处理能力自行调整，从而提高系统的可伸缩性和吞吐量**。
>
> 此外，消息队列还可以提供消息的持久化，即使系统崩溃或者应用程序异常终止，也不会丢失已经发送的消息。这样可以保证系统的可靠性，并避免订单丢失或者重复处理等问题

### 应用场景

1. 异步处理：消息队列可以用于将请求从应用程序异步地传递到后台处理程序。例如，当用户提交一个请求时，应用程序可以将请求放入消息队列中，并立即向用户发送响应，而后台处理程序则可以在后台异步地处理该请求。
2. 解耦系统组件：消息队列可以用于解耦系统中不同组件之间的通信。例如，一个应用程序可以将消息发送到消息队列中，而另一个应用程序可以从消息队列中读取消息，而不需要知道消息的发送者。
3. 广播：消息队列可以用于广播消息给多个接收者。例如，当一个新的产品发布时，可以将通知消息发送到消息队列中，所有的订阅者都可以从消息队列中读取到这个消息。
4. 延迟任务处理：消息队列可以用于将需要延迟处理的任务放到消息队列中。例如，当一个用户需要在未来的某个时间点接收一条消息时，应用程序可以将这条消息放到消息队列中，并设置一个定时器，在特定的时间点将这个消息发送给用户。
5. 流量控制：消息队列可以用于控制系统的流量。例如，当一个应用程序需要处理大量的请求时，可以将这些请求放到消息队列中，而不是直接处理这些请求。这样可以避免系统过载，并且可以平滑地处理请求。

## MQ的优点/为什么要用消息队列

### 1.解耦

![image-20230424204403930](https://gitee.com/yushen611/img/raw/master/image-20230424204403930.png)如图是三个微服务A,B,C，它们之间通过RPC调用。如果不使用消息队列，A与B与C之间的传递是同步的，一旦其中一个服务宕机，整个链路都会受到影响。如果加入了消息队列（如图)，B只需要把消息发送给MQ，C只需要从MQ中拿数据即可，这样就完成了B与C之间的解耦。

### 2.异步

之前是A，B，C之间是同步的方式，加入MQ后，B与C都是异步的从消息队列中读写消息的。

### 3.流量削峰填谷

<img src="https://gitee.com/yushen611/img/raw/master/image-20230424205306386.png" alt="image-20230424205306386" style="zoom:80%;" />

如果B每分钟30W并发，C每分钟只有1W并发，就**需要一个MQ做缓冲**。如果没有MQ,C就因为过大的流量爆掉了。

## MQ的缺点

### 1.可用性降低

加入一个消息中间件，就会多一个业务崩溃的风险。比如MQ挂了，业务就挂了。一般的解决方法是加入MQ集群。

### 2.复杂性提高

需要额外加入生产端与消费端的代码

### 3.数据一致性问题

如何确保**数据是否丢失**，也有可能出现**数据重复**。

## 常用的消息队列

### Go语言常用的消息队列

Go语言中有许多常用的消息队列，以下是其中的一些：

1. RabbitMQ：RabbitMQ是一个开源的消息队列，它支持多种消息协议，包括AMQP、STOMP和MQTT。
2. Kafka：Kafka是一个高吞吐量的分布式发布-订阅消息系统，它可以处理数百万条消息，同时还支持消息的持久化存储。
3. NSQ：NSQ是一个实时分布式消息平台，它具有高可用性、高可扩展性和低延迟等特点。
4. ActiveMQ：ActiveMQ是一个流行的消息代理和集成平台，它支持多种传输协议和消息协议，包括AMQP、STOMP、OpenWire和MQTT。
5. NATS：NATS是一个高性能、轻量级的分布式消息系统，它的设计目标是提供简单、快速和可靠的消息传递服务。

这些消息队列都有各自的特点和优势，可以根据项目的需求选择合适的消息队列。

### 常见消息队列比较

|            | ActiveMQ               | RabbitMQ                                                     | RocketMQ                                                     | Kafka                                                        |
| ---------- | ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 性能       | 6000QPS/单机           | 12000QPS/单机                                                | **10W/单机**                                                 | **100W/单机**                                                |
| 持久化     | 支持（性能会下降）     | 支持（性能会下降）                                           | **天然支持**                                                 | **天然支持**                                                 |
| 多语言支持 | 主流语言都支持         | 主流语言都支持                                               | **5.0版本**主流语言都支持                                    | 主流语言都支持                                               |
| 综合       | 缺乏大规模应用，不推荐 | 高可用(RabbitMQ使用Erlang语言编写，宕机率低);<br />提供管理页面；<br />内部机制难以了解（由于语言小众） | 模型简单；<br />接口易用；<br />功能丰富<br />在阿里大规模应用；<br />性能优秀;<br />java编写； | 天生分布式；<br />性能最好；<br />流失处理，大数据支持好<br />缺点：<br />运维难度大；<br />对带宽有要求 |



消息队列选择建议

> **1.Kafka**
>
> Kafka主要特点是基于Pull的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。
>
> 大型公司建议可以选用，如果有日志采集功能，肯定是首选kafka了。
>
> **2.RocketMQ**
>
> 天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。
>
> RoketMQ在稳定性上可能更值得信赖，这些业务场景在阿里双11已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择RocketMQ。
>
> **3.RabbitMQ**
>
> RabbitMQ :结合erlang语言本身的并发优势，性能较好，社区活跃度也比较高，但是不利于做二次开发和维护。不过，RabbitMQ的社区十分活跃，可以解决开发过程中遇到的bug。
>
> 如果你的数据量没有那么大，小公司优先选择功能比较完备的RabbitMQ。



## 消息重复问题解决方案

如果使用MQ，不可避免会会遇到MQ中的消息的重复。下面有三种解决方案。

### 1.消费时做幂等性处理

* **幂等性消息**：如果消息重复了，但执行了动作都类似于`set count = 10`，这样就算消息重复接受了，也不会影响count的值
* **非幂等性消息**：如果消息重复了，但执行了动作都类似于`set count = count  + 10`，这样消息重复接受了，count的值就取决于消息重复的数量了。

### 2.MVCC多版本并发控制(生产的时候带上版本号)

* 第一个消息带着版本version1来，与消费者需要的version1对上了，消费者就接受，并且把下次要接受的version设置为（version1 + 1），记录为version2
* 如果第二个消息与第一个消息重复，那它也是带着版本version1来，但消费者需要version2才接受，因此拒绝了这个消息的接收。
* 第三个消息与第一个消息不同，带着版本version2来，与消费者需要的version2对上了，消费者就接受，并且把下次要接受的version设置为（version2 + 1），记录为version3

### 3.去重表的方案

建立一个去重表A，往A中插入消息[**add(megId,version)**]，同样的消息ID相同。如果之前已经有一个ID相同的消息已经插入数据库去重表A了，后面来的同样的消息就插入不进去。



# 二、RocketMQ简明教程

## RocketMQ简介

RocketMQ是一种分布式消息中间件，基于高可用，高性能和高扩展性的设计理念。它的逻辑是将消息按照特定的规则进行存储和传递，为消息的传输提供可靠性和顺序性的保障。

**RocketMQ的架构**可以分为四个部分：消息生产者(Producer)，消息消费者(Consumer)，消息中间件(Broker)，以及消息路由配置(Namesrv)。

1. 消息生产者(Producer)：生产者是消息发送的客户端，负责将消息发送到消息队列中。生产者通过消息发送API将消息发送到指定主题(Topic)中。在发送消息时，生产者可以设置消息类型(Message Type)、消息优先级、消息过滤等属性。

2. 消息消费者(Consumer)：消费者是消息接收的客户端，负责从消息队列中订阅和消费消息。消费者可以订阅多个主题(Topic)和多个消息队列(Message Queue)，并可以设置消费模式(Consume Mode)、消费组(Consumer Group)、消息过滤(Filter)等属性。

3. 消息中间件(Broker)：消息中间件是RocketMQ最核心的部分，负责消息的存储、路由以及传输。Broker将消息存储到指定的消息队列(Message Queue)中，并提供了消息重试、消息丢失检测、消息查询等功能。

4. 消息路由配置(Namesrv)：Namesrv是RocketMQ的命名服务(Namespace)组件，负责管理所有RocketMQ Broker的信息，包括Broker地址、主题信息、消息队列信息等。Namesrv通过轮询方式将消息消费者连接到指定的Broker，以保证消息的可靠性和负载均衡。

在RocketMQ的架构中，消息生产者通过与消息中间件交互，将消息发送到指定的消息队列中，消息消费者通过与消息中间件交互，从消息队列中订阅和消费消息。消息中间件提供了多个Broker节点来存储和传输消息，并通过命名服务组件将消息生产者和消息消费者连接到指定的Broker节点。这个架构可以支持大规模的实时数据处理，并且具有高可靠性、高性能的特点。

![image-20230430081359447](https://gitee.com/yushen611/img/raw/master/image-20230430081359447.png)

**RocketMQ的逻辑包含以下几个要素**：

1. 主题（Topic）- 包含多个消息的逻辑容器。

2. 生产者（Producer）- 发送消息的客户端程序。

3. 消费者（Consumer）- 接收消息的客户端程序。

4. 消息队列（Message Queue）- **一个消息主题可以拥有多个消息队列**，每个消息队列包含一部分主题的消息。

5. 消息模式（Message Mode）- 支持顺序消息和广播消息两种模式。

6. 消息驱动（Message Driven）- 通过异步回调或者监听器的方式实现消息驱动。

   ![image-20230430080944401](https://gitee.com/yushen611/img/raw/master/image-20230430080944401.png)

==**注意：一个主题A假设有N个消息队列，那么这个主题A最多有N个消费者**==

偏移量：

即**队列中消息的数量**

<img src="https://gitee.com/yushen611/img/raw/master/image-20230430083323423.png" alt="image-20230430083323423" style="zoom:80%;" />



**RocketMQ的消息流程**为：

1. 生产者向指定的主题发送消息。
2. 消息中间件将消息存储到相应的消息队列中。
3. 消费者从消息队列中订阅消息。
4. 消息中间件将消息发送给消费者，并标记消息已被消费。

RocketMQ的优点是支持大规模数据处理和高可用性，可以通过配置、故障转移和备份机制保证数据的完整性和可靠性。此外，RocketMQ还提供了消息重试、消息过滤和批处理等特性，方便用户进行灵活的消息处理。





## RocketMQ的安装

官方网站：[RocketMQ · 官方网站 | RocketMQ (apache.org)](https://rocketmq.apache.org/zh/)



### 安装方式1：下载安装

参考： [(45条消息) RocketMQ 安装 For Windows10 (完整版）_栗超的博客-CSDN博客](https://blog.csdn.net/lichao_3013/article/details/100574517)

#### 1.下载release版

RocketMQ 的安装包分为两种，二进制包和源码包。 点击[这里](https://dist.apache.org/repos/dist/release/rocketmq/5.1.0/rocketmq-all-5.1.0-source-release.zip) 下载 Apache RocketMQ 5.1.0的源码包。你也可以从[这里](https://dist.apache.org/repos/dist/release/rocketmq/5.1.0/rocketmq-all-5.1.0-bin-release.zip) 下载到二进制包。二进制包是已经编译完成后可以直接运行的，源码包是需要编译后运行的。



#### 2.配置环境变量

    变量名：ROCKETMQ_HOME
    变量值：MQ解压路径\MQ文件夹名

#### 3.启动nameserver与broker服务

进入至‘MQ文件夹\bin’下 执行

```bash
# 启动nameserver
start mqnamesrv.cmd
#启动broker
start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true diskMaxUsedSpaceRatio=99
```





### 安装方式：docker安装（这部分存在问题，不要参考）

参考：[Docker部署RocketMQ5.x (单机部署+配置参数详解+不使用docker-compose直接部署)](https://blog.csdn.net/weixin_44606481/article/details/129758920)

#### **1. 拉取镜像**

[apache/rocketmq - Docker Image | Docker Hub](https://hub.docker.com/r/apache/rocketmq)

版本选择5.1.0 

![image-20230430131310307](https://gitee.com/yushen611/img/raw/master/image-20230430131310307.png)

```bash
docker pull apache/rocketmq:5.1.0
```

注：如果拉取过慢，换成阿里云的源，阿里云具体看官方（很简单，详细）：[阿里云容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

#### **2. 启动NameServer**



参考：[apache/rocketmq: Apache RocketMQ is a cloud native messaging and streaming platform, making it simple to build event-driven applications. (github.com)](https://github.com/apache/rocketmq)

```bash
docker run -d --name namesrv --net=host apache/rocketmq ./mqnamesrv

docker run -d --name namesrv -p 9876:9876 apache/rocketmq:5.1.0 ./mqnamesrv
```

> 这个命令指定了在容器内部启动 RocketMQ NameServer 的方式，其中：
>
> - `docker run` 表示以 Docker 容器的方式启动一个新的容器
> - -d：表示将容器在后台运行。 `-it` 表示以交互式的方式启动容器，可以进行命令行交互
> - --name namesrv：指定容器的名称为namesrv。
> - `--net=host` 表示使用主机网络，即容器直接使用宿主机的网络，这样可以方便地访问宿主机的地址和端口号
> - `apache/rocketmq` 表示启动使用的 Docker 镜像名称和版本号
> - `./mqnamesrv` 表示在容器内部以后台进程方式启动 RocketMQ NameServer
>
> 这样可以在 Docker 容器内部启动一个 RocketMQ NameServer，使其能够连接到 Docker 容器内部的 RocketMQ Broker，从而实现消息的传输和存储。和下一条命令一样，这个命令指定了需要的配置信息，因此也不需要读取配置文件。



*下面也介绍其他启动方式，可跳过*

> 其他启动方式
>
> 2. **先启动RocketMQ的namesrv服务**
>
> ```bash
> docker run -d -p 9876:9876 --name namesrv -e "MAX_POSSIBLE_HEAP=100000000" apache/rocketmq sh mqnamesrv
> ```
>
> > 这个命令是在docker中运行RocketMQ的namesrv服务。其中：
> >
> > - -d：表示将容器在后台运行。
> > - -p 9876:9876：表示将容器内部的9876端口映射为宿主机的9876端口。
> > - --name namesrv：指定容器的名称为namesrv。
> > - -e "MAX_POSSIBLE_HEAP=100000000"：指定容器中Java虚拟机的最大堆大小为100MB。
> > - apache/rocketmq：指定使用的Docker镜像为apache/rocketmq。
> > - sh mqnamesrv：在容器中执行的命令，启动namesrv服务。

#### **3. 启动Broker**



```bash
docker run -d --name rmqbroker --net=host --mount type=bind,source=D:\rmqdata,target=/home/rocketmq/store apache/rocketmq ./mqbroker -n localhost:9876

docker run -d --name rmqbroker -p 10911:10911 -p 10909:10909 --mount type=bind,source=D:\rmqdata,target=/home/rocketmq/store apache/rocketmq:5.1.0 ./mqbroker -n localhost:9876

docker run -d --restart=always --name broker -p 10911:10911 -p 10909:10909 --privileged=true -v /d/project_file/StudyDemo/rocketMq/conf/broker.conf:/opt/rocketmq/conf/broker.conf apache/rocketmq:5.1.0 sh mqbroker -c /opt/rocketmq/conf/broker.conf
```

> 这个docker命令的作用是以容器的方式启动apache/rocketmq镜像中的mqbroker进程，并将主机的D:\rmqdata目录挂载到容器内的/home/rocketmq/store目录中，以便将RocketMQ的消息存储在主机上。具体各选项的含义如下：
>
> - `docker run`: 运行一个新容器的命令。
> - `-d`: 以“守护进程”方式运行容器，即在后台运行。
> - `--name rmqbroker`: 为容器取一个名字，用于后续容器的管理操作。
> - `--net=host`: 使用主机的网络模式，容器和主机共享网络命名空间。
> - `--mount type=bind,source=D:\rmqdata,target=/home/rocketmq/store`: 将主机的D:\rmqdata目录挂载到容器内的/home/rocketmq/store目录中。
>   - `type=bind`: 指定挂载类型为bind类型，即主机目录与容器内目录进行映射。
>   - `source=D:\rmqdata`: 指定主机要挂载的本地目录路径。
>   - `target=/home/rocketmq/store`: 指定容器内挂载点的目录路径。
> - `apache/rocketmq`: 选择要运行的Docker镜像的名称。
> - `./mqbroker -n localhost:9876`: 在容器内运行mqbroker进程，并指定NameServer的地址为localhost:9876。

> **为什么这个命令不需要配置conf文件?**
>
> 这个命令指定了在容器内部启动 RocketMQ Broker 的地址和端口号，以及 NameServer 的地址和端口号，因此在容器内部启动 RocketMQ Broker 时不需要读取配置文件。
>
> 此外，在 Docker 容器内运行 RocketMQ Broker 时，由于容器与主机之间的网络隔离，一些传统方式的配置（如像在原来的物理环境中需要配置的 IP 地址，端口号等等）可能不再适用。因此，可以直接在 Docker 命令中指定需要的配置信息，而无需修改配置文件，简化了配置的过程。



*下面也介绍其他启动方式，可跳过*

> **其他启动方式**
>
> 启动Broker需要先创建一个broker配置文件`broker.conf`，例如：
>
> ```bash
> brokerClusterName = DefaultCluster
> brokerName = broker-a
> brokerId = 0
> deleteWhen = 04
> fileReservedTime = 48
> brokerRole = ASYNC_MASTER
> 
> 
> listenPort=10921
> advertisedPort=10921
> brokerIP1=172.17.0.2
> ```
>
> * 其中`brokerIP1`需要设置成你的Docker宿主机的IP地址。
>
>   > * `broker.conf` 文件中的 `listenPort` 配置项，将 Broker 监听客户端连接的端口号修改为需要的端口号。
>   >
>   > * 修改 `broker.conf` 文件中的 `brokerClusterName` 和 `brokerName` 配置项，将 Broker 间通信使用的端口号修改为需要的端口号。默认端口号如下：
>   >
>   >   - 10911：Master 与 Slave 同步消息的端口号。
>   >
>   >   - 10912：Slave 与 Master 进行消息复制的端口号。
>   >
>   >   - 10913：Broker 与 Broker 之间互相通信的端口号。
>   >
>   > * listenPort 和 advertisedPort 的值需要相同吗?不需要完全相同，但两个端口号需要处于同一子网，并且 advertisedPort 应该是可以公网访问的地址，用于在注册中心中告知其它的Consumer或者Producer访问Broker的地址。而 listenPort 指定的则是 Broker 内部使用的端口号，也就是在 broker 配置文件中使用的端口号。因此，两个端口号最好要一致，这样可以避免一些不必要的麻烦。
>
> 然后执行以下命令启动Broker容器：
>
> > 例如我的conf文件存储在这个路径`D:\project_file\StudyDemo\rocketMq\conf`，我应该这样写这个路径`/d/project_file/StudyDemo/rocketMq/conf/broker.conf`：
>
> * 简单版的参考命令：
>
> ```bash
> docker run -d --name rmqbroker -p 10921:10921 -v /d/project_file/StudyDemo/rocketMq/conf/broker.conf:/opt/rocketmq/conf/broker.conf apache/rocketmq sh mqbroker -n namesrv:9876
> 
> docker run -d --name rmqbroker -p 10911:10911 -p 10909:10909 -v /d/project_file/StudyDemo/rocketMq/conf/broker.conf:/opt/rocketmq/conf/broker.conf apache/rocketmq:5.1.0 sh mqbroker -n namesrv:9876
> ```
>
> > - `docker run` 是 Docker 的启动容器命令
> > - `-d` 表示将该容器运行在后台模式
> > - `--name rmqbroker` 给该容器取一个名称叫做 `rmqbroker`
> > - `-p 10921:10921` 选项将容器中的 10921 端口映射到宿主机的 10921 端口
> > - `-v /d/project_file/StudyDemo/rocketMq/conf/broker.conf:/opt/rocketmq/conf/broker.conf` 选项将宿主机上的 `/d/project_file/StudyDemo/rocketMq/conf/broker.conf` 文件挂载到容器内部的 `/opt/rocketmq/conf/broker.conf` 目录下
> > - `apache/rocketmq` 是该容器所使用的 Docker 镜像名称，表示使用官方提供的 RocketMQ 镜像
> > - `sh mqbroker -n namesrv:9876` 是在容器中执行的命令，其中 `sh` 表示使用 Shell 命令解释器，`mqbroker` 表示启动 RocketMQ Broker 服务，`-n` 选项指定了 Broker 服务所连接的 Namesrv 服务的 IP 地址和端口号，这里使用的是 `namesrv:9876`，其中 `namesrv` 表示 Namesrv 服务所在的主机名，`9876` 表示 Namesrv 服务的默认端口号。
>
> 



#### 4. 启动rocketmq console

[下载 | RocketMQ (apache.org)](https://rocketmq.apache.org/zh/download)

<img src="https://gitee.com/yushen611/img/raw/master/image-20230430111546933.png" alt="image-20230430111546933" style="zoom:80%;" />

下载后里面有个readme文件，指导了如何拉取这个cosole的镜像并使用。

<img src="https://gitee.com/yushen611/img/raw/master/image-20230430112208454.png" alt="image-20230430112208454" style="zoom:80%;" />

**With Docker**

[apacherocketmq/rocketmq-console - Docker Image | Docker Hub](https://hub.docker.com/r/apacherocketmq/rocketmq-console)

* get docker image

```
mvn clean package -Dmaven.test.skip=true docker:build
```

or

```
docker pull apacherocketmq/rocketmq-console:2.0.0
```

> currently the newest available docker image is apacherocketmq/rocketmq-console:2.0.0


* run it (change namesvrAddr and port yourself)

```bash
docker run -d  --name rmqconsole -e "JAVA_OPTS=-Drocketmq.namesrv.addr=127.0.0.1:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8080:8080 -t apacherocketmq/rocketmq-console:2.0.0
```



## Go 语言操作 RocketMQ 的简单示例



### 生产者发送消息示例代码

```go
package main

import (
	"context"
	"fmt"
	"os"
	"strconv"

	"github.com/apache/rocketmq-client-go/v2"
	"github.com/apache/rocketmq-client-go/v2/primitive"
	"github.com/apache/rocketmq-client-go/v2/producer"
)

// Package main implements a simple producer to send message.
func main() {
	p, _ := rocketmq.NewProducer(
		producer.WithNsResolver(primitive.NewPassthroughResolver([]string{"127.0.0.1:9876"})),
		producer.WithRetry(2),
	)
	err := p.Start()
	if err != nil {
		fmt.Printf("start producer error: %s", err.Error())
		os.Exit(1)
	}
	topic := "test"

	for i := 0; i < 10; i++ {
		msg := &primitive.Message{
			Topic: topic,
			Body:  []byte("Hello RocketMQ Go Client! " + strconv.Itoa(i)),
		}
		res, err := p.SendSync(context.Background(), msg)

		if err != nil {
			fmt.Printf("send message error: %s\n", err)
		} else {
			fmt.Printf("send message success: result=%s\n", res.String())
		}
	}
	err = p.Shutdown()
	if err != nil {
		fmt.Printf("shutdown producer error: %s", err.Error())
	}
}
```



### 消费者发送消息接收代码

```go
package main

import (
	"context"
	"fmt"
	"os"

	"github.com/apache/rocketmq-client-go/v2"
	"github.com/apache/rocketmq-client-go/v2/consumer"
	"github.com/apache/rocketmq-client-go/v2/primitive"
)

func main() {
	sig := make(chan os.Signal)
	c, err := rocketmq.NewPushConsumer(
		consumer.WithGroupName("testGroup"),
		consumer.WithNsResolver(primitive.NewPassthroughResolver([]string{"127.0.0.1:9876"})),
	)
	if err != nil {
		fmt.Printf("Failed to create consumer: %s", err.Error())
		return
	}
	err = c.Subscribe("test", consumer.MessageSelector{}, func(ctx context.Context,
		msgs ...*primitive.MessageExt) (consumer.ConsumeResult, error) {
		for _, msg := range msgs {
			fmt.Printf("receive successfully: %s \n", msg.Body)
		}

		return consumer.ConsumeSuccess, nil
	})
	if err != nil {
		fmt.Println(err.Error())
	}
	// Note: start after subscribe
	err = c.Start()
	if err != nil {
		fmt.Printf("Failed to start consumer: %s", err.Error())
		os.Exit(-1)
	}
	<-sig
	err = c.Shutdown()
	if err != nil {
		fmt.Printf("shutdown Consumer error: %s", err.Error())
	}
}

```

### 避免消息重复的示例代码

生产者

```go
package main

import (
    "fmt"
    "github.com/apache/rocketmq-client-go/v2"
    "github.com/apache/rocketmq-client-go/v2/primitive"
)

func main() {
    // 创建一个生产者实例
    p, err := rocketmq.NewProducer(
        rocketmq.WithNameServer([]string{"127.0.0.1:9876"}),
    )
    if err != nil {
        fmt.Printf("create producer error: %s\n", err)
        return
    }

    // 启动生产者实例
    err = p.Start()
    if err != nil {
        fmt.Printf("start producer error: %s\n", err)
        return
    }
    defer p.Shutdown()

    // 生成一组消息
    msgs := []*primitive.Message{
        {
            Topic: "test_topic",
            Body:  []byte("hello, rocketmq!"),
        },
        {
            Topic: "test_topic",
            Body:  []byte("hello, again!"),
        },
    }

    // 发送消息
    for _, msg := range msgs {
        _, err = p.SendSync(msg)
        if err != nil {
            fmt.Printf("send message error: %s\n", err)
            continue
        }
    }
}

```

消费者

```go
package main

import (
    "fmt"
    "github.com/apache/rocketmq-client-go/v2"
    "github.com/apache/rocketmq-client-go/v2/consumer"
    "github.com/apache/rocketmq-client-go/v2/primitive"
    "sync"
)

func main() {
    // 创建一个消费者实例
    c, err := rocketmq.NewPushConsumer(
        consumer.WithNameServer([]string{"127.0.0.1:9876"}),
        consumer.WithGroupName("test_group"),
    )
    if err != nil {
        fmt.Printf("create consumer error: %s\n", err)
        return
    }

    // 注册消息处理函数
    err = c.Subscribe("test_topic", consumer.MessageSelector{}, func(ctx context.Context, msgs ...*primitive.MessageExt) (consumer.ConsumeResult, error) {
        for _, msg := range msgs {
            // 检查是否为重复消息
            if checkDuplicate(msg) {
                // 已处理过的重复消息直接忽略
                continue
            }

            // 自定义消息处理逻辑
            processMessage(msg)

            // 将消息 ID 存入本地状态
            saveMessageID(msg.Properties[primitive.PropertyUniqueClientMessageIdKey], msg.Topic, msg.Queue.ID)
        }

        return consumer.ConsumeSuccess, nil
    })
    if err != nil {
        fmt.Printf("subscribe topic error: %s\n", err)
        return
    }

    // 启动消费者实例
    err = c.Start()
    if err != nil {
        fmt.Printf("start consumer error: %s\n", err)
        return
    }
    defer c.Shutdown()
}

var (
    messageIDs = make(map[string]struct{})
    mutex      = sync.Mutex{}
)

// 检查消息是否重复处理
func checkDuplicate(msg *primitive.MessageExt) bool {
    key := fmt.Sprintf("%s:%s:%d", msg.Topic, msg.Queue.ID, msg.QueueOffset)
    mutex.Lock()
    defer mutex.Unlock()
    if _, ok := messageIDs[key]; ok {
        return true
    }
    return false
}

// 将消息 ID 存入本地状态
func saveMessageID(msgID, topic, queueID string) {
    key := fmt.Sprintf("%s:%s", topic, queueID)
    mutex.Lock()
    defer mutex.Unlock()
    messageIDs[key] = struct{}{}
}

// 处理消息
func processMessage(msg *primitive.MessageExt) {
    fmt.Printf("Received message: %s\n", string(msg.Body))
}

```

上述代码中，处理消息重复的关键在于 `checkDuplicate` 函数和 `saveMessageID` 函数。在消费者每次处理一条消息时，都会先调用 `checkDuplicate` 函数检查该消息是否为重复消息。如果返回 `true` 则说明已经处理过该消息，直接忽略不处理。接下来，如果是第一次处理该消息，则调用 `processMessage` 函数处理，并调用 `saveMessageID` 函数将该消息 ID 存入本地状态。

当下一次消费同样的消息时，`checkDuplicate` 函数就会返回 `true`，从而避免重复处理该消息。如此一来，即使 RocketMQ 的消费者接收到重复消息，也能保证只会处理一遍，从而避免重复操作和数据错误。
