---
title: 用 Node 来搭建 HTTP 服务器
date: 2017-12-01 23:03:36
tags: [Node,HTTP]
---
# 用 Node 来搭建 HTTP 服务器

https://github.com/FRANKIETANG/simple-http-server

可以点进去看 [commit](https://github.com/FRANKIETANG/simple-http-server/commits/master)

## URL

全称是 Uniform Resource Locator （唯一的资源定位器？），相当于一个地址。

`Schema://Host:Port/Path?query#hash`

> Schema => 协议
>
> Host => 主姓名
>
> Port => 端口号，比如 22 -> ssh | 80 -> http | 443 -> https | mongodb -> 27017
>
> Path => 路径
>
> query => 查询字符串
>
> hash => 哈希

``` 
// 举个例子
const url = `address://nanshan.shenzhen.china/shennandadao/10869`
// 地址是://南山.深圳.中国/深南大道/10869号
// Host:Port 可以写 IP 地址或者域名
// 域名 -> DNS (去 google)
```

### 举一个简单的例子

[vscode 断点调试](https://segmentfault.com/a/1190000009084576)

最好用 **postman**，浏览器好像只支持 get 请求。

```
const http = require('http') // http 模块是一定的
const server = http.createServer()
server.listen(8282)
const querystring = require('querystring') // 引入 querystring 模块用来看 url 的 query

server.on('request', (request, response) => { // 受到请求后调用一次
    // console.log(request.url)  // 这里会打印出 /，实际上是 url Path 后的东西，如果我在 loaclhost:8282 后面写上 show-me-something，后台就会返回 /show-me-something，再加点东西也是同理的
    const url = request.url

    const queryString = url.substr(url.indexOf('?') + 1, url.length)

    const query = querystring.parse(queryString)

    console.log(query)

    // console.log(url)

    let responseStr // 定义返回字符串，收到 url 做出不同的返回值

    if (url.indexOf('/hello') > -1) { // 记得加前缀 /
        responseStr = 'hi there'
        if (query.i_need_money === 'true' && Number(query.how_much) > 500) {  // 这里做一个判断他是不是要钱，值得一提的是这里这个 'true'，因为这里是没有类型的所以要用这个 'true'
            responseStr = 'go away'
        } else {
            responseStr = 'ok, here you are'
        }
    } else if (url.indexOf('/bye') > -1) {  // 也可以用正则，不过如果写不好就会比 indexOf 要慢很多很多...
        responseStr = 'see ya next time'
    } else {
        responseStr = 'i cant understand what you are saying'
    }

    response.statusCode = 200
    response.end(responseStr)
    // response.end('this is my first http server')
})
```

```
// postman 的操作
// 后台信息都是靠 console.log

localhost:8282/hello  // GET hi there | 后台 /hello
localhost:8282/bye  // GET see ya next time | 后台 /bye
localhost:8282/  // GET i cant understand what you are saying | 后台 /

// 加上 query
localhost:8282/hello?i_need_money=true  // GET i cant understand what you are saying | 后台 /hello?i_need_money=true

// 引入 querystring 模块后再打印
localhost:8282/hello?i_need_money=true  // GET i cant understand what you are saying | 后台 { i_need_money: 'true' }
localhost:8282/hello?i_need_money=true&how_much=1000  // GET i cant understand what you are saying | 后台 { i_need_money: 'true', how_much: '1000' }

// hello 模块做好判断后
localhost:8282/hello?i_need_money=true&how_much=1000  // GET go away | 后台 { i_need_money: 'true', how_much: '1000' }
localhost:8282/hello?i_need_money=true&how_much=300  // GET ok, here you are | 后台 { i_need_money: 'true', how_much: '300' }
```

## HTTP

以百度为例子

![](https://us1.myximage.com/2017/11/30/10814fc32bca783917e30c6f8245b5e5.png)

>HTTP 请求第一部分（第一行）  `GET /index/ HTTP/1.1`  什么方法 | 什么路径 | 什么 HTTP 版本 
>
>- HTTP 方法 => GET POST PATCH PUT DELETE OPTIONS HEAD
>- 比如 `path: /user get:获取所有用户 | post:创建用户 | patch:修改用户信息 | put:创建 | delete:删除 | options:列举可进行的操作 | head:返回 head 信息 `
>
>HTTP 请求头 第二行到空行之前 重要的键值对有 Content-Type: 请求体的类型（编码、格式）Content-Length: 请求体的长度  Accept: 能够接收的返回体类型  Cookie: cookie 有多个键值对，中间有个等号，以分号为分隔符
>
>HTTP 请求体和请求头以一个空行作为分隔符
>
>HTTP 第三部分  请求体 http-request / response-body

### 举一个简单的例子

[vscode 断点调试](https://segmentfault.com/a/1190000009084576)

最好用 **postman**，浏览器好像只支持 get 请求。

```
const http = require('http') // http 模块是一定的
const server = http.createServer()
server.listen(8282)
const querystring = require('querystring') // 引入 querystring 模块用来看 url 的 query

const users = [];  // 做一个全局数组

server.on('request', (request, response) => { // 受到请求后调用一次
    // console.log(request.url)  // 这里会打印出 /，实际上是 url Path 后的东西，如果我在 loaclhost:8282 后面写上 show-me-something，后台就会返回 /show-me-something，再加点东西也是同理的
    const url = request.url

    const path = url.substr(0, url.indexOf('?'))

    const queryString = url.substr(url.indexOf('?') + 1, url.length)

    const query = querystring.parse(queryString)

    // console.log(query)
    // console.log(url)
    // console.log(path)

    // 做一个请求例子
    switch (path) {
        case '/user':
            switch (request.method) {
                case 'GET':
                    response.statusCode = 200
                    response.end(JSON.stringify(users))
                    break;
                case 'POST':

                    break
            }
            break
        default:
            response.statusCode = 404
            response.end('NOT_FOUND')
            break
    }

})
```

```
// postman 的操作
// 后台信息都是靠 console.log

localhost:8282/user?frankie=1  // GET [] 因为是第一次请求所以user什么都没有

// 文字太难表达了...看下面动图吧
// 也就是通过post创建的用户
```

![](https://us1.myximage.com/2017/11/30/07c50f626d106f548a94d6a079a69ac6.gif)

```
// 通过发送 json 定义用户
const http = require('http') // http 模块是一定的
const server = http.createServer()
server.listen(8282)
const querystring = require('querystring') // 引入 querystring 模块用来看 url 的 query

const users = [];  // 做一个全局数组

server.on('request', (request, response) => { // 受到请求后调用一次
    // console.log(request.url)  // 这里会打印出 /，实际上是 url Path 后的东西，如果我在 loaclhost:8282 后面写上 show-me-something，后台就会返回 /show-me-something，再加点东西也是同理的
    const url = request.url

    const path = url.substr(0, url.indexOf('?'))

    const queryString = url.substr(url.indexOf('?') + 1, url.length)

    const query = querystring.parse(queryString)

    console.log(query)
    console.log(url)
    console.log(path)

    // 做一个请求例子
    // 其他方法也是一样的
    switch (path) {
        case '/user':
            switch (request.method) {
                case 'GET':
                    response.statusCode = 200
                    response.end(JSON.stringify(users))
                    break;
                case 'POST':
                    const contentType = request.headers['content-type']  // 看请求头的属性

                    if (contentType !== 'application/json') {  // 不是json就400
                        response.statusCode = 400
                        response.end('error')
                    }

                    let requestBodyStr = ''
                    request.on('data', (data) => {
                        requestBodyStr += data.toString()  // 把json变成字符串
                    })
                    request.on('end', () => {
                        const user = JSON.parse(requestBodyStr)  // 解析josn字符串
                        users.push(user)
                        response.statusCode = 200
                        response.end(JSON.stringify(user))
                    })

                    // const user = { name: Math.floor(Math.random() * 100) }
                    // users.push(user)
                    // response.statusCode = 200
                    // response.end(JSON.stringify(user))
                    break
            }
            break
        default:
            response.statusCode = 404
            response.end('NOT_FOUND')
            break
    }

})
```

![](https://us1.myximage.com/2017/11/30/f0f0708a615d98816c5dfac1a6188a49.gif)

### 请求体

请求体的格式、编码通常由请求头里的 Content-type 指定，可能会很大（例如 form 格式可以请求 flie，分分钟 几十兆几百兆）（关键词 Buffer，一次吃不下我就一口一口吃）

```
// 把上面那段代码改一下

const http = require('http') // http 模块是一定的
const server = http.createServer()
server.listen(8282)
const querystring = require('querystring') // 引入 querystring 模块用来看 url 的 query

const users = []; // 做一个全局数组

server.on('request', (request, response) => { // 受到请求后调用一次
    // console.log(request.url)  // 这里会打印出 /，实际上是 url Path 后的东西，如果我在 loaclhost:8282 后面写上 show-me-something，后台就会返回 /show-me-something，再加点东西也是同理的
    const url = request.url

    const path = url.substr(0, url.indexOf('?'))

    const queryString = url.substr(url.indexOf('?') + 1, url.length)

    const query = querystring.parse(queryString)

    console.log(query)
    console.log(url)
    console.log(path)

    // 做一个请求例子
    // 其他方法也是一样的
    switch (path) {
        case '/user':
            switch (request.method) {
                case 'GET':
                    response.statusCode = 200
                    response.end(JSON.stringify(users))
                    break;
                case 'POST':
                    const contentType = request.headers['content-type'] // 看请求头的属性

                    if (contentType !== 'application/json') { // 不是json就400
                        response.statusCode = 400
                        response.end('error')
                    }

                    let requestBodyStr = ''
                    request.on('data', (data) => { // 当这个请求收到数据的时候
                        console.log(data) // 看看 data 
                    })
                    request.on('end', () => { // 当发过来的这个请求体已经结束的时候
                        response.end('done')
                    })
                    break
            }
            break
        default:
            response.statusCode = 404
            response.end('NOT_FOUND')
            break
    }

})
```

然后发点数据

![](https://us1.myximage.com/2017/12/01/e73caf3a3b4d360703165be09147afea.gif)

可以看到是一个 `<Buffer>` 这个其实相当于吃了一小口，那我试试传个大家伙

```
// 再修改一下代码
const http = require('http') // http 模块是一定的
const server = http.createServer()
server.listen(8282)
const querystring = require('querystring') // 引入 querystring 模块用来看 url 的 query

const users = []; // 做一个全局数组

server.on('request', (request, response) => { // 受到请求后调用一次
    // console.log(request.url)  // 这里会打印出 /，实际上是 url Path 后的东西，如果我在 loaclhost:8282 后面写上 show-me-something，后台就会返回 /show-me-something，再加点东西也是同理的
    const url = request.url

    const path = url.substr(0, url.indexOf('?'))

    const queryString = url.substr(url.indexOf('?') + 1, url.length)

    const query = querystring.parse(queryString)

    console.log(query)
    console.log(url)
    console.log(path)

    // 做一个请求例子
    // 其他方法也是一样的
    switch (path) {
        case '/user':
            switch (request.method) {
                case 'GET':
                    response.statusCode = 200
                    response.end(JSON.stringify(users))
                    break;
                case 'POST':
                    const contentType = request.headers['content-type'] // 看请求头的属性

                    // if (contentType !== 'application/json') { // 不是json就400
                    //     response.statusCode = 400
                    //     response.end('error')
                    // }

                    let dataCount = 0
                    let requestBodyStr = ''
                    request.on('data', (data) => { // 当这个请求收到数据的时候
                        dataCount++
                        console.log(data)
                    })
                    request.on('end', () => { // 当发过来的这个请求体已经结束的时候
                        console.log(dataCount) // 看看一个文件要吃多少口
                        response.end(dataCount + '')
                    })
                    break
            }
            break
        default:
            response.statusCode = 404
            response.end('NOT_FOUND')
            break
    }

})
```

然后发个算大的文件（48M）

![](https://us1.myximage.com/2017/12/01/fcc0ec6f09967d75f4e2fdfdd6475138.gif)

也就是说这个大东西吃了 873 口

## 假如用 Express 搭服务器

先看看 bin/www 的代码

```
// 7行和9行
var app = require('../app');  // 定义各种路由
var http = require('http');  // 引入http模块

// 28行
server.listen(port);  // 定义端口号
```

怎么把刚刚我上面实现的效果用 Express 框架做出来呢？

```
// app.js 26行改成
app.use('/user', users);

// 打开 routes 的 users.js，修改成
var express = require('express')
var router = express.Router()

const users = []

/* GET users listing. */
router.route('/')
  .get((req, res, next) => {
    res.json(users)
  })
  .post((req, res)=>{
    const user = req.body
    users.push(user)
    res.json(user)
  })

module.exports = router
```

![](https://us1.myximage.com/2017/12/01/0eda1fdee399f2d5f16acd525d5f469a.gif)

其实框架还是解决了很多自己写东西的细节，像什么判断啊，转码啊啥的。那就可以不用管那么多直接写业务逻辑。

## 彩蛋

### 命令行神器

又发现了个命令行神器..

[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

### 前端发送表单发送的数据-后端处理的方式

[commit - form data](https://github.com/FRANKIETANG/simple-http-server/commit/4366e9a1cc5506c7291b3550aa1ec2843644e817)

![](https://us1.myximage.com/2017/12/01/ca7f44d9c3c761bca5d419204b5a5ff0.gif)

拿到数据了...（请忽略我的 JSON 报错）

[commit - querystring replace JSON](https://github.com/FRANKIETANG/simple-http-server/commit/1fdc106bf64cb242d53008b4f93b5c86b53af408)

![](https://us1.myximage.com/2017/12/01/6a58999accae3fe5bcac42cb90ce1400.gif)

### 如何做请求缓存

就是收到第二次同样的请求返回一样的内容（先留个坑吧...）

- 浏览器缓存
- 服务端缓存