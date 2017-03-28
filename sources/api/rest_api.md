基于 RESTful 架构的 API，您可以使用任何编程语言发送 HTTP/HTTPS 请求与云存储通信。特别地，文中请求参数均通过 HTTP/HTTPS 请求头以 `Key: Value` 的形式传递。

---------

## 请求方法

```sh
curl -X GET \
    http://v0.api.upyun.com/<bucket>/<path> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 11:26:58 GMT>"\
    -H "Content-MD5: <Content-MD5>"\
    # 其他可选参数...
```

**基本域名**

- 智能选路（推荐）：`v0.api.upyun.com`
- 电信线路：`v1.api.upyun.com`
- 联通（网通）线路：`v2.api.upyun.com`
- 移动（铁通）线路：`v3.api.upyun.com`

**认证鉴权**

详见[签名认证](/api/authorization/#header)。

---------

<a name="upload_file"></a>
## 上传文件

把文件上传至又拍云存储。在上传图片文件时，可以设置预处理参数，对图片进行处理后再保存。

```
PUT /<bucket>/path/to/file
```

**上传参数**

| 参数       		| 必选 	|  类型  	|                                            说明                |
|-------------------|-------|-----------|---------------------------------------------------------------|
| Date           	| 是   	| String  	| 请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT`          |
| Content-Length    	| 是   	| String  	| 请求内容长度        |
| Content-MD5    	| 否   	| String  	| 上传文件的 MD5 值，如果请求中文件太大计算 MD5 不方便，可以为空        |
| Content-Type   	| 否   	| String  	| 文件类型，默认使用文件扩展名作为文件类型                         |
| Content-Secret 	| 否   	| String  	| 文件密钥，用于保护文件，防止文件被直接访问，见 [Content-Secret 参数说明](/api/rest_api/#Content-Secret) |
| x-upyun-meta-x 	| 否   	| String  	| 文件元信息，见 [Metadata](/api/rest_api/#metadata)           |
| x-gmkerl-thumb 	| 否   	| String  	| 图片预处理参数，见[上传预处理（同步）](/cloud/image/#sync_upload_process)       |

**响应信息**

- 上传成功：返回 `200`，当上传文件是图片且设置 `x-gmkerl-xxx` 时，包含图片[基本信息](/cloud/image/#attribute)（包括图片宽、高、格式、帧数），例如：

```
> HTTP/1.1 200 OK
> x-upyun-width: 50
> x-upyun-height: 50
> x-upyun-file-type: JPEG
> x-upyun-frames: 1
```

- 上传失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

<a name="Content-Secret"></a>
**Content-Secret 参数说明**

* 文件设置 `Content-Secret` 后，文件将不能被直接访问，若需访问，需要在 URL 后加上 「间隔标识符」 和 「文件密钥」。如： 间隔符为 `!`，`Content-Secret` 为 `abc`，图片访问方式为： <a href="https://p.upyun.com/docs/cloud/secret.jpg!abc" target="_blank" title="查看">`https://p.upyun.com/docs/cloud/secret.jpg!abc`</a>。

* **间隔标识符** 用于分隔文件 URL 和文件秘钥，有 3 种可选，分别是：!（感叹号/默认值）、-（中划线）和 _（下划线），可登录又拍云<a href="https://console.upyun.com/services/" target="_blank">控制台</a>，在 「服务」 > 「功能配置」 > 「云处理」 中设置。

* 删除或修改 `Content-Secret`，见 Metadata 的[常见应用案例](/api/rest_api/#metadata_exg)。

* 如果原文件是一张图片，除了通过文件密码进行访问外，还可以通过[图片处理](/cloud/image/#url)进行访问。

* 如果原文件是一张图片，文件秘钥应避免与[缩略图版本](/cloud/image/#thumb)冲突，如果冲突，文件秘钥的优先级大于缩略图版本。

---------

<a name="Multi_upload"></a>
## 断点续传

在上传大文件或移动端上传文件时，因为网络质量、传输时间过长等原因造成上传失败，可以使用断点续传。特别地，断点续传上传的图片不支持预处理。特别地，断点续传上传的文件不能使用其他上传方式覆盖，如果需要覆盖，须先删除文件。

### 名称概念

* 文件分块：直接切分二进制文件成小块。分块大小固定为 1M。最后一个分块除外。
* 上传阶段：使用 `x-upyun-multi-stage` 参数来指示断点续传的阶段。分为以下三个阶段: `initate`(上传初始化), `upload`(上传中), `complete`(上传结束)。各阶段依次进行。
* 分片序号：使用 `x-upyun-part-id` 参数来指示当前的分片序号，序号从 0 起算。
* 顺序上传：对于同一个断点续传任务，只支持顺序上传。
* 上传标识：使用 `x-upyun-multi-uuid` 参数来唯一标识一次上传任务, 类型为字符串, 长度为 36 位。
* 上传清理：断点续传未完成的文件，会保存 24 小时，超过后，文件会被删除。

<!-- 支持混合使用, 不同阶段之间以逗号分隔(eg. `initate,upload` 表示初始化上传并上传第一个文件分块, `upload,complete` 表示上传最后一个文件分块并结束上传) -->

---------

### 初始化断点续传

```
PUT /<bucket>/path/to/file
```

**请求参数**

| 参数         			| 必选 	| 类型  		| 说明                                          	|
|-----------------------|-------|-----------|-----------------------------------------------|
| Content-Length    		| 是   	| String  	| 请求内容长度							       	|
| x-upyun-multi-stage   | 是   	| String  	| 值为 `initiate`， 表示初始化上传任务             |
| x-upyun-multi-length  | 是   	| String  	| 待上传文件大小，整型，单位 Byte   |
| x-upyun-multi-type    | 否   	| String  	| 待上传文件的 `MIME` 类型，默认 `application/octet-stream`，建议自行设置  |
| x-upyun-meta-x        | 否   	| String  	| 用于额外指定文件的元信息，详见 [Metadata](/api/rest_api/#metadata)   |

**响应信息**

初始化断点续传成功返回 `204` 状态码, 包括以下响应参数：

| 参数             		| 类型  		| 说明                               					|
|-----------------------|-----------|-------------------------------------------------------|
| x-upyun-multi-uuid   	| String  	| 本次上传任务的标识                                      	|
| x-upyun-next-part-id 	| String  	| 下一个分块序号。因为是初始化阶段，所以值为 `0`              |

上传失败时返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

### 续传数据

```
PUT /<bucket>/path/to/file
```

**请求参数**

| 参数         			| 必选 	| 类型  		| 说明                                          	|
|-----------------------|-------|-----------|-----------------------------------------------|
| Content-Length    		| 是   	| String  	| 请求内容长度        							|
| x-upyun-multi-stage   | 是   	| String  	| 值为 `upload`， 表示开始上传文件数据             	|
| x-upyun-multi-uuid    | 是   	| String  	| 本次上传任务的标识，是初始化断点续传任务时响应信息中的 `x-upyun-multi-uuid` |
| x-upyun-part-id       | 是   	| String  	| 指定此次分片的唯一 ID，应严格等于上一次请求返回的 `x-upyun-next-part-id`   |
| Content-MD5           | 否   	| String  	| 所上传分块的 MD5 值，等效于[签名认证](/api/authorization/#header)中的 `Content-MD5 ` |

**响应信息**

断点续传此分块成功返回 `204` 状态码, 包括以下响应参数：

| 参数             		| 类型  		| 说明                               					|
|-----------------------|-----------|-------------------------------------------------------|
| x-upyun-multi-uuid   	| String  	| 本次上传任务的标识                                      	|
| x-upyun-next-part-id 	| String  	| 下一个分块序号。若全部分块都已经传完，则该响应头值为 `-1`    |

上传失败时返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

### 结束断点续传

```
PUT /<bucket>/path/to/file
```

**请求参数**

| 参数         			| 必选 	| 类型  		| 说明                                          	|
|-----------------------|-------|-----------|-----------------------------------------------|
| Content-Length    		| 是   	| String  	| 请求内容长度        		|
| x-upyun-multi-stage   | 是   	| String  	| 值为 `complete`， 表示完成断点续传任务           	|
| x-upyun-multi-uuid    | 是   	| String  	| 本次上传任务的标识，是初始化断点续传任务时响应信息中的 `x-upyun-multi-uuid`  |
| x-upyun-multi-md5     | 否   	| String  	| 所上传整个文件的 MD5 值，等效于[签名认证](/api/authorization/#header)中的 `Content-MD5 ` |


**响应信息**

结束断点续传成功，如果返回 `204` 状态码，表示文件覆盖同名文件；如果返回 `201` 状态码，表示文件是新文件。

响应信息还包括以下响应参数：

| 参数             		| 类型  		| 说明                               					|
|-----------------------|-----------|-------------------------------------------------------|
| x-upyun-multi-uuid   	| String  	| 本次上传任务的标识                                  		|
| x-upyun-multi-type   	| String  	| 本次上传文件的 `MIME` 类型                          		|
| x-upyun-multi-length 	| String  	| 本次上传文件大小，整型，单位 Byte   						|

上传失败时返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

## 下载文件

```
GET /<bucket>/path/to/file
```

**响应信息**

- 下载成功：返回 `200`，HTTP body 中返回文件内容。
- 下载失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------


## 删除文件

```
DELETE /<bucket>/path/to/file
```

**请求参数**

|        参数   |  必选  |              说明                             |
| --------------| ----- | --------------------------------------------- |
| x-upyun-async |  否   | `true` 表示进行异步删除，不设置表示同步删除（默认） |


**响应信息**

- 删除成功：返回 `200`。
- 删除失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。


**注**

- 同步删除有频率限制，批量删除请在请求头里加上 `x-upyun-async: true` 进行异步删除（无频率限制）。
- 异步删除请求成功，返回 `200`，但删除操作会延期执行。

---------

## 创建目录

```
POST /<bucket>/path/to/folder
```

**请求参数**

|    参数  |  必选  |                    说明                         |
| -------- | ----- | ----------------------------------------------- |
| folder   |  是   | 值为 `true`                                     |
 

**响应信息**

- 创建成功：返回 `200`。
- 创建失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

## 删除目录

```
DELETE /<bucket>/path/to/folder
```

**响应信息**

- 删除成功：返回 `200`。
- 删除失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

**注**

- 只允许删除空的目录，非空目录需要先删除里面的文件，否则删除请求会被拒绝。

---------

## 获取文件信息

```
HEAD /<bucket>/path/to/file
```

**响应信息**

- 获取成功：返回 `200`，返回头信息如下所示：

    |       参数         |             说明                  |
    | ----------------- | ----------------------------------|
    | x-upyun-file-type | 文件为 `file`，文件夹为 `folder`    |
    | x-upyun-file-size | 文件大小                   |
    | x-upyun-file-date | 文件创建时间                  |

- 获取失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

## 获取目录文件列表

```
GET /<bucket>/path/to/folder
```

**分页参数**

如果目录中文件数量过多，为了更友好的获取文件信息，可以分页获取：

|      参数       | 必选 |   类型  |                    说明                                 |
|--------------- |------|--------|-------------------------------------------------------------|
| x-list-iter    |  否  | string  | 分页开始位置，通过`x-upyun-list-iter` 响应头返回，所以第一次请求不需要填写      |
| x-list-limit   |  否  | string  | 获取的文件数量，默认 100，最大 10000                           |
| x-list-order   |  否  | string  | `asc` 或 `desc`，按时间升序或降序排列。默认 `asc`               |


**响应信息**

- 获取成功：返回 `200`。
- 获取失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

例如：

```
> HTTP/1.1 200 OK
> x-upyun-list-iter: c2Rmc2Rsamdvc2pnb3dlam9pd2Vmd2Z3Zg==
foo.jpg\tN\t4237\t1415096225\nbar\tF\t423404\t1415096260
```

- `x-upyun-list-iter` 返回下一次分页开始位置。它由一串 Base64 编码的随机数组成，当它是 `g2gCZAAEbmV4dGQAA2VvZg` 时，表示**最后一个分页**。
- HTTP body 为各个文件/目录的信息，文件/目录之间以 `\n` 分隔，属性按 「文件\t类型\t大小\t最后修改时间」 排列，类型可选值：`N` 表示文件，`F` 表示目录。

---------

## 获取服务使用量

```
GET /<bucket>/?usage
```

**响应信息**

- 获取成功：返回 `200`。HTTP body 内容为服务的使用量（单位为`Byte`）。
- 获取失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

## Metadata

下载文件时，在响应头里返回文件的所有元数据。

### 添加 Metadata

[上传文件](/api/rest_api/#_2)时，如果在请求头里带上以 `x-upyun-meta-` 开头的参数，那么该参数会作为文件的元数据存储到云存储。例如：

```
curl -d 'abc' \
    http://v0.api.upyun.com/<bucket>/abc.txt \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 30 Oct 2016 11:26:58 GMT>"\
    -H "x-upyun-meta-foo: Bar"\
	-H "x-upyun-meta-a: 2"
```
文件 `abc.txt` 会增加 `x-upyun-meta-foo: bar` 和 `x-upyun-meta-a: 2` 这两个元信息。

---------

### 修改 Metadata

```
PATCH /<bucket>/path/to/file?metadata=<option>
```

option 的取值如下：

| option      		| 说明                                     							|
|-------------------|-------------------------------------------------------------------|
|merge（**默认**） 	| 合并文件元信息，如果是相同的元信息，将被新上传的值替换                         |
|replace          	| 替换文件元信息为新上传的文件元信息                                    |
|delete           	| 删除文件元信息                                                     |

**响应信息**

- 修改成功：返回 `200`。
- 修改失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

**举例**

例 1：合并元信息，`metadata=merge`

```
curl -d 'abc' http://v0.api.upyun.com/<bucket>/abc.txt \
	-H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: 1"

curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=merge \
	-H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: 2" \
    -H "x-upyun-meta-b: 3"
```

文件 abc.txt 的元信息是：

```
x-upyun-meta-a: 2
x-upyun-meta-b: 3
```

例 2：替换元信息，`metadata=replace`

```
curl -d 'abc' http://v0.api.upyun.com/<bucket>/abc.txt \
	-H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: 1" \
    -H "x-upyun-meta-b: 2"

curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=replace \
	-H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: 3" \
    -H "x-upyun-meta-c: 4"
```

文件 abc.txt 的元信息为：

```
x-upyun-meta-a: 3
x-upyun-meta-c: 4
```

例 3：删除元信息，`metadata=delete`

```
curl -d 'abc' http://v0.api.upyun.com/<bucket>/abc.txt \
	-H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: 1" \
    -H "x-upyun-meta-b: 2"

curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=delete \
	-H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: true"
```

文件 abc.txt 的元信息为：

```
x-upyun-meta-b: 2
```

<a name="metadata_exg"></a>
### 常见应用案例

例 1：修改 Metadata 默认不更新文件的 `Last-Modified`，如果需要更新，在参数中指定 `update_last_modified=true`：

```
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=replace&update_last_modified=true \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-a: 3" \
    -H "x-upyun-meta-c: 4"
```

例 2：`Content-Secret` 是被存储为 `x-upyun-meta-secret` 元信息，如果需要修改或删除，可以对 `x-upyun-meta-secret` 进行操作：

```
curl -XPATCH http://v0.api.upyun.com/<bucket>/abc.txt?metadata=delete \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ=" \
    -H "x-upyun-meta-secret: upyun520"
```

---------

## 签名认证（旧）

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
| PASSWORD       | 服务的操作员的密码                                                                         |

将 `PASSWORD` md5 之后（我们暂且将其记作 `PASSWORD_MD5`），上表所注的其他信息以 `&` 字符进行拼接（按表格从上至下的顺序）即（`METHOD&PATH&DATE&CONTENT-LENGTH&PASSWORD_MD5`），并将所得字符串进行 MD5 加密，即得我们所需的 签名（signature）

**注**

- 签名的有效期为 30 分钟。如果超过 30 分钟，则需重新生成签名。
- GET，DELETE 操作的 CONTENT_LENGTH 为 0。

**例如**

请求方式为 `GET`，URI 为 `bucket` 服务的子目录 `/sub`，请求时间为 ` Wed, 29 Oct 2014 02:26:58 GMT`，因为是 `GET`，所以 `CONTENT-LENGTH` 为 `0`，假设该服务有一个授权操作员名为 `upyun`，该操作员密码为 `password`（密码 md5 后为 `5f4dcc3b5aa765d61d8327deb882cf99`），那么：

签名（signature）即是对字符串 `GET&/bucket/sub&Wed, 29 Oct 2014 02:26:58 GMT&0&5f4dcc3b5aa765d61d8327deb882cf99` 计算 MD5 所得，即：`03db45e2904663c5c9305a9c6ed62af3`，因此，只需在请求头部加上如下字段即可：

```
Authorization: UpYun upyun:03db45e2904663c5c9305a9c6ed62af3
```

---------

## 图片预处理（旧）

|         参数                   | 必选 |                       说明                                           |
|-------------------------------|------|----------------------------------------------------------------------|
| x-gmkerl-extract-color-count  | 否   | 主题色提取的颜色数量，默认 `256`                                         |
| x-gmkerl-extract-format       | 否   | 主题色提取格式，默认 `hex`                                              |
| x-gmkerl-thumbnail            | 否   | 缩略图版本名称，可搭配其他 `x-gmkerl-*` 参数使用                          |
| x-gmkerl-type                 | 否   | 缩略类型，见 [x-gmkerl-type 可选值列表](#gmkerl-type)              |
| x-gmkerl-value                | 否   | 缩略类型对应的参数值，见 [x-gmkerl-value 值说明](#gmkerl-value)      |
| x-gmkerl-quality              | 否   | 压缩质量，默认 `95`                                          |
| x-gmkerl-unsharp              | 否   | 是否锐化，默认锐化                                        |
| x-gmkerl-rotate               | 否   | 图片旋转（顺时针），可选值：`auto`、`(0, 360]`。详见[旋转](/cloud/image/#rotate)|
| x-gmkerl-crop                 | 否   | 图片裁剪，格式：`<w>x<h>a<x>a<y>`。详见[裁剪](/cloud/image/#crop)       |
| x-gmkerl-exif-switch          | 否   | 是否保留 EXIF 信息，默认不保留 <br /> 仅在搭配 `x-gmkerl-crop`、`x-gmkerl-type`、`x-gmkerl-thumbnail` 时有效        |
| x-gmkerl-watermark-text       | 否   | 文字水印内容，必须经过 urlencode 处理                  |
| x-gmkerl-watermark-font       | 否   | 文字水印字体，默认 `simsun`，见 [x-gmkerl-watermark-font 可选值列表](#gmkerl-watermark-font)            |
| x-gmkerl-watermark-size       | 否   | 文字水印字体大小，正整数，单位像素，默认 `32`                      |
| x-gmkerl-watermark-align      | 否   | 水印对齐方式，默认 `top,left`，见 [x-gmkerl-watermark-align 值说明](#gmkerl-watermark-align)           |
| x-gmkerl-watermark-margin     | 否   | 水印边距，格式 `x,y`，既 `水平边距,垂直边距`，单位像素，默认 `20,20`    |
| x-gmkerl-watermark-opacity    | 否   | 水印透明度，取值范围 0 ~ 100 的整数，默认 `0`        |
| x-gmkerl-watermark-color      | 否   | 文字水印颜色，RGB 值，默认 `#000000`                     |
| x-gmkerl-watermark-border     | 否   | 文字水印边框，默认无边框，见 [x-gmkerl-watermark-border 值说明](#gmkerl-watermark-border)          |

<a name="gmkerl-type"></a>
**x-gmkerl-type 可选值列表**

|           值             |                含义                |
|--------------------------|-----------------------------------|
| `fix_width`              | 限定宽度，高度自适应                |
| `fix_height`             | 限定高度，宽度自适应                |
| `fix_width_or_height`    | 限定宽度和高度，宽高不足时不缩放     |
| `fix_both`               | 固定宽度和高度，宽高不足时强行缩放   |
| `fix_max`                | 限定最长边，短边自适应              |
| `fix_min`                | 限定最短边，长边自适应              |
| `fix_scale`              | 等比例缩放（1-1000）                 |


<a name="gmkerl-value"></a>
**x-gmkerl-value 值说明**

* 当 `x-gmkerl-type` 指定为 `fix_width_or_height` 或 `fix_both` 时，`x-gmkerl-value` 值的格式为 `<width>x<height>`，如 `480x576`。
* 当 `x-gmkerl-type`  为其他的类型，`x-gmkerl-value` 值只需要指定单个数字，如 `100`，意思为高和/或宽为 `100`。

<a name="gmkerl-watermark-font"></a>
**x-gmkerl-watermark-font 可选值列表**

* `simsun`：宋体(simsun)
* `simhei`：黑体 (simhei)
* `simkai`：楷体 (simkai)
* `simli`：隶书 (simli)
* `simyou`：幼圆 (simyou)
* `simfang`：仿宋 (simfang)

<a name="gmkerl-watermark-align"></a>
**x-gmkerl-watermark-align 值说明**

格式 `valign,halign`，既 `垂直对齐,水平对齐`，`valign`、`halign` 可选值：

* 垂直对齐：`top`，`middle`，`bottom`；
* 水平对齐：`left`，`center`，`right`。

<a name="gmkerl-watermark-border"></a>
**x-gmkerl-watermark-border 值说明**

* 格式 `rgba`， `RGB` 表示边框的颜色，`a` 表示边框的透明度，如 `#cccccccc`

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)