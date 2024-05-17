# npm包管理器

## 国内淘宝镜像

### 命令行永久更改使用指定镜像（淘宝）

```shell
$ npm config set registry https://registry.npmmirror.com
```

### 使用淘宝 NPM

> npm是node官方的包管理器。cnpm是个中国版的npm，是淘宝定制的 cnpm (gzip 压缩支持) 命令行工具代替默认的 npm。
>
> 如果因为网络原因无法使用npm下载，那cnpm这个就派上用场了。
>
> 一定切记切记，npm和cnpm只是下载的地址不同，npm是从国外下载东西，cnpm是从国内下载东西。
>
> 来自淘宝NPM镜像官网的说明：
>
> 这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

```shell
$ npm install -g cnpm --registry=https://registry.npm.taobao.org

$ cnpm install xxxxx
```

### 查看目前使用的npm镜像

```shell
$ npm config get registry
```

## 基本操作

### 查看版本

```shell
$ npm -v
```

### 把xxx模块下载到当前目录下

```shell
$ npm install xxx

# 简写
$ npm i xxx
```

### 下载方式

```shell
# 全局
$ npm install xxx -g
    
# 局部 ，生产环境
$ npm i xxx -S 
$ npm install xxx --save

# 局部 ，开发环境
$ npm i xxx -D 
$ npm install xxx --save-dev
```

> 注意：-D，-S 分别是 --save-dev和 --save的简写，默认就是 -S，可以省略不写
>
> ---
>
>  npm i module_name -S    
>
> 这样安装是局部安装的，会写进package.json文件中的dependencie里。
>
> dependencies： 表示生产环境下的依赖管理；
>
> 说白了你安装一个库如果是用来构建你的项目的，比如echarts、element-ui，是实际在项目中起作用，就可以使用 -s 来安装。
>
> ---
>
> npm i module_name -D   
> 这样安装是局部安装的，会写进package.json文件中的devDependencies 里。
>
> devDependencies ：表示开发环境下的依赖管理；
>
> 如果你安装的库是用来打包的、解析代码的，比如webpack、babel，就可以用 -d 来安装，项目上线了，这些库就没用了，不然留这些库给用户自己来打包和解析代码嘛。

🎈 npm安装模块

* 【npm install xxx】利用 npm 安装xxx模块到当前命令行所在目录；

* 【npm install -g xxx】利用npm安装全局模块xxx；

* 【npm install xxx】安装但不写入package.json；

* 【npm install xxx –save】 安装并写入package.json的”dependencies”中；

* 【npm install xxx –save-dev】安装并写入package.json的”devDependencies”中。

🎈 npm 删除模块

* 【npm uninstall/remove  xxx 】删除xxx模块；
* 【npm uninstall/remove  -g xxx】删除全局模块xxx；



## 初始化一个项目

```shell
$ npm init
```

*   一个项目红可以没有 `node_moduls` 但是不能没有 `package.json`(项目的描述文件)

*   执行 `npm install` 可以自动下载 `package.json`  中的所用到的模块

### package.json和package-lock.json

* **package.json**是通过npm init创建时生成的，package.json文件中会记录项目中所需要的模块。记录的只是每个模块的基本信息。模块名称和大版本信息。

* 在使用npm install的时候会自动生成一个**package-lock.json**的文件，package-lock.json文件则会记录每个模块的详细信息，如模块的具体版本号和各个模块所依赖的子模块的信息。

* **npm install**的过程大致就是从package.json中读取所有的依赖信息，然后再与node_modules中已经安装的依赖进行对比，如果没有则通过package-lock.json获取相应版本号下载安装.如果已经存在则会通过package-lock.json检查更新。

* 进行**更新**的原则就是其范围是在package.json中对应安装包版本所容纳的版本。\^就是指兼容该版本以后的小版本而不更新大版本，如上图package.json所示，"vue":"\^2.5.2"也就是指范围应该在>=2.5.2和<3.0.0之间(tips:网上大多数帖子说的比较笼统，而参考其中的一篇帖子大致就是，\^会忽视版本号开头为0的数字，也就是说"axios":"0.19.0"的范围应该在>=0.19.0和<0.20.0之间)

## 上传到npm一个自己的包

让别人可以通过`npm install xxx`可以安装你的包

1.  npm中新建一个账户

```shell
https://www.npmjs.com
```

2.  新建一个项目

```shell
$ npm init || cnpm init
```

3.  在自己电脑登录

```shell
$ npm login
# 输入: userName + 邮箱 + 密码
# 提示: Logged in as <userName> on http://registry.npmjs.org/. 成功
```

4.  上传

`package.json` 中的项目名不能重名

```shell
npm publish
成功后: + 包名@1.0.0
```

