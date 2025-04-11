---
title: '跨 VLAN 通信'
date: '2025-04-08T15:42:07+08:00'
weight: 2
draft: false
---

### 一、使用路由器的两个物理端口

#### Cisco PT 拓扑图

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.3nrsvsolvd.png)

#### 实验流程

##### 1、配置 PC 的 IP 地址

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/无标题.5fkrqog0xh.png)

##### 2、配置路由器端口的 IP 地址

```shell
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#int g 0/0/0
Router(config-if)#ip add 192.168.1.254 255.255.255.0
Router(config-if)#no sh

Router(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up

Router(config-if)#int g0/0/1
Router(config-if)#ip add 192.168.2.254 255.255.255.0
Router(config-if)#no sh

Router(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.83a811uj26.png)

##### 3、配置 VLAN

```shell
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#vlan 10
Switch(config-vlan)#vlan 20
Switch(config-vlan)#exit
Switch(config)#int f0/1
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 10
Switch(config-if)#int f0/2
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 20
Switch(config-if)#do sh vl b
Switch(config-if)#int g0/1
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 10
Switch(config-if)#int g0/2
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 20
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.esoz6ivcu.png)

##### 4、跨 VLAN 通信

```shell
ping 192.168.2.1
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.4ub44g6z4c.png)

##### 5、实验分析

1. 当主机 `ping 192.168.2.1` 时，会检查目标 IP 是否与自身 IP 处于同一网段
2. 目标 IP 与源 IP 不在同一网段时，主机会将数据包发送到路由器进行路由
3. 主机查找自身的 ARP 缓存表中路由器的 MAC 地址条目并将 ICMP 包发送到路由器
4. 若自身 ARP 缓存表中不存在路由器的 MAC 地址条目，会先发送 ARP 报文请求路由器的 MAC 地址，然后将 ICMP 包发送到路由器

   ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.83a8146w41.png)

5. ICMP 包从 F0/1 端口进入交换机后会在帧头封装一个 802.1Q VLAN 标签
   - TPID：一般为 `0x8100`，表示这是一个带 VLAN 标签的帧
   - TCI：包含了 VLAN ID 和优先级等信息
      - PRI：表示帧的优先级，当交换机阻塞时优先级高的帧会优先发送
      - CFI：表示 MAC 地址是否为经典格式，在以太网中的值通常为 0（经典格式）
      - VLAN ID：VLAN 编号

   ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.5mnzme2ogp.png)

6. ICMP 包从 G0/1 端口离开交换机前会剥掉帧头的 VLAN 标签
7. ICMP 包从 G0/2 端口进入交换机和从 F0/2 端口离开交换机以及目标主机回包时重复上述步骤，完成通信

> [!TIP]
> 通过 TCI 判断数据帧所属的 VLAN ：整个 TCI 字段的后 12 位就是 VLAN ID，也可以使用计算公式（VID = TCI & 0x0FFF）。 以 `0x000a` 为例，该 TCI 字段的后 12 位为 `0x00a`，即十进制的 10，因此该数据帧属于 VLAN 10。

##### 6、实验中遇到的问题

> [!IMPORTANT]
> 请先尝试自己思考，然后查看答案。

{{% details title="交换机的 G0/1 和 G0/2 端口设置为 trunk 模式无法进行正常通信" closed="true" %}}

- ARP 报文从 F0/1 端口进入交换机后会在帧头封装一个 VLAN 标签
- 若将 G0/1 和 G0/2 端口设置为 trunk 模式，交换机转发数据帧时不会剥掉 VLAN 标签
- 路由器的 G0/0/0 和 G0/0/1 是普通物理端口，无法识别携带 VLAN 标签的数据帧 
- ARP 报文被路由器直接丢弃，主机无法获取到网关的 IP 地址，也就无法发送 ICMP 包

{{% /details %}}

{{% details title="Simulation 看不到数据帧中封装的 VLAN 标签" closed="true" %}}

- F0/1 端口是 access 模式，数据帧进入交换机后会在帧头封装一个 VLAN 标签
- G0/1 端口是 access 模式，数据帧离开交换机前又会剥掉帧头中的 VLAN 标签
- F0/2 和 G0/2 原理同上，VLAN 标签仅在交换机内部进行处理
- 所以无法在 Simulation 看到数据帧中封装的 VLAN 标签

{{% /details %}}

### 二、单臂路由

#### Cisco PT 拓扑图

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.2a59teufpz.png)

#### 实验流程

##### 1、配置 PC 的 IP 地址

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/无标题.5fkrqog0xh.png)

##### 2、配置路由器端口的 IP 地址

```shell
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#int g0/0/0
Router(config-if)#no sh

