> **Beta:**
> 该 API 仍处于 Beta 阶段

在`上传大文件` 的时候，面对有可能因为网络质量等其他原因而造成的上传失败，使用分块上传和断点续传机制就显得非常有必要。

## API 基本域名

```
m0.api.upyun.com
```


## 名称概念

* 文件分块：直接切分二进制文件成小块。具体文件的每个分块的大小以及分块数目，可以根据自己情况调整（eg. 10MB 的文件，可以定每个分块 1MB，则分块数目即为10块）
* 请求参数(Request Params): 即是指常规的请求中所带参数
* 参数组(Meta Params): 包含文件上传存储及校验相关的必要信息，也是每次请求时候计算 `policy` 和 `signature` 所需的参数组。
* signature： 校验签名
* policy： 存储/校验信息


## signature和policy算法
### `signature`
1. 将所需的`参数组`键值对按照键的字典顺序排列，并连接成为字符串

2. 将第 1 步中所得字符串与您的`表单API`（可登录 UPYUN 官网获取）字符串拼接

> 注：在分块上传数据和合并文件请求中，需要将此处所用`表单API`替换为上次请求返回的 `token_secret`，再进行 `signature` 计算

3. 将第 2 步中所的的字符串计算 md5，所得即为 `signature`


### `policy`
1. 将请求所需的`参数组`键值对转换为 JSON 字符串
2. 将第 1 步中所得字符串进行 base64 编码，所得即为 `policy`

eg.
假设一个请求所需的的策略参数如下表:

|键|值|
|----|---|
|path|/demo.png|
|expiration| 1409200758|
|file_blocks|1|
|file_hash|b1143cbc07c8e768d517fa5e73cb79ca|
|file_size|653252|

另设，该空间表单 API 为：`cAnyet74l9hdUag34h2dZu8z7gU=`

则，
__计算 `signature` 相应步骤所的结果如下：__

第一步，将`参数组`键值对排序后所得：
```js
expiration1409200758file_blocks1file_hashb1143cbc07c8e768d517fa5e73cb79cafile_size653252path/demo.png
```

第二步，第一步结果加上表单API后：
```
expiration1409200758file_blocks1file_hashb1143cbc07c8e768d517fa5e73cb79cafile_size653252path/demo.pngcAnyet74l9hdUag34h2dZu8z7gU=
```
> 注：在分块上传数据和合并文件请求中，需要将此处所用`表单API`替换为上次请求返回的 `token_secret`，再进行 `signature` 计算

第三步，将第二步所得字符计算 md5 后：
```
a178e6e3ff4656e437811616ca842c48
```

__计算 `policy` 相应步骤所得结果如下：__

第一步，将`参数组`转换为 JSON 字符串后：
```
{"path":"/demo.png","expiration":1409200758,"file_blocks":1,"file_size":653252,"file_hash":"b1143cbc07c8e768d517fa5e73cb79ca"}
```

