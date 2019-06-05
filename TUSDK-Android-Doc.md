# TuSDK 功能操作说明

## 文件构成

### 包名

* 在 Eclipse 中，Android 应用的包名是 Package Name，它定义在 AndroidManifest.xml 文件 <manifest> 中的 package。例如 org.lasque.tusdkdemo
* 在 Android Studio 中，如果有在 build.gradle 文件中定义 applicationId 属性，那么包名即是指此处 applicationId 的值；如果没有在 build.gradle 文件中定义 applicationId，那么包名还是指 AndroidManifest.xml 文件中的 package属性的值


### 应用密钥（AppKey）

* 应用密钥是您的应用在 TuSDK 的唯一标识符。您可以登录后在「我的应用」页面查看到每个应用的密钥。
如果您修改了应用的包名，密钥将随之改变，您需要在代码内更新它。

### 权限密钥（master key）
* 权限密钥位于资源配置包中的 config.json 文件中，您可以通过下载资源包获取。
如果您修改了应用的包名，密钥将随之改变，您需要在代码内更新它。

## 安装与更新

### Android Studio ###
#### 首次安装 ####

您可以依据以下步骤将 TuSDKVideo 集成到您的目标项目中：

1、将 TuSDKVideoDemo libs 下的所有 jar 包（其中，bugly_crash_release_2.6.5.jar 是用来收集 bug 日志，可以不添加）复制到目标项目 libs 目录下,右键点击 jar 文件，并点击弹出菜单中的 “Add As Library”，将jar文件作为类库添加到项目中。

2、找到目标项目下 src/main/jniLibs 目录，如果没有 jniLibs 目录则新建一个，将 TuSDKVideoDemo libs 中的 so 文件（arm64-v8a 等）复制到你项目里的 jniLibs 目录下。 

3、找到目标项目下 src/main/assets 目录，如果没有 assets 目录则新建一个，然后在 assets 目录下新建一个文件夹，名称为 TuSDK.bundle。

4、将打包下载的资源复制到 TuSDK.bundle 目录下（例如资源中的others、stickers、textures复制进TuSDK.bundle 目录下）

5、如果打包后的滤镜在运行后的程序中查看时没有显示正常滤镜名称，则需要您在 TuSDK 项目中的 strings.xml 文件中手动添加其对应名称。
首先在 assets/TuSDK.bundle 文件夹下的 lsq_tusdk_configs.json 文件中找到没正常显示的滤镜，比如  lsq_filter_SkinNature，然后在 strings.xml 文件中写上

   `<string name="lsq_filter_SkinNature">自然</string>`

如果需要配置多种语言，就在每个语言的 strings.xml 文件中分别写上对应名称即可。

#### 更新 ####
* 删除旧文件，然后重复首次安装的步骤。

### ADT (Eclipse) ###
#### 首次安装 ####
1、在 Package Explorer 中的找到你的项目, 将 TuSDKVideoDemo 中 libs 下的 jar 包（其中，bugly_crash_release_2.6.5.jar 是用来收集 bug 日志，可以不添加）和 so 文件（arm64-v8a 等）复制到你的项目 libs 下。

2、找到你项目下的 assets 目录，如果没有 assets 目录则新建一个，然后在 assets 目录下新建一个文件夹，名称为 TuSDK.bundle。

3、将打包下载的资源复制到 TuSDK.bundle 目录下（例如资源中的others、stickers、textures复制进TuSDK.bundle 目录下）

4、如果打包后的滤镜在运行后的程序中查看时没有显示正常滤镜名称，则需要您在 TuSDK 项目中的 strings.xml 文件中手动添加其对应名称。
首先在 assets/TuSDK.bundle 文件夹下的 lsq_tusdk_configs.json 文件中找到没正常显示的滤镜，比如  lsq_filter_SkinNature，然后在 strings.xml 文件中写上

   `<string name="lsq_filter_SkinNature">自然</string>`

如果需要配置多种语言，就在每个语言的 strings.xml 文件中分别写上对应名称即可。

#### 更新 ####
* 删除旧文件，然后重复首次安装的步骤。

## TuSDK 的项目配置

