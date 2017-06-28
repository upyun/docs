## 简介

为了解决异步音频处理时间长，又拍云发布了的同步音频处理，可以实现秒级音频转码。它在上传时使用，把上传的音频处理成指定规格的音频再存储。

本类功能需要[收费](https://www.upyun.com/products/process#section-pricing)，在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建[云存储服务](/api/quick_start/)。

相关阅读：[异步音视频处理](/cloud/av/)，[同步视频处理](/cloud/sync_video/)。

## 音频转码

提供对音频进行转格式、转码率、转声道，通常的设置是格式 mp3、码率 44、声道 2。

### 请求

** 请求方法 **

使用 [FORM API](/api/form_api/#upload_args) 上传音频文件，在上传参数中附加参数 `x-audio-avopts` 来使用。

** 请求参数 **

| 参数       			| 类型       	| 必选  	| 说明                              	|
|-----------------------|---------------|-------|-----------------------------------|
| /ab/<audio_bitrate>   | integer		| 是  	| 音频码率，单位 kbps，默认值 44  		|
| /ac/<audio_channel>	| integer		| 是  	| 音频声道，默认值 2          		|
| /f/<audio_format>   	| string		| 是  	| 音频格式，默认值 mp3 。可选值  mp3 、ogg 	|

** 注 **

- 又拍云 CDN 默认开启了分段缓存（分段分发文件），您不需要担心音频文件大访问慢的问题。

###　响应

详见 FORM API 的[通知规则](http://docs.upyun.com/api/form_api/#notify_return)。

### 举例


** 请求示例 **

```
// FORM API 附加上传参数

x-audio-avopts: /ab/48/ac/5/f/ogg
```

** 响应示例 **

```
HTTP/1.1 200 OK
X-Request-Id: 5133947d3aa83f733da38c5630964d30
Content-Length: 153

{"sign":"6a73b2781706a62794db9c9dac179921","code":200,"file_size":23508491,"url":"\/audio.mp3","time":1494559280,"message":"ok","mimetype":"audio\/mpeg"}
```

<a name="status"></a>
## 状态码说明

| 参数       	| 类型       	|
|---------------|---------------|
| 200    		| 处理成功     	|
| 403   		| 转码失败 		|
| 405   		| 参数错误 		|
| 413    		| 转码超时	  	|
| 5xx    		| 服务端错误，请反馈给[售后](https://www.upyun.com/contact)或您的商务经理   	|

---------

如有疑问请 [联系我们](https://www.upyun.com/contact)