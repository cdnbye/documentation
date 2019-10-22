
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
| `downloadTimeout` | NSTimeInterval | 10.0 | TS file download timeout by HTTP.
| `dcDownloadTimeout` | NSTimeInterval | 15.0 | Max download timeout for WebRTC datachannel.
| `tag` | NSString | @"unknown" | User defined tag which is presented in console.
| `maxPeerConnections` | NSUInteger | 10 | Max peer connections at the same time.

## P2P Engine
Initialize `CBP2pEngine` in `AppDelegate.m`:
初始化化`CBP2pEngine`：
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    CBP2pConfig *config = [CBP2pConfig defaultConfiguration];
    [[CBP2pEngine sharedInstance] startWithToken:@"free" andP2pConfig:config];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    let config = CBP2pConfig.defaultConfiguration()
    CBP2pEngine.sharedInstance().start(token: "free", p2pConfig: config)
    return true
}
```
Where `token` is your Customer ID. You can set it to "free" when debugging. Please replace it by your own token obtained from console before release.
Get parsed local stream url by passing the original stream url(m3u8) to `CBP2pEngine` instance:
```objectivec
NSURL *parsedUrl = [[CBP2pEngine sharedInstance] parseStreamURL:ORIGINAL_URL];
```
```swift
let parsedUrl = CBP2pEngine.sharedInstance().parse(streamURL: ORIGINAL_URL)
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

## Advanced Usage
### Switch Stream URL
When Switching to a new stream URL, before passing new stream url(m3u8) to the player, pass that URL through `CBP2pEngine` instance:
```objectivec
NSURL *newParsedURL = [[CBP2pEngine sharedInstance] parseStreamURL:NEW_ORIGINAL_URL];
```
```swift
let newParsedURL = CBP2pEngine.sharedInstance().parse(streamURL: NEW_ORIGINAL_URL)
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
[CBP2pEngine sharedInstance].channelId = ^NSString * _Nonnull(NSString * _Nonnull urlString) {
    NSString *formatedUrl = [Formater convert:urlString];
    return formatedUrl;
};
```
`It is strongly recommended to add a unique identifier to the channelid to prevent conflicts with other channels.`