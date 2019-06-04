
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
Import CDNByeSDK into your Objective-C app:
```
#import <CDNByeKit/CBP2pEngine.h>
```
If you want to use CDNByeSDK in your Swift app, then you need to create a bridging header that allows your Swift code to work with it.

#### Usage
When initializing an AVPlayer (or any other video player) instance, before passing it a URL, pass that URL through CDNBye P2P Engine:
```ObjC
CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:nil];
NSURL *url = [engine parseStreamURL:@"https://your_stream.m3u8"];   
_player = [[AVPlayer alloc] initWithURL:url];
```
```Swift
let engine = CBP2pEngine.init(token: "free", p2pConfig: nil)
let url = engine.parse(streamURL: "https://your_stream.m3u8")
_player = AVPlayer.init(url: url)
```
That’s it! CDNBye should now be integrated into your app.

