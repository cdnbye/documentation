
## Domain
Websites which integrate CDNBye SDK should register domain to activate P2P service. Please note that CDNBye identifies domain by HTTP Origin, player in iFrame may cause domain name inconsistency.
- Visit `https://oms.cdnbye.com` and register

- After the registration is completed, the website will prompt `You have not bound domain name or created app, are you going now?`, click `Bind your domain` to jump to the domain name binding interface

- Click `Bind Domain` button and input your domain, eg: `demo.cdnbye.com`

- The unverified domain name is unavailable. Click `Validate' button to validate your domain name

- There are tow ways of domain validation(DNS and file), select one of them to operate according to the prompt

## App Id and Token
Before release iOS, Android and Electron APP, you should register AppId and obtain token from `console`:
- Visit `https://oms.cdnbye.com` and register

- Click `APP Management` button, if it's the first time you enter this page, please click `Create Token` button

- Copy the obtained token to the initiator of the SDK

- Click on the `Create App` and fill in the form with information such as the App Name, AppId and platform. Please note that the AppId needs to correspond to the APP. The methods for obtaining the AppId are as follows:

    - AppId of iOS platform can be obtained from `TARGETS`->`General`->`Bundle Identifier`
        <img width="600" src="https://cdnbye.oss-cn-beijing.aliyuncs.com/pic/binding-ios.png" alt="picture">
        
    - AppId of Android platform can be obtained from `app`->`build.gradle`->`android`->`defaultConfig`->`applicationId`
        <img width="400" src="https://cdnbye.oss-cn-beijing.aliyuncs.com/pic/binding-android.png" alt="picture">
        
    - Electron's AppId is defined by yourself and should be configured in the SDK's `p2pConfig`->`appId`
    
    - Flutter App need to `Create App` on corresponding platforms according to AppId

