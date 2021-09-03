## 开发环境

1. Android Studio 下载地址：[Android Studio官网](https://developer.android.google.cn/studio/index.html) OR [Android Studio中文社区](http://www.android-studio.org/)
2. [HBuilderX](https://www.dcloud.io/hbuilderx.html)
3. App离线SDK下载：[最新android平台SDK下载](/AppDocs/download/android.md)

**3.1.10版本起需要申请Appkey，具体请点击[链接](/AppDocs/usesdk/appkey.md)**

## SDK目录说明

~~~
	|-- HBuilder-Hello				App离线打包演示应用
	|-- HBuilder-Integrate-AS		集成uni-app的最简示例
	|-- SDK							SDK库文件目录
	|-- Feature-Android.xls			Android平台各扩展Feature API对应的详细配置
	|-- Readme.txt					版本说明文件及注意事项
	|-- UniPlugin-Hello-AS			uni原生插件开发示例
~~~

## 准备工程

### 方式一：新建工程
打开Android studio新建一个空白项目

![](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-1.png)
	
选择 Empty Activity 点击 Next。

![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-2.png)

Minimum API Level 建议选21及以上

Package name作为应用标志，涉及申请第三方平台账号，一般情况下不建议修改，包名一般设置反向域名，如：io.dcloud.HBuilder
	
点击 Finish 完成创建。

**注意：App离线SDK不支持Kotlin**

### 方式二：导入工程

2.7.0之后提供HBuilder-Integrate-AS工程，可以直接导入HBuilder-Integrate-AS工程，直接运行simpleDemo项目即可。
	
## 配置工程

1. 基础库配置

	将lib.5plus.base-release.aar、android-gif-drawable-release@1.2.23.aar、uniapp-v8-release.aar和oaid_sdk_1.0.25.aar拷贝到libs目录下

	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-10.png)
	
	在build.gradle中添加资源引用
	
	**注意：HBuilderX3.2.5版本之后适配了AndroidX。**
	
	AndroidX版本需添加如下资源
	
	~~~
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    implementation fileTree(include: ['*.aar'], dir: 'libs')
    implementation 'androidx.appcompat:appcompat:1.0.0'
    implementation 'androidx.legacy:legacy-support-v4:1.0.0'
    implementation 'androidx.recyclerview:recyclerview:1.0.0'
    implementation 'com.facebook.fresco:fresco:2.5.0'
    implementation "com.facebook.fresco:animated-gif:2.5.0"
    implementation 'com.github.bumptech.glide:glide:4.9.0'
    implementation 'com.alibaba:fastjson:1.1.46.android'
	~~~
	
	support版本需添加如下资源
	
	~~~
    implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'], exclude: [])
    implementation "com.android.support:support-v4:28.0.0"
    implementation "com.android.support:appcompat-v7:28.0.0"
    implementation 'com.android.support:recyclerview-v7:28.0.0'
    implementation 'com.facebook.fresco:fresco:1.13.0'
    implementation "com.facebook.fresco:animated-gif:1.13.0"
    implementation 'com.github.bumptech.glide:glide:4.9.0'
    implementation 'com.alibaba:fastjson:1.1.46.android'
	~~~
	
	uni-app配置时需要在build.gradle中添加aaptOptions配置（位置：android {}）
	
	~~~
	aaptOptions {  
	    additionalParameters '--auto-add-overlay'  
	    ignoreAssetsPattern "!.svn:!.git:.*:!CVS:!thumbs.db:!picasa.ini:!*.scc:*~"  
	}  
	~~~
	
	**注意：更新SDK时需要同时更新aar/jar文件和build.gradle配置**
	
2. 应用配置

	+ 配置Appkey
 	
	**3.1.10版本起需要申请 Appkey，申请请[参考](/AppDocs/usesdk/appkey.md)**
	
	打开Androidmanifest.xml， 导航到Application节点，创建meta-data节点，name为dcloud_appkey，value为申请的AppKey如下：
	
	```
	<application
        ...>
        <meta-data
            android:name="dcloud_appkey"
            android:value="替换为自己申请的Appkey" />
	```

	+ 配置应用版本号 <h3 id="versionCode"></h3>

	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-4.png)
	
	versionCode为应用的版本号（整数值），用于各应用市场的升级判断，建议与manifest.json中version -> code值一致
	
	versionName为应用的版本名称（字符串），在系统应用管理程序中显示的版本号，建议与manifest.json中version -> name值一致。
	
	applicationId为应用的包名，一般设置为反向域名，不建议修改。

	建议将targetSdkVersion设置为25或以上。
	
	App离线SDK minSdkVersion最低支持21，小于21在部分5.0以下机型上将无法正常使用。
	
	完整版build.gradle可参考如下内容。
	
	~~~
	apply plugin: 'com.android.application'

	android {
		compileSdkVersion 29
		defaultConfig {
			applicationId "com.example.myapplication"
			minSdkVersion 21
			targetSdkVersion 28
			versionCode 1
			versionName "1.0"
			multiDexEnabled true
			ndk {
				abiFilters 'x86', 'armeabi-v7a', 'arm64-v8a'
			}
		}
		buildTypes {
			release {
				minifyEnabled false
				proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
			}
		}
	}

	dependencies {
		implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'], exclude: [])
		implementation 'androidx.appcompat:appcompat:1.0.0'
		implementation 'androidx.legacy:legacy-support-v4:1.0.0'
		implementation 'com.alibaba:fastjson:1.1.46.android'
	}
	~~~
	
	**注意：如果需要集成的第三方sdk存在so库文件，只需添加armeabi-v7a、arm64-v8a、x86三个文件夹即可，否则会导致在部分手机上无法运行。**
	
	+ 配置应用名称 <h3 id="appName"></h3>

	打开app-> src -> main -> res -> values -> strings.xml文件，修改“app_name”字段值，该值为安装到手机上桌面显示的应用名称，建议与manifest.json中name（基础配置中的应用名称）对应。

	+ 配置应用启动页及provider节点

	将下述内容添加到Androidmanifest.xml的application节点中
	
	**注意：新建的项目默认会有一个MainActivity的节点，必须删掉！**
	
	**注意：为适配折叠屏，需要在PandoraEntryActivity的android:configChanges属性中追加“smallestScreenSize|screenLayout|screenSize”，参考如下配置。**

	~~~
		<activity
            android:name="io.dcloud.PandoraEntry"
            android:configChanges="orientation|keyboardHidden|keyboard|navigation"
            android:label="@string/app_name"
            android:launchMode="singleTask"
            android:hardwareAccelerated="true"
            android:theme="@style/TranslucentTheme"
            android:screenOrientation="user"
            android:windowSoftInputMode="adjustResize" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
		<activity
            android:name="io.dcloud.PandoraEntryActivity"
            android:launchMode="singleTask"
            android:configChanges="orientation|keyboardHidden|screenSize|mcc|mnc|fontScale|keyboard|smallestScreenSize|screenLayout|screenSize"
            android:hardwareAccelerated="true"
            android:permission="com.miui.securitycenter.permission.AppPermissionsEditor"
            android:screenOrientation="user"
            android:theme="@style/DCloudTheme"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <action android:name="android.intent.action.VIEW" />
                <data android:scheme="h56131bcf" />
            </intent-filter>
        </activity>
		<!--provider节点必须添加-->
		<provider
            android:name="io.dcloud.common.util.DCloud_FileProvider"
            android:authorities="${apk.applicationId}.dc.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/dcloud_file_provider" />
        </provider>
	~~~

	**注意：${apk.applicationId} 必须替换成当前应用的包名**
	
	如果集成离线SDK时需要重写application，必须继承自DCloudApplication，否则会导致SDK中业务逻辑无法正常运行。
		
	将对应的application配置到Androidmanifest.xml中（如下），并添加tools:replace="android:name"以防出现冲突。
		
	~~~
		<application  
		    android:name="io.dcloud.test.TestApplication"  
		    android:icon="@drawable/icon"  
		    android:label="@string/app_name"  
		    tools:replace="android:name">
		</application>
	~~~

	+ 配置应用图标和启动界面

		+ 图标名称

			icon.png为应用的图标。
	
			push.png为推送消息的图标。
	
			splash.png为应用启动页的图标。

	将icon.png、push.png、splash.png放置到drawable，drawalbe-ldpi，drawable-mdpi，drawable-hdpi，drawable-xhdpi，drawable-xxhdpi文件夹下，不同文件夹下对应不同图片尺寸，可参考[文档](https://blog.csdn.net/xuaho0907/article/details/72848520)
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-6.png)
	
	修改Androidmanifest.xml中application节点的icon属性为@drawable/icon。如下图：
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-15.png)
	
	注意：splash.png在不同设备上显示效果不一样，为防止出现拉伸的状况，建议使用Android studio生成.9.png类型图片，可参考[文档](https://ask.dcloud.net.cn/article/35527)
	
3. 资源配置

	Android studio默认项目没有assets文件夹，需手动创建，创建位置与java目录同级。
		
	* 创建data文件夹并拷贝资源

	将SDK->assets->data文件夹拷贝到刚刚创建的assets文件夹下，如下图：
		
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-7.png)
	
	注意：SDK升级时，data下资源需要同时更新。
		
	* 创建apps文件夹并拷贝资源

	将HBuilderX中的项目导出，将导出的资源复制到assets->apps下，如下图所示：
		
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-8.png)
		
	[导出app资源](/AppDocs/importfeproject/export.md)
		
	* 修改dcloud_control.xml文件

	修改dcloud_control.xml中的appid为拷贝过来的uni-app的id，确保dcloud_control.xml中的appid与manifest.json中的id与文件夹名一致，如下图所示：
		
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-9.png)
	
	生成自定义基座，需要在根节点下添加debug="true"和syncDebug="true"，如下图：
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-20.png)
	
	自定义基座支持热更新需要将debug-server-release.aar打入apk中，生成正式包时可以删除。
	
	[详细步骤可参考链接](https://ask.dcloud.net.cn/article/35482)
	
4. 其他配置

	AndroidX 配置
	
	需要在项目根目录下的gradle.properties文件中添加如下内容
	
	~~~
	android.useAndroidX=true
	android.enableJetifier=true
	~~~

<!--5. 模块配置

	* 依赖库配置

	参考“Feature-Android.xls”文档确定应用中使用到的扩展API，拷贝libs目录中对应的“jar或aar”文件拷贝到工程的app->libs目录下，如使用地图功能，需将下图“jar包”列对应的文件拷贝到libs目录下。
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-11.png)
	
	* 添加应用权限
	
	参考“Feature-Android.xls”文档的“AndroidManifest.xml permission”列，将需要配置的模块的权限拷贝到对应项目的Androidmanifest.xml中，如下图。
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-12.png)
	
	* dcloud_properties.xml 配置
	
	参考“Feature-Android.xls”文档的“properties.xml文件配置”列，将需要配置的模块的features节点和services节点拷贝到dcloud_properties.xml中，如下图。
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-14.png)
	
	* 添加第三方应用信息
	
	参考“Feature-Android.xls”文档的“AndroidManifest.xml Application节点”列，将需要配置的模块的节点拷贝到对应项目的Androidmanifest.xml中并修改对应的应用信息，如下图。
	
	![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/5-13.png)
	
	* 其他模块配置
	
		* [推送插件配置](http://ask.dcloud.net.cn/article/227)
		* [分享插件配置](http://ask.dcloud.net.cn/article/210)
		* [登陆鉴权插件配置](http://ask.dcloud.net.cn/article/211)
		* [地图插件配置](http://ask.dcloud.net.cn/article/212)
		* [支付插件配置](http://ask.dcloud.net.cn/article/214)
		* [定位插件配置](http://ask.dcloud.net.cn/article/213)

-->
	
## 运行

连接手机，点击按钮，运行即可。

![avatar](https://img.cdn.aliyun.dcloud.net.cn/nativedocs/5%2BSDK-android/image/7-6.png)
	

	


	
