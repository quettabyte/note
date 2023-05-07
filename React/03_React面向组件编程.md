## React面向组件编程📚

### 组件

☑理解：用来实现局部功能的代码和资源的集合（html css js image video等等）

🥇作用：复用编码, 简化项目编码, 提高运行效率

要点：

* 组件名必须首字母大写

* 虚拟DOM元素只能有一个根元素

* 虚拟DOM元素必须有结束标签

#### 函数组件

🎨创建

```jsx
function MyComponent(){
  console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
  return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
}
```

🔁渲染流程

1. React解析组件标签，找到了`MyComponent`组件。
2. 发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中。

#### 类组件

🎨创建

```jsx
//1.创建类式组件
class MyComponent extends React.Component {
  render(){
    //render是放在哪里的？—— MyComponent的原型对象上，供实例使用。
    //render中的this是谁？—— MyComponent的实例对象 <=> MyComponent组件实例对象。
    console.log('render中的this:',this);
    return <h2>我是用类定义的组件(适用于【复杂组件】的定义)</h2>
  }
}
```

🔁渲染流程

1. React解析组件标签，找到了`MyComponent`组件。
2. 发现组件是使用类定义的，随后`new`出来该类的实例，并通过该实例调用到原型上的`render`方法。
3. 将`render`返回的虚拟DOM转为真实DOM，随后呈现在页面中。

### 组件属性 state

* `state`是组件对象最重要的属性，值是对象（`key-value`组合）

* 组件被称为“状态机”，通过更新组件的`state`来更新对应的页面显示（重新渲染组件）

❗ **注意**：

1. 组件中render方法中的this为组件实例对象
2. 组件自定义的方法中this为`undefined`,解决办法：
   * 强制绑定`this`；通过函数对象的`bind()`
   * 箭头函数
3. 状态数据，不能直接修改或更新，必须使用`setState()`

* 强制绑定`this`

```jsx
class Demo extends React.Component{
  //构造器调用几次？ ———— 1次
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  // handleClick调用几次？ ———— 点几次调几次
  handleClick() { 
    //changeWeather放在哪里？ ———— Weather的原型对象上，供实例使用
    //由于changeWeather是作为onClick的回调，所以不是通过实例调用的，是直接调用
    //类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined
    console.count();
  }
  //render调用几次？ ———— 1+n次 1是初始化的那次 n是状态更新的次数
  render(){
    return <h1 onClick={this.handleClick}>点我计数</h1>
  }
}
```

* 箭头函数

```jsx
class Demo extends React.Component{
  // 函数是放在实例上的
  handleClick = () => {
    console.count();
  }
  render(){
    return <h1 onClick={this.handleClick}>点我计数</h1>
  }
}
```

* `setState`

```js
//严重注意：状态必须通过setState进行更新,且更新是一种合并，不是替换。
setState({
  key: value,
  key: value,
  ...
})
```



### 组件属性 props

* 每个组件对象都会有props属性
* 组件标签的所有属性都保存在props中
* 通过标签属性从组件外向组件内传递变化的数据
* props是只读的，在实例中不能修改

❗  ❗  ❗ 注意： 组件内部不要修改props数据

✔eg:

* 类组件

```jsx
// 组件中获取props
class Demo extends React.Component{
  render(){
    const {name,age,sex} = this.props;
  }
}
const p = {name:'老刘',age:18,sex:'女'}
ReactDOM.render(<Person {...p}/>, document.getElementById('container'))
ReactDOM.render(<Person name="老刘" age={18} sex="男"/>, document.getElementById('container'))
```

* 函数组件

```jsx
function Person (props){
  const {name,age,sex} = props;
}
ReactDOM.render(<Person name="jerry" age={18} sex="女"/>,document.getElementById('container'))
```

#### props类型限制与默认值

前提条件引入`prop-types`

```jsx
import myprop from 'prop-types'
```

* 只适用类组件

```jsx
class Demo extends React.Component{
  // 对标签属性进行类型、必要性的限制
  static propTypes = {
    name:myprop.string.isRequired, //限制name必传，且为字符串
    sex:myprop.string,//限制sex为字符串
    age:myprop.number,//限制age为数值
  }
  // 默认属性 不传参时使用默认属性
  static defaultProps = {
    sex:'男',//sex默认值为男
    age:18 //age默认值为18
  }
}
```

* 适用函数组件、类组件

