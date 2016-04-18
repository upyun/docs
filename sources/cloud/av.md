## 10分钟了解

又拍云提供基于云的异步音视频处理，通过 API/SDK 使用，功能丰富（支持音视频转码、音视频切片、视频水印、视频截图、音视频剪辑、元数据获取等）。

下面来看看怎么使用。

### 访问异步音视频处理

以`POST`方式向指定地址 `http://p0.api.upyun.com/pretreatment/` 提交处理请求，系统接受请求后立即返回任务 `task_id`，处理完成后再回调通知用户。用户可以通过 `task_id` 查询任务处理进度、处理结果。

请求参数

|         参数       |    类型            |   说明                            |
|-------------------|--------------------|----------------------------------|
| bucket_name       | string             | 文件所在服务名称（空间名称）          |
| notify_url        | string             | 回调通知地址                       |
| source            | string             | 待处理文件路径                     |
| tasks             | string             | 处理任务信息，详见下                |
| accept            | string             | 回调通知内容的MIME类型，取值为：json  |


`tasks`（处理任务信息）生成： （如果使用 SDK 不需要关注 tasks 的生成逻辑）

1、处理任务组装。一次最多可以提交10个处理任务。

```
[
	{
	type:’video’,	//视频转码
	avopts: /s/240p(4:3)/as/1/r/30,	//处理任务，通过预置模板设置
         return_info:true,	//返回元数据
         save_as:/a/b.mp4,	//保存路径
	…
	},
  	{
	type:’vconcat’,	//视频拼接
	avopts:/i/L2EvYi9jLm1wNA==/i/LzEvMi8zLm1wNA==,	//拼接视频列表
	save_as:/concat/a.mp4
	…
	},
	…
]
```

2、把组装好的参数转换为 JSON 字符串；

3、对 JSON 字符串进行 base64 编码处理。

### 授权认证

（如果使用 SDK 不需要关注授权认证逻辑）

请求的授权认证是通过在 `HTTP Request Header` 中添加 `Authorization` 进行验证：

`Authorization:　UPYUN　<operator>:<signature>`

其中，`operator` 是操作员名称，`signature` 是根据参数计算出来的签名

`signature` 计算逻辑：

1、将参数按照参数名称的字典顺序排序；

```
bucket_name: 'imtester'
tasks: 'W3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d'
notify_url: 'http://www.example.com/notify/'
source: '/video/20130514_190031.mp4'
    |        |
    |        |
    |        |
    v        v
bucket_name: 'imtester'
notify_url: 'http://www.example.com/notify/'
source: '/video/20130514_190031.mp4'
tasks: 'W3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d'
```

2、将排序好的参数键值对拼接成一个字符串；

```
bucket_nameimtesternotify_urlhttp://www.example.com/notify/source/video/20130514_190031.mp4tasksW3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d
```

3、将第二步生成字符按下面顺序连接；

```
连接顺序：<operator_name><signature_string><md5_operator_password>
假设操作员名为 operator_tester ，操作员密码为 tester_password
连接结果字符串：
operator_testerbucket_nameimtesternotify_urlhttp://www.example.com/notify/source/video/20130514_190031.mp4tasksW3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d97b5a9d718b7a2064f7e7673c4d8bcb8
```

4、将第三步生成字符串进行 MD5 计算，得到 `signature` 值。

`ad91a9ab81ecc34e973844a6723ce354`

特别地，参数都使用 UTF-8 编码。

### 回调通知

处理完成后，系统根据提交任务时提交的 notify_url 参数，将结果以 HTTP POST 请求方式进行回调通知。

回调通知的参数

