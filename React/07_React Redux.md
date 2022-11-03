## React Redux📚

### React-Redux基本概念

<img src="..\..\img\react-redux-yuanli.jpg" style="zoom:50%;" />

1. ✔明确两个概念：
   * UI组件:不能使用任何redux的api，只负责页面的呈现、交互等。
   * 容器组件：负责和redux通信，将结果交给UI组件。

2. 🎨如何创建一个容器组件————靠react-redux 的 `connect` 函数
   * connect(mapStateToProps,mapDispatchToProps)(UI组件)
     * -mapStateToProps:映射状态，返回值是一个对象
     * -mapDispatchToProps:映射操作状态的方法，返回值是一个对象

3. 🎯备注：
   * 容器组件中的 `store` 是靠 `props` 传进去的，而不是在容器组件中直接引入
   * `mapDispatchToProps` ，也可以是一个对象（简写）
   * 使用了react-redux后也不用再自己检测redux中状态的改变了，容器组件可以自动完成这个工作。

### react-redux基础写法

> 做项目的时候不这么写，会使用简写，但是这个基础写法更容易看懂其中的逻辑与各个文件间的关系

```shell
npm install react-redux
```

1. UI组件

   📂`components`

   ```jsx
   import React, { Component } from 'react'
   export default class Count extends Component {
   	increment = ()=>{ //加法
   		const {value} = this.selectNumber
   		this.props.jia(value*1)
   	}
   	decrement = ()=>{ //减法
   		const {value} = this.selectNumber
   		this.props.jian(value*1)
   	}
   	incrementIfOdd = ()=>{ //奇数再加
   		const {value} = this.selectNumber
   		if(this.props.count % 2 !== 0){
   			this.props.jia(value*1)
   		}
   	}
   	incrementAsync = ()=>{ //异步加
   		const {value} = this.selectNumber
   		this.props.jiaAsync(value*1,500)
   	}
   	render() {
   		return (
   			<div>
   				<h1>当前求和为：{this.props.count}</h1>
   				<select ref={c => this.selectNumber = c}>
   					<option value="1">1</option>
   					<option value="2">2</option>
   					<option value="3">3</option>
   				</select>&nbsp;
   				<button onClick={this.increment}>+</button>&nbsp;
   				<button onClick={this.decrement}>-</button>&nbsp;
   				<button onClick={this.incrementIfOdd}>当前求和为奇数再加</button>&nbsp;
   				<button onClick={this.incrementAsync}>异步加</button>&nbsp;
   			</div>
   		)
   	}
   }
   ```

2. 容器组件

   📂`container`

   ```js
   //引入Count的UI组件
   import CountUI from '../../components/Count'
   //引入action
   import {
   	createIncrementAction,
   	createDecrementAction,
   	createIncrementAsyncAction
   } from '../../redux/count_action'
   
   //引入connect用于连接UI组件与redux
   import {connect} from 'react-redux'
   
   /* 
   	1.mapStateToProps函数返回的是一个对象；
   	2.返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value
   	3.mapStateToProps用于传递状态
   */
   function mapStateToProps(state){
   	return {count:state}
   }
   
   /* 
   	1.mapDispatchToProps函数返回的是一个对象；
   	2.返回的对象中的key就作为传递给UI组件props的key,value就作为传递给UI组件props的value
   	3.mapDispatchToProps用于传递操作状态的方法
   */
   function mapDispatchToProps(dispatch){
   	return {
   		jia:number => dispatch(createIncrementAction(number)),
   		jian:number => dispatch(createDecrementAction(number)),
   		jiaAsync:(number,time) => dispatch(createIncrementAsyncAction(number,time)),
   	}
   }
   
   //使用connect()()创建并暴露一个Count的容器组件
   export default connect(mapStateToProps,mapDispatchToProps)(CountUI)
   ```

3. 📄`store.js`

   ```js
   /* 
   	该文件专门用于暴露一个store对象，整个应用只有一个store对象
   */
   
   //引入createStore，专门用于创建redux中最为核心的store对象
   import {createStore,applyMiddleware} from 'redux'
   //引入为Count组件服务的reducer
   import countReducer from './count_reducer'
   //引入redux-thunk，用于支持异步action
   import thunk from 'redux-thunk'
   //暴露store
   export default createStore(countReducer,applyMiddleware(thunk))
   ```

4. 📄`countant.js`定义action对象中type类型的常量值

   ```js
   /* 
   	该模块是用于定义action对象中type类型的常量值，目的只有一个：便于管理的同时防止程序员单词写错
   */
   export const INCREMENT = 'increment'
   export const DECREMENT = 'decrement'
   ```

