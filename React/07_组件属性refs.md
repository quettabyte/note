## 组件属性 refs📚

下面是几个适合使用 refs 的情况：

* 管理焦点，文本📄选择或媒体🎵播放。
* 触发强制动画动画。
* 集成第三方 DOM 库。

### createRef API 方法

最新的官方推荐使用的方法✅（ 版本 ≥ v16.3）

```jsx
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

```jsx
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

```jsx
saveInput = (c) => {
    this.myinput = c;
}
<input ref = {this.saveInput} type="text"/>
```

### 字符串形式refs

过时并可能会在未来版本中移除❎

```jsx
eventFunc = () => {
    const {myinput} = this.refs;
    console.log(myinput.value);
}
<input ref="myinput" onBlur={this.showData} type="text" placeholder="失去焦点提示数据"/>
```



