
> Live/VOD Flutter P2P Engine for iOS/Android

<a href="https://pub.dartlang.org/packages/cdnbye"><img src="https://img.shields.io/pub/v/cdnbye.svg" alt="pub"></a>

### Features
- Support iOS and Android platform
- Support live and VOD streams over HLS protocol(m3u8)
- Support encrypted HLS stream
- Support cache to avoid repeating the download of TS file
- Very easy to integrate with an existing flutter project
- Support any flutter player
- Efficient scheduling policies to enhance the performance of P2P streaming
- Highly configurable
- Use IP database to group up peers by ISP and regions

### Installation
Add this to your package's pubspec.yaml file:
```
dependencies:
  cdnbye: ^0.6.0
```

### iOS
Requirement: This library requires iOS 9.0+. NOTICE: This framework doesn’t support bitcode currently.
<br>
Uncomment `# platform :ios, '9.0'` at the second line of `ios/Podfile` in your flutter project.
<br>
In order to allow the loading of distributed content via the local proxy, enable loading data from HTTP in your app by opening your info.plist file as source code and adding the following values below the </dict> tag:
```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### Android
Requirement: Kitkat 4.4(API level >= 19), please set `minSdkVersion` to `19`.
<br>
Add relevant uses permissions in `app/src/main/AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_MULTICAST_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```
Starting with Android 9 (API level 28), cleartext support is disabled by default. There are 2 solutions:
<br>
（1） set `targetSdkVersion` under 27
<br>
（2） Add the attribute below in `app/src/main/AndroidManifest.xml` to indicate that the app intends to use cleartext HTTP:
```xml
<application
  ...
  android:usesCleartextTraffic="true"
  ...
    />
```

### Example
```dart
import 'package:flutter/material.dart';
import 'package:video_player/video_player.dart';
import 'package:cdnbye/cdnbye.dart';

// Init p2p engine
_initEngine();

// Start playing video
_loadVideo();

_initEngine() async {
    await Cdnbye.init(
      YOUR_TOKEN,
      config: P2pConfig.byDefault()
    );
}

_loadVideo() async {
    var url = YOUR_STREAM_URL;
    url = await Cdnbye.parseStreamURL(url);           // Parse your stream url
    player = VideoPlayerController.network(url);
}
```

### Obtain Token
See [here](https://docs.cdnbye.com/#/en/bindings?id=app-id-and-token)

