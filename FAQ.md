
## 基础
### CDNBye的P2P服务安全吗？
CDNBye采用的WebRTC data channel技术，是基于SCTP协议和TLS加密的，无需担心数据传输的
安全问题。另外，与后台服务器的通信（包括tracker和信令服务器）是基于安全的HTTPS和WSS。隐私方面，CDNBye不会在服务器缓存任何视频内容相关的信息，视频地址也做了加密处理。
 
### 使用CDNBye的P2P服务需要付费吗？
目前CDNBye已经开始商业化，但同时对小型流媒体网站仍提供免费服务。收费模式如下：
- 只对在控制台(https://oms.cdnbye.com) 绑定的域名提供P2P服务
- 每个账号每日免费赠送5GB P2P流量，次日重置
- 每日在控制台完成签到后再赠送20GB P2P流量，次日清零
- 超出免费额度后停止P2P服务，如果继续使用，需要购买流量包，购买的流量包额度越大，价格越划算，且可以使用更多P2P流量，直到剩余可用流量为零。
- 目前只针对Web端插件收费，iOS和安卓SDK完全免费

### 如何购买流量包？
登录`https://oms.cdnbye.com`，点击左边侧滑栏的`购买流量包`，目前支持支付宝和Paypal两种付款方式，其中支付宝以人民币付款，Paypal以美元付款。
 
### 如果绑定了多个域名/AppId，购买流量包后怎么扣费呢？
如果您在同一个账号同时绑定了多个域名/APP，则开启P2P的所有域名/APP会共享流量包。您可以在控制台的`P2P配置`关闭某个域名的P2P服务从而节省流量包消耗。

### 购买了包月套餐后每日额度不够用怎么办？
建议再买个流量包作为补充，系统会先扣除包月套餐流量，扣完再扣除流量包流量。

### P2P服务停止后会影响视频的正常播放吗？
不会。视频仍会正常播放，只是没有P2P加速效果。

### 如何查看集成后有没有效果？
- 确保已经打开2个以上播放同一个m3u8的客户端
- 确保按文档提供的步骤集成，并且SDK没有任何报错信息
- 打开控制台切换对应域名即可查看p2p流量
- 每个平台的SDK都会提供API来监听p2p流量等统计信息，请参考对应的文档说明

### 对接入P2P服务的网站/APP有什么要求吗？
CDNBye不对色情等非法内容提供P2P服务，请确保您的网站/APP符合法律规范。

### CDNBye会不会造成用户视频播放延迟？
不会。首先，视频的首片数据总是从CDN下载的，因而不会造成首屏延迟。其次，如果在超时时间内无法从其他节点获取数据，那么内置的调度算法会及时切到CDN下载，因此本P2P技术不会带来任何额外的延迟。

### 官方的信令服务好像高峰期不太稳定？
建议不使用默认的信令服务，可以自行部署信令服务或者用第三方免费信令服务，请参考[这里](/signaling.md)。

### 使用过程中出现异常如何反馈？
如果是Web端插件的异常请在p2p配置中开启log并截图浏览器console出错信息发送到service@cdnbye.com，或者在QQ群里反馈。如果是原生SDK出现异常请在p2p配置中开启log并保存日志文件发送到官方邮箱，建议在APP中集成[Bugly](https://bugly.qq.com)，有助于收集crash日志并上传到云端。

### 如何在内网使用CDNBye？
使用CDNBye的p2p服务需要调用两个云端服务，一个是Tracker调度服务，另一个是信令服务。
##### 信令服务
可以直接在内网服务器上部署[信令服务](https://github.com/cdnbye/cbsignal)，信令不需要与外网通信。
##### Tracker服务
Tracker需要通过能与外网通信的服务器进行转发，操作步骤如下：
- 在控制台绑定网站域名
- 联系CDNBye技术人员对转发域名进行授权
- 在服务器配置nginx内网转发，在conf.d增加tracker.conf，示例如下：
    ```bash
    server {
        listen 80;
        listen 443 ssl;
        server_name YOUR_TRACKER_DOMAIN;          # 配置内网转发域名
        access_log /root/log/access.log;
        error_log /root/log/error.log;
    
        # ssl on;
        ssl_certificate  tracker.pem;    # 证书路径
        ssl_certificate_key tracker.key;  # 证书秘钥路径
    
        location / {
            proxy_pass https://tracker.cdnbye.com;      # 转发到CDNBye的tracker服务
            proxy_set_header  Host  $host;
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header  X-Forwarded-For $remote_addr;  
        }
    }
    ```
    同时在nginx.conf添加如下配置：
    ```bash
    worker_rlimit_nofile 1000000;   # 配置Nginx worker进程最大打开文件数
    events {
        worker_connections 102400;  # 单个进程允许的客户端最大连接数
    }
    ```
- 修改前端代码，替换Tracker域名为转发域名

### CDNBye的效果如何？如果使用过程效果不如预期，应该怎么调整？
如果您使用过程中无法达到预期效果，请参照[P2P优化](/m3u8.md)对各参数进行调整，或者联系本平台获取技术支持。

### CDNBye的P2P技术会影响DRM(数字版权管理)吗?
不会。只有在用户获得发布者的服务器授权后才会激活P2P传输。而且，流秘钥是不会在P2P网络中
传输的。P2P网络中传输的ts文件和用户从CDN下载的完全一样。另外，播放的内容不会上传或者缓存在我们的服务器中。

### 平时在线人数不多有必要用P2P吗？
有。P2P的独特优势是带宽"削峰"，平时在线人数不多的时候效果不明显，一旦在观看高峰期带宽突然大幅增长，随着在线人数增多P2P分享率指数式上升，可有效降低带宽峰值，避免由于带宽不足导致用户播放卡顿等问题。

## Web SDK
### Web SDK的原理是什么？
CDNBye采用WebRTC data channel技术和BT算法来实现直播和点播的P2P加速。通过内置的调度算法，
在P2P和CDN之间进行无缝切换，从而在不影响用户播放体验的前提下最大化P2P率。hlsjs-p2p-engine
是CDNBye为播放HLS流而开发的hls.js插件。目前，CDNBye已经支持大部分的HTML5播放器，包括
Hls.js, JWPlayer, Video.js, Clappr, Flowplayer和TCPlayer等。

### 如果用户的浏览器不支持WebRTC，会如何处理？
在这种情况下，插件不起任何作用，用户和往常一样采用HTTP方式下载。

### 目前支持的移动端浏览器有哪些？
- QQ浏览器
- 谷歌浏览器<br>
- 火狐浏览器<br>
- 欧朋浏览器<br>
- 微信浏览器<br>
- QQ内置浏览器<br>
- 猎豹浏览器<br>
- 搜狗浏览器
- 百度浏览器
- 360极速浏览器<br>
- 2345浏览器<br>
- 遨游5浏览器<br>
- Via浏览器
- X浏览器
- 先锋浏览器
<br>
注：以上所列出均是安卓平台，目前iOS平台还未支持，不断完善中...

### 某些移动端浏览器无法正常播放？
目前已知安卓端浏览器不能播放的有:
- 百度APP内置浏览器
<br><br>
可以通过`UserAgent`识别出这些浏览器，然后让原生播放器来接管播放，实例代码如下：
```javascript
if (navigator.userAgent.match(/iPad|iPhone|iPod|Baidu|UCBrowser/i)) {
    // 采用原生浏览器播放
} else {
    // 采用CDNBye播放
}
```

### iOS浏览器是否不支持P2P？
由于iOS的浏览器内核不支持MSE(Media Source Extension)，因此所有iOS系统的手机均不支持P2P，这也是其他Web P2P方案共同的局限性，建议使用原生APP集成iOS端SDK来实现P2P。

### Web端插件P2P无效问题排查步骤
- 同时打开2个以上网页播放同一个视频
- 视频播放后打开网页调试窗口查看debug信息
- 如果有红色的报错信息一般是代码逻辑问题，请根据提示修改
- 如果显示类似 "CDNBye 0.X.X is deprecated, please update to latest version(0.X.X已停止支持，请更新至最新版本)" 说明版本已过期，请升级至最新版
- 如果显示其它信息，请根据提示操作，有以下四种可能：
    - 域名没有绑定，注意CDNBye后台是根据HTTP请求的Origin字段来获取域名的（如果使用iFrame该域名可能和网站域名不一致），请在后台绑定正确的域名
    - 流量已经用完，请登录[控制台](https://oms.cdnbye.com)并切换到对应域名查看流量使用情况
    - 在控制台的`P2P配置`中关闭了P2P服务，重新开启即可
    - 域名被加入黑名单，登录控制台点击`域名信息`即可确认
 
## 客户端SDK
### 安卓SDK可以用于机顶盒吗？
当然可以，只要是安卓5.0以上系统的机顶盒都支持。经过实测，exoplayer的兼容性最好，推荐采用。

### iOS SDK可以用于APPLE TV吗？
暂时还不兼容APPLE TV。

### SDK除了 P2P 还有其他附加功能吗？
有的。SDK还具备智能缓存能力，用于避免文件重复下载问题，从而有效节省带宽消耗，而且本功能是完全免费的。

### 客户端SDK P2P无效问题排查步骤
- iOS
    - 在控制台搜索关键字"CDNBye warning"，根据对应的警告信息进行配置
    - 如果仍然无法排查问题，请先将日志打印功能打开，示例代码如下：
    ```objc
    CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
    config.logLevel =  CBLogLevelDebug;
    [[CBP2pEngine sharedInstance] startWithToken:YOUR_TOKEN andP2pConfig:config];
    ```
    - 然后将日志信息保存到文件并发送给CDNBye技术人员
- 安卓
    - 先打开日志打印功能，示例代码如下：
    ```java
    P2pConfig config = new P2pConfig.Builder().logEnabled(true).build();
    P2pEngine.initEngine(this, YOUR_TOKEN, config);
    ```
    - 在控制台搜索"CDNBye"，根据对应的警告信息进行配置
    - 如果仍然无法排查问题，请先将日志级别调整为Debug，示例代码如下：
    ```java
    P2pConfig config = new P2pConfig.Builder().logEnabled(true).logLevel(LogLevel.DEBUG).build();
    P2pEngine.initEngine(this, YOUR_TOKEN, config);
    ```
    - 然后将日志信息保存到文件并发送给CDNBye技术人员

### 解决Exoplayer无法播放问题
Exoplayer默认[不支持跨协议重定向](https://github.com/google/ExoPlayer/issues/1190)，因此需要在初始化时设置`allowCrossProtocolRedirects=true`，如以下示例：
```java
DataSource.Factory dataSourceFactory =
    new DefaultHttpDataSourceFactory(
            Util.getUserAgent(this, "p2p-engine"),
            DefaultHttpDataSource.DEFAULT_CONNECT_TIMEOUT_MILLIS,
            DefaultHttpDataSource.DEFAULT_READ_TIMEOUT_MILLIS,
            true   /* allowCrossProtocolRedirects */
    );
```
具体用法可以参考[官方demo](https://github.com/cdnbye/android-p2p-engine)。

### 解决安卓机顶盒OOM问题
配置比较低的机顶盒容易出现OOM，可以通过配置提高堆内存大小，在`app/src/main`目录中的`AndroidManifest.xml`的<application>标签中直接插入：
```xml
<application
  ...
  android:largeHeap=“true”
  ...
    />
```
