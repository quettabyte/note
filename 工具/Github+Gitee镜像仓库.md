# 创建github+gitee镜像仓库

**需求：**

大多数的开发者都或多或少在`GitHub`上维护有项目，但是通常`GitHub`访问起来都很慢，或者无法响应。为了不能正常访问`GitHub`的用户，一般会将`Gitee`或其它平台托管作为镜像。

**功能：**

对github的仓库的所有操作，自动同步到gitee的仓库

## 方法一：推送多次

查看当前仓库关联的远程库

```
$ git remote -v
origin  https://github.com/quettabyte/note.git (fetch)
origin  https://github.com/quettabyte/note.git (push)
```

删除`origin`,然后依次关联远程Github和gitee仓库

```
$ git remote rm origin
$ git remote add gitbub https://github.com/quettabyte/note.git
$ git remote add gitee https://gitee.com/quettabyte/note.git

$ git remote -v
gitbub  https://github.com/quettabyte/note.git (fetch)
gitbub  https://github.com/quettabyte/note.git (push)
gitee   https://gitee.com/quettabyte/note.git (fetch)
gitee   https://gitee.com/quettabyte/note.git (push)
```

本地代码提交后，分别推送至两个远端。缺点：多次推送冗余

```
$ git push github master
$ git push gitee master
```

## 方法二：修改 Git 内部配置

查看当前仓库关联的远程库

```
$ git remote -v
origin  https://github.com/quettabyte/note.git (fetch)
origin  https://github.com/quettabyte/note.git (push)
```

添加一条`push`的远端地址

```
$ git remote set-url --add origin https://gitee.com/xxx/repo.git

$ git remote -v
origin  https://github.com/quettabyte/dfdf.git (fetch)
origin  https://github.com/quettabyte/dfdf.git (push)
origin  https://gitee.com/quettabyte/dfdf.git (push)
```

因此`fetch`时将从`GitHub`拉取代码，`push`时将推送到`Gitee`和`GitHub`两个远端

<img src="https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-8-18_07_53.png" style="zoom:80%;" />

## 方法三：GitHub Actions

此方法配置相较上面两种较为复杂，但原理也比较容易理解。

`GitHub Actions`其实就是一个免费的虚拟机，提供了三种可选的操作系统（`Ubuntu Linux`、`Microsoft Windows`和`macOS`），用以执行用户自定义的工作流程。

这个工作流程就是一个以`.yml`为后缀的文件（`YAML`语法），注意此文件要放置在代码仓库中的目录`.github/workflows`下才会生效。

> 注意对于每个工作流程，`GitHub`都会在预先配置好的全新虚拟机中执行

### Hello World

既然`GitHub Actions`就是虚拟机，可以打印`Hello World`试试

首先，在仓库中选中Actions，单击`set up a workflow yourself`创建工作流程。

<img src="https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-8-18_18_55.png" style="zoom: 67%;" />

