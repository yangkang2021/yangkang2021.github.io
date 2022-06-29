# mediasoup的demo的websocket的keepalive与断网检测

### 1. mediassoup的websocket server
- protoo-sever会定期的ping client
- websocket收到ping都会回复pong
![](.mediasoup_websocket_pingpong_images/8e0c0073.jpeg)

### 2. 客户端心跳做法
websocket两端判断网络可用性都需要加心跳检测，做法：
- 用websocket自带的ping-pong，不断的发ping。根据发送情况和返回pong的时间检测网络。
- 在业务层添加自定义信令如"heartbeat"实现。

