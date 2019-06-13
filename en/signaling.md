## Introduction
To communicate with another peer you simply need to exchange contact information and the rest will be done by WebRTC. The process of connecting to the other peers is also known as signaling. Please note that peers with different signaling address can not connect to each other. 

## Deploy by Yourself
Deploy your own Signaling Server (Recommended)

<!--
- [node-signaler](https://github.com/cdnbye/node-signaler) (Written by nodejs, recommended)
-->
- [gosignaler](https://github.com/cdnbye/gosignaler) (Written by Golang, stand-alone version)
- [gosignaler-cluster](https://github.com/cdnbye/gosignaler-cluster) (Written by Golang, cluster version)
 

## Use free Signaling Server
- 'wss://opensignal.cdnbye.com'  (China mainland)
- 'wss://signalcloud.cdnbye.com:9002'  (China mainland)

## Example
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