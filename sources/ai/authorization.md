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

## 代码演示

### 注意事项

1. 本示例适用于云存储，云处理，内容识别，容器云服务。
2. 使用时，根据自己的需求、配置，修改对应参数: `key`，`secret`，`uri`，`method`，`policy`，`md5`。
3. 本示例使用的 `key`，`secret` 在不同服务中的含义不一样，云存储、云处理、内容识别(有存储) 中表示 `Operator` 和 `Password`，
内容识别(无存储)、容器云表示各自服务提供的 `ClientKey` 和 `ClientSecret`。
4. 根据对应 API 文档说明，把认证信息，填入相应的 `http` 请求信息中。
5. 使用 `form API` 上传时，需要用户生成 `policy`，本示例不提供生成 `policy` 的使用实例。

### 常用语言代码示例

#### go 代码演示

```go
package main

import (
	"crypto/hmac"
	"crypto/md5"
	"crypto/sha1"
	"encoding/base64"
	"fmt"
	"strings"
	"time"
)

var (
	key    = "upyun"
	secret = "secret" // 对于上传，处理，跟内容识别需要MD5加密
	uri    = "/v1/apps/"
	method = "GET"
)

func md5Str(s string) string {
	return fmt.Sprintf("%x", md5.Sum([]byte(s)))
}

func makeRFC1123Date(d time.Time) string {
	utc := d.UTC().Format(time.RFC1123)
	return strings.Replace(utc, "UTC", "GMT", -1)
}
func base64ToStr(b []byte) string {
	return base64.StdEncoding.EncodeToString(b)
}

func sign(key, secret, method, uri, date, policy, md5 string) string {
	mac := hmac.New(sha1.New, []byte(secret))
	elems := []string{}
	for _, v := range []string{method, uri, date, policy, md5} {
		if v != "" {
			elems = append(elems, v)
		}
	}
	value := strings.Join(elems, "&")
	fmt.Println(value)
	mac.Write([]byte(value))
	signStr := base64ToStr(mac.Sum(nil))
	return "UPYUN " + key + ":" + signStr
}

func main() {
	date := makeRFC1123Date(time.Now())
	fmt.Println(date)
	fmt.Println(md5Str(secret))

	// 上传，处理，内容识别有存储
	fmt.Println(sign(key, md5Str(secret), method, uri, date, "", ""))

	// 容器云，内容识别无存储
	fmt.Println(sign(key, secret, method, uri, date, "", ""))
}

```

#### python 代码演示

```py
# -*- coding: UTF-8 -*-
import base64
import datetime
import hashlib
import hmac

# Tip 1
# 使用时需要根据自己配置与需求提供参数 key secret uri method
key = 'upyun'
secret = 'secret'
uri = '/v1/apps/'
method = 'GET'


def httpdate_rfc1123(dt=None):
    dt = dt or datetime.datetime.utcnow()
    return dt.strftime('%a, %d %b %Y %H:%M:%S GMT')

def md5(value):
    return hashlib.md5(value.encode()).hexdigest()

def sign(client_key, client_secret, method, uri, date, policy=None, md5=None):
    # Tip 4
    # MD5 信息可选
    signarr = []
    for v in [method, uri, date, policy, md5]:
        if v != None:
            signarr.append(v)
    signstr = '&'.join(signarr)
    signstr = base64.b64encode(
        hmac.new(client_secret.encode(), signstr.encode(),
                 digestmod=hashlib.sha1).digest()
    ).decode()
    return 'UPYUN %s:%s' % (client_key, signstr)


def main():
    # Tip 2
    # 时间需要与 http 头 Date 信息保持一致
    # date = httpdate_rfc1123()
    date = 'Thu, 14 Dec 2017 06:03:27 GMT'

    # Tip 3
    # sign 函数返回的值填入 http 头 Authorization 信息中
    print date

    print md5(secret)
    
    # 上传，处理，内容识别有存储
    print sign(key, md5(secret), method, uri, date)

    # 内容识别无存储，容器云
    print sign(key, secret, method, uri, date)

if __name__ == '__main__':
    main()
```

#### js 代码演示

