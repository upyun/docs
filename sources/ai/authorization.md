使用 API 时，需要对请求进行签名。系统发给用户的请求，也会进行签名，供用户验证请求来源。

## 签名认证

** 签名语法 **

```
Authorization: UPYUN <ClientKey>:<Signature>

<Signature> = Base64 (HMAC-SHA1 (<ClientSecret>,
<Method>&
<URI>&
<Date>&
<Content-MD5>
))
```

** 参数说明 **

| 参数      		| 必选  	| 说明                                      	|
|---------------|-------|-----------------------------------------------------------------|
| ClientKey     | 是    	| 用户密钥 KEY                            						|
| Method        | 是    	| 请求方式，如：GET、POST、PUT、HEAD 等                            	|
| URI           | 是    	| 请求路径	 |
| Date          | 是    	| 请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 `Wed, 29 Oct 2014 02:26:58 GMT`|
| Content-MD5   | 否    	| 请求体的 MD5 校验值，如果请求体为空，可以为空            |
| ClientSecret  | 是    	| 用户密钥 Secret                                   	|

** 注 **

- 非必选参数为空时，连同前面的 `&` 一起不参与签名计算。所有计算的 MD5 值，格式均是 32 位小写。
- HMAC-SHA1 输出的必须是**原生的二进制数据**。
- 请求签名有效期为 30 分钟，如果签名超过有效期，需要重新生成签名。
- 回调签名有效期由用户自行确定，建议设置为 30 分钟。

## 举例 

** 参数信息 **

```
// 操作员信息
ClientKey = TSzF4Cd9JPt6Qcm3WqfDiuUpoAH1			
ClientSecret = KuGnZUD17aN9oyRkjSixBqlwQcH

// 参数
Method = POST							
URI = /image/url/check
Date = Thu, 12 Oct 2017 06:57:50 GMT
Body = {'url': 'http://uprocess.b0.upaiyun.com/demo.jpg'}
Content-MD5 = MD5(Body)
            = DD0F8A735A45323A32EE4D6154E9985B
```

** 生成 Signature **

```
Signature = Base64 (HMAC-SHA1 (<ClientSecret>,
<Method>&
<URI>&
<Date>&
<Content-MD5>
))
// 不用换行或空格，上面格式的换行或空格是为了方便阅读
= Base64 (HMAC-SHA1 (ClientSecret, Method&URI&Date&Content-MD5))
// HMAC-SHA1 返回的原生二进制数据进行 Base64 编码
= CKhrW8SSU0ctnmavfnRs1s1NFBY=
```

** 签名 **

```
Authorization: UPYUN TSzF4Cd9JPt6Qcm3WqfDiuUpoAH1:CKhrW8SSU0ctnmavfnRs1s1NFBY=
```

** 完整请求 **

```
POST /image/url/check HTTP/1.1
Host: banma.api.upyun.com
Date: Thu, 12 Oct 2017 06:57:50 GMT
Content-MD5: DD0F8A735A45323A32EE4D6154E9985B
Authorization: UPYUN TSzF4Cd9JPt6Qcm3WqfDiuUpoAH1:CKhrW8SSU0ctnmavfnRs1s1NFBY=
Content-Length: 50
Content-Type: application/json

{"url": "http://uprocess.b0.upaiyun.com/demo.jpg"}
```

---------

如有疑问请 [联系我们](https://www.upyun.com/contact)