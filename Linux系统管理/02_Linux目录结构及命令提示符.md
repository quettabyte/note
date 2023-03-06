## 一、Linux目录结构

> 设计哲学：
>
> ​		一切皆文件！！！！！

`/`	根目录

|——`/root`：root用户的家目录/宿主目录

|——`/home`：普通用户的家目录/宿主目录

|——`/etc`：应用程序的配置文件

|——`/dev`：存储设备文件（磁盘、分区、设备）

|——`/boot`：内核、启动配置文件

|——`/proc`：动态文件，包括记录CPU、内存、网卡、进程

|——`/lib`：库文件

|——`/opt`：用户的工作目录，保存下载工具、软件、文档

|——`/tmp`：临时目录

## 二、命令提示符

调大字体		`ctrl + shift + '+'`

调小字体		`ctrl + '-'`

```
[root@localhost ~]#

		root:当前登录系统的用户名

		localhost：默认的主机名

		~：当前路径，~是当前用户的家目录

		#：管理员 or $：普通用户
```

## 三、文件目录基本管理指令

### 1. 切换、查看目录

格式：`cd [目录名]`

```sh
[root@localhost ~]# pwd
/root
[root@localhost ~]# cd /etc
[root@localhost etc]# pwd
/etc
[root@localhost etc]# cd
[root@localhost ~]#
```

### 2. 查看目录下的文件

格式：`ls [选项] [目录名称]`

```sh
[root@localhost ~]# cd /
[root@localhost /]# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  us
```

常用选项：

1. `-a`：查看目录下所有文件（隐藏文件——`.`开头的文件）

   ```sh
   [root@localhost ~]# ls -a
   ```

2. `-lh`：显示文件的详细信息

   ```sh
   [root@localhost ~]# ls -l
   total 8
   -rw-------. 1 root root 1.9K Mar  6 18:38 anaconda-ks.cfg
   第一个字符：文件类型
   		普通文件	-
   		目录	d
   		软链接文件	l
   		块设备文件	b	磁盘、分区
   		字符设备文件	c	键盘、显示器、打印机
   1894：文件大小，单位Bytes字节
   Mar 6 18:38		最后一次修改时间
   ```

3. `-ldh`：查看目录的详细信息

   ```sh
   [root@localhost ~]# ls -ldh /etc
   ```

4. `-S`：以文件大小排序显示

   ```sh
   [root@localhost ~]# ls -lhS /tmp/
   ```

5. `-t`：以文件的修改时间排序

   ```sh
   [root@localhost ~]# ls -lht /tmp/
   ```

