# TuSDK 功能操作说明

## 1.文件构成

### 1.包名

iOS 应用的包名是 Bundle Identifier，它定义在 Project Target 中的 Bundle Identifier。


### 2.秘钥（AppKey）

* 替换 TuSDK 初始化中的秘钥（AppKey），`AppDelegate.m`中引入 #import <TuSDK/TuSDK.h>。
* 进行初始化 [TuSDK initSdkWithAppKey:@"xxxxxx5d12xxxxxx-04-xxxxxx"];


### 3.资源文件

* 提供的压缩包中会有 **package_XXXXXX.zip** 文件。
* 解压缩该文件后会有，*other*，*texture*，*sticker* 文件。
* 资源文件可能会因为用户使用的服务不同，只有 *other* 和 *texture* 两个文件夹。


### 4.文件替换操作

* 替换 AppKey 至 TuSDK init 初始化方法中秘钥（AppKey）。
* 将解压缩后的文件替换 **TuSDK.bundle**  文件中的对应文件。


## 2.环境配置

1、将示例工程源码中以下文件拖入到 Xcode 项目中。

* GPUImage.framework
* `libyuv.framework` 
* `TuSDK.framework`
* `TuSDKVideo.framework`
* `TuSDKFace.framework`
* `TuSDK.strings` 为项目语言文件。（如有自己的语言文件可以自行配置文案显示）
* `TuSDK.bundle` 为项目资源文件，包含滤镜，动态贴纸等文件。

2、勾选 **Copy items if needed**，点击 **Finish**。

3、打开 app target，查看 **Build Settings** 中的 **Linking** - **Other Linker Flags** 选项，确保含有 `-ObjC` 一值，若没有则添加。用户使用 Cocoapods 进行了第三方依赖库管理，需要在 `-ObjC` 前添加 `$(inherited)`。`目前直播 SDK 暂不支持 Cocoapods`。

4、在项目的 app target 中，查看 **Build Phases** 中的 **Linking** - **Link Binary With Libraries** 选项中，手动添加 `Photos.framework`，并将 Photos.framework 的 Status 设置成 `Optional`。

5、在项目的 app target 中，查看 **Build Phases** 中的 **Linking** - **Link Binary With Libraries** 选项中，手动添加 `Accelerate.framework`。

6、在项目的 app target 中，查看 **Build Phases** 中的 **Linking** - **Link Binary With Libraries** 选项中，手动添加 `libz.tbd`和`libbz2.tbd`。

7、在项目的 app target 中，查看 **Build Phases** 中的 **Linking** - **Link Binary With Libraries** 选项中，手动添加 `libz.1.2.5.tbd`，`libstdc++.tbd`，`libstdc++.6.tbd`和`libstdc++.6.0.9.tbd`。

8、用户在 TuSDK 网站控制台内查看项目需要的滤镜，并将资源文件进行「打包」操作，然后在「资源打包」处下载。下载的资源文件中包含 `others` 和 `textures`，需要将这两个文件夹替换到 TuSDK.bundle 中对应位置。

9、用户可以在 `TuSDKVideoDemo/Resources` 文件中找到 TuSDK.bundle 文件。文件中包含 `others` ，`textures` 和`ui_default` 三个文件夹。用户需要替换 `others/lsq_tusdk_configs.json` 文件和 `textures` 整个文件夹。

10、SDK 暂时不支持 Cocoapods，进行更新操作，请重复步骤1和步骤7。


## 3.TuSDK 的初始化

1、在`AppDelegate.m`引入头文件 `#import <TuSDK/TuSDK.h>`。

2、在 `AppDelegate.m` 的 `didFinishLaunchingWithOptions` 方法中添加初始化代码，用户如果需求同一应用不同版本发布，可以参考文档[如何使用多个masterkey](https://tutucloud.com/docs/ios-faq/masterkey)

3、为便于开发，可打开 TuSDK 的调试日志，在初始化方法的同一位置添加以下代码：`[TuSDK setLogLevel:lsqLogLevelDEBUG];`发布应用时请关闭日志。

 
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	    // 初始化SDK (请前往 http://tusdk.com 获取您的 APP 开发密钥)
	    [TuSDK initSdkWithAppKey:@"828d700d182dd469-04-ewdjn1"];
	    // 多 masterkey 方式启动方法
	    // @see-https://tusdk.com/docs/ios-faq/masterkey
	    // if ([[[NSBundle mainBundle] bundleIdentifier] isEqualToString:@"com.XXXXXXXX.XXXX"]) {
	    //  [TuSDK initSdkWithAppKey:@"714f0a1265b39708-02-xie0p1" devType:@"release"];
	    //}
	    // 开发时请打开调试日志输出
	    [TuSDK setLogLevel:lsqLogLevelDEBUG];
	}
 

