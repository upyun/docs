## 快速入门

又拍云处理（音视频处理）基于云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建[云存储服务](/api/quick_start)。

收费方面，见[价格](https://www.upyun.com/price.html)。

---------

## 开发者指南

<a name="submit_task"></a>
### 提交处理任务

** 请求方法 **

对已经存在云存储中的音视频文件，以 `POST` 方法向 `http://p0.api.upyun.com/pretreatment/` 提交处理任务，响应中返回任务 `task_id`。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "accept=json"
    -d "service=<service>" \
    -d "notify_url=<notify_url>" \
    -d "source=<音视频文件相对路径>" \
    -d "tasks=<base64 编码后的任务字符串>" \
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。


** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 音/视频文件所在的服务名         		|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#notify_url) |
| source            | string       	| 是   	| 原始音/视频文件路径                     	|
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#tasks)  	|
| accept            | string       	| 是   	| 回调信息的格式，值为`json`  					|

<a name="tasks"></a>
** tasks 参数说明 **

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。** 任务参数见[功能](#function) **。

```
[
	{
        "type": "video",                        // 视频转码
        "avopts": "/s/240p(4:3)/as/1/r/30",     // 参数
        "return_info": true,                    // 返回元数据
        "save_as": "/a/b.mp4"                   // 保存路径
    },
    {
        "type": "vconcat",                                     	// 视频拼接
        "avopts": "/i/L2EvYi9jLm1wNA==/i/LzEvMi8zLm1wNA==",    	// 参数
        "save_as": "/concat/a.mp4"								// 保存路径
    },
    …
]
```

2\. 把 JSON 字符串 Base64 编码，得到 `tasks`。
```
W3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ==
```

** 响应信息 **

- 任务提交成功：返回 `200`，响应体是一组按任务提交先后顺序排序的 `task_id`。例如：

```
[
	"35f0148d414a688a275bf915ba7cebb2",
	"98adbaa52b2f63d6d7f327a0ff223348",
	"c3103189fa906a5354d29bd807e8dc51",
	…
]
```

- 任务提交失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

---------

<a name="notify_url"></a>
### 回调通知 

任务处理完成后，向 `notify_url` 发送 `HTTP POST` 请求，请求体是回调信息。

```
curl -X POST \
    <notify_url> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
	# 其他参数...
```

** 回调信息 **

回调信息是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| service   		| string    | 音/视频文件所在的服务名                                     	|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)   	|
| path          	| array     | 输出文件保存路径                                            |
| description   	| string    | 处理结果描述                                             	|
| task_id       	| string    | 任务对应的 `task_id`                                    	|
| info          	| json      | 输出文件的元数据，仅当 `return_info` 为 `true` 时，有效 		|
| signature（旧）	| string    | 回调通知的签名，[回调签名（旧）](#old_notify_url)的参数      						|
| timestamp（旧）	| integer   | 服务端发送回调时的时间戳，[回调签名（旧）](#old_notify_url)的参数 					|

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)，它提供给客户端，用于验证回调通知的合法性。

---------

<a name="async_upload_process"></a>
### 上传预处理

在上传音视频文件时，对上传的音视频进行处理，生成一个或多个新的文件，同时保存上传的音视频到又拍云存储。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多允许包含 10 个音视频处理任务。** 任务参数见[功能](#function) **。

** apps 参数结构 **

```
apps = [
    {                                               // 异步音视频处理任务
        "name": "naga",                             // 异步任务名称，必填。naga 表示异步音视频处理服务
        "type": "<type>",                           // 视频转码，必填
        "avopts": "<avopts>",                       // 处理参数，必填
        "return_info": true/false,                  // 是否返回元数据，选填
        "save_as": "<save_as>",                     // 结果音频/视频保存路径，选填
        "notify_url": "<notify_url>"                // 回调地址，不填时使用上传参数中的 notify_url
    },
	{ 												// 举例			
	    "name": "naga",                         
	    "type": "video",                        
	    "avopts": "/s/240p(4:3)/as/1/r/30",     
	    "return_info": true,                    
	    "save_as": "/a/b.mp4",                  
	},
    ......
]
```

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见 「音视频处理」 > 「[回调通知](#notify_url)」。

---------

### 进度查询

** 请求方法 **

通过 `task_id`，以 `GET` 方法向 `http://p0.api.upyun.com/status/` 提交进度查询任务。

```
curl http://p0.api.upyun.com/status?service=<service>&task_ids=<task_id1>,<task_id2>,<task_id3> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 查询参数 **

| 参数       		| 类型      	| 必选  	| 说明                                   |
|-------------------|-----------|-------|------------------------------------------|
| service       	| string    | 是   	| 音/视频文件所在的服务名        		    	|
| task_ids          | string    | 是    	| 任务集，多个 `task_id` 使用 `,` 连接，最多 20 个 `task_id`  	  |

** 响应信息 **

- 查询成功：返回 `200`，响应体是 `tasks`。`tasks` 是 JSON 格式，存放各个任务（`task_id`）的进度。例如：

```
{
  "tasks": {
      "35f0148d414a688a275bf915ba7cebb2": 100,
      "98adbaa52b2f63d6d7f327a0ff223348": 20,
      "c3103189fa906a5354d29bd807e8dc51": null,
      …
  }
}
```

`20` 表示任务进度百分比，`100` 表示任务处理完成，`-1` 表示任务处理失败，`null` 表示任务尚未开始。如果长时间是 `null`，请反馈给[售后]((https://www.upyun.com/about_contact.html))或您的商务经理。

- 查询失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

---------

### 结果查询

** 请求方法 **

通过 `task_id`，以 `GET` 方法向 `http://p0.api.upyun.com/result/` 提交处理结果查询任务。

```
curl http://p0.api.upyun.com/result?service=<service>&task_ids=<task_id1>,<task_id2>,<task_id3> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 查询参数 **

| 参数       		| 类型      	| 必选  	| 说明                                   |
|-------------------|-----------|-------|------------------------------------------|
| service       	| string    | 是   	| 音/视频文件所在的服务名        		    	|
| task_ids          | string    | 是    	| 任务集，多个 `task_id` 使用 `,` 连接，最多 20 个 `task_id`  	  |

** 响应信息 **

- 查询成功：返回 `200`，响应体是任务的[回调信息](#notify_url)。回调信息例如：

```
{
    "9d9c32b63a1034834e77672c6f51f661": {
        "path": ["/v2.mp4"],
        "signature": "4042c1f07f546d28",
        "status_code": 200,
        "service": "service",
        "description": "OK",
        "task_id": "9d9c32b63a1034834e77672c6f51f661",
        "timestamp": 1472010905
    },
    "3438a54b4991e8d4a46a003bc15e9867": {
        "path": ["/v2.mp4"],
        "signature": "1c49be9b672394ff",
        "status_code": 200,
        "service": "service",
        "description": "OK",
        "task_id": "3438a54b4991e8d4a46a003bc15e9867",
        "timestamp": 1472010684
    }
    …
}
```

- 查询失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

---------

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        			|
|---------------|-----------------------|
| 200         	| 处理成功    			|
| 400         	| 参数错误    			|
| 401         	| 参数错误    			|
| 404         	| 文件不存在    			|
| 5xx         	| 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/about_contact.html)或您的商务经理 |

---------

### 支持格式

| 类型      		| 输入格式       														| 输出格式       	|
|---------------|-----------------------------------------------------------------------|-------------------|
| 视频容器格式 	| AVI、MP4、FLV、MOV、3GP、ASF、WMV、MPG、F4V、M4V、MKV、VOB（SVCD/DVD）等 	| MP4、FLV、M3U8(TS) 等| 
| 音频容器格式 	| MP3、OGG、M4A 等 														| MP4、MP3、OGG 等	|
| 视频编码格式 	| H.264/AVC 、H.263、H.263+、MPEG-2、MPEG-4、VP8、VP9、Quicktime、RealVideo、Windows Media Video 等 | H.264/AVC、VP8、H.265/HEVC、VP9 等|
| 音频编码格式 	| MP1、MP2、MP3、AAC、AC-3、Vorbis、PCM、RealAudio、Windows Media Audio 等	| AAC、MP3 等		|

更多的格式请查阅 [ffmpeg 支持格式列表](http://ffmpeg.org/general.html#Supported-File-Formats_002c-Codecs-or-Features)。

---------

<a name="preset_mode"></a>
### 预置模板

同等清晰度下，建议优先使用 16:9 的宽高比。特别地，支持 4K 视频处理。

| 清晰度     	| 参数名         	| 分辨率            	| 码率     		|
|---------------|-------------------|-------------------|---------------|
| 全高清      	| 1080p(16:9)     	| 1920x1080,16:9    | 2560Kbps    	|
| 高清        	| 720p(16:9)      	| 1280x720,16:9     | 1152kbps    	|
| 高清        	| 720p(4:3)       	| 960x720,4:3     	| 1152kbps    	|
| 标清        	| 540p(16:9)      	| 960x540,16:9      | 768kbps    	|
| 标清        	| 480p(16:9)      	| 854x480,16:9      | 512kbps    	|
| 标清        	| 480p(4:3)       	| 640x480,4:3       | 512kbps    	|
| 标清        	| 480p(3:2)       	| 720x480,3:2       | 512kbps    	|
| 流畅（低清）  	| 360p(16:9)       	| 640x360,16:9      | 384kbps    	|
| 流畅（低清）  	| 360p(4:3)        	| 480x360,4:3       | 384kbps    	|
| 流畅（低清）  	| 240p(4:3)        	| 320x240,4:3       | 256kbps    	|

---------

### 签名认证（旧）

详见「音视频处理（旧）」> 「[授权认证](/cloud/av_pretreatment/#_2)」。

<a name="old_notify_url"></a>
** 回调签名（旧） **

signature 的计算方法：

```
md5(<operator_name><md5_operator_password><task_id><timestamp>), 并取中间 16 位。
```

<!--
请求的授权认证是通过在 HTTP 请求头中添加 `Authorization` 进行验证：
```
Authorization:　UPYUN　<operator>:<signature>
```

其中，`operator` 是操作员名称，`signature` 是根据参数计算出来的签名。

`signature` 参数通过下面的方式获得：

1\. 将参数按照 **[参数名称的字典序](https://zh.wikipedia.org/wiki/%E5%AD%97%E5%85%B8%E5%BA%8F)** 排序后，将参数键值对连接成一个字符串。

2\. 将第一步生成的字符串按照下面的规则进行连接，获取待签名字符串：

```
<operator_name><signature_string><md5_operator_password>
```

> 其中，`operator_name` 为用于认证授权的操作员名，`signature_string` 为第一步生成的字符串，
> `md5_operator_password` 为对操作员密码进行 md5 计算之后得到的字符串。

3\. 对第二步生成的字符串做 md5 计算，得到最终的 `signature` 值。

> 特别地，参数字符串都必须使用 `utf-8` 编码处理。

以下是一个计算 `signature` 的例子：

假设接口提交如下参数：

```
bucket_name: "demo"
tasks: "W3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ=="
notify_url: "http://www.example.com/notify/"
source: "/video/upyun.mp4"
accept: "json"
```

用于授权验证的操作员名为 `upyun-operator` ，操作员密码为 `onepiece`。

首先根据参数名称的字典序进行排序：

```
accept: "json"
bucket_name: "demo"
notify_url: "http://www.example.com/notify/"
source: "/video/upyun.mp4"
tasks: "W3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ=="
```

将排序后的参数键值对连接成一个字符串，得到：

```
acceptjsonbucket_namedemonotify_urlhttp://www.example.com/notify/source/35000_38720_mp4.tstasksW3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ==
```

连接操作员名和进行 md5 计算之后的操作员密码：

```
upyun-operatoracceptjsonbucket_namedemonotify_urlhttp://www.example.com/notify/source/35000_38720_mp4.tstasksW3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ==e81502a921e78c4ddb017a555586664c
```

将得到的字符串进行 md5 计算，得到最终的 `signature` 值为 `c6f1aa91e24241debe80858aec299862`。
-->

---------

<a name="function"></a>
## 功能

### 视频转码

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`video`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」|
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false` 		|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始视频所在目录+系统随机生成的文件名   |

** avopts 参数说明 **

| 参数              			| 类型   	| 必选	| 说明                                    			|
|---------------------------|-----------|-------|---------------------------------------------------|
| `/f/<format>`             | string    | 否		| 视频格式。输出格式通常根据输入文件格式和输出文件后缀名进行自动猜测，所以在大多数情况下不需要填写此选项           	|
| `/vb/<bitrate>`           | integer   | 否		| 视频比特率，单位 kbps，默认按照视频原始比特率处理       	|
| `/s/<scale>`              | string    | 否		| 视频分辨率，默认按照原始分辨率处理。见「注」				|
| `/as/<auto_scale>`        | boolean   | 否		| 是否根据分辨率自动调整视频的比例，默认 `false`。仅当传递了 `s` 参数时有效    |
| `/ar/<audio_sample_rate>` | integer   | 否		| 音频采样率，单位 Hz，默认按照原始采样率处理。可选值：`44100`、`48000`、`32000`、`22050`、`24000`、`16000`、`0`   |
| `/r/<frame_rate>`         | integer   | 否		| 视频帧率，默认按照原始帧率处理。推荐值：`25`、`30`    					|
| `/sp/<rotate>`            | string    | 否		| 旋转角度，默认按照原始视频角度处理。可选值：`auto（自动扶正）`、`90`、`180`、`270`        |
| `/sm/<map_metadata>`      | boolean   | 否		| 是否保留视频元数据，默认 `true`  
| `/acodec/<audio_codec>`   | string    | 否		| 设置音频编码器，默认按照音频原始编码器处理。可选值：`libmp3lame`、`libfdk_aac`、`copy`       |
| `/vcodec/<video_codec>`   | string    | 否		| 设置视频编码器，默认按照视频原始编码器处理。可选值：`libx264`、`libtheora`、`libx265`、`libvpx-vp9`、`libvpx`、`copy`  |
| `/an/<disable_audio>`     | boolean   | 否		| 是否禁掉音频，默认 `false`                      		|
| `/vn/<disable_video>`     | boolean   | 否		| 是否禁掉视频，默认 `false`                         	|
| `/su/<accelerate_factor>` | float     | 否		| 视频加速倍数，默认 `1.0`。取值范围 `[1.0，10.0]`       	|

** 注 **

- `s` 参数有两种设置方法：1）使用[预置模板](#preset_mode)，值是预置模板的参数名，如 `720p(16:9)`；2）自定义，值是 `宽x高`，如 `1280x720`。

---------

### 视频切片

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`hls`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」 |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`       	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始视频所在目录+系统随机生成的文件名。见「注」   |

** avopts 参数说明 **

| 参数              	| 类型   	| 必选	| 说明                                    			|
|-------------------|-----------|-------|---------------------------------------------------|
| `/ht/<hls_time>`  | string    | 否		| 每片时长，单位 s（秒），默认 `10`              		|
| `/vb/<bitrate>`   | integer   | 否		| 视频比特率，单位 kbps，默认按照视频原始比特率处理      	|
| `/s/<scale>`      | string    | 否		| 视频分辨率，默认按照原始分辨率处理。见「注」      		|
| `/as/<auto_scale>`| boolean   | 否		| 是否根据分辨率自动调整视频的比例，默认 `false`。仅当传递了 `s` 参数时有效        |
| `/r/<frame_rate>` | integer   | 否		| 视频帧率，默认按照原始帧率处理。推荐值：`25`、`30`      |

** 注 **

- `s` 参数有两种设置方法：1）使用[预置模板](#preset_mode)，值是预置模板的参数名，如 `720p(16:9)`；2）自定义，值是「宽x高」，如 `1280x720`。
- 自定义 `save_as` 参数时，必须以 `.m3u8` 后缀结尾。

---------

### 视频水印

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`video`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」 |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`     	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始视频所在目录+系统随机生成的文件名   |

** avopts 参数说明 **

| 参数              			| 类型   	| 必选	| 说明                                    			|
|---------------------------|-----------|-------|---------------------------------------------------|
| `/wmImg/<watermark_img>`  | string    | 是		| 水印图片相对路径，需要安全的 Base64 编码。图片格式支持 `png` 和 `webp`  |
| `/wmGravity/<wmGravity>`  | string    | 否		| 水印图片放置方位，默认 `northeast`。见「注」                              |
| `/wmDx/<watermark_dx>`    | integer   | 否		| 水印图片横偏移量，单位 px，当 `wmGravity` 为 `northeast` 时，默认 `-20` <br /> 横偏移量取值正负的依据：往 `east` 方向偏移，为正；往 `west` 方向偏移，为负       |
| `/wmDy/<watermark_dy>`    | integer   | 否		| 水印图片纵偏移量，单位 px，当 `wmGravity` 为 `northeast` 时，默认 `15` <br /> 纵偏移量取值正负的依据：往 `south` 方向偏移，为正；往 `north` 方向偏移，为负      |

** 注 **

- 9 个方位：
```
northwest     |     north      |     northeast
              |                |
              |                |
--------------+----------------+--------------
              |                |
west          |     center     |          east
              |                |
--------------+----------------+--------------
              |                |
              |                |
southwest     |     south      |     southeast
```

---------

### 视频截图

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`thumbnail`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」  |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`      	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始视频所在目录+系统随机生成的文件名。见「注」   |

** avopts 参数说明 **

| 参数              		| 类型   	| 必选	| 说明                                    			|
|-----------------------|-----------|-------|---------------------------------------------------|
| `/o/<thumb_single>` 	| boolean   | 是		| 是否单张截图，单张截图为 `true`，多张截图为 `false`   	|
| `/n/<thumb_amount>` 	| integer   | 是		| 截图数量。当 `o` 参数为 `false` 时，有效；当 `o` 参数为 `true` 时，无效     |
| `/ss/<thumb_start>` 	| string    | 否		| 截图开始时间，格式为 `HH:MM:SS`，默认 `00:00:00`                  |
| `/es/<thumb_end>`   	| string    | 否		| 截图结束时间，格式为  `HH:MM:SS`，默认视频结束时间。当 `o` 参数为 `true` 时，无效     |
| `/s/<thumb_scale>`  	| string    | 否		| 截图尺寸，格式为 `宽:高`，默认视频原始尺寸                   |
| `/f/<thumb_format>` 	| string    | 否		| 截图输出格式，默认 `jpg`。图片格式支持 `png` 和 `jpg`。见「注」  |

** 注 **

- 多张截图时，自定义 `save_as` 参数时只需指定一个保存路径，具体每张图片保存路径根据规则进行类推，例如 `save_as` 为 `/path/to/img.png`，输出文件保存路径第一张为 `/path/to/img1.png`，第二张为 `/path/to/img2.png`......以此类推。
- 自定义 `f` 和 `save_as` 参数时，使用 `f` 参数指定的格式作为输出格式；仅自定义 `save_as` 参数时，使用 `save_as` 参数指定的后缀名作为输出格式；两者都不自定义时，使用 `f` 参数的默认值 `jpg` 作为输出格式。

---------

### 视频剪辑

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`video`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」  |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`      	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始视频所在目录+系统随机生成的文件名   |

** avopts 参数说明 **

| 参数              	| 类型   	| 必选	| 说明                                    			|
|-------------------|-----------|-------|---------------------------------------------------|
| `/ss/<start_time>`| string    | 否		| 剪辑开始时间，格式为 `HH:MM:SS`，默认视频开始时间   	|
| `/es/<end_time>`  | string    | 否		| 剪辑结束时间，格式为 `HH:MM:SS`，默认视频结束时间   	|

---------

### 视频拼接

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`vconcat`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」 |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`  	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始视频所在目录+系统随机生成的文件名   |

** avopts 参数说明 **

| 参数              	| 类型   	| 必选	| 说明                                    			|
|-------------------|-----------|-------|---------------------------------------------------|
| `/i/<video>`      | string    | 是		| 需要拼接视频文件的相对路径，需要安全的 Base64 编码 <br /> 多个需要拼接视频按 `/i/<video>/i/<video>/...` 方式进行连接。见「注」 |
| `/codec/<codec>`  | string    | 否		| 设置视频编码器，默认不需要设置。可选值：`copy`。见「注」 |

** 注 **

- 视频拼接顺序：需要拼接视频按 `i` 出现顺序，依次拼接在[原始视频（`source`）](#submit_task)后面。
- 如果 `save_as` 参数指定格式跟[原始视频（`source`）](#submit_task)格式、拼接视频格式不一致，系统会自动转码原始视频、拼接视频成  `save_as`  参数指定格式；如果 `save_as` 参数未指定，拼接视频跟原始视频格式不一致，系统会自动转码拼接视频成原始视频格式。
- 如果拼接视频和原始视频的编码一致，可以设置 `codec` 参数为 `copy`，不对拼接视频进行编解码，提高拼接速度。
- 各个拼接视频的分辨率（画面宽高）必须跟[原始视频（`source`）](#submit_task)保持一致，否则会拼接失败。

---------

### 音频转码

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`audio`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」 |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`    	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始音频所在目录+系统随机生成的文件名	   |

** avopts 参数说明 **

| 参数              		| 类型   	| 必选	| 说明                                    			|
|-----------------------|-----------|-------|---------------------------------------------------|
| `/f/<format>`         | string    | 否		| 音频格式，默认按照原始音频后缀名作为输出格式              |
| `/ac/<audio_channel>` | integer   | 否		| 声道，默认 `2`                                         |
| `/ab/<audio_bitrate>` | integer   | 否		| 比特率，单位 kbps，默认按照音频原始比特率处理        |
| `/vbr/<audio_vbr>`    | integer   | 否		| Variable bitrate, 默认按照音频原始 VBR 处理。取值范围 `[0-9]`   |
| `/sm/<map_metadata>`  | boolean   | 否		| 是否保留音频 metadata，默认 `true`                  |

---------

### 音频剪辑

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`audio`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」 |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`      	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始音频所在目录+系统随机生成的文件名   |

** avopts 参数说明 **

| 参数              	| 类型   	| 必选	| 说明                                    			|
|-------------------|-----------|-------|---------------------------------------------------|
| `/ss/<start_time>`| string    | 否		| 剪辑开始时间，格式为 `HH:MM:SS`，默认视频开始时间   |
| `/es/<end_time>`  | string    | 否		| 剪辑结束时间，格式为 `HH:MM:SS`，默认视频结束时间   |

---------

### 音频拼接

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`aconcat`                                 			|
| `avopts`          | string    | 是		| 视频转码参数，格式为 `/key1/value1/key2/value2/...`，见「avopts 参数说明」 |
| `return_info`		| boolean   | 否		| 回调信息是否包含输出文件的元数据，元数据格式为 JSON，默认 `false`  	|
| `save_as`         | string    | 否		| 输出文件保存路径，默认原始音频所在目录+系统随机生成的文件名   |

** avopts 参数说明 **

| 参数              	| 类型   	| 必选	| 说明                                    			|
|-------------------|-----------|-------|---------------------------------------------------|
| `/i/<audio>`      | string    | 是		| 需要拼接音频文件的相对路径，需要安全的 Base64 编码  <br /> 多个需要拼接音频文件按 `/i/<audio>/i/<audio>/...` 方式进行连接   |

** 注 **

- 音频拼接顺序：需要拼接音频按 `i` 出现顺序，依次拼接在[原始音频（`source`）](#submit_task)后面。
- 如果 `save_as` 参数指定格式跟[原始音频（`source`）](#submit_task)格式、拼接音频格式不一致，系统会自动转码原始音频、拼接音频成 `save_as` 参数指定格式；如果 `save_as` 参数未指定，拼接音频跟原始音频格式不一致，系统会自动转码拼接音频成原始音频格式。

---------

### 元数据获取

获取音/视频文件的元信息。

| 参数              	| 类型   	| 必选  	| 说明                                    					|
|-------------------|-----------|-------|-----------------------------------------------------------|
| `type`            | string    | 是		| 固定值，`probe`                                 			|

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
