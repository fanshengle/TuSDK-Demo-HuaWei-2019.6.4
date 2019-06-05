# TuSDK 资源文件操作说明

## 文件构成

### 包名

iOS 应用的包名是 Bundle Identifier，它定义在 Project Target 中的 Bundle Identifier。

Android 的应用包名分为两种情况：
* 在 Eclipse 中，Android 应用的包名是 Package Name。
* 在 Android Studio 中，如果有在 build.gradle 文件中定义 applicationId 属性，那么包名即是指此处 applicationId 的值；如果没有在 build.gradle 文件中定义 applicationId，那么包名还是指 AndroidManifest.xml 文件中的 package属性的值

### 秘钥（AppKey）

* 替换 TuSDK 初始化中的秘钥（AppKey）
* iOS [TuSDK initSdkWithAppKey:@"xxxxxx5d12xxxxxx-04-xxxxxx"];
* Android TuSdk.init(this.getApplicationContext(), "xxxxxx5d12xxxxxx-04-xxxxxx");

### 资源文件

* 提供的压缩包中会有 **package_XXXXXX.zip** 文件。
* 解压缩该文件后会有，*other*，*texture*，*sticker* 文件。
* 资源文件可能会因为用户使用的服务不同，只有 *other* 和 *texture* 两个文件夹。


## 替换文件

* 替换 AppKey 至 TuSDK init 初始化方法中秘钥（AppKey）。
* 将解压缩后的文件替换 **TuSDK.bundle**  文件中的对应文件。


## FAQ


### iOS 相机滤镜替换使用的滤镜的代号

* 替换资源文件后，查看资源文件（TuSDK.bundle/others/lsq_config.json）中滤镜的 filerCode，替换到项目中对应的位置。
* 替换滤镜资源后，需要根据新的 filterCode 更改对应滤镜效果缩略图文件的名称。
* "name":"lsq_filter_VideoFair"`，`VideoFair ` 就是该滤镜的filterCode ，在`_videoFilters = @[@"VideoFair"];` 可以进行选择使用滤镜的设置。

### Android 相机滤镜替换使用的滤镜的代号

* 可以在打包下载的资源文件中找到 `lsq_tusdk_configs.json` 文件，之前在控制台所打包的滤镜资源会在这个文件中显示，比如`"name":"lsq_filter_VideoFair"`，则该滤镜的 filterCode 即为 `VideoFair`，需注意大小写。
* 可以将需要用到的 `filterCode` 放到一个滤镜数组中，切换滤镜时从该数组中取出 `filterCode` 即可，如下：
* 	要支持多款滤镜，直接添加到数组即可 private String[] videoFilters = new String[]{"VideoFair"};


## 关注

* 博客 [blog.tusdk.com](http://blog.tusdk.com/)
* 微博 [@TuSDK](http://weibo.com/tusdk)
* Twitter [@TuSDKHQ](https://twitter.com/TuSDKHQ)
* 微信 [TuSDK-HQ](http://tusdk.com/img/tusdk-wechat-qrcode.png)
* Telegram [@TuSDK](https://telegram.me/TuSDK)

更多信息请访问 [tutucloud.com](http://tutucloud.com/)，欢迎通过 [support@tusdk.com](mailto:support@tusdk.com) 与我们联系。


