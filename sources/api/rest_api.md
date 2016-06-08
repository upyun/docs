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
根据实际情况任选其一,默认推荐客户使用`v0.api.upyun.com`

## 请求方法

```sh
curl -X GET \
    http://v0.api.upyun.com/<bucket>/<path> \
    -H "Authorization: <your_authorization>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
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

UPYUN 支持 HTTP 基本认证与签名认证两种认证授权方式。请根据需要任选其一。

### HTTP 基本认证
REST API 支持 [HTTP 基本认证](http://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81)，其中认证所用的用户名即为已授权给你需要操作的空间的某个操作员名，认证口令为该操作员的密码。

```sh
curl -u http://v0.api.upyun.com/<bucket>
```
或者，可以自行将操作员名和操作员密码拼接后以 Base64 编码后自行在请求头部加上 `Authorization` 字段：

```sh
curl -X GET \
    http://v0.api.upyun.com/<bucket>\
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
>
> - 签名的有效期为 30 分钟。如果超过 30 分钟，则需重新生成签名。
> - GET，DELETE 操作的 CONTENT_LENGTH 为 0。

如：

请求方式为 *`GET`*，URI 为 *`bucket`* 空间的子目录 *`/sub`*，请求时间 为 *` Wed, 29 Oct 2014 02:26:58 GMT`*，因为是 GET，所以 `CONTENT-LENGTH` 为 *`0`*，假设该空间有一个授权操作员名为 *`operator`*，该操作员密码为 *`password`*（密码 md5 后为 *`5f4dcc3b5aa765d61d8327deb882cf99`*），那么：

签名（signature）即是对字符串 *`GET&/bucket/sub&Wed, 29 Oct 2014 02:26:58 GMT&0&5f4dcc3b5aa765d61d8327deb882cf99`* 计算 md5 所得，即：*`03db45e2904663c5c9305a9c6ed62af3`*，因此，只需在请求头部加上如下字段即可：

```
Authorization: UpYun operator:03db45e2904663c5c9305a9c6ed62af3
```


## 上传文件

通过上传接口，可以将文件上传至 UPYUN 的空间，并且，在上传文件的同时，可以设置相关的参数，进行对文件的预处理。目前，上传接口的参数分为如下几部分：

