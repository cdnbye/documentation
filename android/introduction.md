### Android SDK :id=head
> 安卓手机&机顶盒省流量加速神器，与高昂的CDN流量费Say Goodbye!

<a href="https://android-arsenal.com/api?level=21"><img src="https://img.shields.io/badge/API-19%2B-brightgreen.svg?style=flat" alt="api"></a>
<a href="https://github.com/cdnbye/android-p2p-engine"><img src="https://img.shields.io/github/stars/cdnbye/android-p2p-engine.svg?style=social&label=Star" alt="stars"></a>

### 安卓端P2P流媒体加速引擎
P2P技术使观看相同内容的用户之间可以相互分享数据，不仅能效降低视频/直播的带宽成本，还可以提升用户的播放体验，降低卡顿、二次缓存的发生率。本SDK传输能力基于WebRTC Datachannel，可以与CDNBye的[Web端插件](https://github.com/cdnbye/hlsjs-p2p-engine)互联互通，大大提高了P2P网络中的节点数量，打破了浏览器与移动端APP的隔阂，实现了真正意义上的全平台流媒体加速。只需要几行代码即可快速集成到现有项目中，配置自由度高，支持任何安卓播放器，支持安卓手机和机顶盒。SDK还具备智能缓存能力，用于避免文件重复下载问题，从而有效节省带宽消耗。

### 特性
- 限时免费
- 可与CDNBye [Web端](https://github.com/cdnbye/hlsjs-p2p-engine)和[iOS端](https://github.com/cdnbye/ios-p2p-engine)SDK互联互通
- 支持基于HLS流媒体协议(m3u8)的直播和点播场景
- 支持加密HLS传输
- 支持ts文件缓存从而避免重复下载
- 几行代码即可在现有项目中快速集成
- 支持任何安卓播放器
- 通过预加载形式实现P2P加速，完全不影响用户的播放体验
- 高可配置化，用户可以根据特定的使用环境调整各个参数
- 通过有效的调度策略来保证用户的播放体验以及p2p分享率
- Tracker服务器根据访问IP的ISP、地域等进行智能调度

### 系统要求
安卓4.4以上版本(API level >= 19)

### 获取Token
参考[如何获取token](/bindings.md?id=绑定-app-id-并获取token)

### 技术支持
移动端SDK技术支持QQ群：`901641535`