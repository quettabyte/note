## 组件属性 props

### props

* 每个组件对象都会有props属性
* 组件标签的所有属性都保存在props中
* 通过标签属性从组件外向组件内传递变化的数据
* props是只读的，在实例中不能修改

❗  ❗  ❗ 注意： 组件内部不要修改props数据

✔eg:

* 类组件

```jsx
// 组件中获取props
class Demo extends React.Component{
    render(){
        const {name,age,sex} = this.props
    }
}
const p = {name:'老刘',age:18,sex:'女'}
ReactDOM.render(<Person {...p}/>, document.getElementById('container'))
ReactDOM.render(<Person name="老刘" age={18} sex="男"/>, document.getElementById('container'))
```

* 函数组件

```jsx
function Person (props){
    const {name,age,sex} = props;
}
ReactDOM.render(<Person name="jerry" age={18} sex="女"/>,document.getElementById('container'))
```

### props类型限制与默认值

前提条件引入`prop-types`

```js
import myprop from 'prop-types'
```

* 只适用类组件

```jsx

class Demo extends React.Component{
    // 对标签属性进行类型、必要性的限制
    static propTypes = {
        name:myprop.string.isRequired, //限制name必传，且为字符串
        sex:myprop.string,//限制sex为字符串
        age:myprop.number,//限制age为数值
    }
    // 默认属性 不传参时使用默认属性
    static defaultProps = {
        sex:'男',//sex默认值为男
        age:18 //age默认值为18
    }
}
```

* 适用函数组件、类组件

```js
function Demo (props){
    return (JSX)
}
Demo.propTypes = {
    name:PropTypes.string.isRequired, //限制name必传，且为字符串
    sex:PropTypes.string,//限制sex为字符串
    age:PropTypes.number,//限制age为数值
}
//指定默认标签属性值
Demo.defaultProps = {
    sex:'男',//sex默认值为男
    age:18 //age默认值为18
}
```

