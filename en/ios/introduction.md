

<a href="https://cocoapods.org/pods/CDNByeSDK"><img src="https://img.shields.io/cocoapods/v/CDNByeSDK.svg?style=flat" alt="version"></a>
<a href="https://cocoapods.org/pods/CDNByeSDK"><img src="https://img.shields.io/cocoapods/p/CDNByeSDK.svg?style=flat" alt="platform"></a>
<a href="https://github.com/cdnbye/ios-p2p-engine"><img src="https://img.shields.io/github/stars/cdnbye/ios-p2p-engine.svg?style=social&label=Star" alt="stars"></a>

### iOS Video P2P Engine for Any Player
CDNBye iOS P2P Engine scales live/vod video streaming by peer-to-peer network using bittorrent-like protocol. Powered by WebRTC Datachannel, this SDK can interconnect with the Web side [plug-in](https://github.com/cdnbye/hlsjs-p2p-engine) of CDNBye, which greatly increases the number of nodes in the P2P network, breaking the gap between the browser and mobile application. Merely a few lines of codes are required to quickly integrate into existing projects. As expected, it supports any iOS player!

### Features
- Interconnect with CDNBye [hlsjs-p2p-engine](https://github.com/cdnbye/hlsjs-p2p-engine) and [android-p2p-engine](https://github.com/cdnbye/android-p2p-engine)
- Support live and VOD streams over HLS protocol(m3u8)
- Support encrypted HLS stream
- Support cache to avoid repeating the download of TS file
- Very easy to  integrate with an existing ios project
- Support any iOS player
- Efficient scheduling policies to enhance the performance of P2P streaming
- Highly configurable
- Use IP database to group up peers by ISP and regions
- API frozen, new releases will not break your code

### Requirements
This library requires iOS 9.0+.
<br>NOTICE: This framework doesn’t support bitcode currently.

### Get Token
See [here](/en/bindings.md?id=app-id-and-token)
