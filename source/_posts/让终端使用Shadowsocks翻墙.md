---
title: Mac系统让终端使用Shadowsocks翻墙
date: 2016/05/02
update: 2016/07/13
---

# 前言 #
在OS X上终端不会使用设置好的SOCKS5系统代理，那要如何才能让终端也能翻墙呢？
搜索了一些解决方案，有推荐proxychains-ng的，但是在EI Capitan上proxychains-ng会受到苹果的限制，参考[使用proxychains-ng的教程](https://eliyar.biz/code/proxy-for-mac-terminal/)。
最终我采取方案是使用privoxy先将Socks代理转化为Http代理之后，然后在终端中配置代理服务器来解决。

# 实现步骤 #
参考[mac osx 下面shadowsocks 转换成http代理](http://www.fyhqy.com/post-383.html)
按照上面参考文档中的操作，我们已经用privoxy生成了一个127.0.0.1:8118的Http代理，让终端使用这个代理即可实现翻墙。
1. `$ export http_proxy='http://127.0.0.1:8118'`
2. `$ export https_proxy='http://127.0.0.1:8118'`


