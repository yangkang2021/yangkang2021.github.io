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

关于单端口，这里面有3个问题： https://mp.weixin.qq.com/s/IAmJAZ3HA0_FIX0-lannZw
1. tcp和udp可以用同一端口吗？ 可以
2. 两个tcp可以吗？ 可以，需要reuse。 nginx就是那样的。 但可能出现惊群现象。
3. 两个upd可以吗？没测试，但reuse模式应该可以，但只有一个进程能收到包。
mediasoup createWebRtcTransport的时候 要先告诉服务端用tcp还是udp

[Linux下端口复用(SO_REUSEADDR与SO_REUSEPORT)](http://t.zoukankan.com/hehehaha-p-6332326.html)
 
 
 freebsd与linux下bind系统调用小结：

    只考虑AF_INET的情况（同一端口指ip地址与端口号都相同）
freebsd支持SO_REUSEPORT和SO_REUSEADDR选项,而linux只支持SO_REUSEADDR选项。
freebsd下,使用SO_REUSEPORT选项，两个tcp的socket可以绑定同一个端口；同样，使用SO_REUSEPORT选项，两个udp的socket可以绑定同一个端口。
linux下，两个tcp的socket不能绑定同一个端口；而如果使用SO_REUSEADDR选项，两个udp的socket可以绑定同一个端口。
freebsd下，两个tcp的socket绑定同一端口，只有第一个socket获得数据。
freebsd下，两个udp的socket绑定同一端口，如果数据包的目的地址是单播地址，则只有第一个socket获得数据，而如果数据包的目的地址是多播地址，则两个socket同时获得相同的数据。
linux下，两个udp的socket绑定同一端口，如果数据包的目的地址是单播地址，则只有最后一个socket获得数据，而如果数据包的目的地址是多播地址，则两个socket同时获得相同的数据。