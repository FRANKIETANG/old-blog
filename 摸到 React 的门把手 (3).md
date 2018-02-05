---
title: 摸到 React 的门把手 (3)
date: 2017-10-14 00:08:13
tags: [React,Black History]
---
# 摸到 React 的门把手 (3)

经过了上几篇文章 [摸到 React 的门把手](https://frankietang.github.io/2017/08/18/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/) [摸到 React 的门把手 (2)](https://frankietang.github.io/2017/08/20/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B%20(2)/) 的踩坑，我们估计很快就可以摸到门把手了。

## 关于 JSX

实际上 JSX 并不是 JavaScript 和 HTML 的结合，而是和 XML 的结合，就像这样。[https://babeljs.io/repl/](https://babeljs.io/repl/)

![](http://upload-images.jianshu.io/upload_images/3191557-264608323011334e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Introducing - JSX](https://facebook.github.io/react/docs/introducing-jsx.html)

## 关于 React 的虚拟 DOM

> 你用这些 XML 写出来的标签，都不会出现在页面里，只会出现在内存里。React 会使用虚拟 DOM 计算出真正的页面结构，然后再更新到页面中（真正的 DOM 操作）。
>
> 让 JS 操作内存肯定比操作 DOM 要快很多。

![.](http://upload-images.jianshu.io/upload_images/3191557-04a725ede551ad1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看看这一段代码：

- 用 XML 语法声明一个 h1
- babel 将 h1 转为 React Element（虚拟元素）
- React 将虚拟元素转化为真正的 DOM，插入到 #root 里。

## 按需更新

>With our knowledge so far, the only way to update the UI is to create a new element, and pass it to `ReactDOM.render()`.

所以说更新一个元素的唯一方法就是新的元素

我们改一下 index.js

```
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

这个示例通过 [`setInterval()`](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval) 方法，每秒钟调用一次 `ReactDOM.render()`.

## 组件

```
class Welcome extends React.Component {
    render() {
        return <h1>Component</h1>
    }
}

ReactDOM.render(
    <Welcome/>,
    document.getElementById('root')
)
```

这样我们就造了一个 `<Welcome/>` 的组件

`extends React.Component` 不能删掉。

## 组件成为一个单独代码

```
//src/Welcome.js
import React from 'react'

class Welcome extends React.Component {
    render() {
        return <h1>Component</h1>
    }
}

export default Welcome
```

`import React from 'react'` 这个是引用 React ，不写这一句就在这个组件代码里就用不了 `React.Component`

`export` 和 `export default` 作用是导出常量/函数/文件/模块 这些

`export` 和 `import` 可以有多个，`export default` 只能有一个

`export` 导出导入的时候要加 {}，`export default` 则不需要

[ES6：export default 和 export 区别](http://www.jianshu.com/p/edaf43e9384f)

```
//src/index.js
import Welcome from './Welcome'

ReactDOM.render(
  <Welcome/>,
  document.getElementById('root')
)
```

## props

```
//src/Welcome.js
class Welcome extends React.Component {
    render() {
        return <h1>I am {this.props.name}</h1>
    }
}
//其中 class Welcome 变成 funciton Welcome
```

```
ReactDOM.render(
    <Welcome name="tangkalun"/>,
    document.getElementById('root')
)
```

## state

>组件不能改变得到的 props，那么组件中可以变的东西放在哪呢？答案是 state（函数形式的组件不支持 state）。

```
//src/Welcome.js
class Welcome extends React.Component {
    constructor(props){
        super(props)
        this.state = {
            date: new Date()
        }
    }
    render() {
        return (
            <div>
                <h1>I am {this.props.name}</h1>
                <h2>{this.state.date.toString()}.</h2>                
            </div>
        )
    }
}
```

## 改变 state

这里我们可以用 `setState()` 来改变 state。

```
//在 constructor 里加
setInterval(function(){
  this.state = {
    date: new Date()
  }
})
```

这上面的代码是有问题的，实际上还要 `.bind(this)`

```
setInterval(function(){
  this.setState({
    date: new Date()
  })
}.bind(this))
```

> The callback is made in a different context. You need to `bind` to `this` in order to have access inside the callback

[React this.setState is not a function](https://stackoverflow.com/questions/31045716/react-this-setstate-is-not-a-function)

[setState：这个API设计到底怎么样](https://zhuanlan.zhihu.com/p/25954470)

我讨厌 setState 这个 API

## 生命周期

[The Component Lifecycle](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle)

React 的生命周期包括三个阶段：mount（挂载）、update（更新）和 unmount（移除）

### mount

> mount 就是第一次让组件出现在页面中的过程。这个过程的关键就是 render 方法。React 会将 render 的返回值（一般是虚拟 DOM，也可以是 DOM 或者 null）插入到页面中。
>
> 这个过程会暴露几个钩子（hook）方便你往里面加代码：
>
> - `constructor()` 初始化 props 和 state
> - `componentWillMount()` 我要插入了
> - `render()` 将 render 里的 return 的内容插入到页面中
> - `componentDidMount()` 插进去后该做点什么吗？

commit: [钩子](https://github.com/FRANKIETANG/react-demo/commit/cad040489620cf5b4f7e02a571cb2e3c06803932)

![](http://upload-images.jianshu.io/upload_images/3191557-c31f16851c918ba5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### update

>mount 之后，如果数据有任何变动，就会来到 update 过程，这个过程有 5 个钩子：
>
>- `componentWillReceiveProps(nextProps)` - 我要读取 props 啦！
>- `shouldComponentUpdate(nextProps, nextState)` - 请问要不要更新组件？true / false
>- `componentWillUpdate()` - 我要更新组件啦！
>- `render()` - 更新！
>- `componentDidUpdate()` - 更新完毕啦！

### unmount

>当一个组件将要从页面中移除时，会进入 unmount 过程，这个过程就一个钩子：
>
>- componentWillUnmount() - 我要死啦！

## setState 应该放在哪？

commit: [哪些钩子里面可以加 setState](https://github.com/FRANKIETANG/react-demo/commit/3b2061343d754f2975356ef4502d445e943eef5a)

![](http://upload-images.jianshu.io/upload_images/3191557-6dc701032129deb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里面有三个错误

第一个错误说明不能在 constructor 里面 setState

第二个错误说明不能在 render 里面 setState

第三个错误说明 Welcome.shouldComponentUpdate 必须返回 boolean value，那我们改改

![](http://upload-images.jianshu.io/upload_images/3191557-ace3aae6e3f84a99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好吧错误都是 render 的，那就都删掉吧。

还是会有 bug 原因是在 componentWillUpdate 和 componentDidUpdate 里 setState 了，因为每次 setState 都会触发这两个钩子，而这两个钩子却又触发了 setState。

所以只能在这几个钩子里 setState：

- `componentWillMount`
- `componentDidMount`
- `componentWillReceiveProps`
- `componentDidUpdate`

## [看看成果](https://github.com/FRANKIETANG/react-demo)