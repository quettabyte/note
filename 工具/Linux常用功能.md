# Linux常用功能

## mount共享文件夹

windows共享文件夹mount到linux

1. windows将文件夹共享，网络路径为：`\\DESK-PC\Users\desk\Desktop\file-server`

2. Linux挂载该文件系统

   ```
   $ mkdir file-server
   $ sudo mount -t cifs //10.80.3.155/Users/desk/Desktop/file-server file-server -o username="test",password="123456",uid=1001,gid=1001
   ```

   uid、gid 是linux系统当前用户的uid、gid

   username、password 是windows系统的普通用户test

   文件共享时对test开启读写权限

3. 解除挂载

   ```
   $ sudo umount file-server
   ```

## 服务器网卡配置

在工作中使用服务器向设备回放报文时，遇到服务器网卡不定时会向设备发送一些组播、广播报文，经抓包查看，一部分为LLDP，还有一些ICMP报文。

LLDP可以通过以下命令关闭

```
$ ethtool --set-priv-flags enp216s0f3 disable-fw-lldp on
$ ethtool --show-priv-flags enp216s0f3
```

ICMP可能是因为NetworkManager服务自动发送的，可以通过一下命令关闭

* 查看NetworkManager状态:`systemctl status NetworkManager`
* 启动NetworkManager服务:`systemctl start NetworkManager`
* 重启NetworkManager服务（PID会变）:`systemctl restart NetworkManager`
* 重启NetworkManager服务（PID不会变）:`systemctl reload NetworkManager`
* 查看NetworkManager服务是否设置开机自启:`systemctl is-enabled NetworkManager`
* 设置NetworkManager服务开机自启:`systemctl enable NetworkManager`
* 取消NetworkManager服务开机自启:`systemctl disable NetworkManager`

## tcpreplay+tcpdump

```
sudo tcpreplay -l 1 -i enp216s0f3

sudo tcpdump -i enp216s0f3 -Q in -s 0 -w ./a.pcap
```

