---
title: 摸到 Vue.js 的门把手（3）
date: 2017-10-14 00:13:11
tags: [Vue,Black History]
---
# 摸到 Vue.js 的门把手（3）

回炉重造

## 重看 `vue init webpack` 的选项

```
? Generate project in current directory? Yes
? Project name vue-resume
? Project description A Vue.js project
? Author FRANKIETANG <350558468@qq.com>
? Vue build standalone
//可以以后再装
? Install vue-router? No
//这东西很烦，不要
? Use ESLint to lint your code? No
//不需要单元测试
? Setup unit tests with Karma + Mocha? No
//不用管
? Setup e2e tests with Nightwatch? No
```

## 升级了 NPM 之后居然装不了 SCSS ？

会有一个奇怪的 `user "root" does not have permission to access the dev dir "/home/tangkalun/Desktop/vue-resume/node_modules/node-sass/.node-gyp/6.3.1"`

那就用 `sudo SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install --save --unsafe-perm`

不行就用 `export SASS_BINARY_SITE="https://npm.taobao.org/mirrors/node-sass"`

`npm install --save  sass-loader node-sass`

又不行？

`sudo npm install -g cnpm --registry=https://registry.npm.taobao.org`

`sudo cnpm install --save  sass-loader node-sass --unsafe-perm`

不要加 `--unsafe-perm`

在 linux 下有可能循环安装...

用这句 `rm -rf node_modules/ && npm install && npm rebuild`

不用想那么多。人生苦短，能用就行。

## 程序构想

和之前一样分为导航栏，侧栏编写区，预览区

## iconfont 的善用

生成 `<script>` 标签直接用...不用像之前写个脚本来搞一大堆

## element-ui

`npm i element-ui -S`

[文档](http://element.eleme.io/#/zh-CN/component/installation)

## 数据绑定语法为`:value.sync`

[点击这里](https://vuxjs.gitbooks.io/vux/content/form/x-input.html)

## slot 分發內容

[Vue – 如何理解「使用 slot 分發內容」](http://jsnwork.kiiuo.com/archives/2645/vue-%E5%A6%82%E4%BD%95%E7%90%86%E8%A7%A3%E3%80%8C%E4%BD%BF%E7%94%A8-slot-%E5%88%86%E7%99%BC%E5%85%A7%E5%AE%B9%E3%80%8D)

## 完。

[预览](https://frankietang.github.io/vue-resume/dist/index.html)