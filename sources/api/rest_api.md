使用 REST API，您可以使用任何方式发送 HTTP 请求与 UPYUN 服务器通信。因此，你可以使用任何编程语言来使用 REST API。

REST API 支持 HTTP 和 HTTPS 协议，您可以选择最优的方式提交请求。

> **注：**
> - 文中所有 `<>` 标注的字段，均需根据你的实际情况替换（无需 `<>` 符号，仅作标注之用）
> - 文中请求参数若无特殊说明，均表示通过 HTTP 请求头以 `key: value` 的形式传递

## API 基本域名

```
v0.api.upyun.com //自动判断最优线路
v1.api.upyun.com //电信线路
v2.api.upyun.com //联通（网通）线路
v3.api.upyun.com //移动（铁通）线路
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
>
> * `Authorization`, `Date` 这两个参数是必须的
> * `Authorization` 用于认证授权
> * `Content-Length` 在 `PUT`、`POST` 请求中必须设置，UPYUN 不支持 chunked 形式上传。
> * `Date` 为[格林尼治标准时间](http://zh.wikipedia.org/wiki/%E6%A0%BC%E6%9E%97%E5%B0%BC%E6%B2%BB%E5%B9%B3%E6%97%B6)（GMT 格式）


## 认证授权

### HTTP 基本认证
REST API 支持 [HTTP 基本认证](http://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81)，其中认证所用的用户名即为已授权给你需要操作的空间的某个操作员名，认证口令为该操作员的密码。

```sh
curl -u http://v0.api.upyun.com/<bucket>
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

将 `PASSWORD` md5 之后（我们暂且将其记作 `PASSWORD_MD5`），上表所注的其他信息以 `&` 字符进行拼接（按表格从上至下的顺序）即（`METHOD&PATH&DATE&CONTENT-LENGTH&PASSWORD_MD5`），并将所得字符串进行 MD5 加密，即得我们所需的 签名（signature）

> **注：**
> 签名的有效期为 30 分钟。如果超过 30 分钟，则需重新生成签名。

如：

请求方式为 *`GET`*，URI 为 *`bucket`* 空间的子目录 *`/sub`*，请求时间 为 *` Wed, 29 Oct 2014 02:26:58 GMT`*，因为是 GET，所以 `CONTENT-LENGTH` 为 *`0`*，假设该空间有一个授权操作员名为 *`operator`*，该操作员密码为 *`password`*（密码 md5 后为 *`5f4dcc3b5aa765d61d8327deb882cf99`*），那么：

签名（signature）即是对字符串 *`GET&/bucket/sub&Wed, 29 Oct 2014 02:26:58 GMT&0&5f4dcc3b5aa765d61d8327deb882cf99`* 计算 md5 所得，即：*`03db45e2904663c5c9305a9c6ed62af3`*，因此，只需在请求头部加上如下字段即可：

```
Authorization: UpYun operator:03db45e2904663c5c9305a9c6ed62af3
```


## API

### 上传文件
通过上传接口，可以将文件上传至 UPYUN 的空间，并且，在上传文件的同时，可以设置相关的参数，进行对文件的预处理。目前，上传接口的参数分为如下几部分：

* 通用上传参数
* 图片预处理参数
* 水印参数

> **注：** 参数都需以 HTTP Header Field 形式传递


```
PUT /<bucket>/path/to/file
```

#### 通用上传参数

所有的上传请求，都可以添加以下参数：

|      参数      | 必选 |   类型  |                                            说明                                           |
|----------------|------|---------|-------------------------------------------------------------------------------------------|
| Content-MD5    | 否   | String  | 所上传文件的 MD5 校验值，用于 UPYUN 服务端校验                                            |
| Content-Type   | 否   | String  | 默认使用文件扩展名判断文件类型，可自行设置，保证准确性                                    |
| Content-Secret | 否   | String  | 对原图保护，若设置过该值，则无法直接访问原图，需要在原图 URL 的基础上加上密钥值才能访问   |

> **注：**
>
> * 设置 `Content-Secret` 密钥后，原图将被保护，不能被直接访问，只有缩略图是允许被直接访问的。
> * 设置密钥后，若需访问原图，需要在 URL 后加上「缩略图间隔符号」和「访问密钥」（如： 当缩略图间隔符为 *`!`*，访问密钥为 *`secret`*，那么，原图访问方式即为： *`http://bucket.b0.upaiyun.com/sample.jpg!secret`*）

