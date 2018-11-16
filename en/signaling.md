## Introduction
To communicate with another peer you simply need to exchange contact information and the rest will be done by WebRTC. The process of connecting to the other peers is also known as signaling.

## Deploy by Yourself
Deploy your own Signaling Server (Recommended)
- [gosignaler](https://github.com/cdnbye/gosignaler) (written by Golang)
- [php-signaler](https://github.com/cdnbye/php-signaler) (written by PHP)
    
## Use free Signaling Server
- 'wss://signal.cdnbye.com/wss' (Default signaling server)

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