5. 📄`count_action.js` 创建action对象用的

   ```js
   /* 
   	该文件专门为Count组件生成action对象
   */
   import {INCREMENT,DECREMENT} from './constant'
   
   //同步action，就是指action的值为Object类型的一般对象
   export const createIncrementAction = data => ({type:INCREMENT,data})
   export const createDecrementAction = data => ({type:DECREMENT,data})
   
   //异步action，就是指action的值为函数,异步action中一般都会调用同步action，异步action不是必须要用的。
   export const createIncrementAsyncAction = (data,time) => {
   	return (dispatch)=>{
   		setTimeout(()=>{
   			dispatch(createIncrementAction(data))
   		},time)
   	}
   }
   ```

6. 📄`count_reducer.js` 创建一个为Count组件服务的reducer

   ```js
   /* 
   	1.该文件是用于创建一个为Count组件服务的reducer，reducer的本质就是一个函数
   	2.reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)
   */
   import {INCREMENT,DECREMENT} from './constant'
   
   const initState = 0 //初始化状态
   export default function countReducer(preState=initState,action){
   	// console.log(preState);
   	//从action对象中获取：type、data
   	const {type,data} = action
   	//根据type决定如何加工数据
   	switch (type) {
   		case INCREMENT: //如果是加
   			return preState + data
   		case DECREMENT: //若果是减
   			return preState - data
   		default:
   			return preState
   	}
   }
   ```

7. 📄`App.jsx` 渲染这个组件到页面

   ```jsx
   import React, { Component } from 'react'
   import Count from './containers/Count'
   import store from './redux/store'
   
   export default class App extends Component {
   	render() {
   		return (
   			<div>
   				{/* 给容器组件传递store */}
   				<Count store={store} />
   			</div>
   		)
   	}
   }
   ```

### react-redux简写

> 需要配合上面的基础写法来看

1. 将UI组件与容器组件整合成一个文件

   📂`container`

   ```jsx
   //导入所需的模块
   import ....
   
   //UI组件放在这里，不需要默认导出，因为只有容器组件需要调用
   class Count extends Component {
       ...;
   }
   
   //容器组件放在这
   ```

2. 容器组件中 `connect` 中给UI组件传递的 `props` 简写

   ```jsx
   //引入action
   
   //基础版看上面的基础写法
   export default connect(
       state => ({key:value}), //映射状态
       {key:xxxxxAction} //映射操作状态的方法
       // 尽量触发对象的简写形式
   )(UI组件)
   ```

3. 把所有组件的 `reducer` 文件放在📂`src/redux/reducers`下

4. 把所有组件的 `action` 文件放在📂`src/redux/actions`下

5. 在📂`reducers`下新建📄`index.js`文件专门用于汇总并暴露所有的 `reducer`

   ```js
   //引入combineReducers，用于汇总多个reducer
   import {combineReducers} from 'redux'
   //引入为各个组件服务的reducer
   
   //汇总所有的reducer变为一个总的reducer
   //key1和key2就是以后从redux中获取各个组件共享状态的key值
   export default  combineReducers({
   	key1:reducer1,
   	key2:reducer2
   })
   ```

6. 无需自己给容器组件传递 `store` ，给`<App/>`包裹一个`<Provider store={store}>`即可

   ```jsx
   import store from './redux/store'
   import {Provider} from 'react-redux'
   root.render(
   	/* 此处需要用Provider包裹App，目的是让App所有的后代容器组件都能接收到store */
   	<Provider store={store}>
   		<App/>
   	</Provider>
   )
   ```

7. **一个组件要和redux“打交道”要经过哪几步？**

   * 定义好UI组件---不暴露

   * 引入 `connect` 生成一个容器组件，并暴露，写法如下：

     ```js
     connect(
         state => ({key:value}), //映射状态
         {key:xxxxxAction} //映射操作状态的方法
     )(UI组件)
     ```

   * 在UI组件中通过`this.props.xxxxxxx`读取和操作状态

### redux数据共享

需要共享的容器组件的 `reducer` 要使用 `combineReducers` 进行合并，合并后的总状态是一个对象！！！

具体写法看上面`<react-redux简写>`中的`5.`

### react-redux开发者工具的使用

```shell
npm install --save-dev redux-devtools-extension
```

⚙ `store` 中进行配置 

	📄`store.js`

```js
import {composeWithDevTools} from 'redux-devtools-extension'
const store = createStore(allReducer,composeWithDevTools(applyMiddleware(thunk)))
```

💦浏览器中安装 `Redux DevTools`
