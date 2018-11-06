## CDNBye :id=head
> A free hls.js plugin to offload bandwidth from expensive traditional CDNs，while also maximizing a user’s viewing experience. 

## Next Generation Web P2P Technology
This JS library implements WebRTC datachannel to scale live/vod video streaming by peer-to-peer network using bittorrent-like protocol. The forming peer network can be layed over other CDNs or on top of the origin server. Powered by hls.js, it can play HLS on any platform with many popular HTML5 players such as video.js, JWPlayer and Flowplayer.

## Features
- WebRTC data channels for lightweight peer-to-peer communication with no plugins
- Very easy to  integrate with an existing hls.js project
- Highly configurable for users
- Support most popular HTML5 players such as video.js、Clappr、Flowplayer
- Efficient scheduling policies to enhance the performance of P2P streaming
- Use IP database to group up peers by ISP and regions

## Demo
[Click me!](https://cdnbye.github.io/hlsjs-p2p-demo/)

## Browser Support
WebRTC has already been incorporated into the HTML5 standard and it is broadly deployed in modern browsers. The compatibility of CDNBye depends on the browser support of WebRTC and Hls.js. Please note that iOS Safari "Mobile" does not support the MediaSource API.

 Compatibility|Chrome | Firefox | Mac Safari| iOS Safari | Opera | IE | Edge|   
:-: | :-: | :-: | :-: | :-: | :-: | :-:| :-:
WebRTC | Yes | Yes | Yes | Yes | Yes | No | No
Hls.js | Yes | Yes | Yes | No | Yes | Yes | Yes
CDNBye | Yes | Yes | Yes | No | Yes | No | No 

## Contact Us
Email: service@cdnbye.com
