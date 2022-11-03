---
title: React脚手架
date: 2022-11-01 19:47:09
author: cokeice
tags: [JavaScript, React案例]
categories: 
    - 笔记
    - React框架
index_img: /img/react/react.webp
---

<p align='center'>
<a href="https://www.github.com/Cokeic" target="_blank"><img src="https://img.shields.io/badge/Github-@可乐冰-f3e1e1.svg?style=flat-square&logo=Github&logoColor=181717"></a><a href="https://www.gitee.com/Cokeice" target="_blank"><img src="https://img.shields.io/badge/Gitee-@可乐冰-f3e1e1.svg?style=flat-square&logo=Gitee&logoColor=C71D23"></a><a href="https://cokeice.gitee.io/img/wechat/wx.png" target="_blank"><img src="https://img.shields.io/badge/微信-@LNFeng-f3e1e1.svg?style=flat-square&logo=WeChat"></a>

## todoList案例相关知识点📚

1. 拆分组件、实现静态组件，注意：className、style的写法
2. 动态初始化列表，如何确定将数据放在哪个组件的state中？
   * ——某个组件使用：放在其自身的state中
   * ——某些组件使用：放在他们共同的父组件state中（官方称此操作为：状态提升）
3. 关于父子之间通信：
   * 【父组件】给【子组件】传递数据：通过props传递
   * 【子组件】给【父组件】传递数据：通过props传递，要求父提前给子传递一个函数
4. 注意defaultChecked 和 checked的区别，类似的还有：defaultValue 和 value
   * 具体查看 `<✌补充知识.md>`
5. 状态在哪里，操作状态的方法就在哪里

