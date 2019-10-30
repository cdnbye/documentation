
## 安装方法

[CocoaPods](https://cocoapods.org/) 是针对 Objective-C 的依赖管理工具，它能够将使用第三方库的安装过程变得非常简单和自动化，你能够用下面的命令来安装它：

```bash
$ sudo gem install cocoapods
```

#### Podfile

为了使用 CoacoaPods 集成 CDNByeSDK 到你的 Xcode 工程当中，你需要编写你的 `Podfile`
```ruby
target 'TargetName' do
# Uncomment the next line if you're using Swift
# use_frameworks!
pod 'CDNByeSDK'
end
```

然后，运行如下的命令：
```bash
$ pod install
```

如果提示找不到CDNByeSDK，执行以下命令：
```bash
$ pod repo update
```

如果需要更新SDK到最新版本，执行：
```bash
pod update CDNByeSDK --verbose --no-repo-update
```

## 集成方法
CDNBye通过本地代理服务器拦截数据请求的方式来进行P2P缓存和传输，所以需要在项目的info.plist中允许HTTP请求：
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

#### 引入CBP2pEngine
在工程的`AppDelegate.m`文件导入头文件：
```objectivec
#import <CDNByeKit/CBP2pEngine.h>
```

Swift项目需要在统一的bridge头文件（xxx-Bridging-Header.h）里面import。

#### 初始化CBP2pEngine
在工程`AppDelegate.m`的`application:didFinishLaunchingWithOptions:`方法中初始化：
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [[CBP2pEngine sharedInstance] startWithToken:YOUR_TOKEN andP2pConfig:nil];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    CBP2pEngine.sharedInstance().start(token: YOUR_TOKEN, p2pConfig: nil)
    return true
}
```
#### 转换地址
在代码中实例化AVPlayer之后（也可以是其他任何视频播放器），先将URL传给CBP2pEngine，之后将转化的本地URL传给播放器：
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
就这么简单，你的播放器已经具备P2P能力了！

#### 示例
获取完整的[示例程序](https://github.com/cdnbye/ios-p2p-engine)。