### 初始化密钥 ###
打开全局 Application 文件，全局 Application 类可以选择继承 TuSdkApplication 或不继承，然后在 onCreate 方法中使用 TuSdk.init() 方法来进行初始化，并将复制的密钥作为该方法的参数，如下：
  

    @Override
    public void onCreate()
    {
	// 设置资源类，当 Application id 与 Package Name 不相同时，必须手动调用该方法, 且在 init 之前执行。
	// TuSdk.setResourcePackageClazz(R.class);
    TuSdk.enableDebugLog(true);
    // 初始化SDK (请将目标项目所对应的密钥放在这里)
    TuSdk.init(this.getApplicationContext(), "12aa4847a3a9ce68-04-ewdjn1");
    }

### 调试日志开关 ###
为方便开发时定位错误，可打开 TuSDK 的调试日志，即在初始化密钥之前添加以下代码（放在初始化密钥之后无效）：

    TuSdk.enableDebugLog(true);


### 配置 AndroidManifest ###
在 AndroidManifest.xml 中，首先定义程序需要的权限：
    
    <!-- 访问网络 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <!-- 获取WIFI信息 -->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <!-- 允许访问GPS -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <!-- 允许应用程序写数据到外部存储设备（主要是SD卡） -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <!-- 在sdcard中创建/删除文件的权限 -->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
    <!-- 请求访问使用照相设备 -->
    <uses-permission android:name="android.permission.CAMERA" />
    <!-- 开启闪光灯权限 -->
    <uses-permission android:name="android.permission.FLASHLIGHT" />
    
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.FLASHLIGHT" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />

然后定义应用的全局 Application 对象，设置 allowBackup、hardwareAccelerated 和 largeHeap 三个重要选项为 true。

    <application
    	android:name="org.lasque.tusdk.TuApplication"
    	android:allowBackup="true"
    	android:hardwareAccelerated="true"
    	android:largeHeap="true"
    </application>

## TuSDKFilterEngine 的使用

TuSDKFilterEngine 是视频滤镜处理 API 的接口

### 初始化

1. 初始化

        mFilterEngine = new TuSDKFilterEngine(getBaseContext(), false);
        // TuSDKFilterEngine 事件回调
        mFilterEngine.setDelegate(mFilterDelegate);
        // 设置是否输出原始图片朝向 false: 图像被转正后输出
        mFilterEngine.setOutputOriginalImageOrientation(true);
		//设置相机镜头朝向
        mFilterEngine.setCameraFacing( mFrontCamera ? CameraConfigs.CameraFacing.Front : CameraConfigs.CameraFacing.Back);
        // 设置输入的图片朝向 如果输入的图片不是原始朝向 该选项必须配置
        //mFilterEngine.setInputImageOrientation(ImageOrientation.DownMirrored);
        // 设置是否开启动态贴纸功能
        mFilterEngine.setEnableLiveSticker(true);


* 代理方法


        private TuSDKFilterEngine.TuSDKFilterEngineDelegate mFilterDelegate = new TuSDKFilterEngine.TuSDKFilterEngineDelegate(){
        
			/**
	         * 滤镜更改事件，每次调用 switchFilter 切换滤镜后即触发该事件
	         * @param filterWrap 新的滤镜对象
	         */
	        @Override
	        public void onFilterChanged(FilterWrap filterWrap) {
	            // 获取滤镜参数列表. 如果开发者希望自定义滤镜栏,可通过 ilter.getParameter().getArgs() 对象获取支持的参数列表。
	            //List<SelesParameters.FilterArg> args = filterWrap.getFilter().getParameter().getArgs();

				//for (SelesParameters.FilterArg arg : args)
				//{
			    //	/*
			    //	 * 参数为 Key - Value 模式，不同的滤镜，参数数目不一样。
			    //	 *
			    //	 * 常见的参数如下：
			    //	 *
			    //	 * smoothing 润滑
			    //	 * mixed     效果
		    	//	 * eyeSize   大眼 建议最大强度 0.7
		    	//	 * chinSize  瘦脸 建议最大强度 0.4
			    //	 *
			    //	 */
				//	if (arg.equalsKey("smoothing"))
				//	{
				//		// 设置新的参数值，取值范围： 0 ~ 1.0
				//		 arg.setPrecentValue(0.9f);
				//	}
				//	else if(arg.equalsKey("mixed"))
				//	{
				//		// 设置新的参数值，取值范围： 0 ~ 1.0
				//		 arg.setPrecentValue(0.9f);
				//	}
				//	else if(arg.equalsKey("eyeSize"))
				//	{
				//		// 设置新的参数值，取值范围： 0 ~ 1.0
				//		 arg.setPrecentValue(0.9f);
				//	}
				//	else if(arg.equalsKey("chinSize"))
				//	{
				//		// 设置新的参数值，取值范围： 0 ~ 1.0
				//		 arg.setPrecentValue(0.3f);
				//	}
				//}
				//
				//		// 提交参数使之生效
				//       filterWrap.submitFilterParameter();
	
	            // 通知滤镜栏视图更新
	            if (getFilterConfigView() != null)
	            {
	                getFilterConfigView().setSelesFilter(filterWrap.getFilter());
	            }
	        }
	

	    	/**
			 * 相机拍摄数据处理完毕，返回处理后的结果，运行在主线程
			 * @param imageBuffer 处理后的 RGBA 数据
			 * @param imageSize 图片尺寸
			 */
	        @Override
	        public void onPictureDataCompleted(IntBuffer intBuffer, TuSdkSize tuSdkSize) {
	
	        }
	
	    	/**
	         * 预览抓拍完成，返回抓拍结果，运行在主线程
	         * @param bitmap 图像数据
	         */
	        @Override
	        public void onPreviewScreenShot(Bitmap bitmap) {
	
	        }
    	};

