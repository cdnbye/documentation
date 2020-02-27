### dash.js p2p插件 :id=head
> 支持MPEG-dash格式的直播和点播，与高昂的CDN流量费 Say Goodbye!

<a href="https://www.npmjs.com/package/cdnbye-dash"><img src="https://img.shields.io/npm/v/cdnbye-dash.svg?style=flat" alt="npm"></a>
<a href="https://www.jsdelivr.com/package/npm/cdnbye-dash"><img src="https://data.jsdelivr.com/v1/package/npm/cdnbye-dash/badge" alt="jsdelivr"></a>
<a href="https://github.com/cdnbye/dashjs-p2p-engine/tree/master/dist"><img src="https://badge-size.herokuapp.com/cdnbye/dashjs-p2p-engine/master/dist/dashjs-p2p-engine.min.js?compression=gzip&style=flat-square" alt="size"></a>

该插件的优势如下：
- 浏览器原生支持，不需要安装任何插件，采用仿BT算法，在线人数越多效果越好
- 支持基于MPEG-dash流媒体协议的直播和点播场景
- 不改动dash.js源码，并且可以与其无缝衔接，几行代码集成，便于在现有项目中快速集成
- 浏览器不支持WebRTC时无缝切换到HTTP下载模式
- 高可配置化，用户可以根据特定的使用环境调整各个参数
- 支持video.js、Clappr、Flowplayer、DPlayer等第三方播放器
- 通过有效的调度策略来保证用户的播放体验以及p2p率
- Tracker服务器根据访问IP的ISP、地域等进行智能调度
- API已经固化，新版本完全兼容旧版本代码

### 项目地址
使用方法及API文档请点击[这里](https://github.com/cdnbye/dashjs-p2p-engine/blob/master/Readme_zh.md)

### 后台管理系统
在接入P2P插件后，访问`https://oms.cdnbye.com`，注册并绑定域名，即可查看该域名的P2P流量、在线人数、用户地理分布等信息。

### 联系我们
邮箱：service@cdnbye.com