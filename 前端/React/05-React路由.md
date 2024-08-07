## React 路由📚

### 前端路由的基石

```html
<script type="text/javascript" src="https://cdn.bootcss.com/history/4.7.2/history.js"></script>
```

> `history` 是一个栈结构

**BrowserHistory**

直接使用H5推出的 `history` 身上的API，一些旧的浏览器可能不支持

```js
let history = History.createBrowserHistory()
```

**HashHistory**

`hash` 值（锚点），兼容性极佳

```js
let history = History.createHashHistory()
```

⛏**操作：**

```js
history.push("/about") // 跳转到/about
history.replace("/about") // 替换当前的path变为/about
history.goBack() // 回退
history.goForward() // 前进
history.listen((location) => {
    // 当路由路径发生变化，触发这个函数
    // 监测路由的变化，从而做出页面的调整
})
```

**总结React中的路由**：

点击导航栏里的导航按钮，引起 `History` 记录里路径的变化，被前端路由器所监测到，进行匹配组件，从而展示页面



### React中路由的基本使用

```shell
npm install react-router-dom@5
```

```js
import {BrowserRouter,HashRouter,Route,Switch,Redirect,NavLink,Link} from 'react-router-dom'
```

1. 明确好界面中的导航区、展示区

2. 导航区的a标签改为 `Link` 标签

   ```jsx
   <Link to="/xxxxx">Demo</Link>
   ```

3. 展示区写 `Route` 标签进行路径的匹配

   ```jsx
   <Route path='/xxxx' component={Demo}/>
   ```

4. `<App>`的最外侧包裹了一个`<BrowserRouter>`或`<HashRouter>`



### 路由组件与一般组件

1. ✒写法不同

   * 一般组件 

     ```jsx
     <Demo />
     ```

   * 路由组件 

     ```jsx
     <Route path="/demo" component={Demo}/>
     ```

2. 📍存放位置不同

   * 一般组件：`components`
   * 路由组件：`pages`

3. 🧬接收到的 `props` 不同

   * 一般组件：写组件标签时传递了什么，就能收到什么

   * 路由组件：接收到三个固定的属性

     ```
     history:
     	- go: ƒ go(n)
     	- goBack: ƒ goBack()
     	- goForward: ƒ goForward()
     	- push: ƒ push(path, state)
     	- replace: ƒ replace(path, state)
     location:
     	- pathname: "/about"
     	- search: ""
     	- state: undefined
     match:
     	- params: {}
     	- path: "/about"
     	- url: "/about"
     ```

### NavLink与封装NavLink

1. `NavLink` 可以实现路由链接的高亮，通过 `activeClassName` 指定样式名

   ---------不指定 `activeClassName` 被选中则默认添加`"active"`类名

   ```jsx
   <NavLink activeClassName="on" className="mynav" to="/demo">Demo</NavLink>
   ```

2. ✉封装 `NavLink`

   使用场景：当有多个导航按钮时

   ```jsx
   <NavLink activeClassName="on" className="mynav" to="/demo1">Demo1</NavLink>
   <NavLink activeClassName="on" className="mynav" to="/demo2">Demo2</NavLink>
   ...
   <NavLink activeClassName="on" className="mynav" to="/demo3">Demo3</NavLink>
   ```
   
   因为 `activeClassName` 和 `className` 代码太过冗余
   
   **封装**
   
   ```jsx
   exprot default class MyNavLink extends Component{
       render() {
           return (
               <NavLink activeClassName="on" className="mynav" {...this.props}/>
           )
       }
   }
   ```
  
   **使用MyNavLink**
   
     ```jsx
    <MyNavLink to="/demo">Demo</MyNavLink>
     ```
   
   **💢总结要点**
   
   1. `MyNavLink`双标签中的文本Demo通过`props`传到了`MyNavLink`组件实例中，通过`this.props.children`可以获取到
   
   2. `NavLink`如果写成双标签
   
      ```jsx
      <NavLink activeClassName="on" className="mynav" {...this.props}>{this.props.children}</NavLink>
      ```
   
   3. `NavLink` 如果写成单标签可以简写
   
      ```jsx
      <NavLink activeClassName="on" className="mynav" {...this.props}/>
      ```

### Switch的使用

1. 通常情况下，`path` 和 `component` 是一一对应的关系。

2. `Switch` 可以提高路由匹配效率(单一匹配)。

