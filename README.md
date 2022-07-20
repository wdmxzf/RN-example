# RN-example
RN 项目构建
## 基础环境
* Node（>14）
* JDK（jdk11）
* Android 环境或者 iOS 环境
* npm 、yarn
* 设置镜像，但是不要 使用 **cnpm** 命令，它会导致package 不识别路径。


## RN 集成到现有Android 项目
### ReactNative 工程配置
* 创建空文件夹 androidRN
* 创建 package.json 文件
```json
{
  "name": "MyReactNativeApp",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "yarn react-native start"
  }
}
```
* 在 androidRN 目录下执行：
  `yarn add react-native`
* 等待一段时间（10分钟，网速慢20分钟）
* 再执行 `yarn add react`
* ***创建 index.js文件***
```javascript
import React from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';

class HelloWorld extends React.Component {
  render () {
    return (
      <View style={styles.container}>
        <Text style={styles.hello}>Hello, World</Text>
      </View>
    );
  }
}
var styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center'
  },
  hello: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10
  }
});

AppRegistry.registerComponent(
  'MyReactNativeApp',
  () => HelloWorld
);
```
* 生成bundle 文件，若没有assets 文件夹， 需要创建一个。
  `npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/`
### 现有项目配置
* 把生成的bundle 文件 放入已有项目的assts 文件夹下。
* 在现有的 Android 项目里添加依赖
```gradle
    implementation "com.facebook.react:react-native:+" // From node_modules
    implementation "org.webkit:android-jsc:+"
```
* 在 setting.gradle 文件中添加 【1】
```gradle
       maven {
            // All of React Native (JS, Android binaries) is installed from npm
            url "D:/WORKING/ZHG/RN-example/androidRN/node_modules/react-native/android"
        }
        maven {
            // Android JSC is installed from npm
            url("D:/WORKING/ZHG/RN-example/androidRN/node_modules/jsc-android/dist")
        }
```
* **或者** 把现有的项目移入 androidRN 目录下的 android 中， 在 setting.gradle 文件中添加 【2】
```gradle
         maven {
            // All of React Native (JS, Android binaries) is installed from npm
            url "$rootDir/../node_modules/react-native/android"
        }
        maven {
            // Android JSC is installed from npm
            url("$rootDir/../node_modules/jsc-android/dist")
        }
```
* ***注意必须设置【1】或者【2】，不然项目运行不起来***
* 然后再创建一个 ReactActivity
```kotlin
class MyRNActivity : Activity(), DefaultHardwareBackBtnHandler {

    private var mReactRootView: ReactRootView? = null
    private var mReactInstanceManager: ReactInstanceManager? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        SoLoader.init(this, false)
        mReactRootView = ReactRootView(this)
//        val packages: List<ReactPackage> = PackageList(application).getPackages()
        // 有一些第三方可能不能自动链接，对于这些包我们可以用下面的方式手动添加进来：
        // packages.add(new MyReactNativePackage());
        // 同时需要手动把他们添加到`settings.gradle`和 `app/build.gradle`配置文件中。


        mReactInstanceManager = ReactInstanceManager.builder()
            .setApplication(application)
            .setCurrentActivity(this)
            .setBundleAssetName("index.android.bundle")
            .setJSMainModulePath("index")
            .addPackage(MainReactPackage())
            .setUseDeveloperSupport(BuildConfig.DEBUG)
            .setInitialLifecycleState(LifecycleState.RESUMED)
            .build()
        // 注意这里的MyReactNativeApp 必须对应"index.js"中的
        // "AppRegistry.registerComponent()"的第一个参数
        mReactRootView!!.startReactApplication(mReactInstanceManager, "MyReactNativeApp", null)
        setContentView(mReactRootView)
    }

    override fun invokeDefaultOnBackPressed() {
        super.onBackPressed()
    }
}
```
* 启动项目
### 若想联机调试需要如下操作
* 在setting.gradle 末尾添加
  `apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)`
* 在 app/build.gradle 末尾添加：
  `apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
  `
* 在 AndroidManifest.xml中 application 标签内添加`android:usesCleartextTraffic="true"`
> 从 Android 9 (API level 28)开始，默认情况下明文传输（http 接口）是禁用的，只能访问 https 接口。 this prevents your application from connecting to the [Metro bundler][metro]. The changes below allow cleartext traffic in debug builds.
* 先在项目根目录运行：`yarn start` 启动 Metro（不要关闭metro 窗口）
* 再在AS 启动项目，就可以看到rn 的画面了。