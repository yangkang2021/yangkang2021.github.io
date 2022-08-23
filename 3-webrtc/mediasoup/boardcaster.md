# mediasoup的boardcaster的意义何在
1. boardcaster的意义何在：展示一种整合的架构能力。
2. mediasoup没有boardcaster的概念，后者是demo提出的。
3. boardcaster也是一个peer，但是无法接收实时信令通知。
4. boardcaster用http信令实现创建transport，produce人，comsumer等功能。
5. 官方boardcaster-demo实现http信令创建webrtctransport发布手动合成音视频数据等。
6. mediasoup-demo/boardcaster/ffmepg.sh和gstreamer.sh实现命令行调用http创建plainTransport实现ffmpeg从本地文件推流rtp流
```
//MEDIA_FILE是一个本地文件，rtp://xxx就是plainTransport的推流地址地址
ffmpeg \
	-re \
	-v info \
	-stream_loop -1 \
	-i ${MEDIA_FILE} \
	-map 0:a:0 \
	-acodec libopus -ab 128k -ac 2 -ar 48000 \
	-map 0:v:0 \
	-pix_fmt yuv420p -c:v libvpx -b:v 1000k -deadline realtime -cpu-used 4 \
	-f tee \
	"[select=a:f=rtp:ssrc=${AUDIO_SSRC}:payload_type=${AUDIO_PT}]rtp://${audioTransportIp}:${audioTransportPort}?rtcpport=${audioTransportRtcpPort}|[select=v:f=rtp:ssrc=${VIDEO_SSRC}:payload_type=${VIDEO_PT}]rtp://${videoTransportIp}:${videoTransportPort}?rtcpport=${videoTransportRtcpPort}"
```