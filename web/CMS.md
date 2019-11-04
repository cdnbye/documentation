
## 云转码(PPVOD)集成
[PPVOD](http://www.ppvod.com/)是一个安装在服务器端的网络在线点播系统，可把所有音视频文件格式自动转换为可在电脑浏览器、手机浏览器、各种app播放的m3u8格式，可以实现目前主流视频网站的大部分功能，使用PPVOD可以快速的搭建一个视频播放系统。
<br>
目前支持windows和linux系统，[下载地址](http://www.ppvod.com/dianbo/xiazai/440.html)。
<br>
安装完成后，将安装目录中的hls.min.js（linux下路径一般是/home/work/public/dplayer/hls.min.js）替换成CDNBye的[hls.min.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/dist)，将PPVOD安装目录中的DPlayer.min.js（linux下路径一般是/home/work/public/dplayer/DPlayer.min.js）替换成CDNBye的[DPlayer.min.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/demo/ppvod)，同时将share.js（linux下路径一般是/home/work/public/js/share.js）替换成CDNBye提供的[share.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/demo/ppvod)即可。在控制台绑定域名后即可开启P2P服务并查看实时数据。
<br>
PS：使用P2P功能后，请勿开启`播放加密`。

## 苹果CMS集成
为苹果CMSV10播放器增加记忆+P2P播放+自动下一集功能，用以下代码替换`static/player/dplayer.html`全部内容：
```html
<html>
<head>
    <title>dplayer播放器p2p加速+记忆播放</title>
    <meta http-equiv="content-type" content="text/html;charset=UTF-8"/>
    <meta http-equiv="content-language" content="zh-CN"/>
    <meta http-equiv="X-UA-Compatible" content="chrome=1"/>
    <meta http-equiv="pragma" content="no-cache"/>
    <meta http-equiv="expires" content="0"/>
    <meta name="referrer" content="never"/>
    <meta name="renderer" content="webkit"/>
    <meta name="msapplication-tap-highlight" content="no"/>
    <meta name="HandheldFriendly" content="true"/>
    <meta name="x5-page-mode" content="app"/>
    <meta name="Viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=0"/>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/dplayer@latest/dist/DPlayer.min.css">
    <style type="text/css">
        body,html{width:100%;height:100%;background:#000;padding:0;margin:0;overflow-x:hidden;overflow-y:hidden}
        *{margin:0;border:0;padding:0;text-decoration:none}
        #stats{position:fixed;top:5px;left:8px;font-size:12px;color:#fdfdfd;text-shadow:1px 1px 1px #000, 1px 1px 1px #000}
        #dplayer{position:inherit}
    </style>
</head>
<body style="background:#000" leftmargin="0" topmargin="0" marginwidth="0" marginheight="0" oncontextmenu=window.event.returnValue=false>
<div id="dplayer"></div>
<div id="stats"></div>
<script language="Javascript">
    document.oncontextmenu=new Function("event.returnValue=false");
    document.onselectstart=new Function("event.returnValue=false");
</script>
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="https://cdn.jsdelivr.net/npm/dplayer@latest"></script>
<script>
    var webdata = {
        set:function(key,val){
            window.sessionStorage.setItem(key,val);
        },
        get:function(key){
            return window.sessionStorage.getItem(key);
        },
        del:function(key){
            window.sessionStorage.removeItem(key);
        },
        clear:function(key){
            window.sessionStorage.clear();
        }
    };
    var _peerId = '', _peerNum = 0, _totalP2PDownloaded = 0, _totalP2PUploaded = 0;
    var dp = new DPlayer({
        autoplay: true,
        container: document.getElementById('dplayer'),
        volume: 1.0,
        preload: 'auto',
        screenshot: true,
        theme: '#28FF28',
        video: {
            url: parent.MacPlayer.PlayUrl,
            type: 'customHls',
            // pic: './loading.jpg',           // 视频封面
            customType: {
                'customHls': function (video, player) {
                    const hls = new Hls({
                        debug: false,
                        // Other hlsjsConfig options provided by hls.js
                        p2pConfig: {
                            logLevel: false,
                            live: false,        // 如果是直播设为true
                            // Other p2pConfig options provided by CDNBye
                            getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                                console.warn(`totalP2PDownloaded ${totalP2PDownloaded} totalHTTPDownloaded ${totalHTTPDownloaded}`);
                                _totalP2PDownloaded = totalP2PDownloaded;
                                _totalP2PUploaded = totalP2PUploaded;
                                updateStats();
                            },
                            getPeerId: function (peerId) {
                                _peerId = peerId;
                            },
                            getPeersInfo: function (peers) {
                                _peerNum = peers.length;
                                updateStats();
                            },
                        }
                    });
                    hls.loadSource(video.src);
                    hls.attachMedia(video);
                }
            }
        },
    });
    dp.seek(webdata.get('pay'+parent.MacPlayer.PlayUrl));
    setInterval(function(){
        webdata.set('pay'+parent.MacPlayer.PlayUrl,dp.video.currentTime);
    },1000);
    dp.on('ended', function (){
        if(parent.MacPlayer.PlayLinkNext!=''){
            top.location.href = parent.MacPlayer.PlayLinkNext;
        }
    });
    function updateStats() {
        var text = 'P2P已开启 共享' + (_totalP2PUploaded/1024).toFixed(2) + 'MB' + ' 已加速' + (_totalP2PDownloaded/1024).toFixed(2)
            + 'MB' + ' 此片有 ' + _peerNum + ' 位影迷正在观看';
        document.getElementById('stats').innerText = text
    }
</script>
</body>
</html>
```
然后点击苹果CMS后台的`播放器`选项，拷贝以下代码：
```html
MacPlayer.Html = '<iframe border="0" src="'+maccms.path+'/static/player/dplayer.html" width="100%" height="100%" marginWidth="0" frameSpacing="0" marginHeight="0" frameBorder="0" scrolling="no" vspale="0" noResize></iframe>';
MacPlayer.Show();
```