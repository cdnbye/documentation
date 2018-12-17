
## Quick Start Demo

Put the the code below in your web page, run it. Wait for a few seconds，then open the same page from another browser. Now you have a direct P2P connection between two browsers without plugin! The first web peer will serve as a seed, if no one else in the same channel.
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

## Integrate to existing hls.js Project
Simply replace the hls.js script tag like:
 ```javascript
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
```
with
 ```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
```
That's it!


## Integrate to HTML5 Players
See [Player Integration](players.md)

## Include
Include the pre-built script of latest version bundled with hls.js(recommended):
```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script>
```
Or include the latest version without hls.js:
```javascript
<script src="https://cdn.jsdelivr.net/npm/cdnbye@latest/dist/hlsjs-p2p-engine.min.js"></script>
```

## Usage
#### Bundle
Add p2pConfig as a property of hlsjsConfig, then Create hls.js instance passing hlsjsConfig as constructor param.
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
```
#### Engine(the library without hls.js)

Create hls.js instance passsing hlsjsConfig as param. Create P2PEngine instance passing hls.js instance and p2pConfig as params. Call hls.js loadSource and attachMedia methods.
```javascript
var hlsjsConfig = {
    maxBufferSize: 0,       // Highly recommended setting
    maxBufferLength: 5,     // Highly recommended setting
    liveSyncDuration: 30,   // Highly recommended setting
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

