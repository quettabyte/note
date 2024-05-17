# SUPI



## 5G终端标识SUPI,SUCI及IMSI解析

> 前言：IMSI，SUPI，SUCI均为UE终端标识，区别在于IMSI为LTE终端标识，SUPI为5G非加密>终端标识，一般等同于IMSI，SUCI为5G加密终端标识，需要解密后才能得到SUPI
>
> UE: 是移动通讯中一个重要概念，3G和4G网络中，用户终端就叫做UE

***

## IMSI

<img src="https://cokeice-pic.oss-cn-wulanchabu.aliyuncs.com/IMSI-struct.png"  />

## SUPI

<img src="https://cokeice-pic.oss-cn-wulanchabu.aliyuncs.com/SUPI-struct.png"  />

SUCI 由以下部分组成：

```shell
1. SUPI类型（SUPI Type）：范围0到7。它标识了SUCI中隐藏的SUPI的类型。定义了以下值：

    -0：IMSI

    -1：网络特定标识符（NSI）

    -2：全局线路标识符（GLI）

    -3：全球电缆标识符（GCI）

    -4至7：备用值，以备将来使用
    
2. 归属网络标识符（Home Network Identifier）：用来标识用户的归属网络。

    当SUPI类型为IMSI的时候，归属网络标识符由两部分组成：

    移动国家码（MCC）：由三位十进制数字组成，MCC唯一标识了移动用户的注册国家；

    移动网络码（MNC）：由两个或三个十进制数字组成，MNC标识了移动用户的归属PLMN或SNPN。

    当SUPI类型是NSI、GLI或GCI时，归属网络标识符由一个长度可变的字符串组成，表示了IETF RFC 7542[126]第2.2条中规定的域名。

3. 路由标识符（Routing Indicator）：
    
    由归属网络运营商分配的1到4位十进制数字组成，并在USIM中提供，允许与归属网络标识符一起将SUCI网络信令路由到能够服务于用户的AUSF和UDM实体。
    
    路由标识符中的每个十进制数字都是有意义的（例如，“012”与“12”意义不同）。
    如果USIM或ME上未配置路由标识符，则该数据字段应设置为值0（即仅由一个十进制数字“0”）。
    
4. 保护方案标识符（Protection Scheme Identifier）：
    
    范围0~15，它代表了空方案、非空方案或者 HPLMN定义的保护方案。
    如果SUPI类型是GLI、GCI时，则应使用空方案。
    
5. 归属网络公钥标识符（Home Network Public Key Identifier）：

    由0到255之间的值组成。
    它表示由HPLMN或SNPN提供的公钥，用于标识用于SUPI保护的密钥。
    核心网需要找到对应的私钥来解密获取SUPI。当且仅当使用空保护方案时，此数据字段应设置为值0。
    
6. 保护方案输出（Scheme Output）：

    由长度可变的字符串或十六进制数字组成，取决于所使用的保护方案。
    在空方案和SUPI类型为IMSI的情况下，Scheme Output就是IMSI中的MSIN部分。
```

eg:
```c
以十进制数字为例，假设
    IMSI是2341509999999，
    其中MCC=234，MNC=15，MSISN=09999999，路由标识符为678，归属网络公钥标识符为27：

-空保护方案的SUCI包括：0，234，15，678，0，0和09999999

-ProfileA方案的SUCI包括：0，234，15，678，1，27，<EEC临时公钥值>，<加密09999999>和<MAC标记值>。
```