### 方法调用

        // 切换滤镜效果 code 为滤镜代号可在 lsq_tusdk_configs.json 查看
        mFilterEngine.switchFilter(code);

        // 清除所有的贴纸
        mFilterEngine.removeAllLiveSticker();

		//显示一组动态贴纸。当显示一组贴纸时，会清除画布上的其他贴纸
        mFilterEngine.showGroupSticker(itemData);

		//在 GLSurfaceView 的 回调 onSurfaceCreated 中调用该方法
	    mFilterEngine.onSurfaceCreated();

		//在 GLSurfaceView 的 回调 onSurfaceCnaged 中调用该方法
	    mFilterEngine.onSurfaceChanged(width,height);

		//在 GLSurfaceView 的 回调 onSurfaceDestroy 中调用该方法，销毁资源
	    mFilterEngine.onSurfaceDestroy();

		/**
		 * 滤镜处理入口，每次绘制时都需要调用
		 *
		 * @param textureId
		 *            纹理 ID (OES纹理/普通2D纹理)
		 * @param textureWidth
		 *            纹理宽度
		 * @param textureHeight
		 *            纹理高度
		 * @return 经过处理的纹理 ID
		 */
    	 mFilterEngine.processFrame(texId, texWidth, texHeight);
    
### 滤镜的使用

1.初始化滤镜栏

替换资源文件后，查看资源文件（TuSDK.bundle/others/lsq_config.json）中滤镜的 filerCode，替换到项目中对应的位置。

替换滤镜资源后，需要根据新的 filterCode 更改对应滤镜效果缩略图文件的名称。

	    // 滤镜 code 列表, 每个 code 代表一种滤镜效果, 具体 code 可在 lsq_tusdk_configs.json 查看 (例如:lsq_filter_SkinNature02 滤镜的 code 为 SkinNature02)
    	private static final String[] VIDEOFILTERS = new String[]{"Olympus_1_1", "Leica_1_1", "Gold_1_1", "Cheerful_1_1", "White_1_1",
            "s1950_1_1", "Blurred_1_1","Newborn_1_1","Fade_1_1","NewYork_1_1","nature","pink","jelly"};

    /**
     * 初始化滤镜栏视图
     */
    protected void initFilterListView()
    {
        getFilterListView();

        this.mFilterListView.setModeList(Arrays.asList(VIDEOFILTERS));
        ThreadHelper.postDelayed(new Runnable(){

            @Override
            public void run()
            {
                if (!mIsFirstEntry) return;

                mIsFirstEntry = false;
                changeVideoFilterCode(Arrays.asList(VIDEOFILTERS).get(mFocusPostion));
            }

        }, 1000);
    }

    /**
     * 滤镜栏视图
     *
     * @return
     */
    public FilterListView getFilterListView()
    {
        if (mFilterListView == null)
        {
            mFilterListView = (FilterListView) findViewById(R.id.lsq_filter_list_view);
            mFilterListView.loadView();
            mFilterListView.setCellLayoutId(R.layout.filter_list_cell_view);
            mFilterListView.setCellWidth(TuSdkContext.dip2px(62));
            mFilterListView.setItemClickDelegate(mFilterTableItemClickDelegate);
            mFilterListView.reloadData();
            mFilterListView.selectPosition(mFocusPostion);
        }
        return mFilterListView;
    }


