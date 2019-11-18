
## 云转码(PPVOD)
[PPVOD](http://www.ppvod.com/)是一个安装在服务器端的网络在线点播系统，可把所有音视频文件格式自动转换为可在电脑浏览器、手机浏览器、各种app播放的m3u8格式，可以实现目前主流视频网站的大部分功能，使用PPVOD可以快速的搭建一个视频播放系统。
<br>
目前支持windows和linux系统，[下载地址](http://www.ppvod.com/dianbo/xiazai/440.html)。
<br>
安装完成后，将安装目录中的hls.min.js（linux下路径一般是/home/work/public/dplayer/hls.min.js）替换成CDNBye的[hls.min.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/dist)，将PPVOD安装目录中的DPlayer.min.js（linux下路径一般是/home/work/public/dplayer/DPlayer.min.js）替换成CDNBye的[DPlayer.min.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/demo/ppvod)，同时将share.js（linux下路径一般是/home/work/public/js/share.js）替换成CDNBye提供的[share.js](https://github.com/cdnbye/hlsjs-p2p-engine/tree/master/demo/ppvod)即可。在控制台绑定域名后即可开启P2P服务并查看实时数据。
<br>
PS：使用P2P功能后，请勿开启`播放加密`。

## 苹果CMS
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

## 海洋CMS
为海洋CMS v9.99播放器增加记忆+P2P播放+自动下一集功能，用以下代码替换`js/player/dplayer/dplayer.html`全部内容：
```html
<html>
 <head> 
  <meta charset="UTF-8" /> 
  <title>dplayer增加记忆+P2P播放+自动下一集功能</title> 
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" /> 
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/dplayer@latest/dist/DPlayer.min.css" /> 
  <script type="text/javascript" src="jquery.min.js"></script> 
  <script type="text/javascript" src="flv.min.js"></script> 
  <!-- <script type="text/javascript" src="hls.min.js"></script> --> 
  <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/cdnbye@latest"></script> 
  <script type="text/javascript" src="dash.all.min.js"></script> 
  <script type="text/javascript" src="webtorrent.min.js"></script> 
  <script src="https://cdn.jsdelivr.net/npm/dplayer@latest"></script> 
  <script type="text/javascript" src="md5.js"></script> 
  <style type="text/css">
    body,html{width:100%;height:100%;background:#000;padding:0;margin:0;overflow-x:hidden;overflow-y:hidden}
    *{margin:0;border:0;padding:0;text-decoration:none}
	#stats{position:fixed;top:5px;left:10px;font-size:10px;z-index:20719029;display: block;background-image: -webkit-linear-gradient(left, #3498db, #f47920 10%, #d71345 20%, #f7acbc 30%,#ffd400 40%, #3498db 50%, #f47920 60%, #d71345 70%, #f7acbc 80%, #ffd400 90%, #3498db);color: transparent;-webkit-text-fill-color: transparent;-webkit-background-clip: text; background-size: 200% 100%;animation: masked-animation 4s infinite linear;}
    #playerCnt{position:inherit}
</style>
 </head>  
 <body> 
  <div id="playerCnt"></div> 
  <div id="stats"></div> 
  <script type="text/javascript">
	//获取地址栏里传递过来的视频地址
	function getUrlParam(name) {
		var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)"); //构造一个含有目标参数的正则表达式对象
		var r = window.location.search.substr(1).match(reg); //匹配目标参数
		if(r != null) return unescape(r[2]);
		return null; //返回参数值
	}
		
   //设置浏览器缓存项值，参数：项名,值,有效时间(小时)
    function setCookie(c_name, value, expireHours) {
      var exdate = new Date();
      exdate.setHours(exdate.getHours() + expireHours);
      document.cookie = c_name + "=" + escape(value) + ((expireHours === null) ? "" : ";expires=" + exdate.toGMTString());
    }
    //获取浏览器缓存项值，参数：项名
    function getCookie(c_name) {
      if (document.cookie.length > 0) {
        c_start = document.cookie.indexOf(c_name + "=");
        if (c_start !== -1) {
            c_start = c_start + c_name.length + 1;
            c_end = document.cookie.indexOf(";", c_start);
            if (c_end === -1) {
                c_end = document.cookie.length;
            };
            return unescape(document.cookie.substring(c_start, c_end));
         }
     }
    return "";
   }
		
	var videoUrl2 =getUrlParam('videourl');
	var a=videoUrl2.split(",");
	var nextpage=a[0];
	var videoUrl=a[1];
	var vid=a[3];
	var vfrom=a[4];
	var vpart=a[5];
	var did=hex_md5(videoUrl);
	var headtime=Number(getCookie(did) || 0);
	
//获取剧集数据
    function getvideo(url){  	
    		$.ajax({
				url: '/ass.php?url=dp&vid='+vid+'&vfrom='+vfrom+'&vpart='+vpart,
				dataType: 'jsonp',
				jsonp: 'cb',                 
				success:function(data){					
					//剧集及当前集数
				     xyplay=data.s;				     
				     if(xyplay.part>0){xyplay.part--;}
				},				
    		})
	
    }
	//获取剧集数据
	if("undefined" !== typeof nextpage && nextpage!=""){getvideo(nextpage);}    
</script> 
 
<script type="text/javascript">
    var type='normal';
    var live=false;
	var _peerId = '', _peerNum = 0, _totalP2PDownloaded = 0, _totalP2PUploaded = 0;
    if(videoUrl.indexOf('.m3u8')>-1){
        type='Hls';
        live=true;
    }
    else if(videoUrl.indexOf('magnet:')>-1){
        type='webtorrent';
    }
    else if(videoUrl.indexOf('.flv')>-1){
        type='flv';
    }
    else if(videoUrl.indexOf('.mpd')>-1){
        type='dash';
    }
 
    var dp = new DPlayer({
        container: document.getElementById('playerCnt'),
        autoplay: true,
        screenshot: false,
        video: {
            url: videoUrl,
            type: 'customHls',
			customType: {
                'customHls': function (video, player) {
                    const hls = new Hls({
                        debug: false,
                        p2pConfig: {
                            live: false,        // 如果是直播设为true
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
        },
        front:"video_front",
        next:"video_next",
        
        contextmenu: [
          
            {text:"Xyplayer X3 ",link:""},
 
        ]
    });
 
	//绑定准备就绪回调
    dp.on("loadedmetadata", function () {loadedmetadataHandler();});
 
	
	//绑定播放结束回调
    dp.on("ended", function () {endedHandler();});
			
	//视频就绪回调,用来监控播放开始 
    function loadedmetadataHandler() {
                if (  headtime > 0 && dp.video.currentTime < headtime) {
                        dp.seek(headtime);
                        dp.notice("继续上次播放");
 
                } else {
                       dp.notice("视频已就绪");
            
                }
                        dp.on("timeupdate", function () {
                        timeupdateHandler();
                    })
					$("#playerCnt").addClass("dplayer-hide-controller");
           
            }
    //播放进度回调  	
    function timeupdateHandler() {
                 setCookie(did,dp.video.currentTime,24);
           }
	
 
    //播放结束回调		
    function endedHandler() {
               
             setCookie(did,"",-1);
                
            if ("undefined" !== typeof xyplay && "undefined" !== typeof xyplay.video){
               
                if (Number(xyplay.part) + 1 <= xyplay.video.length){
                     dp.notice("视频已结束,为您跳到下一集");
                     setTimeout(function () {video_next();}, 500);
                } else {
                    dp.notice("视频播放已结束");
                }
           
             }else{
             	
              	if("undefined" !== typeof nextpage && nextpage!=""){ top.location.href=nextpage;}	
 
             }
           
            }
 
 
	//播放下集
    function video_next() {
            if ("undefined" !== typeof xyplay && "undefined" !== typeof xyplay.video)
                    if (Number(xyplay.part) + 1>= xyplay.video.length) {
                    	dp.notice("已经是最后一集");
	
                        return false;
                    }
                xyplay.part++;    
                myplay(xyplay.video[xyplay.part]);
               
            }
	
	
	//播放上集	
    function video_front() {
            if ("undefined" !== typeof xyplay && "undefined" !== typeof xyplay.video)
                    if (Number(xyplay.part) <= 0) {
                    	dp.notice("已经是最前一集");
                        return false;
                    }
                xyplay.part--;
                myplay(xyplay.video[xyplay.part]);
 
            }
	
	
	//调用播放
    function myplay(url) {             	
                videoUrl=url; did=hex_md5(videoUrl);
                headtime= Number(getCookie(did));
                dp.switchVideo({url: videoUrl});
                dp.play();	
            }
			
	function updateStats() {
        var text = 'P2P正在为您加速' + (_totalP2PDownloaded/1024).toFixed(2)
            + 'MB 已分享' + (_totalP2PUploaded/1024).toFixed(2) + 'MB' + ' 连接节点' + _peerNum + '个';
        document.getElementById('stats').innerText = text
    }  			
</script>  
</body>
</html>
```
然后点击海洋CMS后台的播放器选项，拷贝以下代码：
```html
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE9" />
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<SCRIPT LANGUAGE="JavaScript1.2">
adTime=parent.adsTime; 
 
chanceAd=1;
var ns=(document.layers);
var ie=(document.all);
var w3=(document.getElementById  &&  !ie);
adCount=0;
function initAd(){
        if(!ns  &&  !ie  &&  !w3) return;
        if(ie)             adDiv=eval('document.all.sponsorAdDiv.style');
        else if(ns)        adDiv=eval('document.layers["sponsorAdDiv"]');
        else if(w3)        adDiv=eval('document.getElementById("sponsorAdDiv").style');
        randAd=Math.ceil(Math.random()*chanceAd);
        if (ie||w3)
        adDiv.visibility="visible";
        else
        adDiv.visibility ="show";
        if(randAd==1) showAd();
		document.getElementById("zzzif").src=parent.adsPage; 
		document.getElementById("zzzif").height=parent.playerh;
		document.getElementById("zzzif").width=parent.playerw; 		
}
function showAd(){
if(adCount<adTime*10){adCount+=1;
        if (ie){documentWidth  =document.body.offsetWidth/2+document.body.scrollLeft-20;
        documentHeight =document.body.offsetHeight/2+document.body.scrollTop-20;}
        else if (ns){documentWidth=window.innerWidth/2+window.pageXOffset-20;
        documentHeight=window.innerHeight/2+window.pageYOffset-20;}
        else if (w3){documentWidth=self.innerWidth/2+window.pageXOffset-20;
        documentHeight=self.innerHeight/2+window.pageYOffset-20;}
        adDiv.left=documentWidth-200;adDiv.top =documentHeight-200;
        setTimeout("showAd()",100);}else closeAd();
}
function closeAd(){
if (ie||w3)
adDiv.display="none";
else
adDiv.visibility ="hide";
}
onload=initAd;
</script>
</head>
<body style="margin:0px;padding:0px;">
<div id="sponsorAdDiv" style="visibility:hidden; z-index:999; height:105%; width:100%;">
<iframe id="zzzif" name="zzzif" src="" scrolling="no" frameborder="0"></iframe>
</div>
 
<div id="player"></div>
 
<script>
 
var playerh=parent.playerh;
document.getElementById('player').innerHTML  = '<iframe width="100%" height='+playerh+' src="dplayer/dplayer.html?videourl='+parent.nextPage+','+parent.now + ','+parent.next+','+parent.vid+','+parent.vfrom+','+parent.vpart+'" frameborder="0" border="0" marginwidth="0" marginheight="0" scrolling="no" allowfullscreen="allowfullscreen" mozallowfullscreen="mozallowfullscreen" msallowfullscreen="msallowfullscreen" oallowfullscreen="oallowfullscreen" webkitallowfullscreen="webkitallowfullscreen"></iframe>';
</script>   
</body>
</html>
```

## 飞飞CMS
为飞飞CMS播放器增加记忆+P2P播放，在网站根目录下创建dplayer.php文件，代码如下：
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
	#stats{position:fixed;top:5px;left:10px;font-size:10px;z-index:20719029;display: block;background-image: -webkit-linear-gradient(left, #3498db, #f47920 10%, #d71345 20%, #f7acbc 30%,#ffd400 40%, #3498db 50%, #f47920 60%, #d71345 70%, #f7acbc 80%, #ffd400 90%, #3498db);color: transparent;-webkit-text-fill-color: transparent;-webkit-background-clip: text; background-size: 200% 100%;animation: masked-animation 4s infinite linear;}
    #playerCnt{position:inherit}
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
    url = '<?php echo($_REQUEST['url']);?>';
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
            url: url,
            type: 'customHls',
			customType: {
                'customHls': function (video, player) {
                    const hls = new Hls({
                        debug: false,
                        p2pConfig: {
                            logLevel: true,
                            live: false,        // 如果是直播设为true
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
        },
    });
	dp.seek(webdata.get('vod'+url));
    setInterval(function(){
        webdata.set('vod'+url,dp.video.currentTime);
    },1000);
    function updateStats() {
        var text = 'P2P正在为您加速' + (_totalP2PDownloaded/1024).toFixed(2)
            + 'MB 已分享' + (_totalP2PUploaded/1024).toFixed(2) + 'MB' + ' 连接节点' + _peerNum + '个';
        document.getElementById('stats').innerText = text
    }
</script>
</body>
</html>
```
然后点击飞飞CMS后台，选择`系统——>播放来源——>添加播放器`，接着创建与播放器标识一样的js文件，如bjm3u8.js，代码如下：
```javascript
cms_player.yun = false;
if(cms_player.jiexi){
	document.write('<iframe class="embed-responsive-item" src="'+cms_player.jiexi+cms_player.url+'" frameborder="0" scrolling="no" allowfullscreen="true"></iframe>');
}else{
	document.write('<script type="text/javascript" src="/dplayer.php?url='+cms_player.url+'"></script>');
}
```
并把这个文件上传至`/Public/player`目录下。