然后在代码编辑器粘贴以下代码，以下为相关命令的含义，更多可 [参考](https://docs.github.com/cn/actions/using-workflows/workflow-syntax-for-github-actions)。

* `name: ...`：工作流程的名称为Console hello world
* `on: ...`：仓库发生推送push事件时执行
* `jobs`：表示执行的一项或多项任务，当前仅有一个任务console
* `console`：任务名为console
* `runs-on ...`：任务console运行的虚拟机为最新的Ubuntu Linux环境
* `steps`：任务console的运行步骤，当前仅有一个步骤
* `run ...`：运行命令echo Hello world

```yaml
# .github/gitflows/main.yml
name: Console hello world

on: push

jobs:
  console:
    runs-on: ubuntu-latest
    steps:
      - run: echo Hello world
```

编辑后点击Commit提交，由于代码是在`GitHub`上提交的，相当于是本地代码推送`push`了一次，而脚本的执行条件就是`push`事件的发生，因此`GitHub Actions`将会触发。单击`Create main.yml`查看此次推送执行的工作流程，成功在虚拟机下打印出`Hello world`。

<img src="https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-8-18_25_47.png" style="zoom: 67%;" />

### 实现镜像

通过Hello World的例子已经大致明白实现镜像的原理。其实就是，在push到github仓库出发`Github Action`执行脚本去同步到gitee。

#### 生成ssh公钥和私钥

```
C:\Users\LN Feng\.ssh>ssh-keygen -t ed25519 -C "quettabyte@163.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\LN Feng/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\LN Feng/.ssh/id_ed25519.
Your public key has been saved in C:\Users\LN Feng/.ssh/id_ed25519.pub.
The key fingerprint is:
SHA256:uJFZL3XZTf2FFVeVIGV/Wfyg+jQES+O2d2Odpek15EE quettabyte@163.com
The key's randomart image is:
+--[ED25519 256]--+
|           ..+.=&|
|          + oo+EB|
|        .o.+o.o+*|
|       = o+.o  o=|
|      = S..+  o++|
|       o .o + *+o|
|      .    + = o.|
|            . .  |
|                 |
+----[SHA256]-----+

# 公钥
$ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGeICQd2Fy3sCWO+t+rf1hVKfEXHsCXu9woZ7HbXxbTK quettabyte@163.com

# 私钥
$ cat ~/.ssh/id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACBniAkHdhct7Aljvrfq39YVSnxFx7Al7vcKGex218W0ygAAAJjxDllm8Q5Z
ZgAAAAtzc2gtZWQyNTUxOQAAACBniAkHdhct7Aljvrfq39YVSnxFx7Al7vcKGex218W0yg
AAAED0mjMLE5/JXcGnLV776dkrQD5SfqcIV832C9+VcpRVmGeICQd2Fy3sCWO+t+rf1hVK
fEXHsCXu9woZ7HbXxbTKAAAAEnF1ZXR0YWJ5dGVAMTYzLmNvbQECAw==
-----END OPENSSH PRIVATE KEY-----
```

#### 公钥添加到Gitee

公钥需要添加到Gitee账户下，这时候携带私钥的主机去提交才能提交成功。

<img src="https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-9-10_10_29.png" alt="2024-5-9-10_10_29.png" style="zoom: 67%;" />

#### 私钥添加到Github

在GitHub的仓库（需要同步的仓库）下，`Settings`功能选择`Secrets and variables`->`Actions`，单击`New repository secret`添加秘钥。注意：需要将文件中所有内容都复制过去。

<img src="https://lnfeng-pic.oss-cn-wulanchabu.aliyuncs.com/tools-note/2024-5-9-10_17_04.png" alt="2024-5-9-10_17_04.png" style="zoom:80%;" />

#### 编辑.yml文件

修改.github/workflow/main.yml

```yaml
# .github/gitflows/main.yml
name: Mirror to Gitee repository

on: [ push, delete, create ]

jobs:
  mirror:
    runs-on: ubuntu-latest
    steps:
      - name: Config private key
        env:
          SSH_PRIVATE_KEY: ${{ secrets.GITEE_PRIVATE_KEY }}
        run: |
          mkdir -p ~/.ssh
          echo "$SSH_PRIVATE_KEY" > ~/.ssh/id_ed25519
          chmod 600 ~/.ssh/id_ed25519
          echo "StrictHostKeyChecking no" >> ~/.ssh/config

      - name: Clone repository and push
        env:
          SRC_REPO: "git@github.com:quettabyte/xxxx.git"
          DIST_REPO: "git@gitee.com:quettabyte/xxxx.git"
        run: |
          git config --global user.name "quettabyte"
          git config --global user.email "quettabyte@163.com"
          git clone --mirror "$SRC_REPO"
          cd `basename "$SRC_REPO"`
          git remote set-url --push origin "$DIST_REPO"
          git push --mirror
```

### hub-mirror-action方式

> 🎨项目比较复杂时建议使用

`GitHub`想到了一个很好的办法，开发者可以发布不同的工作流程到 [官方市场](https://github.com/marketplace?type=actions)，而用户可以引用别人的`actions`即可。同步`GitHub`仓库到`Gitee`的功能，很早就有团队写好发布了，缺陷相对也很少。

[Hub Mirror Action. · Actions · GitHub Marketplace](https://github.com/marketplace/actions/hub-mirror-action)一个用于在hub间（例如Github，Gitee）账户代码仓库同步的action

#### 用法

```
steps:
- name: Mirror the Github organization repos to Gitee.
  uses: Yikun/hub-mirror-action@master
  with:
    src: github/kunpengcompute
    dst: gitee/kunpengcompute
    dst_key: ${{ secrets.GITEE_PRIVATE_KEY }}
    dst_token: ${{ secrets.GITEE_TOKEN }}
    account_type: org
    # src_account_type: org
    # dst_account_type: org
```

#### 参考

- [Hub mirror template](https://github.com/yi-Xu-0100/hub-mirror): 一个用于展示如何使用这个action的模板仓库. from @yi-Xu-0100
- [自动镜像 GitHub 仓库到 Gitee](https://github.com/ShixiangWang/sync2gitee): 一个关于如何使用这个action的介绍. from @ShixiangWang
- [巧用Github Action同步代码到Gitee](http://yikun.github.io/2020/01/17/巧用Github-Action同步代码到Gitee/): Github Action第一篇软文

# SSH介绍

## ssh-keygen命令

ssh-keygen是OpenSSH身份验证密钥实用工具。

ssh-keygen 用于 OpenSSH 身份验证密钥的生成、管理和转换，它支持 RSA 和 DSA 两种认证密钥

### 格式

```shell
ssh-keygen [OPTIONS] <file>...
```

### 选项说明

```shell
-b <bits>       指定密钥长度。
-e              读取 OpenSSH 的私钥或者公钥文件。
-C              添加注释。
-f <filename>   指定用来保存密钥的文件名。
-i              读取未加密的 ssh-v2 兼容的私钥/公钥文件，然后在标准输出设备上显示 openssh 兼容的私钥/公钥。
-l              显示公钥文件的指纹数据。
-N              提供一个新密语。
-P <passphrase> 提供（旧）密语。
-q              静默模式。
-t              指定要创建的密钥类型。
```

### 常用示例

（1）创建一个默认密钥。

```shell
ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/lighthouse/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/lighthouse/.ssh/id_rsa.
Your public key has been saved in /home/lighthouse/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c8jkpkXgRqqfelFHKxq956d+6qYzAR0kHgnaVs9gtYw lighthouse@VM-0-3-centos
The key's randomart image is:
+---[RSA 2048]----+
|  ..*+=          |
| o +.%.o.        |
|. o EoBoo.       |
| . .o.==o.       |
|  .  = +S .      |
|   .o.o+.o       |
|    o..+         |
|   .. o o o      |
|  ..  .B==       |
+----[SHA256]-----+
```

中途需要三次确认，全部缺省直接回车即可。

完成后，在`~/.ssh` 目录下将会看到两个文件：

```shell
$ ls -l ~/.ssh
id_rsa  id_rsa.pub
```

id_rsa 为当前主机的私钥。id_rsa.pub 为当前主机的公钥。

（2）指定要创建的密钥类型，缺省为 RSA。

```shell
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:nTaoqOxlG6IQQ2zDTMvSk2EON+4tLrYqPy7IBrstoy4 root@localhost.localdomain
The key's randomart image is:
+---[RSA 2048]----+
|..=              |
|*B.+             |
|.X*              |
|+..o     o .     |
|o o .   S =      |
|.+ . . . . .     |
|*oo = .          |
|EBo= o           |
|%@B..            |
+----[SHA256]-----+
```

（3）指定密钥的类型并添加注释。

```shell
ssh-keygen -t rsa -C "dablelv@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Wx3MWwj36fwhcnb6hjdIIJ3SUggCLcmFq62Earqy2E0 deng@qq.com
The key's randomart image is:
+---[RSA 2048]----+
|  ..*o .. o .    |
|   = ..  . * o . |
|    o     + * +  |
|   .     + * *   |
|. o     S =.++oo |
|.o .     o  +.+..|
|o . E   .   ..o .|
|++ o         o.+ |
|Oo. .         o..|
+----[SHA256]-----+
```

（4）读取 OpenSSH 的私钥或者公钥文件。

```shell
ssh-keygen -e
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "2048-bit RSA, converted by lighthouse@VM-0-3-centos from Ope"
AAAAB3NzaC1yc2EAAAADAQABAAABAQDb1aKBbvfSefnuzLfhNKlIa4zsbBFG+m7ugZbeBW
RwJXONhSq/AW27+Tq9zDtI6qG+UxmjIorVHbAVl4llVZz8e5b/s5I0yiBoLy/RokpvisNB
kVkWl2oNGtkdHxTSYcJ3jdbTZ+ya6MyOiaMt24jV+zxxS1BXWxA14kS/JqiMC7lx9Vu0Ed
AHY0zq2dj+pX31FB7Xs7p98eO+Est6msCGIInIpzGTlTskL6m7B+aMBaquWlEyQAmRX5G8
YoOFw+aDT4q1aaaaBkFdcy/nhHPpbfM8eIzbAv+khHRjZV8XQCo+UeHzme8nmfWDCWwKZ8
TnpO239diTdl2Wps2YCMex
---- END SSH2 PUBLIC KEY ----
```

（5）安静模式生成密钥对。

```shell
ssh-keygen -q -t rsa
Enter file in which to save the key (/home/lighthouse/.ssh/id_rsa): 
/home/lighthouse/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again:
```

### authorized_keys和known_hosts

有时，你在 ~/.ssh 目录下可能还会看到 authorized_keys 和 known_hosts 这两个文件。

* authorized_keys

如果当前主机是 SSH 服务端，那么会有 authorized_keys，用来存放客户端机器的公钥。

我们需要本地机器通过 SSH 访问远程服务器时为了减少输入密码的步骤，基本上都会在本地机器生成 SSH 公钥，然后将本地 SSH 公钥复制到远程主机的 ~/.ssh/authorized_keys 中，这样就可以免密登录了。

* known_hosts

如果当前主机为 SSH 客户端，你可能会在 ~/.ssh 目录下看到 known_hosts 文件，该文件用来记录连接过的远程主机。

known_hosts 文件每行记录一个连接过的远程服务器的公钥。

文件中的每一行都包含以下字段：**标记符(可选)、主机名、公钥类型、base64 编码的公钥、注释**。字段之间用空格分隔。

如果是首次连接某个远程主机，那么会有安全提示是否继续连接。

```shell
The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```