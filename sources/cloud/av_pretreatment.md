
> ** 该文档为旧版异步视频处理文档，新版请参考 [异步音视频处理文档](/cloud/av/) **

视频预处理接口是用于处理已经上传到对应存储空间中的视频文件，进行转码、截图等操作。在处理完成之后，异步通知用户处理结果。

> **注：**
> 文中所有 `<>` 标注的字段，均需根据你的实际情况替换（无需 `<>` 符号，仅作标注之用）

## API 基本域名

```
p0.api.upyun.com
```

## 请求方法

```sh
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <operator>:<signature>" \
    -H "Date: Wed, 29 Oct 2014 02:26:58 GMT"
    # 其他可选参数...
```

## 授权认证
调用视频处理接口需要进行授权认证，通过在 HTTP Request Header 中添加 `Authorization` 的方式来进行权限验证：

```
Authorization: UPYUN <operator>:<signature>
```

其中 `operator` 为操作员名称，`signature` 为根据参数计算出来的签名。

### 签名算法

授权认证过程中的 `signature` 参数通过下面的方式获得：

1\. 将参数按照参数名称的字典顺序排序后，将参数键值对连接成一个字符串；
2\. 将第一步生成的字符串按照下面的规则进行连接，获取待签名字符串：

```
<operator_name><signature_string><md5_operator_password>
```

其中，`operator_name` 为用于认证授权的操作员名，`signature_string` 为第一步生成的字符串，
`md5_operator_password` 为对操作员密码进行 md5 计算之后得到的字符串；

3\. 对第二步生成的字符串做 md5 计算，得到最终的 `signature` 值。

特别地，参数字符串都必须使用 `utf-8` 编码处理。

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

将得到的字符串进行 md5 计算，得到最终的 `signature` 值为 `ad91a9ab81ecc34e973844a6723ce354`

特别地，`signature` 参数不参与签名计算。

## 视频处理
提交视频处理请求到处理队列，处理完成之后异步回调通知用户

### 请求地址
http://p0.api.upyun.com/pretreatment/

### 请求方法
POST，需要进行授权验证

### 请求参数
| 参数        | 类型 | 必选 | 参数说明                                     |
|-------------|----------|------|----------------------------------------------|
| bucket_name | string   | true | 空间名称，即要处理的文件所在的空间           |
| source      | string   | true | 待处理源文件路径                             |
| notify_url  | string   | true | 异步回调地址，在处理完成之后将会进行异步通知 |
| tasks       | string   | true | 提交的任务数据，base64 处理之后的字符串       |
| accept       | string   | false | 如果指定为 `json`，则在回调时会返回 `application/json` 格式的内容，否则返回 `application/x-www-form-urlencoded` 格式的内容      |

`tasks` 参数通过下面三个步骤生成：

1\. 组装业务参数，每次最多可以提交 10 组处理参数；

```
[
    {
        type: 'video',
        bitrate: 4000,
        ...
    },
    ...
]
```

2\. 将业务参数转换为 JSON 字符串；

3\. 对第二步生成的字符串进行 base64 encode 处理。

### 处理参数
又拍云的视频处理服务目前支持四种类型的处理请求：

* 视频转码
* HLS 切割
* 视频截图
* 视频信息获取

其中，`视频信息读取` 的任务可用于获取空间内的任一视频文件的源信息

1\. 视频转码

