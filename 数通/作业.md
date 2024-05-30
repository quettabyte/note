# DatacomIA 作业

## 地址冲突检测原理

论述

## Ping通原理（数据转发原理）

论述

## 设备远程管理、配置保存、配置恢复

实验

## DHCP原理

论述+实验

一、R1充当dhcp client, R2配置dhcp server,分配 192.168.120/24 gw：192.168122 dns：8.8.8.8， 租期 3 天

![](https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/datacom-note/tmp/2024-5-21-18_35_14.png)

二、通过抓包和图示总结dhcp正常获取到ip地址的过程以及报文

三、如果要求R1只能获取192.168.12.100.这个地址不能分配给其他的主机，那该如何配置，在R1上`dis ip interface bref`来验证

> undo ip address dhcp-alloc ：抓包会发现 `release`报文

四、DHCP中继配置

![](https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/datacom-note/tmp/2024-5-21-18_35_33.png)

测试R1能否获取IP地址，通过抓取R2到R4的报文，来总结relay和server之间是如何通讯的，跟client和server之间通讯有什么区别。

五、总结排错流程

## PPP协议

一、LCP（链路控制协议）原理阐述

二、MRU如何协商，如果两边MRU不同是如何协商的，请抓包截图

三、Magic number如何进行防环	

> MRU: Maximum Recive Unit最大接受单元=MTUT两边互相发送MTU，进行协商，选择最小的MRU作为接口MTU
>
> Magic number：魔术字；功能：用于防环

四、通过抓包截图来展示PPP LCP + PPP PAP 认证的完整过程并阐述

> PAP认证：password authentication protocol 密码认证协议

五、通过抓包截图展示PPP LCP + PPP CHAP认证完整过程并阐述

> CHAP认证：Challenge Handshake Authentication Protocol 挑战握手认证协议 

六、举例ACK、NAK、Reject三种报文的场景。

> ACK：Option匹配，Value合法（正确场景）
>
> NAK：Option匹配，Value不合法（Magic Number不同，认证方式不同）
>
> Reject：Option不匹配（两端一端开启认证，另一端不开启认证）
