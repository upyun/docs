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


## 签名认证

为了避免基本认证中 Base64 编码可逆带来的安全隐患，又拍云云存储支持的更为安全的认证方式。API 使用这种方式进行认证鉴权。

**签名格式**

```
Authorization: UPYUN <Operator>:<Signature>
Date: <格林尼治标准时间>
Content-MD5: <Content-MD5>

<Signature> = Base64 (HMAC-SHA256 (
<Method>&
<URI>&
<Date>&
<Policy>&
<Content-MD5>&
<Password>
))
```

相关参数说明：

|      参数      |  必选   |                       说明                                           |
|---------------|---------|------------------------------------------------------------------------------|
| Operator      |   是    |服务的操作员名称                            |
| Method        |   是    |请求方式，如：GET、POST、PUT、HEAD 等                            |
| URI           |   是    |请求路径，需 URL 编码处理 ([RFC 1738](http://tools.ietf.org/html/rfc1738 )) |
| Date          | 	否    |请求日期时间，GMT 格式字符串 ([RFC 1123](http://tools.ietf.org/html/rfc1123))，如 *`Wed, 29 Oct 2014 02:26:58 GMT`*          |
| Password      |   是    |服务的操作员的密码                                                    |
| Policy        |   否    |请求体中的请求参数的 Base64 编码，如果没有参数，可以为空。详见 [Policy 算法](#policy)   |
| Content-MD5   |   否    |上传文件的 MD5，如果请求中没有文件或文件太大计算 MD5 不方便，可以为空            |

**注**

- 签名的有效期为 30 分钟，如果超过 30 分钟，则需重新生成签名。
- 请求签名：REST API，`Authorization` 放在请求头中，详见[请求方法](/api/rest_api/#_1)；FORM API，`Authorization` 放在请求内容中，详见[请求方法](/api/form_api/#_1)。
- 回调签名：`Authorization` 放在请求头中。


<a name="policy"></a>
**Policy 算法**

Policy 生成步骤：

1. 将请求体中的请求参数键值对转换为 JSON 字符串；
2. 将第 1 步的字符串进行 Base64 Encode 处理，得到 policy。

**注**

- policy 必须是 UTF-8 编码，且不包含换行符。


**举例**

参数信息：
```
// 操作员信息
Operator = upyun			
Password = UpYun520

// 请求信息
Method = POST							
URI = /upyun-temp
Date = ''

// 请求内容信息
File-MD5 = ab296a01090ca2eab5fe5b246999da54   
bucket = upyun-temp
save-key = /demo.jpg
```

生成 Signature：

```
Policy = Base64 ({"bucket":"upyun-temp","save-key":"/img.jpg"})
	   = eyJidWNrZXQiOiJkZW1vYnVja2V0Iiwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9

Signature = Base64 (HMAC-SHA256 (
<Method>&
<URI>&
<Date>&
<Policy>&
<Content-MD5>&
<Password>
))
// 内容拼接时，不用换行或空格，上面格式的换行或空格是为了方便阅读
= Base64 (HMAC-SHA256 (GET&/upyun-temp&&eyJidWNrZXQiOiJkZW1vYnVja2V0Iiwic2F2ZS1rZXkiOiIvaW1nLmpwZyJ9&ab296a01090ca2eab5fe5b246999da54&UpYun520))
// HMAC-SHA256 返回的原始二进制数据进行 Base64 编码
= uO3SCqumhPvXMijy9gOGqZ92+YiMdjBEDphvGQVOEeg=
```

完整签名：

```
Authorization: UPYUN upyun:uO3SCqumhPvXMijy9gOGqZ92+YiMdjBEDphvGQVOEeg=
```