
## 云转码(PPVOD)集成
[PPVOD]是一个安装在服务器端的网络在线点播系统，可把所有音视频文件格式自动转换为可在电脑浏览器、手机浏览器、各种app播放的m3u8格式，可以实现目前主流视频网站的大部分功能，使用PPVOD可以快速的搭建一个视频播放系统。
<br>
目前支持windows和linux系统，[下载地址](http://www.ppvod.com/dianbo/xiazai/440.html)。
<br>
安装完成后，将安装目录中的hls.mim.js（linux下路径一般是/home/work/public/dplayer/hls.mim.js）替换成CDNBye的[hls.min.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/dist)即可。在控制台绑定域名后即可开启P2P服务并查看实时数据。