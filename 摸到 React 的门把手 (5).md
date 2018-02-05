---
title: 摸到 React 的门把手 (5)
date: 2017-10-14 00:09:47
tags: [React,Black History]
---
# 摸到 React 的门把手 (5)

这次我们就来天我们上一次挖的深坑 [摸到 React 的门把手 (4)](https://frankietang.github.io/2017/08/22/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20(4)/) ，慢慢填，不用急。干那么快干嘛，要好好享受过程。体验学到知识的快感。

## localStorage

看名字就感觉应该是一个保存本地数据的玩意对吧（我猜的）[loocalStorage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage)

我们用这个方法就可以保存数据而不会一刷新就会初始化了

- 用户提交数据的时候，将所有的 todo 字符串的形式保存在 localStorage
- 重新打开页面的时候，将 localStorage 里面的字符串变为对象赋值给 todoList

封装 localStorage 封装成两个函数

```
export function save(key,value){
    return window.localStorage.setItem(key,
        JSON.stringify(value))
}

export function load(key){
    return JSON.parse(window.localStorage.getItem(key))
}
```

load 和 save 的调用

```
// load
localStore.load('todoList') || []
// save
localStore.save('todoList',this.state.todoList)
```

![](http://upload-images.jianshu.io/upload_images/3191557-7c1b95c23aa12525.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`||` 居然还有这种操作？可以用来定义默认参数？

![](http://upload-images.jianshu.io/upload_images/3191557-0dcd24f922e4a0aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不过 ES6 有了新的默认参数用法

![](http://upload-images.jianshu.io/upload_images/3191557-48b523717d27a58e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好了这里就不展开了。

## 优化代码

之前在每个 setState 之后会运行一次 save，但是我们知道 componentDidUpdate 会在组件更新之后调用，相当于 “组件更新” 等价于 “数据更新”

利用 componentDidUpdate 封装代码。

## 数据库

其实我们不需要服务器，用 LeanCloud 就可以了。

点击创建应用

![](http://upload-images.jianshu.io/upload_images/3191557-f47971710bf3edd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[看文档](https://leancloud.cn/docs/leanstorage_guide-js.html)

[JavaScript SDK 安装指南](https://leancloud.cn/docs/sdk_setup-js.html)

安装

```
$ sudo npm install leancloud-storage --save
```

复制这一段代码到我们的App.js

![](http://upload-images.jianshu.io/upload_images/3191557-49cd74d87191419f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
ping api.leancloud.cn
```

验证结果如下则正常

![](http://upload-images.jianshu.io/upload_images/3191557-e10c06115a5054f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后我们测试下面这一段代码。

![](http://upload-images.jianshu.io/upload_images/3191557-d0ceaa7fa89d6cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

OK 了

![](http://upload-images.jianshu.io/upload_images/3191557-4797976d0ab6e532.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后我们就继续把 LeanCloud 的对象和用户的开发文档看一遍，就可以继续写代码了。

## 继续撸代码

先把我们上面的 localStore 给删掉

然后做出一个登录框

![](http://upload-images.jianshu.io/upload_images/3191557-c5799a036bc6459c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还要让它变成一个选项卡来回切换

![](http://upload-images.jianshu.io/upload_images/3191557-caefa908df34d17f.gif?imageMogr2/auto-orient/strip)

把 form 表单的 input 和 formData 绑定用下面这种方法是不行的

```
    changeUsername(e){
        this.state.formData.username = e.target.value
        this.setState(this.state)
    }
```

其实是不行的，原因看 [Why can't I directly modify a component's state, really?](https://stackoverflow.com/questions/37755997/why-cant-i-directly-modify-a-components-state-really) 里面 [Pranesh Ravi](https://stackoverflow.com/users/4945468/pranesh-ravi) 的答案。

>Just a reminder: most basic methods for cloning in JS (`slice`, ES6 destructuring, etc.) are shallow. If you have a nested array or nested objects you'll need to look at other methods of deep copying, e.g. `JSON.parse(JSON.stringify(obj))` (though this particular method won't work if your object has circular references). 

因为对象是嵌套的，所以要用深拷贝的方法。

点击注册后使用 LeanCloud API 来注册

![](http://upload-images.jianshu.io/upload_images/3191557-0359d75695fc17ef.gif?imageMogr2/auto-orient/strip)

这样数据就能来到了

来到后还要把它记住并显示出来

![](http://upload-images.jianshu.io/upload_images/3191557-a186a02fe0ba8bc2.gif?imageMogr2/auto-orient/strip)

实现注册成功关闭窗口

![](http://upload-images.jianshu.io/upload_images/3191557-d801a196febd2d97.gif?imageMogr2/auto-orient/strip)

用户进入页面时读取上次登录的 user

![](http://upload-images.jianshu.io/upload_images/3191557-bbc5b207c51506ce.gif?imageMogr2/auto-orient/strip)

做出可以登出的功能

![](http://upload-images.jianshu.io/upload_images/3191557-963e5edeb970c9f1.gif?imageMogr2/auto-orient/strip)

完成登录功能

![](http://upload-images.jianshu.io/upload_images/3191557-10f8da0986e07f6b.gif?imageMogr2/auto-orient/strip)

代码：

- [LeanCloud](https://github.com/FRANKIETANG/banana-todolist/commits/master)
- [localStore](https://github.com/FRANKIETANG/react-todo-list/commits/master)