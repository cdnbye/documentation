
### 简介
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
<script src="//unpkg.com/videojs-contrib-media-sources@4.4.4/dist/videojs-contrib-media-sources.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest/dist/videojs-contrib-hlsjs.min.js"></script>

<body>
<div id="main">
    <video id="player" class="video-js vjs-default-skin" height="360" width="640" controls preload="none">
        <source src="https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"
                type="application/x-mpegURL"/>
    </video>
    <p id="version"></p>
    <h3>download info:</h3>
    <p id="info"></p>
</div>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
    var player = videojs('#player', {
        autoplay: true,
        html5: {
            hlsjsConfig: {
                debug: false,
                // Other hlsjsConfig options provided by hls.js
                p2pConfig: {
                    logLevel: true,
                    live: false,        // 如果是直播设为true
                    getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                        var total = totalHTTPDownloaded + totalP2PDownloaded;
                        document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                    },
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
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest/dist/hls.light.min.js"></script>
<!-- flowplayer -->
<script src="//releases.flowplayer.org/7.2.6/flowplayer.min.js"></script>

<!-- body section -->
<div id="player"></div>
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>

<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
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
                logLevel: true,
                live: false,        // 如果是直播设为true
                getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                    var total = totalHTTPDownloaded + totalP2PDownloaded;
                    document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                },
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
    <script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
    <!-- JW Player Builds -->
    <script src="//ssl.p.jwpcdn.com/player/v/8.0.11/jwplayer.js"></script>
    <!-- JWPlayer Hlsjs Provider -->
    <script src="//cdn.jsdelivr.net/npm/cdnbye@latest/dist/jwplayer.hlsjs.provider.min.js"></script>
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
                logLevel: true,
                live: false,        // 如果是直播设为true
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

## DPlayer
[DPlayer介绍](https://github.com/MoePlayer/DPlayer)
```html
<meta charset="UTF-8">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/dplayer@latest/dist/DPlayer.min.css">
<style type="text/css">
    body,html{width:100%;height:100%;background:#000;padding:0;margin:0;overflow-x:hidden;overflow-y:hidden}
    *{margin:0;border:0;padding:0;text-decoration:none}
    #stats{position:fixed;top:5px;left:10px;font-size:12px;color:#fdfdfd;z-index:2147483647;text-shadow:1px 1px 1px #000, 1px 1px 1px #000}
    #dplayer{position:inherit}
</style>
<div id="dplayer"></div>
<div id="stats"></div>
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="https://cdn.jsdelivr.net/npm/dplayer@latest"></script>
<script>
    var _peerId = '', _peerNum = 0, _totalP2PDownloaded = 0, _totalP2PUploaded = 0;
    const dp = new DPlayer({
        container: document.getElementById('dplayer'),
        autoplay: true,
        video: {
            url: 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8',
            type: 'customHls',
            customType: {
                'customHls': function (video, player) {
                    const hls = new Hls({
                        debug: false,
                        // Other hlsjsConfig options provided by hls.js
                        p2pConfig: {
                            logLevel: 'debug',
                            live: false,        // 如果是直播设为true
                            // Other p2pConfig options provided by CDNBye
                        }
                    });
                    hls.loadSource(video.src);
                    hls.attachMedia(video);
                    hls.p2pEngine.on('stats', function (stats) {
                        _totalP2PDownloaded = stats.totalP2PDownloaded;
                        _totalP2PUploaded = stats.totalP2PUploaded;
                        updateStats();
                    }).on('peerId', function (peerId) {
                        _peerId = peerId;
                    }).on('peers', function (peers) {
                        _peerNum = peers.length;
                        updateStats();
                    });

                }
            }
        }
    });

    function updateStats() {
        var text = 'P2P正在为您加速' + (_totalP2PDownloaded/1024).toFixed(2)
            + 'MB 已分享' + (_totalP2PUploaded/1024).toFixed(2) + 'MB' + ' 连接节点' + _peerNum + '个';
        document.getElementById('stats').innerText = text
    }
</script>
```

## CKPlayer
[CKPlayer介绍](http://www.ckplayer.com/)
<br>本示例基于嵌入hlsjs-p2p-engine的[P2P-CKPlayer](https://github.com/cdnbye/P2P-CKPlayer)
<br>只能使用P2P-CKPlayer，不能使用官方的，否则无P2P效果
```html
<!DOCTYPE html>
<html>
<body>
<div id="video" style="width: 100%; height: 400px;max-width: 600px;"></div>
<h3>download info:</h3>
<p id="info"></p>
<script type="text/javascript" src="//cdn.jsdelivr.net/npm/p2p-ckplayer@latest/ckplayer/ckplayer.min.js" charset="UTF-8"></script>
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
                logLevel: 'debug',
                live: false,        // 如果是直播设为true
                getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                    var total = totalHTTPDownloaded + totalP2PDownloaded;
                    document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                },
                // Other p2pConfig options provided by CDNBye
                // https://docs.cdnbye.com/#/en/API
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
    <script src="//cdn.jsdelivr.net/npm/clappr@latest/dist/clappr.min.js"></script>
    <!-- CDNBye P2PEngine -->
    <script src="//cdn.jsdelivr.net/npm/cdnbye@latest/dist/hlsjs-p2p-engine.min.js"></script>
    <!-- CDNBye Clappr Plugin -->
    <script src="//cdn.jsdelivr.net/npm/cdnbye@latest/dist/clappr-plugin.min.js"></script>
    <!-- Clappr Level Selector Plugin -->
    <script type="text/javascript"
            src="//cdn.jsdelivr.net/gh/clappr/clappr-level-selector-plugin@latest/dist/level-selector.min.js"></script>
</head>
<body>
<div id="player"></div>
<h3>download info:</h3>
<p id="info"></p>
<script>
    var player = new Clappr.Player(
        {
            source: "https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8",
            parentId: "#player",
            autoPlay: true,
            plugins: [CDNByeClapprPlugin, LevelSelector],
            levelSelectorConfig: {
                title: 'Quality',
                labels: {
                    2: 'High', // 500kbps
                    1: 'Med', // 240kbps
                    0: 'Low', // 120kbps
                },
                labelCallback: function(playbackLevel, customLabel) {
                    return customLabel + playbackLevel.level.height+'p'; // High 720p
                }
            },
            playback: {
                hlsjsConfig: {
                    // Other hlsjsConfig options provided by hls.js
                    p2pConfig: {
                        logLevel: 'debug',
                        live: false,        // 如果是直播设为true
                        getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                            var total = totalHTTPDownloaded + totalP2PDownloaded;
                            document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                        },
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
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/mediaelement@latest/build/mediaelementplayer.min.css">
    <script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
    <script src="//cdn.jsdelivr.net/npm/mediaelement@latest/build/mediaelement-and-player.min.js"></script>
</head>
<body>
<video id="player"
       src="https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"
       class="mejs__player"
       controls>
</video>
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
    var player = new MediaElementPlayer('player', {
        hls: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                logLevel: true,
                live: false,        // 如果是直播设为true
                getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                    var total = totalHTTPDownloaded + totalP2PDownloaded;
                    document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                },
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
[TCPlayer介绍](https://cloud.tencent.com/document/product/881/20207)(腾讯云播放器)
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
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="//imgcache.qq.com/open/qcloud/video/vcplayer/TcPlayer-2.2.3.js"></script>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
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
                live: false,        // 如果是直播设为true
                getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                    var total = totalHTTPDownloaded + totalP2PDownloaded;
                    document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                },
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
    <script src="//cdn.jsdelivr.net/npm/chimee@0.11.0/lib/index.browser.min.js"></script>
    <!-- CDNBye P2PEngine -->
    <script src="//cdn.jsdelivr.net/npm/cdnbye@latest/dist/hlsjs-p2p-engine.min.js"></script>
    <!-- Chimee HLS Kernel -->
    <script src="//cdn.jsdelivr.net/npm/p2p-chimee-kernel-hls@latest"></script>
</head>
<body>
<div id="player"></div>
<h3>download info:</h3>
<p id="info"></p>
<script>
    new Chimee({
        wrapper: '#player',  // video dom容器
        src: 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8',
        controls: true,
        kernels: {
            hls: {
                handler: window.ChimeeKernelHls,
                maxBufferSize: 0,       // Highly recommended setting
                maxBufferLength: 5,     // Highly recommended setting
                liveSyncDuration: 30,   // Highly recommended setting
                p2pConfig: {
                    logLevel: 'debug',
                    live: false,        // 如果是直播设为true
                    getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                        var total = totalHTTPDownloaded + totalP2PDownloaded;
                        document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                    },
                    // Other p2pConfig options provided by CDNBye
                }
            }
        },
    });
</script>
</body>
</html>
```

## XGPlayer
[XGPlayer介绍](http://h5player.bytedance.com/)
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name=viewport content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1,user-scalable=no,minimal-ui">
    <meta name="referrer" content="no-referrer">
    <title>CDNBye XGPlayer Demo</title>
    <style type="text/css">
        html, body {width:100%;height:100%;margin:auto;overflow: hidden;}
        body {display:flex;}
        #mse {flex:auto;}
    </style>
    <script type="text/javascript">
        window.addEventListener('resize',function(){document.getElementById('mse').style.height=window.innerHeight+'px';});
    </script>
</head>
<body>
<div id="mse"></div>
<script src="//cdn.jsdelivr.net/npm/xgplayer@1.1.4-beta.3/browser/index.js" charset="utf-8"></script>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="//cdn.jsdelivr.net/npm/p2p-xgplayer-hlsjs@latest"></script>
<script type="text/javascript">
    new window.HlsJsPlayer({
        id: 'mse',
        url: '//video-dev.github.io/streams/x36xhzz/x36xhzz.m3u8',
        autoplay: true,
        playsinline: true,
        height: window.innerHeight,
        width: window.innerWidth,
        useHls: true,
        hlsOpts: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                logLevel: true,
                live: false,        // 如果是直播设为true
                getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                    console.log(`totalP2PDownloaded ${totalP2PDownloaded} totalP2PUploaded ${totalP2PUploaded} totalHTTPDownloaded ${totalHTTPDownloaded}`)
                },
                // Other p2pConfig options provided by CDNBye
            }
        }
    });
</script>
</body>
</html>
```

## fluidplayer
[fluidplayer介绍](https://www.fluidplayer.com/)
```html
<link rel="stylesheet" href="https://cdn.fluidplayer.com/v2/current/fluidplayer.min.css" type="text/css"/>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="https://cdn.fluidplayer.com/v2/current/fluidplayer.min.js"></script>
<video id='hls-video'>
    <source src='https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8' type='application/x-mpegURL'/>
</video>
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
    fluidPlayer(
        'hls-video',
        {
            layoutControls: {
                fillToContainer: true
            },
            hlsjsConfig: {
                // debug: false,
                // Other hlsjsConfig options provided by hls.js
                p2pConfig: {
                    logLevel: true,
                    live: false,        // 如果是直播设为true
                    getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                        var total = totalHTTPDownloaded + totalP2PDownloaded;
                        document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                    },
                    // Other p2pConfig options provided by CDNBye
                    // https://docs.cdnbye.com/#/API
                }
            }
        }
    );
</script>
```

## OpenPlayer
[OpenPlayer介绍](https://www.openplayerjs.com/)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CDNBye OpenPlayer Demo</title>
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/openplayerjs@latest/dist/openplayer.css">
</head>
<body>
<video class="op-player__media" id="video" controls playsinline>
    <source src="https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"></video>
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script src="//cdn.jsdelivr.net/npm/openplayerjs@latest"></script>
</body>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
    var player = new OpenPlayer('video', null, false, {
        hls: {
            debug: false,
            // Other hlsjsConfig options provided by hls.js
            p2pConfig: {
                logLevel: 'debug',
                live: false,        // 如果是直播设为true
                getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                    var total = totalHTTPDownloaded + totalP2PDownloaded;
                    document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                },
                // Other p2pConfig options provided by CDNBye
            }
        }
    });
    player.init();
</script>
</html>
```

## Plyr
[Plyr介绍](https://plyr.io/)
```html
<!DOCTYPE html>
<html lang="en">
<div class="container">
    <video controls crossorigin playsinline></video>
</div>
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>
<link rel="stylesheet" href="//cdn.plyr.io/3.4.7/plyr.css">
<script src="//cdn.plyr.io/3.4.7/plyr.js"></script>
<script src="//cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
    document.addEventListener('DOMContentLoaded', () => {
        var source = 'https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8';
        var video = document.querySelector('video');

        // For more options see: https://github.com/sampotts/plyr/#options
        // captions.update is required for captions to work with hls.js
        var player = new Plyr(video, {
            captions: {active: true, update: true, language: 'en'},
            // autoplay: true,
        });

        if (!Hls.isSupported()) {
            video.src = source;
        } else {
            // For more Hls.js options, see https://github.com/dailymotion/hls.js
            var hls = new Hls({
                p2pConfig: {
                    logLevel: true,
                    live: false,        // 如果是直播设为true
                    getStats: function (totalP2PDownloaded, totalP2PUploaded, totalHTTPDownloaded) {
                        var total = totalHTTPDownloaded + totalP2PDownloaded;
                        document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
                    },
                    // Other p2pConfig options provided by CDNBye
                }
            });
            hls.loadSource(source);
            hls.attachMedia(video);
            window.hls = hls;

            // Handle changing captions
            player.on('languagechange', () => {
                // Caption support is still flaky. See: https://github.com/sampotts/plyr/issues/994
                setTimeout(() => hls.subtitleTrack = player.currentTrack, 50);
            });
        }

        // Expose player so it can be used from the console
        window.player = player;
    });
</script>
```