
## P2P Configuration
Create `CBP2pConfig` instance.
```ObjC
CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
```
```Swift
let config = CBP2pConfig.defaultConfiguration()
```

The default fields (shown below) can be overridden.

| Field | Type | Default | Description |
| :-: | :-: | :-: | :-: |
| `logLevel` | CBLogLevel | CBLogLevelWarn | Print log level(CBLogLevelNone, CBLogLevelDebug, CBLogLevelInfo, CBLogLevelWarn, CBLogLevelError).                                                                                       
| `webRTCConfig` | RTCConfiguration | - | Providing options to configure WebRTC connections.
| `wsSignalerAddr` | NSString | @"wss://signal.cdnbye.com/wss" | The address of signal server.
| `maxBufferSize` | NSUInteger | 1024 * 1024 * 1024 | The max size of binary data that can be stored in the cache for VOD.
| `p2pEnabled` | BOOL | YES | Enable or disable p2p engine.
| `packetSize` | NSUInteger | 64 * 1024 | The maximum package size sent by datachannel, 64KB should work with most of recent browsers.
| `downloadTimeout` | NSTimeInterval | 10 | TS file download timeout.
| `tag` | NSString | @"unknown" | User defined tag which is presented in console.

## P2P Engine
Initialize `CBP2pEngine` to an instance or static variable:
```ObjC
CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:config];
```
```Swift
let engine = CBP2pEngine.init(token: "free", p2pConfig: config)
```
Get parsed local stream url by passing the original stream url(m3u8) to `CBP2pEngine` instance:
```ObjC
NSURL *parsedURL = [engine parseStreamURL:ORIGINAL_URL];
```
```Swift
let parsedURL = engine.parse(streamURL: ORIGINAL_URL)
```

## P2P Statistics
Add a observer to observe downloading statistics:
```ObjC
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceiveMsg:) name:kP2pEngineDidReceiveStatistics object:nil];
```
```Swift
NotificationCenter.default.addObserver(self, selector: #selector(didReceiveMsg), name: NSNotification.Name(rawValue: kP2pEngineDidReceiveStatistics), object: nil)
```
Then get the downloading statistics, including p2pDownloaded, p2pUploaded, httpDownloaded and peers from dictionary:
```ObjC
- (void)didReceiveMsg:(NSNotification *)note {
    NSDictionary *dict = (NSDictionary *)note.object;
    NSLog(@"didReceiveMsg %@", dict);
}
```
```Swift
@objc func didReceiveMsg(note:NSNotification) {
}
```
PS: The unit of download and upload is KB.

## Complete Example
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
```ObjC
NSURL *newParsedURL = [engine parseStreamURL:NEW_ORIGINAL_URL];
```
```Swift
let newParsedURL = engine.parse(streamURL: NEW_ORIGINAL_URL)
```
### Dynamic m3u8 path issue
Some m3u8 urls play the same live/vod but have different paths on them. For example, example.com/clientId1/file.m3u8 and example.com/clientId2/file.m3u8. In this case, you can format a common channelId for them.
```ObjC
engine.channelId = ^NSString * _Nonnull(NSString * _Nonnull urlString) {
    NSString *formatedUrl = [Formater convert:urlString];
    return formatedUrl;
};
```