|         参数       |    类型    |    说明                                                                                           |
|-------------------|-----------|---------------------------------------------------------------------------------------------------|
| bucket_name       | string    | 文件所在服务名称（空间名称）                                                                           |
| status_code       | integer   | 处理结果状态码，200 表示成功处理                                                                       |
| path              | array     | 输出文件保存路径                                                                                     |
| description       | string    | 处理结果描述                                                                                        |
| task_id           | string    | 任务对应的 task_id                                                                                  |
| info              | string    | 视频文件的元数据信息。经过 base64 处理过之后的 JSON 字符串，仅当 type 为 video且 return_info 为 true 时返回  |
| signature         | string    | 回调验证签名，用户端程序可以通过校验签名，判断回调通知的合法性                                               |
| timestamp         | integer   | 服务器回调此信息时的时间戳                                                                             |


回调验证签名 `signature` 的计算方法：

```
md5(<operator_name><operator_password><task_id><timestamp>)
// md5 中各字段为字符串，且先后顺序与上保持一致
```

### 音视频上传预处理

通过 FORM API 和 分块 API 上传音视频文件，在上传请求中附加处理参数，文件上传完后，自动开始音视频处理，处理完成后回调通知。

```
"save_key": "path/to/save/your/upload/file"，   //音视频保存路径
"notify_url": "http://callback/url", 	//可选，回调通知
"apps": [
  {
"app_name": "naga",			//异步音视频处理，任务1
"accept": "json",			//必填，回调返回 JSON 格式内容
"task": {
	"type": "video",			//视频转码
      "avopts": "/s/240p(4:3)/as/1/r/30",	//处理任务，通过预置模板设置
      "save_as": "path/to/save/process/result/file",  //处理结果音视频保存路径
    	}
  }，
  {
	"app_name": "naga",			//异步音视频处理，任务2
 "accept": "json",			//必填，回调返回 JSON 格式内容
     "task": {
	"type":"vconcat",				//视频拼接
	"avopts":"/i/L2EvYi9jLm1wNA==/i/LzEvMi8zLm1wNA==",//拼接视频列表
	"save_as":"/concat/a.mp4"
    	}
  }
]
```

如果 `task` 里面不存在 `save_as` 参数,则文件生成在 `save_key` 所在目录下。如果不存在 `notify_url` 参数,，则不回调。


## 基本功能

这是一组音视频处理的基础功能。

`通用参数`（支持所有音视频处理功能）

|         参数       |    类型    |    说明                                                             |
|-------------------|-----------|---------------------------------------------------------------------|
| save_as           | string    | 输出文件保存路径（同一个空间下），如果没有指定，系统自动生成在同空间同目录下      |
| return_info       | boolean   | 是否返回 JSON 格式元数据，默认 false。支持 type 值为 video 功能            |

### 视频转码

|         参数                |    类型   |    说明                                                                                         |
|----------------------------|-----------|------------------------------------------------------------------------------------------------|
| `/type/`                   | string    | 处理类型，值为 video                                                                             |
| `/vb/<bitrate>`            | integer   | 视频比特率，单位 kbps，默认按照视频原始比特率处理                                                      |
| `/s/<scale>`               | string    | 视频分辨率，默认按照原始分辨率处理。格式：预置模板如720p(16:9)，自定义如1820x720，建议使用预置模板[见附件一](/cloud/trans_template/) |
| `/as/<auto_scale>`         | boolean   | 是否根据分辨率自动调整视频长宽比例，仅当传递了 scale 参数时有效                                          |
| `/r/<frame_rate>`          | integer   | 视频帧率，既每秒显示的帧数，常用帧率"25"、"30"等，默认按照原始帧率处理                                    |
| `/sp/<rotate>`             | string    | 旋转角度，默认按照原始视频角度处理。可选值：auto（自动扶正），90，180，270。                               |
| `/sm/<map_metadata>`       | boolean   | 是否保留视频 meta 信息，默认值 true                                                                |
| `/acodec/<audio_codec>`    | string    | 设置音频编码器，可选："libmp3lame"、 "libfdk_aac"、"copy"，默认按照音频原始编码器处理                    |
| `/vcodec/<video_codec>`    | string    | 设置视频编码器，可选："libx264"、 "libtheora"、"copy"，默认按照视频原始编码器处理                        |
| `/an/<disable_audio>`      | boolean   | 是否禁掉音频，默认 false                                                                           |
| `/vn/<disable_video>`      | boolean   | 是否禁掉视频，默认 false                                                                           |
| `/su/<accelerate_factor>`  | float     | 视频加速倍数，取值范围`[1.0，10.0]`                                                                 |

