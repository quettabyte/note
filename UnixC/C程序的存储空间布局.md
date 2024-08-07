# C程序的存储空间布局

![2024-5-18-00-27-42.png](https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/unixc-note/2024-5-18-00-27-42.png)

## 摘要

堆区、栈区、静态存储区（BSS段、数据段、正文段）以及static修饰符

>  BSS段（未初始化的数据） + 数据段（初始化的数据） 也有人叫数据区
> 正文段 也叫 代码区

`size`命令可以查看正文段、数据段和bss段的长度（以字节为单位）。例如：

```shell
$ size ./a.out /usr/bin/sh
   text	   data	    bss	    dec	    hex	filename
   1298	    556	      4	   1858	    742	./a.out
 905942	  36000	  22920	 964862	  eb8fe	/usr/bin/sh
```

第4列和第5列是分别是以十进制和十六进制表示这三段的总长度。



## 正文段

通常指CPU执行的机器指令部分，也可以说是存放程序执行代码的一块内存区域。

## 初始化数据段

通常称为数据段，他包含了程序中需要明确地赋初值的变量。

任何函数外声明的变量，例如 `int maxcount = 99`，它的初值便存放在初始化数据段。

初始化数据段和正文段都是由exec从程序文件中读入的，由`size`命令可以看出，都是存放在磁盘程序文件中的。

执行a.out的时候，当前的bas进程会调用fork创建一个子进程，然后子进程执行exec函数替换为新程序`a.out`。exec只是用磁盘上的`a.out`替换当前子进程的正文段、数据段、BSS段、堆和栈。