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

为了避免基本认证中 Base64 编码可逆带来的安全隐患，又拍云云存储支持的更为安全的认证方式。API 使用这种方式进行认证鉴权。

** 签名格式 **

```
Authorization: UPYUN <Operator>:<Signature>
Date: <格林尼治标准时间>
Content-MD5: <Content-MD5>

<Signature> = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Policy>&
<Content-MD5>
))
```

相关参数说明：

| 参数      		| 必选 	|                       说明                                           |
|---------------|-------|------------------------------------------------------------------------------|
| Operator      | 是  	| 服务的操作员名称                            |
| Method        | 是  	| 请求方式，如：GET、POST、PUT、HEAD 等                            |
| URI           | 是  	| 请求路径，需要 URL encoding ([RFC 1738](http://tools.ietf.org/html/rfc1738 )) |
| Date          | 否  	| 请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT` <br /> REST API 中，必选；FORM API 中，非必选 |
| Policy        | 否  	| FORM API 中，表示上传参数的 Base64 编码，详见 [Policy 算法](#policy)；REST API 中，为空  |
| Content-MD5   | 否  	| FORM API 中，表示上传文件的 MD5 值；REST API 中，表示请求体的 MD5 值。			|
| Password      | 是  	| 服务的操作员密码的 MD5 值               |

** 注 **

- 非必选参数为空时，不参与签名计算。所有计算的 MD5 值，格式均是 32 位小写。HMAC-SHA1 输出的必须是原生的二进制数据。
- 请求签名：[REST API](/api/rest_api/#_1)，`Authorization` 放在请求头中；[FORM API](/api/form_api/#_1)，`authorization` 放在请求体中。
- 请求签名有效期：REST API，有效期为 30 分钟；FORM API，自定义签名有效期。如果签名超过有效期，需要重新生成签名。
- 回调签名：`Authorization` 放在请求头中，`Content-MD5` 表示回调请求体的 MD5 值；`Policy` 为空。
- 回调签名有效期：由用户自行确定，建议设置为 30 分钟。

<a name="policy"></a>
** FORM API - Policy 算法 **

生成步骤：

1. 将请求参数键值对转换为 JSON 字符串；
2. 将第 1 步所得到的字符串进行 Base64 Encode 处理，得到 policy。

** 注 **

- 参数和参数值必须是 UTF-8 编码，且不包含换行符。


** 举例 **

例 1，[REST API 上传文件](/api/rest_api/#_2)请求签名

```
// 操作员信息
Operator = upyun			
Password = MD5(upyun520) = ab296a01090ca2eab5fe5b246999da54

// 请求头
Method = PUT							
URI = /upyun-temp/demo.jpg
Date = Wed, 9 Nov 2016 14:26:58 GMT
// 上传文件 MD5 值
Content-MD5 = 7ac66c0f148de9519b8bd264312c4d64

// Policy 为空，不参与签名计算
Policy = ''   
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
// 内容拼接时，不用换行或空格，上面格式的换行或空格是为了方便阅读
= Base64 (HMAC-SHA1 (ab296a01090ca2eab5fe5b246999da54,PUT&/upyun-temp/demo.jpg&Wed, 9 Nov 2016 14:26:58 GMT&7ac66c0f148de9519b8bd264312c4d64))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= 9xZ6Z8dZXJm7cy3Jt6kskVS7cic=
```

完整签名：

```
Authorization: UPYUN upyun:9xZ6Z8dZXJm7cy3Jt6kskVS7cic=
```

例 2，[FORM API 上传文件](/api/form_api/#_2)请求签名

```
// 操作员信息
Operator = upyun			
Password = MD5(upyun520) = ab296a01090ca2eab5fe5b246999da54

// 请求头
Method = POST							
URI = /upyun-temp/

// 请求体中的请求参数  
bucket = upyun-temp
save-key = /demo.jpg
expiration = 1478674618
date = Wed, 9 Nov 2016 14:26:58 GMT
content-md5 = 7ac66c0f148de9519b8bd264312c4d64

// 请求参数键值对转换为 JSON 字符串，计算 Policy
Policy = Base64 ({"bucket": "upyun-temp", "save-key": "/demo.jpg", "expiration": "1478674618", "date": "Wed, 9 Nov 2016 14:26:58 GMT", "content-md5": "7ac66c0f148de9519b8bd264312c4d64"})
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
// 内容拼接时，不用换行或空格，上面格式的换行或空格是为了方便阅读
= Base64 (HMAC-SHA1 (ab296a01090ca2eab5fe5b246999da54,POST&/upyun-temp/&Wed, 9 Nov 2016 14:26:58 GMT&eyJidWNrZXQiOiAidXB5dW4tdGVtcCIsICJzYXZlLWtleSI6ICIvZGVtby5qcGciLCAiZXhwaXJhdGlvbiI6ICIxNDc4Njc0NjE4IiwgImRhdGUiOiAiV2VkLCA5IE5vdiAyMDE2IDE0OjI2OjU4IEdNVCIsICJjb250ZW50LW1kNSI6ICI3YWM2NmMwZjE0OGRlOTUxOWI4YmQyNjQzMTJjNGQ2NCJ9&7ac66c0f148de9519b8bd264312c4d64))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= NnMeoZosYLnOGUiY/Skb0W1DMNA=
```

完整签名：

```
authorization: UPYUN upyun:NnMeoZosYLnOGUiY/Skb0W1DMNA=
```

例 3，[FORM API 回调通知](/api/form_api/#notify_return)请求签名

```
// 操作员信息
Operator = upyun			
Password = MD5(upyun520) = ab296a01090ca2eab5fe5b246999da54

// 请求头
Method = POST							
URI = /upyun_notify_url
Date = Wed, 9 Nov 2016 14:26:58 GMT
// 请求体的 MD5 值
Content-MD5 = MD5({"code": 200, "message": "ok", "url": "%2F2011%2F12%2Ffd0e30047f81fa95.mp3", "time": 1478701618})
	        = ed091459198a814d549701dab1dc4880

// 请求体
code = 200
message = ok
url = %2F2011%2F12%2Ffd0e30047f81fa95.mp3
time = 1478701618

// Policy 为空，不参与签名计算
Policy = ''
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
// 内容拼接时，不用换行或空格，上面格式的换行或空格是为了方便阅读
= Base64 (HMAC-SHA1 (ab296a01090ca2eab5fe5b246999da54,POST&/upyun_notify_url&Wed, 9 Nov 2016 14:26:58 GMT&ed091459198a814d549701dab1dc4880))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= FlK2YFEhcAbbKNmcAoVLL6drqIs=
```

完整签名：

```
Authorization: UPYUN upyun:FlK2YFEhcAbbKNmcAoVLL6drqIs=
```

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)