

## OSI七层协议模型

#### 应用层

#####         FTP、HTTP、SMTP、Telnet、DNS

#### 表示层

#### 会话层

#### 传输层

#####         TCP、UDP等

#### 网络层

#####         IP、ICMP、ARP等

#### 数据链路层

#####         PPP协议

#### 物理层

​                                                                                                                                    ==TCP VS UDP 对比==     

都是传输层协议，TCP:传输控制协议    UDP:用户数据包协议

|                           TCP                           |                      UDP                       |
| :-----------------------------------------------------: | :--------------------------------------------: |
|                        面向连接                         |                   非面向连接                   |
|                      可靠但性能差                       |                 不可靠但性能好                 |
|                      带数据包序列                       |                 不带数据包序列                 |
|                       全双工直连                        |         一对一，一对多，多对一，多对多         |
|            慢开始、拥塞避免，快重传、快恢复             |                   无拥塞控制                   |
| 对效率要求低，对准确性要求高的场景适用，如下载、Email等 | 对效率要求高，准确性要求低的场景适用，如音视频 |

<!--TCP建立连接需要tcp三次握手，通过三次确认-->

## 交换基础

#### 交换机接口模式

交换机有三种接口模式

Access、Trunk和Hybrid（混合的）

##### Trunk的作用

- 将交换机与交换机之间连接的端口设置为trunk,实现交换机之间的不同Vlan的通信
- 使用一条链路，使用标识来区分不同Vlan数据

##### Trunk封装类型

IEEE(华为)    dot1q

##### Trunk配置

```shell
[SwitchA] interface ethernet 0/0/24            //进入端口0/0/24

[SwitchA-Ethernet0/0/24] port link-type trunk //设置端口模式为access


[SwitchA-Ethernet0/0/24] port trunk allow-pass vlan 10 20  //设置vlan 10 20可以通过trunk

[SwitchA-Ethernet0/0/24] quit //退出
```



#### STP原理

*STP*（Spanning Tree Protocol）是生成树协议的英文缩写，通过在交换网络中部署生成树（Spanning-tree）技术，能够防止网络中出现二层环路。

STP运行后，如果网络中存在环路，那么STP通过阻塞（Block）特定的接口从而打破环路，并且在网络出现拓扑变更时及时收敛，以保证网络的冗余性。

##### STP的操作

<img src="F:\自理网络知识\STP操作.png" style="zoom:80%;" />

##### MSTP

•MSTP兼容STP和RSTP，通过多实例能实现对业务流量和用户流量的隔离，同时还提供了数据转发的多个冗余路径，在数据转发过程中实现VLAN数据的负载均衡。

•在MSTP中，你可以将若干个VLAN映射到一个实例（instance），MSTP将为每个instance运行一棵生成树，可以基于instance设置优先级、端口路径开销等参数。

<img src="F:\自理网络知识\MSTP配置示例.png" style="zoom:80%;" />



#### VLAN基础

##### Vlan的概念

vlan（Virtual Local Area Network）的中文名为"虚拟局域网",作用是分割广播域，控制广播风暴、增强网络的安全性、简化网络管理。

##### Vlan的划分

- 交换机单接口加入vlan

```shell
[Quidway] system-view //进入配置视图

[Quidway] sysname SwitchA //给交换机命名

[SwitchA] vlan batch 2 3 //同时创建vlan2与vlan3

[SwitchA] interface ethernet 0/0/1 //进入端口0/0/1

[SwitchA-Ethernet0/0/1] port link-type access //设置端口模式为access

[SwitchA-Ethernet0/0/1] port default vlan 2 //将端口加入vlan2中

[SwitchA-Ethernet0/0/1] quit //退出

[SwitchA] interface ethernet 0/0/2 //进入端口0/0/2

[SwitchA-Ethernet0/0/2] port link-type access //端口模式为access

[SwitchA-Ethernet0/0/2] port default vlan 3 //将端口加入vlan3中

[SwitchA-Ethernet0/0/2] quit //退出
```