#### 图片预处理参数
当上传图片文件至空间时，除了通用的上传参数外，UPYUN 还提供了以下可选参数，用于对上传的图片进行预处理：

| 参数                             | 说明                                                                                               |
|----------------------------------|----------------------------------------------------------------------------------------------------|
| `x-gmkerl-type`                  | 缩略类型（具体请参见注 1）                                                     |
| `x-gmkerl-value`                 | 缩略类型对应的参数值，单位为像素，须搭配 `x-gmkerl-type` 使用（具体请参见注 2） |
| `x-gmkerl-quality `              | 图片质量，默认 `95`，可选（1~100）                                                              |
| `x-gmkerl-unsharp`               | 图片锐化，默认 `true` 即开启锐化                                                                           |
| `x-gmkerl-thumbnail`             | 在 UPYUN 管理平台创建好缩略图版本该缩略方式包含了所需的缩略参数，参数更简洁，使用更方便            |
| `x-gmkerl-exif-switch`           | 是否保留原图的 EXIF 信息，默认 `false` 即不保留                                                   |
| `x-gmkerl-crop`                  | 格式为 `x,y,width,height`，如：`0,0,100,200` （具体请参见注 3）                   |
| `x-gmkerl-rotate`                | 旋转角度，目前只允许设置：`auto，90，180，270` （具体请参见注 4）         |


**注 1：x-gmkerl-type 值可选列表**

|           值           |                含义                |
|------------------------|------------------------------------|
| `fix_width`              | 限定宽度，高度自适应                |
| `fix_height`             | 限定高度，宽度自适应                |
| `fix_width_or_height`    | 限定宽度和高度，宽高不足时不缩放   |
| `fix_both`               | 固定宽度和高度，宽高不足时强行缩放 |
| `fix_max`                | 限定最长边，短边自适应              |
| `fix_min`                | 限定最短边，长边自适应              |
| `fix_scale`              | 等比例缩放（1-99）                  |

**注 2：x-gmkerl-value 值的使用**

> * 若 `x-gmkerl-type` 指定为 `fix_width_or_height` 或 `fix_both`，则，`x-gmkerl-value` 值的格式为 `<width>x<height>`，如 `480x576`。
> * 若 `x-gmkerl-type`  为其他的类型，则 `x-gmkerl-value` 只需指定单个数字，如 `42`，意为高宽同为 42。

**注 3：x-gmkerl-crop 值的使用**

> * `(x, y)`左上角坐标；`width`：要裁剪的宽度；`height`：要裁剪的高度。x >= 0 && y >=0 && width > 0 && height > 0 且必须是正整型。
> * 裁剪参数`(x,y)`若大于原图大小，则将`(x,y)`重置为`(0,0)`进行裁剪。
> * `width+x` 若大于原图的宽度，则只裁剪到原图的最大宽度为止，不进行空白画布填充。
> * `heigth+y` 若大于原图的高度，则只裁剪到原图的最大高度为止，不进行空白画布填充。

**注 4：x-gmkerl-rotate 值的使用**

> * 若参数设置为`auto`，则根据原图的 EXIF 信息进行旋转（旋转后将修改原图的 EXIF 信息），其他参数则进行强制旋转。
> * 旋转失败时若参数为`auto`，则忽略错误进行保存操作；其他参数则直接返回错误信息。

#### 水印参数
在上传图片文件时，可以通过添加下面的参数进行添加文字水印的处理

| 参数                             | 说明                                                                                               |
|----------------------------------|----------------------------------------------------------------------------------------------------|
| `x-gmkerl-watermark-text`        | 文字水印内容，必须经过 urlencode 处理                                                                                           |
| `x-gmkerl-watermark-font`   | **默认 `simsun`** 文字水印字体（具体请参见注 1）                                                                                       |
| `x-gmkerl-watermark-size`   | **默认 `32`**；文字水印尺寸，取值必须为整数，单位像素                                                                                       |
| `x-gmkerl-watermark-align`       | **默认 `top,left`** 水印对齐方式 （具体请参见注 2） |
| `x-gmkerl-watermark-margin`      | **默认 `20,20`** 水印边距，格式 `x,y`，既 `水平边距,垂直边距`（如 `20,20`），单位像素                                                                                           |
| `x-gmkerl-watermark-opacity`     | **默认 `0`** 水印透明度，取值范围 0 ~ 100 的整数                                                                                        |
| `x-gmkerl-watermark-color`  | **默认 `#000000`** 文字水印颜色，RGB 值                                                                                       |
| `x-gmkerl-watermark-border` | 文字水印边框，默认无边框，（具体请参见注 3）|

