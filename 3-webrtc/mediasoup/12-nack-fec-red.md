# webrtc和mediasoup的nack，fec，red

>行业前辈们的聊天纪要

### 1.nack
- 必须的

### 2.fec
- 带内fec增加很少带宽
- mediasoup不支持fec，fec可能会消耗大量cpu
- mediasoup基于packet, 比基于frame的实现fec要困难

### 3.red
- webrtc M88甚至更早就支持opus red，M99已经默认不开启，但协商不成功则不启用。
- red相当于成本翻倍，但音频流量占比少，所以音频加上red能提高音频质量而不用占用很多带宽完全值得。
- red虽然增加了流量，但是他不增加udp数据包个数，这个很重要，在一定程度可以避免网络拥塞的恶化
- sfu如果拉流端有的支持、有的不支持red，sfu不能透传会比较麻烦。

### 4.经验
- 冗余度(指抗丢包的冗余带宽占比)=丢包率的时候性价比最高。
- webrtc无法识别丢包是拥塞导致的还是物理链路原因，就会预测降带宽，导致预测结果上不去而不发送足够fec和red，可以强行发送。
- 大厂抗大丢包率(百分之几十)靠fec
- 理论上来说，在不拥塞的情况下，大厂的做法是尽量提高带宽利用率
- fec+nack ，基本能抗70%
- webrtc准备把pcc加到拥塞控制中
- opus音频：延时小时，用带内fec+nack ；延时大时，用带内fec+red

### 5.问题
- fec耗cpu还没太大用？暂时只考虑red, 不考虑video fec？
- red的成本太高，流量翻倍？
- nack+fec+red是不是更加冗余？

### 5.参考
- 提到一个开源项目Jitsi Meet：https://jitsi.org/jitsi-meet/