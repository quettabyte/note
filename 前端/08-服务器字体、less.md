# 服务器字体、iconfont、iconfont-阿里巴巴矢量图标库、Less

***

## 字体

[CSS 字体 (w3school.com.cn)](https://www.w3school.com.cn/css/css_font.asp)

### 字体导入

在项目文件夹下创建一个 `font`文件夹用来存放字体 `.ttf`文件

在css文件中使用 `@font-face`引入字体文件

```css
@font-face {
  font-family: 'heibai';
  src: url(font/maobi.ttf);
}
```

`font-family:`属性用来定义引入的字体名

`src:url();` 用来指定文件位置

**用法**

```css
p{font-family: 'heibai';}
```



## 阿里巴巴矢量图标库

在[iconfont-阿里巴巴矢量图标库](https://www.iconfont.cn/)网站搜索需要的图标字体iconfont

下载好需要的iconfont后，打开demo_index.html

有具体的使用方法

* Unicode

  ```html
  <span class="iconfont">&#x33;</span>
  ```

* Font class

  ```html
  <link rel="stylesheet" href="./iconfont.css">
  ```

* Symbol

  ```html
  <!-- 1 -->
  <script src="./iconfont.js"></script>
  <!-- 2 -->
  <style>
  .icon {
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }
  </style>
  <!-- 3 -->
  <svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-xxx"></use>
  </svg>
  ```



## less

* 定义变量

  大部分网站都有一个主题色，一般使用变量

  ```less
  @maincolor: #ff0000;
  ```

* 嵌套

  使用后代选择器时，可以使用嵌套

  ```less
  nav{
    height: 60px;
    a{
      color: @maincolor;
      &:hover{
        color: red;
      }
    }
  }
  ```

* 运算

  ```less
  .div3{
    border-width: (1px * 3);
    border-color: (#111 + #222);
    border-width: (10px - 2px);
    border-width: (10px / 2px);
  }
  ```

* 混合

  可以在别的选择器中引用

  ```less
  .public{
    width: 100px;
    height:100px;
  }
  .mydiv{
    .public;
    color: #000;
  }
  // 生成后
  .mydiv{
    width: 100px;
    height:100px;
    color: #000;
  }
  ```

  可以传递参数

  ```less
  .public(@w,@h){
    width: @w;
    height:@h;
  }
  .mydiv{
    .public(100px,100px);
  }
  ```

  如果不传参数可以设置默认参数

  > 带参数的.public不会生成到css文件中

  ```less
  .public(@w:100px,@h:100px){
    width: @w;
    height:@h;
  }
  .mydiv{
    .public();
  }
  ```

  ​













