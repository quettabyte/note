## 组件属性 state

### State

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

* setState

```jsx
//严重注意：状态必须通过setState进行更新,且更新是一种合并，不是替换。
setState({
    key: value,
    key: value,
    ...
})
```
