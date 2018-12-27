
## 简介
CDNBye通过WebRTC datachannel技术和BT算法，在观看同一视频/直播的用户之间构建P2P网络，在节省带宽成本的同时，提升用户的播放体验。

采用本插件的前提是浏览器支持WebRTC (Chrome, Firefox, Opera, Safari)。

## 在Hls.js增加的新API

### `Hls.engineVersion` (static method)
当前插件的版本号

### `Hls.WEBRTC_SUPPORT` (static method)
判断当前浏览器是否支持WebRTC
```javascript
if (Hls.WEBRTC_SUPPORT) {
  // WebRTC is supported
} else {
  // Use a fallback
}
```

## 实例化与参数配置
### `var hls = new Hls({p2pConfig: [opts]});`  
创建一个新的`Hls`实例。

### `var engine = hls.p2pEngine;`
从`Hls`实例中获取`P2PEngine`实例。

如果指定了`opts`，那么对应的默认值将会被覆盖。

| 字段 | 类型 | 默认值 | 描述 |
| :-: | :-: | :-: | :-: |
| `logLevel` | string or boolean | 'none' | log的等级，分为debug、info、warn、error、none，设为true等于debug，设为false等于none。
| `live` | boolean | true | 直播或者点播模式，建议在点播模式下设为false，p2p插件会预缓存buffer以避免卡顿。
| `wsSignalerAddr` | string | 'wss://signal.cdnbye.com/wss' | 信令服务器地址。
| `wsMaxRetries` | number | 3 |websocket连接重试次数。
| `wsReconnectInterval` | number | 5 | websocket重连时间间隔。
| `loadTimeoutRate` | number | 0.7 | p2p下载的超时时间比率，用于计算超时时间。
| `maxBufferSize` | Object | {"pc": 1024 * 1024 * 200, "mobile": 1024 * 1024 * 100} | p2p缓存的最大数据量，mobile字段暂未实现。
| `p2pEnabled` | boolean | true | 是否开启P2P。
| `channelId` | function | - | 标识channel的字段，同一个channel的用户可以共享数据。（参考高级用法）
| `segmentId` | function | - | 标识ts文件的字段，防止相同ts文件具有不同的路径。（参考高级用法）
| `packetSize` | number | 64 * 1024 | 每次通过datachannel发送的包的大小，64KB适用于较新版本的浏览器，如果要兼容低版本浏览器可以设置成16KB。
| `webRTCConfig` | Object | {} | 用于配置stun和datachannel的[字典](https://github.com/feross/simple-peer)。


## P2PEngine API

### `var engine = new P2PEngine(hlsjs, p2pConfig);`
实例化`P2PEngine`。也可以从`Hls`实例获取`P2PEngine`实例：
```javascript
var hls = new Hls();
var engine = hls.p2pEngine;
```

### `engine.version`
获取`P2PEngine`的版本号。

### `engine.isSupported()`
判断当前浏览器是否支持WebRTC data channel。

### `engine.enableP2P()`
在p2p暂停或未启动情况下启动p2p。

### `engine.disableP2P()`
停止p2p并释放内存。

### `engine.destroy()`
停止p2p、销毁engine并释放内存。在Hls.js销毁时会自动调用。

## P2PEngine事件

### `engine.on('peerId', function (peerId) {})`
当从服务端获取到peerId时回调该事件。

### `engine.on('peers', function (peers) {})`
当与新的节点成功建立p2p连接时回调该事件。

### `engine.on('stats', function (stats) {})`
该回调函数可以获取p2p信息，包括：</br>
stats.totalHTTPDownloaded: 从HTTP(CDN)下载的数据量（单位KB）</br>
stats.totalP2PDownloaded: 从P2P下载的数据量（单位KB）</br>
stats.totalP2PUploaded: P2P上传的数据量（单位KB）

## 高级用法
### 解决动态m3u8路径问题
某些流媒体提供商的m3u8是动态生成的，不同节点的m3u8地址不一样，例如example.com/clientId1/file.m3u8和example.com/clientId2/file.m3u8,
而本插件默认使用m3u8作为channelId。这时候就要构造一个共同的chanelId，使实际观看同一直播/视频的节点处在相同频道中。
```javascript
p2pConfig: {
    channelId: function (m3u8Url) {
        const formatedUrl = format(m3u8Url);   // 忽略差异部分，构造一个一致的channelId
        return formatedUrl;
    }
}
```

### 解决动态ts路径问题
类似动态m3u8路径问题，相同ts文件的路径也可能有差异，这时候需要忽略ts路径差异的部分。插件默认用ts的序号来标识每个ts文件，所以已经帮开发者解决了这个问题。如果想严格按ts路径匹配，可以按如下设置：
```javascript
p2pConfig: {
    segmentId: function (level, sn, tsUrl) {
        // const formatedUrl = `${level}-${sn}`;  // 默认实现
        const formatedUrl = tsUrl;  // 将ts文件的URL作为唯一标识的ID
        return formatedUrl;
    }
}
```

### 配置STUN服务器
```javascript
p2pConfig: {
    webRTCConfig: { 
        config: {         // custom webrtc configuration (used by RTCPeerConnection constructor)
            iceServers: [
                { urls: 'stun:stun.l.google.com:19302' }, 
                { urls: 'stun:global.stun.twilio.com:3478?transport=udp' }
            ] 
        }
    }
}
```
