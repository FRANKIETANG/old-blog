---
title: 摸到 React 的门把手 (6)
date: 2017-10-14 00:10:29
tags: [React,Black History]
---
# 摸到 React 的门把手 (6)

慢慢的撸出一个应用，就像看着自己的孩子长大一样。

## 继续撸代码

增加邮箱注册并找回密码的方法。

![](http://upload-images.jianshu.io/upload_images/3191557-adc098e0863d2a19.gif?imageMogr2/auto-orient/strip)

请求 LeanCloud 的 API

![](http://upload-images.jianshu.io/upload_images/3191557-d526e3072a087e32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

返回登录框

![](http://upload-images.jianshu.io/upload_images/3191557-cb586c8af567044d.gif?imageMogr2/auto-orient/strip)

## 分成一个一个小模块

就是把每个功能分成一个一个的小模块，但是在这一步要注意，会有大量的`props`和`state`。只要不弄混，你就能体会到模块化的好处。

[React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)

![](http://upload-images.jianshu.io/upload_images/3191557-85d314006e43f8d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们用 props 在组件直接传递数据（从父组件到子组件），用 state 保存组件自身的数据，这就是 props 和 state 的区别。

注意每个模块的`state` 

App 的 `state`

```
this.state = {
  user: getCurrentUser() || {},
  newTodo: '',
  todoList: []
}
```

UserDialog 的 `state`

```
this.state = {
  selectedTeb: 'signInOrSignUp', //forgotPassword
  formData: {
    email: '',
    username: '',
    password: '',
  }
}
```

SignInOrSignUp 的 `state`

```
this.state = {
  selected: 'signUp'
}
```

所以说`state`的分布策略是每个组件只保存与自己有关系的数据到state里。而`props`的用法就是，负责传递数据或函数到子组件中。

如果一个组件没有`state`只有`props`，说明这个组件没有特殊逻辑，是一个纯（pure）的组件，而且还有一个特点是不能对（props）做任何修改。

[Props are Read-Only](https://facebook.github.io/react/docs/components-and-props.html#props-are-read-only)

我们可以把组件只有 `props` 没有 `state` 写成一个函数。

在封装的时候要注意`this`，因为之前还是一个组件，所以是可以利用`this`来接住父组件的数据的，而变成了一个函数之后，`this`传入就是这个函数的东西了。所以 `{this.props.xxx}`就变成了`{props.props.xxx}`。

另外如果组件只有一个方法，也可以改写一个函数

注意

```
export default function (props) {
    return <input type="text" value={props.content} 
    className="TodoInput" 
    onKeyPress={submit.bind(null,props)} 
    onChange={changeTitle.bind(null,props)}/>
}
//相当于
let temp = function(e){
    changeTitle.call(null, props, e)
}
onChange={temp}
```

可以说这几行代码是很看基础的。

为什么在那几个函数明明没有用到 React ，但是要用 import React 。这是因为这个函数里面有 JSX ，而引用了这句话后`import React from 'react`浏览器就能读懂。[我想这将是最有价值的 react 入门与进阶教程](http://web.jobbole.com/91637/)

重构到此为止

## 把 TodoItem 的数据上传到云端

我们再回过去看看 LeanCloud 的文档

![](http://upload-images.jianshu.io/upload_images/3191557-087167c7a4e954a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把这段代码复制来用一下，却得到了这样的反馈。

![](http://upload-images.jianshu.io/upload_images/3191557-4c6b054d27c27e04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

控制中心也收到数据了

![](http://upload-images.jianshu.io/upload_images/3191557-1cb2b198f0b21514.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以我们就思考一下 TodoItem 的流程

- 创建一个 Todo，就在中心留下一条记录
- 用户修改一个 Todo，就发请求修改对应的 Todo
- 用户删除 Todo，我们就删除一个

![](http://upload-images.jianshu.io/upload_images/3191557-213d7d92c6c93bf2.gif?imageMogr2/auto-orient/strip)

这里要用到文档里的代码 [批量操作](https://leancloud.cn/docs/leanstorage_guide-js.html#批量操作) [单用户权限设置](https://leancloud.cn/docs/acl-guide.html#单用户权限设置) [删除对象](https://leancloud.cn/docs/leanstorage_guide-js.html#删除对象) [更新对象](https://leancloud.cn/docs/leanstorage_guide-js.html#更新对象)

注意在更新对象的函数

```
  update({id,title,status,deleted},successFn,errorFn){
    let todo = AV.Object.createWithoutData('Todo',id)
    title !== undefined && todo.set('title',title)
    status !== undefined && todo.set('status',status)
    deleted !== undefined && todo.set('deleted',deleted)
//这里为什么要那么麻烦？
//为什么我要像上面那样写代码？
//考虑如下场景
//update({id:1, title:'hi'})
//调用 update 时，很有可能没有传 status 和 deleted
//也就是说，用户只想「局部更新」
//所以我们只 set 该 set 的
//那么为什么不写成 title && todo.set('title', title) 呢，为什么要多此一举跟 undefined 做对比呢？
//考虑如下场景
//update({id:1, title: '', status: null}}
//用户想将 title 和 status 置空，我们要满足
    todo.save().then((response) => {
      successFn && successFn.call(null)
    },(error) => {
      errorFn && errorFn.call(null,error)
    })
  }
```

啊真的累，React 真的难啊，几个小模块就那么多接口...

[所有代码](https://github.com/FRANKIETANG/banana-todolist/commits/master)

[预览](https://frankietang.github.io/banana-todolist/build/index.html)

我会另开几篇文章来详解这里免得知识点的，因为涉及的知识点有点多。