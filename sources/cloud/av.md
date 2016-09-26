音视频处理服务是对**已经上传到云存储中的音视频文件**进行视频转码、音频转码、HLS 切片、视频截图、视频水印、元信息获取等处理，处理完成后，以回调的方式通知用户处理结果。如果待处理音视频文件不存在又拍云存储，需要预先调用文件上传 API 将音视频文件上传到又拍云存储中。

> 注：文中所有 `<>` 标注的字段，均需根据实际情况进行替换。

## 使用方法

### 请求方法

以 `POST` 方式向 `http://p0.api.upyun.com/pretreatment/` 提交处理请求，又拍云接受请求后立即返回任务 `task_id`，处理完成后会通过回调的方式通知用户。同时，用户可以通过 `task_id` 查询任务处理进度和处理结果。

例如：

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <operator>:<signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>"
    -d "accept=json" \
    -d "bucket_name=<bucket_name>" \
    -d "notify_url=<notify_url>" \
    -d "source=<又拍云存储空间中的音视频文件路径>" \
    -d "tasks=<base64 编码后的任务字符串>"
```

### 请求参数

|        参数       |    类型       | 必选     |   说明                                |
|-------------------|--------------|------|---------------------------------------|
| bucket_name       | string       |  是    | 文件所在服务名称（空间名称）          |
| notify_url        | string       |  是    | 回调通知地址                          |
| source            | string       |  是   | 待处理文件路径                        |
| tasks             | string       |  是   | 处理任务信息，详见下                  |
| accept            | string       |  是    | 必须指定为 `json`  |


`tasks` 参数通过下面三个步骤生成：

1\. 组装业务参数。一次最多可以提交 10 个处理任务。

```
[
    {
        "type": "video",                        // 视频转码
        "avopts": "/s/240p(4:3)/as/1/r/30",     // 处理参数 [注1]
        "return_info": true,                    // 返回元数据
        "save_as": "/a/b.mp4"                   // 保存路径
    },
    {
        "type": "vconcat",                                     // 视频拼接
        "avopts": "/i/L2EvYi9jLm1wNA==/i/LzEvMi8zLm1wNA==",    // 处理参数 [注1]
        "save_as": "/concat/a.mp4"
    },
    …
]
```

> 注： `avopts` 是音视频处理参数，具体参数和说明请见 [这里](/cloud/av/#_8)

2\. 把组装好的参数转换为 JSON 字符串。

3\. 对 JSON 字符串进行 base64 编码处理, 得到以下结果。
```
W3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ==
```

### 授权认证

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


### 回调通知

处理完成后，系统根据提交任务时的 `notify_url` 参数，将处理结果转换成 JSON 字符串，并以 `HTTP POST` 请求进行回调通知。

** 回调通知参数 **

|        参数       |    类型   |    说明                                                                                                      |
|-------------------|-----------|--------------------------------------------------------------------------------------------------------------|
| bucket_name       | string    | 文件所在服务名称（空间名称）                                                                                 |
| status_code       | integer   | 处理结果状态码，200 表示成功处理                                                                             |
| path              | array     | 输出文件保存路径                                                                                             |
| description       | string    | 处理结果描述                                                                                                 |
| task_id           | string    | 任务对应的 task_id                                                                                           |
| info              | json      | 音视频文件的元数据信息                                                                                       |
| signature         | string    | 回调验证签名，用户端程序可以通过校验签名，判断回调通知的合法性                                               |
| timestamp         | integer   | 服务器回调此信息时的时间戳                                                                                   |


回调验证签名 `signature` 的计算方法：

```
md5(<operator_name><md5_operator_password><task_id><timestamp>), 并取中间 16 位。
```


### 进度查询

通过 `task_id` 以 `GET` 方式向指定地址 `http://p0.api.upyun.com/status` 查询任务处理进度。

例如：

```
curl http://p0.api.upyun.com/status?bucket_name=demo&task_ids=35f0148d414a688a275bf915ba7cebb2,98adbaa52b2f63d6d7f327a0ff223348,c3103189fa906a5354d29bd807e8dc51 \
    -H "Authorization: UPYUN <operator>:<signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>"
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
curl http://p0.api.upyun.com/result?bucket_name=demo&task_ids=35f0148d414a688a275bf915ba7cebb2,98adbaa52b2f63d6d7f327a0ff223348,c3103189fa906a5354d29bd807e8dc51 \
    -H "Authorization: UPYUN <operator>:<signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>"
```

