
## 简介
本插件是以hls.js作为播放内核的，所以可以集成到内置hls.js的任何H5视频播放器中！以下为部分播放器集成示例，当然您也可以尝试集成到其他播放器中。

## videojs
[videojs介绍](http://videojs.com/)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CDNBye videojs5 demo</title>
</head>
<link href="//vjs.zencdn.net/7.2/video-js.min.css" rel="stylesheet">
<script src="//vjs.zencdn.net/7.2/video.min.js"></script>
<!-- increase browser support with MSE polyfill -->
<script src="https://unpkg.com/videojs-contrib-media-sources@4.4.4/dist/videojs-contrib-media-sources.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/videojs-contrib-hlsjs.min.js"></script>
<body>
<div id="main">
    <video id="player" class="video-js vjs-default-skin" height="360" width="640" controls preload="none">
        <source src="https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"
                type="application/x-mpegURL"/>
    </video>
</div>
<script>
    var player = videojs('#player', {
        autoplay: true,
        html5: {
            hlsjsConfig: {
                debug: false,
                // Other hlsjsConfig options provided by hls.js
                p2pConfig: {
                    tag: 'videojs',
                    // Other p2pConfig options provided by CDNBye
                }
            }
        }
    });
</script>
</body>
</html>
```
    
## flowplayer 
[flowplayer介绍](https://flowplayer.com/)
```html
<!-- head section -->
<!-- skin -->
<link rel="stylesheet" href="https://releases.flowplayer.org/7.2.6/skin/skin.css">
<!-- CDNBye hls.js -->
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/hls.light.min.js"></script>
<!-- flowplayer -->
<script src="https://releases.flowplayer.org/7.2.6/flowplayer.min.js"></script>
<!-- body section -->
<div id="player"></div>
<script>
    flowplayer('#player', {
        live: true,  // set if it's a live stream
        ratio: 9/16, // set the aspect ratio of the stream
        clip: {
            sources: [
                // path to the HLS m3u8
                { type: "application/x-mpegurl", src: "https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"},
            ]
        },
        hlsjs: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                tag: 'flowplayer',
                // Other p2pConfig options provided by CDNBye
            }
        }
    });
</script>
```

## jwplayer
[jwplayer介绍](https://www.jwplayer.com/)
```html
<!doctype html>
<html>
<head>
    <meta charset="UTF-8">
    <title>CDNBye JWPlayer Demo</title>
    <!-- CDNBye Plugin -->
    <script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
    <!-- JW Player Builds -->
    <script src="//ssl.p.jwpcdn.com/player/v/8.0.11/jwplayer.js"></script>
    <!-- JWPlayer Hlsjs Provider -->
    <script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/jwplayer.hlsjs.provider.min.js"></script>
</head>
<body>
<header></header>
<center>
    <div id="demoplayer"></div>
</center>
<script>
    jwplayer.key = "uoW6qHjBL3KNudxKVnwa3rt5LlTakbko9e6aQ6VUyKQ=";
    jwplayer('demoplayer').setup({
        file: 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8',
        width: 512,
        height: 288,
        autostart: true,
        hlsjsConfig: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                tag: 'jwplayer',
                // Other p2pConfig options provided by CDNBye
            }
        },
    });
    jwplayer().on('play', function () {
        jwplayer().hls.p2pEngine.on('stats', function ({totalHTTPDownloaded, totalP2PDownloaded}) {
            // get totalHTTPDownloaded and totalP2PDownloaded here
        })
    })
</script>
</body>
</html>
```

## DPlayer(推荐)
[DPlayer介绍](https://github.com/MoePlayer/DPlayer)
<br>本示例基于嵌入hlsjs-p2p-engine的[P2P-DPlayer](https://github.com/cdnbye/P2P-DPlayer)
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/p2p-dplayer@latest/dist/DPlayer.min.css">
<div id="dplayer"></div>
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="https://cdn.jsdelivr.net/npm/p2p-dplayer@latest"></script>
<script>
    var dp = new DPlayer({
        container: document.getElementById('dplayer'),
        autoplay: true,
        video: {
            url: 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8',
            type: 'hls'
        },
        hlsjsConfig: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                logLevel: true,
                // Other p2pConfig options provided by CDNBye
            }
        }
    });
</script>
```

