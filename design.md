## 概述
随着H5越来越普及，flash逐渐被淘汰已成为不可逆转的趋势。而在H5采用的视频传输格式中，hls由于兼容ios
和android、可以穿过任何允许HTTP数据通过的防火墙、容易使用内容分发网络来传输媒体流和码率自适应等众多
优势而在业界得到广泛使用。通过使用[hls.js](https://github.com/video-dev/hls.js)这个第三方库，
几乎所有现代浏览器都可以播放hls视频。hls天生分片传输的优势，使其可以采用p2p的方式进行传输，从而减小
服务器的负担。在web端，无插件化实现p2p传输能力的最好选择就是WebRTC技术，与hls.js类似，WebRTC也
支持几乎所有现代浏览器。本项目的目标是开发一个hls.js的插件，通过WebRTC data channel技术，在不影响
用户体验的前提下，最大化p2p率，从而为CP节省带宽成本。

## 适用场景
- 点播
- 高延时直播(30s以上)
- 在已有项目中使用hls.js或即将采用hls.js

## 设计理念
- 采用仿BT算法，简化BT的流程，并且针对流媒体的特点对算法进行调整
- 不改动hls.js源码，并且可以与其无缝衔接，几行代码集成，便于在现有项目中快速集成
- 高可配置化，用户可以根据特定的使用环境调整各个参数
- 支持video.js、Clappr、Flowplayer等第三方播放器
- 通过有效的调度策略来保证用户的播放体验以及p2p率
- Tracker服务器根据访问IP的ISP、地域等进行智能调度

## 整体架构

<img width="600" src="pics/architecture.png" alt="architecture">

## Fragment Loader调度过程

<img width="600" src="pics/loader.png" alt="loader">

## P2P分享过程

<img width="600" src="pics/bittorrent.png" alt="bittorrent">







  