## 快速入门

内容识别(无存储)是直接把内容提交给平台，提交方式可以是内容本身或 URL，平台处理完成后返回结果，不保留数据。您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经[开通内容识别服务](https://console.upyun.com/toolbox/audit/)。

服务需要收费，可以免费试用，详情敬请[联系商务](https://www.upyun.com/products/audit#section-pricing)。

---------

## 图片识别(图片内容)

上传图片内容，进行识别。`act=porn` 表示色情识别。

### 请求

**请求语法**

```
POST /image/bin/check?act=porn HTTP/1.1
Host: banma.api.upyun.com
Date: GMT Date
Authorization: UPYUN <ClientKey>:<Signature>
Content-Type: <图片内容类型，必填>

[Image Content...]
```

** 签名认证 **

`Authorization` 详见[签名认证](/ai/authorization/#_1)。

`代码实现` 详见[代码示例](/api/authorization/#_3)。

### 响应

响应参数是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理 |
| error	         	| string   	| 错误信息，空字符串表示无错误信息 |
| porn 				| json  	| 色情识别结果												|
| porn.scores	   	| list    	| 各个分类及概率, 从左至右依次是 `正常概率`、`色情概率`、`性感概率` |
| porn.label   		| integer   | 推荐分类，可能值 `0`、`1`、`2`，`0` 表示正常，`1` 表示色情，`2` 表示性感  |
| porn.rate       	| float    	| 推荐分类的概率                                            		|
| porn.review 		| bool  	| 是否需要人工审核									|


### 举例

**请求示例**

```
POST /image/bin/check HTTP/1.1
Host: banma.api.upyun.com 
Date: Thu, 12 Oct 2017 07:32:34 GMT
Content-Type: image/jpeg
Authorization: UPYUN 2GO7K6NCPInWZqvJewohbDH5jmtaBY0x:mW7gtK0PRvhR0Mzn/x6RSAbh3cg=
Content-Length: 447330

[Image Content...]
```

**响应示例**

```
HTTP/1.1 200 OK
Server: marco/1.8
Content-Type: application/json
Content-Length: 163
Date: Thu, 12 Oct 2017 07:32:29 GMT

{"status_code":200,"error":"","porn":{"scores":[0.999045193195343,0.00015256137703545392,0.0008022452238947153],"rate":0.999045193195343,"label":0,"review":false}}
```

## 图片识别(图片 URL)

提交图片 URL，进行识别。`act=porn` 表示色情识别。

### 请求

**请求语法**

```
POST /image/url/check?act=porn HTTP/1.1
Host: banma.api.upyun.com
Date: GMT Date
Authorization: UPYUN <ClientKey>:<Signature>
Content-Type: application/json

{"url": "<图片的绝对路径>"}
```

** 签名认证 **

`Authorization` 详见[签名认证](/ai/authorization/#_1)。

`代码实现` 详见[代码示例](/api/authorization/#_3)。

**请求参数**

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| url       		| string       	| 是   	| 图片的 URL        			|

### 响应

响应参数是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| status_code       | integer   | 处理结果状态码，`200` 表示成功处理 |
| error          	| string   	| 错误信息，空字符串表示无错误信息 |
| porn 				| json  	| 色情识别结果												|
| porn.scores	   	| list    	| 各个分类及概率, 从左至右依次是 `正常概率`、`色情概率`、`性感概率` |
| porn.label   		| integer   | 推荐分类，可能值 `0`、`1`、`2`，`0` 表示正常，`1` 表示色情，`2` 表示性感  |
| porn.rate       	| float    	| 推荐分类的概率                                            		|
| porn.review 		| bool  	| 是否需要人工审核									|

### 举例

**请求示例**

```
POST /image/url/check HTTP/1.1
Host: banma.api.upyun.com
Date: Thu, 12 Oct 2017 06:57:50 GMT
Authorization: UPYUN 2GO7K6NCPInWZqvJewohbDH5jmtaBY0x:CKhrW8SSU0ctnmavfnRs1s1NFBY=
Content-Length: 50
Content-Type: application/json

{"url": "http://uprocess.b0.upaiyun.com/demo.jpg"}
```

**响应示例**

```
HTTP/1.1 200 OK
Server: marco/1.8
Content-Type: application/json
Content-Length: 164
Date: Thu, 12 Oct 2017 06:57:45 GMT

{"status_code":200,"error":"","porn":{"scores":[0.9996986389160156,0.0001312318054260686,0.0001701978617347777],"rate":0.9996986389160156,"label":0,"review":false}}
```


## 点播识别(点播 URL)

提交视频点播 URL，进行识别。识别是异步进行的，收到请求后，会先返回任务的 ID，待结果出来后，回调返回识别结果。`act=porn` 表示色情识别。

### 请求

**请求语法**

```
POST /video/check?act=porn HTTP/1.1
Host: banma.api.upyun.com
Date: GMT Date
Authorization: UPYUN <ClientKey>:<Signature>
Content-Type: application/json

{"url": "<视频点播的绝对路径>", "notify_url": "<回调地址>"}
```

** 签名认证 **

`Authorization` 详见[签名认证](/ai/authorization/#_1)。

`代码实现` 详见[代码示例](/api/authorization/#_3)。

**请求参数**

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| url       		| string       	| 是   	| 点播的 URL        				|
| notify_url       	| string       	| 是   	| 回调地址, 识别结果将以 `POST` 请求发往该地址 |
| interval       	| integer       | 否   	| 截图间隔, 默认每 10s 从点播中取一帧    |


### 响应

响应参数是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| task_id 			| string  	| 任务 ID												|


### 回调

任务处理完成后，向 `notify_url` 发送 `HTTP POST` 请求，请求体是回调信息。

```
curl -X POST \
    <notify_url> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
	# 其他参数...
```

** 回调参数 **

回调参数为 JSON 字符串，参数名及说明如下：

| 参数       		              | 类型   	| 说明
|-------------------------------|-----------|-----------------------------------------------------------
| task_id                    	| string    | 任务对应的 `task_id`	|
| status_code   	            | integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)	|
| service	   		            | string    | 视频点播所在的服务名	|
| source        	            | string    | 截图保存路径，如果请求参数中未设置 `save_as`，不存在	|
| error       		            | string   	| 错误信息，空字符串表示无错误信息	|
| result                        | json      | 识别结果集，包含 `porn`	|
| result.porn                   | json      | 鉴黄结果，包含 `label`、`rate`、`review`	|
| result.porn.label 	        | integer  	| 图片被判定的分类，可能值 `0`、`1`、`2`，`0` 表示正常，`1` 表示色情，`2` 表示性感	|
| result.porn.rate              | float   	| 图片被判定为某个分类的概率，介于 `[0-1]` 之间	|
| result.porn.review            | boolean  	| 是否需要人工复审，`true` 表示需要，`false` 表示不需要	|
| filelist        	            | json    	| 截图集, 为每个时间点的截图审核结果	|
| filelist.result	            | json  	| 识别结果集，包含 `porn`	|
| filelist.result.shot_time     | string    | 截图时间点	|
| filelist.result.porn 	        | json  	| 鉴黄结果，包含 `label`、`rate`、`review`	|
| filelist.result.porn.label 	| integer  	| 图片被判定的分类，可能值 `0`、`1`、`2`，`0` 表示正常，`1` 表示色情，`2` 表示性感	|
| filelist.result.porn.rate     | float   	| 图片被判定为某个分类的概率，介于 `[0-1]` 之间	|
| filelist.result.porn.review   | boolean  	| 是否需要人工复审，`true` 表示需要，`false` 表示不需要	|

### 举例

**请求示例**

```
POST /video/check HTTP/1.1
Host: banma.api.upyun.com
Date: Thu, 12 Oct 2017 06:57:51 GMT
Authorization: UPYUN 2GO7K6NCPInWZqvJewohbDH5jmtaBY0x:qXT2W1ROzdO3WXVM2nlLguc370k=
Content-Length: 109
Content-Type: application/json

{"url": "http://uprocess.b0.upaiyun.com/demo/short_video/UPYUN.mp4", "notify_url": "http://httpbin.org/post"}
```

**响应示例**

```
HTTP/1.1 200 OK
Server: marco/1.8
Content-Type: application/json
Content-Length: 65
Date: Thu, 12 Oct 2017 06:57:45 GMT

{"status_code":200,"task_id":"AIYgOOTk2OMav3iS64zPSS1Js0FzkT95"}
```

**回调示例**

```
POST <notify_url> HTTP/1.1
Content-Type: application/json 
Date: Thu, 12 Oct 2017 04:06:43 GMT 
Content-Md5: d2acd6c01e1de9afa203b585f614c0cf 
Content-Length: 1775 
Authorization: UPYUN 2GO7K6NCPInWZqvJewohbDH5jmtaBY0x:P8qmASfkbtM/6CMo1WlqW59W9+g=

{"error":"","filelist":[{"result":{"porn":{"label":0,"rate":0.9995450377464294,"review":false}},"shot_time":0},{"result":{"porn":{"label":0,"rate":0.9990586638450623,"review":false}},"shot_time":10},{"result":{"porn":{"label":0,"rate":0.9997151494026184,"review":false}},"shot_time":20},{"result":{"porn":{"label":0,"rate":0.9990173578262329,"review":false}},"shot_time":30},{"result":{"porn":{"label":0,"rate":0.9789921641349792,"review":false}},"shot_time":40},{"result":{"porn":{"label":0,"rate":0.9992973804473877,"review":false}},"shot_time":50},{"result":{"porn":{"label":0,"rate":0.9562959671020508,"review":false}},"shot_time":60},{"result":{"porn":{"label":0,"rate":0.9989614486694336,"review":false}},"shot_time":70}],"result":{"porn":{"label":0,"rate":0.9931503997908698,"review":false}},"service":"","source":"","status_code":200,"task_id":"rDt7IaxKeQMGWVvX8YZ5MbwJtT0WuQbh"}
```


## 文本识别

提交文本内容，进行识别。识别包括色情、广告、暴恐等，`act=spam` 表示违规识别。

### 请求

**请求语法**

```
POST /text/check?act=spam HTTP/1.1
Host: banma.api.upyun.com
Date: GMT Date
Authorization: UPYUN <ClientKey>:<Signature>
Content-Type: application/json

{"text": "<文本内容>"}
```

** 签名认证 **

`Authorization` 详见[签名认证](/ai/authorization/#_1)。

`代码实现` 详见[代码示例](/api/authorization/#_3)。

**请求参数**

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| text       		| string       	| 是   	| 文本内容，JSON 字符串，最大允许 64k  |

### 响应

响应参数是 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| status_code	    | integer   | 处理结果状态码，`200` 表示成功处理 |
| error	         	| string   	| 错误信息，空字符串表示无错误信息 |
| spam 				| json  	| 文本识别结果	|	
| spam.task_id   	| string   	| 任务 ID  |
| spam.scores	   	| list    	| 各个分类及概率, 从左至右依次是 `正常概率`、`违规概率` |
| spam.label   		| integer   | 推荐分类，可能值 `0`、`1`、`2`，`0` 表示正常，`1` 表示违规，`2` 表示疑似  |
| spam.rate       	| float    	| 推荐分类的概率                                            		|
| spam.review 		| bool  	| 是否需要人工审核，`true` 表示需要，`false` 表示不需要	|


### 举例

**请求示例**

```
POST /text/check HTTP/1.1
Host: banma.api.upyun.com
Date: Tue, 19 Sep 2017 07:47:49 GMT
Authorization: UPYUN x2biJtrSOGhuYj4vzPg70nfZeLEIdmlH:Q+sPbbukYstHJprpSLsS+lnOrro=
Content-Length: 30
Content-Type: application/json

{"text": "这是正常文本"}
```

**响应示例**

```
HTTP/1.1 200 OK
Server: marco/1.8
Content-Type: application/json
Content-Length: 190
Date: Tue, 19 Sep 2017 07:47:50 GMT

{"status_code": 200, error: "", "spam":{"task_id":"6583350f","scores":[0.9713295698165894,0.028670458123087883],"rate":0.028670458123087883,"label":1,"review":false}}
```

<a name="status"></a>
## 状态码说明

| 参数       	| 类型       	|
|---------------|---------------|
| 200    		| 处理成功     	|
| 400    		| 请求参数错误 	|
| 401    		| 请求认证失败 	|
| 5xx    		| 服务端错误，请反馈给[售后](https://www.upyun.com/contact)或您的商务经理   	|


## Python 演示

```py
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
client_key = '2GO7K6NCPInWZqvJewohbDH5jmt'
client_secret = 'BKQrAn5OjgG3HVlkJo78DbyiuL'

# 图片 URL 进行色情识别 
def image_check():
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'POST', '/image/url/check', headers['Date'])
    data = {
        'url': 'http://uprocess.b0.upaiyun.com/demo.jpg'
    }
    resp = requests.post('http://banma.api.upyun.com/image/url/check', headers=headers, json=data)
    print(resp.status_code, resp.text)


# 视频点播 URL 进行色情识别
def video_check():
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'POST', '/video/check', headers['Date'])
    data = {
        'url': 'http://uprocess.b0.upaiyun.com/demo/short_video/UPYUN.mp4',
        'notify_url': 'http://httpbin.org/post'
    }
    resp = requests.post('http://banma.api.upyun.com/video/check', headers=headers, json=data)
    print(resp.status_code, resp.text)


# 文本识别
def text_check():
    # 文本审核
    headers = {}
    headers['Date'] = httpdate_rfc1123()
    headers['Authorization'] = sign(client_key, client_secret, 'POST', '/text/check', headers['Date'])
    data = {
        'text': '这里放文本内容'
    }
    resp = requests.post('http://banma.api.upyun.com/text/check', headers=headers, json=data)
    print(resp.status_code, resp.text)


# 调用
def main():
    image_check()
    video_check()
    text_check()


if __name__ == '__main__':
    main()
```
