# RN-example
RN 项目构建
## 基础环境
* Node（>14）
* JDK（jdk11）
* Android 环境或者 iOS 环境
* npm 、yarn
* 设置镜像，但是不要 使用 **cnpm** 命令，它会导致package 不识别路径。

## 创建RN 工程
* 创建 RN 工程 ：
  `npx react-native init RNproject`
* 可能会创建失败，这时创建RN 脚手架
  `npm install -g react-native-cli`
    * 然后创建项目
      `react-native init RNproject`
    * 这时可能会报错 `cli.init is not a function `
      执行： `yarn add react-native --exact`, 再看react-native 版本（`react-native -v`）,变成最新版本了。
    * 然后再执行：`react-native init RNProject`
* 打开模拟器，或者连接真机，cd 到你创建的项目 RNProject 里，
  执行 `yarn android ` 或者  ` yarn react-native run-android` ， 会打开另一个命令窗口运行metro。（有两个窗口同时运行）
* 等待一段时间（十分钟以上，网络不好的 半小时起）