系统预置转码模板见附件五《预置转码模板列表》。

### HLS 切片

|         参数         |    类型    |    说明                                                                                           |
|---------------------|-----------|---------------------------------------------------------------------------------------------------|
| `/type/`            | string    | 处理类型，值为 `hls`                                                                                 |
| `/ht/<hls_time>`    | string    | 指定切割的时间片长度，单位 s（秒）                                                                      |
| `/vb/<bitrate>`     | integer   | 视频比特率，单位 kbps，默认按照视频原始比特率处理                                                         |
| `/s/<scale>`        | string    | 视频分辨率，默认按照原始分辨率处理格式：预置模板如720p(16:9)，自定义如1820x720，建议使用预置模板[见附件一](/cloud/trans_template/)      |
| `/as/<auto_scale>`  | boolean   | 是否根据分辨率自动调整视频长宽比例，仅当传递了 scale 参数时有效                                             |
| `/r/<frame_rate>`   | integer   | 视频帧率，既每秒显示的帧数，常用帧率25、30等，默认按照原始帧率处理                                           |

系统预置转码模板见附件五《预置转码模板列表》。

### 视频水印

为视频添加图片水印。

|         参数                        |    类型    |    说明                                                  |
|------------------------------------|-----------|----------------------------------------------------------|
| `/type/`                           | string    | 处理类型，值为 video                                        |
| `/wmImg/<watermark_img>`           | string    | 水印图片相对路径                                            |
| `/wmGravity/<watermark_gravity>`   | string    | 水印图片位置，默认 northeast，详见注1                         |
| `/wmDx/<watermark_dx>`             | integer   | 水印图片横坐标偏移量，单位px，默认值-20（跟水印图片位置相关）      |
| `/wmDy/<watermark_dy>`             | integer   | 水印图片纵坐标偏移量，单位px，默认值15（跟水印图片位置相关）       |


注1：水印图片的位置：共9个方位
```
    northwest       |     north      |   northeast
     （西北)         |     （北）      |    （东北）
  ------------------+----------------+-----------------
       west         |    center      |     east
      （北）         |     （中)       |    （东）
  ------------------+----------------+-----------------
    southwest       |    south       |   southeast
     （西南)         |    （南)        |    （东南）
```

### 视频截图

对视频进行截图，可以截单张图，也可以在一段时间内截多张图。

|         参数         |    类型    |    说明                                                        |
|---------------------|-----------|----------------------------------------------------------------|
| `/type/`            | string    | 处理类型，值为 `thumbnail`                                       |
| `/o/<thumb_single>` | boolean   | 是否仅截取单张图片，默认值 `true`                                  |
| `/n/<thumb_amount>` | integer   | 截图数量，当 `thumb_single` 参数的值为 `false` 时，需指定截图数量     |
| `/ss/<thumb_start>` | string    | 截图开始时间，格式为 `HH:MM:SS`，默认值为 `00:00:00`                |
| `/es/<thumb_end>`   | string    | 截图结束时间，格式为  `HH:MM:SS`，默认值为视频结束时间                |
| `/s/<thumb_scale>`  | string    | 截图尺寸，格式为 `640:480`，默认值为视频原始尺寸                      |
| `/f/<thumb_format>` | string    | 截图输出格式，支持 `png/jpg` 格式，默认 `jpg`                       |


### 视频剪辑

|         参数         |    类型    |    说明                                         |
|---------------------|-----------|-------------------------------------------------|
| `/type/`            | string    | 处理类型，值为 `video`                             |
| `/ss/<start_time>`  | string    | 剪辑开始时间，格式为 `HH:MM:SS`，默认值为视频开始时间   |
| `/es/<end_time>`    | string    | 剪辑结束时间，格式为 `HH:MM:SS`，默认值为视频结束时间   |


