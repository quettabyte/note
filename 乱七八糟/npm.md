### 国内淘宝镜像

* 命令行永久更改使用指定镜像（淘宝）

  ```shell
  npm config set registry https://registry.npm.taobao.org
  ```

* 使用淘宝 NPM 镜像

  ```shell
  npm install -g cnpm --registry=https://registry.npm.taobao.org
  ```

  这样就可以使用 cnpm 命令来安装模块了： 

  ```shell
  cnpm install xxxxx
  ```
  
* 查看目前使用的npm镜像

  ```shell
  npm config get registry
  ```
  

### 基本操作

* 查看版本

  ```shell
  npm -v
  ```

* 把xxx模块下载到当前目录下

  ```shell
  npm install xxx
  #简写
  npm i xxx
  ```

* 下载方式

  ```shell
  #全局
  npm install xxx -g 
  #局部 ，生产环境（以后下载局部都用-S）
  npm i xxx -S 
  npm install xxx --save
  #局部 ，开发环境
  npm i xxx -D 
  npm install xxx --save-dev
  ```


**初始化一个项目**

```shell
npm init
```

* 一个项目红可以没有 `node_moduls` 但是不能没有 `package.json`(项目的描述文件)

* 执行 `npm install` 可以自动下载 `package.json`  中的所用到的模块

### 上传自己一个包

1. npm中新建一个账户 

   ```
   https://www.npmjs.com
   ```

2. 新建一个项目

   ```
   npm init || cnpm init
   ```

3. 在自己电脑登录  

   ```
   npm login
   输入: userName + 邮箱 + 密码
   提示: Logged in as <userName> on http://registry.npmjs.org/. 成功
   ```

4. 上传

   `package.json` 中的项目名不能重名

   ```shell
   npm publish
   成功后: + 包名@1.0.0
   ```

   

  

  

  

  