| 参数              | 类型    | 必选  | 参数说明                                                                                                     |
|-------------------|---------|-------|--------------------------------------------------------------------------------------------------------------|
| type              | string  | true  | 处理类型，进行视频转码时值必须为 `video`                                                                   |
| bitrate           | integer | false | 视频比特率，单位 `kbit/s`，默认按照视频原始比特率处理                                                             |
| scale             | string  | false | 视频分辨率，格式 `1024:768`，默认按照原始分辨率处理                                                       |
| auto_scale        | boolean | false | 是否根据分辨率自动调整视频长宽比例，仅当传递了 `scale` 参数时有效                                          |
| frame_rate        | integer | false | 帧率，既每秒显示的帧数，常用帧率："24"、"25"、"30"等，默认按照原始帧率处理                                  |
| rotate            | string  | false | 旋转角度，默认按照原始视频角度处理                                                                           |
| map_metadata      | boolean | false | 是否保留视频 meta 信息，默认值 `true`                                                                        |
| audio_codec       | string  | false | 设置音频编码器,  可选: "libmp3lame", "libfdk_aac", "copy"                                                   |
| video_codec       | string  | false | 设置视频编码器, 可选: "libx264", "libtheora", "copy"                                                       |
| disable_audio     | boolean | false | 是否禁掉音频，默认 `false`                                                                                 |
| disable_video     | boolean | false | 是否禁掉视频，默认 `false`                                                                                 |
| format            | string  | false | 视频输出格式，支持 mp4/flv，默认按照原始格式输出                                                             |
| accelerate_factor | float   | false | 设置视频加速的倍数，取值范围［1.0，10.0］这个值是一个浮点数，可以是 `2.5`，表示加速 2.5 倍                     |
| save_as           | string  | false | 转码后输出的文件保存路径（同一个空间下），如果没有传递，会根据请求的参数生成一个文件名保存在原始文件同目录下 |
| return_info       | boolean | false | 是否返回视频元数据，仅当处理类型为 `video` 时有效，默认 `false`                                          |
| start_time        | string  | false | 视频起始处理时间，格式 `HH:MM:SS`，默认为视频开始 |
| end_time          | string  | false | 视频结束处理时间，格式 `HH:MM:SS`，默认为视频结束 |
| watermark_img     | string  | false | 水印图片路径，建议使用 png 格式图片，暂时不支持外链 |                   
| watermark_gravity | string  | false | 水印图片位置，支持 `NorthWest`，`North`，`NorthEast`，`West`，`Center`，`East`，`SouthWest`，`South`，`SouthEast`，默认 `NorthEast` |
| watermark_dx      | int     | false | 水印图片横坐标偏移量，单位 px，当水印位置为 NorthEast 时，默认值为 -20. 其余情况下默认值为 0 |
| watermark_dy      | int     | false | 水印图片纵坐标偏移量，单位 px，当水印位置为 NorthEast 时，默认值为 15. 其余情况下默认值为 0 |

