## Introduction

CDNBye implements [WebRTC](https://en.wikipedia.org/wiki/WebRTC) datachannel to scale live/vod video streaming by peer-to-peer network using bittorrent-like protocol.

To use CDNBye hlsjs-p2p-engine, WebRTC support is required (Chrome, Firefox, Opera, Safari).

## Use Hls.js wrapped with P2PEngine

### `Hls.engineVersion` (static method)
Show the current version of CDNBye plugin.

### `Hls.WEBRTC_SUPPORT` (static method)
Is WebRTC natively supported in the environment?
```javascript
if (Hls.WEBRTC_SUPPORT) {
  // WebRTC is supported
} else {
  // Use a fallback
}
```

## Create instance

### `var hls = new Hls({p2pConfig: [opts]});` 
Create a new `Hls` instance.

### `var engine = hls.p2pEngine;`
Get the `P2PEngine` instance from `Hls` instance.

If `opts` is specified, then the default options (shown below) will be overridden.

| Field | Type | Default | Description |
| :-: | :-: | :-: | :-: |
| `logLevel` | string or boolean | 'none' | Print log level(debug, info, warn, error, none，false=none, true=debug).
| `live` | boolean | true | tell engine whether in live or VOD mode, set to false will pre-buffer for smooth playing.
| `wsSignalerAddr` | string | 'wss://signal.cdnbye.com/wss' | The address of signal server.
| `wsMaxRetries` | number | 15 | The maximum number of reconnection attempts that will be made by websocket before giving up.
| `wsReconnectInterval` | number | 30 | The number of seconds to delay before attempting to reconnect by websocket.
| `maxBufferSize` | Object | {"pc": 1024 * 1024 * 300, "mobile": 1024 * 1024 * 150} | The max size of binary data that can be stored in the cache.
| `p2pEnabled` | boolean | true | Enable or disable p2p engine.
| `getStats` | function | - | Get the downloading statistics, including totalP2PDownloaded, totalP2PUploaded and totalHTTPDownloaded.
| `getPeerId` | function | - | Emitted when the peer Id of this client is obtained from server.
| `getPeersInfo` | function | - | Emitted when successfully connected with new peer.
| `channelId` | function | - | Pass a function to generate channel Id.(See advanced usage)
| `segmentId` | function | - | Pass a function to generate segment Id.(See advanced usage)
| `packetSize` | number | 64 * 1024 | The maximum package size sent by datachannel, 64KB should work with most of recent browsers. Set it to 16KB for older browsers support.
| `webRTCConfig` | Object | {} | A [Configuration dictionary](https://github.com/feross/simple-peer) providing options to configure WebRTC connections.
| `validateSegment` | function | - | Pass a function to check segment validity downloaded from peers.

## P2PEngine API

### `var engine = new P2PEngine(hlsjs, p2pConfig);`
Create a new `P2PEngine` instance. Or you can get `P2PEngine` instance from hlsjs:
```javascript
var hls = new Hls();
var engine = hls.p2pEngine;
```

### `engine.version`
Get the version of `P2PEngine`.

### `engine.isSupported()`
Returns true if WebRTC data channel is supported by the browser.

### `engine.enableP2P()`
Resume P2P if it has been stopped.

### `engine.disableP2P()`
Disable P2P to stop p2p and free used resources.

### `engine.destroy()`
Stop p2p and free used resources, it will be called automatically before hls.js is destroyed.  

## P2PEngine Events

### `engine.on('peerId', function (peerId) {})`
Emitted when the peer Id of this client is obtained from server.

### `engine.on('peers', function (peers) {})`
Emitted when successfully connected with new peer.

### `engine.on('stats', function (stats) {})`
Emitted when data is downloaded/uploaded.</br>
stats.totalHTTPDownloaded: total data downloaded by HTTP(KB).</br>
stats.totalP2PDownloaded: total data downloaded by P2P(KB).</br>
stats.totalP2PUploaded: total data uploaded by P2P(KB).

## Advanced Usage
### Another way to get the downloading statistics
```javascript
p2pConfig: {
    getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
        // do something
    }
}
```

### Another way to get peer Id
```javascript
p2pConfig: {
    getPeerId: function (peerId) {
        // do something
    }
}
```

### Another way to get peers information
```javascript
p2pConfig: {
    getPeersInfo: function (peers) {
        // do something
    }
}
```

### Dynamic m3u8 path issue
Some m3u8 urls play the same live/vod but have different paths on them. For example, 
example.com/clientId1/file.m3u8 and example.com/clientId2/file.m3u8. In this case, you can format a common channelId for them.
```javascript
p2pConfig: {
    channelId: function (m3u8Url) {
        const formatedUrl = format(m3u8Url);   // format a channelId by removing the different part
        return formatedUrl;
    }
}
```

### Dynamic ts path issue
Like dynamic m3u8 path issue, you should format a common segmentId for the same ts file. We have da that for you. If you want to set the path as segment ID, override the segmentID like this:
```javascript
p2pConfig: {
    segmentId: function (level, sn, tsUrl) {
        // const formatedUrl = `${level}-${sn}`;  // default implementation
        const formatedUrl = tsUrl;  // the actual path of ts file
        return formatedUrl;
    }
}
```

### Config STUN Servers
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

### How to Check Segment Validity
Sometimes we need to prevent a peer from sending a fake segment
 (such as the bittorrent with a hash function). 
 CDNBye provide a validation callback with buffer of the 
 downloaded segment, developer should implement the actual 
 validator. For example, you can create a program that generates 
 hashes for the segments and stores them in a specific file or 
 injects into m3u8 playlist files the hashes information. If 
 the callback returns false, then the segment is not valid. 
 ```javascript
p2pConfig: {
    validateSegment: function (level, sn, buffer) {
        var hash = hashFile.getHash(level, sn);
        return hash === md5(buffer);
    }
}
```