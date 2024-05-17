## React组件间通信📚

### 父子组件（props）

1.  传递数据（父传子）与传递方法（子传父）

    ```jsx
    //----------父组件--------------
    //render:
    <Child name="tom" age=18 /> 

    //----------子组件--------------
    const {name,age} = this.props
    //render:
    <span>name:{name}</span>
    <span>age:{age}</span>
    ```

    *   子组件向父组件传递数据需要父组件先给子组件传递一个方法

    ```jsx
    //----------父组件--------------
    state = {name:'tom'}
    this.changeName = (name) => {
      this.setState({name})
    }
    //render:
    <Child changeName={this.changeName} />

    //----------子组件--------------
    this.changeName = () => {
      this.props.changeName("jack");
    }
    //render:
    <button onClick={this.changeName}>点我改父组件中的名字为jack</button>
    ```

2.  `ref`标记（父组件拿到子组件的引用，从而调用子组件的方法）

```js
// 在父组件中清除子组件的input输入框的value值
this.refs.form.reset()
```

### 状态提升

**中间人模式**

> 就是使用父子组件通信的方式实现，将数据放在它们共同的祖组件，然后通过props来实现

📌应用场景：

*   亲兄弟组件通信可以考虑使用这种方式，

*   当层级嵌套比较多的时候，不要使用这种方式。

### 发布订阅模式实现

> 订阅一般放在 `componentDidMount`里

```js
let bus = {
    list: [], // 存放订阅者
    // 订阅者
    subscribe(cb) {
        this.list.push(cb);
    },
    // 发布者
    publish(data) {
        this.list.forEach(cb => cb && cb(data));
    }
}
```

*   订阅

```js
componentDidMount(){
  bus.subscribe((data) => {
    this.setState({
      text: data
    })
  })
}
```

*   发布

```js
bus.publish("Hello")
```

### context状态树传参

> 应用生产者、消费者模式

*   先定义全局 `context` 对象

```js
const GlobalContext = React.createContext()
```

*   生产者

```jsx
<GlobalContext.Provider value={{
    call: "打电话",
    sms: "短信",
    info:this.state.info,
		changeInfo: (value) => {
      this.setState({
        info: value
      })
    }  
  }}>
  <Child>
  	<Grandson />
  </Child>
</GlobalContext.Provider>
```

*   消费者 `Child` 组件和 `Grandson` 组件都可以作为消费者

```jsx
// Grandson组件
render() {
  return (
  	<GlobalContext.Consumer>
      value => {
        return <div>value.call</div>
      }
    </GlobalContext.Consumer>
  )
}
```

### 总结

**几种通信方式**

    1.props：
    	(1).children props
    	(2).render props
    2.消息订阅-发布：
    	pubs-sub、event等等
    3.集中式管理：
    	redux、dva等等
    4.conText:
    	生产者-消费者模式

**比较好的搭配方式**

    父子组件：props
    兄弟组件：消息订阅-发布、集中式管理
    祖孙组件(跨级组件)：消息订阅-发布、集中式管理、conText(开发用的少，封装插件用的多)

