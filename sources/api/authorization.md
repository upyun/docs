## 基本认证

```sh
curl -u <operator>:<password> http://v0.api.upyun.com/<bucket>/
```

或者，将操作员名和操作员的密码按 `operator:password` 拼接 Base64 编码后加在请求头的 `Authorization` 字段中：

```sh
curl -X GET \
    http://v0.api.upyun.com/<bucket>/ \
    -H "Authorization: Basic b3BlcmF0b3I6cGFzc3dvcmQ="
```

特别地，[HTTP 基本认证](http://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81)采用 Base64 编码和传输操作员信息，存在操作员信息泄露的风险。

---------

## 签名认证

为了避免基本认证中 Base64 编码可逆带来的安全隐患，又拍云提供了签名认证这种更安全的认证方式。它结合请求关键信息和用户身份信息，计算一个消息摘要，作为请求的 Authorization，保证请求的安全。

### 放在 HTTP Header 中

Authorization 放在 Header 中，REST API 、FORM API 的回调通知使用这种方式。

** 签名计算方法 **

```
Authorization: UPYUN <Operator>:<Signature>

<Signature> = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Content-MD5>
))
```

相关参数说明：

| 参数      		| 必选 	| 说明                                           	|
|---------------|-------|---------------------------------------------------|
| Operator      | 是  	| 服务的操作员名称                            		|
| Method        | 是  	| 请求方式，如：GET、POST、PUT、HEAD 等               	|
| URI           | 是  	| 请求路径	 |
| Date          | 是  	| 请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT` |
| Content-MD5   | 否  	| 请求体的 MD5 值，如果文件太大计算 MD5 不方便或请求体为空，可以为空			|
| Password      | 是  	| 服务的操作员密码的 MD5 值               				|

** 注 **

- 非必选参数为空时，连同前面的 `&` 一起不参与签名计算。所有计算的 MD5 值，格式均是 32 位小写。
- HMAC-SHA1 输出的必须是原生的二进制数据。
- REST API 签名有效期为 30 分钟；FORM API 回调通知签名有效期由用户自行确定，建议设置为 30 分钟。

** 举例 **

[REST API 上传文件](/api/rest_api/#_2)请求签名

```
// 操作员信息
Operator = operator123			
Password = MD5(password123) = 482c811da5d5b4bc6d497ffa98491e38

// 参数信息
Method = PUT							
URI = /upyun-temp/demo.jpg
Date = Wed, 09 Nov 2016 14:26:58 GMT
// 上传文件的 MD5
Content-MD5 = 7ac66c0f148de9519b8bd264312c4d64
```

生成 Signature：

```
Signature = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Content-MD5>
))
= Base64 (HMAC-SHA1 (482c811da5d5b4bc6d497ffa98491e38,PUT&/upyun-temp/demo.jpg&Wed, 09 Nov 2016 14:26:58 GMT&7ac66c0f148de9519b8bd264312c4d64))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= YUaAZX+WNAcJdNGHS5SBlITME5A=
```

Authorization 签名：

```
Authorization: UPYUN operator123:YUaAZX+WNAcJdNGHS5SBlITME5A=
```

请求 Header：

```
PUT /upyun-temp/demo.jpg HTTP/1.1
Authorization: UPYUN operator123:YUaAZX+WNAcJdNGHS5SBlITME5A=
Content-MD5: 7ac66c0f148de9519b8bd264312c4d64
Date: Wed, 09 Nov 2016 14:26:58 GMT
Content-Type: image/jpeg
Host: v0.api.upyun.com
Content-Length: 33456
```

[FORM API 回调通知](/api/form_api/#notify_return)签名

```
// 操作员信息
Operator = operator123			
Password = MD5(password123) = 482c811da5d5b4bc6d497ffa98491e38

// 参数信息
Method = POST							
URI = /upyun_notify_url
Date = Wed, 09 Nov 2016 14:26:58 GMT
Content-MD5 = MD5({"code": 200, "message": "ok", "url": "%2F2011%2F12%2Ffd0e30047f81fa95.mp3", "time": 1478701618})
	        = ed091459198a814d549701dab1dc4880
```

生成 Signature：

```
Signature = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Content-MD5>
))
= Base64 (HMAC-SHA1 (482c811da5d5b4bc6d497ffa98491e38,POST&/upyun_notify_url&Wed, 09 Nov 2016 14:26:58 GMT&ed091459198a814d549701dab1dc4880))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= 3x6z6M9U2Ugi1FxLPhQldiXFzAc=
```

Authorization 签名：

```
Authorization: UPYUN operator123:3x6z6M9U2Ugi1FxLPhQldiXFzAc=
```

完整的请求：

```
POST /upyun_notify_url HTTP/1.1
Authorization: UPYUN operator123:3x6z6M9U2Ugi1FxLPhQldiXFzAc=
Content-MD5: ed091459198a814d549701dab1dc4880
Date: Wed, 09 Nov 2016 14:26:58 GMT
Content-Type: application/json
Content-Length: 192

{"code": 200, "message": "ok", "url": "%2F2011%2F12%2Ffd0e30047f81fa95.mp3", "time": 1478701618}
```

---------

### 放在 HTTP Body 中

Authorization 放在 Body 中，FORM API 上传文件使用这种方式。

** 签名计算方法 **

```
authorization=UPYUN <Operator>:<Signature>

