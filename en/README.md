## CDNBye :id=head
> A free hls.js plugin to offload bandwidth from expensive traditional CDNs，while also maximizing a user’s viewing experience. 

## P2P Streaming Technology for Next Generation
CDNBye hlsjs-p2p-engine implements WebRTC datachannel to scale live/vod video streaming by peer-to-peer network using bittorrent-like protocol. The forming peer network can be layed over other CDNs or on top of the origin server. Powered by hls.js, it can play HLS on any platform with many popular HTML5 players such as video.js, JWPlayer and Flowplayer.

## Features
- WebRTC data channels for lightweight peer-to-peer communication with no plugins
- Support live and VOD streams over HLS protocol(m3u8)
- Very easy to  integrate with an existing hls.js project
- Seamlessly fallback to normal server usage if a browser doesn't support WebRTC
- Highly configurable for users
- Support most popular HTML5 players such as video.js、Clappr、Flowplayer
- Efficient scheduling policies to enhance the performance of P2P streaming
- Use IP database to group up peers by ISP and regions
- API frozen, new releases will not break your code

## Demo
[Click me!](https://demo.cdnbye.com/)

## Browser Support
WebRTC has already been incorporated into the HTML5 standard and it is broadly deployed in modern browsers. The compatibility of CDNBye depends on the browser support of WebRTC and Hls.js. Please note that iOS Safari "Mobile" does not support the MediaSource API.

 Compatibility|Chrome | Firefox | Mac Safari| Android Wechat/QQ | Opera | IE | Edge| iOS Safari | 
:-: | :-: | :-: | :-: | :-: | :-: | :-:| :-:| :-:
WebRTC | ✔ | ✔ | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
Hls.js | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | ❌ |
CDNBye | ✔ | ✔ | ✔ | ✔ | ✔ | ❌ | ❌ | ❌ |

## Contact Us
Email: service@cdnbye.com
