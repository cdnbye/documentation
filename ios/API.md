
## P2P配置
实例化`CBP2pConfig`：
```objectivec
CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
```
```swift
let config = CBP2pConfig.defaultConfiguration()
```
覆盖相应的字段：

| 字段 | 类型 | 默认值 | 描述 |
| :-: | :-: | :-: | :-: |
| `logLevel` | CBLogLevel | CBLogLevelWarn | 打印日志的级别(CBLogLevelNone, CBLogLevelDebug, CBLogLevelInfo, CBLogLevelWarn, CBLogLevelError)。                                                                                      
| `webRTCConfig` | RTCConfiguration | - | 通过RTCConfiguration来修改WebRTC默认配置。
| `announce` | NSString | @"https://tracker.cdnbye.com/v1" | tracker服务器地址。
| `wsSignalerAddr` | NSString | @"wss://signal.cdnbye.com" | 信令服务器地址。
| `diskCacheLimit` | NSUInteger | 1024 * 1024 * 1024 | 点播模式下P2P在磁盘缓存的最大数据量(设为0可以禁用磁盘缓存)。
| `memoryCacheLimit` | NSUInteger | 100 * 1024 * 1024 | P2P在内存缓存的最大数据量。
| `p2pEnabled` | BOOL | YES | 开启或关闭p2p engine。
| `wifiOnly` | BOOL | NO | 是否只在wifi模式上传数据（建议在云端设置）。
| `localPort` | NSUInteger | 52019 | 本地代理服务器的端口号。
| `downloadTimeout` | NSTimeInterval | 10.0 | HTTP下载ts文件超时时间（单位：秒）。
| `dcDownloadTimeout` | NSTimeInterval | 15.0 | datachannel下载二进制数据的最大超时时间（单位：秒）。
| `tag` | NSString | @"unknown" | 用户自定义的标签，可以在控制台查看分布图。
| `maxPeerConnections` | NSUInteger | 20 | 最大连接节点数量。
| `useHttpRange` | BOOL | YES | 在可能的情况下使用Http Range请求来补足p2p下载超时的剩余部分数据。
| `userAgent` | NSString | nil | 设置请求ts时候的User-Agent

## P2P Engine
初始化化`CBP2pEngine`：
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
    [[CBP2pEngine sharedInstance] startWithToken:YOUR_TOKEN andP2pConfig:config];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    let config = CBP2pConfig.defaultConfiguration()
    CBP2pEngine.sharedInstance().start(token: YOUR_TOKEN, p2pConfig: config)
    return true
}
```
其中token是用于标识用户的字符串，在调试阶段将token设为"free"即可。在上线前请替换成从控制台获取的token。将原始播放地址(m3u8)传给`CBP2pEngine`，从而获取本地播放地址：
```objectivec
NSURL *parsedUrl = [[CBP2pEngine sharedInstance] parseStreamURL:ORIGINAL_URL];
```
```swift
let parsedUrl = CBP2pEngine.sharedInstance().parse(streamURL: ORIGINAL_URL)
```

## P2P统计
通过`NSNotificationCenter`来监听P2P下载信息：
```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
```
```swift
NotificationCenter.default.addObserver(self, selector: #selector(didReceiveMsg), name: NSNotification.Name(rawValue: kP2pEngineDidReceiveStatistics), object: nil)
```
在回调的字典中获取`p2pDownloaded`、`p2pUploaded`、`httpDownloaded`、`peers`、`serverConnected`等：
```objectivec
- (void)didReceiveMsg:(NSNotification *)note {
    NSDictionary *dict = (NSDictionary *)note.object;
    NSLog(@"didReceiveMsg %@", dict);
}
```
```swift
@objc func didReceiveMsg(note:NSNotification) {
}
```
PS：下载和上传数据量的单位是KB。

## 高级用法
### 切换源
当播放器切换到新的播放地址时，只需要将新的播放地址(m3u8)传给`CBP2pEngine`，从而获取新的本地播放地址：
```objectivec
NSURL *newParsedURL = [[CBP2pEngine sharedInstance] parseStreamURL:NEW_ORIGINAL_URL];
```
```swift
let newParsedURL = CBP2pEngine.sharedInstance().parse(streamURL: NEW_ORIGINAL_URL)
```

### 切换信令
某些场景下需要动态修改信令地址，防止单个信令负载过大，例如根据播放地址的哈希值选择信令。可以通过`CBP2pEngine`运行时动态调整配置，示例如下：
```objectivec
CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
config.wsSignalerAddr = @"wss://yoursignal2.com";
[CBP2pEngine sharedInstance].p2pConfig = config;
```
需要注意的是这个方法会重置`CBP2pEngine`的所有config，因此之前已经修改的字段需要再设置一次以保持一致。


### 自行配置 STUN 和 TURN 服务器地址
STUN用于p2p连接过程中获取公网IP地址，TURN则可以在p2p连接不通时用于中转数据。本SDK已内置公开的STUN服务，开发者可以通过P2pConfig来更换STUN地址。TURN服务器则需要开发者自行搭建，可以参考[coturn](https://github.com/coturn/coturn)。
```objectivec
#import <WebRTC/RTCIceServer.h>

NSMutableArray *ICEServers = [NSMutableArray array];
RTCIceServer *server = [[RTCIceServer alloc] initWithURLStrings:@[YOUR_STUN_OR_TURN_SERVER]];
[ICEServers addObject:server];
config.webRTCConfig = [[RTCConfiguration alloc] init];
config.webRTCConfig.iceServers = ICEServers;
```
### 解决动态m3u8路径问题
某些流媒体提供商的m3u8是动态生成的，不同节点的m3u8地址不一样，例如example.com/clientId1/file.m3u8和example.com/clientId2/file.m3u8, 而本插件默认使用m3u8作为channelId。这时候就要构造一个共同的chanelId，使实际观看同一直播/视频的节点处在相同频道中。
```objectivec
[CBP2pEngine sharedInstance].channelId = ^NSString * _Nonnull(NSString * _Nonnull urlString) {
    NSString *formatedUrl = [Formater convert:urlString];
    return formatedUrl;
};
```
`强烈建议在chanelId中加入唯一标识符，防止与其他频道产生冲突。`