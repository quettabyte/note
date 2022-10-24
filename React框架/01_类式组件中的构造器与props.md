# 01_类式组件中的构造器与props

首先说以下结论😇：类中的constructor能省略就省略

### 官网写法🤩

```js
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

### 写构造器函数有什么用？

1. 通过给this.state赋值对象来初始化内部state

2. 为事件处理函数绑定实例（如果不绑定实例，会在触发事件响应函数时丢失this）

   eg：

   ```jsx
   export class Demo extends Component {
       constructor(props) {
           super(props);
           // 不要在这里调用 this.setState()
           this.state = { counter: 0 };
           this.handleClick = this.handleClick.bind(this);
   	}
       function handleClick(){ // 此函数放在原型上
           // 事件处理
       }
       render() {
           return <div onClick={this.handleClick}>demo</div>
       }
   }
   
   export default Demo
   ```

但是😊，上面两种作用有更好的解决办法（简写）

初始化state可以直接在类里写赋值语句来初始化；事件处理函数防止丢失this可以使用箭头函数，所以可以省略`constructor`

eg:

```jsx
export class Demo extends Component {
    state = {
        // 初始化state
        opan: 0
    }
    handleClick = () => { //此函数放在实例上
        // 事件处理
    }
    render() {
        return <div onClick={this.handleClick}>demo</div>
    }
}

export default Demo
```

### 构造函数中super(props)传参问题

> 官网是这么说的：在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前调用 `super(props)`。否则，`this.props` 在构造函数中可能会出现未定义的 bug。

意思是，如果这样写

```js
constructor() {
    console.log(this.props);
}
```

打印结果就是`undefined`，如果你希望在constructor里使用this.props就必须调用 `super(props)`。

构造器是否接受props，是否传递给super，取决与：是否希望在构造器中通过this访问props。

参考：官网[React.Component – React (reactjs.org)](https://zh-hans.reactjs.org/docs/react-component.html#constructor)