<Signature> = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Policy>&
<Content-MD5>
))
```

相关参数说明：

| 参数      		| 必选 	| 说明                                           	|
|---------------|-------|---------------------------------------------------|
| Operator      | 是  	| 服务的操作员名称                            		|
| Method        | 是  	| 请求方式，如：GET、POST、PUT、HEAD 等               	|
| URI           | 是  	| 请求路径    |
| Date          | 否  	| 请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT`  |
| Policy        | 是  	| [上传参数](/api/form_api/#upload_args)的 Base64 编码，详见 [Policy 算法](#policy)  |
| Content-MD5   | 否  	| 上传文件的 MD5 值									|
| Password      | 是  	| 服务的操作员密码的 MD5 值               				|

** 注 **

- 非必选参数为空时，连同前面的 `&` 一起不参与签名计算。所有计算的 MD5 值，格式均是 32 位小写。
- HMAC-SHA1 输出的必须是原生的二进制数据。
- 自定义签名有效期，建议为 30 分钟。如果签名超过有效期，需要重新生成签名。

<a name="policy"></a>
** Policy 算法 **

生成步骤：

1. 将[上传参数](/api/form_api/#upload_args)键值对转换为 JSON 字符串；
2. 将第 1 步所得到的字符串进行 Base64 Encode 处理，得到 policy。

** 注 **

- 参数和参数值必须是 UTF-8 编码，且不包含换行符。
- 参数 `date` 和 `content-md5` 就是签名中的 `Date` 和 `Content-MD5`。

** 举例 **

[FORM API 上传文件](/api/form_api/#_2)请求签名

```
// 操作员信息
Operator = operator123			
Password = MD5(password123) = 482c811da5d5b4bc6d497ffa98491e38

// 参数
Method = POST							
URI = /upyun-temp

// 上传参数，需要计算 Policy  
bucket = upyun-temp
save-key = /demo.jpg
expiration = 1478674618
date = Wed, 09 Nov 2016 14:26:58 GMT
content-md5 = 7ac66c0f148de9519b8bd264312c4d64
```

生成 Policy：

```
Policy = Base64 ({"bucket": "upyun-temp", "save-key": "/demo.jpg", "expiration": "1478674618", "date": "Wed, 09 Nov 2016 14:26:58 GMT", "content-md5": "7ac66c0f148de9519b8bd264312c4d64"})
	   = eyJidWNrZXQiOiAidXB5dW4tdGVtcCIsICJzYXZlLWtleSI6ICIvZGVtby5qcGciLCAiZXhwaXJhdGlvbiI6ICIxNDc4Njc0NjE4IiwgImRhdGUiOiAiV2VkLCA5IE5vdiAyMDE2IDE0OjI2OjU4IEdNVCIsICJjb250ZW50LW1kNSI6ICI3YWM2NmMwZjE0OGRlOTUxOWI4YmQyNjQzMTJjNGQ2NCJ9
```

生成 Signature：

```
Signature = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Policy>&
<Content-MD5>
))
= Base64 (HMAC-SHA1 (482c811da5d5b4bc6d497ffa98491e38,POST&/upyun-temp&Wed, 09 Nov 2016 14:26:58 GMT&eyJidWNrZXQiOiAidXB5dW4tdGVtcCIsICJzYXZlLWtleSI6ICIvZGVtby5qcGciLCAiZXhwaXJhdGlvbiI6ICIxNDc4Njc0NjE4IiwgImRhdGUiOiAiV2VkLCA5IE5vdiAyMDE2IDE0OjI2OjU4IEdNVCIsICJjb250ZW50LW1kNSI6ICI3YWM2NmMwZjE0OGRlOTUxOWI4YmQyNjQzMTJjNGQ2NCJ9&7ac66c0f148de9519b8bd264312c4d64))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= DTGOeaCa1yk1JWG4G3DH+u5sI5M=
```

Authorization 签名：

```
authorization=UPYUN operator123:DTGOeaCa1yk1JWG4G3DH+u5sI5M=
```

完整的请求：

```
POST /upyun-temp HTTP/1.1
Host: v0.api.upyun.com
Content-Type:multipart/form-data; boundary=——WebKitFormBoundaryE19zNvXGzXaLvS5C

----WebKitFormBoundaryE19zNvXGzXaLvS5C
Content-Disposition: form-data; name="policy"

eyJidWNrZXQiOiAidXB5dW4tdGVtcCIsICJzYXZlLWtleSI6ICIvZGVtby5qcGciLCAiZXhwaXJhdGlvbiI6ICIxNDc4Njc0NjE4IiwgImRhdGUiOiAiV2VkLCA5IE5vdiAyMDE2IDE0OjI2OjU4IEdNVCIsICJjb250ZW50LW1kNSI6ICI3YWM2NmMwZjE0OGRlOTUxOWI4YmQyNjQzMTJjNGQ2NCJ9&file=
----WebKitFormBoundaryE19zNvXGzXaLvS5C
Content-Disposition: form-data; name="file"; filename="demo.jpg"

def
----WebKitFormBoundaryE19zNvXGzXaLvS5C
Content-Disposition: form-data; name="authorization"

UPYUN operator123:DTGOeaCa1yk1JWG4G3DH+u5sI5M=
----WebKitFormBoundaryE19zNvXGzXaLvS5C
```

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)