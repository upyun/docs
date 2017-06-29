## 简介

为了解决异步视频处理时间长，又拍云推出针对 M3U8（ts）的实时视频处理解决方案，可以实现秒级视频拼接、合并、元信息获取。您可以把视频切片成 M3U8（ts），来使用实时视频处理。

本类功能现在免费，欢迎使用。在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建[云存储服务](/api/quick_start/)。

相关阅读：[异步音视频处理](/cloud/av/)，[同步音频处理](/cloud/sync_audio/)。

<a name="cancat"></a>
## M3U8 拼接

多个 M3U8 拼接成一个。

### 请求

** 请求语法 **

```
POST http://p1.api.upyun.com/<service>/m3u8er/concat HTTP/1.1
Host: p1.api.upyun.com
Date: GMT Date
Authorization: SignatureValue
Content-Type: application/json

{"m3u8s": ["<拼接 M3U8 的存储地址>"], "save_as": "<结果保存地址>"}
```

** 签名验证 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       	| 类型       	| 必选  	| 说明                              	|
|---------------|---------------|-------|-----------------------------------|
| m3u8s   		| array			| 是  	| 拼接 M3U8 的存储地址，按提交的顺序进行拼接 	|
| save_as 		| string		| 是  	| 结果保存地址         						|

### 响应

