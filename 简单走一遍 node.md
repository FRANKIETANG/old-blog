---
title: 简单走一遍 node
date: 2017-11-26 19:45:04
tags: [Node]
---
# 简单走一遍 node

一个 Express 便利贴项目 | 预览 http://tangkalun.top 源码 https://github.com/FRANKIETANG/Express-node

详情代码看我 [commit](https://github.com/FRANKIETANG/Express-node/commits/master) 

http://expressjs.com

## Todo

- 增删改查
- 便利贴可拖动
- GitHub 登录

## 环境搭建

### Express

```
npm init -y
npm install express --save
```

老套路，先用 express 问候一下世界

```
// app.js
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(3000, () => console.log('Example app listening on port 3000!'))
```

用 `node app.js` 成功的问候了世界

接下来安装 express-generator

```
npm install express-generator --save-dev
```

`./node_modules/express-generator/bin/express-cli.js . -f -e` 自动生成

```
// 看看目录
tangkalun@tangkalun-PC:~/Desktop/Express-node$ ls
app.js  bin  node_modules  package.json  public  routes  views
```

```
// 根据提示
npm i
npm start
```

换端口的方法是 `PORT=4000 node bin/www`

### webpack 配置

看以前写过的博客

### onchange

https://www.npmjs.com/package/onchange 查看当前文件状况

```
"build": "webpack --config=src/webpack.config.js",
"watch": "onchange 'src/**/*.js' 'src/**/*.less' -- npm run build"
```

## **需要了解的内容有**

- [middleware](http://expressjs.com/en/guide/using-middleware.html)
- [template engines with Express](http://expressjs.com/en/guide/using-template-engines.html)
- [Routing](http://expressjs.com/en/guide/routing.html)

比如说一个简单的 middleware 可以这样子写

```
app.use('/student', function (req, res, next) {
  res.send('hello frankie')
})
```

我理解的话，大概是一个类似路由的东西

模板引擎：https://www.npmjs.com/package/ejs

有一句话是设置路径的，很重要。是用来设置是路由还是其他公共路径下的文件不会被当成路由加载

```
app.use(express.static(path.join(__dirname, 'public')));
```

## 组件

没什么好说的都是以前做的轮子变形

不过发布订阅的确写得简单了点...

## API

`crud` `restful`

- 获取所有的 note `GET /api/notes  req:{}  res:{stauts: 0, data: [{},{}]} {status:1,errorMsg: '失败的原因'}`
- 创建一个 note `POST /api/note/create  req:{note: 'hello world'}  res:{stauts: 0}  {status:1,errorMsg: '失败的原因'}`
- 修改一个 note `POST /api/note/edit  req:{note: 'new note', id:100}`
- 删除一个 note `POST /api/note/delete req:{id:100}`

## 数据库

https://www.npmjs.com/package/sequelize

https://github.com/demopark/sequelize-docs-Zh-CN

```
// 用 n 模块降级到 6.10.3
$ npm install --save sequelize
$ npm install --save sqlite3
```

## 登录登出功能

[理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

https://www.npmjs.com/package/passport 注意文档中的 Sessions 和 Middleware

https://www.npmjs.com/package/passport-github 注意文档中的 Configure Strategy 和 Authenticate Requests

http://www.cnblogs.com/gabrielchen/p/5800225.html

https://github.com/settings/applications/new

[使用 GitHub OAuth 第三方验证登录](https://diamondfsd.com/article/7fc2b070-e238-4fbb-acaf-47f0e3fdaabc)

http://www.open-open.com/lib/view/open1416812717570.html

## 权限

[permission - commit](https://github.com/FRANKIETANG/Express-node/commit/23e800fd0f76661225f5bae731cad4f22d302a6d)

## 彩蛋

node 如何调试

```
npm i -g node-inspector
node-inspector
// 然后打开他指定的端口
// 然后打开项目
node --debug bin/www
```

## 后话

为了能让自己回想起自己的代码是啥意思，多看看自己的 commit

过一段时间想个更有难度的项目吧... 不应该老是做这种像 demo 一样的东西