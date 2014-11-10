HTTP FORM API（表单 API） 功能允许用户直接上传文件到UPYUN，而不需要通过客户服务器进行中转，从而为客户网站提供了便捷的解决方案，极大的降低了服务器成本。

普通上传流程如下：

![普通上传流程](http://our80.b0.upaiyun.com/wiki_pic/form_api/normal_form.png)

UPYUN表单流程如下：

![UPYUN表单流程](http://our80.b0.upaiyun.com/wiki_pic/form_api/upyun_form.png)

对比两种流程方式，UPYUN表单功能具有以下几点**优势**：

* 避免了客户服务器的文件中转，减少了不必要的流量、带宽和客户服务器开销
* UPYUN 自动选择最近最快的节点接收客户端上传的文件，文件上传速度更快
* 客户服务端无需开发多余的业务代码，减少开发和测试成本，提高开发效率


该功能支持多客户端浏览器、客户端软件、手机 APP 等。

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
curl http://v0.api.upyun.com/<bucket-name> \
    -F file=@<filename> \
    -F policy=<policy> \
    -F signature=<signature>

```

### Signature和Policy算法 ###

**Policy**

用于将上传请求相关的参数，例如保存路径，文件类型，预处理结果等，另外，也包含了上传请求授时间等。


Policy 生成步骤：

1. 将请求所需的参数键值对转换为 JSON 字符串
2. 将第 1 步中所得字符串进行 Base64 处理，即得 Policy

**注：**
>  Policy 必须是 UTF-8 编码格式，且不包含换行符，否则可能无法通过签名验证。

**Signature**

为了保证空间的安全性，UPYUN 表单 API 的上传需要使用到每个空间对应的「表单 API 验证密钥（form_api_secret）」

通过将表单 API 与 Policy 组合，并进行 MD5 计算后，所得的 MD5 值将作为 `Signature` 用于安全校验。

Signature 生成步骤：

1. 生成 Policy 字符串
2. 将第 1 步中所得字符串与您的表单API（可登录 UPYUN 官网获取）字符串用 `&` 拼接
3. 将第 2 步中所的的字符串计算 md5，所得即为 Signature

如，假设一个请求所需的的策略参数如下表:

|     键     |     值     |
|------------|------------|
| bucket     | demobucket |
| expiration | 1409200758 |
| save-key   | /img.jpg   |

另设，该空间表单 API 为：`cAnyet74l9hdUag34h2dZu8z7gU=`

则，
__计算 `policy` 相应步骤所得结果如下：__

第一步，将`参数组`转换为 JSON 字符串后：
```
{"bucket":"demobucket","expiration":1409200758,"save-key":"/img.jpg"}
```

第二步，将第一步所得字符串 base64 编码后：
2.
```
eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9
```

__计算 `signature` 相应步骤所的结果如下：__

第一步，生成 Policy：
```js
eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9
```

第二步，第一步结果加上表单API后：
```
eyJidWNrZXQiOiJkZW1vYnVja2V0IiwiZXhwaXJhdGlvbiI6MTQwOTIwMDc1OCwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9&cAnyet74l9hdUag34h2dZu8z7gU=
```

第三步，将第二步所得字符计算 md5 后：
```
646a6a629c344ce0e6a10cadd49756d4
```


### 表单 API 参数


|         参数         | 必选 |                                                 说明                                                |
|----------------------|------|-----------------------------------------------------------------------------------------------------|
| bucket               | 是   | 保存所上传的文件的 UPYUN 空间名                                                                     |
| save-key             | 是   | 保存路径，如: '/path/to/file.ext'，可用占位符 [\[注1\]](#note1)                      |
| expiration           | 是   | 请求的过期时间，UNIX 时间戳（秒）                                                                   |
| allow-file-type      | 否   | 文件类型限制，制定允许上传的文件扩展名                                                              |
| content-length-range | 否   | 文件大小限制，格式：`min,max`，单位：字节，如 `102400,1024000`，允许上传 100Kb～1Mb 的文件          |
| content-md5          | 否   | 所上传的文件的 MD5 校验值，UPYUN 根据此来校验文件上传是否正确                                       |
| content-secret       | 否   | 原图访问密钥 [\[注2\]](#note2)                                                 |
| content-type         | 否   | UPYUN 默认根据扩展名判断，手动指定可提高精确性                                                      |
| image-width-range    | 否   | 图片宽度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传宽度为 0～1024px 之间               |
| image-height-range   | 否   | 图片高度限制，格式：`min,max`，单位：像素，如 `0,1024`，允许上传高度在 0～1024px 之间               |
| notify-url           | 否   | 异步通知 URL，见 [\[通知规则\]](#notify_return)                                                      |
| return-url           | 否   | 同步通知 URL，见 [\[通知规则\]](#notify_return)                                                      |
| x-gmkerl-thumbnail   | 否   | 缩略图版本名称，仅支持图片类空间，可搭配其他 `x-gmkerl-*` 参数使用 [\[注3\]](#note3) |
| x-gmkerl-type        | 否   | 缩略类型 [\[注4\]](#note4)                                                           |
| x-gmkerl-value       | 否   | 缩略类型对应的参数值 [\[注4\]](#note5)                                               |
| x-gmkerl-quality     | 否   | **默认 95**缩略图压缩质量                                                                           |
| x-gmkerl-unsharp     | 否   | **默认锐化（true）**是否进行锐化处理                                                                |
| x-gmkerl-rotate      | 否   | 图片旋转（顺时针），可选：`auto`，`90`，`180`，`270` 之一                                           |
| x-gmkerl-crop        | 否   | 图片裁剪，格式：`x,y,width,height`，均需为正整型                                                    |
| x-gmkerl-exif-switch | 否   | 是否保留 exif 信息，仅在搭配 `x-gmkerl-crop`，`x-gmkerl-type`，`x-gmkerl-thumbnail` 时有效。        |
| ext-param            | 否   | 额外参数，UTF-8 编码，并小于 255 个字符 [\[注5\]](#note5)                            |


**注：**
> * 若在上传非图片文件时使用 `image-width-range`，`image-height-range`，会返回「不是图片」的错误；
> * 若请求中带有图片处理参数，最终只保存处理后的图片，上传的原图不保存；
> * 若同时提交旋转和裁剪参数，系统将按照先旋转，后裁剪的顺序进行处理；
> * 若原图中包含EXIF信息，那么旋转操作后保存的图片中，EXIF信息的图片方向将被修改；
> * 图片处理参数仅在上传图片时有效，暂不支持对已保存在UPYUN的原图进行图片处理。

<a name="notify_return"></a>
### 通知规则

* 如果没有设置 `return-url`，那么 UPYUN 处理完上传操作后，将把结果信息返回输出到 body 中。
* 如果设置了 `return-url`，那么 UPYUN 处理完上传操作后，将会把结果信息以 Query String 的形式追加到 `return-url` 指定的 URL 后，并进行 302 跳转，如，`return-url` 为 *`http://yourdomain.com/return/`* 那么，所跳转的 URL 可能会如下所示：
* URL 中包括：code、message、url、time 和 sign(或 non-sign) 参数。[\[注6\]](#note6)

```
http://yourdomain.com/return/?code=503&message=%E6%8E%88%E6%9D%83%E5%B7%B2%E8%BF%87%E6%9C%9F&url=%2F2011%2F12%2Ffd0e30047f81fa95.bz2&time=1332129461&sign=b11cb84538e884d63e14e52d35a7bd21
```

如果是图片类空间，则会额外增加：image-width、image-height、image-frames 和 image-type 四个参数。

* 如果设置了 `notify-url`，那么UPYUN处理完上传操作后，服务端将以发送 POST 请求的方式把上传至 `notify-url` 对应的地址，POST 所提交的参数和上述 `return-url` 中的相同

### 表单API状态代码表

| HTTP 状态码 |                 返回代码                |                                       含义                                      |
|-------------|-----------------------------------------|---------------------------------------------------------------------------------|
|         200 | OK                                      | 文件上传成功                                                                    |
|         400 | Is not a multipart request.             | 请求方式错误：非表单形式                                                        |
|         400 | Form parameter invalid.                 | 请求参数不合法                                                                  |
|         400 | Not accept, Miss policy.                | 缺少 policy 参数                                                                |
|         400 | Not accept, Miss signature.             | 缺少 signature 参数                                                             |
|         400 | Not accept, No file data.               | 没有上传任何文件数据，请检查表单中文件域的名称是否为 file                       |
|         400 | Not accept, Bucket is null.             | 空间名参数为空                                                                  |
|         400 | Not accept, Save-key is null.           | 保存路径参数为空                                                                |
|         400 | Not accept, Expiration is null.         | 授权过期参数为空                                                                |
|         400 | Not accept, Ext-param too long.         | 额外参数内容长度过长                                                            |
|         403 | Not accept, POST URI error.             | 表单提交的 URI 错误                                                             |
|         403 | Authorize has expired.                  | 上传授权已过期(请检查服务器时间或设置的过期时间是否正确)                        |
|         403 | Not accept, Signature error.            | 签名错误，可能是表单签名密匙不正确                                              |
|         403 | Not accept, Form API disabled.          | 表单 API 功能未启用                                                             |
|         403 | Not accept, Content-md5 error.          | 上传文件内容的 md5 校验值错误，客户端传递的 md5 值与服务器端计算的 md5 值不相同 |
|         403 | Content-Secret only accept [a-zA-Z0-9]. | 原图保护密钥使用了不允许的字符                                                  |
|         403 | Not accept, File size too small.        | 上传文件过小                                                                    |
|         403 | Not accept, File size too large.        | 上传文件过大                                                                    |
|         403 | Not accept, File type Error.            | 上传文件类型不允许，请参考 allow-file-type 参数                                 |
|         403 | Not accept, Not an image file.          | 上传的不是图片文件，请参考 image-width-range 参数                               |
|         403 | Not accept, Image width too small.      | 上传的图片宽度过小                                                              |
|         403 | Not accept, Image width too large.      | 上传的图片宽度过大                                                              |
|         403 | Not accept, Image height too small.     | 上传的图片高度过小                                                              |
|         403 | Not accept, Image height too large.     | 上传的图片高度过大                                                              |
|         403 | Image Rotate Invalid Parameters.        | 图片旋转参数错误                                                                |
|         403 | Image Crop Invalid Parameters.          | 图片裁剪参数错误                                                                |
|         403 | Bucket blocked.                         | 空间已被禁用                                                                    |
|         404 | Bucket does not exist.                  | 空间不存在                                                                      |
|         404 | Bucket removed.                         | 空间已被删除                                                                    |
|         503 | System Error, please try again.         | 系统错误，请重试                                                                |



### 注释说明

<a name="note1"></a>
#### 注1：save-key详细说明

|  类型  |                  格式                 |                      说明                     |
|--------|---------------------------------------|-----------------------------------------------|
| 绝对值 | String                                | 即手动指定具体的路径，如: `/path/to/file.ext` |
| 时间类 | {year} {mon} {day} {hour} {min} {sec} | 日期、时间相关内容                            |
| md5类  | {filemd5}                             | 文件内容的 md5 值                             |
| 随机类 | {random} {random32}                   | 16 位或 32 位随机字符和数字                   |
| 文件名 | {filename} {suffix} {.suffix}         | 上传文件的文件名及扩展名                      |

**举例说明：**

    上传文件名：sample.jpg
    当前时间：2013-01-01 00:00:00
    save-key：/{year}/{mon}/{day}/upload_{filename}{.suffix}
    那么最终的保存路径为：/2013/01/01/upload_sample.jpg

**提示：**

* 如果你的应用一次授权只允许上传 1 个文件，`save-key `请使用绝对值；
* 如果你的应用一次授权在过期时间内允许上传多个文件，`save-key `可使用一些命名变量；
* 上传多个文件时，为避免发生路径重名导致文件被覆盖的现象，建议使用 `random32 `等重复性较低的命名变量。


<a name="note2"></a>
#### 注2：content-secret详细说明 ####

**使用场景：**

原图保存在UPYUN后，不允许直接对外提供访问，仅允许以缩略图的方式，或带有水印的原图对外提供访问时，可以使用 `content-secret` 参数设置一个“访问密钥”。

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
#### 注3：图片缩略详细说明 ####

* 仅支持图片类空间，且只保存处理后的缩略图，不保存原图
* 在使用缩略图版本参数(`x-gmkerl-thumbnail`)前，需要确保已经存在[[如何创建自定义缩略图|缩略图版本号]]，否则将直接错误返回。
* 允许在使用缩略图版本参数(`x-gmkerl-thumbnail)`的同时，再搭配其他 `x-gmkerl-*` 参数对图片进行处理，优先级为 `x-gmkerl-*` 参数 > 缩略图版本中对应参数
* 因为文件类空间不存在缩略图版本配置，所以如果需要对原图进行缩略处理，可以使用 `x-gmkerl-type` 和 `x-gmkerl-value` 两个参数


<a name="note4"></a>
#### 注4：缩略类型详细说明 ####

|         参数        |                说明                |      单位      |             举如            |
|---------------------|------------------------------------|----------------|-----------------------------|
| fix_max             | 限定最长边，短边自适应             | 像素值         | 如: 150                     |
| fix_min             | 限定最短边，长边自适应             | 像素值         | 如: 150                     |
| fix_width_or_height | 限定宽度和高度，宽高不足时不缩放   | 像素值         | width x height，如: 150x130 |
| fix_width           | 限定宽度，高度自适应               | 像素值         | 如: 150                     |
| fix_height          | 限定高度，宽度自适应               | 像素值         | 如: 150                     |
| fix_both            | 固定宽度和高度，宽高不足时强行缩放 | 像素值         | width x height，如: 150x130 |
| fix_scale           | 等比例缩放                         | 比例值（1-99） | 如: 50                      |


<a name="note5"></a>
#### 注5：额外参数详细说明 ####

**使用场景：**

当用户使用`return-url`或`notify-url`参数时，upyun 会将表单操作的结果，以同步或异步的方式返回给用户，结果内容包含 code、message、url、time 和 sign(或 non-sign 见[通知规则](#notify_return)，无法回传其他内容。而 `ext-param` 参数可将用户自定义的内容原封不动的返回给用户。


**使用须知：**

仅支持 UTF-8 格式，最长不超过255个字节。


**举例说明：**

设置过`notify-url`的表单请求提交后，upyun 将以异步的方式把操作结果返回给用户。用户在接收到这个结果时可能无法得知是之前的哪个请求的结果，所以，在表单提交前可以给 `ext-param` 参数设置一个clientId_xxx，用户在 upyun 返回的结果中就可以获得这个 clientId_xxx 值并对之前的某个请求做后续的处理了。



<a name="note6"></a>
#### 注6：sign与non-sign参数说明 ####

* sign 是根据 code、message、url 、time 和表单 API 验证密匙(`form_api_secret`) 使用 `&` 拼接后进行 md5 处理得到。
* 如果因发生错误，表单 API 未取得，则会特殊的返回 `non-sign`，其处理方式和上述 `sign` 相同，只是没有表单 API 验证密匙(`form_api_secret`)
