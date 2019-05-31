
## P2P配置
实例化`CBP2pConfig`：
```
CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
```
覆盖相应的字段：

| 字段 | 类型 | 默认值 | 描述 |
| :-: | :-: | :-: | :-: |
| `logLevel` | CBLogLevel | CBLogLevelWarn | 打印日志的级别(CBLogLevelNone, CBLogLevelDebug, CBLogLevelInfo, CBLogLevelWarn, CBLogLevelError)。                                                                                      
| `webRTCConfig` | RTCConfiguration | - | 通过RTCConfiguration来修改WebRTC默认配置。
| `wsSignalerAddr` | NSString | @"wss://signal.cdnbye.com/wss" | 信令服务器地址。
| `maxBufferSize` | NSUInteger | 1024 * 1024 * 1024 | 点播模式下P2P在磁盘缓存的最大数据量。
| `p2pEnabled` | BOOL | YES | 开启或关闭p2p engine。
| `packetSize` | NSUInteger | 64 * 1024 | 每次通过datachannel发送的包的大小，64KB适用于与浏览器进行P2P。
| `downloadTimeout` | NSTimeInterval | 10 | TS文件的下载超时时间。
| `tag` | NSString | @"unknown" | 用户自定义的标签，可以在控制台查看分布图。

## P2P Engine
实例化`CBP2pEngine`：
```
CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:config];
```
Get parsed local stream url by passing the original stream url(m3u8) to `CBP2pEngine` instance:
将原始播放地址(m3u8)传给`CBP2pEngine`，从而获取本地播放地址：
```
NSURL *parsedURL = [engine parseStreamURL:ORIGINAL_URL];
```

## P2P统计
通过`NSNotificationCenter`来监听P2P下载信息：
```
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
```
在回调的字典中获取totalP2PDownloaded、totalP2PUploaded、totalHTTPDownloaded等：
```
- (void)didReceiveMsg:(NSNotification *)note {
    NSDictionary *dict = (NSDictionary *)note.object;
    NSLog(@"didReceiveMsg %@", dict);
}
```

## 完整的例子
```
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
    NSURL *url = [engine parseStreamURL:@"https://video-dev.github.io/streams/x36xhzz/url_2/193039199_mp4_h264_aac_ld_7.m3u8"];
    self.player.player = [[AVPlayer alloc] initWithURL:url];
    
    self.player.view.frame = CGRectMake(0, 100, 400, 400);
    [self.view addSubview:self.player.view];
    
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
    
    [self.player.player play];
}
/*
 通过观察者模式获取P2P下载信息
 */
- (void)didReceiveMsg:(NSNotification *)note {
    NSDictionary *dict = (NSDictionary *)note.object;
    NSLog(@"didReceiveMsg %@", dict);
}
@end
```