forever可以看做是一个nodejs的守护进程，能够启动，停止，重启我们的app应用。

1. 全局安装 forever

```shell
#记得加-g，forever要求安装到全局环境下 
sudo npm install forever -g
# 卸载
npm uninstall -g forever
```

2. 启动

```shell
# 1. 简单的启动 
forever start app.js 
 
# 2. 指定forever信息输出文件，当然，默认它会放到~/.forever/forever.log 
forever start -l forever.log app.js 
 
# 3. 指定app.js中的日志信息和错误日志输出文件， 
# -o 就是console.log输出的信息，-e 就是console.error输出的信息 
forever start -o out.log -e err.log app.js 
 
# 4. 追加日志，forever默认是不能覆盖上次的启动日志， 
# 所以如果第二次启动不加-a，则会不让运行 
forever start -l forever.log -a app.js 
 
# 5. 监听当前文件夹下的所有文件改动 
forever start -w app.js
```

3. 文件改动监听并自动重启

```shell
# 1. 监听当前文件夹下的所有文件改动（不太建议这样） 
forever start -w app.js 
```

4. 显示所有运行的服务

```shell
forever list 
```

5. 停止操作

```shell
# 1. 停止所有运行的node App 
forever stopall 
 
# 2. 停止其中一个node App 
forever stop app.js 
# 当然还可以这样 
# forever list 找到对应的id，然后： 
forever stop [id]
```

6. 重启操作 

```shell
# 重启操作跟停止操作保持一致。 
# 启动所有 
forever restartall
```