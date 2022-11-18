# webrtc的屏幕共享

### 一. 屏幕共享需求：
1. 视频: pc端桌面或者窗口可以指定，移动端就桌面。
2. 音频: 支持系统声音共享，原有的mic声音保持通话。

### 一. webrtc自身的屏幕共享方案

1. pc：windows，mac，linux
   - 用各种平台api：实现了窗口和屏幕采集
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
- pc(windows/mac/linux)：**用obs**。
- Android：**在webrtc android的基础上增加AudioTrack录制系统声音**。
- ios：**用ReplayKit**。
- web端：用chrome自带的api。
