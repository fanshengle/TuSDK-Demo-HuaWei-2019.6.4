## 附件为七牛与声网同TuSDK对接的Demo 

###文件介绍:

- iOS
- qiniu-QNRTC-iOS-master 是七牛QNRTC与TuSDK对接的Demo
- agora-tusdk-OpenLive-iOS-OC 是声网直播与TuSDK对接的Demo

- Android
- qiniu-TuSDK-QNRTC-Android-master 是七牛QNRTC与TuSDK对接的Demo
- Agora-TuSDK-Video-Source 是声网直播与TuSDK对接的Demo

###注意事项:

- iOS
- 各Demo中对接接口所在位置
- 七牛QNRTC与TuSDK对接 qiniu-QNRTC-iOS-master: QRDRTCViewController.h -> - (void)RTCEngine:(QNRTCEngine *)engine cameraSourceDidGetSampleBuffer:(CMSampleBufferRef)sampleBuffer;
- 声网直播与TuSDK对接 agora-tusdk-OpenLive-iOS-OC : TuSDKVideoPreProcessing -> agora::media::IVideoFrameObserver




- Android
- 各Demo中对接接口所在位置
- 七牛QNRTC与TuSDK对接 qiniu-TuSDK-QNRTC-Android-master : RoomActivity -> QNCaptureVideoCallback()
- 声网直播与TuSDK对接 Agora-TuSDK-Video-Source : VideoChatActivity -> onDrawFrame()