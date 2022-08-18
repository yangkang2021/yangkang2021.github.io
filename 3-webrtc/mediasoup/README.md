# mediasoup知识体系

1. [mediasoup的开源项目整理](01-mediasoup-opensource.md)
1. [mediasoup-demo编译](mediasoup_build.md)
2. [mediasoup-demo的docker部署](mediasoup_docker.md)
3. [mediasoup-demo信令](mediasoup_demo_signalling.md)
4. [mediasoup的c++调试(vs2019/xcode/gdb)](mediasoup_cpp_debug.md)
5. [libmediasoupclient介绍](libmediasoupclient_intro.md)
6. [mediasoup-demo的android客户端代码介绍](mediasoup_demo_android_client.md)
7. [protoo使用与实现](protoo.md)
8. [mediasoup官方单端口实现](mediasoup_singleport.md)
9. [ediasoup的demo的websocket的keepalive与断网检测](mediasoup_websocket_pingpong.md)
10. [boardcaster](boardcaster.md)
11. [mediasoup-rust](mediasoup-rust.md)
12. [mediasoup媒体控制与协商机制](11-webrtc-parameters.md)
13. [webrtc和mediasoup的nack,fec,red](12-nack-fec-red.md)
13. [mediasoup的web端demo的参数](13-mediasoup的web端demo的参数.md)



关于立体声
是很多地方会有resample 或者downmix操作。突然想到应该有人研究过48k 立体声直通，比如做直播的。
立体声的话，sdp要加stereo=1吧
检查采集音源, 推流端SDP, 拉流端SDP. 拉流渲染设备是否支持..浏览器基本就这几个影响.

，两个udp的socket绑定同一端口，如果数据包的目的地址是单播地址，则只有最后一个socket获得数据，而如果数据包的目的地址是多播地址，则两个socket同时获得相同的数据。