# GDB调试工具

GDB是一个由GNU开源组织发布的、UNIX/LINUX操作系统下的、基于命令行的、功能强大的程序调试工具。

## 参考文章

[GDB调试-从入门实践到原理\_高性能架构探索的博客-CSDN博客](https://blog.csdn.net/namelij/article/details/122322390)

[使用 GDB 进行调试：入门 (howtogeek.com)](https://www.howtogeek.com/devops/debugging-with-gdb-getting-started/)

[GDB调试原理——ptrace系统调用 - 爱折腾的西山居士 - 博客园 (cnblogs.com)](https://www.cnblogs.com/xsln/p/ptrace.html)

## 启动方式

使用gdb调试，一般有以下几种启动方式：

```shell
$ gdb filename: 调试可执行程序

$ gdb attach pid: 通过”绑定“进程ID来调试正在运行的进程

$ gdb filename -c coredump_file: 调试可执行文件
```

常用选项：

`-q`：去掉其他不必要的输出，如GDB版本、官网等。

### 调试可执行程序

使用`gcc -g`选项，生成带有调试信息的可执行文件

调试信息包括程序中各个变量或函数的数据类型以及可执行代码中的源代码的行号和地址间的关系等等。

```shell
$ gcc -g main.c -o main 
$ gdb main

GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-114.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /root/test_main...done.
(gdb) break xxx:xxx  设置好断点
(gdb) run
```

### 绑定进程ID

在实际开发环境中，需要调试的程序很有可能是随着操作系统一同启动的。这时就没办法使用上一个GDB调试方法了。

众所周知，操作系统会为我们的每一个进程都分配一个进程ID，知道进程ID，就可以使用`attach`命令进行调试

```shell
$ ps -aux | grep main  #查看main进程的PID
$ gdb -q attach PID

xxxxxx...done.
xxxxxxxxxx...done.
(gdb) break xxx:xxx  设置好断点
(gdb) continue
```

### coredump

在发生段错误的时候，程序崩溃时的快照会被dump，这个操作叫做coredump(核心转储)，可以使用GDB去调试此文件，可以还原程序崩溃时的场景。（默认会生成core.pid的文件）

需要注意：系统默认是关闭此功能的，需要手动打开

```shell
# 临时配置 size为允许生成的coredump大小，unlimited无限
$ ulimit -c size
$ ulimit -c unlimited

# 永久配置(root)
$ mkdir -p /www/coredump/
$ chmod 777 /www/coredump/
$ vim /etc/profile
    ulimit -c unlimited
$ vim /etc/security/limits.conf
    *       soft        core    unlimited
$ echo "/www/coredump/core-%e-%p-%h-%t" > /proc/sys/kernel/core_pattern
```

命令

```shell
$ gdb ./main -c /www/coredump/core_xxx
```

## 常用命令

### 执行

| 命令                 | 描述                          |
| ------------------ | --------------------------- |
| `run(r)`           | 开始运行程序（调试可执行程序执行该命令）        |
| `continue(c)`      | 继续运行程序（绑定PID进程后执行该命令）       |
| `next(n)`          | 单步执行程序（遇到函数调用，会直接执行完这个函数）   |
| `step(s)`          | 遇到函数时，可以进入函数内部              |
| `set step-mode on` | 不跳过不含调试信息的函数，可以显示和调试汇编代码    |
| `finish`           | 执行完当前函数并打印返回值，然后返回函数调用处     |
| `return 0`         | 不执行完这个函数，直接返回到函数调用处，可以指定返回值 |

### 断点

| 命令                 | 描述                   |
| ------------------ | -------------------- |
| `break(b)`         | 设置断点                 |
| `info breakpoints` | 打印所有断点信息             |
| `b main.c:12`      | 在文件main.c的12行处设置中断   |
| `b main.c:max`     | 在main.c中的max函数入口设置断点 |
| `b func`           | 直接在func函数入口设置断点      |
| `ignore n count`   | 接下来对于编号为n的断点忽略count次 |
| `clear`            | 删除当前所在函数的所有断点        |
| `clear function`   | 删除指定function函数内的所有断点 |
| `delete`           | 删除所有断点               |
| `delete n`         | 删除指定编号的断点            |
| `enable n`         | 启用指定编号的断点            |
| `disable n`        | 禁用指定编号的断点            |

### 打印

| 命令                           | 描述                                    |
| ---------------------------- | ------------------------------------- |
| `call printf("%s\n", str)`   | 调用printf函数，打印字符串(可以使用call或者print调用函数) |
| `print var`                  | 打印变量var的值                             |
| `print ptr `                 | 查看该指针指向的类型及指针地址                       |
| `print *(struct xxx *)ptr`   | 查看指向的结构体的内容                           |
| `set print pretty on`        | 每行只显示结构体的一名成员                         |
| `print *array@10`            | 打印从数组开头连续10个元素的值                      |
| `print array[60]@10`         | 打印array数组下标从60开始的10个元素，即第60\~69个元素    |
| `set print array-indexes on` | 打印数组元素时，同时打印数组的下标                     |
| `set print null-stop`        | 不显示’\000’这种                           |

**打印指定内存地址的值**
使用x命令来打印内存的值，格式为`x/nfu addr`，以`f`格式打印从`addr`开始的`n`个长度单元为`u`的内存值。

*   `n`：输出单元的个数
*   `f`：输出格式，如`x`表示以16进制输出，`o`表示以8进制输出，默认为`x`
*   `u`：一个单元的长度，`b`表示1个byte，`h`表示2个byte（half word），`w`表示4个byte，`g`表示8个byte（giant word）

```shell
eg：以16进制打印从ptr地址开始的5个字节的内存值

(gdb) x/5xb ptr 
```

**指定格式打印变量**

| /fmt | 功能 |
| --- | --- |
| /x | 以十六进制打印 |
| /d | 有符号、十进制 |
| /u | 无符号、十进制 |
| /o | 八进制 |
| /t | 二进制 |
| /f | 浮点数形式打印 |
| /c | 字符形式打印 |

```shell
(gdb) print /x key.imsi
$15 = {0x98, 0x76, 0x54, 0x32, 0x10, 0xff, 0xff, 0xff}
(gdb) print /t val
$16 = 10010011
```

### 修改变量的值

```shell
(gdb) set var key.imsi = {0x98, 0x76, 0x54, 0x32, 0x10, 0xff, 0xff, 0xff}
(gdb) set var xxx = xxx
```

### 多进程、多线程

| 命令                           | 描述                                  |
| ---------------------------- | ----------------------------------- |
| `info frame`                 | 查看进程列表                              |
| `info threads`               | 查看线程列表                              |
| `backtrace [n](bt)`          | 打印栈帧                                |
| `print $_thread`             | 显示当前正在调试的线程编号                       |
| `set scheduler-locking on`   | 调试一个线程时，其他线程暂停执行                    |
| `set scheduler-locking off`  | 调试一个线程时，其他线程同步执行                    |
| `set scheduler-locking step` | 仅用step调试线程时其他线程不执行，用其他命令如next调试时仍执行 |