Router(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up

Router(config-if)#int g0/0/0.10
Router(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0.10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0.10, changed state to up

Router(config-subif)#en dot1q 10
Router(config-subif)#ip add 192.168.1.254 255.255.255.0
Router(config-subif)#int g0/0/0.20
Router(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0.20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0.20, changed state to up

Router(config-subif)#en dot1q 20
Router(config-subif)#ip add 192.168.2.254 255.255.255.0
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.45v8hvnhj.png)

##### 3、配置 VLAN

```shell
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#int f0/1
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 10
% Access VLAN does not exist. Creating vlan 10
Switch(config-if)#int f0/2
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 20
% Access VLAN does not exist. Creating vlan 20
Switch(config-if)#int g0/1
Switch(config-if)#sw mo tr

Switch(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

Switch(config-if)#sw tr al vl al
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.1lc0a92wxu.png)

##### 4、跨 VLAN 通信

```shell
ping 192.168.2.1
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.4ub44g6z4c.png)

##### 5、实验分析

1. 使用路由器的单个物理端口创建多个虚拟子接口
2. 在子接口中使用命令 `encapsulation dot1Q vlan <VLAN_ID>` 启用 dot1Q 封装
3. 进行 dot1Q 封装后的虚拟子接口可以处理携带 VLAN 标签的数据帧
4. 将交换机的 G0/1 端口设置为 trunk 模式，使所有 VLAN 流量都可以到达路由器
5. ICMP 包从 F0/1 端口进入交换机后会在帧头封装一个 VLAN 10 标签
6. 由于 G0/1 端口是 trunk 模式，转发数据帧时不会剥掉帧头的 VLAN 10 标签
7. 路由器的 G0/0/0.10 子接口收到携带 VLAN 10 标签的数据帧后会进行路由和转发
8. 路由器的 G0/0/0.20 子接口转发数据前会替换 VLAN 标签为 VLAN 20
9. 由于 F0/2 端口是 access 模式，交换机剥掉数据帧中的 VLAN 20 标签后转发到目标主机

### 三、三层交换技术

#### Cisco PT 拓扑图

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.8z6pjoq3wj.png)

#### 实验流程

##### 1、配置 PC 的 IP 地址

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/无标题.5fkrqog0xh.png)

##### 2、配置三层交换机的路由功能

```shell
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#vlan 10
Switch(config-vlan)#vlan 20
Switch(config-vlan)#exit
Switch(config)#int vlan 10
Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

Switch(config-if)#ip add 192.168.1.254 255.255.255.0
Switch(config-if)#int vlan 20
Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

Switch(config-if)#ip add 192.168.2.254 255.255.255.0
Switch(config-if)#exit
Switch(config)#ip routing
Switch(config)#int g1/0/1
Switch(config-if)#sw mo tr

Switch(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan20, changed state to up

Switch(config-if)#sw tr al vl al
Switch(config-if)#int g1/0/2
Switch(config-if)#sw mo tr

Switch(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/2, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/2, changed state to up

Switch(config-if)#sw tr al vl al
Switch(config-if)#exit
Switch(config)#do sh ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    192.168.1.0/24 is directly connected, Vlan10
C    192.168.2.0/24 is directly connected, Vlan20

Switch(config)#
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.b934l3rs3.png)

##### 3、配置二层交换机 VLAN

**交换机 S1：**

```shell
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#int g0/1
Switch(config-if)#sw mo tr
Switch(config-if)#sw tr al vl al
Switch(config-if)#int f0/1
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 10
% Access VLAN does not exist. Creating vlan 10
Switch(config-if)#
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.5tr7kqd6jn.png)

**交换机 S2：**

```shell
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#int f0/1
Switch(config-if)#sw mo ac
Switch(config-if)#sw ac vl 20
% Access VLAN does not exist. Creating vlan 20
Switch(config-if)#int g0/1
Switch(config-if)#sw mo tr
Switch(config-if)#sw tr al vl al
Switch(config-if)#
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.9rjl1ensb6.png)

##### 4、跨 VLAN 通信

```shell
ping 192.168.2.1
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/图片.4ub44g6z4c.png)

##### 5、实验分析


### 四、单臂路由访问互联网

### 五、三层交换技术访问互联网

