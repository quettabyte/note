## JavaScript XML📚

react定义的一种类似于XML的JS扩展语法：JS+XML本质是`React.createElement(component, props, ...children)`方法的语法糖

✅语法规则

1. 定义虚拟DOM时，不要写引号。
2. 标签中混入JS表达式时要用{}。
3. 样式的类名指定不要用class，要用className。
4. 内联样式，要用style={{key: value}}的形式去写。
5. 只有一个根标签
6. 标签必须闭合
7. 标签首字母
   * 若小写字母开头，则将该标签转为html中同名元素，若html中无该标签对应的同名元素，则报错
   * 若大写字母开头，react就去渲染对应的组件，若组件没有定义，则报错

✔eg:

```jsx
const myId = 'cokeice';
const myData = 'HeLlo,rEaCt';
const VDOM = (
    <div>
        <h2 className="title" id={myId.toLowerCase()}>
            <span style={{ color: 'white' }}>{myData.toLowerCase()}</span>
        </h2>
        <h2 className="title" id={myId.toUpperCase()}>
            <span style={{ color: 'white' }}>{myData.toLowerCase()}</span>
        </h2>
        <input type="text" />
    </div>
)
```

❗ ❗ ❗ 注意区分：【js语句(代码) 】与【js表达式】

1. 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方

   下面这些都是表达式：

   (1). a

   (2). a+b

   (3). demo(1)

   (4). arr.map() 

   (5). function test () {}

2. 语句(代码)：

   下面这些都是语句(代码)：

   (1). if(){}

   (2). for(){}

   (3). switch(){case:xxxx}
