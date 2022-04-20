---

title: AndroidStudio集成ReactNative到已有工程

date: 2018-01-04 19:34:46

tags: [ ReactNative , 集成RN ]

categories: ReactNative

keywords: ReactNative , 集成RN 

description: AndroidStudio集成ReactNative到已有工程

top_img: http://image.jucaiwy.com/image/20180104/react_native.jpg

cover: http://image.jucaiwy.com/image/20180104/react_native.jpg

---

最近在看ReactNative的教程，书本上都是直接npm init出来的一个个工程，我突发奇想，觉得直接继承到现有的app里边比较方便开发，说干就干，在网上查了不少资料，也跳了不少坑，现在把我搭建环境的步骤记录下来，方便以后查看


### 1. 新建AndroidStudio工程

首先需要新建AndroidStudio工程，注意使用

```
compileSdkVersion 23
minSdkVersion 16
targetSdkVersion 23
```

以及依赖：

```
implementation 'com.android.support:appcompat-v7:23.0.1'
```

### 2. 初始化

打开AndroidStudio底部的Terminal，然后再命令行模式下，输入：

```bash
npm init
```

如图所示：

![](http://image.jucaiwy.com/image/20180104/EXnbv92.png)

然后，一路回车，会生成 package.json文件

![](http://image.jucaiwy.com/image/20180104/q0oJTnh.png)

### 3. 安装node-module

继续在AndroidStudio底部的Terminal中，输入以下命令：
```bash
npm install --save react react-native
```

然后，回车，大一一分多钟以后，会在工程目录中看到 node_modules 这个包，表示安装成功

安装中：

![](http://image.jucaiwy.com/image/20180104/3B4dP8p.png)

安装成功：

![](http://image.jucaiwy.com/image/20180104/QGitFFv.png)

### 4. 下载.flowconfig

可以使用命令下载这个文件：

```bash
curl -o .flowconfig https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig
```
	
或者直接在根目录中新建文档：.flowconfig,然后复制以下文本，在这个文件中进行保存

[https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig](https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig)


### 5. 添加index.android.js

这里我们使用index.android.js为ReactNative的界面入口，也是展示HelloWord的js界面，我们可以直接在工程根目录下创建index.android.js文件，代码：
```
	import React from 'react';
	import {
	    AppRegistry,
	    StyleSheet,
	    Text,
	    View
	} from 'react-native';
	
	class HelloWorldApp extends React.Component {
	    render() {
	        return (
	            <View style={styles.container}>
	                <Text style={styles.hello}>Hello world! I am from ReactNattive!!</Text>
	            </View>
	        )
	    }
	}
	
	var styles = StyleSheet.create({
	    container: {
	        flex: 1,
	        justifyContent: 'center',
	    },
	    hello: {
	        fontSize: 20,
	        textAlign: 'center',
	        margin: 10,
	    },
	});
	
	AppRegistry.registerComponent('ReactNative', () => HelloWorldApp);
```

这里需要注意最后一行代码，registerComponent，这里的第一个参数，必须和自己新建的Android项目名称一样，我们的项目名称是ReactNative，所以第一个参数应该是ReactNative，第二个参数和index.android.js中定义的方法名一样。

所有的文件添加完成后，整个包是这样的：

![](http://image.jucaiwy.com/image/20180104/7zODqQG.png)



### 6. App添加依赖

首先需要再ReactNative项目app模块的build.gradle目录中添加react-native依赖
```
	compile "com.facebook.react:react-native:+"
```

添加完成后，完整的代码如下：

``` 
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
    defaultConfig {
        applicationId "jd.com.react"
        minSdkVersion 16
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        // 开始修改java.lang.UnsatisfiedLinkError: dlopen failed: "/data/data/com.snapdeal.main/lib-main/libgnustl_shared.so" is 32-bit instead of 64-bit
        ndk {
            abiFilters "armeabi-v7a", "x86"
        }

        packagingOptions {
            exclude "lib/arm64-v8a/librealm-jni.so"
        }
        // 结束修改java.lang.UnsatisfiedLinkError: dlopen failed: "/data/data/com.snapdeal.main/lib-main/libgnustl_shared.so" is 32-bit instead of 64-bit
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

}

dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    implementation 'com.android.support:appcompat-v7:23.0.1'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    testImplementation 'junit:junit:4.12'

    compile "com.facebook.react:react-native:+"
}
	
```


这里可以看到，我们工程的编译版本已经修改为23，引用的

```
com.android.support:appcompat-v7:23.0.1
```


### 7. 修改maven为本地路径

在工程根目录下，找到build.gradle文件，添加maven路径为本地生成的node-modules

```
maven {
    // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
    url "$rootDir/../node_modules/react-native/android"
}
```

修改完成后，完整的文件为：

![](http://image.jucaiwy.com/image/20180104/RGxteet.png)


### 8. 添加Application

为当前工程指定Application，新建一个Application，继承自ReactApplication，这里需要注意的是，如果当前编译版本不是23，那么在新建这个类的时候，将会提示**找不到ReactApplication**

```java
	public class MyApplication extends Application implements ReactApplication {
    	private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
	        @Override
	        public boolean getUseDeveloperSupport() {
	            return BuildConfig.DEBUG;
	        }
	
	        @Override
	        protected List<ReactPackage> getPackages() {
	            return Arrays.<ReactPackage>asList(
	                    new MainReactPackage()
	            );
	        }
	    };

	    @Override
	    public ReactNativeHost getReactNativeHost() {
	        return mReactNativeHost;
	    }
	
	    @Override
	    public void onCreate() {
	        super.onCreate();
	    }
	}
```

新建完成后：

1. 需要再AndroidManifest.xml中为当前项目添加这个application的注册
2. 需要添加网络权限，因为需要从服务端刷新js界面
```
<uses-permission android:name="android.permission.INTERNET" />
```
3. 需要添加调试界面DevSettingsActivity 
```
<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
```


### 9. 修改MainActivity

首先在MainActivity的布局文件中添加ReactRootView，我这里直接删除了之前新建工程默认出现的TextView，替换为

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

	 <com.facebook.react.ReactRootView
	     android:id="@+id/react_root_view_id"
	     android:layout_width="300dp"
	     android:layout_height="300dp" />
</LinearLayout>
```

然后修改MainActivity：

```java
public class MainActivity extends Activity {
	    ReactRootView reactRootView;
	    ReactInstanceManager reactInstanceManager;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.react_root_view);
	        reactRootView = (ReactRootView) findViewById(R.id.react_root_view_id);
	
	        // 这里需要注意，官方文档setJSMainModuleName在0.51.0版本中找不到，替换为setJSMainModulePath
	        reactInstanceManager = ReactInstanceManager.builder()
	                .setApplication(getApplication())
	                .setBundleAssetName("index.android.bundle")
	                .setJSMainModulePath("index.android")
	                .addPackage(new MainReactPackage())
	                .setUseDeveloperSupport(BuildConfig.DEBUG)
	                .setInitialLifecycleState(LifecycleState.RESUMED)
	                .build();
	
	        //ReactNative 是项目名，需要和index.adnroid.js中的保持一致
	        reactRootView.startReactApplication(reactInstanceManager, "ReactNative", null);
	    }
	}
```
至此，已经完成了配置，我们可以运行当前应用到真机上，然后启动npm进行测试了

### 10. 测试

	1. 启动npm，在Terminal窗口中，输入 npm start即可启动
	2. 运行当前App到手机上
	3. 手机必须和电脑在同意网段，可以使用电脑开个热点，手机连接
	4. 手机进入应用后，摇一摇，会出现debug的dialog，可以配置IP和端口


### 11. 后记：
这个文章是我一遍搭建，一边整理的，参考了[http://blog.csdn.net/github_33304260/article/details/76571961](http://blog.csdn.net/github_33304260/article/details/76571961) 可能存在配置问题，有缘人看到了，可以试试，有啥问题，随时留言或者和我联系，我再进行更新文档
