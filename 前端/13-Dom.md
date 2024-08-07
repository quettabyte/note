# DOM(Document Object Model)

文档对象模型（Document Object Model，简称DOM）

## Node属性

* `nodeName` 返回节点名称

* `parentNode` 返回当前节点的父级节点

* `childElementCount` 返回子元素的个数

* 返回所有子节点

  > var x = ul.children;
  >
  > 变量x是动态变化的，每次调用x都会重新获取子元素。

  `childNodes` ==包括#text==

  `children` （常用）

* 返回第一个子节点

  `firstChild` ==包括#text==

  `firstElementChild` （常用）

* 返回最后一个子节点

  `lastChild` ==包括#text==

  `lastElementChild` （常用）

* 返回上一个节点

  `previousSibling` ==包括#text==

  `previousElementSibling` （常用）

* 返回下一个节点

  `nextSibling` ==包括#text==

  `nextElementSibling` （常用）
  
* `innerHTML` 获取或替换所有子节点

* `innerText` 获取或替换所有文本

* `outerHTML` 获取或替换元素所有子节点（包含自身）

  

## Node方法

* `hasChildNodes()` 是否有子节点 ==包括#text==

  返回布尔值

  ```js
  var ulList = document.querySelector('ul');
  ulList.hasChildNodes();
  ```

* `appendChild()` 向当前节点末尾添加子节点

  ```js
  ulList.appendChild(newLi);
  ```

* `insertBefore()` 向指定位置添加节点

  两个参数：新节点和参照节点

  ```js
  ulList.insertBefore(newLi,li);
  ```

* `replaceChild()` 替换

  两个参数：新节点和被替换节点
  
  ```js
  ulList.replaceChild(newL,li);
  ```
  
* `removeChild()` 删除

  参数：要删除的节点

  ```js
  ulList.removeChild(li);
  ```

* `cloneNode()` 克隆节点

  参数：true深拷贝/false浅拷贝

  ```js
  var newThree=li.cloneNode();
  ```



## Document属性和方法

* `document.documentElement` 指向<html>元素
* `document.body` 指向<body>元素
* `document.title` 返回title元素中的文本，该属性可写
* `document.URL` 返回当前页面的网址
* `document.domain` 返回当前文档的域名
* 查找元素 <a href="./10-元素操作.md" target="_blank">10-元素操作 </a>
* `document.write()` 文档写入

* `document.createElement()` 创建元素

## 页面传值

```js
 var h2=document.getElementsByTagName('h2');
// 1. 通过获取?的索引 截取?后面的内容
var info=URL.slice(URL.lastIndexOf('?')+1);
// 2. 通过&字符将字符串分割为数组 split
var infoArr=info.split('&');
// 3. 遍历数组 将每一项通过=分隔
for(var i=0;i<infoArr.length;i++){
    var newArr=infoArr[i].split('=');
    switch(newArr[0]){
        case 'name': h2[0].innerHTML=newArr[1]; break;
        case 'age': h2[1].innerHTML=newArr[1]; break;
        case 'like': h2[2].innerHTML=newArr[1]; break;
        default:break;
    }
}
```

**获取url里key的value**

```js
function getValue(key){
    // 调用函数执行代码
    var URL=document.URL;
    var index=URL.indexOf('?');
    var newUrl=URL.slice(index+1);
    var arr=newUrl.split('&');
    for(var i=0;i<arr.length;i++){
        var newArr=arr[i].split('=');
        if(newArr[0]===key){
            return newArr[1];
        }
    }
}
```



## 元素属性方法

* `setAttribute('属性名','属性值')` 为元素新增属性,或设置当前属性

  ```js
  img.setAttribute('src','https://xxxx')ｄｆｄｆｄｆｄｓａ
  ```

  >点语法设置属性 只能体现标签本身有的属性
  >
  >`setAttribute`设置属性 标签本身有的属性和自定义属性都可以体现
  >
  >`div.index='hello';` 这个不能实现，因为是自定义属性
  >
  >`div.setAttribute('index1','hello');`

* `removeAttribute('属性名')` 删除元素属性

  ```js
  div.setAttribute('index','hello');
  div.removeAttribute('index');
  ```

* `getAttribute('属性名')` 获取元素属性

  ```js
  div.getAttribute('index');
  ```



## 元素大小方法

**offset**

* `offsetLeft` 元素左外边框距离左边的距离

* `offsetTop` 元素上外边框距离上边的距离

* `offsetHeight` 元素垂直方向所占空间

  height + padding + border的高度 ==和子元素大小无关==

* `offsetWidth` 元素水平方向所占空间

  同上

**client**

height + padding 的高度  ==和子元素大小无关==

* `clientHeight` 元素垂直方向所占空间
* `clentWidth` 元素水平方向所占空间

**scroll**

子元素不大于父元素的情况下和 `client` 相同 ==和子元素大小有关==

子元素大于父元素：子元素大小 + padding + border + margin 2

* `scrollHeight` 元素垂直方向所占空间
* `scrollWidth` 元素水平方向所占空间
* `scrollLeft` 有滚动条的情况设置或者获取向左滚动的距离
* `scrollTop` 有滚动条的情况设置或者获取向上滚动的距离



## 获取宽高

Dom是文档对象模型

document对象是文档的根节点

element代表元素节点，代表页面中的一个个标签

***

* 获取视口宽度/高度 ==（可见区域，不包括滚动条）==

  `document.documentElement.clientHeight`

  `document.documentElement.clientWidth`

* 获取页面内容高度 ==Body对象==

  `document.body.clientHeight`

* 获取垂直滚动条向下滚动的距离
  `document.documentElement.scrollTop`

* 获取水平滚动条向右滚动的距离

  `document.documentElement.scrollLeft`

* 返回串口的文档显示区的宽度/高度 ==（包括滚动条）==

  `window.innerWidth`

  `window.innerHeight`