- 交换机接口批量加入vlan

```shell
[HW2700-2] vlan batch 2 to 5

[HW2700-2]port-group g2

[HW2700-2-port-group-g2]group-member Ethernet 0/0/1 to Ethernet 0/0/6       //选择1~6 端口加入group

[HW2700-2-port-group-g2]port link-type access      //接口为access口，显示1~6口设置为access类型

[HW2700-2-port-group-g2]port default vlan 2        //端口组所有端口加入vlan2

[HW2700-2]dis vlan   //显示vlan信息

[HW2700-2]port-group g3

[HW2700-2-port-group-g3]group-member Ethernet 0/0/6 to Ethernet 0/0/12

[HW2700-2-port-group-g3]port link-type access     //所有接口配置为access类型
[HW2700-2-port-group-g3]port default vlan 3      //端口组所有端口加入vlan3
```

##### VLAN间路由互通

###### 通过子接口实现vlan间路由互通

在路由器以太网接口上进行“子接口”的划分，与交换机的Trunk链路对接

###### 子接口也叫单臂路由（sub-interface）特点

1. 软件的，逻辑接口
2. 可以配置IP,指定vlan-id
3. GE 0/0/0.10   GE 0/0/0.20  ... 接口名称可自定义

```shell
GE o/o/o.10 配置地址192.169.10.254/24     //为vlan10的网关地址，相呼应
GE o/o/o.20 配置地址192.169.20.254/24     //为vlan20的网关地址，相呼应
```

<img src="F:\自理网络知识\通过子接口实现vlan间路由.png" alt="通过子接口实现vlan间路由" style="zoom:80%;" />

###### Vlanif

Vlanif（Vlan interface)工作原理（一般在三层交换机上配置）

三层交换机进行三层转发的关键就是在每个VLAN基础上虚拟出一个vlanif接口，并配上IP，从而实现三层转发

<img src="F:\自理网络知识\vlanif 三层示意图.png" style="zoom:80%;" />

Vlan interface基础配置

```shell
[SwitchA] vlan batch 2 3 //同时创建vlan2与vlan3

[SwitchA] interface ethernet 0/0/1 //进入端口0/0/1

[SwitchA-Ethernet0/0/1] port link-type access //设置端口模式为access

[SwitchA-Ethernet0/0/1] port default vlan 2 //将端口加入vlan2中

[SwitchA-Ethernet0/0/1] quit //退出

[SwitchA] interface ethernet 0/0/2 //进入端口0/0/2

[SwitchA-Ethernet0/0/2] port link-type access //端口模式为access

[SwitchA-Ethernet0/0/2] port default vlan 3 //将端口加入vlan3中

[SwitchA-Ethernet0/0/2] quit //退出
#为vlanif10及vlanif20配置IP地址，作为vlan10和vlan20用户的网关
[SwitchA] interface vlanif 10
[SwitchA-vlanif10] ip addr 192.168.10.254 24
[SwitchA-vlanif10] quit      //退出
[SwitchA] interface vlanif 20
[SwitchA-vlanif20] ip addr 192.168.20.254 24
```

二、三层交换机、路由器简单组网

<img src="F:\自理网络知识\二、三层交换机、路由器简单组网.png" style="zoom: 80%;" />



#### 交换机端口镜像与链路聚合

##### 端口镜像

在某些场景中，我们需要监控交换机特定端口的入站或出站报文。

##### 链路聚合

•链路聚合（Link Aggregation）是将—组物理接口捆绑在一起作为一个逻辑接口来增加带宽的一种方法，又称为多接口负载均衡组（Load Sharing Group）或链路聚合组（Link Aggregation Group），相关的协议标准请参考IEEE802.3ad。

•通过在两台设备之间建立链路聚合组，可以提供更高的通讯带宽和更高的可靠性。链路聚合不仅为设备间通信提供了冗余保护，而且不需要对硬件进行升级。

<img src="F:\自理网络知识\链路聚合概述.png" style="zoom:80%;" />

