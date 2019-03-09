
## 快速入门Demo

将以下拷贝到您的网页中并运行。再打开另一个相同的网页。见证奇迹的时候到了！您已在两个网页之间建立了一个P2P连接，在不安装任何插件的情况下。如果在这个频道中（一个m3u8标识了一个频道）没有其它参与者，那么您打开的第一个网页将作为种子为第二个网页提供数据。
```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
<video id="video" controls></video>
<p id="version"></p>
<h3>download info:</h3>
<p id="info"></p>
<script>
    document.querySelector('#version').innerText = `hls.js version: ${Hls.version}  cdnbye version: ${Hls.engineVersion}`;
    if(Hls.isSupported()) {
        var video = document.getElementById('video');
        var hls = new Hls({
            p2pConfig: {
                logLevel: true,
                live: false,        // 如果是直播设为true
                // Other p2pConfig options provided by CDNBye
            }
        });
        hls.loadSource('https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8');
        hls.attachMedia(video);
        hls.on(Hls.Events.MANIFEST_PARSED,function(event, data) {
            video.play();
        });
        hls.p2pEngine.on('stats', function ({totalHTTPDownloaded, totalP2PDownloaded, totalP2PUploaded}) {
            var total = totalHTTPDownloaded + totalP2PDownloaded;
            document.querySelector('#info').innerText = `p2p ratio: ${Math.round(totalP2PDownloaded/total*100)}%, saved traffic: ${totalP2PDownloaded}KB, uploaded: ${totalP2PUploaded}KB`;
        });
    }
</script>
```

## 小白站长快速集成
- 选择一个网站目前在用的[播放器](players.md)（例如DPlayer）
- 将播放器部分代码替换成demo的代码，修改播放地址
- 调试通过后部署到服务器
- 访问[CDNBye后台系统OMS](https://oms.cdnbye.com)，按提示绑定域名后即可查看P2P效果

## 使用原生hls.js项目
如果您在项目中引入了hls.js的script标签，那么只需要将该标签如：
 ```javascript
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
```
替换为
 ```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
```
就是这么简单！


## 第三方播放器集成
参考[第三方播放器](players.md), 推荐已经打包P2P插件的[P2P-DPlayer](https://github.com/cdnbye/P2P-DPlayer)和[P2P-CKPlayer](https://github.com/cdnbye/P2P-CKPlayer)。

## 引入插件
通过script标签引入已经和hls.js打包的最新版本（推荐）：
```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
```
或者引入没有与hls.js打包的独立版本：
```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/hlsjs-p2p-engine.min.js"></script>
```

## 使用插件
#### Bundle
在hlsjsConfig对象字面量中加入p2pConfig字段，然后在实例化hls.js时把hlsjsConfig作为参数传入。
```javascript
var hlsjsConfig = {
    debug: true,
    // Other hlsjsConfig options provided by hls.js
    p2pConfig: {
        logLevel: 'debug',
        // Other p2pConfig options if applicable
    }
};
// Hls constructor is overriden by included bundle
var hls = new Hls(hlsjsConfig);
// Use `hls` just like the usual hls.js ...
hls.loadSource(contentUrl);
hls.attachMedia(video);
hls.on(Hls.Events.MANIFEST_PARSED,function() {
    video.play();
});
```
#### Engine(没有打包hls.js的插件，需要自己引入hls.js)
实例化hls.js并将hlsjsConfig作为参数传入。然后实例化P2PEngine并将p2pConfig作为参数传入。调用hls.js的loadSource和attachMedia方法。
```javascript
var hlsjsConfig = {
    maxBufferSize: 0,       // Highly recommended setting in live mode
    maxBufferLength: 5,     // Highly recommended setting in live mode
    liveSyncDuration: 30,   // Highly recommended setting in live mode
};

var p2pConfig = {
    logLevel: 'debug',
    // Other p2pConfig options if applicable
};

var hls = new Hls(hlsjsConfig);
if (P2PEngine.isSupported()) {
    new P2PEngine(hls, p2pConfig);        // Key step
}

// Use `hls` just like your usual hls.js…
hls.loadSource(contentUrl);
hls.attachMedia(video);
hls.on(Hls.Events.MANIFEST_PARSED,function() {
    video.play();
});
```

