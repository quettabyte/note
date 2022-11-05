## React扩展📚

### setState

对象式： `setState(stateChange, [callback])`

* `stateChange` 为状态改变对象(该对象可以体现出状态的更改)
* `callback` 是可选的回调函数, 它在状态更新完毕、界面也更新后( `render` 调用后)才被调用

函数式：`setState(updater, [callback])`

* `updater`为返回 `stateChange` 对象的函数。
* `updater` 可以接收到 `state` 和 `props` 。
* `callback`同上

**总结**

1. 对象式的 `setState` 是函数式的 `setState` 的简写方式(语法糖)
2. 使用原则
   * 如果新状态不依赖于原状态 ==> 使用对象方式
   * 如果新状态依赖于原状态 ==> 使用函数方式
   * 如果需要在 `setState()` 执行后获取最新的状态数据, 要在第二个 `callback` 函数中读取

### 样式的模块化

```css
.title {
  background-color: skyblue;
}
```

```jsx
import hello from './index.module.css'
export default class Hello extends Component{
  render(){
    return <h2 className={hello.title}>Hello,React!</h2>
  }
}
```

### lazyLoad(懒加载)

> 通过React的lazy函数配合import()函数动态加载路由组件
>
> 路由组件代码会被分开打包

```jsx
import React, { Component,lazy,Suspense} from 'react'
//使用懒加载必须有一个loading组件，网络慢的时候会显示
import Loading from './Loading'
// 需要懒加载的组件的引入方式
const Home = lazy(()=> import('./Home') ) 
const About = lazy(()=> import('./About'))
```

```jsx
//2.通过<Suspense>指定在加载得到路由打包文件前显示一个自定义loading界面
<Suspense fallback={<Loading/>}>
  {/* 注册路由 */}
  <Route path="/about" component={About}/>
  <Route path="/home" component={Home}/>
</Suspense>
```

### Fragment

> 有时候不想在最外层套一个`Fragment`标签。在React转成真实DOM的时候会自动丢掉
>
> 可以不用必须有一个真实的DOM根标签了

```jsx
import React, { Component,Fragment } from 'react'
//------------
render() {
  return (
    <Fragment key={1}>
      <input type="text"/>
      <input type="text"/>
    </Fragment>
  )
}
```

### Context

```
1) 创建Context容器对象：
	const XxxContext = React.createContext()  
	
2) 渲染子组时，外面包裹xxxContext.Provider, 通过value属性给后代组件传递数据：
	<xxxContext.Provider value={数据}>
		子组件
    </xxxContext.Provider>
    
3) 后代组件读取数据：

	//第一种方式:仅适用于类组件 
	  static contextType = xxxContext  // 声明接收context
	  this.context // 读取context中的value数据
	  
	//第二种方式: 函数组件与类组件都可以
	  <xxxContext.Consumer>
	    {
	      value => ( // value就是context中的value数据
	        要显示的内容
	      )
	    }
	  </xxxContext.Consumer>
```

### 组件优化

**Component的2个问题**

> 1. 只要执行 `setState()` ,即使不改变状态数据, 组件也会重新 `render()` ==> 效率低
>
> 2. 只当前组件重新 `render()` , 就会自动重新 `render` 子组件，纵使子组件没有用到父组件的任何数据 ==> 效率低

**效率高的做法**

>  只有当组件的 `state` 或 `props` 数据发生改变时才重新 `render()`

**原因**

>  Component中的 `shouldComponentUpdate()` 总是返回true

**解决**

办法1: 

* 重写 `shouldComponentUpdate()` 方法
* 比较新旧 `state` 或 `props` 数据, 如果有变化才返回 `true` , 如果没有返回 `false`

办法2: 

* 使用 `PureComponent`

* `PureComponent` 重写了`shouldComponentUpdate()` , 只有 `state` 或 `props` 数据有变化才返回 `true`

注意: 

* 只是进行 `state` 和 `props` 数据的浅比较, 如果只是数据对象内部数据变了, 返回 `false`
* 不要直接修改 `state` 数据, 而是要产生新数据
* 项目中一般使用 `PureComponent` 来优化

```jsx
import React, { PureComponent } from 'react'
//方法1
shouldComponentUpdate(nextProps,nextState){
		return !this.state.carName === nextState.carName
}
//方法2
export default class Parent extends PureComponent
```

### render props(插槽)

**如何向组件内部动态传入带内容的结构(标签)?**

* Vue中：

  使用slot技术, 也就是通过组件标签体传入结构 `<A><B/></A>`

* React中：

  * 使用children props: 通过组件标签体传入结构
  * 使用render props: 通过组件标签属性传入结构,而且可以携带数据，一般用render函数属性

**children props**

```jsx
<A>
  <B>xxxx</B>
</A>
{this.props.children}
```

弊端：A组件不能给B组件传数据

**render props**

```jsx
<A render={(data) => <C data={data}></C>}></A>
//A组件: 
{this.props.render(内部state数据)}
//C组件: 
读取A组件传入的数据显示 {this.props.data} 
```

### 错误边界

> 错误边界(Error boundary)：用来捕获后代组件错误，渲染出备用页面

只能捕获后代组件生命周期产生的错误，不能捕获自己组件产生的错误和其他组件在合成事件、定时器中产生的错误

**使用方式：**

`getDerivedStateFromError` 配合 `componentDidCatch`

```jsx
// 生命周期函数，一旦后台组件报错，就会触发
static getDerivedStateFromError(error) {
    console.log(error);
    // 在render之前触发
    // 返回新的state
    return {
        hasError: true,
    };
}

componentDidCatch(error, info) {
    // 统计页面的错误。发送请求发送到后台去
    console.log(error, info);
}
```

