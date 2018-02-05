---
title: 做一个音乐 Webapp 的流程
date: 2017-10-15 12:41:39
tags: [JavaScript]
---
# 做一个音乐 Webapp

超低仿 Vanilla JS 写的 QQ 音乐

[预览](https://frankietang.github.io/qq-music/index.html) | [源码](https://github.com/FRANKIETANG/qq-music)

那么乱的笔记估计就只有我才能看懂

- 按需求制定一下接口
- 功能拆分成一个一个模块（首页 + 推荐 / 排行榜 / 搜索 / 播放器界面）
- 技术选型（CSS 预处理器 / JS 库）

## 怎么在网页抄数据

![](https://i.loli.net/2017/09/22/59c4990ac3f33.png)

## 伪造请求

![](https://i.loli.net/2017/09/23/59c5f31f6601d.png)

```
curl 'https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg?g_tk=5381&uin=0&format=json&inCharset=utf-8&outCharset=utf-8&notice=0&platform=h5&needNewCode=1&_=1507564199109' -H 'pragma: no-cache' -H 'origin: https://m.y.qq.com' -H 'accept-encoding: gzip, deflate, br' -H 'accept-language: zh-CN,zh;q=0.8' -H 'user-agent: Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1' -H 'accept: application/json' -H 'cache-control: no-cache' -H 'authority: c.y.qq.com' -H 'cookie: pgv_pvi=1576136704; pgv_si=s2924205056; RK=QfWPx2jbHN; tvfe_boss_uuid=bdd869ba21d19595; o_cookie=350558468; ts_refer=ADTAGmyqq; ptui_loginuin=350558468; ptisp=ctc; ptcz=bde020f9828475fc3e22f0fc78ba0024b7a615fad803b91694a069a02cefdb0b; pt2gguin=o0350558468; LW_sid=11S540S7L3v4m1k4L632K6r1m3; LW_uid=g1l53027M384N1y4K6Z2v6d1L4; eas_sid=H1Y5t0B7H3o461B4f692f682Y2; ts_uid=2559117424; qqmusic_fromtag=10; checkmask=3; yqq_stat=0; ts_refer=www.google.ca/; ts_uid=2559117424; pgv_info=ssid=s8871552255; pgv_pvid=2779555285' -H 'referer: https://m.y.qq.com/' --compressed
```

`npm install express --save` 

`npm install request --save` `npm install request-promise --save`（发请求的库）

[request-promise](https://github.com/request/request-promise)

![](https://i.loli.net/2017/09/23/59c60819cabdf.png)

`npm install -g nodemon`

[nodemon](https://nodemon.io/)

我去...用 nodemon 不能用 `import XXX from 'XXX'` 要用 `var XXX = require('XXX')`

![](https://i.loli.net/2017/09/23/59c60b0dcf706.png)

我去...原来还有 n 模块这玩意...

[n](https://www.npmjs.com/package/n) 接受了这个设定还是挺不错的...

```
//qq-server 代码
const express = require('express')
const request = require('request-promise')

const app = express()

app.get('/', async (req, res) => {
    const url = `https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg?g_tk=5381&uin=0&format=json&inCharset=utf-8&outCharset=utf-8&notice=0&platform=h5&needNewCode=1&_=${+ new Date()}`
    try {
        res.json(
            await request({
                uri: url,
                json: true,
                headers: {
                    'accept': 'application/json',
                    'authority': 'c.y.qq.com',
                    'origin': 'https://m.y.qq.com',
                    'referer': 'https://m.y.qq.com/',
                    'user-agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1'
                }
            })
        )
    } catch (e) {
        res.json({ error: e.message })
    }
})

app.listen(4000)
```

运行 `nodemon qq-server.js` 打开 localhost:4000

![](https://i.loli.net/2017/09/23/59c6163e42f5b.png)

开心 终于不用跨域请求了 也不用自己去抄数据了hhhh 新技能 get

再分析一个 API 接口

```
https://c.y.qq.com/soso/fcgi-bin/search_for_qq_cp?g_tk=5381&uin=0&format=json&inCharset=utf-8&outCharset=utf-8&notice=0&platform=h5&needNewCode=1&w=%E6%9D%8E%E8%8D%A3%E6%B5%A9&zhidaqu=1&catZhida=1&t=0&flag=1&ie=utf-8&sem=1&aggr=0&perpage=20&n=20&p=1&remoteplace=txt.mqq.all&_=1506154238572
```

`w=%E6%9D%8E%E8%8D%A3%E6%B5%A9` 这个是李荣浩

![](https://i.loli.net/2017/09/23/59c618bbb79d2.png)

```
//search部分的核心代码
app.get('/search', async(req,res)=>{
    const { keyword, page = 1 } = req.query
    const url=`https://c.y.qq.com/soso/fcgi-bin/search_for_qq_cp?g_tk=5381&uin=0&format=json&inCharset=utf-8&outCharset=utf-8&notice=0&platform=h5&needNewCode=1&w=${encodeURIComponent(keyword)}&zhidaqu=1&catZhida=1&t=0&flag=1&ie=utf-8&sem=1&aggr=0&perpage=20&n=20&p=${page}&remoteplace=txt.mqq.all&_=${+ new Date()}`
    try {
        res.json(
            await request({
                uri: url,
                json: true,
                headers: HEADERS
            })
        )
    } catch (e) {
        res.json({ error: e.message })
    }    
})
```

![](https://i.loli.net/2017/09/23/59c6200b767cb.png)

所有参数都放在 ? 后面 用 & 链接

你看真的成了

那就是说...我自己做了一个 API ...

让这个 API 跨域 `npm install cors --save`

https://zeit.co/now 把做出来的 server.js 上传就可以用了

## 模块化

import export

## 音乐歌词 API

![](https://i.loli.net/2017/09/25/59c8db302e050.png)

点击 network 看 JS

把这个地址复制，在 console 用 `fetch()` 跑一遍

![](https://i.loli.net/2017/09/25/59c8dbc2a1f79.png)

再回到 network 看 XHR

![](https://i.loli.net/2017/09/25/59c8dc2dd23db.png)

写一个正则，把 callback 去掉，括号去掉。

`MusicJsonCallback({...}).replace(/MusicJsonCallback\((.*)\)/, '$1')`

`let json = {...} `

`JSON.parse(json)`

`json.lyric`

`let div = document.createElement('div')`

`div.innerHTML = json.lyric`

`div.firstChild.nodeValue`

## hash

单页面保存数据的方式

就是当你打开这个页面就可以直接转跳到那首歌的入口

`href="#player?artist=${artist}&songid=${song.songid}&songname=${song.songname}&albummid=${song.albummid}&duration=${song.interval}"`


## 知识点

fetch / await async

`[].slice.call()`

IntersectionObserver

`map` 和 `forEach` 的区别

懒加载 / 曝光加载

正则

```
'View frankietang on GitHub'.match(/View (\w+) on GitHub/)[1]
'frankietang'
```

css 里面的 `filter:blur(15px)` 毛玻璃效果

进度条可以先移到最边边，然后慢慢的往右移。

`max-height: calc(100% - 205px);`

`location.hash` 点击时 url 的变化

![](https://ooo.0o0.ooo/2017/09/28/59ccf1d9533c4.png)

MVC -- Model View Controller

autoprefixer-cli

[NonDocumentTypeChildNode.previousElementSibling](https://developer.mozilla.org/zh-CN/docs/Web/API/NonDocumentTypeChildNode/previousElementSibling)