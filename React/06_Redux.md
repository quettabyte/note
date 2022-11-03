## Redux📚

### Redux工作流程

<img src="..\..\img\redux-yuanli.jpg" style="zoom:50%;" />

客户-服务员-老板-厨师🤣

### redux精简版

1. 去除Count组件自身的状态

2. 📂`src`下建立:

   * -redux

   * -store.js
   * -count_reducer.js

3. 📄`store.js`：

   * 引入redux中的 `createStore` 函数，创建一个 `store`
   * `createStore` 调用时要传入一个为其服务的 `reducer`
   * 记得暴露 `store` 对象

   ```js
   /* 该文件专门用于暴露一个store对象，整个应用只有一个store对象*/
   //引入createStore，专门用于创建redux中最为核心的store对象
   import {createStore} from 'redux'
   //引入为Count组件服务的reducer
   import countReducer from './count_reducer'
   //暴露store
   export default createStore(countReducer)
   ```

4. 📄`count_reducer.js`：

   * `reducer` 的本质是一个函数，接收：`preState` , `action` ，返回加工后的状态
   * `reducer` 有两个作用：初始化状态，加工状态
   * `reducer` 被第一次调用时，是 `store` 自动触发的，
     * 传递的 `preState` 是undefined,
     * 传递的 `action` 是:{type:'@@REDUX/INIT_a.2.b.4}

   ```js
   /* 
   	1.该文件是用于创建一个为Count组件服务的reducer，reducer的本质就是一个函数
   	2.reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)
   */
   const initState = 0 //初始化状态
   export default function countReducer(preState=initState,action){
   	// console.log(preState);
   	//从action对象中获取：type、data
   	const {type,data} = action
   	//根据type决定如何加工数据
   	switch (type) {
   		case 'increment': //如果是加
   			return preState + data
   		case 'decrement': //若果是减
   			return preState - data
   		default:
   			return preState
   	}
   }
   ```

5. 在📄`index.js`中监测 `store` 中状态的改变，一旦发生改变重新渲染`<App/>`

   * 📜备注：redux只负责管理状态，至于状态的改变驱动着页面的展示，要靠我们自己写。
   * 💢注意：react-redux中不需要监听，因为创建容器组件会自动监测 `store` 中的状态，从而调 `render`

   ```js
   const myroot = document.getElementById('root')
   const root = ReactDOM.createRoot(myroot)
   root.render(<App />)
   store.subscribe(()=>{
   	root.render(<App />)
   })
   ```

### redux 完整版

与精简版的主要区别🧩：（新增文件）

* 📄`count_action.js` 专门用于创建 `action` 对象
* 📄`constant.js` 放置容易写错的 `type` 值

```js
// 该文件专门为Count组件生成action对象
import {INCREMENT,DECREMENT} from './constant'
export const increment = data => ({type:INCREMENT,data})
export const decrement = data => ({type:DECREMENT,data})
//-----------------分隔线-----------------
// 该模块是用于定义action对象中type类型的常量值，
// 目的只有一个：便于管理的同时防止程序员单词写错
export const INCREMENT = 'increment'
export const DECREMENT = 'decrement'
```

### 异步action

1. ✔明确：延迟的动作不想交给组件自身，想交给 `action`

2. 何时需要异步 `action`：想要对状态进行操作，但是具体的数据靠异步任务返回。

3. ✒具体编码：
   1. `npm install redux-thunk -save`，并配置在store中

      ```shell
      npm install redux-thunk -save
      ```

   2. 创建action的函数不再返回一般对象，而是一个函数，该函数中写异步任务。

      ```js
      //异步action，就是指action的值为函数,
      //异步action中一般都会调用同步action，异步action不是必须要用的。
      export const createIncrementAsyncAction = (data,time) => {
      	return (dispatch)=>{
      		setTimeout(()=>{
      			dispatch(createIncrementAction(data))
      		},time)
      	}
      }
      //-----------------分隔线-----------------
      import {createStore,applyMiddleware} from 'redux'
      import thunk from 'redux-thunk' //引入redux-thunk，用于支持异步action
      export default createStore(reducer,applyMiddleware(thunk))
      ```

   3. 异步任务有结果后，分发一个同步的action去真正操作数据。

4. 🎯备注：异步action不是必须要写的，完全可以自己等待异步任务的结果了再去分发同步action。