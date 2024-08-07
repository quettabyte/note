# DOM(Browser Object Model)

浏览器对象模型（Browser Object Model，简称BOM）

## window对象

### 窗口位置

* 返回浏览器窗口左上角相对于当前屏幕左上角的`水平`/`垂直`距离，不兼容FF浏览器

  `screenLeft`/`screenTop`

* 功能同上，兼容FF

  `screenX`/`screenY`

### 窗口大小

IE9以下不兼容

* 返回网页在当前窗口中可见部分的`宽度`/`高度`，包含滚动条`宽度`/`高度`

  `outerWidth`/`outerHeight`

* 返回浏览器窗口`宽度`/`高度`，包含浏览器菜单和边框

### 打开窗口

* 打开

  `window.open()` 

  打开一个新的浏览器窗口，接受四个参数

  参数：URL/打开方式/窗口参数/是否取代当前页面历史记录的布尔值

  常见网页内的聊天框

  ```js
  window.open("www.baidu.com","_blank","width=500,height=500",true);
  ```

* 关闭

  `window.close()`

  只能关闭`open()`打开的窗口



## window子对象

### screen对象

**功能**：包含显示设备的信息

**属性列举**

* `screen.height`/`screen.width`

  返回设备的分辨率

* `screen.avaiWidth`/`screen.avaiHeight`

  返回屏幕可用宽高，值为屏幕的实际大小减去操作系统某些功能占据的空间，如系统任务栏

### location对象

**功能**：保存当前文档信息、将URL解析为独立片段

**属性**

| 属性     | 描述                                              |
| -------- | ------------------------------------------------- |
| href     | 返回当前页面完整的URL、修改这个属性，即跳转新页面 |
| hash     | 返回URL中的hash（#号后跟零或多个字符）            |
| host     | 返回服务器名称和端口号                            |
| port     | 返回服务器端口号                                  |
| pathname | 返回URL中的目录和文件名                           |
| hostname | 返回不带端口号的服务器名称                        |
| protocol | 返回页面使用的协议（http://或https://）           |
| search   | 返回URL的查询字符串，字符串以问号开头             |

* `href`

  ```js
  // 跳转新页面
  btnl.onclick = function() {
      location.href = "test.html";
  }
  ```

### navigator对象

**功能**：提供一系列属性用于检测浏览器

**属性**

* `onLine` 是否联网
* `userAgent` 浏览器嗅探 判断浏览器的很多信息

### history对象

**功能**：保存用户上网的历史记录

**方法、属性：**

* `go()` 在用户历史记录中任意跳转，接受一个参数，表示前后跳转页数的整数值（后退一页-1，前进一页1），也可传字符串参数，跳转到第一个包含该字符串的位置
* `back()` 后退
* `forward()` 前进
* `length` 保存历史记录的属性















