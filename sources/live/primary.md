## 回源配置
又拍云直播系统支持两种服务类型：又拍云源和自主源站。
### 又拍云源

又拍云源：表示流媒体内容直接推流到又拍云 CDN ，将推流与分发都进行 CDN 加速。又拍云源现仅支持 RTMP 推流。

> 管理后台：创建服务 > 直播加速 > 又拍云源

创建又拍云源，填写服务名、已备案的播放域名和推流域名，推流域名与播放域名不能共用。
可选择性填写接入点，如果填写了具体的接入点，则只有该接入点的 URL 可进行推流，其他接入点均推流失败。若没有填写具
体接入点，则任意接入点可进行推流。

> 当推流域名填写 push.com ，播放域名填写 play.com ，接入点填写 live ，如果推流 URL 为 
> rtmp://push.com/live/stream 时，其 rtmp 播放 URL 为 rtmp://play.com/live/stream  
> 其中，stream 为流名，又称流密钥，live/stream 又称为频道，同一条流推拉流域名不同，但频道名一致。  
> 注：以上域名、接入点及流名的命名仅为举例用，下同。 


### 自主源站

自主源站：流媒体内容在客户源站，又拍云通过回客户源拉流的方式对其内容进行分发。

> 管理后台：创建服务 > 直播加速 > 自主源站

自主源站需要明确回源协议是 RTMP 还是 HTTP，并填写源站 IP 或者 域名，回源 IP 现不支持多个，若回源 IP 会经常变更，建议源站填写域名形式。当回源协议是 RTMP 时，默认端口是 1935，当回源协议是 HTTP 时，默认端口为 80，支持端口自定义。因自主源站是回客户源拉流，则无推流域名，只需要设置播放域名。
>  HTTP 协议回源时，以 HTTP-FLV 形式回源，若源为 HLS 可通过文件加速系统创建服务，配置回源信息。  
>  接入点配置说明同又拍云源。

以上两种业务模式，均支持 RTMP、HTTP-FLV 和 HLS 播放，后续章节会详细介绍。

## 域名绑定

> 直播管理后台：服务 > 加速域名 > 域名绑定  
> 源站类型：全部

域名绑定仅支持已备案成功的自定义域名进行绑定，如果该域名未备案成功，则不能通过域名绑定审核。
审核结果会控制台和邮件两种方式进行通知，正常审核时间为一个工作日。

添加域名绑定，如果是又拍云源，需要绑定推流域名和播放域名，如果是自主源站，则只需要绑定播放域名。绑定完成后，需要到域名服务商的 DNS 解析管理中，将域名 CNAME 解析到又拍云内部域名。

> 当业务模式为又拍云源时，推流域名 CNAME 解析到 `<bucket>.s1.aicdn.com`，播放域名 CNAME 解析到 `<bucket>.s0.aicdn.com`。  
> 当业务模式为自主源站时，只需将播放域名 CNAME 解析到 `<bucket>.s0.aicdn.com`。  
> 注：`<>` 内的 bucket 需要替换成对应的服务名，下同。  


## HTTP-FLV  
> 管理后台：服务 > 基础配置 > HTTP-FLV 输出  
> 源站类型：全部

通过管理后台，可以配置任一播放域名作为 HTTP-FLV 的输出域名。

当推流 url 为 rtmp://push.com/live/stream 时，对域名播放域名开启 HTTP-FLV 输出配置后， 可以通过 http://play.com/live/stream.flv 进行播放。

## HLS +
> 管理后台：服务 > 基础配置 > HLS+  
> 源站类型：全部

通过管理后台，可以配置任一播放域名作为 HLS 的输出域名。HLS+ ，又称为流式 HLS 技术，是将标准的 HLS 进行流式处理，大幅度降低标准 HLS 延迟，H5 端直播兼容性更好，且具有回源量小、系统简单、排错容易、防盗链、避免 HLS 404 等优势。

开启该配置后，可通过 http://play.com/live/stream.m3u8 对 rtmp://push.com/live/stream 的推流进行播放。

