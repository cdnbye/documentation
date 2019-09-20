
## Environment Configuration
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

### Add Uses Permission
Add relevant uses permissions in `app/src/main/AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

### Allow HTTP Request
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

### Proguard Configuration
Please add the following code in proguard-rules.pro:
```
-dontwarn com.cdnbye.**
-keep class com.cdnbye.**{*;}
-keep interface com.cdnbye.**{*;}
-dontwarn org.webrtc.**
-keep class org.webrtc.**{*;}
```

## Import SDK
### Import by Gradle
```
dependencies {
  implementation 'com.cdnbye:sdk:0.7.0'
}
```

### Import Manually
#### Download SDK
Download the latest version [SDK](https://cdnbye.oss-cn-beijing.aliyuncs.com/android_sdk/cdnbye-0.7.0.jar)，then copy it to `app/libs`. 

#### Modify build.gradle
Add new dependencies to your application's build.gradle `app/build.gradle` as shown below:
```
dependencies {
    implementation 'org.webrtc:google-webrtc:1.0.28513'
    implementation 'com.alibaba:fastjson:1.2.58'
    implementation 'org.java-websocket:Java-WebSocket:1.4.0'
    implementation 'com.orhanobut:logger:2.2.0'
    implementation 'com.squareup.okhttp3:okhttp:3.12.0'
    implementation 'org.nanohttpd:nanohttpd:2.3.1'
    implementation 'com.jakewharton:disklrucache:2.0.2'
    implementation fileTree(include: ['*.jar'], dir: 'libs')
}
```

## Quick Start
It is recommended to initialize `P2pEngine` in `Application.OnCreate()`.
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
        P2pEngine.initEngine(this, YOUR_TOKEN, null);
    }
}
```
Where `token` is your Customer ID. You can set it to "free" when debugging. Please replace it by your own token obtained from console before release. 

### Playback Address
When initializing a media player (or any other video player) instance, before passing it a URL, pass that URL through CDNBye P2P Engine.
```java
private void onPlay(){
  String parsedUrl = P2pEngine.getInstance().parseStreamUrl("https://your_stream.m3u8");
  mediaPlayer.play(parsedUrl);
}
```
That’s it! CDNBye should now be integrated into your app.

#### Demo
A completed example can be found [here](https://github.com/cdnbye/android-p2p-engine)