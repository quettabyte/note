# tcpreplay工具

tcpreplay是一系列工具的集合。包括（tcpprep、tcprewrite、tcpreplay和tcpbridge）。

*   其中tcpreplay是真正实现流量回放功能的工具。

*   tcpprep可以划分哪些包是client的,哪些是server的,一会发包的时候client的包从一个网卡发,server的包可能从另一个网卡发。

*   tcprewrite可以就是修改2层, 3层, 4层报文头部。

官网：<http://tcpreplay.appneta.com/>

## 安装

CentOS环境：

```shell
$ yum -y install tcpreplay

```

## 查看版本

```shell
[root@iZ0jl780lb0oipdcqpac1tZ ~]# tcpreplay -V
tcpreplay version: 4.4.3 (build git:v4.4.3)
Copyright 2013-2022 by Fred Klassen <tcpreplay at appneta dot com> - AppNeta
Copyright 2000-2012 by Aaron Turner <aturner at synfin dot net>
The entire Tcpreplay Suite is licensed under the GPLv3
Cache file supported: 04
Not compiled with libdnet.
Compiled against libpcap: 1.5.3
64 bit packet counters: enabled
Verbose printing via tcpdump: enabled
Packet editing: disabled
Fragroute engine: disabled
Injection method: PF_PACKET send()
Not compiled with netmap
```

## 查看帮助信息

```shell
[root@iZ0jl780lb0oipdcqpac1tZ ~]# tcpreplay -h
tcpreplay (tcpreplay) - Replay network traffic stored in pcap files
Usage:  tcpreplay [ -<flag> [<val>] | --<name>[{=| }<val>] ]... \
                <pcap_file(s)> | <pcap_dir(s)>

   -q, --quiet                Quiet mode
   -T, --timer=str            Select packet timing mode: select, ioport, gtod, nano
       --maxsleep=num         Sleep for no more then X milliseconds between packets
   -v, --verbose              Print decoded packets via tcpdump to STDOUT
   -A, --decode=str           Arguments passed to tcpdump decoder
   -K, --preload-pcap         Preloads packets into RAM before sending
   -c, --cachefile=str        Split traffic via a tcpprep cache file
   -2, --dualfile             Replay two files at a time from a network tap
   -i, --intf1=str            Client to server/RX/primary traffic output interface
   -I, --intf2=str            Server to client/TX/secondary traffic output interface
       --listnics             List available network interfaces and exit
   -l, --loop=num             Loop through the capture file X times
       --loopdelay-ms=num     Delay between loops in milliseconds
       --pktlen               Override the snaplen and use the actual packet len
   -L, --limit=num            Limit the number of packets to send
       --duration=num         Limit the number of seconds to send
   -x, --multiplier=str       Modify replay speed to a given multiple
   -p, --pps=str              Replay packets at a given packets/sec
   -M, --mbps=str             Replay packets at a given Mbps
   -t, --topspeed             Replay packets as fast as possible
   -o, --oneatatime           Replay one packet at a time for each user input
       --pps-multi=num        Number of packets to send for each time interval
       --unique-ip            Modify IP addresses each loop iteration to generate unique flows
       --unique-ip-loops=str  Number of times to loop before assigning new unique ip
       --no-flow-stats        Suppress printing and tracking flow count, rates and expirations
       --flow-expiry=num      Number of inactive seconds before a flow is considered expired
   -P, --pid                  Print the PID of tcpreplay at startup
       --stats=num            Print statistics every X seconds, or every loop if '0'
   -V, --version              Print version information
   -h, --less-help            Display less usage information and exit
   -H, --help                 display extended usage information and exit
   -!, --more-help            extended usage information passed thru pager
       --save-opts[=arg]      save the option state to a config file
       --load-opts=str        load options from a config file

Options are specified by doubled hyphens and their name or by a single
hyphen and the flag character.
tcpreplay is a tool for replaying network traffic from files saved with
tcpdump or other tools which write pcap(3) files.

Please send bug reports to:  <tcpreplay-users@lists.sourceforge.net>
```

## 常用选项

```shell
-p, --pps=str              Replay packets at a given packets/sec

指定发包速率，每秒发送的packet。也有人叫叫EPS速率（不知道是否标准）
```

```shell
-M, --mbps=str             Replay packets at a given Mbps

指定带宽大小，单位Mbps
```

```shell
-i, --intf1=str            Client to server/RX/primary traffic output interface

指定发包网卡

如何确定网卡

[root@iZ0jl780lb0oipdcqpac1tZ ~]# ip addr | grep up
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000

找一个DOWN的网卡，然后用命令使该网卡的网口灯闪烁30秒
ethtool -p eth0 30  
在服务器上找到没有网线，但灯在闪烁的网口，就可以确定网卡对应哪个网口了
```

```shell
-t, --topspeed             Replay packets as fast as possible

以最快速度回放包
```

```shell
-l, --loop=num             Loop through the capture file X times

对pcap文件回放X次 为0一直发
```

## 使用

```shell
$ sudo tcpreplay -t -l 1 -i enp216s0f3 ./test.pcap

以最高速度从enp216s0f3网卡回放1次test.pcap

$ nohup tcpreplay -i eth0 -p 5000 -l 100000000  test.pcap &

后台运行
```