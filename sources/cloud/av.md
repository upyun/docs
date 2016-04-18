异步音视频处理接口是用于处理**已经上传到对应存储空间**中的视频文件，进行转码、截图等操作。在处理完成之后，异步通知用户处理结果。

## 使用方法

### 请求方法

以 `POST` 方式向 `http://p0.api.upyun.com/pretreatment/` 提交处理请求，UPYUN 接受请求后立即返回任务 `task_id`，处理完成后会通过回调的方式通知用户。同时，用户可以通过 `task_id` 查询任务处理进度和处理结果。

例如：

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <operator>:<signature>" \
    -H "Date: Wed, 29 Oct 2014 02:26:58 GMT"
    # 其他可选参数...
```

### 请求参数

|        参数       |    类型            |   说明                                |
|-------------------|--------------------|---------------------------------------|
| bucket_name       | string             | 文件所在服务名称（空间名称）          |
| notify_url        | string             | 回调通知地址                          |
| source            | string             | 待处理文件路径                        |
| tasks             | string             | 处理任务信息，详见下                  |
| accept            | string             | 必须指定为 `json`  |


`tasks` 参数通过下面三个步骤生成：

1\. 组装业务参数。一次最多可以提交10个处理任务。

```
[
	{
	    "type": "video",                        //视频转码
	    "avopts": "/s/240p(4:3)/as/1/r/30",     //处理任务，通过预置模板设置
        "return_info": true,                  //返回元数据
        "save_as": "/a/b.mp4",                //保存路径
	    …
	},
  	{
	    "type": "vconcat",                      //视频拼接
	    "avopts": "/i/L2EvYi9jLm1wNA==/i/LzEvMi8zLm1wNA==",    //拼接视频列表
	    "save_as": "/concat/a.mp4"
	    …
	},
	…
]
```

2\. 把组装好的参数转换为 JSON 字符串。

3\. 对 JSON 字符串进行 base64 编码处理。

### 授权认证

请求的授权认证是通过在 HTTP 请求头中添加 `Authorization` 进行验证：
```
Authorization:　UPYUN　<operator>:<signature>
```

其中，`operator` 是操作员名称，`signature` 是根据参数计算出来的签名。

`signature` 参数通过下面的方式获得：

1\. 将参数按照参数名称的字典顺序排序后，将参数键值对连接成一个字符串。

2\. 将第一步生成的字符串按照下面的规则进行连接，获取待签名字符串：

```
<operator_name><signature_string><md5_operator_password>
```

>其中，`operator_name` 为用于认证授权的操作员名，`signature_string` 为第一步生成的字符串，
>`md5_operator_password` 为对操作员密码进行 md5 计算之后得到的字符串。

3\. 对第二步生成的字符串做 md5 计算，得到最终的 `signature` 值。

> 特别地，参数字符串都必须使用 `utf-8` 编码处理。

以下是一个计算 `signature` 的例子：

假设接口提交如下参数：

```
bucket_name: 'imtester'
tasks: 'W3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d'
notify_url: 'http://www.example.com/notify/'
source: '/video/20130514_190031.mp4'
```

用于授权验证的操作员名为 `operator_tester` ，操作员密码为 `tester_password`。

首先根据参数名称的字段顺序进行排序：

```
bucket_name: 'imtester'
notify_url: 'http://www.example.com/notify/'
source: '/video/20130514_190031.mp4'
tasks: 'W3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d'
```

将排序后的参数键值对连接成一个字符串，得到：

```
bucket_nameimtesternotify_urlhttp://www.example.com/notify/source/video/20130514_190031.mp4tasksW3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d
```

连接操作员名和进行 md5 计算之后的操作员密码：

```
operator_testerbucket_nameimtesternotify_urlhttp://www.example.com/notify/source/video/20130514_190031.mp4tasksW3sidHlwZSI6InZpZGVvIiwiYml0cmF0ZSI6IjUwMCIsInJvdGF0ZSI6ImF1dG8iLCJmb3JtYXQiOiJtcDQifSx7InR5cGUiOiJ0aHVtYm5haWwiLCJ0aHVtYl9zaWdubGUiOmZhbHNlLCJ0aHVtYl9hbW91bnQiOjEwMCwiZm9ybWF0IjoicG5nIn1d97b5a9d718b7a2064f7e7673c4d8bcb8
```

将得到的字符串进行 md5 计算，得到最终的 `signature` 值为 `ad91a9ab81ecc34e973844a6723ce354`。


### 回调通知

处理完成后，系统根据提交任务时提交的 `notify_url` 参数，将结果以 `HTTP POST` 请求进行回调通知。

** 回调通知参数 **

|        参数       |    类型   |    说明                                                                                                      |
|-------------------|-----------|--------------------------------------------------------------------------------------------------------------|
| bucket_name       | string    | 文件所在服务名称（空间名称）                                                                                 |
| status_code       | integer   | 处理结果状态码，200 表示成功处理                                                                             |
| path              | array     | 输出文件保存路径                                                                                             |
| description       | string    | 处理结果描述                                                                                                 |
| task_id           | string    | 任务对应的 task_id                                                                                           |
| info              | string    | 视频文件的元数据信息。经过 base64 处理过之后的 JSON 字符串，仅当 type 为 video且 return_info 为 true 时返回  |
| signature         | string    | 回调验证签名，用户端程序可以通过校验签名，判断回调通知的合法性                                               |
| timestamp         | integer   | 服务器回调此信息时的时间戳                                                                                   |


回调验证签名 `signature` 的计算方法：

```
md5(<operator_name><operator_password><task_id><timestamp>), 并取中间 16 位。
```


### 进度查询

通过 `task_id` 以 `GET` 方式向指定地址 `http://p0.api.upyun.com/status` 查询任务处理进度。