```jsx
function Demo (props){
  return (JSX)
}
Demo.propTypes = {
  name:PropTypes.string.isRequired, //限制name必传，且为字符串
  sex:PropTypes.string,//限制sex为字符串
  age:PropTypes.number,//限制age为数值
}
//指定默认标签属性值
Demo.defaultProps = {
  sex:'男',//sex默认值为男
  age:18 //age默认值为18
}
```

### 组件属性 refs

下面是几个适合使用 `refs` 的情况：

* 管理焦点，文本📄选择或媒体🎵播放。
* 触发强制动画动画。
* 集成第三方 DOM 库。

#### createRef API 方法

最新的官方推荐使用的方法✅（ 版本 ≥ v16.3）

```jsx
myRef = React.createRef();
eventFunc = () => {
  console.log(this.myRef.current.value);
}
<input ref={this.myRef}  />
```

`React.createRef`调用后可以返回一个容器，该容器可以存储被`ref`所标识的节点

一个容器只能绑定一个节点

ref的值根据节点的类型而有所不同：

* 当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
* 当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的挂载实例作为其 `current` 属性。
* **你不能在函数组件上使用 `ref` 属性**，因为他们没有实例。
* 如果要在函数组件中使用 `ref`，你可以使用 [`forwardRef`](https://zh-hans.reactjs.org/docs/forwarding-refs.html)（可与 [`useImperativeHandle`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle) 结合使用），或者可以将该组件转化为 class 组件。

#### 回调refs

```jsx
eventFunc = () => {
  console.log(this.myinput.value);
}
<input ref={(c) => this.myinput = c} type="text"/>
```

存在一个无关紧要的问题的🔔

> ✅官网描述
>
> 如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null` ，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 `ref ` 并且设置新的。通过将 `ref` 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

将 `ref` 的回调函数定义成class的绑定函数的方式：

```jsx
saveInput = (c) => {
  this.myinput = c;
}
<input ref = {this.saveInput} type="text"/>
```

#### 字符串形式refs

过时并可能会在未来版本中移除❎

```jsx
eventFunc = () => {
  const {myinput} = this.refs;
  console.log(myinput.value);
}
<input ref="myinput" onBlur={this.showData} type="text" placeholder="失去焦点提示数据"/>
```

### 受控组件与非受控组件

#### 非受控组件

```jsx
class Login extends React.Component{
  render(){
    return(
      <input ref={c => this.username = c} type="text" name="username"/>
    )
  }
}
```

#### 受控组件

```jsx
class Login extends React.Component{
	state = {
    username:''
  }
	saveUsername = event => this.setState({username:event.target.value});
	render(){
    return(
      <input onChange={this.saveUsername} type="text" name="username"/>
    )
  }
}
```

**🏆总结：**

* 受控组件：页面中所有输入类的DOM，比如说input，随着用户的输入，React就可以把数据维护到状态`state`里，等需要用到数据的时候直接从状态里就可以拿到数据，这就是受控组件。
* 非受控组件：需要数据的时候才从DOM元素中去拿数据。
* 受控组件可以减少使用`ref`

### 高阶函数与函数柯里化

#### 高阶函数

如果一个函数符合下面2个规范中的任何一个，那该函数就是高阶函数。

1. 若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数。

2. 若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数。

常见的高阶函数有：`Promise`、`setTimeout`、`arr.map()`等等

#### 函数柯里化

通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式。 

```js
function sum(a){
  return(b)=>{
    return (c)=>{
      return a+b+c
    }
  }
}
```

#### 作用 eg:

当需要操作的输入类标签较多的时候，如果不用柯里化，就需要每个标签都得有与之对应的修改 `state` 的函数，很麻烦

```jsx
state = {
  username:'',
  password:'',
  phone: '',
  address: '',
  email: ''
}
saveFormData = (dataType)=>{
  return (event)=>{
    this.setState({[dataType]:event.target.value})
  }
}
render(){
  return(
    <form onSubmit={this.handleSubmit}>
      用户名：<input onChange={this.saveFormData('username')} type="text" name="username"/>
      密码：<input onChange={this.saveFormData('password')} type="password" name="password"/>
      手机号：<input onChange={this.saveFormData('phone')} type="text" name="phone"/>
      住址：<input onChange={this.saveFormData('address')} type="text" name="address"/>
      邮箱：<input onChange={this.saveFormData('email')} type="text" name="email"/>
      <button>登录</button>
    </form>
  )
}
```

🔗不使用柯里化也能实现:

```jsx
state = {
    username:'',
    password:'',
}
saveFormData = (dataType,event) => {
    this.setState({[dataType]:event.target.value})
}
render(){
	return(
		<form onSubmit={this.handleSubmit}>
      用户名：<input onChange={event => this.saveFormData('username',event) } type="text" name="username"/>
			密码：<input onChange={event => this.saveFormData('password',event) } type="password" name="password"/>
			<button>登录</button>
    </form>
	)
}
```

### 生命周期函数（钩子）

#### 旧版本

<img src="https://cokeice-pic.oss-cn-wulanchabu.aliyuncs.com/react%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F(%E6%97%A7).png" style="width:600px;" />

1. 初始化阶段: 由`ReactDOM.render()`触发---初次渲染

   ```
   constructor() //构造器函数
   componentWillMount() //将要挂载 UNSAFE_
   render() //渲染组件
   componentDidMount() //挂载完成
   	// ====> 常用:一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
   ```

2. 更新阶段: 由组件内部`this.setSate()`或父组件`render`触发

          componentWillReceiveProps(nextProps) //只有父组件render会触发 UNSAFE_
          shouldComponentUpdate(nextProps, nextState) //是否更新组件，参数：将要更新的props和state
          componentWillUpdate() //组件将要更新 UNSAFE_
          render() ====> 必须使用的一个
          componentDidUpdate(preProps, preState) //组件更新完成，参数：更新前的props和state

3. 卸载组件: 由 `ReactDOM.unmountComponentAtNode()` 触发

   ```
   componentWillUnmount() //组件将要卸载 
   	// ====> 常用:一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息
   ```

#### 新版本

<img src="https://cokeice-pic.oss-cn-wulanchabu.aliyuncs.com/react%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F(%E6%96%B0).png" style="width:600px;" />

* 新版删除了三个钩子函数：`componentWillMount` 、`componentWillReceiveProps` 、 `componentWillUpdate` 。在新版本中这三个钩子需要加 `UNSAFE_` 前缀才能使用，后续可能会废弃。

* 新增两个钩子（实际场景用得很少）：`getDerivedStateFromProps` 、`getSnapshotBeforeUpdate`

1. 初始化阶段: 由`ReactDOM.render()`触发---初次渲染

      ```
      constructor()
      getDerivedStateFromProps 
      render()
      componentDidMount() 
      ```

2. 更新阶段: 由组件内部`this.setSate()`或父组件重新`render`触发

      ```
      getDerivedStateFromProps
      shouldComponentUpdate()
      render()
      getSnapshotBeforeUpdate
      componentDidUpdate()
      ```

3. 卸载组件: 由`ReactDOM.unmountComponentAtNode()`触发

      ```
      componentWillUnmount()
      ```

4.  `getDerivedStateFromProps`

   * 需使用 `static` 修饰
   * 需返回一个对象更新 `state` 或返回 `null`
   * 适用于如下情况：`state` 的值任何时候都取决于 `props`

   ```jsx
   static getDerivedStateFromProps(props,state){
     console.log('getDerivedStateFromProps',props,state);
     return null
   }
   ```

5.  `getSnapshotBeforeUpdate`

   * 在组件更新之前获取快照
   * 得组件能在发生更改之前从 DOM 中捕获一些信息（如滚动位置）
   * 返回值将作为参数传递给 `componentDidUpdate()`

   🔗eg：固定滚动条

   ```jsx
   class NewsList extends React.Component{
     state = {newsArr:[]}
     componentDidMount(){
       setInterval(() => {
         //获取原状态
         const {newsArr} = this.state
         //模拟一条新闻
         const news = '新闻'+ (newsArr.length+1)
         //更新状态
         this.setState({newsArr:[news,...newsArr]})
       }, 1000);
     }
     
     getSnapshotBeforeUpdate(){
       return this.refs.list.scrollHeight
     }
     
     componentDidUpdate(preProps,preState,height){
       this.refs.list.scrollTop += this.refs.list.scrollHeight - height
     }
     
     render(){
       return(
         <div className="list" ref="list">
           {
             this.state.newsArr.map((n,index)=>{
               return <div key={index} className="news">{n}</div>
             })
           }
         </div>
       )
     }
   }
   ```

#### 最重要的三个钩子

* `render` ：初始化渲染和更新渲染
* `componentDidMount` ：进行初始化，如开启定时器、发送网络请求、订阅消息
* `componentWillUnmount` ：进行收尾，如关闭定时器、取消订阅消息
