# FishChatServer2


目录
=================
* [1说明](#1说明)
* [2特性](#2特性)
* [3架构](#3架构)
* [4协议](#4协议)
* [5服务说明](#5服务说明)
* [6依赖](#6依赖)
* [7部署](#7部署)
    * [7.1普通部署](#7.1普通部署)
    * [7.2容器部署](#7.2容器部署)
* [8测试](#8测试)
* [9监控](#9监控)


1说明
======
吸取了第一版的经验以及我们商业版的探索. 第二版我更多的思考在不要过多的实现轮子, 这个版本将很多业务无关的代码去掉, 并且尽量靠拢`微服务`.
部署方式可以支持：
> * 普通部署
> * 容器部署 (Kubernetes + Docker)


2特性
======


**[⬆ 回到顶部](#目录)**

3架构
======

![](./doc/architecture.png)

**[⬆ 回到顶部](#目录)**

4协议
======
在`protocol`目录下

* external 是对外的协议，采用`protobuf`实现
* rpc 是服务内部的调用，采用`grpc`

**[⬆ 回到顶部](#目录)**

5服务说明
======
进入server目录下

```shell
access
gateway
logic
manager
register
```

**[⬆ 回到顶部](#目录)**

6依赖
======

### 6.1系统环境
```shell
golang >= 1.4
jdk >= 1.8 (数据处理很多服务用java编写)
```


### 6.2第三方依赖

```shell
etcd
redis
mongodb
kafka
ElasticSearch(可选)
```

**[⬆ 回到顶部](#目录)**

7部署
======

### 7.1普通部署

为了方便, 我们在单机上进行部署 (实际部署的时候, 每个服务角色都可以自由水平扩展)

#### 7.1.1依赖安装

* kafka安装 : http://kafka.apache.org/quickstart (默认启动即可)

安装完成之后创建两个topic:

```shell
p2p topic:
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic logic_producer_p2p

group topic:
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic logic_producer_group

```

* redis安装 : 采用默认安装即可

* mongodb安装 : 采用默认安装即可

* etcd安装 : 需要3.0以上版本, 采用默认安装即可

#### 7.1.2server安装

进入server下面的各个目录 运行 `go build`, 然后启动服务即可(因为服务做了`服务发现`, 所以对启动顺序没有要求), 这里为了简单, 每个服务我们只启动一个, 当然启动任意个都是支持的.

* gateway安装

```shell
➜  FishChatServer2 git:(master) ✗ cd server/gateway 
➜  gateway git:(master) ✗ go build
➜  gateway git:(master) ✗ ./gateway 
```

* access安装

```shell
➜  FishChatServer2 git:(master) ✗ cd server/access 
➜  access git:(master) ✗ go build
➜  access git:(master) ✗ ./access 
```

* logic安装

```shell
➜  FishChatServer2 git:(master) ✗ cd server/logic 
➜  logic git:(master) ✗ go build
➜  logic git:(master) ✗ ./logic 
```


* register安装

```shell
➜  FishChatServer2 git:(master) ✗ cd server/register 
➜  register git:(master) ✗ go build
➜  register git:(master) ✗ ./register 
```

* manager安装

```shell
➜  FishChatServer2 git:(master) ✗ cd server/manager 
➜  manager git:(master) ✗ go build
➜  manager git:(master) ✗ ./manager 
```

#### 7.1.3job安装

* msg_job安装

```shell
➜  FishChatServer2 git:(master) ✗ cd jobs/msg_job 
➜  msg_job git:(master) ✗ go build
➜  msg_job git:(master) ✗ ./msg_job 
```

#### 7.1.4中间件服务安装

* idgen安装

```shell
➜  FishChatServer2 git:(master) ✗ cd service/idgen 
➜  idgen git:(master) ✗ go build
➜  idgen git:(master) ✗ ./idgen 
```


### 7.2容器部署

部署完全采用`Kubernetes + Docker`

所以第一步需要搭建`Kubernetes`和`Docker`, 幸运的是现在网络上已经有大量的资料了, 这块我就不多写了.

**[⬆ 回到顶部](#目录)**


8测试
======
### 8.1点对点聊天测试
进入client/p2p目录, 用户可以启动两个以上的进程, 两两之间互相聊天

```shell
➜  FishChatServer2 git:(master) ✗ cd client/p2p 
➜  p2p git:(master) ✗ go build
➜  p2p git:(master) ✗ ./p2p 
输入我的id :321
输入对方的id :收到点对点消息: 返回码[0], 对方ID[321], 消息内容[hello]
```

### 8.2群聊测试


**[⬆ 回到顶部](#目录)**

9监控
======

**[⬆ 回到顶部](#目录)**