2.代理方法

    

滤镜组列表点击事件

    private TuSdkTableView.TuSdkTableViewItemClickDelegate<String, FilterCellView> mFilterTableItemClickDelegate = new TuSdkTableView.TuSdkTableViewItemClickDelegate<String, FilterCellView>()
    {
        @Override
        public void onTableViewItemClick(String itemData,
                                         FilterCellView itemView, int position)
        {
            onFilterGroupSelected(itemData, itemView, position);
        }
    };

滤镜组选择事件

    /**
     * @param itemData 滤镜名（code）
     * @param itemView 
     * @param position
     */
    protected void onFilterGroupSelected(String itemData,
                                         FilterCellView itemView, int position)
    {
        FilterCellView prevCellView = (FilterCellView) mFilterListView.findViewWithTag(mFocusPostion);
        mFocusPostion = position;
        changeVideoFilterCode(itemData);
        mFilterListView.selectPosition(mFocusPostion);
        deSelectLastFilter(prevCellView);
        selectFilter(itemView, position);
        getFilterConfigView().setVisibility((position == 0)?View.GONE:View.VISIBLE);
    }

取消上一个滤镜的选中状态

    private void deSelectLastFilter(FilterCellView lastFilter)
    {
        if (lastFilter == null) return;

        updateFilterBorderView(lastFilter,true);
        lastFilter.getTitleView().setBackground(TuSdkContext.getDrawable(R.drawable.tusdk_view_filter_unselected_text_roundcorner));
        lastFilter.getImageView().invalidate();
    }

设置滤镜单元边框是否可见

    /**
     * @param lastFilter
     * @param isHidden
     */
    private void updateFilterBorderView(FilterCellView lastFilter,boolean isHidden)
    {
        View filterBorderView = lastFilter.getBorderView();
        filterBorderView.setVisibility(isHidden ? View.GONE : View.VISIBLE);
    }

滤镜选中状态

    private void selectFilter(FilterCellView itemView, int position)
    {
        updateFilterBorderView(itemView, false);
        itemView.setFlag(position);
        TextView titleView = itemView.getTitleView();
        titleView.setBackground(TuSdkContext.getDrawable(R.drawable.tusdk_view_filter_selected_text_roundcorner));
    }

显示智能美颜底部栏

    public void showSmartBeautyLayout()
    {
        updateFilterViewStaff(true);

        // 滤镜栏向上动画并显示
        ViewCompat.setTranslationY(mFilterBottomView,
                mFilterBottomView.getHeight());
        ViewCompat.animate(mFilterBottomView).translationY(0).setDuration(200).setListener(mViewPropertyAnimatorListener);
        showBeautySeekBar();
    }

隐藏滤镜栏

    public void hideFilterStaff()
    {
        if(mFilterBottomView.getVisibility() == View.GONE) return;
        updateFilterViewStaff(false);
        // 滤镜栏向下动画并隐藏
        ViewCompat.animate(mFilterBottomView)
                .translationY(mFilterBottomView.getHeight()).setDuration(200);
    }

更新滤镜栏相关视图的显示状态

    private void updateFilterViewStaff(boolean isShow)
    {
        mIsFilterShow = isShow;
        mFilterBottomView.setVisibility(isShow? View.VISIBLE: View.GONE);
    }

显示美颜调节栏

    private void showBeautySeekBar()
    {
        if (mIsFirstEntry)
        {
            changeVideoFilterCode(Arrays.asList(VIDEOFILTERS).get(mFocusPostion));
        }

        if (mBeautyLayout == null || mFilterLayout == null)
            return;

        mBeautyLayout.setVisibility(View.VISIBLE);
        mFilterLayout.setVisibility(View.GONE);
        updateSmartBeautyTab(mBeautyTab,true);
        updateSmartBeautyTab(mFilterTab,false);

        if (mSelesOutInput == null)
        {
            setEnableAllSeekBar(false);
            return;
        }

        // 滤镜参数
        SelesParameters params = mSelesOutInput.getFilterParameter();
        if (params == null)
        {
            setEnableAllSeekBar(false);
            return;
        }

        List<SelesParameters.FilterArg> list = params.getArgs();
        if (list == null || list.size() == 0)
        {
            setEnableAllSeekBar(false);
            return;
        }

        for(SelesParameters.FilterArg arg : list)
        {
            if (arg.equalsKey("smoothing"))
            {
                setEnableSeekBar(mSmoothingBarLayout,true,arg.getPrecentValue(),
                        R.drawable.tusdk_view_widget_seekbar_drag);
            }
            else if (arg.equalsKey("eyeSize"))
            {
                setEnableSeekBar(mEyeSizeBarLayout,true,arg.getPrecentValue(),
                        R.drawable.tusdk_view_widget_seekbar_drag);
            }
            else if (arg.equalsKey("chinSize"))
            {
                setEnableSeekBar(mChinSizeBarLayout,true,arg.getPrecentValue(),
                        R.drawable.tusdk_view_widget_seekbar_drag);
            }
        }
    }

