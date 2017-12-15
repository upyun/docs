## 快速入门

本文档提供了常用语言认证鉴权的使用例子，请确保您已经注册又拍云账号并完成实名验证。

---------

## 注意事项

1. 本文档适用于云存储，云处理，内容识别，容器云服务。
2. 使用 `form` `API` 上传时，需要用户生成 `policy`，本文档不提供生成 `policy` 的使用实例。
3. 使用时，根据自己的需求，配置，修改对应参数: `key`，`secret`，`uri`，`method`，`policy`，`md5`。
4. 本文档使用的 `key`，`secret` 在不同服务中的含义不一样，云存储，云处理，内容识别(有存储) 表示操作员和密钥的 `MD5` 值，
内容识别(无存储)，容器云表示各自服务提供的 `client_key`，`client_secret`。
5. 根据又拍云文档说明，把认证信息，填入相应的 `http` 请求信息中。
6. 认证鉴权的详细信息请查看又拍云文档

## 常用语言代码示例

#### go 代码演示

```go
package main

import (
	"crypto/hmac"
	"crypto/sha1"
	"encoding/base64"
	"fmt"
	"strings"
	"time"
)

var (
	key    = "upyun"
	secret = "secret"
	uri    = "/v1/apps/"
	method = "GET"
)

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
    date = httpdate_rfc1123()
    # date = 'Thu, 14 Dec 2017 06:03:27 GMT'

    # Tip 3
    # sign 函数返回的值填入 http 头 Authorization 信息中
    print date
    print sign(key, secret, method, uri, date)


if __name__ == '__main__':
    main()
```

#### js 代码演示

```js
var hmacsha1 = require('hmacsha1')

function sign(key, secret, method, uri, date, policy=null, md5=null) {
    elems = [];
    [method, uri, date, policy, md5].forEach(item => {
        if (item != null) {
            elems.push(item)
        }
    })
    value = elems.join('&');
    sign = hmacsha1(secret, value);
    return `UPYUN ${key}:${sign}`;
}

date = new Date().toGMTString();
console.log(date);
console.log(sign('upyun', 'secret', 'GET', '/v1/apps/', date));
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

public class auth {
    private static final String HMAC_SHA1_ALGORITHM = "HmacSHA1";

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
        if (policy != "") {
            value = value + "&" + policy;
        }

        if (md5 != "") {
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
        String date = getRfc1123Time();
        String hmac = sign("upyun", "secret", "GET", "/v1/apps/", date, "", "");
        System.out.println(date);
        System.out.println(hmac);
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

    echo $date . "\n";
    echo sign($key, $secret, $method, $uri, $date);
}

main();
```