例如：

```
curl http://p0.api.upyun.com/status?bucket_name=imtester&task_ids=35f0148d414a688a275bf915ba7cebb2,98adbaa52b2f63d6d7f327a0ff223348,c3103189fa906a5354d29bd807e8dc51
```

** 查询参数 **

|        参数       |    类型            |   说明                                   |
|-------------------|--------------------|------------------------------------------|
| bucket_name       | string             | 文件所在服务名称（空间名称）             |
| task_ids          | string             | 任务 id 以 `,` 作为分隔符，最多 20 个    |

** 返回数据 **

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

> 特别地，当值为 `null` 时，表示没有查询到相关的任务信息。

### 结果查询

通过 `task_id` 以 `GET` 方式向指定地址 `http://p0.api.upyun.com/result` 查询任务处理结果。

例如：

```
curl http://p0.api.upyun.com/result?bucket_name=imtester&task_ids=35f0148d414a688a275bf915ba7cebb2,98adbaa52b2f63d6d7f327a0ff223348,c3103189fa906a5354d29bd807e8dc51
```

查询参数与返回数据与 [进度查询](/cloud/av/#_6) 相同


## 处理参数

### 通用参数

本组参数支持所有音视频处理功能。

|        参数       |    类型   |    说明                                                                           |
|-------------------|-----------|-----------------------------------------------------------------------------------|
| type              | string    | 音视频处理类型。不同的处理任务对应不同的 type，详见下方各处理任务说明                  |
| save_as           | string    | 输出文件保存路径（同一个空间下），如果没有指定，系统自动生成在同空间同目录下      |
| return_info       | boolean   | 是否返回 JSON 格式元数据，默认 false。支持 type 值为 video 功能                   |
| avopts            | string    | 音视频处理参数, 格式为 `/key/value/key/value/...`                                    |

> 特别的， 在 `avopts` 中使用 0 表示布尔值的 `false`, 1 表示布尔值的 `true`。以下如无特殊情况不做再次申明。


### 视频转码

|         参数               |    类型   |   说明                                    |
|----------------------------|-----------|----------------------------------------------------------|
| `type`                     | string    | 处理类型，值为 video                                                                                                                   |
| `/vb/<bitrate>`            | integer   | 视频比特率，单位 kbps，默认按照视频原始比特率处理                                                                                      |
| `/s/<scale>`               | string    | 视频分辨率，默认按照原始分辨率处理。格式：预置模板如 720p(16:9)，自定义如 1820x720，建议使用预置模板[见附件](/cloud/attachment/#_1) |
| `/as/<auto_scale>`         | boolean   | 是否根据分辨率自动调整视频长宽比例，仅当传递了 scale 参数时有效                                                                        |
| `/r/<frame_rate>`          | integer   | 视频帧率，既每秒显示的帧数，常用帧率 "25"、"30" 等，默认按照原始帧率处理                                                               |
| `/sp/<rotate>`             | string    | 旋转角度，默认按照原始视频角度处理。可选值：auto（自动扶正），90，180，270。                                                           |
| `/sm/<map_metadata>`       | boolean   | 是否保留视频 meta 信息，默认值 true                                                                                                    |
| `/acodec/<audio_codec>`    | string    | 设置音频编码器，可选："libmp3lame"，"libfdk_aac"，"copy"，默认按照音频原始编码器处理                                                   |
| `/vcodec/<video_codec>`    | string    | 设置视频编码器，可选："libx264"，"libtheora"，"libx265"，"libvpx-vp9"，"libvpx"，"copy"，默认按照视频原始编码器处理                    |
| `/an/<disable_audio>`      | boolean   | 是否禁掉音频，默认 false                                                                                                               |
| `/vn/<disable_video>`      | boolean   | 是否禁掉视频，默认 false                                                                                                               |
| `/su/<accelerate_factor>`  | float     | 视频加速倍数，取值范围 `[1.0，10.0]`                                                                                                   |

系统预置转码模板见[视频转码预置模板](/cloud/attachment/#_1)。

### HLS 切片

|        参数         |    类型   |    说明                                                                                                                                   |
|---------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `hls`                                                                                                                      |
| `/ht/<hls_time>`    | string    | 指定切割的时间片长度，单位 s（秒）                                                                                                        |
| `/vb/<bitrate>`     | integer   | 视频比特率，单位 kbps，默认按照视频原始比特率处理                                                                                         |
| `/s/<scale>`        | string    | 视频分辨率，默认按照原始分辨率处理格式：预置模板如 720p(16:9)，自定义如 1820x720，建议使用预置模板[见附件](/cloud/attachment/#_1)      |
| `/as/<auto_scale>`  | boolean   | 是否根据分辨率自动调整视频长宽比例，仅当传递了 scale 参数时有效                                                                           |
| `/r/<frame_rate>`   | integer   | 视频帧率，既每秒显示的帧数，常用帧率25、30等，默认按照原始帧率处理                                                                        |

系统预置转码模板见[视频转码预置模板](/cloud/attachment/#_1)。

### 视频水印

为视频添加图片水印。

|         参数                       |    类型   |    说明                                                            |
|------------------------------------|-----------|--------------------------------------------------------------------|
| `type`                             | string    | 处理类型，值为 `video`                                             |
| `/wmImg/<watermark_img>`           | string    | 水印图片相对路径                                                   |
| `/wmGravity/<watermark_gravity>`   | string    | 水印图片位置，默认 northeast，详见注1                              |
| `/wmDx/<watermark_dx>`             | integer   | 水印图片横坐标偏移量，单位 px，当 wmGravity 为 northeast 时默认值 -20      |
| `/wmDy/<watermark_dy>`             | integer   | 水印图片纵坐标偏移量，单位 px，当 wmGravity 为 northeast 时默认值 15       |


> 注：水印图片的位置共 9 个方位：

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

### 视频截图

对视频进行截图，可以截单张图，也可以在一段时间内截多张图。

|        参数         |    类型   |    说明                                                               |
|---------------------|-----------|-----------------------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `thumbnail`                                            |
| `/o/<thumb_single>` | boolean   | 是否仅截取单张图片，默认值 `true`                                     |
| `/n/<thumb_amount>` | integer   | 截图数量，当 `thumb_single` 参数的值为 `false` 时，需指定截图数量     |
| `/ss/<thumb_start>` | string    | 截图开始时间，格式为 `HH:MM:SS`，默认值为 `00:00:00`                  |
| `/es/<thumb_end>`   | string    | 截图结束时间，格式为  `HH:MM:SS`，默认值为视频结束时间                |
| `/s/<thumb_scale>`  | string    | 截图尺寸，格式为 `640:480`，默认值为视频原始尺寸                      |
| `/f/<thumb_format>` | string    | 截图输出格式，支持 `png/jpg` 格式，默认 `jpg`                         |


### 视频剪辑

|        参数         |    类型   |    说明                                                 |
|---------------------|-----------|---------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `video`                                  |
| `/ss/<start_time>`  | string    | 剪辑开始时间，格式为 `HH:MM:SS`，默认值为视频开始时间   |
| `/es/<end_time>`    | string    | 剪辑结束时间，格式为 `HH:MM:SS`，默认值为视频结束时间   |


### 视频拼接

|        参数         |    类型   |    说明                                                                                               |
|---------------------|-----------|-------------------------------------------------------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `vconcat`                                                                              |
| `/i/<video>`        | string    | 需要拼接的视频文件路径, 视频必须在源视频空间下。可多次接收 `/i/<video>` 参数，如 `/i/<video>/i/<video>` |

> 如果指定 `save_as`（通用参数）格式跟源视频（`source`）格式、拼接视频格式不一致，系统自动转码源视频、拼接视频成  `save_as`  格式；如果未指定 `save_as`，拼接视频跟源视频格式不一致，系统自动转码拼接视频成源视频格式。

### 音频转码

|         参数               |    类型   |    说明                                              |
|----------------------------|-----------|------------------------------------------------------|
| `type`                     | string    | 处理类型，值为 audio                                 |
| `/ac/<audio_channel>`      | integer   | 声道，默认 2                                         |
| `/ab/<audio_bitrate>`      | integer   | 比特率，单位 kbps，默认按照音频原始比特率处理        |
| `/vbr/<audio_vbr>`         | integer   | variable bitrate, 范围 `[0-9]`，默认按照音频原始 VBR 处理   |
| `/sm/<map_metadata>`       | boolean   | 是否保留音频 meta 信息，默认值 true                  |

> 可以通过 `save_as`（通用参数） 自定义保存路径。

### 音频剪辑

|         参数        |    类型   |    说明                                                 |
|---------------------|-----------|---------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `audio`                                  |
| `/ss/<start_time>`  | string    | 剪辑开始时间，格式为 `HH:MM:SS`，默认值为视频开始时间   |
| `/es/<end_time>`    | string    | 剪辑结束时间，格式为 `HH:MM:SS`，默认值为视频结束时间   |


### 音频拼接

|         参数        |    类型   |    说明                                                                                                 |
|---------------------|-----------|---------------------------------------------------------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `aconcat`                                                                                |
| `/i/<media>`        | string    | 需要拼接的音频文件路径, 音频必须在源音频空间下。可多次接收 `/i/<media>` 参数，如 `/i/<media>/i/<media>`   |

> 如果指定 `save_as`（通用参数）格式跟源音频（`source`）格式、拼接音频格式不一致，系统自动转码源音频、拼接音频成  `save_as`  格式；如果未指定 `save_as`，拼接音频跟源音频格式不一致，系统自动转码拼接音频成源音频格式。


### 元数据获取

获取音视频文件的元信息。

|         参数        |    类型   |    说明                    |
|---------------------|-----------|----------------------------|
| `type`              | string    | 处理类型，值为 `probe`     |
