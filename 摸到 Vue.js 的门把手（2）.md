---
title: 摸到 Vue.js 的门把手（2）
date: 2017-10-14 00:12:40
tags: [Vue,Black History]
---
# 摸到 Vue.js 的门把手（2）

用 vue-cli 快速搭建

```
npm init -y
npm install -g vue-cli
vue init webpack .
//全部回车
npm i
npm run dev
```

## 项目分三组件

`<Topbar/>` / `<ResumeEditor/>` / `<ResumePreview/>`

## 禁用 ESLint 

```
//build/webpack.base.conf.js
  module: {
    rules: [
      //{
        //test: /\.(js|vue)$/,
        //loader: 'eslint-loader',
        //enforce: "pre",
        //include: [resolve('src'), resolve('test')],
        //options: {
          //formatter: eslintFriendlyFormatter
        //}
      //},
```

## GitHub 预览

```
//config/index.js
assetsPublicPath: '/vue-resume/dist',
```

`npm run build`

`https://frankietang.github.io/vue-resume/dist/#/`

## 使用 SCSS

`npm install --save  sass-loader node-sass`

## SVG 合并和使用方法

- [使用脚本将所有 svg 拼成一个 svg，原来的多个 svg 变成了多个 symbol](https://github.com/FRANKIETANG/vue-resume/commit/a112088f1c0bc772813f855139fcdd4cdeeea380)
- [运行 node build/svg-symbol.js](https://github.com/FRANKIETANG/vue-resume/commit/78556c0f7cb18d20e078f3abbbb85fd9b4c8ed43)
- [将 SVG 插入 body 中](https://github.com/FRANKIETANG/vue-resume/commit/476cec015bbea1e7fc55bbfb33ba627c68353084)

```
//任意地方
<svg>
  <use xlink:href="#icon-xxx"></use>
</svg>
```

**值得注意的是，之前的 ResumeEditor 中的 data 是对象，对象是无序的，应该用数组让项目有一个完整的顺序**

## 填写区核心代码

```
        <ol class="panels">
            <li v-for="item in resume.config" 
            v-show="item.field === selected">
                <div v-if="resume[item.field] instanceof Array">
                    <div class="subitem" v-for="subitem in resume[item.field]">
                        <div class="resumeField" v-for="(value,key) in subitem">
                            <label>{{key}}</label>
                            <input type="text" :value="value">                            
                        </div>
                        <hr>
                    </div>
                </div>
                <div v-else class="resumeField" 
                v-for="(value,key) in resume[item.field]">
                    <label>{{key}}</label>
                    <input type="text" 
                    v-model="resume[item.field][key]">
                </div>
            </li>
        </ol>
```

## 如何把填进去的数据放到预览页面呢？

可以做一个公共数据储存区域

填写区填进去的数据先放到公共数据再传到预览区

## Vuex

[最基本的 Vuex 记数应用](https://jsfiddle.net/n9jmu5v7/1269/)

## getter 与 setter

```
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

## 提交载荷（Payload）

你可以向 `store.commit` 传入额外的参数，即 mutation 的 **载荷（payload）**：

```
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}

store.commit('increment', 10)
```

在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读：

```
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

store.commit('increment', {
  amount: 10
})
```

## $store

这个 `$` 在 Vue 里是代表全局的意思

## 多次 import 同一个文件

Node对引入的模块都会进行缓存（缓存的是编译和执行后的对象），减少二次引入开销；
在Node的加载机制中，缓存的优先级是最高的；
这一点同时适用于不同的模块加载机制，无论ES2015的import还是CommonJS的require；

## CSS white-space: pre-line

去除一行文本中的空格，但是保留一行的换行符，作用是防止用户在输入框输入空格而产生对用户不友好显示效果

## Object-Path

[object-path](https://github.com/mariocasciaro/object-path)

[Object-Path 源码解读](https://satanwoo.github.io/2015/11/05/Object-Path-Source-Code/)

## Object.assign()

`Object.assign()` 方法用于将所有可枚举的属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

[Object.assign()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

## ES6

```
//旧版本
components: {
  'MyDialog': MyDialog
}
//ES6
components: {
  MyDialog
}
```