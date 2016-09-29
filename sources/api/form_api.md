HTTP FORM API 允许用户直接上传文件到又拍云，而不需要通过客户服务器进行中转。支持 HTTP 和 HTTPS 协议，您可以选择最优的方式提交请求。

普通上传与又拍云 FORM API 上传流程如下：

```

                                       +-----------------+ +-----------------+ +-----------------+
                                       | Client/Browser  | |    FORM API     | | Customer Server |
                                       +-----------------+ +-----------------+ +-----------------+
        +----------------+                      |                   |                   |
        |  User/Client   |                      |                   |                   |
        +---+------------+                     +++    Request signature and policy     +++
            |        ^                         |-|====================================>|-|
            |        |                         |-|                  |                  |-|
            |        |                         |-|                  |                  |-|
            v        |                         |-|    Response signature and policy    |-|
        +----------------+                     |-|<====================================|-|
        |Customer Server |                     +++                  |                  +++
        +---+------------+                      |                   |                   |
            |        ^                          |                   |                   |
            |        |                          |                   |                   |
            |        |                         +++     Upload      +++                 +++
            v        |                         |-|================>|-|     Notify      |-|
        +----------------+                     |-|                 |-|---------------->|-|
        |   UPYUN API    |                     |-|                 |-|                 |-|
        +----------------+                     |-|Response/Redirect|-|                 |-|
                                               |-|<================|-|                 |-|
                                               +++                 +++                 +++
                                                |                   |                   |
                                                |                   |                   |

            普通上传流程                                   UPYUN FORM API 上传流程

```

对比两种流程方式，又拍云 FORM API 具有以下几点**优势**：

* 避免了客户服务器的文件中转，减少了不必要的流量、带宽和客户服务器开销
* 又拍云自动选择最近最快的节点接收客户端上传的文件，文件上传速度更快
* 客户服务端无需开发多余的业务代码，减少开发和测试成本，提高开发效率

使用表单 API 进行文件上传，推荐的采用以下流程：

1. 请求客户服务器，生成、获取上传所需的 signature、policy 参数。为了提升上传成功率（避免签名过期）、安全性(设置尽可能短的授权有效期)，我们建议在每次执行上传操作之前，都重新执行本步骤获取相关参数；
2. 请求又拍云 API 上传文件，校验返回结果/异步回调通知客户服务器；
3. 处理常规客户业务流程。


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
curl http://v0.api.upyun.com/<bucket-name> \
    -F file=@<filename> \
    -F policy=<policy> \
    -F signature=<signature>