## 4.TuSDKFilterProcessor 的使用

TuSDKFilterProcessor 是视频滤镜处理 API 的接口，处理的是视频 buffer 数据

### 1.TuSDKFilterProcessor 初始化

1.初始化滤镜代号（filterCode）的数组，TuSDKFilterProcessor 和 滤镜栏初始化均需要滤镜代号，所以需要提前配置。

```
    _videoFilters =@[@"Normal",@"porcelain",@"nature"];
    _videoFilterIndex = 0;
```

2.初始化 TuSDKFilterProcessor

* 在文件中引入 `#import <TuSDKVideo/TuSDKVideo.h>`
* 创建对象 **@property (nonatomic,strong) TuSDKFilterProcessor *filterProcessor;**
* 初始化对象

```

    // 传入图像的方向是否为原始朝向(相机采集的原始朝向)，SDK 将依据该属性来调整人脸检测时图片的角度。如果没有对图片进行旋转，则为 YES
    BOOL isOriginalOrientation = NO;
    
    // 初始化，输入的数据类型支持 BGRA 和 YUV 数据
    self.filterProcessor = [[TuSDKFilterProcessor alloc] initWithFormatType:kCVPixelFormatType_32BGRA isOriginalOrientation:isOriginalOrientation];
    
    // 遵守代理 TuSDKFilterProcessorDelegate
    self.filterProcessor.delegate = self;
    
    // 是否开启了镜像
    self.filterProcessor.horizontallyMirrorFrontFacingCamera = NO;
    // 告知摄像头默认位置
    self.filterProcessor.cameraPosition = AVCaptureDevicePositionFront;
    // 输出是否按照原始朝向
    self.filterProcessor.adjustOutputRotation = NO;
    // 开启动态贴纸服务（需要大眼瘦脸特效和动态贴纸的功能需要开启该选项）
    [self.filterProcessor setEnableLiveSticker:YES];
    
    // 切换滤镜（在 TuSDKFilterProcessor 初始化前需要提前配置滤镜代号，即 filterCode 的数组）
    // 默认选中的滤镜代号，这个要与 filterView 默认选择的滤镜顺序保持一致
    [_filterProcessor switchFilterWithCode:_videoFilters[1]];
```

* 代理方法

```
/**
 *  滤镜改变 (如需操作UI线程， 请检查当前线程是否为主线程)
 *
 *  @param processor 视频处理对象
 *  @param newFilter 新的滤镜对象
 */
- (void)onVideoProcessor:(TuSDKFilterProcessor *)processor filterChanged:(TuSDKFilterWrap *)newFilter;
{
    //赋值新滤镜 同时刷新新滤镜的参数配置；（配合滤镜栏使用）
    _currentFilter = newFilter;
    [_filterView refreshAdjustParameterViewWith:newFilter.code filterArgs:newFilter.filterParameter.args];

}

```

### 2.方法调用

```

- (CVPixelBufferRef)shortVideoRecorder:(PLShortVideoRecorder *)recorder cameraSourceDidGetPixelBuffer:(CVPixelBufferRef)pixelBuffer {
    //此处可以做美颜/滤镜等处理
    // TuSDK mark - TUSDK 美颜处理 暂时屏蔽其他滤镜处理，可根据需求使用
    pixelBuffer =  [_filterProcessor syncProcessPixelBuffer:pixelBuffer];
    
    // 销毁处理缓存的数据
    [_filterProcessor destroyFrameData];

/*

七牛的逻辑省略

*/
    
    return pixelBuffer;
}


```

### 3.使用的位置

1. 相机中需要设置  [self.filterProcessor setEnableLiveSticker:YES];，即开启人脸服务。
2. 视频编辑中需要设置  [self.filterProcessor setEnableLiveSticker:NO];，即关闭人脸服务。
3. 视频编辑中需要去除贴纸使用的相关代码，只保留滤镜调用的代码。

### 4.滤镜的使用

1.引入头文件 #import "FilterView.h"
2.遵守代理 FilterViewEventDelegate
3.创建对象

```
// 滤镜栏
@property (nonatomic, strong) FilterView *filterView;
// 当前获取的滤镜对象
@property (nonatomic,strong) TuSDKFilterWrap *currentFilter;

```

4.初始化滤镜栏

