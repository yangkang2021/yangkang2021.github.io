# mediasoup的boardcaster
> boardcaster展示用http创建Transport进行推拉流，不需要websocket。
> 1. 不需要信令，ffmpeg，gstreamer等就能直接接入。
> 2. 不一定需要摄像头麦克风，纯音视频帧就能接入。

### 意义何在
1. boardcaster的意义何在：展示一种整合的架构能力。
2. mediasoup woker没有boardcaster的概念，后者是demo提出的。
3. boardcaster也是一个peer，但是无法接收实时信令通知。用http信令实现创建transport，produce人，comsumer等。

### 两个示例
1. 官方boardcaster-demo实现http信令创建webrtctransport发布手动合成音视频数据等。
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

2. mediasoup-demo/boardcaster/ffmepg.sh和gstreamer.sh实现命令行调用http创建plainTransport实现ffmpeg从本地文件推流rtp流
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