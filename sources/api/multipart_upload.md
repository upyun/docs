

在上传大文件的时候，面对有可能因为网络质量等其他原因而造成的上传失败，使用分块上传和断点续传机制就显得非常有必要。

## API 基本域名

```
m0.api.upyun.com
```

## 名称概念

* 文件分块：直接切分二进制文件成小块。具体文件的每个分块的大小以及分块数目，可以根据自己情况调整（eg. 10MB 的文件，可以定每个分块 1MB，则分块数目即为 10 块）
* 请求参数（Request Params）： 即是指常规的请求中所带参数
* 参数组（Meta Params）： 包含文件上传存储及校验相关的必要信息，也是每次请求时候计算 `policy` 和 `signature` 所需的参数组。
* signature： 校验签名
* policy： 存储/校验信息


## signature 和 policy 算法

signature 和 policy 算法与表单 API 相同，请参考 [这里](/api/form_api/#policy)。

> 注：在分块上传数据和合并文件请求中，需要将此处所用『表单 API 验证密钥』替换为上次请求返回的 `token_secret`，再进行 `signature` 计算


假设一个请求所需的的策略参数如下表:

|键|值|
|----|---|
|path|/demo.png|
|expiration| 1409200758|
|file_blocks|1|
|file_hash|b1143cbc07c8e768d517fa5e73cb79ca|
|file_size|653252|

另设，该空间『表单 API 验证密钥』为：`cAnyet74l9hdUag34h2dZu8z7gU=`

计算 policy 和 signature 相应步骤所得结果如下：

第一步，将参数组转换为 JSON 字符串后：
```
{"path":"/demo.png","expiration":1409200758,"file_blocks":1,"file_size":653252,"file_hash":"b1143cbc07c8e768d517fa5e73cb79ca"}
```

第二步，将第一步所得字符串 base64 编码后：
```
eyJwYXRoIjoiL2RlbW8ucG5nIiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwiZmlsZV9ibG9ja3MiOjEsImZpbGVfc2l6ZSI6NjUzMjUyLCJmaWxlX2hhc2giOiJiMTE0M2NiYzA3YzhlNzY4ZDUxN2ZhNWU3M2NiNzljYSJ9
```
第三步，第二步结果与『表单 API 验证密钥』用 `&` 拼接后：
```
eyJwYXRoIjoiL2RlbW8ucG5nIiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwiZmlsZV9ibG9ja3MiOjEsImZpbGVfc2l6ZSI6NjUzMjUyLCJmaWxlX2hhc2giOiJiMTE0M2NiYzA3YzhlNzY4ZDUxN2ZhNWU3M2NiNzljYSJ9&cAnyet74l9hdUag34h2dZu8z7gU=
```

> 注：在分块上传数据和合并文件请求中，需要将此处所用『表单 API 验证密钥』替换为上次请求返回的 `token_secret`，再进行第四步计算

第四步，将第三步所得字符计算 md5 后即得到 signature：
```
98cf3f74698bfec04886eee446439ae3
```

--------

## 上传步骤

表单分块上传的整个步骤分为：

1. 初始化上传
2. 分块上传数据
3. 文件合并完成上传


### 初始化上传

```
POST /<bucket-name>/
```

#### 请求类型

```
Content-Type: application/x-www-form-urlencoded
```

#### 请求参数

|参数          |参数类型       |必选       |参数说明        |
|-------------|--------------|----------|--------------|
|policy       |string        | true     |文件存储/校验信息，见[算法](#signature)|
|signature    |string        | true     |校验签名，见[算法](#policy)       |

#### 参数组

| 参数  | 参数说明|
|----|----|
|path|文件存储路径|
|expiration| 授权有效期，Unix UTC 时间戳|
|file_blocks| 本次所上传的文件的分块数目|
|file_hash|所上传文件整个文件的 md5 hash 值|
|file_size|所上传文件整个文件的大小(单位 Byte)|

> 注：请选取适当的 `file_blocks` 以保证分块的大小** 不超过 5M **，并且除最后一块外其余分块** 不小于 100K **。

其他可选参数如下：

| 参数 |  可选性 | 备注 |
|---|---|---|
| allow-file-type      | 否   | 文件类型限制，制定允许上传的文件扩展名                                                              |
| content-length-range | 否   | 文件大小限制，格式：`min,max`，单位：字节，如 `102400,1024000`，允许上传 100Kb～1Mb 的文件          |
| content-md5          | 否   | 所上传的文件的 MD5 校验值，又拍云根据此来校验文件上传是否正确                                       |
| content-secret       | 否   | 原图访问密钥 [\[表单 API 注 2\]](/api/form_api/#note2)                                                 |
| content-type         | 否   | 又拍云默认根据扩展名判断，手动指定可提高精确性                                                      |
| image-width-range    | 否   | 图片宽度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传宽度为 0～1024px 之间               |
| image-height-range   | 否   | 图片高度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传高度在 0～1024px 之间               |
| notify-url           | 否   | 异步通知 URL，见 [\[表单 API 通知规则\]](/api/form_api/#notify_return)                                                      |
| return-url           | 否   | 同步通知 URL，见 [\[表单 API 通知规则\]](/api/form_api/#notify_return)                                                      |
| x-gmkerl-thumb       | 否   | 图片处理参数，见 [上传作图](/cloud/image/#_2)                                                       |
| x-gmkerl-type        | 否   | `get_meta`（获取图片信息）或 `get_theme_color`（主题色提取）                                        |
| x-gmkerl-extract-color-count       | 否   |     主题色提取颜色数量，默认 `256`                                                    |
| x-gmkerl-extract-format     | 否   |   主题色提取格式，默认 `hex`                                                                 |
| ext-param            | 否   | 额外参数，UTF-8 编码，并小于 255 个字符 [\[表单 API 注 5\]](/api/form_api/#note5)                            |
| apps                 | 否   | json 格式的异步处理任务列表。见 [异步处理任务](/api/form_api/#_4)                                   |
| *x-gmkerl-thumbnail* **DEPRECATED**   | *否*   | *缩略图版本名称，仅支持图片类空间，可搭配其他 `x-gmkerl-*` 参数使用 [\[表单 API 注 3\]](/api/form_api/#note3)* |
| *x-gmkerl-type*  **DEPRECATED**       | *否*   | *缩略类型 [\[表单 API 注 4\]](/api/form_api/#note4)*                                                           |
| *x-gmkerl-value*  **DEPRECATED**      | *否*   | *缩略类型对应的参数值 [\[表单 API 注 4\]](/api/form_api/#note5)*                                               |
| *x-gmkerl-quality*  **DEPRECATED**    | *否*   | ***默认 95**缩略图压缩质量*                                                                           |
| *x-gmkerl-unsharp*   **DEPRECATED**   | *否*   | ***默认锐化（true）**是否进行锐化处理*                                                                |
| *x-gmkerl-rotate*  **DEPRECATED**     | *否*   | *图片旋转（顺时针），可选：`auto，90，180，270` 之一*                                           |
| *x-gmkerl-crop*  **DEPRECATED**       | *否*   | *图片裁剪，格式：`<w>x<h>a<x>a<y>`。 其中 w, h 分别表示裁剪后的宽和高，x, y 表示左上角坐标。如 100x100a0a0。*         |
| *x-gmkerl-exif-switch* **DEPRECATED** | *否*   | *是否保留 EXIF 信息，仅在搭配 `x-gmkerl-crop，x-gmkerl-type，x-gmkerl-thumbnail` 时有效。*        |


#### 返回结果

```js
{
  save_token: '68640c7ab4120b4992907cabc6ea2c48',
  token_secret: "89a3d8d8a26a8adb32a3651a63c0d5cc",
  bucket_name: 'example',
  blocks: 5,
  status: [0,0,0,0,0],
  expired_at: 1401784219
}
```

**返回参数说明**

|参数        |说明          |
|-----------|-------------|
|save_token |分块上传索引 key，下一步分块上传数据时必须携带本参数  |
|token_secret| 用于之后请求计算 `policy` 和 `signature` 所用，代替『表单 API 验证密钥』 |
|bucket_name|文件保存空间                                |
|blocks     |文件分块数量                                  |
|status     |分块文件上传状态，`1`表示已完成上传，`0`表示分块未完成上传。数组索引表示分块序号，** 从 0 开始 **；      |
|expired_at |当前分块上传数据有效期，超过有效期之后数据将会被清理  |


### 分块上传数据

```
POST /<bucket-name>/
```

#### 请求类型

```
Content-Type: multipart/form-data
```

#### 请求参数

|参数          |参数类型       |必选       |参数说明        |
|-------------|--------------|----------|--------------|
|policy       |String        | true     |文件存储/校验信息，见[算法](#policy)|
|signature    |String        | true     |校验签名，见[算法](#signature)        |
|file         |Blob         | true     |上传的文件分块数据  |

#### 参数组

|参数|参数说明|
|---|---|
|save_token|即初始化上传请求返回结果中的 `save_token`|
|expiration| Unix UTC 时间戳，授权有效期，超过这个时间之后授权将会失效|
| block_index|该分块在完整文件中的分块索引|
| block_hash|该分块文件的 md5 值|

#### 返回结果

上传成功返回:

```
{
  save_token: '68640c7ab4120b4992907cabc6ea2c48',
  token_secret: "89a3d8d8a26a8adb32a3651a63c0d5cc",
  bucket_name: 'example',
  blocks: 5,
  status: [1,1,1,1,0],
  expired_at: 1401784219
}
```

**返回参数说明**

|参数        |说明          |
|-----------|-------------|
|save_token |分块上传索引 key，下一步分块上传数据时必须携带本参数  |
|token_secret| 用于之后请求计算 `policy` 和 `signature` 所用，代替『表单 API 验证密钥』 |
|bucket_name|文件保存空间                                |
|blocks     |文件分块数量                                  |
|status     |分块文件上传状态，`1`表示已完成上传，`0`表示分块未完成上传。数组索引表示分块序号，** 从 0 开始 **；      |
|expired_at |当前分块上传数据有效期，超过有效期之后数据将会被清理  |

### 文件合并完成上传

```
POST /<bucket-name>/
```

#### 请求类型

```
Content-Type: application/x-www-form-urlencoded
```

#### 请求参数

|参数          |参数类型       |必选       |参数说明        |
|-------------|--------------|----------|--------------|
|policy       |string        |true     |文件存储/校验信息，见[算法](#policy)|
|signature    |string        |true     |校验签名，见[算法](#signature)      |

#### 参数组

|参数|参数说明|
|----|----|
|save_token|即初始化上传请求返回结果中的 `save_token`|
|expiration|Unix UTC 时间戳，授权有效期，超过这个时间之后授权将会失效|



#### 返回结果

表单 API 结合 `return-url` 和 `notify-url` 两个，有三种方式将上传结果返回:
* HTTP Body 同步返回；在没有传递 `return-url` 时，都会使用这种方式以 JSON 字符串的方式返回数据到客户端；
* 客户端同步跳转回调；传递 `return-url` 参数时，会通过 HTTP 302 的方式在客户端跳转到回调地址，通过 GET 参数传递；
* 服务器异步队列通知；传递了 `notify-url` 参数时，会通过又拍云的服务端异步队列发送 POST 请求以标准的*x-www-form-urlencoded*表单进行通知，如果回调通知失败，再重试 10 次（累计一天时间）之后将丢弃这条回调任务；

三种方式将返回/提交一样的数据，签名验证机制也完全一样，以 HTTP Body 返回的数据为例上传成功返回：

```js
{
  bucket_name: 'example',
  path: '/demo.png',
  mimetype: 'image/png',
  file_size: 65422,
  image_width: 800,
  image_height: 600,
  image_frames: 1,
  image_type: 'PNG',
  last_modified: 1402021925,
  signature: 'c0ec4d6b0bb0515b4b889a12436118aa'
}
```

**返回参数说明**

|参数          |说明                                                           |
|-------------|--------------------------------------------------------------|
|bucket_name  | 空间名                                                      |
|path         |文件保存路径                                                     |
|mimetype |保存文件的 content-type                                           |
|file_size |文件大小                                                      |
|image_width  |图片宽度，仅图片返回                                           |
|image_height |图片高度，仅图片返回                                           |
|image_frames |图片帧数，仅图片返回                                           |
|image_type   |图片类型，仅图片返回                                         |
|last_modified|文件最后修改时间，既本次上传操作成功创建文件的时间                      |
|signature    |校验签名，用于校验返回数据的合法性 ，生成方式参见签名算法                |
|ext-param    |初始化时指定的额外参数（如果没有指定则不返回这个字段）              |


同步回调方式会通过 HTTP 302 的方式进行跳转：

```
> Access-Control-Allow-Methods:PUT,POST
> Access-Control-Allow-Origin:*
> X-Powered-By:Crocodile/0.0.1
> ...
> Location: http://www.example.com/callback/return.php?path=%2Fdemo.png&content_type=image%2Fpng&file_size=65422&image_width=800&image_height=600&image_frames=1&last_modified=1402021925&signature=bc0b1ee186486c050146616a6ec74747
```

### 错误代码说明

请参考 [API 错误码表](/api/errno/)