第二步，将第一步所得字符串 base64 编码后：
2.
```
eyJwYXRoIjoiL2RlbW8ucG5nIiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwiZmlsZV9ibG9ja3MiOjEsImZpbGVfc2l6ZSI6NjUzMjUyLCJmaWxlX2hhc2giOiJiMTE0M2NiYzA3YzhlNzY4ZDUxN2ZhNWU3M2NiNzljYSJ9
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
Content-Type : application/x-www-form-urlencoded
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
|expiration| 授权有效期，Unix 时间戳|
|file_blocks| 本次所上传的文件的分块数目|
|file_hash|所上传文件整个文件的 md5 hash值|
|file_size|所上传文件整个文件的大小(单位 bit)|

其他可选参数如下：

| 参数 | 说明 | 可选性 | 备注
|---|---|---|---
| allow-file-type      | 否   | 文件类型限制，制定允许上传的文件扩展名                                                              |
| content-length-range | 否   | 文件大小限制，格式：`min,max`，单位：字节，如 `102400,1024000`，允许上传 100Kb～1Mb 的文件          |
| content-md5          | 否   | 所上传的文件的 MD5 校验值，UPYUN 根据此来校验文件上传是否正确                                       |
| content-secret       | 否   | 原图访问密钥 [\[表单 API 注2\]](/api/form_api/#note2)                                                 |
| content-type         | 否   | UPYUN 默认根据扩展名判断，手动指定可提高精确性                                                      |
| image-width-range    | 否   | 图片宽度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传宽度为 0～1024px 之间               |
| image-height-range   | 否   | 图片高度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传高度在 0～1024px 之间               |
| notify-url           | 否   | 异步通知 URL，见 [\[表单 API 通知规则\]](/api/form_api/#notify_return)                                                      |
| return-url           | 否   | 同步通知 URL，见 [\[表单 API 通知规则\]](/api/form_api/#notify_return)                                                      |
| x-gmkerl-thumbnail   | 否   | 缩略图版本名称，仅支持图片类空间，可搭配其他 `x-gmkerl-*` 参数使用 [\[表单 API 注3\]](/api/form_api/#note3) |
| x-gmkerl-type        | 否   | 缩略类型 [\[表单 API 注4\]](/api/form_api/#note4)                                                           |
| x-gmkerl-value       | 否   | 缩略类型对应的参数值 [\[表单 API 注4\]](/api/form_api/#note5)                                               |
| x-gmkerl-quality     | 否   | **默认 95**缩略图压缩质量                                                                           |
| x-gmkerl-unsharp     | 否   | **默认锐化（true）**是否进行锐化处理                                                                |
| x-gmkerl-rotate      | 否   | 图片旋转（顺时针），可选：`auto`，`90`，`180`，`270` 之一                                           |
| x-gmkerl-crop        | 否   | 图片裁剪，格式：`x,y,width,height`，均需为正整型                                                    |
| x-gmkerl-exif-switch | 否   | 是否保留 exif 信息，仅在搭配 `x-gmkerl-crop`，`x-gmkerl-type`，`x-gmkerl-thumbnail` 时有效。        |
| ext-param            | 否   | 额外参数，UTF-8 编码，并小于 255 个字符 [\[表单 API 注5\]](/api/form_api/#note5)                            |


#### 返回结果

```js
{
  save_token: '68640c7ab4120b4992907cabc6ea2c48',
  token_secret: "89a3d8d8a26a8adb32a3651a63c0d5cc",
  bucket_name: 'example',
  blocks: 5,
  status: [1,0,1,0,0],
  expired_at: 1401784219
}
```

**返回参数说明**

|参数        |说明          |
|-----------|-------------|
|save_token |分块上传索引key，下一步分块上传数据时必须携带本参数  |
|token_secret| 用于之后请求计算 `policy` 和 `signature` 所用 |
|bucket_name|文件保存空间                                |
|blocks     |文件分块数量                                  |
|status     |分块文件上传状态，`true`表示已完成上传，`false`表示分块未完成上传。数组索引表示分块序号，从0开始；      |
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
|expiration| Unix时间戳，授权有效期，超过这个时间之后授权将会失效|
| block_index|该分块在完整文件中的分块索引|
| block_hash|该分块文件的 md5 值|

#### 返回结果

上传成功返回:

```
{
  save_token: '68640c7ab4120b4992907cabc6ea2c48',
  bucket_name: 'example',
  blocks: 5,
  status: [1,1,1,1,0],
  expired_at: 1401784219
}
```

**返回参数说明**

|参数        |说明          |
|-----------|-------------|
|save_token |分块上传索引key，下一步分块上传数据时必须携带本参数  |
|token_secret| 用于之后请求计算 `policy` 和 `signature` 所用 |
|bucket_name|文件保存空间                                |
|blocks     |文件分块数量                                  |
|status     |分块文件上传状态，`true`表示已完成上传，`false`表示分块未完成上传。数组索引表示分块序号，从0开始；      |
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
|expiration|Unix时间戳，授权有效期，超过这个时间之后授权将会失效|



#### 返回结果

表单API结合 `return_url` 和 `notify_url` 两个，有三种方式将上传结果返回:
* HTTP Body 同步返回；在没有传递 `return_url` 时，都会使用这种方式以JSON字符串的方式返回数据到客户端；
* 客户端同步跳转回调；传递 `return_url` 参数时，会通过HTTP 302的方式在客户端跳转到回调地址，通过GET参数传递；
* 服务器异步队列通知；传递了 `notify_url` 参数时，会通过UPYUN的服务端异步队列发送POST请求以标准的*x-www-form-urlencoded*表单进行通知，如果回调通知失败，再重试10次（累计一天时间）之后将丢弃这条回调任务；

三种方式将返回/提交一样的数据，签名验证机制也完全一样，以HTTP Body返回的数据为例上传成功返回：

```js
{
  bucket_name: 'example',
  path: '/demo.png',
  content_type: 'image/png',
  content_length: 65422,
  image_width: 800,
  image_height: 600,
  image_frames: 1,
  last_modified: 1402021925,
  signature: 'c0ec4d6b0bb0515b4b889a12436118aa'
}
```

**返回参数说明**

|参数          |说明                                                           |
|-------------|--------------------------------------------------------------|
|path         |文件保存路径                                                     |
|content_type |保存文件的content-type                                           |
|content_length |文件大小                                                      |
|image_width  |文件宽度，仅图片空间返回                                           |
|image_height |文件高度，仅图片空间返回                                           |
|image_frames |文件帧数，仅图片空间返回                                           |
|last_modified|文件最后修改时间，既本次上传操作成功创建文件的时间                      |
|signature    |校验签名，用于校验返回数据的合法性 ，生成方式参见签名算法                |


同步回调方式会通过HTTP 302的方式进行跳转：

```
> Access-Control-Allow-Methods:PUT,POST
> Access-Control-Allow-Origin:*
> X-Powered-By:Crocodile/0.0.1
> ...
> Location: http://www.example.com/callback/return.php?path=%2Fdemo.png&content_type=image%2Fpng&content_length=65422&image_width=800&image_height=600&image_frames=1&last_modified=1402021925&signature=bc0b1ee186486c050146616a6ec74747
```

### 错误代码说明

#### HTTP Status Codes
所有API请求都会根据情况返回适当的HTTP状态

| 代码      | 文本内容     | 描述                             |
| --------- | ------------ | -------------------------------- |
| 200       | OK           | 所有成功的请求都将返回这个状态   |
| 201       | Created      | 成功创建了新的资源 |
| 302       | Created      | 成功创建资源并需要进行同步回调跳转时 |
| 400       | Bad Request  | 无效请求，通常为表单数据封装错误     |
| 401       | Unauthorized | 参数错误、授权无效或过期                   |
| 403       | Forbidden    | 文件处理异常 |
| 404       | NotFound     | 请求的接口或者数据不存在时|
| 409       | Conflict     | 请求并发冲突 |
| 500       | Internal Server Error | 程序出现异常、错误时 |

#### 错误消息
所有接口的错误信息都要JSON格式返回，返回值格式

```
{
  error_code: "40401",
  path: '/demo.zip',
  message : "Bucket NotFound."
}
```

#### 错误代码
| 代码      | 文本内容                      | 描述                             |
| -----------| ------------------------- | -------------------------------- |
| 40001      |                           | 参数错误，详情见返回的错误信息|
| 40101      | Auth failed                | 授权错误                         |
| 40301      | Form API disabled.         | 表单API被禁用                      |
| 40302      | Invalid file blocks.       | 文件分块数量超过限制               |
| 40303      | Invalid file hash.        | 文件hash值错误                      |
| 40304      | Missing file.             | 文件分块信息不存在                  |
| 40305      | Block hash error.          | 文件分块hash校验失败         |
| 40306      | File hash error.          | 文件拼接完成后hash校验失败         |
| 40307      | Process failed.          | 文件处理失败                      |
| 40401      | Bucket NotFound.           | 空间不存在                         |
| 40402      | Blocks NotFound.           | 文件分块信息不存在                  |
