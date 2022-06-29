# mediasoup的boardcaster的意义何在
1. boardcaster的意义何在：展示一种整合的架构能力。
2. mediasoup没有boardcaster的概念，后者是demo提出的。
3. boardcaster也是一个peer，但是无法接收实时信令通知。
4. boardcaster用http信令实现创建transport，produce人，comsumer等功能。
5. 官方boardcaster-demo实现http信令创建webrtctransport发布手动合成音视频数据等。
6. mediasoup-demo/boardcaster/ffmepg.sh和gstreamer.sh实现命令行调用http创建painttransport实现ffmpeg从本地文件推流rtp流