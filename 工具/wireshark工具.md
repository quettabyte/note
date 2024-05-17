# wireshark抓工具

## 裁剪pcap文件

```shell
在wireshark安装目录下执行

editcap.exe -c num sourcefile destfile

-c 指定分割后的pcap文件中报文个数，60w条约等于500M

例：

editcap.exe -c 600000 D:\apn\1.pcap D:\apn\1\iot.pcap

生成：

iot_00000_20230705110800.pcap
iot_00001_20230705110804.pcap
...
```