查询参数与 [进度查询](/cloud/av/#_6) 相同, 返回数据与 [回调通知](/cloud/av/#_5) 相同。

** 返回数据 **

```
{
    "9d9c32b63a1034834e77672c6f51f661": {
        "path": ["/v2.mp4"],
        "signature": "4042c1f07f546d28",
        "status_code": 200,
        "bucket_name": "bucket",
        "description": "OK",
        "task_id": "9d9c32b63a1034834e77672c6f51f661",
        "timestamp": 1472010905
    },
    "3438a54b4991e8d4a46a003bc15e9867": {
        "path": ["/v2.mp4"],
        "signature": "1c49be9b672394ff",
        "status_code": 200,
        "bucket_name": "bucket",
        "description": "OK",
        "task_id": "3438a54b4991e8d4a46a003bc15e9867",
        "timestamp": 1472010684
    }
    …
}
```


### 上传预处理

以上异步音视频处理接口只能用于处理已经上传到对应存储空间中的视频文件。对于一个处理任务，需要发起两次请求（第一次上传文件，第二次发起请求）。

通过在 [表单 API](/api/form_api/#api_1) 的 `apps` 参数里添加异步音视频处理任务，并指定任务名称为 `naga`，即可实现在上传音视频文件之后同时发起异步音视频预处理任务。这样只需要一次请求就可以完成音视频处理。


表单发起上传预处理任务的参数与音视频处理请求的 [参数](/cloud/av/#_9) 相同，只需要另外加上`"name": "naga"` 这个参数。示例如下：
```
{
    "name": "naga",                         // 需要指定 name 为 naga
    "type": "video",                        // 视频转码
    "avopts": "/s/240p(4:3)/as/1/r/30",     // 处理参数
    "return_info": true,                    // 返回元数据
    "save_as": "/a/b.mp4",                  // 保存路径
},
```

发起上传预处理处理任务的具体方法见 [这里](/api/form_api/#_4)。处理完成后的回调信息为 json 格式，字段及含义与音视频处理请求 [相同](/cloud/av/#_5)。


## 处理参数

### 通用参数

本组参数支持所有音视频处理功能。

|        参数       |    类型   |    说明                                                                           |
|-------------------|-----------|-----------------------------------------------------------------------------------|
| type              | string    | 音视频处理类型。不同的处理任务对应不同的 type，详见下方各处理任务说明                  |
| save_as           | string    | 输出文件保存路径，如果没有指定，系统将按照一定命名规则自动生成在同目录下，可通过 [结果查询](/cloud/av/#_7) 与 [回调通知](/cloud/av/#_5) 获取该路径    |
| return_info       | boolean   | 是否返回 JSON 格式元数据，默认 false                                             |
| avopts            | string    | 音视频处理参数, 格式为 `/key/value/key/value/...`                                    |

注：

- `avopts` 中使用 0 表示布尔值的 `false`, 1 表示布尔值的 `true`。以下如无特殊情况不做再次申明。
- `save_as` ： 在 **HLS 切片** 任务中，该参数必须以 `.m3u8` 结尾；在 **视频截图** 任务中，处理多张截图时，输出图片文件名会根据该参数进行推算，例如 `save_as` 为 `/path/to/img.png`，则输出文件名分别为 `/path/to/img1.png`，`/path/to/img2.png`，以此类推；当没有指定输出格式的情况下，`save_as` 的后缀名会决定输出格式。


### 视频转码

|         参数               |    类型   |   说明                                    |
|----------------------------|-----------|----------------------------------------------------------|
| `type`                     | string    | 处理类型，值为 `video`                                                                                                                   |
| `/f/<format>`              | string    | 视频格式，默认按照原始视频后缀名作为输出格式                                                                                      |
| `/vb/<bitrate>`            | integer   | 视频比特率，单位 kbps，默认按照视频原始比特率处理                                                                                      |
| `/s/<scale>`               | string    | 视频分辨率，默认按照原始分辨率处理。格式：预置模板如 720p(16:9)，自定义如 1820x720，建议使用预置模板 [见附件](/cloud/attachment/#_1) |
| `/as/<auto_scale>`         | boolean   | 是否根据分辨率自动调整视频长宽比例（将保留原始视频竖屏或横屏状态），仅当传递了 scale 参数时有效                                        |
| `/ar/<audio_sample_rate>`  | integer   | 音频采样率，单位 Hz，可选值：44100，48000，32000，22050，24000，16000，0                                                            |
| `/r/<frame_rate>`          | integer   | 视频帧率，既每秒显示的帧数，常用帧率 "25"、"30" 等，默认按照原始帧率处理                                                               |
| `/sp/<rotate>`             | string    | 旋转角度，默认按照原始视频角度处理。可选值：auto（自动扶正），90，180，270。                                                           |
| `/sm/<map_metadata>`       | boolean   | 是否保留视频 meta 信息，默认值 true                                                                                                    |
| `/acodec/<audio_codec>`    | string    | 设置音频编码器，可选："libmp3lame"，"libfdk_aac"，"copy"，默认按照音频原始编码器处理                                                   |
| `/vcodec/<video_codec>`    | string    | 设置视频编码器，可选："libx264"，"libtheora"，"libx265"，"libvpx-vp9"，"libvpx"，"copy"，默认按照视频原始编码器处理                    |
| `/an/<disable_audio>`      | boolean   | 是否禁掉音频，默认 false                                                                                                               |
| `/vn/<disable_video>`      | boolean   | 是否禁掉视频，默认 false                                                                                                               |
| `/su/<accelerate_factor>`  | float     | 视频加速倍数，取值范围 `[1.0，10.0]`                                                                                                   |

系统预置转码模板见 [视频转码预置模板](/cloud/attachment/#_1)。

### HLS 切片

|        参数         |    类型   |    说明                                                                                                                                   |
|---------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `type`              | string    | 处理类型，值为 `hls`                                                                                                                      |
| `/ht/<hls_time>`    | string    | 指定切割的时间片长度，单位 s（秒）                                                                                                        |
| `/vb/<bitrate>`     | integer   | 视频比特率，单位 kbps，默认按照视频原始比特率处理                                                                                         |
| `/s/<scale>`        | string    | 视频分辨率，默认按照原始分辨率处理格式：预置模板如 720p(16:9)，自定义如 1820x720，建议使用预置模板 [见附件](/cloud/attachment/#_1)      |
| `/as/<auto_scale>`  | boolean   | 是否根据分辨率自动调整视频长宽比例，仅当传递了 scale 参数时有效                                                                           |
| `/r/<frame_rate>`   | integer   | 视频帧率，既每秒显示的帧数，常用帧率25、30等，默认按照原始帧率处理                                                                        |

系统预置转码模板见 [视频转码预置模板](/cloud/attachment/#_1)。

### 视频水印

为视频添加图片水印。

|         参数                       |    类型   |    说明                                                            |
|------------------------------------|-----------|--------------------------------------------------------------------|
| `type`                             | string    | 处理类型，值为 `video`                                             |
| `/wmImg/<watermark_img>`           | string    | 水印图片相对路径，如 `/a/b.png`，水印图片格式支持 png 和 webp <br /> 特别地，相对路径需要安全的 Base64 编码                                                   |
| `/wmGravity/<watermark_gravity>`   | string    | 水印图片放置方位，默认 `northeast`，详见注1                              |
| `/wmDx/<watermark_dx>`             | integer   | 水印图片横偏移量，单位 px，当 `wmGravity` 为 `northeast` 时默认值 -20 <br /> 横偏移量取值正负的依据：往 `east` 方向偏移，为正；往 `west` 方向偏移，为负       |
| `/wmDy/<watermark_dy>`             | integer   | 水印图片纵偏移量，单位 px，当 `wmGravity` 为 `northeast` 时默认值 15 <br /> 纵偏移量取值正负的依据：往 `south` 方向偏移，为正；往 `north` 方向偏移，为负      |


注：

- 水印图片的位置：（共 9 个方位）
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
| `/o/<thumb_single>` | boolean   | 是否仅截取单张图片，单张截取为 `true`，多张截取为 `false`             |
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
| `/i/<video>`        | string    | 需要拼接视频文件的相对路径，如 `/a/b.mp4`，路径需要安全的 Base64 编码 <br /> 多个拼接视频按 `/i/<video>/i/<video>/...` 方式进行连接 |

注：

- 各个拼接视频的分辨率（画面宽高）必须跟源视频（`source`）一致，否则会拼接失败。
- 视频拼接顺序：拼接视频按提交的顺序，依次拼接在源视频后面。
- 如果指定 `save_as`（[通用参数](/cloud/av/#_10)）的格式跟源视频格式、拼接视频格式不一致，系统自动转码源视频、拼接视频成  `save_as`  格式；如果未指定 `save_as`，拼接视频跟源视频格式不一致，系统自动转码拼接视频成源视频格式。


### 音频转码

|         参数               |    类型   |    说明                                              |
|----------------------------|-----------|------------------------------------------------------|
| `type`                     | string    | 处理类型，值为 `audio`                                 |
| `/f/<format>`              | string    | 音频格式，默认按照原始音频后缀名作为输出格式              |
| `/ac/<audio_channel>`      | integer   | 声道，默认 2                                         |
| `/ab/<audio_bitrate>`      | integer   | 比特率，单位 kbps，默认按照音频原始比特率处理        |
| `/vbr/<audio_vbr>`         | integer   | variable bitrate, 范围 `[0-9]`，默认按照音频原始 VBR 处理   |
| `/sm/<map_metadata>`       | boolean   | 是否保留音频 meta 信息，默认值 true                  |

注：

- 可以通过 `save_as`（[通用参数](/cloud/av/#_10)） 自定义保存路径。

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
| `/i/<audio>`        | string    | 需要拼接音频文件的相对路径，如 `/a/b.mp3`，路径需要安全的 Base64 编码  <br /> 多个音频文件按 `/i/<audio>/i/<audio>/...` 方式进行连接   |

注：

- 音频拼接顺序：拼接音频按提交的顺序，依次拼接在源音频（`source`）后面。
- 如果指定 `save_as`（[通用参数](/cloud/av/#_10)）的音频格式跟源音频格式、拼接音频格式不一致，系统自动转码源音频、拼接音频成  `save_as`  格式；如果未指定 `save_as`，拼接音频跟源音频格式不一致，系统自动转码拼接音频成源音频格式。


### 元数据获取

获取音视频文件的元信息。

|         参数        |    类型   |    说明                    |
|---------------------|-----------|----------------------------|
| `type`              | string    | 处理类型，值为 `probe`     |
