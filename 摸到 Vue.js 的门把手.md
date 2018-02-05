---
title: 摸到 Vue.js 的门把手
date: 2017-10-14 00:11:47
tags: [Vue,Black History]
---
# 摸到 Vue.js 的门把手

文档那么多中文多和谐啊是不是？[Vue.js](https://cn.vuejs.org/)

## 起步

先把 webpack 给配好。

接下来我们输入 Vue.js 测试代码给我们的代码

```
//index.html
      <div id="app">
          {{ message }}
      </div> 
//index.js
import Vue from 'vue'

var app = new Vue({
    el: '#app',
    data: {
      message: 'Hello Vue!'
    }
  })
```

真的是非常的和谐，很像 React 

![](http://upload-images.jianshu.io/upload_images/3191557-19472a5df4b492fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 双向绑定

```
//index.html
<div class="newTask">
	<input type="text" v-model="newTodo">
</div>
//index.js
var app = new Vue({
    el: '#app',
    data: {
        newTodo: '',
        todoList: []
    },
    created: function(){
        let i = 0
        setInterval(()=>{
            this.newTodo = i
            i += 1
        },1000)
    }
})
```

只要 data.newTodo 被 JS 改了，input.value 就会变成一样的值。

```
//index.html
<div class="newTask">
	<input type="text" v-model="newTodo">
</div>
//index.js
var app = new Vue({
    el: '#app',
    data: {
        newTodo: '',
        todoList: []
    },
    created: function(){
        let i = 0
        setInterval(()=>{
            console.log(this.newTodo)
            i += 1
        },1000)
    }
})
```

只要 input.value 被用户改了，data.newTodo 就会变成一样的值；

## 绑定事件

```
//index.html
<input type="text" v-model="newTodo" @keypress.enter="addTodo">
//index.js
methods: {
    addTodo: function(){
        this.todoList.push({
            title: this.newTodo,
            createdAt: new Date()
        })
        console.log(this.todoList)
    }
}
```

## 输入东西在页面展示

```
<li v-for="todo in todoList">
  {{ todo.title }}
</li>
```

## 复选框

```
//index.html
<input type="checkbox" v-model="todo.done">{{ todo.title }}
<span v-if="todo.done">已完成</span>
<span v-else>未完成</span>
//index.js
addTodo: function(){
    this.todoList.push({
        title: this.newTodo,
        createdAt: new Date(),
        done: false
    })
    this.newTodo = "" //input 框变成空的
}
```

## localStorage 保存数据

```
created: function() {
  window.onbeforeunload = () =>{
    let dataString = JSON.stringify(this.todoList)
    window.localStorage.setItem('myTodos', dataString)
  }
  let oldDataString = window.localStorage.getItem('myTodos')
  let oldData = JSON.parse(oldDataString)
  this.todoList = oldData || []
},
```

[onbeforeunload - MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/onbeforeunload) 当窗口即将被[`卸载`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/onunload)时,会触发该事件.此时页面文档依然可见,且该事件的默认动作可以被[`取消`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault).

[JSON - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON) 简单深拷贝

[localStorage - MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage) `localStorage` 属性允许你访问一个 local [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象。存储在 localStorage 里面的数据没有过期时间（expiration time）。

## 数据存储选 leancloud

[JavaScript SDK 安装指南](https://leancloud.cn/docs/start.html)

[数据存储开发指南 · JavaScript](https://leancloud.cn/docs/leanstorage_guide-js.html#数据存储开发指南___JavaScript)

之前有一个没有注意到的东西，就是利用这一段代码的时候打印出来的东西

```
    signUp: function () {
      let user = new AV.User();
      user.setUsername(this.formData.username);
      user.setPassword(this.formData.password);
      user.signUp().then(function (loginedUser) {
        console.log(loginedUser);
      }, function (error) {
      });
    }
```

> 注意打印出来的三个属性：attributes, createdAt, id。
>
> 其中 attributes 就是我们传给数据库的 username（我们不是还传了一个 password 吗？服务器是不会把 password 传给前端的）
>
> createdAt 是这个数据创建的时间，id 是用户的 id，也是我们区别用户的唯一凭据。

## Vue 的切换 Tab

在 React 中是这样切换的

```
{ this.state.selectedTab === 'signInOrSignUp' ? <SignIn />  : <SignUp /> }
```

而 Vue 是这样

```
<div class="signUp" v-if="actionType === 'signUp'">
<div class="logIn" v-if="actionType === 'logIn'">
```

## 可注册 / 可登入 / 可登出 功能

这个和我之前 React 没什么区别了

就是看 leancloud 的文档抄抄

注意 `v-if` 的使用技巧

## 保存数据功能

[保存数据](https://leancloud.cn/docs/leanstorage_guide-js.html#保存对象)

```
  // 声明类型
  var TodoFolder = AV.Object.extend('TodoFolder');
  // 新建对象
  var todoFolder = new TodoFolder();
  // 设置名称
  todoFolder.set('name','工作');
  // 设置优先级
  todoFolder.set('priority',1);
  todoFolder.save().then(function (todo) {
    console.log('objectId is ' + todo.id);
  }, function (error) {
    console.error(error);
  });
```

照着抄

注意不可以把代码放在 `window.onbeforeunload` 里，请求发出页面就刷新了

![](http://upload-images.jianshu.io/upload_images/3191557-509e312e01c9434c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>从结果可以看到，AllTodos 保存请求失败了，被 `canceled` 了。
>
>如果一个页面就要死了（刷新就表示不要当前页面了，当前页面可以死了），那么这个页面发出的请求其实就没有任何意义了。既然没有意义，浏览器为什么浪费时间去发这个页面里的请求呢？所以浏览器直接取消了这个请求。
>
>简单来说，那就是：beforeunload 事件里面的所有请求都发不出去，会被取消！
>我还从来没有在哪一本书里看到过这个知识点。所以说「实践」是非常重要的。

这样就应该把上面那一段代码封装成一个函数调用。

## 角色权限管理

[角色的创建](https://leancloud.cn/docs/acl-guide.html#角色的创建)

```
  // 创建一个针对 User 的查询
  var query = new AV.Query(AV.User);
  query.get('55f1572460b2ce30e8b7afde').then(function(otherUser) {
    var post = new AV.Object('Post');
    post.set('title', '这是我的第二条发言，谢谢大家！');
    post.set('content','我最近喜欢看足球和篮球了。');

    // 新建一个 ACL 实例
    var acl = new AV.ACL();
    acl.setPublicReadAccess(true);
    acl.setWriteAccess(AV.User.current(), true);
    acl.setWriteAccess(otherUser, true);

    // 将 ACL 实例赋予 Post 对象
    post.setACL(acl);

    // 保存到云端
    return post.save();
  }).then(function() {
    // 保存成功
  }).catch(function(error) {
    // 错误信息
    console.log(error);
  });
```

## 居然有 React 一样的 BUG

就是 logout 之后 再 login 别的账号会没有数据

和 React 的解决方法一样封装成一个函数放在 `methods` 里。

## 遇到奇怪的 BUG

```
rm -rf node_modules/
npm install
```