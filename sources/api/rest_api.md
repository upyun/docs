使用 REST API，您可以使用任何方式发送 HTTP 请求与 UPYUN 服务器通信。因此，你可以使用任何编程语言来使用 REST API。

> **注：**
> 文中所有 `<>` 标注的字段，均需根据你的实际情况替换（无需 `<>` 符号，仅作标注之用）

## API 基本域名

```
`v0.api.upyun.com` //自动判断最优线路
`v1.api.upyun.com` //电信线路
`v2.api.upyun.com` //联通（网通）线路
`v3.api.upyun.com` //移动（铁通）线路
```
根据实际情况任选其一

## 请求方法

```sh
curl -X GET \
    http://v0.api.upyun.com/<bucket>/<path> \
    -H "Authorization: <your_authorization>" \
    -H "Date: Wed, 29 Oct 2014 02:26:58 GMT" \
    -H "Content-Length: <content_length>"
    # 其他可选参数...

```
> **注：**
> * `Authorization` 用于认证授权
> * `Content-Length` 在 `PUT`、`POST` 请求中必须设置
> * `Date` 为[格林尼治标准时间](http://zh.wikipedia.org/wiki/%E6%A0%BC%E6%9E%97%E5%B0%BC%E6%B2%BB%E5%B9%B3%E6%97%B6)（GMT 格式）


## 认证授权

### HTTP 基本认证
REST API 支持 [HTTP 基本认证](http://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81)，其中认证所用的用户名即为已授权给你需要操作的空间的某个操作员名，认证口令为该操作员的密码。

```sh
curl -u http://vo.api.upyun.com/<bucket>
```
或者，可以自行将操作员名和操作员密码拼接后以 Base64 编码后自行在请求头部加上 `Authorization` 字段：

```sh
curl -X GET \
    http://v0.api.upyun.com/<bucket> \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ="
```

### 签名认证
为了避免 HTTP 基本认证 Base64 可逆带来的安全隐患，REST API 还支持更为安全的签名认证

__签名格式__
```
Authorization: UpYun <operator>:<signature>
```

__签名（signature）算法__

签名所需的相关信息如下表：

|    所需信息    |                                           说明                                           |
|----------------|------------------------------------------------------------------------------------------|
| METHOD         | 请求方式，如：GET、POST、PUT、HEAD 等                                                    |
| PATH           | 请求路径，需 URL 编码处理 ([RFC 1738](http://tools.ietf.org/html/rfc1738 )) |
| DATE           | 请求日期，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))                |
| CONTENT_LENGTH | 请求内容长度，除 GET 等无实体请求外，需和请求头部的 `Content-Length` 一致                |
| PASSWORD       | 空间的操作员密码                                                                         |

将上表所注的信息以 `&` 字符进行拼接（按表格从上至下的顺序）即（`METHOD&PATH&DATE&CONTENT-LENGTH&PASSWORD`），并将所得字符串进行 MD5 加密，即得我们所需的 签名（signature）

> **注:**
> 签名的有效期为30分钟。如果超过30分钟，则需重新生成签名。

如：

请求方式为 *`GET`*，URI 为 *`bucket`* 空间的子目录 *`/sub`*，请求时间 为 *` Wed, 29 Oct 2014 02:26:58 GMT`*，因为是 GET，所以 `CONTENT-LENGTH` 为 *`0`*，假设该空间有一个授权操作员名为 *`operator`*，该操作员密码为 *`password`*，那么：

签名（signature）即是对字符串 *`GET&bucket/sub&Wed, 29 Oct 2014 02:26:58 GMT&0&password`* 计算 md5 所得，即：*`5574a733d321ccd692099a0a7f2f47e1`*，因此，只需在请求头部加上如下字段即可：

```
Authorization: UpYun operator:5574a733d321ccd692099a0a7f2f47e1
```


## API

### 上传文件
通过上传接口，可以将文件上传至 UPYUN 的空间，并且，在上传文件的同时，可以设置相关的参数，进行对文件的预处理。目前，上传接口的参数分为如下几部分：

* 通用上传参数
* 图片预处理参数

**注：**
> * 参数都需以 HTTP Header Field 形式传递
> * 图片类型的空间无法上传非图片类型文件



```
PUT /<bucket>/path/to/file
```

#### 通用上传参数

所有的上传请求，都可以添加以下参数：

|      参数      | 必选 |   类型  |                                            说明                                           |
|----------------|------|---------|-------------------------------------------------------------------------------------------|
| mkdir          | 否   | Boolean | `true` 或者 `false`，表示是否要自动创建请求路径中不存在的目录                             |
| Content-MD5    | 否   | String  | 所上传文件的 MD5 校验值，用于 UPYUN 服务端校验                                            |
| Content-Type   | 否   | String  | 默认使用文件扩展名判断文件类型，可自行设置，保证准确性                                    |
| Content-Secret | 否   | String  | 对原图保护，若设置过该值，则无法直接访问原图，需要在原图 URL 的基础上加上密钥值才能访问。 |

> **注：**
> * 设置 `Content-Secret` 密钥后，原图将被保护，不能被直接访问，只有缩略图是允许被直接访问的
> * 设置密钥后，若需访问原图，需要在 URL 后加上「缩略图间隔符号」和「访问密钥」（如： 当缩略图间隔符为 *`!`*，访问密钥为 *`secret`*，那么，原图访问方式即为： http://bucket.b0.upaiyun.com/sample.jpg!secret）

#### 图片预处理参数
当上传图片文件至图片类型的空间时候，除了通用的上传参数外，UPYUN 还提供了以下可选参数，用于对上传的图片进行预处理：

|          参数          |                                                说明                                                |
|------------------------|----------------------------------------------------------------------------------------------------|
| `x-gmkerl-type`        | 缩略类型（见下表「x-gmkerl-type 值可选列表」）                                                     |
| `x-gmkerl-value`       | 缩略类型对应的参数值，单位为像素，须搭配 x-gmkerl-type 使用（见下附注「`x-gmkerl-value` 的使用」）|
| `x-gmkerl-quality `    | **默认 `95`** 图片压缩质量，可选（1~100）                                                          |
| `x-gmkerl-unsharp`     | **默认 `true`** 图片锐化                                                                           |
| `x-gmkerl-thumbnail`   | 在 UPYUN 管理平台创建好缩略图版本该缩略方式包含了所需的缩略参数，参数更简洁，使用更方便            |
| `x-gmkerl-exif-switch` | **默认 `false` 即删除** 是否保留原图的 EXIF 信息                                                   |


**x-gmkerl-type 值可选列表**

|           值           |                含义                |
|------------------------|------------------------------------|
| fix\_width              | 限定宽度，高度自适应                |
| fix\_height             | 限定高度，宽度自适应                |
| fix\_width\_or\_height | 限定宽度和高度，宽高不足时不缩放   |
| fix\_both               | 固定宽度和高度，宽高不足时强行缩放 |
| fix\_max                | 限定最长边，短边自适应              |
| fix\_min                | 限定最短边。长边自适应              |
| fix\_scale             | 等比例缩放（1-99                  |

**注：`x-gmkerl-value` 的使用**

> * 若 `x-gmkerl-type` 指定为 `fix_width_or_height` 或 `fix_both`，则，`x-gmkerl-value` 值的格式为 `<width>x<height>`，如 `480x576`
> * 若 `x-gmkerl-type`  为其他的类型，则 `x-gmkerl-value` 只需指定单个数字，如 `42`，意为高宽同为 42


**返回信息**

所有上传请求，均会在请求响应中返回相应的状态码，相应结果的状态码及相关含义，请参阅「[REST API 状态码表](#REST API 状态码表)」


当请求将图片文件上传至图片类型的空间时，响应中会包含相关的预处理信息（如果有设置相关参数的话）
，例如：

```
> HTTP/1.1 200 OK
> x-upyun-width: 50
> x-upyun-height: 50
> x-upyun-frames: 1
> x-upyun-file-type: JPEG
```
其中，`x-upyun-` 开头的头部信息即为所上传图片的相关信息。

### 下载文件

```
GET /<bucket>/path/to/file
```

返回信息:

1. 文件存在: 返回 `200`, HTTP body 中返回文件内容
2. 文件不存在: 返回 `404`

### 获取文件信息

```
HEAD /<bucket>/path/to/file
```

返回信息:

1. 文件存在: 返回 `200`, 返回头信息如下所示:

    |       参数        |  说明 |
    | ----------------- | ----- |
    | x-upyun-file-type | 文件为 `file`，文件夹为 `folder` |
    | x-upyun-file-size | 文件大小 |
    | x-upyun-file-date | 文件创建时间 |

2. 文件不存在: 返回 `404`

### 删除文件

```
DELETE /<bucket>/path/to/file
```

返回信息:

1. 文件存在: 返回 `200`
2. 文件不存在: 返回 `404`


### 创建目录

```
POST /<bucket>/path/to/folder
```

|  参数  | 必选  | 说明 |
| ------ | ----- | ----- |
| folder |  是   | `true` 或者 `false`，`true` 表示当前请求为创建目录 |
| mkdir  |  否   | `true` 或者 `false`，`true` 表示自动创建不存在的父级目录，最多允许创建 10 级目录 |


返回信息:

1. 创建成功: 返回 `200`
2. 失败: 返回 `403`

### 删除目录

```
DELETE /<bucket>/path/to/folder
```

返回信息:

1. 目录存在: 返回 `200`
2. 目录不存在: 返回 `404`

### 获取目录文件列表

```
GET /<bucket>/path/to/folder
```

返回信息:

1. 成功: 返回 `200`。HTTP body 内容为各个文件的属性

如:

```
foo.jpg\tN\t4237\t1415096225\nbar\tF\t423404\t1415096260
```

> **注：**
> 文件/目录之间以`\n`分隔，属性之间以:「文件\t类型\t大小\t最后修改时间」 顺序以`\t`分隔
> 类型可选值: `N`(文件), `F`(目录)

2. 文件列表不存在: 返回 `404`

### 获取空间使用情况

```
GET /<bucket>/?usage
```

返回信息:

1. 成功: 返回 `200`。HTTP body 内容为空间的使用量（单位为`Byte`）
2. 文件列表不存在: 返回 `404`


### REST API 状态码表

| HTTP状态码 |             返回代码            |                                                含义                                               |
|------------|---------------------------------|---------------------------------------------------------------------------------------------------|
|        200 | OK                              | 操作成功                                                                                          |
|        400 | Bad Request                     | 错误请求(如 URL 缺少空间名)                                                                       |
|        401 | Unauthorized                    | 访问未授权                                                                                        |
|        401 | Sign error                      | 签名错误(操作员和密码,或签名格式错误)                                                             |
|        401 | Need Date Header                | 发起的请求缺少 Date 头信息                                                                        |
|        401 | Date offset error               | 发起请求的服务器时间错误，请检查服务器时间是否与世界时间一致                                      |
|        403 | Not Access                      | 权限错误(如非图片文件上传到图片空间)                                                              |
|        403 | File size too max               | 单个文件超出大小(100Mb 以内)                                                                      |
|        403 | Not a Picture File              | 图片类空间错误码，非图片文件或图片文件格式错误。针对图片空间只允许上传 jpg/png/gif/bmp/tif 格式。 |
|        403 | Picture Size too max            | 图片类空间错误码，图片尺寸太大。针对图片空间，图片总像素在 200000000 以内。                       |
|        403 | Bucket full                     | 空间已用满                                                                                        |
|        403 | Bucket blocked                  | 空间被禁用,请联系管理员                                                                           |
|        403 | User blocked                    | 操作员被禁用                                                                                      |
|        403 | Image Rotate Invalid Parameters | 图片旋转参数错误                                                                                  |
|        403 | Image Crop Invalid Parameters   | 图片裁剪参数错误                                                                                  |
|        404 | Not Found                       | 获取文件或目录不存在；上传文件或目录时上级目录不存在                                              |
|        406 | Not Acceptable(path)            | 目录错误（创建目录时已存在同名文件；或上传文件时存在同名目录)                                     |
|        503 | System Error                    | 系统错误                                                                                          |
