
## Installation with CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries in your projects. You can install it with the following command:

```bash
$ gem install cocoapods
```

#### Podfile

To integrate CDNByeSDK into your Xcode project using CocoaPods, specify it in your `Podfile`:
```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '9.0'

target 'TargetName' do
# Uncomment the next line if you're using Swift
# use_frameworks!   
pod 'CDNByeSDK'
end
```

Then, run the following command:
```bash
$ pod install
```

If can not find CDNByeSDK in repo, run command:
```bash
$ pod repo update
```

Update SDK if needed:
```bash
pod update CDNByeSDK --verbose --no-repo-update
```

## Integration
In order to allow the loading of distributed content via the local proxy, enable loading data from HTTP in your app by opening your info.plist file as source code and adding the following values below the </dict> tag:
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

#### Include
Import CDNByeSDK in `AppDelegate.m`:
```objectivec
#import <CDNByeKit/CBP2pEngine.h>
```
If you want to use CDNByeSDK in your Swift app, then you need to create a bridging header that allows your Swift code to work with it.

#### Initialize CBP2pEngine
Initialize CBP2pEngine in `AppDelegate.m`:
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [[CBP2pEngine sharedInstance] startWithToken:@"free" andP2pConfig:nil];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    CBP2pEngine.sharedInstance().start(token: "free", p2pConfig: nil)
    return true
}
```

#### Usage
When initializing an AVPlayer (or any other video player) instance, before passing it a URL, pass that URL through CDNBye P2P Engine:
```objectivec
NSURL *originalUrl = [NSURL URLWithString:@"https://your_stream.m3u8"];
NSURL *parsedUrl = [[CBP2pEngine sharedInstance] parseStreamURL:originalUrl];
_player = [[AVPlayer alloc] initWithURL:parsedUrl];
```
```swift
let orginalUrl = URL.init(string: "https://your_stream.m3u8")
let parsedUrl = CBP2pEngine.sharedInstance().parse(streamURL: orginalUrl!)
_player = AVPlayer.init(url: parsedUrl)
```
That’s it! CDNBye should now be integrated into your app.

#### Demo
A completed example can be found [here](https://github.com/cdnbye/ios-p2p-engine)