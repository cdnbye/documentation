
## 环境配置
### 开发环境
- Android Studio 开发工具，[下载地址](http://developer.android.com/intl/zh-cn/sdk/index.html)

### 开启Java8支持
在`app/build.gradle`中加入以下配置以开启Java8支持：
```
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

### 添加相关权限
在`app/src/main`目录中的`AndroidManifest.xml`中增加如下权限声明:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### 允许HTTP请求
从Android P系统开始，如果应用使用的是非加密的明文流量的http网络请求，则会导致该应用无法进行网络请求，https则不会受影响。由于本地代理服务需要使用http协议访问，针对这个问题，有以下两种解决方法：
<br>
（1） `targetSdkVersion` 降到27以下
<br>
（2） 更改网络安全配置，在`app/src/main`目录中的`AndroidManifest.xml`的<application>标签中直接插入：
```xml
<application
  ...
  android:usesCleartextTraffic="true"
  ...
    />
```

### 混淆配置
为了保证正常使用 SDK ，请在 proguard-rules.pro 文件中添加以下代码：
```
-dontwarn com.cdnbye.**
-keep class com.cdnbye.**{*;}
-keep interface com.cdnbye.**{*;}
-dontwarn org.webrtc.**
-keep class org.webrtc.**{*;}
```

## 导入SDK
### Gradle方式导入
添加Jcenter仓库 Gradle依赖：
```
dependencies {
  implementation 'com.cdnbye:sdk:latest.release'
}
```
<!--
### 手动导入
##### 下载 SDK
下载最新版本的[安卓SDK](https://cdnbye.oss-cn-beijing.aliyuncs.com/android_sdk/cdnbye-0.8.0.jar)，并拷贝到工程的`libs`目录下。

##### 修改 build.gradle
双击打开您的工程目录下的`app/build.gradle`，添加如下依赖：
```
dependencies {
    implementation 'org.webrtc:google-webrtc:1.0.30039'
    implementation 'com.alibaba:fastjson:1.2.58'
    implementation 'org.java-websocket:Java-WebSocket:1.4.0'
    implementation 'com.orhanobut:logger:2.2.0'
    implementation 'com.squareup.okhttp3:okhttp:3.12.0'
    implementation fileTree(include: ['*.jar'], dir: 'libs')
}
```
-->
## 快速开始
推荐在`Application.OnCreate()`中启动P2P加速服务。在播放之前使用parseStreamUrl()方法去转换播放地址开始播放。
### 导入 P2pEngine
```java
import com.cdnbye.sdk.P2pEngine;
```

### 实例化 P2pEngine
```java
public class MyApplication extends android.app.Application {
    @Override
    public void onCreate() {
        super.onCreate();
        P2pEngine.initEngine(this, YOUR_TOKEN, null);
    }
}
```
其中 YOUR_TOKEN 是用于标识用户的字符串，请换成自己的token，点击[这里](/bindings?id=绑定-app-id-并获取token)查看如何注册`Appid`并获取`token`。

### 获取播放地址
使用加速功能，必须通过SDK把地址转换成加速地址，再把转换后的地址传给播放器即可（建议使用[ExoPlayer](https://github.com/google/ExoPlayer)）。
```java
private void onPlay(){
  String parsedUrl = P2pEngine.getInstance().parseStreamUrl("https://your_stream.m3u8");
  mediaPlayer.play(parsedUrl);
}
```
就这么简单，你的播放器已经具备P2P能力了！

#### 示例
获取完整的[示例程序](https://github.com/cdnbye/android-p2p-engine)。

#### P2P无效问题排查步骤
请参考[常见问题](/FAQ?id=客户端sdk-p2p无效问题排查步骤)

#### 解决Exoplayer无法播放问题
请参考[常见问题](/FAQ?id=解决exoplayer无法播放问题)