显示滤镜列表

    private void showFilterLayout()
    {
        if (mBeautyLayout == null || mFilterLayout == null)
            return;

        mFilterLayout.setVisibility(View.VISIBLE);
        mBeautyLayout.setVisibility(View.GONE);
        updateSmartBeautyTab(mBeautyTab,false);
        updateSmartBeautyTab(mFilterTab,true);

        if (mFocusPostion>0 && getFilterConfigView() != null && mSelesOutInput != null)
        {
            getFilterConfigView().post(new Runnable()
            {

                @Override
                public void run() {
                    getFilterConfigView().setSelesFilter(mSelesOutInput.getFilter());
                    getFilterConfigView().setVisibility(View.VISIBLE);
                }});

            getFilterConfigView().setSeekBarDelegate(mConfigSeekBarDelegate);
            getFilterConfigView().invalidate();
        }
    }

滤镜拖动条监听事件

    private FilterConfigView.FilterConfigViewSeekBarDelegate mConfigSeekBarDelegate = new FilterConfigView.FilterConfigViewSeekBarDelegate()
    {

        @Override
        public void onSeekbarDataChanged(FilterConfigSeekbar seekbar, SelesParameters.FilterArg arg)
        {
            if (arg == null) return;

            if (arg.equalsKey("smoothing"))
                mSmoothingProgress = arg.getPrecentValue();
            else if (arg.equalsKey("eyeSize"))
                mEyeSizeProgress = arg.getPrecentValue();
            else if (arg.equalsKey("chinSize"))
                mChinSizeProgress = arg.getPrecentValue();
            else if (arg.equalsKey("mixied"))
                mMixiedProgress = arg.getPrecentValue();
        }

    };

更新美颜滤镜Tab

    private void updateSmartBeautyTab(TuSdkTextButton button, boolean clickable)
    {
        int imgId = 0, colorId = 0;

        switch (button.getId())
        {
            case R.id.lsq_filter_btn:
                imgId = clickable? R.drawable.lsq_style_default_btn_filter_selected
                        : R.drawable.lsq_style_default_btn_filter_unselected;
                colorId = clickable? R.color.lsq_filter_title_color : R.color.lsq_filter_title_default_color;
                break;
            case R.id.lsq_beauty_btn:
                imgId = clickable? R.drawable.lsq_style_default_btn_beauty_selected
                        : R.drawable.lsq_style_default_btn_beauty_unselected;
                colorId = clickable? R.color.lsq_filter_title_color : R.color.lsq_filter_title_default_color;
                break;
        }

        button.setCompoundDrawables(null, TuSdkContext.getDrawable(imgId), null, null);
        button.setTextColor(TuSdkContext.getColor(colorId));
    }

设置调节栏是否有效

    private void setEnableSeekBar(ConfigViewSeekBar viewSeekBar,boolean enable,float progress,int id)
    {
        if (viewSeekBar == null) return;

        viewSeekBar.setProgress(progress);
        viewSeekBar.getSeekbar().setEnabled(enable);
        viewSeekBar.getSeekbar().getDragView().setBackgroundResource(id);
    }

拖动条监听事件

    private TuSeekBar.TuSeekBarDelegate mTuSeekBarDelegate = new TuSeekBar.TuSeekBarDelegate()
    {
        @Override
        public void onTuSeekBarChanged(TuSeekBar seekBar, float progress)
        {
            if (seekBar == mSmoothingBarLayout.getSeekbar())
            {
                mSmoothingProgress = progress;
                applyFilter(mSmoothingBarLayout,"smoothing",progress);
            }
            else if (seekBar == mEyeSizeBarLayout.getSeekbar())
            {
                mEyeSizeProgress = progress;
                applyFilter(mEyeSizeBarLayout,"eyeSize",progress);
            }
            else if (seekBar == mChinSizeBarLayout.getSeekbar())
            {
                mChinSizeProgress = progress;
                applyFilter(mChinSizeBarLayout,"chinSize",progress);
            }
        }
    };

