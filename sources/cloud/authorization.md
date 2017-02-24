## 签名认证

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

| 参数      		| 必选  	| 说明                                      	|
|---------------|-------|-----------------------------------------------------------------|
| Operator      | 是    	|服务的操作员名称                            						|
| Method        | 是    	|请求方式，如：GET、POST、PUT、HEAD 等                            	|
| URI           | 是    	|请求路径	 |
| Date          | 是    	|请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT`|
| Content-MD5   | 否    	|请求体的 MD5 校验值，如果请求体为空，可以为空            |
| Password      | 是    	|服务的操作员密码的 MD5 值                                   	|

** 注 **

- 非必选参数为空时，连同前面的 `&` 一起不参与签名计算。所有计算的 MD5 值，格式均是 32 位小写。
- HMAC-SHA1 输出的必须是原生的二进制数据。
- 请求签名有效期为 30 分钟，如果签名超过有效期，需要重新生成签名。
- 回调签名有效期由用户自行确定，建议设置为 30 分钟。

** 举例 **

参数信息：
```
// 操作员信息
Operator = operator123			
Password = MD5(password123) = 482c811da5d5b4bc6d497ffa98491e38

// 参数
Method = POST							
URI = /pretreatment/
Date = Wed, 09 Nov 2016 14:26:58 GMT
Content-MD5 = MD5(service=upyun-temp&notify_url=/upyun_notify_url&source=/tmp.mp4&tasks=W3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ==&accept=json)
            = a2d75510f7ec654cc24cfa2b5a5a8182
```

生成 Signature：

```
Signature = Base64 (HMAC-SHA1 (<Password>,
<Method>&
<URI>&
<Date>&
<Content-MD5>
))
// 内容拼接时，不用换行或空格，上面格式的换行或空格是为了方便阅读
= Base64 (HMAC-SHA1 (ab296a01090ca2eab5fe5b246999da54,POST&/pretreatment/&Wed, 09 Nov 2016 14:26:58 GMT&a2d75510f7ec654cc24cfa2b5a5a8182))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= lSPhJS7LVUkrCMUq3PBZSvhsnqo=
```

Authorization 签名：

```
Authorization: UPYUN upyun:e9QV8W8yBDDGyknkwTesxn94jN0=
```

完整的请求：

```
POST /pretreatment/ HTTP/1.1
Authorization: UPYUN upyun:lSPhJS7LVUkrCMUq3PBZSvhsnqo=
Content-MD5: a2d75510f7ec654cc24cfa2b5a5a8182
Date: Wed, 9 Nov 2016 14:26:58 GMT
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: p0.api.upyun.com
Content-Length: 668

service=upyun-temp&notify_url=/upyun_notify_url&source=/tmp.mp4&tasks=W3siYXZvcHRzIjoiL3MvMjQwcCg0OjMpL2FzLzEvci8zMCIsInJldHVybl9pbmZvIjp0cnVlLCJzYXZlX2FzIjoiL2EvYi5tcDQiLCJ0eXBlIjoidmlkZW8ifSx7ImF2b3B0cyI6Ii9pL0wyRXZZaTlqTG0xd05BPT0vaS9MekV2TWk4ekxtMXdOQT09Iiwic2F2ZV9hcyI6Ii9jb25jYXQvYS5tcDQiLCJ0eXBlIjoidmNvbmNhdCJ9XQ==&accept=json
```

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)