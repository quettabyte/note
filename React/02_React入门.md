## React入门📚

### react开发者工具

<img src="..\..\img\前端\image-20221103215647967.png" alt="image-20221103215647967" style="zoom: 80%;" />

⚙在浏览器插件商店搜索`React Developer Tools`下载并安装

### react脚手架

> 技术：react+webpack+es6+eslint
>
> 特点：模块化，组件化，工程化

#### 创建项目并启动

详见[官方说明](https://cra.docschina.org/docs/getting-started)

```shell
npx create-react-app my-app
cd my-app
npm start
```

#### react脚手架项目结构

执行命令后，你的项目结构应该如：

📂`public`----静态资源文件夹

* favicon.icon ------ 网站页签图标
* **index.html -------- 主页面**
* logo192.png ------- logo图
* logo512.png ------- logo图
* manifest.json ----- 应用加壳的配置文件
* robots.txt -------- 爬虫协议文件

📂`src`----源码文件夹

* App.css -------- App组件的样式
* **App.js --------- App组件**
* App.test.js ---- 用于给App做测试
* index.css ------ 样式
* **index.js ------- 入口文件**
* logo.svg ------- logo图
* reportWebVitals.js ------- 页面性能分析文件(需要web-vitals库的支持)
* setupTests.js ------ 组件单元测试的文件(需要jest-dom库的支持)

📂`node_modules` ---- 存放用包管理工具下载安装的包的文件夹

📄`package.json` ---- 记录当前项目所依赖模块的版本信息，更新模块时锁定模块的大版本号（版本号的第一位）,不能锁定后面的小版本

📄`package-lock.json` ---- 记录了node_modules目录下所有模块（包）的名称、版本号、下载地址、及这个模块又依赖了哪些依赖。(npm5以后)

### Hello World

最简易的 React 示例如下：

```js
import ReactDOM from 'react-dom/client'
```

```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<h1>Hello, world!</h1>);
```

它将在页面上展示一个 “Hello, world!” 的标题。
