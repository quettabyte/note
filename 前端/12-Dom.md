# 

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

**通过url获取的信息展示在页面上**

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

* 查找元素 <a href="./10-元素操作.md">10-元素操作 </a>

* `document.write()` 文档写入

* `document.createElement()` 创建元素







## 元素属性方法











## 元素大小方法





















