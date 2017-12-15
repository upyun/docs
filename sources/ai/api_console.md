## 快速入门

内容识别处理接口是用户调整内容(图片，视频)的识别分类，提交给又拍云处理(确认屏蔽，彻底删除，取消屏蔽)内容，内容必须是保存在又拍云存储。请确保您已经注册又拍云账号并完成实名验证，请确保您已经[开通内容识别服务](https://console.upyun.com/toolbox/audit/)。

---------

## 内容处理

提交任务到又拍云进行内容相关处理，包括确认屏蔽，彻底删除，取消屏蔽。

### 请求

**请求语法**

```
POST /console/identify?act=<action> HTTP/1.1
Host: banma.api.upyun.com
Date: GMT Date
Authorization: UPYUN <ClientKey>:<Signature>
Content-Type: application/json

{"service":"<服务名>", "tasks":[{"kind":<文件类型>, "path":"<文件路径>"}]}
```

** 签名认证 **

`Authorization` 详见[签名认证](/ai/authorization/#_1)。

`代码实现` 详见[代码示例](/api/auth_examples/)。

**请求参数**

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| action       		| string       	| 是   	| 请求的处理任务，`shield` 表示确认屏蔽，`cancel_shield` 表示取消屏蔽，`delete` 表示彻底删除  |
| service       	| string       	| 是   	| 用户服务名  |
| tasks       		| list       	| 是   	| 一组任务列表，详见 [tasks 参数说明](#tasks) |
| kind       		| integer       | 是   	| 内容的类型，`1` 表示图片，`2` 表示视频 |
| path       		| string       	| 是   	| 内容的路径 |

<a name="tasks"></a>
** tasks 参数说明 **

按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。

```
[
	{
        "kind": 1,     // 提交的文件类型，1 表示图片 2 表示视频
        "path": "/upyun.jpg"  // 提交的文件路径  
	},
	…
]
```

### 响应

响应参数是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理 |
| error	         	| string   	| 错误信息，空字符串表示无错误信息 |

### 举例

**请求示例**

```
POST /console/identify?act=shield HTTP/1.1
Host: banma.api.upyun.com 
Date: Thu, 12 Oct 2017 07:32:34 GMT
Content-Type: application/json
Authorization: UPYUN 2GO7K6NCPInWZqvJewohbDH5jmtaBY0x:mW7gtK0PRvhR0Mzn/x6RSAbh3cg=
Content-Length: 61

{"service":"upyun", "tasks":[{"kind":1, "path":"/upyun.jpg"}]}
```

**响应示例**

```
HTTP/1.1 200 OK
Server: marco/1.8
Content-Type: application/json
Content-Length: 31
Date: Thu, 12 Oct 2017 07:32:29 GMT

{"status_code":200,"error":""}
```

## 获取内容屏蔽信息

获取指定内容是否被屏蔽及屏蔽密钥

### 请求

**请求语法**

```
GET /console/shield_info?service=<service_name>&path=<path> HTTP/1.1
Host: banma.api.upyun.com
Date: GMT Date
Authorization: UPYUN <ClientKey>:<Signature>
```

** 签名认证 **

`Authorization` 详见[签名认证](/ai/authorization/#_1)。

`代码实现` 详见[代码示例](/api/auth_examples/)。

**请求参数**

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service           | string       	| 是   	| 用户服务名  |
| path       		| string       	| 是   	| 内容路径  |

### 响应

响应参数是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|----------------|-----------|-----------------------------------------------------------|
| status_code    | integer   | 处理结果状态码，`200` 表示成功处理 |
| error	         | string    | 错误信息，空字符串表示无错误信息 |
| shielded       | boolean   | 文件是否被屏蔽
| secret         | string    | 屏蔽文件的 `secret` 内容，没有屏蔽返回空字符串

### 举例

**请求示例**

```
GET /console/shield_info?service=upyun&path=/upyun.jpg HTTP/1.1
Host: banma.api.upyun.com 
Date: Thu, 12 Oct 2017 07:32:34 GMT
Authorization: UPYUN 2GO7K6NCPInWZqvJewohbDH5jmtaBY0x:mW7gtK0PRvhR0Mzn/x6RSAbh3cg=
```

**响应示例**

```
HTTP/1.1 200 OK
Server: marco/1.8
Content-Type: application/json
Content-Length: 57
Date: Thu, 12 Oct 2017 07:32:29 GMT

{"status_code":200,"error":"","shielded":false,secret:""}
```

# 示例代码

使用时，`client_key` 和 `client_secret` 替换为内容识别控制台上的密钥

请求信息根据请求参数要求填写

```py
# python
# -*- coding: UTF-8 -*-
import base64
import datetime
import hashlib
import hmac

import requests

def b64(s):
    return base64.b64encode(s.encode()).decode()

# 时间格式化
def httpdate_rfc1123(dt=None):
    dt = dt or datetime.datetime.utcnow()
    return datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')


# 签名
def sign(client_key, client_secret, method, uri, date):
    signarr = [method, uri, date]
    signstr = '&'.join(signarr)
    signstr = base64.b64encode(
        hmac.new(client_secret.encode(), signstr.encode(), digestmod=hashlib.sha1).digest()
    ).decode()
    return 'UPYUN %s:%s' % (client_key, signstr)

# 密钥
client_key = 'GO7K6NCPInWZqvJewohbDH5jmt'
client_secret = 'KQrAn5OjgG3HVlkJo78DbyiuL'

# 内容屏蔽
def shield():
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'POST', '/console/identify?act=shield', headers['Date'])
    data = {
        "service":"upyun",
        "tasks": [{
        'kind':1,
        'path': '/test.jpg',
    }]}
    resp = requests.post('http://banma.api.upyun.com/console/identify?act=shield', headers=headers, json=data)
    print(resp.status_code, resp.text)

# 内容取消屏蔽
def cancel_shield():
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'POST', '/console/identify?act=cancel_shield', headers['Date'])
    data = {
        "service":"upyun",
        "tasks": [{
        'kind':1,
        'path': '/test.jpg',
    }]}
    resp = requests.post('http://banma.api.upyun.com/console/identify?act=cancel_shield', headers=headers, json=data)
    print(resp.status_code, resp.text)

# 内容删除
def delete():
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'POST', '/console/identify?act=delete', headers['Date'])
    data = {
        "service":"upyun",
        "tasks": [{
        'kind':1,
        'path': '/test.jpg',
    }]}
    resp = requests.post('http://banma.api.upyun.com/console/identify?act=delete', headers=headers, json=data)
    print(resp.status_code, resp.text)

# 获取内容屏蔽信息
def get_info():
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'GET', '/console/shield_info?service=upyun&path=/test.jpg', headers['Date'])

    resp = requests.get('http://banma.api.upyun.com/console/shield_info?service=upyun&path=/test.jpg', headers=headers)
    print(resp.status_code, resp.text)


def main():
    shield()
    cancel_shield()
    delete()
    get_info()

if __name__ == '__main__':
    main()
```