```js
var crypto = require('crypto');

function sign(key, secret, method, uri, date, policy=null, md5=null) {
    elems = [];
    [method, uri, date, policy, md5].forEach(item => {
        if (item != null) {
            elems.push(item)
        }
    })
    value = elems.join('&');
    auth = hmacsha1(secret, value);
    return `UPYUN ${key}:${auth}`;
}

function MD5(value) {
    return crypto.createHash('md5').update(value).digest('hex');
}

function hmacsha1(secret, value) {
    return crypto.createHmac('sha1', secret).update(value, 'utf-8').digest().toString('base64');
}

date = new Date().toGMTString();
console.log(date);

key = 'upyun';
secret = 'secret';
method = 'GET';
uri = '/v1/apps/';

console.log(MD5('secret'));

// 上传，处理，内容识别有存储
console.log(sign(key, MD5(secret), method, uri, date));

// 内容识别无存储，容器云
console.log(sign(key, secret, method, uri, date));
```

#### java 代码演示

```java
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.security.SignatureException;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;

import java.util.Base64;
import java.util.Calendar;
import java.text.SimpleDateFormat;
import java.util.Locale;
import java.util.TimeZone;

import java.security.*;
import java.io.*;

public class auth {
    private static final String HMAC_SHA1_ALGORITHM = "HmacSHA1";

    public static String md5(String string) {
        byte[] hash;
        try {
            hash = MessageDigest.getInstance("MD5").digest(string.getBytes("UTF-8"));
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException("UTF-8 is unsupported", e);
        } catch (NoSuchAlgorithmException e) {
            throw new RuntimeException("MessageDigest不支持MD5Util", e);
        }
        StringBuilder hex = new StringBuilder(hash.length * 2);
        for (byte b : hash) {
            if ((b & 0xFF) < 0x10) hex.append("0");
            hex.append(Integer.toHexString(b & 0xFF));
        }
        return hex.toString();
    }

    public static byte[] hashHmac(String data, String key)
            throws SignatureException, NoSuchAlgorithmException, InvalidKeyException {
        SecretKeySpec signingKey = new SecretKeySpec(key.getBytes(), HMAC_SHA1_ALGORITHM);
        Mac mac = Mac.getInstance(HMAC_SHA1_ALGORITHM);
        mac.init(signingKey);
        return mac.doFinal(data.getBytes());
    }

    public static String sign(String key, String secret, String method, String uri, String date, String policy,
            String md5) throws Exception {
        String value = method + "&" + uri + "&" + date;
        if (policy != null && policy.length() > 0) {
            value = value + "&" + policy;
        }

        if (md5 != null && md5.length() > 0) {
            value = value + "&" + md5;
        }
        byte[] hmac = hashHmac(value, secret);
        String sign = Base64.getEncoder().encodeToString(hmac);
        return "UPYUN " + key + ":" + sign;
    }


    public static String getRfc1123Time() {
        Calendar calendar = Calendar.getInstance();
        SimpleDateFormat dateFormat = new SimpleDateFormat(
            "EEE, dd MMM yyyy HH:mm:ss z", Locale.US);
        dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
        return dateFormat.format(calendar.getTime());
    }

    public static void main(String[] args) throws Exception {
        String key = "upyun";
        String secret = "secret";
        String method = "GET";
        String uri = "/v1/apps/";

        String date = getRfc1123Time();
    
        System.out.println(date);

        System.out.println(md5(secret));

        // 上传，处理，内容识别有存储
        System.out.println(sign(key, md5(secret), method, uri, date, "", ""));
        
        // 内容识别无存储，容器云
        System.out.println(sign(key, secret, method, uri, date, "", ""));
    }
}
```

#### php 代码演示

```php
<?php

function sign($key, $secret, $method, $uri, $date, $policy=null, $md5=null)
{
    $elems = array();
    foreach (array($method, $uri, $date, $policy, $md5) as $v)
    {
        if ($v)
        {
            $elems[] = $v;
        }
    }
    $value = implode('&', $elems);
    $sign = base64_encode(hash_hmac('sha1', $value, $secret, true));
    return 'UPYUN ' . $key . ':' . $sign;
}

function main()
{
    $key = 'upyun';
    $secret = 'secret';
    $uri = '/v1/apps/';
    $method = 'GET';
    $date = gmdate('D, d M Y H:i:s \G\M\T');

    $md = md5($secret);

    echo $md . "\n";

    echo $date . "\n";

    // 上传，处理，内容识别有存储
    echo sign($key, md5($secret), $method, $uri, $date) . "\n";

    // 内容识别无存储，容器云
    echo sign($key, $secret, $method, $uri, $date);
}

main();
```

---------

如有疑问请 [联系我们](https://www.upyun.com/contact)