##### Eth-trunk的工作模式

华为的网络设备支持两种Eth-trunk工作模式：

- 手工负载分担模式 
- LACP模式 



## 路由基础

#### IP路由选择原理

##### 路由器的工作内容

- 发现到达网络中各个网段的路径；
- 选择最佳路径；
- 维护路由表及路由信息；
- 转发数据报文

##### IP路由表

查看路由表：display ip routing-table

路由条目的来源

- 直连路由 – 直连接口所在网段的路由。
- 静态路由 – 由网络管理员手工配置的路由条目。
- 动态路由 – 路由器通过动态路由协议学习到的路由。

##### 静态路由配置

<img src="F:\自理网络知识\静态路由示意.png" style="zoom:80%;" />

###### 静态路由的配置

- 静态路由的配置（关联下一跳IP的方式）：

 [Router**] ip route-static** 网络号 掩码 下一跳IP地址

- 静态路由的配置（关联出接口的方式）：

 [Router] **ip route-static** 网络号 掩码 出接口

- 静态路由的配置（关联出接口和下一跳IP的方式）：

 [Router**] ip route-static** 网络号 掩码 出接口 下一跳IP地址

即                ==目标网段+对应的掩码+下一跳地址==

示例：**[R1] ip route-static** *192.168.100.0 24 192.168.12.2*

<img src="F:\自理网络知识\静态路由配置示例.png" style="zoom:80%;" />

**注意：**

- 通信是双向的，因此要留意往返流量（的路由）。
- 路由的行为是逐跳的，因此需保证沿途的每一台路由器都有路由。

##### 默认路由

默认路由也被称为缺省路由，一般配置在末梢路由及出口路由上

<img src="F:\自理网络知识\默认路由.png" style="zoom:80%;" />

##### 路由汇总

<img src="F:\自理网络知识\路由汇总的概念.png" style="zoom:80%;" />

##### IP路由查找的最长匹配原则

当路由器在将目的IP地址在路由表中执行查找时，采用的原则是“最长匹配原则”，也就是查找目的IP地址与路由前缀匹配度最长的表项，使用该表项作为最终数据转发的依据。

##### 路由优先级

<img src="F:\自理网络知识\浮动静态路由.png" style="zoom:80%;" />

在配置静态路由时，可以手工指定该静态路由的优先级（在华为网络产品中，静态路由优先级缺省为60）

```shell
ip route-static 10.9.9.0 24 10.1.13.1
ip route-static 10.9.9.0 24 10.1.23.2 preference 80
```



当某一方链路出现故障另外的路由就出现，以起到冗余的功能

##### 静态路由与BFD



#### 动态路由协议

##### 动态路由协议的分类

<img src="F:\自理网络知识\动态路由协议分类.png" style="zoom:80%;" />

##### 距离矢量路由协议（RIP)

基于UDP520端口，优先级为100，低于静态路由（60）

##### 链路状态路由协议

###### 工作原理

1. LSA的泛洪

   运行链路状态路由协议的路由器，彼此之间交互的就不是路由信息了，而是LSA（链路状态通告）

2. LSDB的维护

   每台路由器将搜集到的LSAs放入自己的LSDB（链路状态数据库）存储起来。有了LSDB，路由器相当于掌握了全网的拓扑。

3. SPF计算

   每台路由器基于LSDB，使用SPF（最短路径算法）进行计算，得到一个以自己为根、覆盖全网的一棵无环的树。

4. 维护路由表

   每台路由器根据SPF的计算结果，将路由加载进路由表。

###### OSPF

​       OSPF(Open Shortest Path First,开放的最短路径优先协议),封装在Ip层，协议号89作为标识。

RouterID

- OSPF Router-ID用于在OSPF domain中唯一地表示一台OSPF路由器。
- OSPF Router-ID的设定可以通过手工配置的方式，或者通过协议自动选取的方式。当然，在实际网络部署中，强烈建议手工配置OSPF的Router-ID，因为这关系到协议的稳定。

OSPF Cost

