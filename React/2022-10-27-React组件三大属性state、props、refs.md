---
title: React组件三大属性state、props、refs
date: 2022-10-27 16:04:04
author: cokeice
tags: [JavaScript, React]
categories: 
    - 笔记
    - React框架
index_img: /img/react/react.webp
---

<p align='center'>
<a href="https://www.github.com/Cokeic" target="_blank"><img src="https://img.shields.io/badge/Github-@可乐冰-f3e1e1.svg?style=flat-square&logo=Github&logoColor=181717"></a><a href="https://www.gitee.com/Cokeice" target="_blank"><img src="https://img.shields.io/badge/Gitee-@可乐冰-f3e1e1.svg?style=flat-square&logo=Gitee&logoColor=C71D23"></a><a href="https://cokeice.gitee.io/img/wechat/wx.png" target="_blank"><img src="https://img.shields.io/badge/微信-@LNFeng-f3e1e1.svg?style=flat-square&logo=WeChat"></a>

## 组件属性 state📚

* state是组件对象最重要的属性，值是对象（key-value组合）

* 组件被称为“状态机”，通过更新组件的state来更新对应的页面显示（重新渲染组件）

❗  ❗  ❗ 注意：

1. 组件中render方法中的this为组件实例对象
2. 组件自定义的方法中this为`undefined`,解决办法：
   * 强制绑定this；通过函数对象的`bind()`
   * 箭头函数
3. 状态数据，不能直接修改或更新，必须使用`setState()`

✔eg:

* 强制绑定this

```js
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

```js
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

* setState

```js
//严重注意：状态必须通过setState进行更新,且更新是一种合并，不是替换。
setState({
    key: value,
    key: value,
    ...
})
```
## 组件属性 props📚

* 每个组件对象都会有props属性
* 组件标签的所有属性都保存在props中
* 通过标签属性从组件外向组件内传递变化的数据
* props是只读的，在实例中不能修改

❗  ❗  ❗ 注意： 组件内部不要修改props数据

✔eg:

* 类组件

```js
// 组件中获取props
class Demo extends React.Component{
    render(){
        const {name,age,sex} = this.props
    }
}
const p = {name:'老刘',age:18,sex:'女'}
ReactDOM.render(<Person {...p}/>, document.getElementById('container'))
ReactDOM.render(<Person name="老刘" age={18} sex="男"/>, document.getElementById('container'))
```

* 函数组件

```js
function Person (props){
    const {name,age,sex} = props;
}
ReactDOM.render(<Person name="jerry" age={18} sex="女"/>,document.getElementById('container'))
```

### props类型限制与默认值

前提条件引入`prop-types`

```js
import myprop from 'prop-types'
```

* 只适用类组件

```js

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

```js
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

## 组件属性 refs📚

下面是几个适合使用 refs 的情况：

* 管理焦点，文本📄选择或媒体🎵播放。
* 触发强制动画动画。
* 集成第三方 DOM 库。

### createRef API 方法

最新的官方推荐使用的方法✅（ 版本 ≥ v16.3）

```js
myRef = React.createRef();
eventFunc = () => {
    console.log(this.myRef.current.value);
}
<input ref={this.myRef}  />
```

React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点

一个容器只能绑定一个节点

ref的值根据节点的类型而有所不同：

* 当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
* 当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的挂载实例作为其 `current` 属性。
* **你不能在函数组件上使用 `ref` 属性**，因为他们没有实例。
* 如果要在函数组件中使用 `ref`，你可以使用 [`forwardRef`](https://zh-hans.reactjs.org/docs/forwarding-refs.html)（可与 [`useImperativeHandle`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle) 结合使用），或者可以将该组件转化为 class 组件。

### 回调refs

```js
eventFunc = () => {
    console.log(this.myinput.value);
}
<input ref={(c) => this.myinput = c} type="text"/>
```

存在一个无关紧要的问题的🔔

> ✅官网描述
>
> 如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

将ref的回调函数定义成class的绑定函数的方式：

```js
saveInput = (c) => {
    this.myinput = c;
}
<input ref = {this.saveInput} type="text"/>
```

### 字符串形式refs

过时并可能会在未来版本中移除❎

```js
eventFunc = () => {
    const {myinput} = this.refs;
    console.log(myinput.value);
}
<input ref="myinput" onBlur={this.showData} type="text" placeholder="失去焦点提示数据"/>
```