> 要将 RTMP 推流进行 HLS 转协议播放，推流的视频格式仅支持 H264，音频支持 AAC，MP3。hls+ 支持纯音频输出，暂需又拍人工配置。  
>
> HLS+ 如要使用 https，需要又拍人工增加配置。

## 推流防盗链 
> 配置需提供密钥   
> 源站类型：又拍云源  

Token 防盗链可以对推流的请求进行校验，可设置 token 有效时间和截止时间来控制推流的时限。  

一个含防盗链的推流地址格式如下：  
```
rtmp://push.com/live/stream?domain={domain}&token={token}&valid_ts={valid_ts}&expired_ts={expired_ts}

token = MD5(domain/live/stream + valid_ts + expired_ts + secret)
```
参数说明：  
secret：密钥，用户与又拍约定，要求 32 位以内的数字、大小写英文字母、中划线、下划线及特殊字符 ~ ! @ # $ _ % ^ & * ( ) 组合。  
domain：域名，开启 token 防盗链的域名。  
valid_ts：有效时间，在 token 有效期内同一条 token 推流 URL 一直有效，服务器不会主动断开，valid_ts 必须是 UNIX TIME 格式，如 1472659200，表示 2016/9/1 00:00:00。  
expired_ts：截止时间，截止时间到后，服务器主动断开已建立的 token 推流连接，expired_ts 必须是 UNIX TIME 格式，如 1473004800，表示 2016/9/5 00:00:00。  

```
示例：  
推流 URL 为 rtmp://push.com/live/stream，  
则 domain = push.com，  
假设约定 secret = a1b2c3d4e53gxwb07，有效时间 valid_ts = 1472659200，截止时间 expired_ts = 1473004800，  
那么 token = MD5(push.com/live/stream14726592001473004800a1b2c3d4e53gxwb07) 
           = 8405243ad35e5d08b9621110a15b0a12    
则rtmp://push.com/live/stream?domain=push.com&token=8405243ad35e5d08b9621110a15b0a12  
&valid_ts=1472659200&expired_ts=1473004800， 该 token 防盗链在 2016/9/1 00:00:00 之前推流都有效，  
而时间到 2016/9/5 00:00:00 后，所有的推流连接都将被服务器断开。  
```
有效时间与截止时间的组合能帮助用户简单实现复杂应用场景，设置有效时间，可以让有效时间来控制本次生成的 token 防盗链的可用时长，设置截止时间，可以控制服务器在截止时间到后断开客户端推流连接，便于用户细分其不同客户群，进行相关权限控制。  
有效时间与截止时间可同时使用，也可二选一。  
```
当只使用有效时间时，到有效时间后，该 token 失效，但服务器不会主动断开建立的客户端 token 连接。  

rtmp://push.com/live/stream?domain={domain}&token={token}&valid_ts={valid_ts}  

token = MD5(domain/live/stream + valid_ts + secret)  
```

```
当只使用截止时间时，到截止时间后，服务器主动断开已有的客户端 token 连接，在截止时间前，该 token 推流均可用。  

rtmp://push.com/live/stream?domain={domain}&token={token}&expired_ts={expired_ts} 

token = MD5(domain/live/stream + expired_ts + secret)  
```

> 注：计算公式中的 secret，客户需妥善保管，谨防外泄。  
> 注：MD5 后计算出的 token 值是 32 位的，必须小写。  
> 推流暂仅支持 token 防盗链。 

关于 token 的推流，您可以参考[这里](https://github.com/monkey-wenjun/live_push_token/)的演示代码。

## 拉防盗链   
拉流防盗链只针对播放域名，HTTP 协议拉流防盗链规则同文件加速，包括 IP 禁用、地区访问限制、回源鉴权、Token 防盗链、域名防盗链等。详细规则见文件加速[ 防盗链](http://docs.upyun.com/cdn/feature/#_1)。 

> 拉流暂仅支持 HTTP-FLV 和 HLS 防盗链。

