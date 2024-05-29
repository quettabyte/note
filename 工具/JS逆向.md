# JS逆向

## 基本流程

1. 在视频播放页面，`F12`或`右上角...->更多工具->开发人员工具`或`Ctrl+Shift+I`。

2. 在网络中查找媒体相关的请求。

3. 找到m3u8文件。

4. 下载m3u8文件

>M3U8文件是指UTF-8编码格式的M3U文件。M3U文件是记录了一个索引纯文本文件，打开它时播放软件并不是播放它，而是根据它的索引找到对应的音视频文件的网络地址进行在线播放。视频网站采用的是流媒体传输协议，就是将一段视频切成无数个小段，这几个小段就是ts格式的视频文件，一段一段的网站上播放
>

5. 如果发现ts文件下载下来无法播放，那就是被加密了。
6. 例如文件中METHOD=`AES-128`就是加密方式。URI=`"xxxx"`就是加密的密钥（key），IV=`0x...`是偏移量。

```
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-KEY:METHOD=AES-128,URI="xxxxxxxx",IV=0x14425E3B0DC348EFA6F26DFCB1916E7A
```

7. ts文件被加密是无法正常播放的，是需要`key`、`IV`一块对ts文件进行解密后才可正常播放。

8. 一次加密的可以直接将URI=`"xxxx"`下载后，查看文件中的16字节的16进制例如`B2EEA94E334493B56DC62FCBC2E79E26`，这个就是key值。

9. 如果不是16字节或者使用该key值无法解密ts视频文件，则说明是二次加密。

10. 二次加密如果对JS不是很懂。则很难找到解密方式。

11. 可以在视频播放页面找到URI=`"xxxx"`的

    ![](https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-29-16_38_19.png)

12. 打开调用栈，找到send的位置，打上断点，然后点击单步执行，慢慢的找key

13. 网课，send打断点，点击播放，断点断住第一次，key位置为null，在点击执行，还会再该send位置断住，查看`e>frag>levelkey>key`就是解密后的key。

    <img src="https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-29-17_29_33.png" style="zoom: 80%;" />

14. 通过猫抓插件，下载ts并合并为mp4。

![](https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-29-17_32_28.png)
