# mediasoup的Broadcaster与ffmpeg/gstreamer接入
> Broadcaster展示用http创建Transport进行推拉流，不需要websocket。
> 1. 不需要信令，ffmpeg，gstreamer等就能直接接入。
> 2. 不一定需要摄像头麦克风，纯音视频帧就能接入。

### 意义何在
1. Broadcaster的意义何在：展示一种整合的架构能力。
2. mediasoup worker没有Broadcaster的概念，后者是demo提出的。
3. Broadcaster也是一个peer，但是无法接收实时信令通知。用http信令实现创建transport，produce人，comsumer等。

### 两个示例
1. 官方Broadcaster-demo实现http信令创建webrtctransport发布手动合成音视频数据等。
    ```
    void Broadcaster::CreateSendTransport(bool enableAudio, bool useSimulcast)
    {
        std::cout << "[INFO] creating mediasoup send WebRtcTransport..." << std::endl;
    
        json sctpCapabilities = this->device.GetSctpCapabilities();
        /* clang-format off */
        json body =
        {
            { "type",    "webrtc" },
            { "rtcpMux", true     },
            { "sctpCapabilities", sctpCapabilities }
        };
        /* clang-format on */
    
        auto r = cpr::PostAsync(
                   cpr::Url{ this->baseUrl + "/broadcasters/" + this->id + "/transports" },
                   cpr::Body{ body.dump() },
                   cpr::Header{ { "Content-Type", "application/json" } },
                   cpr::VerifySsl{ verifySsl })
                   .get();
    
        if (r.status_code != 200)
        {
            std::cerr << "[ERROR] unable to create send mediasoup WebRtcTransport"
                      << " [status code:" << r.status_code << ", body:\"" << r.text << "\"]" << std::endl;
    
            return;
        }
    ...
    ```

2. mediasoup-demo/Broadcaster/ffmepg.sh和gstreamer.sh实现命令行调用http创建plainTransport实现ffmpeg从本地文件推流rtp流
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
   
    
## ffmpeg和gstreamer接入
3. mediasoup接入ffmpeg进行推拉流
    - 实现原理：使用PlainRtpTransport转发纯rtp包实现
    - 单通道单流：1个PlainRtpTransport是否只能推拉一路流，多路流rtp包混在一起ffmpeg能否解包。
    - 没有信令：peer和流的变化无法通知。
    - peer： 没有信令通知，就应独立于peers单独存在，不一定要join，能拿到producer列表就行拉流。
     
4. 推流流程：
    > 这里假设每1个PlainRtpTransport对应1路推流或者拉流
    
    - 在room内创建一个ffmpegPeer
    - 创建server的rtp接收端：给ffmpegPeer创建PlainRtpTransport，返回得到服务端监听的ip和端口。
    - 创建produce流：流的rtpParameters编码格式参数。
    - 用gstreamer或者ffmpeg 往 服务端监听的ip和端口 推送rtp包。

4. 拉流流程：
    > 这里假设每1个PlainRtpTransport对应1路推流或者拉流
    - 在room内创建一个ffmpegPeer，可以推拉流共用
    - 创建server的rtp接收端：给ffmpegPeer创建PlainRtpTransport，返回得到服务端监听的ip和端口。
    - 创建consumer
    - connect到PlainRtpTransport：把本地的接收端口给到服务器
    - 用gstreamer或者ffmpeg在本地端口 接收rtp包
6. 参考实现[https://blog.csdn.net/weixin_29405665/article/details/111994983](https://blog.csdn.net/weixin_29405665/article/details/111994983)