```jsx
<Switch>
    <Route path="/about" component={About}/>
    <Route path="/home" component={Home}/>
    <Route path="/home" component={Test}/> 
    {/* 如果没有Switch，匹配到/home还会继续向下匹配，如果下面的路由很多，效率就会很低 */}
    {/* 有Switch不会匹配到Test组件 */}
</Switch>
```

### 解决多级路径刷新页面样式丢失的问题

1. `public/index.html` 中 引入样式时不写 `./` 写` /` （常用）

2. `public/index.html` 中 引入样式时不写 `./` 写 `%PUBLIC_URL%` （常用）

3. 使用 `HashRouter`

### 路由的严格匹配与模糊匹配

1. 默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）

2. 开启严格匹配：

   ```jsx
   <Route exact={true} path="/about" component={About}/>
   ```

3. 严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由

### Redirect的使用

1. 一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到 `Redirect` 指定的路由

2. 具体编码：

   ```jsx
    <Switch>
       <Route path="/about" component={About}/>
       <Route path="/home" component={Home}/>
       <Redirect to="/about"/>
   </Switch>
   ```

### 嵌套路由(多级路由)

1. 注册子路由时要写上父路由的 `path` 值

2. 路由的匹配是按照注册路由的顺序进行的

```jsx
// 父路由
<Route path="/home" component={About}/>
// 子路由
<MyNavLink to="/home/message">Message</MyNavLink>
```

### params传递参数

路由链接(携带参数)

```jsx
<Link to='/demo/test/tom/18'}>详情</Link>
```

注册路由(声明接收)

```jsx
<Route path="/demo/test/:name/:age" component={Test}/>
```

接收参数：

```jsx
this.props.match.params
```

### search传递参数

路由链接(携带参数)

```jsx
<Link to='/demo/test?name=tom&age=18'}>详情</Link>
```

注册路由(无需声明，正常注册即可)

```jsx
<Route path="/demo/test" component={Test}/>
```

接收参数

```jsx
this.props.location.search
```

解析参数

```js
import qs from 'querystring'

```

备注：获取到的search是urlencoded编码字符串，需要借助querystring解析

### state传递参数

路由链接(携带参数)

```jsx
<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>
```

注册路由(无需声明，正常注册即可)

```jsx
<Route path="/demo/test" component={Test}/>
```

接收参数：

```jsx
this.props.location.state
```

备注：刷新也可以保留住参数

### 编程式路由导航

借助`this.prosp.history`对象上的API对操作路由跳转、前进、后退

* -this.prosp.history.push()  跳转
* -this.prosp.history.replace()  替换跳转
* -this.prosp.history.goBack()  后退
* -this.prosp.history.goForward()  前进
* -this.prosp.history.go(n)  n为2前进2个记录，n为-1后退1个记录 

### withRouter

💗作用：普通组件的 `props` 里没有`history`、`loaction`、`match`这三个属性，没办法实现编程式路由导航，使用 `withRouter` 可以让普通组件也能写编程式路由导航

> `withRouter` 可以加工一般组件，让一般组件具备路由组件所特有的API
> `withRouter` 的返回值是一个新组件

✒用法：

```jsx
import {withRouter} from 'react-router-dom'
class Header extends Component {
	back = ()=>{
		this.props.history.goBack()
	}
	forward = ()=>{
		this.props.history.goForward()
	}
	go = ()=>{
		this.props.history.go(-2)
	}
	render() {
		console.log('Header组件收到的props是',this.props);
		return (
			<div className="page-header">
				<h2>React Router Demo</h2>
				<button onClick={this.back}>回退</button>&nbsp;
				<button onClick={this.forward}>前进</button>&nbsp;
				<button onClick={this.go}>go</button>
			</div>
		)
	}
}
export default withRouter(Header)
```

### BrowserRouter与HashRouter的区别

1. 底层原理不一样：
   * `BrowserRouter `使用的是H5的 `history `API，不兼容IE9及以下版本。
   * `HashRouter` 使用的是URL的哈希值。
2. path表现形式不一样
   * `BrowserRouter` 的路径中没有#,例如：localhost:3000/demo/test
   * `HashRouter` 的路径包含#,例如：localhost:3000/#/demo/test

3. 刷新后对路由state参数的影响
   * `BrowserRouter` 没有任何影响，因为 `state` 保存在 `history` 对象中。
   * `HashRouter` 刷新后会导致路由 `state` 参数的丢失！！！

4. 备注：`HashRouter` 可以用于解决一些路径错误相关的问题。
