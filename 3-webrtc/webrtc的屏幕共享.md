# 基于webrtc的全平台-屏幕共享-方案

### 一. 屏幕共享需求：
1. 视频: pc端桌面或者窗口可以指定，移动端就桌面。
2. 音频: 支持系统声音共享，原有的mic声音保持通话。

### 二. webrtc自身的屏幕共享方案

1. pc：windows，mac，linux
   - 用各自平台api：实现了窗口和屏幕采集
   - 代码目录：webrtc\src\modules\desktop_capture
   - **分析：没有录制系统声音，mic的声音在通话流。**
   - [参考](https://blog.csdn.net/zhangpeng_linux/article/details/85858475)
2. android
    - 采用MediaProjection实现了屏幕采集
    - 代码目录：webrtc\src\sdk\android\api\org\webrtc\ScreenCapturerAndroid.java
    - **分析：没有录制系统声音，mic的声音在通话流。**
    - [MediaProjection使用参考](https://www.itxm.cn/post/23918.html)
3. ios
    - **没用实现**
4. web端
    - 用chrome自带的api

### 三. 我们的方案
1. pc(windows/mac/linux)：**用obs**。
2. Android：**在webrtc android的基础上增加AudioTrack+AudioPlaybackCapture录制系统声音**。
    - AudioTrack + MediaRecorder.AudioSource.MIC/Default
    - AudioTrack + AudioPlaybackCapture：Android10及以后支持
3. ios：**用ReplayKit**。
    - RPScreenRecorder只能本应用内录屏
    - RPSystemBroadcastPickerView+独立进程的扩展应用实现，需要跨进程传递采集的数据。
    - 实现一个BroadcastExtensionLauncher
4. web端：用chrome自带的api。

### 四. 怎么处理：麦克风音频流 + 系统声音流的并存
- 方案1：多路音频流独立，可能有回音消除问题吗
- 方案2：混音成一路到通话流。
