
## P2P Configuration
Create `CBP2pConfig` instance.
```objectivec
CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
```
```swift
let config = CBP2pConfig.defaultConfiguration()
```

The default fields (shown below) can be overridden.

| Field | Type | Default | Description |
| :-: | :-: | :-: | :-: |
| `logLevel` | CBLogLevel | CBLogLevelWarn | Print log level(CBLogLevelNone, CBLogLevelDebug, CBLogLevelInfo, CBLogLevelWarn, CBLogLevelError).                                                                                       
| `webRTCConfig` | RTCConfiguration | - | Providing options to configure WebRTC connections.
| `announce` | NSString | @"https://tracker.cdnbye.com/v1" | The address of tracker server.
| `wsSignalerAddr` | NSString | @"wss://signal.cdnbye.com" | The address of signal server.
| `diskCacheLimit` | NSUInteger | 1024 * 1024 * 1024 | The max size of binary data that can be stored in the cache for VOD(Set to 0 will disable disk cache).
| `memoryCacheLimit` | NSUInteger | 100 * 1024 * 1024 | The max size of binary data that can be stored in the memory cache.
| `p2pEnabled` | BOOL | YES | Enable or disable p2p engine.
| `localPort` | NSUInteger | 52019 | The port for local http server.
| `packetSize` | NSUInteger | 64 * 1024 | The maximum package size sent by datachannel, 64KB should work with most of recent browsers.
| `downloadTimeout` | NSTimeInterval | 10 | TS file download timeout by HTTP.
| `dcDownloadTimeout` | NSTimeInterval | 3 | Download timeout for WebRTC datachannel.
| `tag` | NSString | @"unknown" | User defined tag which is presented in console.
| `maxPeerConnections` | NSUInteger | 10 | Max peer connections at the same time.

## P2P Engine
Initialize `CBP2pEngine` to an instance or static variable:
```objectivec
CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:config];
```
```swift
let engine = CBP2pEngine.init(token: "free", p2pConfig: config)
```
Where `token` is your Customer ID. You can set it to "free" when debugging. Please replace it by your own token obtained from console before release.
Get parsed local stream url by passing the original stream url(m3u8) to `CBP2pEngine` instance:
```objectivec
NSURL *parsedUrl = [engine parseStreamURL:ORIGINAL_URL];
```
```swift
let parsedUrl = engine.parse(streamURL: ORIGINAL_URL)
```

## P2P Statistics
Add a observer to observe downloading statistics:
```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
```
```swift
NotificationCenter.default.addObserver(self, selector: #selector(didReceiveMsg), name: NSNotification.Name(rawValue: kP2pEngineDidReceiveStatistics), object: nil)
```
Then get the downloading statistics, including p2pDownloaded, p2pUploaded, httpDownloaded and peers from dictionary:
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
PS: The unit of download and upload is KB.

## Complete Example
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

## Advanced Usage
### Switch Stream URL
When Switching to a new stream URL, before passing new stream url(m3u8) to the player, pass that URL through `CBP2pEngine` instance:
```objectivec
NSURL *newParsedURL = [engine parseStreamURL:NEW_ORIGINAL_URL];
```
```swift
let newParsedURL = engine.parse(streamURL: NEW_ORIGINAL_URL)
```
### Use Your Own STUN or TURN Server
STUN (Session Traversal Utilities for NAT) allows clients to discover their public IP address and the type of NAT they are behind. This information is used to establish the media connection. Although there are default STUN servers in this SDK, you can replace them with your own via P2PConfig. TURN (Traversal Using Relays around NAT) server is used to relay traffic if direct connection fails. You can config your [TURN](https://github.com/coturn/coturn) server in the same way as STUN.
```objectivec
#import <WebRTC/RTCIceServer.h>

NSMutableArray *ICEServers = [NSMutableArray array];
RTCIceServer *server = [[RTCIceServer alloc] initWithURLStrings:@[YOUR_STUN_OR_TURN_SERVER]];
[ICEServers addObject:server];
config.webRTCConfig = [[RTCConfiguration alloc] init];
config.webRTCConfig.iceServers = ICEServers;
```
### Dynamic m3u8 Path Issue
Some m3u8 urls play the same live/vod but have different paths on them. For example, example.com/clientId1/file.m3u8 and example.com/clientId2/file.m3u8. In this case, you can format a common channelId for them.
```objectivec
engine.channelId = ^NSString * _Nonnull(NSString * _Nonnull urlString) {
    NSString *formatedUrl = [Formater convert:urlString];
    return formatedUrl;
};
```