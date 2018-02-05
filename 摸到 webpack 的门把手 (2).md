---
title: 摸到 webpack 的门把手 (2)
date: 2017-10-14 00:05:14
tags: [webpack,Black History]
---

# 摸到 webpack 的门把手 (2)

经过了上一篇 [摸到 webpack 的门把手](https://frankietang.github.io/2017/08/20/%E6%91%B8%E5%88%B0%20webpack%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/) 的踩坑，我们大概知道了 webpack 是用 loader 加载不同的资源，把大东西压成小东西。大不一定能满足，小也有小的优点嘛。对吧，有的时候大家就喜欢小。

## webpack 到底干了什么

bundle.js 把每一个每一个模块都 call 一下，然后把模块存到 installedModules 里，方便其他模块使用。有空填坑分析代码。

## 压缩代码的一些问题

我们会发现 jQuery 的代码并没有压缩，其实我们可以使用 `webpack -p`，webpack 给出的解释是
`shortcut for --optimize-minimize --define process.env.NODE_ENV="production"`

运行 `-p` ，实际执行

-  使用UglifyJsPlugin进行 JS文件压缩,webpack 自带的压缩插件
-  运行[LoaderOptionsPlugin](https://webpack.js.org/plugins/loader-options-plugin/#components/sidebar/sidebar.jsx)
-  设置Node环境变量

```
//运行 webpack -p 会通过如下方式调用 DefinePlugin
var webpack=require('webpack');

module.exports={
  plugins:[
    new webpack DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
}
```

`DefinePlugin` 在原始的源码中执行查找和替换操作. 在导入的代码中,任何出现 `process.env.NODE_ENV`的地方都会被替换为`”production”`. 因此, 形如`if (process.env.NODE_ENV !== ‘production’) console.log(‘…’)` 的代码就会等价于 `if (false) console.log(‘…’)` 并且最终通过`UglifyJS`等价替换掉.

[webpack - public-path](https://webpack.js.org/guides/public-path/)

## watch

每次写完代码都要 npm run webpack ，很麻烦，那我们需要 `webpack --progress --watch` 监听文件变动，只要我们保存了文件就会自动编译代码。

在 npm script 加一句 `"watch": "webpack --progress --watch"`，然后 npm run watch 试试

![](http://upload-images.jianshu.io/upload_images/3191557-f654ac96a8135f12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不但能刷新代码，还能刷新浏览器

看到 Using webpack-dev-server 那里，它叫我们在根目录跑这句话

```
npm install --save-dev webpack-dev-server
```

在 webpack.config.js 加点东西

```
devServer: {
    contentBase: './dist'
},
```

在 package.json 的 npm script 加点东西

```
"start": "webpack-dev-server --open"
```

成了，看下图。
![](http://upload-images.jianshu.io/upload_images/3191557-494866b6ba1c3ca0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果我们改一下 src/bundle.js 就会发现

- bundle.js 自动打包
- [http://localhost:8080/](http://localhost:8080/) 自动刷新

![](http://upload-images.jianshu.io/upload_images/3191557-c12e95d7cc12d8af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意，我们就不能再直接打开 index.html 了，因为它引用的是 /bundle.js，用 file:// 协议打开 index.html 的话，会请求 file:///bundle.js，显然这个文件不存在。

[webpack - development](https://webpack.js.org/guides/development/)

## [查看成果](https://github.com/FRANKIETANG/webpack-demo)