应用滤镜

    private void applyFilter(ConfigViewSeekBar viewSeekBar,String key,float progress)
    {
        if (viewSeekBar == null || mSelesOutInput == null) return;

        viewSeekBar.getConfigValueView().setText((int)(progress*100) + "%");
        SelesParameters params = mSelesOutInput.getFilterParameter();
        params.setFilterArg(key, progress);
        mSelesOutInput.submitFilterParameter();
    }

### 贴纸的使用

1、初始化贴纸view

    /**
     * 初始化贴纸组视图
     */
    protected void initStickerListView()
    {

        mStickerBottomView = (RecyclerView) findViewById(R.id.lsq_sticker_list_view);
        mStickerBottomView.setVisibility(View.GONE);

        stickerListAdapter = new StickerListAdapter();
        GridLayoutManager manager = new GridLayoutManager(this,5);
        mStickerBottomView.setLayoutManager(manager);
        mStickerBottomView.setAdapter(stickerListAdapter);
        stickerListAdapter.setStickerList(getRawStickGroupList());

        stickerListAdapter.setOnItemClickListener(new StickerListAdapter.OnItemClickListener() {

            @Override
            public void onClickItem(StickerGroup itemData, StickerListAdapter.StickerHolder
                    stickerHolder, int position) {
                onStickerGroupSelected(itemData,stickerHolder,position);
            }
        });
    }


获取本地贴纸列表

    public List<StickerGroup> getRawStickGroupList()
    {
        List<StickerGroup> list = new ArrayList<StickerGroup>();
        try {
            InputStream stream = getResources().openRawResource(R.raw.square_sticker);
            if (!isSquareSticker)
                stream = getResources().openRawResource(R.raw.full_screen_sticker);

            if (stream == null) return null;

            byte buffer[] = new byte[stream.available()];
            stream.read(buffer);
            String json = new String(buffer, "UTF-8");

            JSONObject jsonObject = JsonHelper.json(json);
            JSONArray jsonArray = jsonObject.getJSONArray("stickerGroups");

            for(int i = 0; i < jsonArray.length();i++)
            {
                JSONObject item = jsonArray.getJSONObject(i);
                StickerGroup group = new StickerGroup();
                group.groupId = item.optLong("id");
                group.previewName = item.optString("previewImage");
                group.name = item.optString("name");
                list.add(group);
            }
            return list;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

加载在线滤镜需要在资源文件里配置在线资源列表（full_screen_sticker 和 square_sticker）放在raw文件夹下

资源id在控制台上线资源里获取

格式

    {
      "stickerGroups" : [
    {
      "name" : "荧光猫耳",
      "id" : "1455",
      "previewImage" : "/stickerGroup/img?id=1455"
    },
    {
      "name" : "金刚",
      "id" : "1456",
      "previewImage" : "/stickerGroup/img?id=1456"
    }
      ]
    }

贴纸组列表点击事件

    private TuSdkTableView.TuSdkTableViewItemClickDelegate<StickerGroup, StickerCellView> mStickerTableItemClickDelegateNew = new TuSdkTableView.TuSdkTableViewItemClickDelegate<StickerGroup, StickerCellView>()
    {
        @Override
        public void onTableViewItemClick(StickerGroup itemData,
                                         StickerCellView itemView, int position)
        {
            onStickerGroupSelected(itemData, itemView, position);
        }
    };

贴纸组选择事件

    protected void onStickerGroupSelected(StickerGroup itemData,
                                          StickerCellView itemView, int position)
    {
        // 设置点击贴纸时呈现或是隐藏贴纸
        if (position == 0)
        {
            mFilterEngine.removeAllLiveSticker();
            mStickerListView.setSelectedPosition(position);
            return;
        }

        // 如果贴纸已被下载到本地
        if (mStickerListView.isDownloaded(itemData))
        {
            mStickerListView.setSelectedPosition(position);
            // 必须重新获取StickerGroup,否则itemData.stickers为null
            itemData = StickerLocalPackage.shared().getStickerGroup(itemData.groupId);
            mFilterEngine.showGroupSticker(itemData);
        }else
        {
            mStickerListView.downloadStickerGroup(itemData);
        }
    }


1、集成过程所需资源及控件可在Demo中找到，复制进工程下相应位置
