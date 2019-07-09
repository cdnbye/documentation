
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
| `wsSignalerAddr` | NSString | @"wss://signal.cdnbye.com/wss" | 信令服务器地址。
| `maxBufferSize` | NSUInteger | 1024 * 1024 * 1024 | 点播模式下P2P在磁盘缓存的最大数据量。
| `p2pEnabled` | BOOL | YES | 开启或关闭p2p engine。
| `localPort` | NSUInteger | 52019 | 本地代理服务器的端口号。
| `packetSize` | NSUInteger | 64 * 1024 | 每次通过datachannel发送的包的大小，64KB适用于与浏览器进行P2P。
| `downloadTimeout` | NSTimeInterval | 10 | HTTP下载ts文件超时时间（单位：秒）。
| `dcDownloadTimeout` | NSTimeInterval | 3 | datachannel下载二进制数据的超时时间（单位：秒）。
| `tag` | NSString | @"unknown" | 用户自定义的标签，可以在控制台查看分布图。

## P2P Engine
实例化`CBP2pEngine`：
```objectivec
CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:config];
```
```swift
let engine = CBP2pEngine.init(token: "free", p2pConfig: config)
```
其中token是用于标识用户的字符串，目前SDK是免费使用的，因此token设为"free"即可。将原始播放地址(m3u8)传给`CBP2pEngine`，从而获取本地播放地址：
```objectivec
NSURL *parsedUrl = [engine parseStreamURL:ORIGINAL_URL];
```
```swift
let parsedUrl = engine.parse(streamURL: ORIGINAL_URL)
```

## P2P统计
通过`NSNotificationCenter`来监听P2P下载信息：
```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
```
```swift
NotificationCenter.default.addObserver(self, selector: #selector(didReceiveMsg), name: NSNotification.Name(rawValue: kP2pEngineDidReceiveStatistics), object: nil)
```
在回调的字典中获取p2pDownloaded、p2pUploaded、httpDownloaded、peers等：
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

## 完整的例子
```objectivec
#import "ViewController.h"
#import <AVFoundation/AVFoundation.h>
#import <AVKit/AVKit.h>
#import <CDNByeKit/CBP2pEngine.h>

@interface ViewController ()
@property (strong, nonatomic)AVPlayerViewController *player;
@end

@implementation ViewController
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    self.player = [[AVPlayerViewController alloc] init];
    CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
    config.logLevel =  CBLogLevelDebug;
    config.tag = @"avplayer";
    CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:config];
    NSURL *originalUrl = [NSURL URLWithString:@"https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"];
    NSURL *parsedUrl = [engine parseStreamURL:originalUrl];
    self.player.player = [[AVPlayer alloc] initWithURL:parsedUrl];
    
    self.player.view.frame = CGRectMake(0, 100, 400, 400);
    [self.view addSubview:self.player.view];
    
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
    
    [self.player.player play];
}
/*
 Get the downloading statistics, including totalP2PDownloaded, totalP2PUploaded and totalHTTPDownloaded.
 */
- (void)didReceiveMsg:(NSNotification *)note {
    NSDictionary *dict = (NSDictionary *)note.object;
    NSLog(@"didReceiveMsg %@", dict);
}
@end
```

## 高级用法
### 切换源
但播放器切换到新的播放地址时，只需要将新的播放地址(m3u8)传给`CBP2pEngine`，从而获取新的本地播放地址：
```objectivec
NSURL *newParsedURL = [engine parseStreamURL:NEW_ORIGINAL_URL];
```
```swift
let newParsedURL = engine.parse(streamURL: NEW_ORIGINAL_URL)
```
### 自行配置stun服务器地址
```objectivec
#import <WebRTC/RTCIceServer.h>

NSMutableArray *ICEServers = [NSMutableArray array];
RTCIceServer *server = [[RTCIceServer alloc] initWithURLStrings:@[@"YOUR_STUN_SERVER"]];
[ICEServers addObject:server];
config.webRTCConfig = [[RTCConfiguration alloc] init];
config.webRTCConfig.iceServers = ICEServers;
```
### 解决动态m3u8路径问题
某些流媒体提供商的m3u8是动态生成的，不同节点的m3u8地址不一样，例如example.com/clientId1/file.m3u8和example.com/clientId2/file.m3u8, 而本插件默认使用m3u8作为channelId。这时候就要构造一个共同的chanelId，使实际观看同一直播/视频的节点处在相同频道中。
```objectivec
engine.channelId = ^NSString * _Nonnull(NSString * _Nonnull urlString) {
    NSString *formatedUrl = [Formater convert:urlString];
    return formatedUrl;
};
```