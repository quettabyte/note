## 虚拟DOM📚

1. 本质是Object类型的对象（一般对象）。
2. 虚拟DOM比较“轻”，真实DOM比较“重”，因为虚拟DOM是React内部再用，无需真实DOM上那么多的属性。
3. 虚拟DOM最终会被React转化为真是DOM，呈现在页面上。

### 创建虚拟DOM

1. JSX创建

   ```jsx
   const VDOM = ( /* 此处一定不要写引号，因为不是字符串 */
       <h1 id="title">
           <span>Hello,React</span>
       </h1>
   ) 
   ```

2. JS创建

   ```js
   const VDOM = React.createElement('h1',{id:'title'},React.createElement('span',{},'Hello,React'));
   ```

3. 总结

   使用JS创建虚拟DOM过程十分繁琐，尤其当页面结构嵌套较深的时候。使用JSX创建却十分简单，就像写HTML语言那样可以直接写标签。但是最终计算机还是执行的JS那样的语句，原因是JSX会被`babel`编译成JS的语句。

