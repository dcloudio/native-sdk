uni小程序SDK原生开发者交流QQ群： 892918401

## 开发环境

- JAVA环境 jdk1.7+(最优1.8)
- Android Studio 下载地址：[Android Studio官网](https://developer.android.google.cn/studio/index.html) OR [Android Studio中文社区](http://www.android-studio.org/)
- 下载uni小程序 SDK [详情](UniMPDocs/SDKDownload/android.md)；
- [HBuilderX-2.6.2+](https://www.dcloud.io/hbuilderx.html)

## 集成方法

打开Android Studio加载已有android项目或新建android项目。然后按照以下步骤配置您的项目。

## 添加基础依赖库及资源文件

解压uni小程序SDK包。得到uniMPSDK文件夹。
uniMPSDK文件夹目录结构说明:

```
|-- uniMPSDK/SDK	//uni小程序SDK
	|-- assets		// assets资源文件
	|-- Libs		//依赖库
	|-- res			// 资源文件
	|-- src			//微信分享支付需要的activity
	|-- AndroidManifest.xml //模块配置信息
	|-- proguard.cfg  //混淆配置
/-- uniMPSDK/DEMO	//uni小程序SDK示例DEMO
/-- uniMPSDK\Features // 框架已有的原生功能依赖库
	|-- Features 依赖库说明.xls
	|-- libs //原生功能依赖库
```


#### uniMPSDK/SDK/Libs 依赖库说明

Libs文件夹中的`依赖库`是不用全部都集成到宿主项目中。可根据功能增删修改。具体可阅读`Features 依赖库说明.xls`参考修改添加等操作。

除视频、地图、分享、支付、登录、直播pusher等SDK，只集成基础模块如下:

 ```
 uniMPSDK-release.aar  //必须集成
 uniapp-release.aar //必须集成
 sqlite-release.aar
 oaid_sdk_1.0.25.aar //必须集成 注意(3.0.7版本的SDK及以下版本请集成msa_mdid_1.0.13.aar)
 messaging-release.aar
 iBeacon-release.aar
 fingerprint-release.aar
 contacts-release.aar
 Bluetooth-release.aar
 android-gif-drawable-release@1.2.17.aar //必须集成
 ```
 
```
dependencies {
	implementation 'com.android.support:recyclerview-v7:28.0.0' //必须集成
    implementation 'com.android.support:support-v4:28.0.0' //必须集成
    implementation 'com.android.support:appcompat-v7:28.0.0' //必须集成
    implementation 'com.alibaba:fastjson:1.1.46.android' //必须集成
    implementation 'com.facebook.fresco:fresco:1.13.0'//必须集成
    implementation 'com.facebook.fresco:animated-gif:1.13.0'//必须集成
	implementation 'com.github.bumptech.glide:glide:4.9.0'//必须集成
}
```

**res资源：**

3.1.6版本开始增加values-zh中文资源。默认values目录资源存放英文资源。只修改values下的数据在中文系统手机上运行无法正常显示改后数据。需要添加values-zh资源一并修改。



**标注`必须集成`的依赖库一定要集成。版本号尽量一致。如果依赖库不一致或未集成可能会出现页面显示异常等现象**

**注意**

不要只拷贝`依赖库`(aar资源)。SDK根目录下的其他资源（非Libs）必须要拷贝到项目中的。

**3.1.0版本开始 unimpSDK的minSdkVersion从19调整到21**

#### 复制SDK资源到android项目目录

将SDK目录下的资源拷贝到工程项目相对路径中.具体可参考uniMPSDK示例DEMO

![](https://img-cdn-tc.dcloud.net.cn/uploads/article/20200215/69ac35d5a6db85d8890c610d567acf25.png)

**注意**

SDK资源必须都要拷贝到项目中。缺少则无法正常运行。如：assets中的资源不拷贝。会报错`未添加ui模块`的弹窗

#### gradle配置
以下为基础配置。具体可参考uniMPSDK示例DEMO中app/build.gradle文件
```
//必须配置
def mfph = [
    //宿主包名
    "apk.applicationId" : "xxx.xxx.xxxxx",
]
android {
	defaultConfig {
		targetSdkVersion 26 //最优26 2.8.11开始支持29~30
		ndk {
            abiFilters 'x86','armeabi-v7a',"arm64-v8a" //不支持armeabi
        }
		manifestPlaceholders = mfph
	}
	//此处配置必须添加 否则无法正确运行
    aaptOptions {
        additionalParameters '--auto-add-overlay'
        //noCompress 'foo', 'bar'
        ignoreAssetsPattern "!.svn:!.git:.*:!CVS:!thumbs.db:!picasa.ini:!*.scc:*~"
    }
}
//导入aar需要的配置
repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
	//导入SDK相关依赖jar、aar
	implementation fileTree(include: ['*.jar'], dir: 'libs')
	implementation fileTree(include: ['*.aar'], dir: 'libs')
	//必须添加的依赖
	implementation 'com.android.support:recyclerview-v7:28.0.0'
    implementation 'com.android.support:support-v4:28.0.0'
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.alibaba:fastjson:1.1.46.android'
    implementation 'com.facebook.fresco:fresco:1.13.0'
    implementation 'com.facebook.fresco:animated-gif:1.13.0'
	implementation 'com.github.bumptech.glide:glide:4.9.0'
}
```

**注意！！！！ targetSdkVersion 取值范围最高30 最优26 设置值域超过28可能在android10以上手机出现白屏问题。 HX2.8.11+已支持targetSdkVersion设置30**


### uni小程序集成的第三方依赖库与宿主发生冲突

 - 遇到此问题。请先检查宿主使用的相关三方依赖库的版本与[uni小程序SDK集成的三方依赖库的版本](https://ask.dcloud.net.cn/article/35419)是否一致。
 - 如果版本号一致！删除uni小程序SDK三方依赖库的文件或gradle配置即可。
 - 如果版本号不一致！则需要宿主主动修改相关集成三方依赖库的java代码。来适配uni小程序中的三方依赖库的版本逻辑。

## 生成小程序应用资源

**注意！！！！ uni小程序仅支持uni应用！！！**

首先在 HBuilderX 中选择您的 uni-app 项目，如果没有请新建一个 uni-app 项目，如下图，创建 uni-app 项目

![](https://img-cdn-tc.dcloud.net.cn/uploads/article/20200215/1943ed21ceedcd85c9ba0bc74d4cd0f5.png)

然后选中您的项目，右键->发行->原生App-制作应用wgt包

注：HBuilderX 2.6.2 以下版本选项是 “原生App-制作移动App资源升级包” ，2.6.2 版本近期会发布，只是修改了描述功能是一样的.

![](https://img-cdn-tc.dcloud.net.cn/uploads/article/20200225/9d254389e00fe11f36606b3fc6577ddb.png)

项目编译完成后会在控制台，输出wgt包的路径，点击路径可以直接打开wgt所在目录

![](https://img-cdn-tc.dcloud.net.cn/uploads/article/20200225/dc6b2614816763dcdd9a25ad4c56a25a.png)

![](https://img-cdn-tc.dcloud.net.cn/uploads/article/20200225/9e9da9a8e0a0a5da8c331917a0225ba5.png)

如图，`__UNI__04E3A11.wgt`就是应用资源包，（`__UNI__04E3A11` 为小程序的 appid）

## 导入小程序应用资源

> 内置uni小程序集成方式

打开android原生项目。在主Module模块的assets路径下创建apps/(内置uni小程序的appid)/www 路径。例如：`apps/__UNI__04E3A11/www `。将之前导出的应用资源包解压释放到`apps/__UNI__04E3A11/www `路径下。可参考DEMO示例工程。如下图:
**(解压方法将资源包的扩展 .wgt 重命名为 .zip 然后使用解压软件打开)**

`内置uni小程序资源 更新资源时需要修改uniapp版本号（versionName、versionCode都需要修改）才会更新，如果版本号相同则不会更新需要注意！！！`

注意：不少开发者对示例图片有些误解 是创建`apps/__UNI__04E3A11/www `目录，不是创建`apps.__UNI__04E3A11.www`文件夹。请注意是`apps/__UNI__04E3A11/www `目录。并将wgt资源包解压释放到www文件夹中！！！

![](https://img-cdn-tc.dcloud.net.cn/uploads/article/20200305/f96b6f4e801b8c7cc3e89ed7f0242e42.png)

> 非内置uni小程序集成方式

应用资源包(.wgt)可以选择从云端获取或共享文件等方式。存放到手机SD卡中。宿主通过uni小程序SDK的API调用`releaseWgtToRunPathFromePath`实现释放资源包集成uni小程序。 （暂时不支持assets路径下的资源）

## 代码实现

阅读[uni小程序SDK API参考手册](https://ask.dcloud.net.cn/article/36984)

#### 初始化uniMPSDK

调用uni小程序SDK的DCUniMPSDK.getInstance().initialize 进行初始化。

**示例：**

```
MenuActionSheetItem item = new MenuActionSheetItem("关于", "gy");
List<MenuActionSheetItem> sheetItems = new ArrayList<>();
sheetItems.add(item);
DCSDKInitConfig config = new DCSDKInitConfig.Builder()
                .setCapsule(true)
                .setMenuDefFontSize("16px")
                .setMenuDefFontColor("#ff00ff")
                .setMenuDefFontWeight("normal")
                .setMenuActionSheetItems(sheetItems)
                .build();
DCUniMPSDK.getInstance().initialize(this, config);
```

**Tips**

为了防止其他三方SDK可能影响小程序的运行 请禁止在小程序进程初始化其他三方SDK

```
// 非小程序进程
if(!RuningAcitvityUtil.getAppName(getBaseContext()).contains("io.dcloud.unimp")) {
	//请在此处初始化其他三方SDK
	... ...
}
```

#### 设置DefaultMenuButton监听事件

设置setDefMenuButtonClickCallBack监听默认菜单按钮点击事件。如示例中"关于"的设置，当关于被点击后会触发IMenuButtonClickCallBack

**示例：**

```
DCUniMPSDK.getInstance().setDefMenuButtonClickCallBack(new DCUniMPSDK.IMenuButtonClickCallBack() {
            @Override
            public void onClick(String id) {
                switch (id) {
                    case "gy":{
                        Logger.e("用户点击了关于");
                    }
                }
            }
});
```

**Tips**

uni小程序运行在独立进程中。所以小程序中的内存与宿主是不会共享的。在点击事件处理上请注意！！！


#### 启动内置uni小程序

通过DCUniMPSDK.getInstance().startApp启动小程序

**示例：**

```
try {
	DCUniMPSDK.getInstance().startApp(context,"__UNI__04E3A11", MySplashView.class);
} catch (Exception e) {
	e.printStackTrace();
}
```

#### 启动非内置uni小程序

 - 通过调用DCUniMPSDK.getInstance().releaseWgtToRunPathFromePath释放wgt资源包到运行时目录下。
 - 调用DCUniMPSDK.getInstance().startApp启动小程序

**示例：**

```
String wgtPath = context.getExternalCacheDir().getPath()+"/__UNI__04E3A11.wgt";
DCUniMPSDK.getInstance().releaseWgtToRunPathFromePath("__UNI__04E3A11", wgtPath, new ICallBack() {
	@Override
	public Object onCallBack(int code, Object pArgs) {
		if(code ==1) {//释放wgt完成
			try {
				DCUniMPSDK.getInstance().startApp(context, "__UNI__04E3A11");
			} catch (Exception e) {
				e.printStackTrace();
			}
		} else{//释放wgt失败
			Toast.makeText(context, "资源释放失败", Toast.LENGTH_SHORT).show();
		}
		return null;
	}
});
```

#### 启动uni小程序携带启动参数

调用DCUniMPSDK.getInstance().startApp启动uni小程序时。设置相应arguments(启动参数)参数即可。具体说明请阅读[uni小程序SDK API参考手册](https://ask.dcloud.net.cn/article/36984)中startApp方法相关说明。

**示例**

```
try {
	JSONObject arguments = new JSONObject();
	arguments.put("a","1");
	DCUniMPSDK.getInstance().startApp(context, "__UNI__04E3A11", arguments);
} catch (Exception e) {
	e.printStackTrace();
}
```

#### 启动uni小程序直达二级页面

调用DCUniMPSDK.getInstance().startApp启动uni小程序时。设置相应path(直达页面地址)参数即可。具体说明请阅读[uni小程序SDK API参考手册](https://ask.dcloud.net.cn/article/36984)中startApp方法相关说明。

**示例**

```
try {
	DCUniMPSDK.getInstance().startApp(context,"__UNI__04E3A11", "pages/component/scroll-view/scroll-view");
} catch (Exception e) {
	e.printStackTrace();
}
```

#### Tips

 - 以上示例代码可参考uniMPSDK中DEMO示例。
 - 由于uni小程序是多进程架构，在跨进程启动 Activity 的时候，activty 不可以是 singleTask 方式，否则会有页面栈问题（从小程序进入一个 activity，返回之后发现回到主进程了）。


### 混淆配置

具体请查看SDK目录下的`proguard.cfg`文件。涵盖了目前提供的模块相关的混淆配置。

### 小程序应用管理

#### uni小程序的应用资源集成方式

 - 放在项目assets目录下 属于内置应用。无需开发者拷贝应用资源，直接通过appid 调用SDK的startApp启动即可。
 - 开发者自行下载或其他方式获取到.wgt文件(uni小程序应用资源包)后。可通过SDK的getAppBasePath获取运行时路径。自行解压释放.wgt文件到运行时路径。.wgt文件生成请参考`第二步，生成小程序应用资源`。也可通过SDK的releaseWgtToRunPathFromePath方法释放wgt资源包。释放完毕后通过startApp启动uni小程序。

#### uni小程序应用资源升级

 - 可通过SDK的getAppBasePath获取运行时路径。自行拷贝应用资源到运行时路径。也可通过SDK的releaseWgtToRunPathFromePath方法释放升级后的wgt资源包。直接替换原有应用资源。

#### uni小程序应用删除

 - 可通过SDK的getAppBasePath获取运行时路径。遍历查找出要删除的appid应用资源路径。执行file删除操作即可。
 - assets目录下属于内置应用。不支持删除操作。
