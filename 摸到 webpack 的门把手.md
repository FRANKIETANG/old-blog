---
title: 摸到 webpack 的门把手
date: 2017-10-14 00:04:38
tags: [webpack,Black History]
---
# 摸到 webpack 的门把手

之前接触过一阵子 webpack ，想了一下还是写篇博客吧。要不然又忘掉怎么配置了。

## 怎么安装？

[来，我们先看 webpack 的官网](https://webpack.js.org/)

点击到 guides ，我们直接抄，抄完我们就知道 webpack 的流程了

不过有几点我们是要注意的

- 注意 index.js 中的 `_.join`，这个 _ 实际上是 lodash 暴露的全局变量。
- 为了使用 lodash，HTML 使用 script 引入了 lodash v4.16.6。可以用 npm 装回来 `npm install --save lodash`
- 尽量运行 `./node_modules/.bin/webpack` 

## 看看文件结构

- `./node_modules/.bin/webpack src/index.js dist/bundle.js` 将 src/index.js 变成 dist/bundle.js
- index.html 引用的是 dist/bundle.js
- lodash 被安装在 node_modules 里
- webpack 也被安装在 node_modules里，`./node_modules/.bin/webpack` 就是一个可执行文件
- webpack、lodash 的版本号都被写在 package.json 里了

[读懂diff - 阮一峰](http://www.ruanyifeng.com/blog/2012/08/how_to_read_diff.html)

## 引用 jQuery

- `npm i -S jquery`

  ```
   import _ from 'lodash'
  +import j from 'jquery'

   function component () {
  -  var element = document.createElement('div');
  +  var element = j('<div></div>');

     /* lodash is required for the next line to work */
  -  element.innerHTML = _.join(['Hello','webpack'], ' ');
  +  element.html(_.join(['Hello','webpack'], ' '))

  -  return element;
  +  return element.get(0);
   }

   document.body.appendChild(component());
  ```

-  `./node_modules/.bin/webpack src/index.js dist/bundle.js` 跑这句话

- 点击 commits 看看有什么变化

- 建议 index.js 改成 `console.log(1)` 然后运行 `./node_modules/.bin/webpack src/index.js dist/bundle.js`，看看 bundle.js 和 index.js 的区别

## 做一点改良

- 我们可以在根目录搞一个 `webpack.config.js`，在里面写上一点代码。

  ```
  var path = require('path')

  module.exports = {
      entry: './src/index.js',
      output: {
          filename: 'bundle.js',
          path: path.resolve(__dirname, 'dist')
      }
  }
  ```

  重点是这三个玩意 src/index.js 、dist 和 bundle.js 在哪就行，方便我们后面改。

- 然后我们就可以用上这句 `./node_modules/.bin/webpack --config webpack.config.js`

- 或者我们可以进行下面的改良

  ```
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "webpack": "webpack"
    },
    "keywords": [],
  ```

  然后运行 npm run webpack

## 尝试 import 一个自己的文件

- 创建一个 `foo.js`

  ```
  export default function(){
      return 'tangkalun'
  }
  ```

- 然后在 `index.js` 加上 `import foo from './foo'`，顺便 `console.log` 一下

- `npm run webpack`

## 利用 webpack 压缩 JS 文件

- [webpack 自带的](https://webpack.js.org/guides/production/) 支持所有 [UglifyJS 选项](https://github.com/mishoo/UglifyJS2#usage)

- 在 `webpack.config.js` 加上这样一句话，UglifyJS 的选项可以一个都不加

  ```
  var webpack = require('webpack');

  module.exports = {
    /*...*/
    plugins:[
      new webpack.optimize.UglifyJsPlugin()
    ]
  };
  ```

- `npm run webpack`

- 压完了

## 看看成果 

[点击这里](https://github.com/FRANKIETANG/webpack-demo)