```

**注：**

> 请确认您上传的表单格式符合 [RFC 1867](https://www.ietf.org/rfc/rfc1867.txt) 协议规范。

### policy 算法 ###

policy 用于上传请求相关的参数设置，例如保存路径，文件类型，预处理结果，上传请求授权时间等。

policy 生成步骤：

1. 将请求所需的参数键值对转换为 JSON 字符串
2. 将第 1 步中所得字符串进行 Base64 Encode 处理，即得 policy

**注：**
>  policy 必须是 **UTF-8** 编码格式，且**不包含换行符**，否则可能无法通过签名验证。

以下是一个计算 policy 的例子：

>假设一个请求所需的的策略参数如下表:
>
>|     键     |     值     |
>|------------|------------|
>| bucket     | demobucket |
>| expiration | 1409200758 |
>| save-key   | /img.jpg   |
>
>第一步，将参数组转换为 JSON 字符串后：
>
>```
>{"bucket":"demobucket","expiration":1409200758,"save-key":"/img.jpg"}
>```
>
>第二步，将第一步所得字符串 base64 编码即得到 policy：
>
>```
>eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9
>```


### signature 算法

为了保证空间的安全性，又拍云表单 API 的上传需要使用到每个空间对应的「表单 API 验证密钥（form_api_secret）」

通过将表单 API 与 policy 组合，并进行 MD5 计算后，所得的 MD5 值将作为 `signature` 用于安全校验。

signature 生成步骤：

1. 生成 `policy` 字符串
2. 将第 1 步中所得字符串与您的「表单 API 验证密钥」（可登录又拍云官网获取）字符串用 `&` 拼接
3. 将第 2 步中所得的字符串计算 md5，所得即为 `signature`


以下是一个计算 signature 的例子：

>假设该空间的「表单 API 验证密钥」为：`cAnyet74l9hdUag34h2dZu8z7gU=`
>
>第一步，计算得到相应的 policy 字符串为（见 [policy 算法](#policy)）：
>
>```
>eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9
>```
>
>第二步，第一步结果与「表单 API 验证密钥」用 `&` 拼接后得到：
>
>```
>eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9&cAnyet74l9hdUag34h2dZu8z7gU=
>```
>
>第三步，将第二步所得字符串计算 md5 后即得到 `signature`：
>
>```
>646a6a629c344ce0e6a10cadd49756d4
>```
>

### 表单 API 参数


|         参数         | 必选 |                                                 说明                                                |
|----------------------|------|-----------------------------------------------------------------------------------------------------|
| bucket               | 是   | 保存所上传的文件的又拍云服务名                                                                      |
| save-key             | 是   | 保存路径，如: '/path/to/file.ext'，可用占位符 [\[注 1\]](#note1)                                    |
| expiration           | 是   | 请求的过期时间，**UNIX UTC** 时间戳（秒）                                                           |
| allow-file-type      | 否   | 允许上传的文件扩展名，以 `,` 分隔。如 `jpg,jpeg,png`                                                |
| content-length-range | 否   | 文件大小限制，格式：`min,max`，单位：字节，如 `102400,1024000`，表示允许上传 100Kb～1Mb 的文件      |
| content-md5          | 否   | 所上传的文件的 MD5 校验值，又拍云根据此来校验文件上传是否正确                                       |
| content-secret       | 否   | 文件访问密钥 [\[注 2\]](#note2)                                                                     |
| content-type         | 否   | 又拍云默认根据扩展名判断，手动指定可提高精确性。如 `image/jpeg`                                     |
| image-width-range    | 否   | 图片宽度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传宽度为 0～1024px 之间               |
| image-height-range   | 否   | 图片高度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传高度在 0～1024px 之间               |
| notify-url           | 否   | 异步通知 URL，见 [通知规则](#notify_return)                                                         |
| return-url           | 否   | 同步通知 URL，见 [通知规则](#notify_return)                                                         |
| x-gmkerl-thumb       | 否   | 图片处理参数，见 [上传作图](/cloud/image/#_2)                                                       |
| x-gmkerl-type        | 否   | `get_meta`（获取图片信息）或 `get_theme_color`（主题色提取）                                        |
| x-gmkerl-extract-color-count       | 否   |     主题色提取颜色数量，默认 `256`                                                    |
| x-gmkerl-extract-format     | 否   |   主题色提取格式，默认 `hex`                                                                 |
| apps                 | 否   | json 格式的异步处理任务列表。见 [异步处理任务](/api/form_api/#_4)                                   |
| ext-param            | 否   | 额外参数，UTF-8 编码，并小于 255 个字符 [\[注 3\]](#note3)                                          |
| b64encoded           | 否   | 表示上传的文件需要`base64`解码。可用值为`on`                                                        |
| *x-gmkerl-thumbnail* **DEPRECATED**   | *否*   | *缩略图版本名称，仅支持图片类空间，可搭配其他 `x-gmkerl-*` 参数使用* |
| *x-gmkerl-type*  **DEPRECATED**       | *否*   | *缩略类型*                                                           |
| *x-gmkerl-value*  **DEPRECATED**      | *否*   | *缩略类型对应的参数值*                                               |
| *x-gmkerl-quality*  **DEPRECATED**    | *否*   | ***默认 95**缩略图压缩质量*                                                                           |
| *x-gmkerl-unsharp*   **DEPRECATED**   | *否*   | ***默认锐化（true）**是否进行锐化处理*                                                                |
| *x-gmkerl-rotate*  **DEPRECATED**     | *否*   | *图片旋转（顺时针），可选：`auto，90，180，270` 之一*                                           |
| *x-gmkerl-crop*  **DEPRECATED**       | *否*   | *图片裁剪，格式：`<w>x<h>a<x>a<y>`。 其中 w, h 分别表示裁剪后的宽和高，x, y 表示左上角坐标。如 100x100a0a0。*         |
| *x-gmkerl-exif-switch* **DEPRECATED** | *否*   | *是否保留 EXIF 信息，仅在搭配 `x-gmkerl-crop，x-gmkerl-type，x-gmkerl-thumbnail` 时有效。*        |


**注：**
> * 以上参数名和参数值均 **区分大小写**。
> * 一次请求只允许上传一个文件。
> * 若在上传非图片文件时使用 `image-width-range`，`image-height-range`，会返回「不是图片」的错误。
> * 若请求中带有图片处理参数（`x-gmkerl-thumb`），最终只保存处理后的图片，上传的原图不保存。
> * 若设置了 `x-gmkerl-type` 参数，则图片上传完成后会返回原图的 meta 信息或主题色。

<a name="notify_return"></a>
### 通知规则

* 如果没有设置 `return-url`，那么又拍云处理完上传操作后，将把结果信息返回输出到 body 中。
* 如果设置了 `return-url`，那么又拍云处理完上传操作后，将会把结果信息以 Query String 的形式追加到 `return-url` 指定的 URL 后，并进行 302 跳转，如，`return-url` 为 *`http://yourdomain.com/return/`* 那么，所跳转的 URL 可能会如下所示：
* URL 中包括：code、message、url、time 和 sign(或 no-sign) 参数。[\[注 4\]](#note4)

```
http://yourdomain.com/return/?code=503&message=%E6%8E%88%E6%9D%83%E5%B7%B2%E8%BF%87%E6%9C%9F&url=%2F2011%2F12%2Ffd0e30047f81fa95.bz2&time=1332129461&sign=b11cb84538e884d63e14e52d35a7bd21
```

如果上传的文件是图片，则会额外增加：image-width、image-height、image-frames 和 image-type 四个参数（这四个参数不参与加密签名）。

* 如果设置了 `notify-url`，那么又拍云处理完上传操作后，服务端将以发送 `POST` 请求的方式把上传至 `notify-url` 对应的地址，POST 所提交的参数和上述 `return-url` 中的相同


### 返回信息

1. 上传成功返回 `200` 或执行 302 跳转（见 [通知规则](#notify_return)）
2. 上传失败返回相应的出错信息，具体请参阅「[API 错误码表](/api/errno/)」


### 异步处理任务

如果指定了 `apps` 参数，那么在文件上传到又拍云之后，又拍云会发起相应的异步处理任务。目前，又拍云支持**异步图片处理**和**异步音视频处理**服务。

`apps` 参数为 json 格式的任务列表，结构及说明示例如下：

```
apps = [
    {                                              // 任务1
        "name": "thumb",                           // 异步任务名称
        "x-gmkerl-thumb": "/fw/100/quality/95",    // 任务参数
        "save_as": "/path/to/fw_100.jpg"           // 结果存放路径
    },
    {                                              // 任务2
        "name": "thumb",
        "x-gmkerl-thumb": "/fw/300/quality/95",    // 任务参数
        "save_as": "/path/to/fw_300.jpg",          // 结果存放路径
        "notify_url": "http://your/notify/url"     // 指定回调地址，可以覆盖表单参数中的 notify_url
    }
]
```

上例中的 apps 会在文件上传成功后生成两个异步作图任务，两个任务的作图参数分别是 `/fw/100/quality/95` 和 `/fw/300/quality/95`，作图结果分别被保存到 `/path/to/fw_100.jpg` 和 `/path/to/fw_300.jpg`。


> **注**
>
> - `apps` 里最多包含 10 个任务
> - `name` 为必选参数，详见 [异步任务名称](/api/form_api/#_5)
> - 异步处理的源文件路径为表单参数 `save-key` 指定的路径
> - 任务参数中可以指定任务完成后的回调地址，如果没有指定，则使用表单参数中的 `notify_url`

异步任务提交成功后，又拍云会在返回信息和回调信息里带上 `task_ids` 字段，里面包含每个任务的 `task_id`。异步任务处理完成后，又拍云 会往 `notify_url` （如果设置了这个参数的话）以回调的形式发送处理结果，回调信息里包含相应的 `task_id` 等信息。

#### 异步任务名称

|  任务名  |                  说明                     |
|--------|-------------------------------------------|
|  `thumb` | [异步作图服务](/cloud/image/#_4)                             |
|  `naga`  | [异步音视频处理服务](/cloud/av/#_8)                        |



### 注释说明

<a name="note1"></a>
#### 注 1：save-key 详细说明

|  类型  |                  格式                 |                      说明                     |
|--------|---------------------------------------|-----------------------------------------------|
| 绝对值 | String                                | 即手动指定具体的路径，如: `/path/to/file.ext` |
| 时间类 | {year} {mon} {day} {hour} {min} {sec} | 日期、时间相关内容（UTC 时间）                            |
| md5 类  | {filemd5}                             | 文件内容的 md5 值                             |
| 随机类 | {random} {random32}                   | 16 位或 32 位随机字符和数字                   |
| 文件名 | {filename} {suffix} {.suffix}         | 上传文件的文件名及扩展名                      |

**举例说明：**

    上传文件名：sample.jpg
    UTC 时间：2013-01-01 00:00:00
    save-key：/{year}/{mon}/{day}/upload_{filename}{.suffix}
    那么最终的保存路径为：/2013/01/01/upload_sample.jpg

    上传文件名：sample
    文件 md5：3691308f2a4c2f6983f2880d32e29c84
    UTC 时间：2013-01-01 12:34:56
    save-key：/{year}/{mon}/{day}/{hour}/{min}/{sec}/upload_{filemd5}{.suffix}
    那么最终的保存路径为：/2013/01/01/12/34/56/upload_3691308f2a4c2f6983f2880d32e29c84

**提示：**

> * 上传多个文件时，为避免发生路径重名导致文件被覆盖的现象，建议使用 `random32 `等重复性较低的命名变量。


<a name="note2"></a>
#### 注 2：content-secret 详细说明 ####

**使用场景：**

原图保存在又拍云后，不允许直接对外提供访问，仅允许以缩略图的方式，或带有水印的原图对外提供访问时，可以使用 `content-secret` 参数设置一个“访问密钥”。

**使用须知：**

* 仅支持图片类空间，且必须存在[缩略图版本号](如何创建自定义缩略图)！
* 若设置了“访问密钥”，则无法根据原文件 URL 直接访问，需在 URL 后面加上 (缩略图间隔标志符+密钥) 进行访问

**举例说明：**

    图片类空间名：demobucket
    缩略图间隔标志符：!
    密钥：secret
    保存的文件路径：/dir/sample.jpg
  那么该图片的对外访问地址为：http://demobucket.b0.upaiyun.com/dir/sample.jpg!secret

<a name="note3"></a>
#### 注 3：额外参数详细说明 ####

**使用场景：**

当用户使用`return-url`或`notify-url`参数时，又拍云会将表单操作的结果，以同步或异步的方式返回给用户，结果内容包含 code、message、url、time 和 sign(或 no-sign 见[通知规则](#notify_return)，无法回传其他内容。而 `ext-param` 参数可将用户自定义的内容原封不动的返回给用户。


**使用须知：**

仅支持 UTF-8 格式，最长不超过 255 个字节。


**举例说明：**

设置过`notify-url`的表单请求提交后，又拍云将以异步的方式把操作结果返回给用户。用户在接收到这个结果时可能无法得知是之前的哪个请求的结果，所以，在表单提交前可以给 `ext-param` 参数设置一个 clientId_xxx，用户在又拍云返回的结果中就可以获得这个 clientId_xxx 值并对之前的某个请求做后续的处理了。



<a name="note4"></a>
#### 注 4：sign 与 no-sign 参数说明 ####

* sign 是根据 code、message、url、time 和表单 API 验证密匙(`form_api_secret`) 使用 `&` 拼接后进行 md5 处理得到。拼接时需注意 url 的编码与解码问题。如果使用了 `ext-param` 参数, 则 `ext-param` 也需加入签名计算中。同样使用 `&` 拼接。
拼接在 `form_api_secret` 之后即可。

示例:

回调信息

```
{"code":200,"message":"ok","url":"\/2015\/06\/17\/190623\/upload_QQ\u56fe\u7247201506011111206f7c696f0920f097d7eefd750334003e.png","time":1434539183,"image-width":1024,"image-height":768,"image-frames":1,"image-type":"PNG","sign":"086c46cfedfc22bfa2e4971a77530a76"}
```
则有:

code: `200`

message: `'ok'`

url: `'/2015/06/17/190623/upload_QQ 图片 201506011111206f7c696f0920f097d7eefd750334003e.png'`

time: `1434539183`

form_api_secret: `lGetaXubhGezKp89+6iuOb5IaS3=`

拼接后的字符串为:

```
200&ok&/2015/06/17/190623/upload_QQ 图片 201506011111206f7c696f0920f097d7eefd750334003e.png&1434539183&lGetaXubhGezKp89+6iuOb5IaS3=

```

最终的签名为: `'086c46cfedfc22bfa2e4971a77530a76'`

* 如果因发生错误，表单 API 未取得，则会特殊的返回 `no-sign`，其处理方式和上述 `sign` 相同，只是表单 API 验证密匙(`form_api_secret`) 为空字符串 `''`。
