

# Git分布式版本控制系统

![gitlogo](https://git-scm.com/images/logo@2x.png)

## 安装Git

Linux

```shell
sudo apt-get install git
```

如果是其他Linux版本，可以直接通过源码安装。先从Git官网下载源码，然后解压，依次输入：./config，make，sudo make install这几个命令安装就好了。

windows
Git官网直接[下载](https://git-scm.com/downloads)安装程序，然后按默认选项安装即可。

基本设置

```shell
$ git config --global user.name "quettabyte"
$ git config --global user.email "quettabyte@163.com"
$ git config --global user.password "xxxx"
```

查看当前配置

```shell
$ git config --global user.name
$ git config --global user.email
$ git config --global user.password
```

本地生成ssh公钥

```shell
ssh-keygen -t rsa -C "email@example.com"
```

## 创建Git仓库

```shell
$ mkdir test
$ cd test
$ git init
```

## 删除Git仓库

```shell
$ rm -rf test
```

## 把文件添加到Git仓库

```shell
$ git add readme.txt
$ git commit -m "wrote a readme file"
```

## 创建分支

```shell
$ git branch <name>
```

```shell
#git checkout命令加上-b参数表示创建并切换
$ git checkout -b dev
```

```shell
#git switch命令加上-c参数表示创建并切换
$ git switch -c dev
```

## 切换分支

```shell
$ git checkout dev
```

```shell
$ git switch dev
```

## 查看分支

当前分支前面会标一个`*`号
每次add commit都是提交到当前分支的

```shell
$ git branch
* dev
  master
```

## 合并分支

合并某分支到当前分支

```shell
$ git merge dev
```

## 删除分支

合并完成后，就可以放心的删除dev分支了

```shell
$ git branch -d dev
```

## 创建远程git仓库

*   在Github上创建一个新的仓库
*   在本地克隆仓库

```shell
git clone https://github.com/cokeyice/hello-world
```

*   推送文件

```shell
$ git add .
$ git commit -m "log info"
$ git push -u origin master

```

shell自动输入账号密码`gitee`

```shell
#!/bin/bash
#


git add --all .
git commit -m "submit something"

/usr/bin/expect << eof
set timeout 3
spawn git push -u origin master
expect "//gitee.com':"
send "cokeice\n"
expect "@gitee.com':"
send "3w.CNGNWLF.com\n"
expect eof
eof
```

github仓库的push必须使用token

> 2020 年 7 月，我们宣布，我们打算 要求对所有经过身份验证的 Git 操作使用基于令牌的身份验证（例如，个人访问、OAuth 或 GitHub 应用程序安装令牌）。 从 2021 年 8 月 13 日开始，在 GitHub.com 上对 Git 操作进行身份验证时，我们将不再接受帐户密码。
>
> 也就是说，2021 年 8 月 13 日之后，我们对 GitHub 上做 Git 操作将无法再使用账户密码来进行身份验证，必须使用基于 token 的身份验证。

## windows10系统删除本地git记录的账号密码

打开控制面板，进入用户账户： 进入凭据管理器： 选择Windows凭据，看到下方的普通凭据，删除git的记录即可：   删除了git本地的账户密码，再次clone项目，会提示输入用户名和密码操作

<img src="https://cokeice-pic.oss-cn-wulanchabu.aliyuncs.com/1683971268976.png" />

# 仓库命令

## 查看仓库状态

```shell
$ git status
```

## 查看远程仓库地址

```shell
$ git remote -v
$ git remote show origin
```

## 添加远程仓库地址

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

```shell
$ git remote add origin git@github.com:quettabyte/netradio.git
```

## 删除远程仓库地址

```shell
$ git remote rm origin
```

## 推送代码

把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

```shell
$ git push -u origin master
```