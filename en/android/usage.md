
## Integration
### Developing Environment

- Android Studio，[download](http://developer.android.com/intl/zh-cn/sdk/index.html)

### Turn on Java 8 support
If not enabled already, you also need to turn on Java 8 support in application's build.gradle, by adding the following to the android section:
```
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

### Manually
#### Import SDK
Download the latest version [SDK]()，then copy `cdnbye.jar` to `app/libs`. 

#### Modify build.gradle
Add new dependencies to your application's build.gradle `app/build.gradle` as shown below:
```
dependencies {
    implementation 'org.webrtc:google-webrtc:1.0.20371'
    implementation 'com.alibaba:fastjson:1.2.58'
    implementation 'org.java-websocket:Java-WebSocket:1.4.0'
    implementation 'com.orhanobut:logger:2.2.0'
    implementation 'com.squareup.okhttp3:okhttp:3.14.2'
    implementation 'com.koushikdutta.async:androidasync:2.2.1'
    implementation 'com.jakewharton:disklrucache:2.0.2'
    implementation files('libs/cdnbye.jar')
}
```

#### Add Uses Permission
Add relevant uses permissions in `app/src/main/AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

#### Proguard Configuration
Please add the following code in proguard-rules.pro:
```

```

## Quick Start
It is recommended to initialize `P2pEngine` in Application.OnCreate().
### Import P2pEngine
```java
import com.cdnbye.sdk.P2pEngine;
```

### Initialize P2pEngine
```java
public class MyApplication extends android.app.Application {
    @Override
    public void onCreate() {
        super.onCreate();
        P2pEngine.initEngine(this, "YOUR_TOKEN", null);
    }
}
```
Where `token` is your Customer ID. Currently this SDK is free of charge, set it to "free" is ok. 

### Playback Address
When initializing a media player (or any other video player) instance, before passing it a URL, pass that URL through CDNBye P2P Engine.
```java
 private void onPlay(){
      String parsedUrl = P2pEngine.getInstance().parseStreamUrl("https://your_stream.m3u8");
      mediaPlayer.play(parsedUrl);
 }
```
That’s it! CDNBye should now be integrated into your app.