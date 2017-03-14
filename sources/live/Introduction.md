## 产品概述
又拍直播云（UPLive），致力于为用户提供简单快速接入的直播整体解决方案。又拍云直播基于又拍云内容分发网络为直播应用提供超低延迟、高码率、高并发的从推流端到播放端的一站式解决方案，支持 RTMP、HTTP-FLV 和 HLS 输出等基本功能，包括录制、转码等增值服务。

## 产品架构
直播 CDN 主要支持两种模式，一种是客户推流至又拍 CDN，推流至又拍时，用户只需要做好推流端，其他源站功能比如转码录制都可以由 CDN 完成。
<center>![UPYUN 源](http://image-pro.b0.upaiyun.com/product/uplive/image/owner2.png)</center>  

另一种是客户提供源站，又拍 CDN 回客户源站拉流，该方式客户对其主播流的可控程度更大，但需要保障源站的稳定性。具体使用哪种源站，根据具体的业务情况而定。
<center>![自主 源](http://image-pro.b0.upaiyun.com/product/uplive/image/guest.png)</center>

具体使用哪种源站，根据实际的业务情况而定。


