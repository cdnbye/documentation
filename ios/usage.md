
## 安装方法

[CocoaPods](https://cocoapods.org/) 是针对 Objective-C 的依赖管理工具，它能够将使用第三方库的安装过程变得非常简单和自动化，你能够用下面的命令来安装它：

```bash
$ sudo gem install cocoapods
```

#### Podfile

为了使用 CoacoaPods 集成 CDNByeSDK 到你的 Xcode 工程当中，你需要编写你的 `Podfile`

```ruby
target 'TargetName' do
pod 'CDNByeSDK'
end
```

然后，运行如下的命令：

```bash
$ pod install
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
```
#import <CDNByeKit/CBP2pEngine.h>
```

#### 开始使用
在代码中实例化AVPlayer之后（也可以是其他任何视频播放器），先将URL传给CBP2pEngine，之后将转化的本地URL传给播放器：
```
CBP2pEngine *engine = [[CBP2pEngine alloc] initWithToken:@"free" andP2pConfig:nil];
NSURL *url = [engine parseStreamURL:@"https://your_stream.m3u8"];   
_player = [[AVPlayer alloc] initWithURL:url];
```
就这么简单，你的播放器已经具备P2P能力了！