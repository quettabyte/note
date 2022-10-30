## 组件📚

理解☑：

用来实现局部功能的代码和资源的集合（html css js image video等等）

作用🥇：

复用编码, 简化项目编码, 提高运行效率

### React面向组件编程

React开发者工具调试：React Developer Tools

❗ ❗ ❗组件需要注意的地方：

1. 组件名必须首字母大写
2. 虚拟DOM元素只能有一个根元素
3. 虚拟DOM元素必须有结束标签

### 函数组件

创建🎨

```jsx
function MyComponent(){
    console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
    return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
}
```

渲染流程🔁

1. React解析组件标签，找到了MyComponent组件。
2. 2.发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中。

### 类组件

创建🎨

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

渲染流程🔁

1. React解析组件标签，找到了MyComponent组件。
2. 发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
3. 将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。





