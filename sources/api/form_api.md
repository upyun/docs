通过网页 FORM，使用 HTTP/HTTPS 直接把文件上传到又拍云存储。特别地，文中请求参数放在 HTTP/HTTPS body 中传递，请确保您上传表单格式符合 [RFC 1867](https://www.ietf.org/rfc/rfc1867.txt) 协议规范。

---------

## 请求方法

```sh
curl http://v0.api.upyun.com/<bucket> \
    -F authorization="UPYUN <Operator>:<Signature>" \
    -F file=@<filename> \
    -F policy=<policy> \
    # 其他参数...
```

**基本域名**

- 智能选路（推荐）：`v0.api.upyun.com`
- 电信线路：`v1.api.upyun.com`
- 联通（网通）线路：`v2.api.upyun.com`
- 移动（铁通）线路：`v3.api.upyun.com`

**认证鉴权**

详见[签名认证](/api/authorization/#body)。

**推荐上传流程**

```
            +-----------------+ +-----------------+ +-----------------+
            | Client/Browser  | |    FORM API     | | Customer Server |
            +-----------------+ +-----------------+ +-----------------+
                    |                   |                   |
                    |                   |                   |
                   +++        Request authorization        +++
                   |-|====================================>|-|
                   |-|                  |                  |-|
                   |-|                  |                  |-|
                   |-|        Response authorization       |-|
                   |-|<====================================|-|
                   +++                  |                  +++
                    |                   |                   |
                    |                   |                   |
                    |                   |                   |
                   +++     Upload      +++                 +++
                   |-|================>|-|     Notify      |-|
                   |-|                 |-|---------------->|-|
                   |-|                 |-|                 |-|
                   |-|     Response    |-|                 |-|
                   |-|<================|-|                 |-|
                   +++                 +++                 +++
                    |                   |                   |
                    |                   |                   |                              
```

1. 客户端请求客户服务器，生成、获取上传所需的 [policy](/api/authorization/#policy)、[authorization](/api/authorization/#body)；
2. 客户端通过 FORM API 上传文件，返回上传结果信息，（可选）回调通知客户服务器；
3. 客户端处理其他业务流程。

---------

<a name="upload_args"></a>
## 上传参数

| 参数           		| 必选 	| 说明                                           					|
|-----------------------|-------|-------------------------------------------------------------------|
| bucket                | 是   	| 文件上传到的服务                                                    |
| save-key              | 是   	| 文件保存路径，可用占位符，见 [路径设置](#save-key)                    	|
| expiration            | 是   	| 请求的过期时间，UNIX UTC 时间戳，单位秒。建议设为 30 分钟              	|
| date            		| 否   	| 请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT` |
| content-md5           | 否   	| 上传文件的 MD5 值，如果请求中文件太大计算 MD5 不方便，可以为空 	|
| return-url            | 否   	| 同步通知 URL，见 [通知规则](#notify_return) |
| notify-url            | 否   	| 异步通知 URL，见 [通知规则](#notify_return)  |
| content-secret        | 否   	| 文件密钥，用于保护文件，防止文件被直接访问，见 [Content-Secret 参数说明](/api/rest_api/#Content-Secret)         |
| content-type          | 否   	| 文件类型，默认使用文件扩展名作为文件类型                             |
| allow-file-type       | 否   	| 允许上传的文件扩展名，以 `,` 分隔。如 `jpg,jpeg,png`                |
| content-length-range  | 否   	| 文件大小限制，格式：`min,max`，单位：字节 <br /> 如 `102400,1024000`，表示允许上传 100Kb～1Mb 的文件      |
| image-width-range     | 否   	| 图片宽度限制，格式：`min,max`，单位：像素 <br /> 如 `0,1024`，允许上传宽度为 0～1024px 之间  |
| image-height-range    | 否   	| 图片高度限制，格式：`min,max`，单位：像素 <br /> 如 `0,1024`，允许上传高度在 0～1024px 之间  |
| x-gmkerl-thumb        | 否   	| 图片预处理，见 [上传预处理（同步）](/cloud/image/#sync_upload_process)   |
| x-gmkerl-type         | 否   	| 可选值：`get_meta`，获取图片信息；`get_theme_color`，获取图片主题色          |
| apps                  | 否   	| 异步预处理，见 [异步预处理](#async_process)            |
| b64encoded            | 否   	| 对通过 Base64 编码上传的文件进行 Base64 解码，值为 `on`            |
| ext-param             | 否   	| 额外参数，见 [ext-param](#ext-param)                  |

**注**

* 每个上传请求只能上传一个文件，请求的参数名和参数值 **区分大小写**。
* 若在上传非图片文件时使用 `image-width-range`，`image-height-range`，会返回「不是图片」的错误。
* 若请求中带有预处理参数（`x-gmkerl-thumb`），图片处理后再保存。
* 若设置了 `x-gmkerl-type` 参数，则图片上传完成后会返回图片的 meta 信息或主题色。

---------

## 响应信息

- 上传成功：返回 `200` 或访问指定的 URL（见 [通知规则](#notify_return)）。
- 上传失败：返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」。

---------

<a name="notify_return"></a>
## 通知规则

**return-url 参数说明**

* 如果没有设置 `return-url`，文件上传完成后，结果信息在响应体中返回。
* 如果设置了 `return-url`，文件上传完成后，结果信息以 Query String 的形式追加到 `return-url` 指定的 URL 后，并访问。例如，`return-url` 为 `http://www.yourdomain.com/return/`，访问的 URL 是：

```
http://yourdomain.com/return/?code=200&message=ok&url=%2F2011%2F12%2Ffd0e30047f81fa95.mp3&time=1332129461
```

* 结果信息的参数名及说明如下：

| 参数       	| 说明                                                      	|
|---------------|-----------------------------------------------------------|
| code      	| 状态码，`200` 表示上传成功                      				|
| message  		| URL encoding 的描述信息，`ok` 表示上传成功，其他的表示错误信息             		|
| url         	| URL encoding 的文件保存路径                              					|
| time        	| UNIX UTC 时间戳，单位秒        								|
| sign/no-sign  | 签名，详见[回调签名（旧）](#sign)。使用[回调签名（新）](/api/authorization/#header)时，不存在 |
| image-width	| 图片的宽。上传文件是图片时，才存在             				|
| image-height  	| 图片的高。上传文件是图片时，才存在                            	|
| image-type  	| 图片类型。上传文件是图片时，才存在       						|
| image-frames  	| 图片帧数。上传文件是图片时，才存在        						|
| task_ids  	| 异步任务 ID 集。设置了 `apps` 参数时，才存在        			|

<!--
包括 `code`、`message`、`url`、`time` 和 [`sign`](#sign)(或 [`no-sign`](#sign)) 参数。如果上传的是图片，还包括 `image-width`、`image-height`、`image-frames` 和 `image-type` 四个参数（备注：这四个参数不参与旧版回调签名计算）。如果设置了 `apps` 参数，还包括 `task_ids`。
-->

**notify-url 参数说明**

* 如果没有设置 `notify-url`，不发起异步通知。
* 如果设置了 `notify-url`，文件上传完成后，向 `notify-url` 发送 `HTTP POST` 请求，请求体是回调信息。回调信息是 JSON 字符串，内容和 `return-url` 中的[结果信息](#notify_return)相同。回调通知签名，见[签名认证](/api/authorization/#header)，它提供给客户端用于验证回调通知的合法性。

---------

<a name="save-key"></a>
## 路径设置

`save-key` 支持的设置类型：

|  类型   |                  格式                   |                      说明                     |
|--------|-----------------------------------------|-----------------------------------------------|
| 绝对值  | String                                  | 指定具体的路径，如: `/path/to/file.txt`    |
| 时间类  | {year} {mon} {day} {hour} {min} {sec}   | 日期、时间相关内容（UTC 时间）                            |
| md5 类 | {filemd5}                               | 文件的 md5 值                             |
| 随机类  | {random} {random32}                     | 16 位或 32 位随机字符和数字                   |
| 文件名  | {filename} {suffix} {.suffix}           | 上传文件的文件名及扩展名                      |

**举例说明**

例 1：时间类 + 随机类

```
上传文件名：sample.jpg
UTC 时间：2013-01-01 10:05:20
save-key：/{year}/{mon}/{day}/upload_{random32}{.suffix}
保存路径为：/2013/01/01/upload_b92c9cc21ce6a88ea696705c88b57c3f.jpg
```


例 2：时间类 + 文件名

```
上传文件名：sample.jpg
UTC 时间：2014-02-02 11:05:20
save-key：/{year}/{mon}/{day}/{hour}_{min}_{sec}_{filename}{.suffix}
保存路径为：/2014/02/02/11_05_20_sample.jpg
```

为了避免路径重名导致文件被覆盖，建议使用复杂的低重复性的命名方式。

---------

<a name="async_process"></a>
## 异步预处理

如果设置了 `apps` 参数，文件上传完成后，会创建异步处理任务。目前，异步预处理支持**异步图片处理**、**异步音视频处理**和**异步图片鉴别**。

###　apps 参数结构

```
apps = [
    {                                           // 异步图片处理任务
        "name": "thumb",                        // 异步任务名称，必填。thumb 表示异步图片处理服务
        "x-gmkerl-thumb": "<图片处理参数>",		// 处理参数，必填
        "save_as": "<save_as>",           		// 结果图片保存路径，选填
		"notify_url": "<notify_url>"     		// 回调地址，不填时使用上传参数中的  notify_url
    },
	......
]

apps = [
    {                                              	// 异步音视频处理任务
        "name": "naga",						   	   	// 异步任务名称，必填。naga 表示异步音视频处理服务
		"type": "<type>",                           // 视频转码，必填
        "avopts": "<avopts>",        				// 处理参数，必填
        "return_info": true/false,                  // 是否返回元数据，选填
        "save_as": "<save_as>",                     // 结果音频/视频保存路径，选填
        "notify_url": "<notify_url>"     			// 回调地址，不填时使用上传参数中的 notify_url
    },
	......
]

apps = [
    {                                               // 异步内容审核任务
        "name": "imgaudit",                         // 异步任务名称，必填。imgaudit 表示异步内容审核服务
        "notify_url": "<notify_url>"                // 回调地址，不填时使用上传参数中的 notify_url
    },
	......
]
```

**注**

- `apps` 按 JSON 格式组装任务，最多可以包含 10 个任务，每个任务必须指定 `name` 参数。
- 原文件的保存路径由[上传参数](#upload_args)中的 `save-key` 指定。
- 异步图片处理，详见 「图片处理」 > 「[上传预处理（异步）](/cloud/image/#async_upload_process)」；异步音视频处理，详见「音视频处理」 > 「[上传预处理](/cloud/av/#async_upload_process)」；异步内容审核，详见 「内容审核」 > 「[上传预处理](/cloud/audit/#async_upload_process)」。

---------

### 响应信息

异步处理任务创建成功后，在上传返回信息和回调信息（详见[通知规则](#notify_return)）中会带上 `task_ids` 字段，里面包含每个任务的 `task_id`。

---------

### 回调通知

异步处理任务完成后，如果存在 `notify_url`（任务中不存在 `notify_url`，使用[上传参数](#upload_args)中的 `notify_url`，如果上传参数中也不存在，不回调），以回调的形式发送处理结果，里面包含 `task_id` 等信息，具体详见各服务的回调通知。

---------

<a name="ext-param"></a>
## ext-param

当使用 `return-url` 或 `notify-url` 参数发送返回信息或回调信息时，结果信息（详见[通知规则](#notify_return)）仅包含又拍云指定的内容，无法回传用户自定义的内容。如果需要回传自定义的内容，可以指定 `ext-param` 参数。

- 自定义的内容必须是 UTF-8 编码，且长度不能超过 255 个字节。
- `notify-url` 回调是异步进行的，如果回调信息需要区分自定义内容来自那个上传请求，可以在 `ext-param` 参数中添加个 clientId_xxx 标识。

---------

## 签名认证（旧）

### 请求签名（旧）

**请求方法**

```sh
curl http://v0.api.upyun.com/<bucket> \
    -F file=@<filename> \
    -F policy=<policy> \
    -F signature=<signature> \
	# 其他参数...
```

**signature 算法**

为了保证服务的安全性，使用服务的表单 API 密钥（可登录又拍云控制台查看）对 policy 进行签名，签名的结果即是 signature。

signature 生成步骤：

1. 生成 policy 字符串（见 [policy 算法](/api/authorization/#policy)）；
2. 将第 1 步中的字符串与表单 API 密钥字符串用 `&` 拼接；
3. 将第 2 步中的字符串计算 md5，所得即为 `signature`。


例如，假设服务的表单 API 验证密钥为：`cAnyet74l9hdUag34h2dZu8z7gU=`

第一步，生成 policy 字符串：

```
eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9
```

第二步，第一步的字符串与表单 API 密钥字符串用 `&` 拼接：

```
eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9&cAnyet74l9hdUag34h2dZu8z7gU=
```

第三步，将第二步的字符串计算 md5 后即得到 `signature`：

```
646a6a629c344ce0e6a10cadd49756d4
```

---------

<a name="sign"></a>
### 回调签名（旧）

**sign 算法**

`sign` 是根据 `code`、`message`、`url`、`time` 和表单 API 验证密匙(`form_api_secret`) 使用 `&` 拼接后进行 MD5 处理得到。拼接时需注意 URL 的编码与解码问题。如果使用了 `ext-param` 参数, `ext-param` 也需要使用 `&` 拼接在 `form_api_secret` 之后。

如果上传的是图片，存在 `image-width`、`image-height`、`image-frames` 和 `image-type` 四个参数，这四个参数不参与签名计算。
如果设置了 `apps` 参数，存在 `task_ids`，它也不参与签名计算。

例如，code：`200`

message：`'ok'`

url：`'/2015/06/17/190623/upload_QQ 图片 201506011111206f7c696f0920f097d7eefd750334003e.png'`

time：`1434539183`

form_api_secret：`lGetaXubhGezKp89+6iuOb5IaS3=`

拼接后的字符串：

```
200&ok&/2015/06/17/190623/upload_QQ 图片 201506011111206f7c696f0920f097d7eefd750334003e.png&1434539183&lGetaXubhGezKp89+6iuOb5IaS3=
```

`sign` 是 `'086c46cfedfc22bfa2e4971a77530a76'`。

回调信息（已经签名）是：

```
{"code":200,"message":"ok","url":"\/2015\/06\/17\/190623\/upload_QQ\u56fe\u7247201506011111206f7c696f0920f097d7eefd750334003e.png","time":1434539183,"image-width":1024,"image-height":768,"image-frames":1,"image-type":"PNG","sign":"086c46cfedfc22bfa2e4971a77530a76"}
```

**no-sign 算法**

如果发生异常，表单 API 验证秘钥获取失败，返回 `no-sign`。`no-sign` 算法跟 `sign` 相同，只是表单 API 验证密匙(`form_api_secret`) 为空字符串 `''`。

例如，使用上例参数拼接后的字符串是：

```
200&ok&/2015/06/17/190623/upload_QQ 图片 201506011111206f7c696f0920f097d7eefd750334003e.png&1434539183
```
`no-sign` 是 `'bbaeeb9d05623fe1b380f756a291011a'`。

---------

## 图片预处理（旧）

| 参数                   		| 必选	| 说明                                       				    |
|-------------------------------|-------|---------------------------------------------------------------|
| x-gmkerl-extract-color-count  | 否   	| 主题色提取的颜色数量，默认 `256`                                 	|
| x-gmkerl-extract-format       | 否   	| 主题色提取格式，默认 `dec`                                      	|
| x-gmkerl-thumbnail            | 否   	| 缩略图版本名称，可搭配其他 `x-gmkerl-*` 参数使用                  	|
| x-gmkerl-type                 | 否   	| 缩略类型，见 [x-gmkerl-type 可选值列表](#gmkerl-type)           	|
| x-gmkerl-value                | 否   	| 缩略类型对应的参数值，见 [x-gmkerl-value 值说明](#gmkerl-value)  	|
| x-gmkerl-quality              | 否   	| 压缩质量，默认 `95`                                          	|
| x-gmkerl-unsharp              | 否   	| 是否锐化，默认锐化                                        		|
| x-gmkerl-rotate               | 否   	| 图片旋转（顺时针），可选值：`auto`、`(0, 360]`。详见[旋转](/cloud/image/#rotate) |
| x-gmkerl-crop                 | 否   	| 图片裁剪，格式：`<w>x<h>a<x>a<y>`。详见[裁剪](/cloud/image/#crop)	|
| x-gmkerl-exif-switch          | 否   	| 是否保留 EXIF 信息，默认不保留 <br /> 仅在搭配 `x-gmkerl-crop`、`x-gmkerl-type`、`x-gmkerl-thumbnail` 时有效 	|


<a name="gmkerl-type"></a>
**x-gmkerl-type 可选值列表**

| 值             			| 含义             					|
|---------------------------|-----------------------------------|
| `fix_width`               | 限定宽度，高度自适应                	|
| `fix_height`              | 限定高度，宽度自适应                	|
| `fix_width_or_height`     | 限定宽度和高度，宽高不足时不缩放     	|
| `fix_both`                | 固定宽度和高度，宽高不足时强行缩放   	|
| `fix_max`                 | 限定最长边，短边自适应              	|
| `fix_min`                 | 限定最短边，长边自适应              	|
| `fix_scale`               | 等比例缩放（1-1000）               	|


<a name="gmkerl-value"></a>
**x-gmkerl-value 值说明**

* 当 `x-gmkerl-type` 指定为 `fix_width_or_height` 或 `fix_both` 时，`x-gmkerl-value` 值的格式为 `<width>x<height>`，如 `480x576`。
* 当 `x-gmkerl-type`  为其他的类型，`x-gmkerl-value` 值只需要指定单个数字，如 `100`，意思为高和/或宽为 `100`。

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
