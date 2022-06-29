# mediasoup官方单端口实现
添加中间层：WebRtcServer，只能做到每一个worker各一个端口。

1. 实现方式：通过添加中间层WebRtcServer实现。由WebRtcServer统一接收包后给到WebrtcTrasnport走原来的流程。
2. 对客户端没有影响： 所有的流程，接口，信令不变。
3. 兼容性：支持单端口同时保留多端口方案。demo默认单端口，可用MEDIASOUP_USE_WEBRTC_SERVER=false切换到原来的多端口方案。
4. demo具体变动：
    - 启动worker就创建一个WebRtcServer。
    - 每次创建WebrtcTransport的时候传入WebRtcServer，就走单端口方案，不传走原来的多端口。
5. 分析：
    - WebRtcServer要建立客户端和WebrtcTransport的对应关系；
    - 创建WebrtcTransport信令返回的ice候选是WebRtcServer的同一个ip:port则客户端就直接连WebRtcServer。
6. 相关文档地址
    - [mediasoup3.10.0版本发布说明](https://mediasoup.discourse.group/t/mediasoup-3-10-0-released-with-the-new-webrtcserver-class-listen-into-a-single-port/4313)
    - [mediasoup的代码变更](https://github.com/versatica/mediasoup/commit/5c858603cd7113e094001770be8ec8b8836f9cbb)
    - [mediasoup-demo的代码变更](https://github.com/versatica/mediasoup-demo/commit/c3610f3edd9cab732f778cfe9c1388fa9c024101)
    - [mediasoup的API文档变化-worker-createWebRtcServer](https://mediasoup.org/documentation/v3/mediasoup/api/#worker-createWebRtcServer)
    - [mediasoup的API文档变化-WebRtcServer](https://mediasoup.org/documentation/v3/mediasoup/api/#WebRtcServer)
7. 一张图总结
![](.mediasoup_singleport_images/d32492c0.jpeg)