## CKPlayer
[CKPlayer介绍](http://www.ckplayer.com/)
<br>本示例基于嵌入hlsjs-p2p-engine的[P2P-CKPlayer](https://github.com/cdnbye/P2P-CKPlayer)
```html
<!DOCTYPE html>
<html>
<body>
<div id="video" style="width: 100%; height: 400px;max-width: 600px;"></div>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/p2p-ckplayer@latest/ckplayer/ckplayer.min.js" charset="UTF-8"></script>
<script type="text/javascript">
    var videoObject = {
        container: '#video',//“#”代表容器的ID，“.”或“”代表容器的class
        variable: 'player',//该属性必需设置，值等于下面的new chplayer()的对象
        autoplay:true,
        html5m3u8:true,
        video:'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8',//视频地址
        hlsjsConfig: {   // hlsjs和CDNBye的配置参数
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                logLevel: true,
                // Other p2pConfig options provided by CDNBye
                // https://github.com/cdnbye/hlsjs-p2p-engine/blob/master/docs/%E4%B8%AD%E6%96%87/API.md
            }
        }
    };
    var player = new ckplayer(videoObject);
</script>
</body>
</html>
```

## clappr
[clappr介绍](https://github.com/clappr/clappr)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CDNBye Clappr Demo</title>
    <!-- Clappr Builds -->
    <script src="https://cdn.jsdelivr.net/npm/clappr@latest/dist/clappr.min.js"></script>
    <!-- CDNBye P2PEngine -->
    <script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/hlsjs-p2p-engine.min.js"></script>
    <!-- CDNBye Clappr Plugin -->
    <script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/clappr-plugin.min.js"></script>
</head>
<body>
<div id="player"></div>
<script>
    var player = new Clappr.Player(
        {
            source: "https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8",
            parentId: "#player",
            autoPlay: true,
            plugins: {
                playback: [CDNByeClapprPlugin]
            },
            playback: {
                hlsjsConfig: {
                    debug: false,
                    // Other hlsjsConfig options provided by hls.js
                    p2pConfig: {
                        tag: 'clappr',
                        // Other p2pConfig options provided by CDNBye
                    }
                }
            }
        });
</script>
</body>
</html>
```

## MediaElement.js
[MediaElement.js介绍](http://www.mediaelementjs.com/)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CDNBye MediaElement Demo</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/mediaelement@latest/build/mediaelementplayer.min.css">
    <script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/mediaelement@latest/build/mediaelement-and-player.min.js"></script>
</head>
<body>
<video id="player"
       src="https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"
       class="mejs__player"
       controls>
</video>
<script>
    var player = new MediaElementPlayer('player', {
        hls: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                tag: 'mediaelement',
                // Other p2pConfig options provided by CDNBye
            }
        },
        success: function (me) {
            me.play();
        }
    });
</script>
</body>
</html>
```

## TCPlayer  
[TCPlayer介绍](https://cloud.tencent.com/document/product/267/7479)(腾讯云播放器)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, shrink-to-fit=no">
    <title>CDNBye TCPlayer Demo</title>
    <meta name="format-detection" content="telephone=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black">
    <meta name="msapplication-tap-highlight" content="no">
    <style>
        body {
            margin: 0;
            padding: 0;
        }
    </style>
</head>
<body>
<div id="video-container" style="margin: 0px auto;">
</div>
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="//imgcache.qq.com/open/qcloud/video/vcplayer/TcPlayer-2.2.3.js"></script>
<script>
    var options = {
        m3u8: 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8' ,
        autoplay: true,
        live: false,
        width: '480',
        height: '320',
        hlsConfig: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                logLevel: true,
                tag: 'tcplayer',
                // Other p2pConfig options provided by CDNBye
            }
        }
    };
    var player = new TcPlayer('video-container', options);
    window.qcplayer = player;
</script>
</body>
</html>
```

## Chimee
[Chimee介绍](http://chimee.org/)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CDNBye Chimee Demo</title>
    <!-- Chimee Builds -->
    <script src="https://cdn.jsdelivr.net/npm/chimee@0.11.0/lib/index.browser.min.js"></script>
    <!-- CDNBye P2PEngine -->
    <script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/hlsjs-p2p-engine.min.js"></script>
    <!-- Chimee HLS Kernel -->
    <script src="https://cdn.jsdelivr.net/npm/p2p-chimee-kernel-hls@latest"></script>
</head>
<body>
<div id="player"></div>
<script>
    new Chimee({
        wrapper: '#player',  // video dom容器
        src: 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8',
        controls: true,
        kernels: {
            hls: {
                handler: window.ChimeeKernelHls,
                p2pConfig: {
                    tag: 'chimee'
                    // Other p2pConfig options provided by CDNBye
                }
            }
        },
    });
</script>
</body>
</html>
```