```

    // 注：当前Demo中参数调节栏和左滑油滑手势冲突，在自己的项目中可自定义UI
    CGFloat filterViewHeight = 246;
    _filterView = [[FilterView alloc]initWithFrame:CGRectMake(0, self.view.lsqGetSizeHeight - filterViewHeight, self.view.lsqGetSizeWidth, filterViewHeight)];
    // 是否允许调节参数，默认false; 不调节参数时，不会创建滤镜的参数栏
    _filterView.canAdjustParameter = true;
    // 遵守代理
    _filterView.filterEventDelegate = self;
    // 默认选中的滤镜代号，这个要与 filterProcessor 默认选择的滤镜顺序保持一致
    // 同时选择第二个滤镜代号保持 UI 一致
    _filterView.currentFilterTag = 1;
    _filterView.backgroundColor = [UIColor colorWithRed:0.22 green:0.22 blue:0.22 alpha:0.7];
    [_filterView createFilterWith:_videoFilters];
    [_filterView refreshAdjustParameterViewWith:_currentFilter.code filterArgs:_currentFilter.filterParameter.args];
    
    [self.view addSubview:_filterView];
    _filterView.hidden = YES;
    
```


5.代理方法

```
#pragma mark -- 滤镜栏点击代理方法 FilterEventDelegate

// 调节滤镜效果
- (void)filterViewParamChangedWith:(TuSDKICSeekBar *)seekbar changedProgress:(CGFloat)progress
{
    //  滤镜效果    mixied
    //  滤镜磨皮强度 smoothing
    //  滤镜瘦脸强度 chinSize 建议最大强度 0.4 arg.precent = progress * 0.4;
    //  滤镜大眼强度 eyeSize 建议最大强度 0.7 arg.precent = progress * 0.7;
        
//    TuSDKMediaFilterEffectData *filterEffect = (TuSDKMediaFilterEffectData *)self.applyingEffectData;
//    [filterEffect submitParameter:index argPrecent:precent];

    // 修改特效
    TuSDKMediaFilterEffectData *filterEffect = (TuSDKMediaFilterEffectData *)self.applyingEffectData;
    
        NSLog(@"当前调节的滤镜参数名为 : %@",filterEffect.filterArgs[index].key);
    
        if ([filterEffect.filterArgs[index].key isEqualToString:@"smoothing"]) {
    
            // value range 0-1
            [filterEffect submitParameter:index argPrecent:precent*0.7];
            
        }else if ([filterEffect.filterArgs[index].key isEqualToString:@"chinSize"])
        {
            [filterEffect submitParameter:index argPrecent:precent*0.4];
        }else if([filterEffect.filterArgs[index].key isEqualToString:@"eyeSize"])
        {
            [filterEffect submitParameter:index argPrecent:precent*0.7];
        }else if ([filterEffect.filterArgs[index].key isEqualToString:@"mixied"])
        {
            [filterEffect submitParameter:index argPrecent:precent*0.8];
        }
}

- (void)filterView:(FilterView *)filterView didSelectFilterCode:(NSString *)effectCode;
{
    // 滤镜特效暂时只支持同时存在一个
    TuSDKMediaFilterEffectData *filterEffectData = [[TuSDKMediaFilterEffectData alloc] initWithEffectCode:effectCode];
    [_filterProcessor addMediaEffect:filterEffectData];
}

```

### 5.贴纸的使用

1.引入头文件 #import "StickerScrollView.h"
2.遵守代理 StickerViewClickDelegate
3.创建对象

```
// 贴纸栏
@property (nonatomic, strong) StickerScrollView *stickerView;

```

4.初始化贴纸栏

```

    CGFloat stickerViewHeight = 246;
    _stickerView = [[StickerScrollView alloc]initWithFrame:CGRectMake(0, self.view.lsqGetSizeHeight - stickerViewHeight, self.view.lsqGetSizeWidth, stickerViewHeight)];
    // 遵守代理
    _stickerView.stickerDelegate = self;
    _stickerView.backgroundColor = [UIColor colorWithRed:0.22 green:0.22 blue:0.22 alpha:0.7];
    [self.view addSubview:_stickerView];
    _stickerView.hidden = YES;

```

5. 代理方法

```

#pragma mark -- 贴纸栏点击代理方法 StickerViewClickDelegate

- (void)clickStickerViewWith:(TuSDKPFStickerGroup *)stickGroup
{
    if (!stickGroup) {
        //为nil时 移除已有贴纸组；
        [_filterProcessor removeMediaEffectsWithType:TuSDKMediaEffectDataTypeSticker];
        _stickerView.hidden = YES;
        
        return;
    }
    //展示对应贴纸组；
    [_filterProcessor showGroupSticker:stickGroup];
}

```

6.相机贴纸栏数据动态下载

* 创建对象 @property (nonatomic, strong) TuSDKOnlineStickerDownloader *_stickerDownloader;
* 遵守代理 TuSDKOnlineStickerDownloaderDelegate
* 初始化贴纸在线下载工具

