# webrtc的屏幕共享
>屏幕共享需求：视频(桌面或者窗口可以指定) + 音频(系统声音+mic声音)

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

### 三. 我们的方案
1. pc：用obs。
2. Android：在webrtc android的基础上增加AudioTrack录制系统声音。
3. ios：用ReplayKit实现。
