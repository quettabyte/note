

## 属性

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

  

## 方法

* `hasChildNodes()` 是否有子节点 ==包括#text==

  返回布尔值

  ```js
  var ulList = document.querySelector('ul');
  ulList.hasChildNodes();
  ```

* `document.createElement('节点名称')` 创建节点

  ```js
  var newLi = document.createElement('li');
  ```

* `appendChild()` 向当前节点末尾添加子节点

  ```js
  ulList.appendChild(newLi);
  ```

* `insertBefore()` 向指定位置添加节点

  两个参数：新节点和参照节点

* d

* d

* d

* d

* d

* d











