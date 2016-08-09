## 概述
又拍直播云（UPLive），基于又拍云内容分发网络为直播应用提供超低延迟、高码率、高并发的从推流端到播放端的一站式解决方案，支持 RTMP、HTTP-FLV 和 HLS 输出等基本功能，包括录制、转码等增值服务。

## 服务类型
又拍云直播系统支持两种服务类型：又拍云源和自主源站。

<center>![UPYUN 源](http://image-pro.b0.upaiyun.com/product/uplive/image/owner2.png)</center>  

  
又拍云源：表示流媒体内容直接推流到又拍云 CDN ，将推流与分发都进行 CDN 加速。又拍云源现仅支持 RTMP 推流。

> 管理后台：直播加速 > 创建服务 > 又拍云源

创建又拍云源，填写服务名、已备案的播放域名和推流域名，推流域名与播放域名不能共用。
可选择性填写接入点，如果填写了具体的接入点，则只有该接入点的 URL 可进行推流，其他接入点均推流失败。若没有填写具
体接入点，则任意接入点可进行推流。

> 当推流域名填写 push.com ，播放域名填写 play.com ，接入点填写 app ，如果推流 URL 为 
> rtmp://push.com/app/stream 时，其 rtmp 播放 URL 为 rtmp://play.com/app/stream  
> 其中，stream 为流名，又称流密钥，app/stream 又称为频道，同一条流推拉流域名不同，但频道名一致。  
> 注：以上域名、接入点及流名的命名仅为举例用，下同。 


<center>![自主 源](http://image-pro.b0.upaiyun.com/product/uplive/image/guest.png)</center>

  
自主源站：流媒体内容在客户源站，又拍云通过回客户源拉流的方式对其内容进行 CDN 分发。

> 管理后台：直播加速 > 创建服务 > 自主源站

自主源站需要明确回源协议是 RTMP 还是 HTTP，并填写源站 IP 或者 域名，回源 IP 现不支持多个，若回源 IP 会经常变更，建议源站填写域名形式。当回源协议是 RTMP 时，默认端口是 1935，当回源协议是 HTTP 时，默认端口为 80，支持端口自定义。因自主源站是回客户源拉流，则无推流域名，只需要设置播放域名。
>  HTTP 协议回源时，以 HTTP-FLV 形式回源，若源为 HLS 可通过文件加速系统创建服务，配置回源信息。  
>  接入点配置说明同又拍云源。

以上两种业务模式，均支持 RTMP、HTTP-FLV 和HLS 播放，后续章节会详细介绍。

## 基础配置
直播基础配置是直播业务场景使用非常频繁的功能。

### 域名绑定

> 直播管理后台：服务 > 加速域名 > 域名绑定
> 源站类型：全部

域名绑定仅支持已备案成功的自定义域名进行绑定，如果该域名未备案成功，则不能通过域名绑定审核。
审核结果会通过邮件进行通知，正常审核时间为一个工作日。
以下是关于域名绑定的一些重要说明（同文件加速）：

* 除常规形式的域名外（包括顶级域名），我们还支持泛域名绑定，比如 *.yourdomain.com；特别地，其中 * 最多支持匹配 4 层：

```
[v] *.yourdomain.com => foo.yourdomain.com
[v] *.yourdomain.com => bar.yourdomain.com
[v] *.yourdomain.com => foo.bar.yourdomain.com
[v] *.yourdomain.com => foo.bar.baz.yourdomain.com
[v] *.yourdomain.com => foo.bar.baz.qux.yourdomain.com
[x] *.yourdomain.com => foo.bar.baz.qux.quxx.yourdomain.com

[v] *.bar.yourdomain.com => foo.bar.yourdomain.com
[v] *.bar.yourdomain.com => baz.foo.bar.yourdomain.com
[x] *.bar.yourdomain.com => foo.bar.baz.yourdomain.com
```

* 添加域名绑定后，需要到域名服务商的 DNS 解析管理中，将推流域名的 CNAME 解析到 `<bucket>.s1.aicdn.com`，将播放域名的 CNAME 解析到 `<bucket>.s0.aicdn.com`。

> 当业务模式为又拍云源时，用户需要将其推流域名和播放域名分别 CNAME 到对应的又拍云内部域名。  
> 推流 cname 域名为 `<bucket>.s1.aicdn.com`，播放 cname 域名为 `<bucket>.s0.aicdn.com`。  
> 注：`<>` 内的 bucket 需要替换成对应的服务名，下同。

> 当业务模式为自主源站时，用户只需要将其播放域名 CNAME 到对应的又拍云内部播放域名 `<bucket>.s0.aicdn.com`。    

### 回源配置
> 管理后台：服务 > 基础配置 > 回源配置  
> 源站类型：客户源站

#### 回源协议
回源协议支持 HTTP 和 RTMP，当选择 HTTP 回源时，默认端口号为 80，当选择 RTMP 回源时，默认端口号为 1935，具体端口号可以自定义，通过后台设置。
#### 源站
该配置项为可访问的网络地址，可以直接填 IP 地址也可以填写域名地址，现不支持多 IP 。如果是域名地址，那么 CDN 在回源时会对该域名地址进行 DNS 解析，然后通过解析出来的 IP 地址再进行访问，因此若解析失败也会导致无法正常回源。

### HTTP-FLV  
> 管理后台：服务 > 基础配置 > HTTP-FLV 输出  
> 源站类型：全部

通过管理后台，可以配置任一播放域名作为 HTTP-FLV 的输出域名。

当推流 url 为 rtmp://push.com/app/stream 时，对域名播放域名开启 HTTP-FLV 输出配置后， 可以通过 http://play.com/app/stream.flv 进行播放。
### HLS 输出
> 管理后台：服务 > 基础配置 > HLS 输出  
> 源站类型：全部

通过管理后台，可以配置任一播放域名作为 HLS 的输出域名。

开启该配置后，可通过 http://play.com/app/stream.m3u8 对 rtmp://push.com/app/stream 的推流进行播放。

### 推流防盗链 
Token 防盗链可以对推流和播放的请求进行校验，可设置签名过期时间来控制流的访问时限。
 
一个最简的推流地址格式如下：
```
rtmp://push.com/live/stream?domain={domain}&token={token}&expired_ts={expired_ts}
```
参数说明：  
domain：域名，开启 token 防盗链的域名。  
expire_ts：有效期，客户自由填写，超过有效期将停止服务。  
token：需计算得出，计算公式：token = MD5(domain + expire_ts + secret)。  

示例：
比如推流 URL 为 rtmp://push.com/live/stream，则 domain = push.com，  
假设其 expired_ts = 1465244082，secret = a1b2c3d4e53gxwb07  
那么 token = MD5(push.com/live/stream1465244082a1b2c3d4e53gxwb07)  
> 注：计算公式中的 secret，由业务系统提供并告知客户，作为客户的唯一标识，客户需妥善保管，谨防外泄。  
> 推流仅支持 token 防盗链。  

### 拉流防盗链   
HTTP 协议拉流防盗链规则同文件加速，详细规则见 CDN 防盗链规范：http://docs.upyun.com/cdn/feature/#_1。

## 增值服务

### 录播
> 需提供的配置信息：需要录制的拉流 URL。  
> 录制方式：触发或定时录制。  

录播的主要作用是将推流内容录制成文件，最终用于点播。

现默认录制成 mp4 格式文件，上传到又拍云存储，客户可以自定义上传到哪个存储服务（暂不支持，接口未开放，现在是默认云存储为 live-recorder）。

又拍录制系统会自动将录制下来的内容上传到又拍云存储后，可以根据云存储获取目录文件列表 来获取相关录制文件列表。
如果对录制文件格式有其他要求，可在又拍云处理中心对其进行相关处理，比如格式处理，视频拼接，详细请见[云处理文档](http://docs.upyun.com/cloud/)。

如果需要将 rtmp://play.com/live/stream 这条流进行录制，录制后文件具体路径为：  
```
live-recorder.b0.upaiyun.com/play.com/live/stream/recorder20160604163702.mp4  

其中 live-recorder 为存储空间，recorder20160604163702.mp4 为具体的录制文件名，  
recorder 为标识字符，20160604163702 为录制完成时间，mp4 为文件类型。
```
录制系统会将录制文件默认保存在该空间以这条流 URL 为路径的目录下，  
即 live-recorder.b0.upaiyun.com/play.com/live/stream/，使用又拍 cdn，直接可通过   http://client.com/play.com/live/stream/recorder20160604163702.mp4 来访问，client.com 为客户点播域名，需绑定在录制文件所有的云存储空间，该过程即对存储内容进行点播。

录制支持触发录制与定时录制两种方式。

#### 触发录制
指定配置某一条流为触发录制的流，则在这条流推流到又拍 CDN 的时候，又拍录制系统就对其开始录制，当这推流断开后停止录制，再推流后又继续开始录，以此循环。推流断开时，录制文件自动停止录制，下次再推流后，录制的文件名将与前一文件名不一样，这样同一条流会生成多个不同文件，并以文件名最后的时间表示其先后顺序。

#### 定时录制
指定配置某一条流从几点开始录制，并到几点结束，不管在这时间段内推流断开几次，录制系统将在相应时间段内对其推流的所有流内容进行录制在同一文件名下，定时任务只生成一个文件。

#### 回调
> 需提供接收回调的地址（建议为 URL）。

录制文件所在的路径以 post 请求返回给客户，具体的 json 格式为
```
{"timestamp": "2016-06-04 16:38:45",  
 "path": ["http://live-recorder.b0.upaiyun.com/play.com/live/stream/recorder20160604163702.mp4"]}
```
其中，timestamp 为发送 json 回调任务时间，path 为录制文件具体路径。

### 转码
> 需提供需要转码的流、需要匹配的后缀及转码模板。

支持音视频流实时转码处理，通过转码模版可配置编码标准、分辨率、码率、输出流类型等流处理参数。
默认支持使用又拍云直播服务的 RTMP，HTTP-FLV 和 HLS 协议的流转码支持 12 种转码模板和客户自定义转码配置，详细模板信息：http://docs.upyun.com/cloud/attachment/  支持自定义转码后缀，分隔符支持中划线（-）、下划线（_）和感叹号（!）。

支持触发式转码，需提前配置需要转码的流地址以及转码的触发后缀，如 需要转码的原始流为：http://play.com/live/stream 触发转码的后缀匹配为 -small，对应的转码模板为 540p(16:9) 当有用户请求 http://play.com/live/stream-small  时触发转码，当最后一个请求该转码流的用户断开连接后，停止转码。

### 禁播
互动直播业务特性为：直播视频内容由主播实时推送至服务器,若主播传播非法内容,则影响巨大。因此厂商需要对直播视频内容进行实时监控,当出现非法内容时,及时禁止非法内容的传播。
秒级禁播则是针对该业务场景而研发的功能,由又拍提供一个 API。 接口给客户,当客户发现某直播视频内容非法时,实时断开主播推流,从而阻止非法内容的传播。

接口支持立即禁播，恢复禁播，禁播一段时间。

> 如需转码、录制和禁播等功能请联系售前，如有任何问题、意见或建议，请咨询客服人员。    
  客服热线：0571-81020203（售前）、0571-81020204（客服）  
  客服 QQ：200576786  
  技术支持：support@upyun.com  
  建议反馈：feedback@upyun.com  

## IOS 推流 SDK
### 系统说明
* 支持 iOS 8 及以上系统版本   
* 支持 ARMv7，ARM64，x86_64 架构

###  功能说明
 * 采集模块源码开放，音视频可以自由配置  
 * 支持硬件编码  
 * 多码率可选  
 * 支持 H.264 视频编码  
 * 支持 AAC 音频编码  
 * 支持前后摄像头  
 * 支持自动对焦  
 * 支持手动调整对焦点  
 * 支持闪光灯操作  
 * 支持多分辨率编码   
 * 支持构造带安全授权凭证的 RTMP 推流地址  
 * 支持 ARMv7, ARM64, x86_64 架构  
 * 支持 RTMP 协议直播推流    
 * 支持推流时可变码率  
 * 提供发送 buffer      
 * 支持视频 Orientation 操作   
 * 支持弱网丢帧策略   
 * 支持后台音频推流  
 * 支持水印功能  
 * 支持美颜功能  
  
项目地址：https://github.com/upyun/ios-live-sdk  
反馈及建议：livesdk@upai.com

## 安卓推流 SDK
###  系统说明
* Android 4.1(API 16) 以上
* 支持ARMv5，ARMv7,ARMv8版本

###  功能说明
* 支持 H.264 和 AAC 硬编
* 硬编支持 Android Min API 18（Android 4.3）及其以上版本
* 支持 RTMP 封包及推流
* 支持 RTMP 推流自适应网络质量动态切换码率或自定义策略
* 支持纯音频推流，以及后台运行
* 支持动态更改 Encoding Orientation

项目地址：https://github.com/upyun/android-push-sdk  
反馈及建议：livesdk@upai.com

## IOS 播放器 SDK
### 系统说明
* 支持 iOS 8 及以上系统版本   
* 支持 ARMv7，ARM64，x86_64 架构  

### 功能说明
* 支持 RTMP 、HLS 和 HTTP-FLV 协议的直播流媒体播放
* 高可定制：可自定义尺寸，按钮，进度条，全屏，旋转等 UI 属性
* 支持单音频播放
* 支持 RTMP 直播首屏秒开
* RTMP 直播累积延迟消除技术
* 支持 speex 解码
* 音画同步调整
* 一屏多画
* 点播支持
* 支持 seek 功能
* 视频缓冲设置，缓冲进度
* 支持预连接，后播放功能
* 集成亮度调整和音量调整
* 支持静音播放
* 支持播放器状态回调
* 支持流信息，播放器信息察看（Dashboard）
* 支持播放本地视频文件
* 集成播放质量监控  
  
项目地址：https://github.com/upyun/ios-live-sdk  
反馈及建议：livesdk@upai.com

## 安卓播放器 SDK
###  系统说明
* Android 2.3 (API 9) 及其以上
* 支持 ARMv5，ARMv7,ARMv8 版本

###  功能说明
* 支持 RTMP 、HLS 和 HTTP-FLV 协议的直播流媒体播放
* 支持常见的音视频文件播放（MP4、M4A、flv 等）
* 支持 MediaCodec 硬件解码
* 提供播放器核心类 PLMediaPlayer
* 提供 PLVideoView 控件
* 支持播放器音量设置，可实现静音功能
* 支持纯音频播放
* 支持直播累积延时优化
* 支持 ARM，ARMv7a，ARM64v8a，X86 主流芯片体系架构
* 可高度定制化的 MediaController
* 高可定制：可自定义尺寸，按钮，进度条，全屏，旋转等 UI 属性
* 单音频播放
* RTMP 直播首屏秒开支持
* RTMP 直播累积延迟消除技术
* 支持 speex 解码
* 音画同步调整
* 支持点播
* 支持 seek 功能
* 视频缓冲设置，缓冲进度
* 支持预连接，后播放功能
* 集成亮度调整和音量调整
* 支持静音播放
* 支持播放器状态回调
* 支持流信息，播放器信息察看（Dashboard）
* 支持播放本地视频文件


项目地址：https://github.com/upyun/android-player-sdk  
反馈及建议：livesdk@upai.com