### 视频拼接

|         参数         |    类型    |    说明                                                                        |
|---------------------|-----------|--------------------------------------------------------------------------------|
| `/type/`            | string    | 处理类型，值为 `vconcat`                                                         |
| `/i/<videos>`       | list      | 需要拼接的视频文件路径的列表，按列表顺序依次把视频拼接在源视频后。所有视频必须在源视频空间下   |

如果指定 `save_as`（通用参数）格式跟源视频（`source`）格式、拼接视频格式不一致，系统自动转码源视频、拼接视频成  `save_as`  格式；如果未指定 `save_as`，拼接视频跟源视频格式不一致，系统自动转码拼接视频成源视频格式。

特别地，自动转码过程按视频转码计费。

### 音频转码

|         参数                |    类型   |    说明                                           |
|----------------------------|-----------|-------------------------------------------------|
| `/type/`                   | string    | 处理类型，值为 audio                               |
| `/ac/<audio_channel>`      | integer   | 声道，默认 2                                      |
| `/ab/<audio_bitrate>`      | integer   | 比特率，单位 kbps，默认按照音频原始比特率处理           |
| `/vbr/<audio_vbr>`         | integer   | variable bitrate `[0-9]`，默认按照音频原始 VBR处理   |
| `/sm/<map_metadata>`       | boolean   | 是否保留音频 meta 信息，默认值 true                  |

可以通过 `save_as`（通用参数） 自定义保存路径。

### 音频剪辑

|         参数         |    类型    |    说明                                         |
|---------------------|-----------|-------------------------------------------------|
| `/type/`            | string    | 处理类型，值为 `audio`                             |
| `/ss/<start_time>`  | string    | 剪辑开始时间，格式为 `HH:MM:SS`，默认值为视频开始时间   |
| `/es/<end_time>`    | string    | 剪辑结束时间，格式为 `HH:MM:SS`，默认值为视频结束时间   |


### 音频拼接

|         参数         |    类型    |    说明                  |
|---------------------|-----------|--------------------------|
| `/type/`            | string    | 处理类型，值为 `aconcat`    |
| `/i/<medias>`       | list      | 需要拼接的音频文件路径的列表   |

如果指定 `save_as`（通用参数）格式跟源音频（`source`）格式、拼接音频格式不一致，系统自动转码源音频、拼接音频成  `save_as`  格式；如果未指定 `save_as`，拼接音频跟源音频格式不一致，系统自动转码拼接音频成源音频格式。

特别地，自动转码过程按音频转码计费。


## 属性获取

这是一组获取音视频信息的功能。

### 元数据

获取音视频文件的元信息。

|         参数         |    类型    |    说明                  |
|---------------------|-----------|--------------------------|
| `/type/`            | string    | 处理类型，值为 `probe`     |

## 过程查询

这是一组关于音视频处理过程的功能。

### 进度查询

通过 `task_id` 以 `GET` 方式向指定地址 `http://p0.api.upyun.com/status/` 查询任务处理进度。

查询参数

|         参数       |    类型            |   说明                               |
|-------------------|--------------------|-------------------------------------|
| bucket_name       | string             | 文件所在服务名称（空间名称）             |
| task_ids          | string             | 任务 id 以 `,` 作为分隔符，最多 20 个    |

返回数据

```
{
	tasks:{
	35f0148d414a688a275bf915ba7cebb2: 100,
	98adbaa52b2f63d6d7f327a0ff223348: 20,
	c3103189fa906a5354d29bd807e8dc51: null,
	…
	}
}
```

特别地，当值为 null 时，表示没有查询到相关的任务信息。

### 结果查询

通过 `task_id` 以 `GET` 方式向指定地址 `http://p0.api.upyun.com/result/` 查询任务处理结果。

除了 `API` 地址不一样外，其余的都跟进度查询一样。