| 参数        	| 说明         	|
|:--------------|---------------|
| status_code   | 状态码，见 「[状态码说明](#status)」        	|
| message       | 返回信息      	|

### 举例

** 请求示例 **

```
POST http://p1.api.upyun.com/<service>/m3u8er/concat HTTP/1.1
Host: p1.api.upyun.com
Content-Length: 114
Date: Thu, 11 May 2017 06:45:23 GMT
Content-Type: application/json
Authorization: UPYUN <operator>:6LrWf1AG2qiEjW1RByafxQmSwNQ=

{"save_as": "m3u8s/concat_40s.m3u8", "m3u8s": ["m3u8s/HZW.mp4.0s_20s.m3u8", "m3u8s/bxjg-1700-mp2.ts.0s_20s.m3u8"]}
```

** 响应示例 **

```
HTTP/1.1 200 OK
Server: marco/1.0
Content-Type: application/json
X-Request-Id: 1cdb466a48493b641522965699f4e9f0
Content-Length: 37
Date: Thu, 11 May 2017 07:48:00 GMT

{"status_code": 200, "message": "ok"}
```

<a name="clip"></a>
## M3U8 剪辑

从 M3U8 中剪辑一段，或去掉一段保留前后两段。

### 请求

** 请求语法 **

```
POST http://p1.api.upyun.com/<service>/m3u8er/clip HTTP/1.1
Host: p1.api.upyun.com
Date: GMT Date
Authorization: SignatureValue
Content-Type: application/json

{"m3u8": "<M3U8 的存储地址>", "include": [<开始时间点>, <结束时间点>], "save_as": "<结果保存地址>"}
```

** 签名验证 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       	| 类型       	| 必选  	| 说明                              	|
|---------------|---------------|-------|-----------------------------------|
| m3u8   		| string		| 是  	| M3U8 的存储地址 	|
| save_as   	| string		| 是  	| 结果保存地址 	|
| include 		| array			| 否  	| 包含某段内容的开始结束时间，单位是秒。当 `index` 为 false 时，为开始结束分片序号 |
| exclude   	| array			| 否  	| 不包含某段内容的开始结束时间，单位是秒。当 `index` 为 false 时，为开始结束分片序号 |
| index 		| boolean		| 否  	| `include` 或者 `exclude` 中的值是否为 ts 分片序号，默认为 false |

** 注 **

- `include` 与 `exclude` 互斥，只能设置一个。当两个都设置时，使用 `include`。
- 当 `include` 中设定时间在 ts 分片中间时，会保留整个 ts 分片的内容；当是 `exclude` 时，会去掉整个 ts 分片的内容。
- 当 `index` 是 true 时，`include` 或 `exclude` 使用 ts 文件的序号，序号从 0 开始，即第一个 ts 分片是 0，第二个 ts 分片是 1，以此类推。


### 响应

| 参数        	| 说明         	|
|:--------------|---------------|
| status_code   | 状态码，见 「[状态码说明](#status)」        	|
| message       | 返回信息      	|


### 举例

** 请求示例 **

```
POST http://p1.api.upyun.com/<service>/m3u8er/clip HTTP/1.1
Host: p1.api.upyun.com
Content-Length: 90
Date: Thu, 11 May 2017 06:46:39 GMT
Content-Type: application/json
Authorization: UPYUN <operator>:7z19rZnzeSnzeQvdE6BlG9m7Cn0=

{"save_as": "m3u8s/HZW.mp4.0s_20s.m3u8", "include": [0, 20], "m3u8": "m3u8s/HZW.mp4.m3u8"}
```

** 响应示例 **

```
HTTP/1.1 200 OK
Server: marco/1.0
Content-Type: application/json
X-Request-Id: cfe98cb5fa525984c9f57bdab1a513fb
Content-Length: 37
Date: Thu, 11 May 2017 07:49:14 GMT

{"status_code": 200, "message": "ok"}
```

<a name="snapshot"></a>
## 视频截图（M3U8 截图）

从 M3U8，MP4 或其他格式视频中获取一张截图。

### 请求

** 请求语法 **

```
POST http://p1.api.upyun.com/<service>/snapshot HTTP/1.1
Host: p1.api.upyun.com
Date: GMT Date
Authorization: SignatureValue
Content-Type: application/json

{"source": "<视频的存储地址>", "point": "<时间点>", "save_as": "<截图保存地址>"}
```

** 签名验证 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数              		| 类型   	| 必选	| 说明                                    			|
|-----------------------|-----------|-------|---------------------------------------------------|
| source 				| string 	| 是  | 视频的存储地址					|
| save_as 				| string 	| 是  | 截图保存地址					|
| point   				| string 	| 是  | 截图时间点，格式为 `HH:MM:SS`					|
| size    				| string 	| 否  | 截图尺寸，格式为 `宽x高`，默认是视频尺寸			|
| format  				| string 	| 否  | 截图格式，可选值为 `jpg`，`png`, `webp`, 默认根据 save_as 的后缀生成 |


### 响应

| 参数        		| 说明         	|
|:------------------|---------------|
| status_code   	| 状态码，见 「[状态码说明](#status)」        	|
| message       	| 返回信息      	|
| content_type  	| 截图类型       |
| content_length	| 截图大小      	|
| save_as       	| 截图保存地址   	|


### 举例

** 请求示例 **

```
POST http://p1.api.upyun.com/<service>/snapshot HTTP/1.1
Host: p1.api.upyun.com
Date: Tue, 20 Jun 2017 10:19:15 GMT
Authorization: UPYUN <operator>:4+Hqjo788qC4v4zHRxnvhpweFQo=
Content-Length: 96
Content-Type: application/json

{"service": "<service>", "source": "/tmp/360p.mp4", "save_as": "/foo03.jpg", "point": "00:00:05"}
```

** 响应示例 **

```
HTTP/1.1 200 OK
Server: marco/1.4
Content-Type: application/json
X-Request-Id: 8f404a0b864531edbaa7288563e0db0e
Content-Length: 87
Date: Tue, 20 Jun 2017 10:19:27 GMT

{"status_code": 200, "message": "ok","content_type":"image/jpeg","content_length":14482,"save_as":"/foo03.jpg"}
```

## 获取 M3U8 信息

获取 M3U8 时长和分片信息。

### 请求

** 请求语法 **

```
POST http://p1.api.upyun.com/<service>/m3u8er/get_meta HTTP/1.1
Host: p1.api.upyun.com
Date: GMT Date
Authorization: SignatureValue
Content-Type: application/json

{"m3u8": "<M3U8 的存储地址 >"}
```

** 签名验证 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       	| 类型       	| 必选  	| 说明                              	|
|---------------|---------------|-------|-----------------------------------|
| m3u8   		| string		| 是  	| M3U8 的存储地址 	|

### 响应

| 参数        	| 说明         	|
|:--------------|---------------|
| status_code   | 状态码，见 「[状态码说明](#status)」        	|
| message       | 返回信息      	|
| meta.duartion | m3u8 时长     	|
| meta.points   | 时间点        	|


### 举例

** 请求示例 **

```
POST http://p1.api.upyun.com/<service>/m3u8er/get_meta HTTP/1.1
Host: p1.api.upyun.com
Content-Length: 29
Date: Thu, 11 May 2017 06:40:06 GMT
Content-Type: application/json
Authorization: UPYUN <operator>:3FRsny9cB/7VVRaHv4WyZBvpms0=

{"m3u8": "m3u8s/concat.m3u8"}
```

** 响应示例 **

```
HTTP/1.1 200 OK
Server: marco/1.0
Content-Type: application/json
X-Request-Id: 3f58bba91921504e395b8010037ca038
Content-Length: 5707
Date: Thu, 11 May 2017 08:48:05 GMT

{"status_code": 200, "message": "ok", "meta": {"duration": 2850.2974559999984, "points": [11.277933000000001, 23.723700000000001, 34.634599999999999, 42.008632999999996, 50.483765999999996, 64.764698999999993, 70.804065999999992, 82.31556599999999, 92.892798999999997, 100.20009899999999, 114.74796599999999, 123.92379899999999, 131.09763199999998, 140.97416499999997, 158.32483199999996, 160.05989899999994, 172.70586599999996, 181.04753299999996, 191.79159999999996, <后续省略>]}}
```

<a name="status"></a>
## 状态码说明

| 参数       	| 类型       	|
|---------------|---------------|
| 200    		| 处理成功     	|
| 400    		| 请求参数错误 	|
| 401    		| 请求参数错误 	|
| 404    		| m3u8/视频不存在  	|
| 5xx    		| 服务端错误，请反馈给[售后](https://www.upyun.com/contact)或您的商务经理   	|

---------

如有疑问请 [联系我们](https://www.upyun.com/contact)
