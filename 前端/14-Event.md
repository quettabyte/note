# 事件对象（Event）

***

## UI事件

不一定与用户操作有关的事件

***

* `load` 当页面完全加载后再window上触发（图片也可以触发load事件）

* `resize` 当窗口大小变化时在window上触发

  > 除火狐外其他浏览器会在浏览器窗口变化1像素时就触发事件，而火狐是在用户停止调整窗口大小时才触发。不建议在此事件中加入大计算量代码，因为可能频繁执行，导致浏览器速度变慢。另外窗口最大最小化也会触发事件

* `scroll` 当用户滚动带滚动条的元素中的内容时，在该元素上面触发



## 鼠标事件

### 事件类型

| 事件          | 描述     |
| ------------- | -------- |
| onclick       | 单击     |
| ondblclick    | 双击     |
| oncontextmenu | 右键菜单 |
| onmouseover   | 移入     |
| onmouseout    | 移出     |
| onmouseenter  | 移入     |
| onmouseleave  | 移出     |
| onmousedown   | 按下     |
| onmouseup     | 抬起     |
| onmousemove   | 移动     |

* `over` 与 `out` 在指针移入自己或自己子元素都会触发

### 坐标位置

* 在屏幕中的坐标

  `screenX`  `screenY` 

* 表示事件发生时鼠标指针在视口中的坐标==不包含滚动距离== 相对于body

  `clientX` `clientY` 

* 在页面中的坐标==包含滚动距离（不兼容IE）== 相对于body

  `pageX` `pageY`

* 获取点击目标的坐标==有兼容性==

  `offsetX`  `offsetY` 

### 案例

[拖拽](https://gitee.com/cokeice/study_web/blob/master/javascript/05-BOM,EVENT/02-拖拽.html)、[蒙版图](https://gitee.com/cokeice/study_web/blob/master/javascript/05-BOM,EVENT/03-蒙版图.html)、[放大镜](https://gitee.com/cokeice/study_web/blob/master/javascript/05-BOM,EVENT/04-放大镜.html)

## 键盘事件

* `keydown` 当用户按下键盘上的任意键时触发，按住不动将重复触发

* `keyup` 当用户释放键盘上的键时触发

   `event.keyCode`键码

* `keypress` 当用户按下键盘上的字符键时触发，按住不懂将重复触发

  `event.charCode `键码（ASCII编码形式展示，需通过`String.formCharCode()`方法转换，IE9以下不支持）

* `shiftkey`、`altkey`、`ctrlkey`

* [案例](https://www.gitee.com/cokeice/study_web/blob/master/javascript/05-BOM,EVENT/05-键盘.html)、[按键游戏](https://gitee.com/cokeice/study_web/blob/master/javascript/05-BOM,EVENT/07-按键游戏.html)、[弹方块](https://gitee.com/cokeice/study_web/blob/master/javascript/05-BOM,EVENT/08-弹方块.html)

## 表单事件

* `focus` 元素获得焦点时触发
* `blur` 元素失去焦点时触发
* `change` 内容被修改并且失去焦点时才会触发
* `input` 输入框中内容一旦有变化就触发
* `submit()` 提交表单



## 阻止默认

* `preventDefault()`

  ```js
  // 阻止默认
  window.oncontextmenu = function (e) {
      e.preventDefault();
  }
  ```



## 阻止冒泡

* `stopPropagation()`

  ```js
  document.querySelector("button").onclick = function (e) {
      // 阻止冒泡
      e.stopPropagation();
      console.log("button");
  }
  document.body.onclick = function (e) {
      // 阻止冒泡
      // e.stopPropagation();
      console.log("body");
  }
  window.onclick = function (e) {
      console.log("window");
  }
  ```




## 移动端事件

* `ontouchstart` 手指按下
* `ontouchend` 手指松开
* `ontouchmove` 手指移动

```js
window.ontouchstart = function(e) {
    // e.touches  类数组集合
    // 判断 几根手指在屏幕上
    var touch = e.touches[]
}
```

**手指拖拽dHero**

```js
dHero.ontouchstart = function (e) {
    // 组织默认 屏幕不会随鼠标晃动
    e.preventDefault();
    if (e.touches.length > 1) {
        return;
    }
    // 获取手指
    var touch = e.touches[0];
    var x = touch.pageX;
    var y = touch.pageY;
    var l = dHero.offsetLeft;
    var t = dHero.offsetTop;
    window.ontouchmove = function (e2) {
        var touch2 = e2.touches[0];
        dHero.style.left = touch2.pageX - x + l + 'px';
        dHero.style.top = touch2.pageY - y + t + 'px';
    }
}
```



## 练习

[飞机大战](https://gitee.com/cokeice/study_web/tree/master/javascript/06-练习/01-飞机大战)











