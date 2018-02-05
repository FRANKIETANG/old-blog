---
title: deepin shadowsocks-qt5 全局翻了个墙
date: 2017-10-25 00:58:53
tags: [fq]
---
# deepin shadowsocks-qt5 全局翻了个墙

最近真的是非常时期 `git push` `git pull` `git clone` 全部显示了

Connection closed by 192.30.255.113 port 22

以下是解决办法

```
sudo apt-get install shadowsocks-qt5
sudo apt-get install proxychains
// 配置 /etc/proxychains.conf
// 不要 socks4 127.0.0.1 9095 加上
socks5 127.0.0.1 1080
// 然后在每一条命令都加上
proxychains
```

关于怎么配 shadowsocks-qt5 网上一搜一大把，祝各位看到这篇文章能早日肉身翻墙吧。