**注 1：x-gmkerl-watermark-font 值可选列表**

> 目前支持的中文字体包括：
>
> * `simsun`：宋体(simsun)
> * `simhei`：黑体 (simhei)
> * `simkai`：楷体 (simkai)
> * `simli`：隶书 (simli)
> * `simyou`：幼圆 (simyou)
> * `simfang`：仿宋 (simfang)

**注 2：x-gmkerl-watermark-align 值的使用**

> 格式 `valign,halign`，既 `垂直对齐,水平对齐`（如 `bottom,right`），可选值包括：
>
> * 垂直对齐：`top`，`middle`，`bottom`
> * 水平对齐：`left`，`center`，`right`

**注 3：x-gmkerl-watermark-border 值的使用**

> 格式 `rgba`，既 `RGB 值透明度`，如 `#cccccccc`


**返回信息**

上传成功时返回 `200`；上传失败时返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

当上传的文件是图片时，响应中会包含相关的预处理信息（如果有设置相关参数的话），例如：

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

1. 下载成功: 返回 `200`, HTTP body 中返回文件内容
2. 下载失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

### 获取文件信息

```
HEAD /<bucket>/path/to/file
```

返回信息:

1. 获取成功: 返回 `200`, 返回头信息如下所示:

    |       参数        |  说明 |
    | ----------------- | ----- |
    | x-upyun-file-type | 文件为 `file`，文件夹为 `folder` |
    | x-upyun-file-size | 文件大小 |
    | x-upyun-file-date | 文件创建时间 |

2. 获取失败: 相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

### 删除文件

```
DELETE /<bucket>/path/to/file
```

返回信息:

1. 删除成功: 返回 `200`
2. 删除失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」


### 创建目录

```
POST /<bucket>/path/to/folder
```

|  参数  | 必选  | 说明 |
| ------ | ----- | ----- |
| folder |  是   | `true` 或者 `false`，`true` 表示当前请求为创建目录 |


返回信息:

1. 创建成功: 返回 `200`
2. 创建失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

### 删除目录

```
DELETE /<bucket>/path/to/folder
```

返回信息:

1. 删除成功: 返回 `200`
2. 删除失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

> **注：**
> API 只允许删除空的目录，若非空，则需先删除里面的文件，否则将提示访问被拒绝

### 获取目录文件列表

```
GET /<bucket>/path/to/folder
```

#### 分页参数

如果目录中文件数量过多，可以加以下 HTTP 请求头进行分页获取：

|      参数      | 必选 |   类型  |                                            说明                                           |
|----------------|------|---------|-------------------------------------------------------------------------------------------|
| X-List-Iter    | 否   | string  | 分页开始位置，由前一次请求的 `x-upyun-list-iter` 响应头返回                                |
| X-List-Limit   | 否   | string  | 返回的文件数量                                    |
| X-List-Order | 否   | string  | `asc` 或 `desc`，按时间升序或降序排列。默认 `desc`   |

> **注：**
>
> * 第一次请求时不需要 `X-List-Iter` 参数。
> * 一次请求的最大 `X-List-Limit` 值为 10000。

返回信息:

1. 获取成功: 返回 `200`。HTTP 头部的 `x-upyun-list-iter` 字段返回下一次分页开始的位置，HTTP body 内容为各个文件的属性。

如:

```
> HTTP/1.1 200 OK
> x-upyun-list-iter: c2Rmc2Rsamdvc2pnb3dlam9pd2Vmd2Z3Zg==
foo.jpg\tN\t4237\t1415096225\nbar\tF\t423404\t1415096260
```

> **注：**
>
> * 文件/目录之间以`\n`分隔，属性之间以:「文件\t 类型\t 大小\t 最后修改时间」 顺序以`\t`分隔。
> * 类型可选值: `N`(文件), `F`(目录)。
> * `x-upyun-list-iter` 为 `g2gCZAAEbmV4dGQAA2VvZg` 时表示**已经返回最后一个分页**，后面没有更多数据了。


2. 获取失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」


### 获取空间使用情况

```
GET /<bucket>/?usage
```

返回信息:

1. 获取成功: 返回 `200`。HTTP body 内容为空间的使用量（单位为`Byte`）
2. 获取失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」