2\. HLS 转码切片
HLS（[HTTP Live Streaming](http://zh.wikipedia.org/wiki/HTTP_Live_Streaming)） 转码切割处理除了 `hls_time` 之外其他参数与普通视频转码一致

| 参数     | 类型 | 必选 | 参数说明                              |
|----------|----------|------|---------------------------------------|
| type     | string   | true | 处理类型，进行 hls 切片时值必须为 `hls` |
| hls_time | string   | true | 指定切割的时间片长度                  |

3\. 视频截图

| 参数         | 类型 | 必选  | 参数说明                                                        |
|--------------|----------|-------|-----------------------------------------------------------------|
| type         | string   | true  | 处理类型，截图时值必须为 `thumbnail`                          |
| thumb_single | boolean  | true  | 是否仅截取单张图片，默认值 `true`                             |
| thumb_amount | integer  | false | 截图数量，当 thumb_single 参数的值为 `false` 时，本参数为必须 |
| thumb_start  | string   | false | 截图开始时间，格式为 `00:04:13`，默认值为 `00:00:00`        |
| thumb_end    | string   | false | 截图结束时间，格式为 `00:09:13`，默认值为视频结束时间         |
| thumb_scale  | string   | false | 截图尺寸，格式为 `1024:768`，默认值为视频原始尺寸             |
| thumb_format | string   | false | 截图输出格式，支持 png/jpg 格式，默认 `jpg`                   |

4\. 视频元数据获取

| 参数 | 类型 | 必选 | 参数说明                                         |
|------|----------|------|--------------------------------------------------|
| type | string   | true | 处理类型，获取视频文件元数据时值必须为 `probe` |

5\. 音频转码

| 参数 | 类型 | 必选 | 参数说明                                                     |
|------|----------|------|----------------------------------------------------------|
| type          | string  | true  | 处理类型，进行音频转码时值必须为 `audio`      |
| format        | string  | false | 音频输出格式，支持 mp4/mp3，默认按照原始格式输出|
| audio_channel | integer | false | 声道，默认 `2`                                |
| audio_bitrate | integer | false | 比特率，如 `64`                               |
| audio_vbr     | integer | false | variable bitrate [0-9]                          |
| map_metadata  | boolean | false | 是否保留视频 meta 信息，默认值 `true`         |
| start_time    | string  | false | 音频起始处理时间，格式 `HH:MM:SS`，默认为视频开始 |
| end_time      | string  | false | 音频结束处理时间，格式 `HH:MM:SS`，默认为视频结束 |

### 返回结果
成功提交处理请求之后，接口会针对提交的处理任务返回一组唯一的 `task_id`，可以根据这个 `task_id` 查询处理进度。

```
[
    '35f0148d414a688a275bf915ba7cebb2',
    '98adbaa52b2f63d6d7f327a0ff223348',
    ...
]

```

特别地，返回的 `task_id` 的顺序与提交的任务参数 `tasks` 里的任务顺序匹配对应。

## 处理结果回调通知
当视频处理完成之后，系统将根据任务提交时的 `notify_url` 参数，将处理结果以 `HTTP POST` 请求进行回调通知。

### 回调参数

| 参数        | 类型    | 参数说明                                                                                                                 |
|-------------|---------|--------------------------------------------------------------------------------------------------------------------------|
| bucket_name | string  | 视频文件所在空间名                                                                                                     |
| status_code | integer | 处理结果状态码，`200` 表示成功处理                                                                                     |
| path        | array   | 生成的目标文件保存路径                                                                                                   |
| description | string  | 处理结果描述                                                                                                             |
| task_id     | string | 任务对应的 `task_id`                                                                                                   |
| info        | string  | 视频文件的元数据信息，经过 base64 处理过之后的 JSON 字符串，仅当提视频转码任务，且处理参数中包含 `return_info` 且值为 `true` 时返回 |
| signature   | string  | 回调验证签名                                                                                                             |

回调验证签名的计算方法与认证授权的计算方式相同。特别地，因为 `path` 参数返回的数据是一个数组，
在计算签名的时候，将其使用空字符串连接之后再计算签名。

**处理结果状态码** 字段说明

`status_cod` 表示转码后的状态码

| 状态码 | 说明                                 |
|--------|--------------------------------------|
| 200    | 转码成功                             |
| 400    | 提交的任务格式不合法或者原文件不合法 |
| 406    | 用户指定的转码的参数错误             |
| 500    | 转码出现错误                         |
| 504    | 转码超时                             |

**info** 字段说明
对 `info` 的值进行 base64 解码和 JSON 转换之后，得到下面的数据：

```json
{
    streams: [
        {
            index: 0,
            video_fps: 30.0,
            type: "video",
            video_height: 150,
            codec: "h264",
            bitrate: 65304,
            duration: 12.866667,
            video_width: 150,
            codec_desc: "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
            metadata: {
                handler_name: "VideoHandler",
                language: "und"
            }
        },
        {
            index: 1,
            audio_channels: 1,
            audio_samplerate: 44100.0,
            codec_desc: "AAC (Advanced Audio Coding)",
            codec: "aac",
            bitrate: 128107, "duration: 11.971338,
            type: "audio",
            metadata: {
                handler_name: "SoundHandler",
                language: "und"
            }
        }
    ],
    format: {
        duration: 12.867,
        fullname: "QuickTime / MOV",
        bitrate: 193359.0,
        format: "mov,mp4,m4a,3gp,3g2,mj2"
    }
}
```

1\. `format` 字段说明

| 名称     | 类型    | 说明         |
|----------|---------|--------------|
| duration | float   | 文件长度     |
| fullname | string  | 格式详细名字 |
| bitrate  | float   | 总比特率     |
| format   | string  | 格式简称     |
| filesize | integer | 文件大小     |

2\. `stream` 字段说明

| 名称             | 类型    | 说明                                     |
|------------------|---------|------------------------------------------|
| index            | int     | stream 的 index                            |
| type             | string  | 流的类型                                 |
| codec            | string  | 编码解码器                               |
| codec_desc       | string  | 编码解码器的全名                         |
| duration         | float   | 流的长度                                 |
| metadata         | object  | 原数据                                   |
| bitrate          | float   | 比特率                                   |
| attached_pic     | mixed   | (0, 1 or None) is stream a poster image? |
| video_width      | integer | 视频的宽                                 |
| video_height     | integer | 视频的高                                 |
| video_fps        | integer | 视频的帧率                               |
| audio_channels   | integer | 音轨的数量                               |
| audio_samplerate | float   | 采样率                                   |


## 处理进度查询
根据 `task_id` 查询任务处理进度

### 请求地址
http://p0.api.upyun.com/status/

### 请求方法
GET，需要进行授权验证

### 请求参数

| 参数        | 类型 | 必选 | 参数说明                        |
|-------------|----------|------|---------------------------------|
| bucket_name | string   | true | 处理文件对应的空间名称          |
| task_ids    | string   | true | 任务 id 以 `,` 作为分隔符，最多 20 个 |

### 返回数据

```
{
    tasks: {
        35f0148d414a688a275bf915ba7cebb2: 100,
        98adbaa52b2f63d6d7f327a0ff223348: 20,
        c3103189fa906a5354d29bd807e8dc51: null
        ...
    }
}

```

特别的，当值为 `null` 时，表示没有查询到相关的任务信息。
