---
title: 摸到 React 的门把手 (2)
date: 2017-10-14 00:07:46
tags: [React,Black History]
---
# 摸到 React 的门把手 (2)

经过了上一篇 [摸到 React 的门把手](https://frankietang.github.io/2017/08/18/%E6%91%B8%E5%88%B0%20React%20%E7%9A%84%E9%97%A8%E6%8A%8A%E6%89%8B/) 的踩坑，我们现在可以更加深入的看看 React 其中的奥妙了。

## 安装

点击 Get Started 第一个就是 ReactDOM 的 Hello World，我们先别看这些，先看看怎么安装，点击 Installation

那我们就试试 Create a New App 

![](http://upload-images.jianshu.io/upload_images/3191557-5c60dc8c2e8b2bfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

搞完 `create-react-app my-app` 后会有提示，看看就好，记住这四句命令

![](http://upload-images.jianshu.io/upload_images/3191557-b2844d1fd40c9dff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

弹出如下页面就成功了

![](http://upload-images.jianshu.io/upload_images/3191557-237bac3ead01e3d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## create-react-app 里面有什么

我们来看一下文件目录

![](http://upload-images.jianshu.io/upload_images/3191557-52e47859bb2f5dc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- src 目录，用于存放所有源代码，最重要的就是 index.js 了。和 webpack 那个 index.js 差不多
- public 目录，用于存放不需要 build 的资源，如 publib/index.html

当我们运行了 `npm run build` 这句话，我们会发现多了一个 build 文件夹，里面的文件全部都压缩过了，感觉有点像 webpack 啊。。

## 试着写写 React 

我们把全部文件删掉，然后运行

```
create-react-app .
npm start
```

改 src/inden.js 

```
ReactDOM.render(
    <h1>Hello, world!</h1>,
    document.getElementById('root')
)
```

展现出一个大大的 hello world 

我们做点小修改

-  我们把 Hello World 改成 Hi World，只用直接在 `<h1>text</h1>` 这里改就好了。

- 把 document.getElementById('root') 改为 document.getElementById('root2')，哟吼？网页直接显示了报错信息

  ![](http://upload-images.jianshu.io/upload_images/3191557-c625b5538e2c6912.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

-  把 publib/index.html 里的 19 行改为 `<div id="root2"></div>`，我们会发现页面有变回去了，说明这些都是相关联的。

-  `<div id="root"></div>` 里添加了在 index.js 写好了的 HTML 代码。

## 将这个应用部署到 GitHub 

```
git add .
git commit -m 'update'
npm run build
```

我们会发现看上去好像已经上传了，但实际上并没有，上网查了查原来是 .gitignore 被 create-react-app 改掉了，你需要删除 .gitignore 里面的 /build 这一行。

然后我们就可以愉快的 push 上去了

嗯？怎么没有效果啊？还记得前面曾经有提示![](http://upload-images.jianshu.io/upload_images/3191557-ece626b7c04a108c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好，我们设置好路径

```
"homepage": "https://frankietang.github.io/react-demo/build"
```

搞定。

## [看看成果](https://github.com/FRANKIETANG/react-demo)