```

    _stickerDownloader = [[TuSDKOnlineStickerDownloader alloc]init];
    // 遵守代理
    _stickerDownloader.delegate = self;
    // 指定相机默认贴纸读取模式（全部贴纸，方形贴纸，全屏贴纸，对应下方使用的 json 文件）
    self.cameraStickerType = lsqCameraStickersTypeAll;

```

* 实现代理方法

```
#pragma mark - TuSDKOnlineStickerDownloaderDelegate

- (void)onDownloadProgressChanged:(uint64_t)stickerGroupId progress:(CGFloat)progress changedStatus:(lsqDownloadTaskStatus)status;
{
    if (status == lsqDownloadTaskStatusDowned) {
        [self initStickersData];
    }else if (status == lsqDownloadTaskStatusDownFailed){
        [self initStickersData];
    }
}
```

* 实现在线下载功能需要，`StickerScrollView` 和  `StickerCollectionViewCell`
* demo 提供了两个 json 文件，`fullScreenSticker.json` 和 `squareSticker.json`。
* 分别代表全屏贴纸和方形贴纸
* 贴纸的形状不是 SDK 自动匹配的，需要手动在这两个 json 文件中配置
* json 文件中信息如下

```
  "stickerGroups" : [
    {
      // 贴纸名称
      "name" : "米妮",
      // 贴纸 ID，通过贴纸 ID 进行贴纸的获取和文件下载  
      "id" : "1419",   
      // 贴纸文件的缩略图，网络请求的地址都是一样的，只改变对应的贴纸 ID 即可
      "previewImage" : "https://img.tusdk.com/api/stickerGroup/img?id=1419"
    },
    {
      "name" : "粉红公主",
      "id" : "1475",
      "previewImage" : "https://img.tusdk.com/api/stickerGroup/img?id=1475"
    },

```

* StickerScrollView 文件中 - (void)initStickersData; 方法中有 json 文件的使用方法
* 在线下载功能实现需要借助贴纸 ID

```

    // 获取贴纸组数据源
    NSArray<TuSDKPFStickerGroup *> *allStickes = [[TuSDKPFStickerLocalPackage package] getSmartStickerGroups];
    NSString *jsonFileName = @"";
    
    if (_cameraStickerType == lsqCameraStickersTypeAll) {
        _stickerGroups = allStickes;
        return;
    }else if (_cameraStickerType == lsqCameraStickersTypeSquare){
        jsonFileName = @"squareSticker";
    }else if (_cameraStickerType == lsqCameraStickersTypeFullScreen){
        jsonFileName = @"fullScreenSticker";
    }
    
    // 根据预先配置在 json 文件中的 json 信息进行下载
    // 解析JSON 获取正方形贴纸数组
    NSString *configJsonPath = [[NSBundle mainBundle]pathForResource:jsonFileName ofType:@"json"];
    NSDictionary *squareStickersDic = [[NSString stringWithContentsOfFile:configJsonPath encoding:NSUTF8StringEncoding error:nil] lsqToJson];
    NSMutableArray<NSDictionary *> *list = [NSMutableArray arrayWithArray:[squareStickersDic objectForKey:@"stickerGroups"]];
    
    // 获取贴纸信息
    NSMutableArray *stickerArr = [[NSMutableArray alloc]init];
    [list enumerateObjectsUsingBlock:^(NSDictionary * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        uint64_t stickerID = (uint64_t)[[obj objectForKey:@"id"] intValue];
        BOOL localStickersContains = NO;
        
        for (TuSDKPFStickerGroup *group in allStickes) {
            if (group.idt == stickerID) {
                localStickersContains = YES;
                [stickerArr addObject:group];
                break;
            }
        }
        if (!localStickersContains) {
            [stickerArr addObject:obj];
        }
    }];
```

* 可以不使用 demo 中提供的 `fullScreenSticker.json` 和 `squareSticker.json` 文件来获取贴纸 ID ，也可以动态下发贴纸 ID 和 preview 的 URL 地址，这部分需要用户进行自定义修改。
* `fullScreenSticker.json` 和 `squareSticker.json` 不是必需文件，用户可自行定义或调整。

## 5.FAQ


### 1.滤镜替换使用的滤镜的代号

* 替换资源文件后，查看资源文件（TuSDK.bundle/others/lsq_config.json）中滤镜的 filerCode，替换到项目中对应的位置。
* 替换滤镜资源后，需要根据新的 filterCode 更改对应滤镜效果缩略图文件的名称。
* "name":"lsq_filter_VideoFair"`，`VideoFair ` 就是该滤镜的filterCode ，在`_videoFilters = @[@"VideoFair"];` 可以进行选择使用滤镜的设置。





