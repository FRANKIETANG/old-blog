---
title: 摸到 React 的门把手 (4)
date: 2017-10-14 00:09:14
tags: [React,Black History]
---
# 摸到 React 的门把手 (4)

经过一段时间的踩坑 [摸到 React 的门把手](https://frankietang.github.io/2017/08/18/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/) [摸到 React 的门把手 (2)](https://frankietang.github.io/2017/08/20/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%282%29/) [摸到 React 的门把手 (3)](https://frankietang.github.io/2017/08/21/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20%283%29/) ，是不是可以做一个项目了？

## Todo List

不管学什么框架，好像大家都喜欢做 Todo List 啊... AngularJS Vue.js React 都有Todo List ... 那我也做一个呗。

![](http://upload-images.jianshu.io/upload_images/3191557-46b343dbe1c59d29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 有什么功能

- 输入框输入要做的事
- 按回车添加要做的事
- 添加要做的事后输入框清空
- 每一个要做的事可以标记成已完成
- 要做的事能删除

## Todo List长什么样

- 有一个大盒子

- 里面有一个标题

- 有一个类似 `<input>` 的玩意

- 一个列表，重点

  - todoList
    - 一个数组 id 用来区分要做的事
    - title 是这个要做的事是什么
    - status 要有一个 completed 值表示完成，空表示未完成
    - deleted 是一个 boolean ，表示是否要删除


  - newTodo 用来容纳用户在 input 输入的值，为什么不用 input 的 value 属性？因为 value= 后面加引号会错。在 React 中是无法直接更改 from 表单元素的值的，必须通过 setState() 去响应用户的输入。

## 开始写代码

注意 JavaScript 会自动给行末添加分号。如果 return 后面换行不加括号就会变成 `return;`，所以为了提高可读性还是加括号会比较好。

添加 CSS `npm i -S normalize.css`

然后在 `import` 加上 CSS 和 JS ，另外一定要注意顺序。normalize.css 要放在最前面。

## 怎么交互

试了一下 newTodo 的值改成 `''` ，在 input 里什么都输入不了

![](http://upload-images.jianshu.io/upload_images/3191557-2fe83ce52dc119fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

浏览器告诉了我们两种方法

- If the field should be mutable use `defaultValue`.
- set either `onChange` or `readOnly`.

那我们用一下 `defaultValue` ，ok 了

![](http://upload-images.jianshu.io/upload_images/3191557-0a3d90f9199c3e88.gif?imageMogr2/auto-orient/strip)

监听用户的回车事件 我们可以用 [GlobalEventHandlers.onkeypress](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalEventHandlers/onkeypress) 这个属性

![](http://upload-images.jianshu.io/upload_images/3191557-383c1bddda0d2c8c.gif?imageMogr2/auto-orient/strip)

我们用 props 的话我们要注意绑定按回车那个 this ，就是我系列文章上一篇的 bind(this) 

这样，我们就能够往里面加东西了

![](http://upload-images.jianshu.io/upload_images/3191557-1b876867916258d3.gif?imageMogr2/auto-orient/strip)

但是我们又发现了一个问题，input 的 value 没有重置。那是因为 defaultValue，只会影响 input 的第一次值，后面的 newTodo 怎么变，都不会影响 input

那我们试试 onChange 这种方法，成了

![](http://upload-images.jianshu.io/upload_images/3191557-2fc657e33903e0cb.gif?imageMogr2/auto-orient/strip)

紧接着是标记为已完成的事件和未完成的事件

我们让 checked 的值先等于 null，点击后就变成了 completed

![](http://upload-images.jianshu.io/upload_images/3191557-44d0d5f42f5c0a9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![](http://upload-images.jianshu.io/upload_images/3191557-5d8f997dcfc09154.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击 checkbox 后增加了 `status: 'completed'`

接下来新增一个删除 todo

![](http://upload-images.jianshu.io/upload_images/3191557-a6bbcdeda9ada74d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![](http://upload-images.jianshu.io/upload_images/3191557-3279099d4a906453.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们还要让 deleted 变成真 deleted

那我们就用 `filter` 这个 API ，`.filter((item)=> !item.deleted)`

![](http://upload-images.jianshu.io/upload_images/3191557-76ff1267a6ea4bd5.gif?imageMogr2/auto-orient/strip)

基本功能已经做出来了

##代码

- [基本骨架完成](https://github.com/FRANKIETANG/react-todo-list/commit/2431a2a81415c3fd674f14a894a2bd3f7777f805)
- [将输入框变成 TodoInput 组件](https://github.com/FRANKIETANG/react-todo-list/commit/37a39392ac65e03d4a4551ca6f2aa33400fcd5ec)
- [将每个待办封装成 TodoItem 组件](https://github.com/FRANKIETANG/react-todo-list/commit/db78ca8d08545394b5ecd93e344fc955508ce393)
- [添加 normalize.css](https://github.com/FRANKIETANG/react-todo-list/commit/ad05ce18fe7083a2e76573c3032b28cc6c715103)
- [添加 reset.css](https://github.com/FRANKIETANG/react-todo-list/commit/9b62a921aa2adbfec99c17c04cc4438e7ae17b69)
- [fix value](https://github.com/FRANKIETANG/react-todo-list/commit/556f760571f1b60c7b7c25f46e6698bacb168d67)
- [监听回车事件](https://github.com/FRANKIETANG/react-todo-list/commit/5eeef99c4ae2678e60c28a691b0b5d935c1b2591)
- [App 传一个函数给 TodoInput](https://github.com/FRANKIETANG/react-todo-list/commit/c8727d8482b81f84e1441166f3be016b8affea9a)
- [bind(this)](https://github.com/FRANKIETANG/react-todo-list/commit/b9de4886314df06317d176b00b769196fcbd642d)
- [可以添加 todo 了](https://github.com/FRANKIETANG/react-todo-list/commit/b765bebe0097d7283d34209f84404e9433e667c4)
- [fix value again](https://github.com/FRANKIETANG/react-todo-list/commit/20701a8e764cfe158b3b387c6686fada36f54c52)
- [fix value again and again](https://github.com/FRANKIETANG/react-todo-list/commit/730db326d22892d3ef4e32e6e705c061d24a1142)
- [标记为已完成或者未完成](https://github.com/FRANKIETANG/react-todo-list/commit/a78fea70fd172c1aa35b77d85e1f9720f738ea1c)
- [删除 todo](https://github.com/FRANKIETANG/react-todo-list/commit/9bc07f8a2c564c95bacc7e8ba1728e4b69b20784)
- [真正删除 todo](https://github.com/FRANKIETANG/react-todo-list/commit/f0ea5a391f90588fd093eb859c273bbd96a05930)
- [TodoItem 样式](https://github.com/FRANKIETANG/react-todo-list/commit/85b7255e4daa64699badfa2b35f8eab4268516f5)
- [TodoInput 样式](https://github.com/FRANKIETANG/react-todo-list/commit/d1df4c1ecadaf9aa5af0a55e30d295e2b8b943c5)