* 通用上传参数
* 预处理参数

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
| Content-Secret | 否   | String  | 文件密钥。若设置该值，则无法直接访问原文件，需要在原文件 URL 的基础上加上密钥值才能访问   |
| X-Upyun-Meta-X | 否   | String  | 用于额外指定文件的元信息，详见 [metadata 参数](/api/rest_api/#metadata)                   |

> **注：**
>
> * 设置 `Content-Secret` 密钥后，原文件将被保护，不能被直接访问。如果原文件是一张图片，则加上 [作图参数](/cloud/image/#url) 或 [缩略图版本](/cloud/image/#_3) 之后的图片是可以访问的。
> * 设置密钥后，若需访问原文件，需要在 URL 后加上「间隔标识符」和「访问密钥」（如： 当间隔符为 *`!`*，访问密钥为 *`secret`*，那么，原文件访问方式即为： *`http://bucket.b0.upaiyun.com/sample.jpg!secret`*）
> * **间隔标识符** 用于分隔文件 URL 和文件参数。可登录又拍云管理平台选择 `!(英文感叹号)` 、 `-` 、 `_` 中的任意一种 ，本文档使用 `!` 作为示例间隔标识符。
> * 密钥不能与 [缩略图版本](/cloud/image/#_3) 冲突。
> * 删除或修改 `Content-Secret` 请见 [metadata 参数](/api/rest_api/#metadata)。

#### 预处理参数

对于图片文件，加上 `x-gmkerl-thumb` 参数可以将上传图片进行预处理后的结果存储到 UPYUN。具体参数用法和说明请见 [上传作图](/cloud/image/#_2)。

#### 返回信息

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


## 下载文件

```
GET /<bucket>/path/to/file
```

返回信息:

1. 下载成功: 返回 `200`, HTTP body 中返回文件内容
2. 下载失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」


## 获取文件信息

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


## 删除文件

```
DELETE /<bucket>/path/to/file
```

|  参数  | 必选  | 说明 |
| ------ | ----- | ----- |
| x-upyun-async |  否   | `true` 表示进行异步删除，不设置表示同步删除（默认） |


返回信息:

1. 删除成功: 返回 `200`
2. 删除失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」


> **注：**
>
> - 同步删除会有频率限制，如果有大量删除操作请在请求头里加上 `x-upyun-async: true` 进行异步删除（无频率限制）。
> - 异步删成功会返回 200。但文件删除操作会延期执行。


## 创建目录

```
POST /<bucket>/path/to/folder
```

|  参数  | 必选  | 说明 |
| ------ | ----- | ----- |
| folder |  是   | `true` 或者 `false`，`true` 表示当前请求为创建目录 |


返回信息:

1. 创建成功: 返回 `200`
2. 创建失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

## 删除目录

```
DELETE /<bucket>/path/to/folder
```

返回信息:

1. 删除成功: 返回 `200`
2. 删除失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

> **注：**
> API 只允许删除空的目录，若非空，则需先删除里面的文件，否则将提示访问被拒绝


## 获取目录文件列表

```
GET /<bucket>/path/to/folder
```

#### 分页参数

如果目录中文件数量过多，可以加以下 HTTP 请求头进行分页获取：

|      参数      | 必选 |   类型  |                                            说明                                           |
|----------------|------|---------|-------------------------------------------------------------------------------------------|
| X-List-Iter    | 否   | string  | 分页开始位置，由前一次请求的 `x-upyun-list-iter` 响应头返回                                |
| X-List-Limit   | 否   | string  | 返回的文件数量                                    |
| X-List-Order | 否   | string  | `asc` 或 `desc`，按时间升序或降序排列。默认 `asc`   |

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


## 获取空间使用情况

```
GET /<bucket>/?usage
```

返回信息:

1. 获取成功: 返回 `200`。HTTP body 内容为空间的使用量（单位为`Byte`）
2. 获取失败: 返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」


## metadata 信息

在上传文件的时候，如果在请求头里带上以 `X-Upyun-Meta-` 开头的参数，那么该参数会被当作文件的元数据存储到 UPYUN。在通过 API GET 文件的时候，UPYUN 会在响应头里返回文件的所有元数据信息。

例如：
```
curl -d 'abc' \
    http://v0.api.upyun.com/<bucket>/abc.txt \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-Foo: Bar"
```
可以将 `X-Upyun-Meta-Foo: Bar` 这个元信息存储到 UPYUN。

### 修改 metadata 信息

```
PATCH /<bucket>/path/to/file?metadata=<option>
```
返回信息:

1. 修改成功：返回 `200`
2. 修改失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」

其中，`option` 的取值如下：

|option | 说明 |
|----|-----|
|merge（**默认**） |  合并文件元信息, 相同的元信息将被新上传的值替换 |
|replace | 替换文件元信息为新上传的文件元信息 |
|delete | 删除文件元信息 |

以下为几个修改元信息的例子以及相应的说明：


例 1：合并元信息

```
curl -d 'abc' http://v0.api.upyun.com/<bucket>/abc.txt -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: 1"
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=merge -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: 2" \
    -H "X-Upyun-Meta-B: 3"
```
以上命令执行后，文件 abc.txt 的元信息为：
```
X-Upyun-Meta-A: 2
X-Upyun-Meta-B: 3
```


例 2：替换元信息

```
curl -d 'abc' http://v0.api.upyun.com/<bucket>/abc.txt -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: 1" \
    -H "X-Upyun-Meta-B: 2"
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=replace -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: 3" \
    -H "X-Upyun-Meta-C: 4"
```
以上命令执行后，文件 abc.txt 的元信息为：
```
X-Upyun-Meta-A: 3
X-Upyun-Meta-C: 4
```


例 3：删除元信息

```
curl -d 'abc' http://v0.api.upyun.com/<bucket>/abc.txt -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: 1" \
    -H "X-Upyun-Meta-B: 2"
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=delete -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: true"
```
以上命令执行后，文件 abc.txt 的元信息为：
```
X-Upyun-Meta-B: 2
```

注 1：

> 修改 metadata 默认不更新文件的 `Last-Modified`，如果要更新请在参数中指定 `update_last_modified=true`。如：

```
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=replace&update_last_modified=true \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-A: 3" \
    -H "X-Upyun-Meta-C: 4"
```

注 2：

> `Content-Secret` 被存储为文件的 `X-Upyun-Meta-Secret` 元信息，如果要删除或修改请对 `X-Upyun-Meta-Secret` 进行操作。如：

```
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=delete \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "X-Upyun-Meta-Secret: true" \
```
