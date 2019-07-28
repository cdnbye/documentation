
## 绑定域名
Web SDK的使用需要先绑定域名，注意CDNBye后台是根据HTTP请求的Origin字段来获取域名的（如果使用iFrame该域名可能和网站域名不一致）。绑定域名步骤如下：
- 访问`https://oms.cdnbye.com`并注册

- 注册完成后，系统会提示`您还未绑定域名，是否现在绑定？`，点击`去绑定`跳转到域名绑定界面

- 点击左上角的`绑定域名`，在输入框中输入当前使用的网站域名，如`demo.cdnbye.com`

- 还未验证的域名处于不可用状态，点击右边的`认证`按钮，开始进行域名验证

- 验证方式分为两种：DNS验证和文件验证，选择其中一个按提示进行操作即可

## 绑定 App Id 并获取token
移动端SDK和Electron需要在后台绑定AppId以及获取token才能上线正常使用，步骤如下：
- 访问`https://oms.cdnbye.com`并注册

- 点击`应用管理`，首次进入需要先自动生成token，点击`获取Token`

- 将获取的token复制到SDK的初始化函数中

- 点击`创建应用`，在表单中填入应用名称、AppId、平台类别等信息，其中AppId需要与APP相对应，各平台获取方法如下：
    
    - iOS平台在`TARGETS`->`General`->`Bundle Identifier`获取
        <img width="600" src="https://cdnbye.oss-cn-beijing.aliyuncs.com/pic/binding-ios.png" alt="picture">
        
    - Android平台在`app`->`build.gradle`->`android`->`defaultConfig`->`applicationId`获取
        <img width="400" src="https://cdnbye.oss-cn-beijing.aliyuncs.com/pic/binding-android.png" alt="picture">
        
    - Electron的AppId自行拟定即可，并在SDK的`p2pConfig`->`appId`中进行配置

