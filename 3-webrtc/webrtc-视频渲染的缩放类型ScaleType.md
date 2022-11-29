# webrtc-视频渲染的缩放类型ScaleType



### 一. ios
```
if (_remoteVideoSize.width > 0 && _remoteVideoSize.height > 0) {
    // Aspect fill remote video into bounds.
    CGRect remoteVideoFrame =
        AVMakeRectWithAspectRatioInsideRect(_remoteVideoSize, bounds);
    CGFloat scale = 1;
    if (remoteVideoFrame.size.width > remoteVideoFrame.size.height) {
      // Scale by height.
      scale = bounds.size.height / remoteVideoFrame.size.height;
    } else {
      // Scale by width.
      scale = bounds.size.width / remoteVideoFrame.size.width;
    }
    remoteVideoFrame.size.height *= scale;
    remoteVideoFrame.size.width *= scale;
    _remoteVideoView.frame = remoteVideoFrame;
    _remoteVideoView.center =
        CGPointMake(CGRectGetMidX(bounds), CGRectGetMidY(bounds));
  } else {
    _remoteVideoView.frame = bounds;
  }
```

### 二. android
```
renderer.setScalingType(RendererCommon.ScalingType.SCALE_ASPECT_FIT,RendererCommon.ScalingType.SCALE_ASPECT_FIT);
```
#### 三. opengl

### 四. GDI

### 五. D3d

##### 六. Metal