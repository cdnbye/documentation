
## 简介
信令服务器用于在WebRTC建立P2P连接过程中交换信令。

## 自行部署
推荐自行部署信令服务器，以下为开源参考：
- [node-signaler](https://github.com/cdnbye/node-signaler) (nodejs版，推荐)
- [gosignaler](https://github.com/cdnbye/gosignaler) (Go语言版)

## 第三方免费信令服务
- 'wss://opensignal.cdnbye.com' （香港节点）
- 'wss://signalcloud.cdnbye.com:9002' （中国大陆节点）

## 使用示例
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