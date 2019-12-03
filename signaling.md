
## 简介
信令服务器用于在WebRTC建立P2P连接过程中交换信令。
<br>
注意：不同信令服务器的节点即使播放同一个视频也无法进行P2P。

## 自行部署
推荐自行部署信令服务器，以下为开源参考：
<!--
- [node-signaler](https://github.com/cdnbye/node-signaler) (nodejs版，推荐)
-->
- [gosignaler](https://github.com/cdnbye/gosignaler) (Go语言编写，单机版)
- [gosignaler-cluster](https://github.com/cdnbye/gosignaler-cluster) (Go语言编写，集群版)


## 第三方免费信令服务
- 'wss://signal.cdnbye.com' (韩国节点，默认)
- 'wss://opensignal.cdnbye.com' （美国节点）
- 'wss://signalcloud.cdnbye.com' （德国节点）
- 'wss://opensignal.320072.top'  (中国节点，由猫云赞助)

## 使用示例

### Web SDK
```javascript
var hlsjsConfig = {
    p2pConfig: {
        wsSignalerAddr: 'ws://your.signalservice.com',
        // Other p2pConfig options provided by hlsjs-p2p-engine
    }
};
// Hls constructor is overriden by included bundle
var hls = new Hls(hlsjsConfig);
// Use `hls` just like the usual hls.js ...
```

### 安卓SDK
```java
P2pConfig config = new P2pConfig.Builder()
    .wsSignalerAddr("ws://your.signalservice.com")
    .build();
P2pEngine.initEngine(ctx, YOUR_TOKEN, config);
```

### iOS SDK
```objectivec
CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
config.wsSignalerAddr = @"ws://your.signalservice.com";
self.engine = [[CBP2pEngine alloc] initWithToken:YOUR_TOKEN andP2pConfig:config];
```