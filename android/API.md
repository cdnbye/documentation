
## P2P配置
用建造者模式实例化`CBP2pConfig`，以下的参数是默认值：
```java
P2pConfig config = new P2pConfig.Builder()
    .logEnabled(false)                                // 是否开启debug模式
    .logLevel(LogLevel.WARN)                          // 打印日志的级别
    .announce("https://api.cdnbye.com/v1")            // tracker服务器地址
    .wsSignalerAddr("wss://signal.cdnbye.com/wss")    // 信令服务器地址
    .downloadTimeout(10_000, TimeUnit.MILLISECONDS)   // HTTP下载ts文件超时时间
    .dcDownloadTimeout(4_000, TimeUnit.MILLISECONDS)  // datachannel下载二进制数据的超时时间
    .localPort(52019)                                 // 本地代理服务器的端口号
    .maxBufferSize(1024*1024*1024)                    // 点播模式下P2P在磁盘缓存的最大数据量(设为0可以禁用磁盘缓存)。
    .p2pEnabled(true)                                 // 开启或关闭p2p engine
    .packetSize(64*1024)                              // 每次通过datachannel发送的包的大小，64KB适用于与浏览器进行P2P
    .withTag("unknown")                               // 用户自定义的标签，可以在控制台查看分布图
    .webRTCConfig(null)                               // 通过webRTCConfig来修改WebRTC默认配置
    .build();  
P2pEngine.initEngine(getApplicationContext(), token, config);
```

## P2P Engine
实例化P2pEngine，获得一个全局单例：
```java
P2pEngine engine = P2pEngine.initEngine(Context context, String token, P2pConfig config);
```
参数说明:
<br>

| 参数 | 类型 | 是否必须 | 说明 |
| :-: | :-: | :-: | :-: |
| `context` | Context | 是 | 建议使用Application 的 Context 对象。                                                                                      
| `token` | String | 是 | CDNBye分配的token。
| `config` | P2pConfig | 否 | 自定义配置。

### P2pEngine API
#### `P2pEngine.Version`
当前插件的版本号。

#### `P2pEngine.getInstance()`
获取`P2pEngine`的单例。

#### `engine.parseStreamUrl(String url)`
将原始播放地址(m3u8)转换成本地代理服务器的地址。

#### `engine.isConnected()`
是否已与CDNBye后台建立连接。

#### `engine.stopP2p()`
停止P2P加速并释放资源，一般不需要调用。

#### `engine.restartP2p()`
重启P2P加速服务，一般不需要调用。

#### `engine.getPeerId()`
获取对等连接的id。

### P2P统计
通过`P2pStatisticsListener`来监听P2P下载信息：
```java
engine.addP2pStatisticsListener(new P2pStatisticsListener() {
            @Override
            public void onHttpDownloaded(long value) {
            }

            @Override
            public void onP2pDownloaded(long value) {
            }

            @Override
            public void onP2pUploaded(long value) {
            }

            @Override
            public void onPeers(List<String> peers) {
            }
        });
```
PS：下载和上传数据量的单位是KB。

## 高级用法
### 切换源
当播放器切换到新的播放地址时，只需要将新的播放地址(m3u8)传给`P2pEngine`，从而获取新的本地播放地址：
```java
String newParsedURL = P2pEngine.getInstance().parseStreamUrl(url);
```
### 自行配置 STUN 和 TURN 服务器地址
STUN用于p2p连接过程中获取公网IP地址，TURN则可以在p2p连接不通时用于中转数据。本SDK已内置公开的STUN服务，开发者可以通过P2pConfig来更换STUN地址。TURN服务器则需要开发者自行搭建，可以参考[coturn](https://github.com/coturn/coturn)。
```java
import org.webrtc.PeerConnection;
import org.webrtc.PeerConnection.RTCConfiguration;

List<PeerConnection.IceServer> iceServers = new ArrayList<>();
iceServers.add(PeerConnection.IceServer.builder(YOUR_STUN_OR_TURN_SERVER).createIceServer());
RTCConfiguration rtcConfig = new RTCConfiguration(iceServers);
P2pConfig config = new P2pConfig.Builder()
    .webRTCConfig(rtcConfig)
    .build();
```
### 解决动态m3u8路径问题
某些流媒体提供商的m3u8是动态生成的，不同节点的m3u8地址不一样，例如example.com/clientId1/file.m3u8和example.com/clientId2/file.m3u8, 而本插件默认使用m3u8作为channelId。这时候就要构造一个共同的chanelId，使实际观看同一直播/视频的节点处在相同频道中。在构造P2pConfig时通过在ChannelIdCallback的onChannelId方法中重新构造channelId，再把config传给P2pEngine即可：
```java
import com.cdnbye.sdk.ChannelIdCallback;

P2pConfig config = new P2pConfig.Builder()
    .channelId(new ChannelIdCallback() {
        @Override
        public String onChannelId(String urlString) {
            return formatChannelId(urlString);
        }
    }).build();
```
