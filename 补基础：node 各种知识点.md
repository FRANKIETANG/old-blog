---
title: 补基础： node 各种知识点
date: 2017-11-29 18:56:54
tags: [Node,HTTP]
---
# 补基础：node 各种知识点

简单带过所有开发环境

NVM:nvm是一个node.js版本管理器，我们将会使用nvm安装node.js的不同版本
[NVM安装指南](https://github.com/creationix/nvm#installation)

Node.js和npm: [Node.js官方文档](https://nodejs.org/dist/latest-v8.x/docs/api/) | [Node.js 教程| 菜鸟教程](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0ahUKEwieh52Cgd_XAhUKrI8KHT9NBAoQFggmMAA&url=http%3A%2F%2Fwww.runoob.com%2Fnodejs%2Fnodejs-tutorial.html&usg=AOvVaw2jflp9kjA1IHdV5QL7UYRB)

Git: [Git 教程| 菜鸟教程](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0ahUKEwjD3buFgd_XAhUJvo8KHem-BIIQFggmMAA&url=http%3A%2F%2Fwww.runoob.com%2Fgit%2Fgit-tutorial.html&usg=AOvVaw2SlbSmAVE814KUmXI236qx)

MongoDB：MongoDB是时下最流行的NoSQL数据库，[MongoDB官网](https://www.mongodb.com/) | [官方文档](https://docs.mongodb.com/manual/introduction/)

[Ubuntu下安装MongoDB官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

Redis:运用最广的K-V数据库之一。在Ubuntu下使用 `sudo apt-get install redis-server` [官网](https://redis.io/)

## HTTP

[HTTP - MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)

[HTTP](https://blog.zain.red/2017/11/23/http/)

[关于HTTP协议，一篇就够了](http://www.jianshu.com/p/80e25cb1d81a)

## Git 和 Linux

看我的旧文章 [Linux 的基本命令行和 Git 的基本操作](http://www.jianshu.com/p/82142a85df5d)

## Node

写一个脚本文件 show.js，满足以下需求：运行 node /path/to/show.js，输出当前目录下的所有文件。

```
touch show.js
vi show.js

#!/usr/bin/env node  // 告诉 bash 用 node 运行
var fs = require("fs")
console.log("查看当前目录")
fs.readdir(process.cwd(),function(error, files){
  if(error){
    return console.error(error)
  }
  files.forEach(function(file){
    console.log(file)
  })
})

node show.js
```

写一个脚本文件 view.js，满足以下需求：运行 node /path/to/view.js xxx，如果 xxx 文件存在，就输出 xxx 内容；如果 xxx 文件不存在，就输出「xxx 不存在」

```
touch view.js
vi view.js

#!/usr/bin/env node  // 告诉 bash 用 node 运行
var file = process.argv[2]  // 获取输入命令行第三个参数
var fs = require('fs')
fs.stat(file, function(error, stat){
  if(stat&&stat.isFile()) {
    console.log('文件存在')
    var data = fs.readFileSync(file, "utf-8")  // 读取文件内容
    console.log(data)
  } else {
    console.log('文件不存在或不是标准文件')
  }
})

node view.js show.js
```

## npm

把上面那两个代码上传

```
npm adduser  // 一顿操作
ls
npm init  // 一顿起名
/*
在 package.json 设置东西
"bin":{
  "view":"view.js",
  "show":"show.js"
}
*/
npm publish

npm i -g frankie-demo-2017-11-19
/*
新加了两行
/usr/local/bin/show -> /usr/local/lib/node_modules/frankie-demo-2017-11-19/show.js
/usr/local/bin/view -> /usr/local/lib/node_modules/frankie-demo-2017-11-19/view.js
*/
// 然后就可以全局使用 show 和 view 了

npm uninstall -g frankie-demo-2017-11-19  // 卸载掉 demo
```

https://www.npmjs.com/package/frankie-demo-2017-11-19