- OSPF使用cost“开销”作为路由度量值。
- OSPF接口cost=100M /接口带宽，其中100M为OSPF的参考带宽（reference-bandwidth），可以修改。
- 每一个激活OSPF的接口都有一个cost值。
- 一条OSPF路由的cost由该路由从起源一路到达本地的所有入接口cost值的总和。

<img src="F:\自理网络知识\OSPF Cost计算.png" style="zoom:80%;" />

​                                                         **实际组网中经常通过修改cost以达到路由优选以及链路冗余**

OSPF的三张表

- 邻居表（Peer table）：

  OSPF是一种可靠的路由协议，要求在路由器之间传递链路状态通告之前，需先建立OSPF邻居关系，hello报文用于发现直连链路上的其他OSPF路由器，再经过一系列的OSPF消息交互最终建立起全毗邻的邻居关系，OSPF路由器的邻居信息显示在邻居表中。

- 链路状态数据库（Link-state database，简称LSDB）：

  OSPF用LSA（link state Advertisement，链路状态通告）来描述网络拓扑信息，然后OSPF路由器用LSDB来存储网络的这些LSA。OSPF将自己产生的以及邻居通告的LSA搜集并存储在LSDB中。掌握LSDB的查看以及对LSA的深入分析才能够深入理解OSPF。

- OSPF路由表（Routing  table）：

  基于LSDB进行SPF（Dijkstra算法）计算，而得出的OSPF路由表。

OSPF五种报文

1. Hello报文    建立和维护OSPF邻居关系
2. DBD报文    链路状态数据库描述信息（描述LSDB中LSA头部信息），用于两台设备进行数据库同步
3. LSR报文    链路状态请求，用于向对方请求所需的LSA
4. LSU报文    链路状态更新，用于向对方发送其所需的LSA(一条或者多条LSA)
5. LSAck报文    用来对收到的LSU中的LSA进行确认

DR、BDR

-  为减小多路访问网络中的 OSPF 流量，OSPF 会在每一个MA网络（多路访问网络）选举一个指定路由器 (DR) 和一个备用指定路由器 (BDR)

OSPF area

- 减少了LSA洪泛的范围，有效地把拓扑变化控制在区域内，达到网络优化的目的。
- 在区域边界可以做路由汇总，减小了路由表。
- 充分利用OSPF特殊区域的特性，进一步减少LSA泛洪，从而优化路由。
- 多区域提高了网络的扩展性，有利于组建大规模的网络

<img src="F:\自理网络知识\OSPF多区域.png" style="zoom:80%;" />

###### 单区域OSPF的基础配置

<img src="F:\自理网络知识\OSPF配置示例.png" style="zoom:80%;" />

```shell
R1的配置如下：
    [R1] interface GE0/0/0
    [R1-GigabitEthernet0/0/0] ip add 192.168.12.1 24
    [R1-GigabitEthernet0/0/0] int g 0/0/1
    [R1-GigabitEthernet0/0/1] ip add 192.168.1.254 24

	[R1] ospf 1 router-id 1.1.1.1
	[R1-ospf-1] area 0
	[R1-ospf-1-area-0.0.0.0] network 192.168.12.0 0.0.0.255
	[R1-ospf-1-area-0.0.0.0] network 192.168.1.0 0.0.0.255
```

```shell
R2的配置如下：
	[R2] ospf 1 router-id 2.2.2.2
	[R2-ospf-1] area 0
	[R2-ospf-1-area-0.0.0.0] network 192.168.12.0 0.0.0.255
	[R2-ospf-1-area-0.0.0.0] network 192.168.23.0 0.0.0.255
```

```shell
R3的配置如下：
	[R3] ospf 1 router-id 3.3.3.3
	[R3-ospf-1] area 0
	[R3-ospf-1-area-0.0.0.0] network 192.168.2.0 0.0.0.255
	[R3-ospf-1-area-0.0.0.0] network 192.168.23.0 0.0.0.255
```





## 交换进阶

#### Smart Link 

