## 快速入门

又拍云处理（内容审核）基于 CDN 或云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建 [CDN 服务](/cdn/guide/)或[云存储服务](/api/quick_start/)。

收费方面，现在免费公测中，后续会进行收费。

---------

## 开发者指南

### 图片

<a name="image_upload_process"></a>
#### 上传预处理

![上传预处理](http://uprocess.b0.upaiyun.com/docs/cloud/audit.png)

在上传图片时，提供预处理参数。待图片上传完成后，自动进行异步处理，处理完成后，回调通知用户处理结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多只允许包含一个任务。

** apps 参数结构 **

```
apps = [
    {                                               
        "name": "imgaudit",                         // 异步任务名称，imgaudit 表示图片识别
        "notify_url": "<notify_url>"                // 回调地址，不填时使用上传参数中的 notify_url
    }
]
```

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见「[回调通知](#image_notify_url)」。

---------

<a name="image_submit_task"></a>
#### 已经存在云存储中的图片处理

** 请求方法 **

对已经存在云存储中的图片，以 `POST` 方法向 `http://p0.api.upyun.com/pretreatment/` 提交处理任务，响应中返回任务 `task_id`。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
    -d "notify_url=<notify_url>" \
    -d "app_name=<app_name>" \
    -d "tasks=<base64 编码后的任务字符串>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 图片所在的服务名         			|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#image_notify_url)    	|
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#image_tasks)  	|
| app_name          | string       	| 是   	| 处理功能，`imgaudit` 表示图片识别 			|


<a name="image_tasks"></a>
** tasks 参数说明 **

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。

```
[
	{							
		"source": "/upyun.jpg",     // 需要审核的图片
	},
	…
]
```

2\. 把 JSON 字符串 Base64 编码，得到 `tasks`。

** 响应信息 **

- 任务提交成功：返回 `200`，响应体是一组按任务提交先后顺序排序的 `task_id`。例如：

```
[
	"35f0148d414a688a275bf915ba7cebb2",
	"98adbaa52b2f63d6d7f327a0ff223348",
	"c3103189fa906a5354d29bd807e8dc51",
	…
]
```

- 任务提交失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见「[回调通知](#image_notify_url)」。

---------

<a name="image_notify_url"></a>
#### 回调通知 

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

** 回调信息 **

回调信息为 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| service	   		| string    | 图片所在的服务名                                     		|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)   	|
| source        	| string    | 图片路径                                            		|
| result 			| json  	| 识别结果集，包含 `porn`										|
| porn 				| json  	| 鉴黄结果，包含 `label`、`rate`、`review`						|
| label  			| integer  	| 图片被判定的分类，可能值 `0`、`1`，`0` 表示正常，`1` 表示色情 	|
| rate  			| float   	| 图片被判定为某个分类的概率，介于 `[0-1]` 之间	 		|
| review  			| boolean  	| 是否需要人工复审，`true` 表示需要，`false` 表示不需要 			|
| task_id       	| string    | 任务对应的 `task_id`                                    	|
| error       		| string   	| 错误信息，空字符串表示无错误信息   							|

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 举例 **

```
{
    "service": "upyun-temp",
	"status_code": 200,
    "source": "upyun.jpg",
	"result": {
		"porn": { 
			"label": 0,
			"rate": 0.9833183288574219,
	    	"review": false  
		} 
	},
    "task_id": "7b01610c7fa700b56cd7c80be5e08d5c",
	"error": ""
}
```

---------

### 视频点播

<a name="video_upload_process"></a>
#### 上传预处理

![上传预处理](http://uprocess.b0.upaiyun.com/docs/cloud/audit.png)

在上传视频点播时，提供预处理参数。待视频上传完成后，自动进行异步处理，处理完成后，回调通知用户处理结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多只允许包含一个任务。

** apps 参数结构 **

```
apps = [
	{									
	    "name": "videoaudit",   		// 异步任务名称，videoaudit 表示点播识别                       
        "notify_url": "<notify_url>"    // 回调地址，不填时使用上传参数中的 notify_url
		"interval": "6",   				// 截图间隔周期，非必填
		"save_as": "/foo_{index}.jpg",  	// 截图保存路径，非必填
		"resize": "640x480",   				// 截图保存尺寸，非必填
	}
]
```

`interval`、`save_as`、`resize` 参数解释说明，见 [tasks 参数说明](#video_tasks)。

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见「[回调通知](#video_notify_url)」。

---------

<a name="video_submit_task"></a>
#### 已经存在云存储中的视频点播处理

** 请求方法 **

对已经存在云存储中的视频点播，以 `POST` 方法向 `http://p0.api.upyun.com/pretreatment/` 提交处理任务，响应中返回任务 `task_id`。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
    -d "notify_url=<notify_url>" \
    -d "app_name=<app_name>" \
    -d "tasks=<base64 编码后的任务字符串>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 视频点播所在的服务名         			|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#video_notify_url)    	|
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#video_tasks)  	|
| app_name          | string       	| 是   	| 处理功能，`videoaudit` 表示点播识别 |


<a name="video_tasks"></a>
** tasks 参数说明 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| source       		| string       	| 是   	| 视频点播的路径         			|
| interval        	| string       	| 否   	| 截图间隔时间，默认 `6`，单位秒    	|
| save_as           | string       	| 否   	| 截图保存路径，默认不保存截图 <br /> 支持[动态参数](/api/form_api/#save-key)和 `{index}`(必填)，后缀作为截图格式，支持 `jpg` 和 `png`	|
| resize          	| string       	| 否   	| 截图保存尺寸，默认原图大小，格式宽x高，如 `640x480` |

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。

```
[
  	{							
		"source": "/upyun.mp4", 	
		"save_as": "/foo_{index}.jpg",   // {index} 表示第几张截图，第一张是 /foo_1.jpg，第二张是 /foo_2.jpg
	},
	…
]
```

2\. 把 JSON 字符串 Base64 编码，得到 `tasks`。

** 响应信息 **

- 任务提交成功：返回 `200`，响应体是一组按任务提交先后顺序排序的 `task_id`。例如：

```
[
	"35f0148d414a688a275bf915ba7cebb2",
	…
]
```

- 任务提交失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见「[回调通知](#video_notify_url)」。

---------

<a name="video_notify_url"></a>
#### 回调通知 

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

** 回调信息 **

回调信息为 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| service	   		| string    | 视频点播所在的服务名                                     		|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)   	|
| filelist        	| json    	| 截图集                                            			|
| source        	| string    | 截图保存路径，如果请求参数中未设置 `save_as`，不存在      |
| shot_time        	| string    | 截图时间点                                            		|
| result 			| json  	| 识别结果集，包含 `porn`										|
| porn 				| json  	| 鉴黄结果，包含 `label`、`rate`、`review`						|
| label  			| integer  	| 图片被判定的分类，可能值 `0`、`1`，`0` 表示正常，`1` 表示色情 	|
| rate  			| float   	| 图片被判定为某个分类的概率，介于 `[0-1]` 之间	 		|
| review  			| boolean  	| 是否需要人工复审，`true` 表示需要，`false` 表示不需要 			|
| task_id       	| string    | 任务对应的 `task_id`                                    	|
| error       		| string   	| 错误信息，空字符串表示无错误信息   							|


** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 举例 **

```
{
    "service": "upyun-temp",
    "status_code": 200,
    "source": "upyun.mp4",
    "filelist": [
        {
            "result": {
					"porn": { 
					"label": 0,
					"rate": 0.9833183288574219,
			    	"review": false  
				} 
            },
            "shot_time": 0,
            "save_as": "foo_1.jpg"
        }
    ],
    "task_id": "7b01610c7fa700b56cd7c80be5e08d5c",
	"error": ""
}
```

---------

### 视频直播

<a name="live_create"></a>
#### 创建任务

以 `POST` 方法向 `http://p1.api.upyun.com/<service>/liveaudit/create` 提交任务。特别地，`<service>` 需替换为具体的服务名。

```
curl -X POST \
    http://p1.api.upyun.com/<service>/liveaudit/create \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
    -d "source=<source>" \
    -d "save_as=<save_as>" \
    -d "notify_url=<notify_url>" 
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 图片存储的服务名         			|
| source        	| string       	| 是   	| RTMP 流地址，必须以 `rtmp://` 开头  |
| save_as           | string       	| 是   	| 截图保存地址，支持[动态参数](/api/form_api/#save-key)，后缀作为截图格式，支持 `jpg` 和 `png`  	|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#live_notify_url)    	|
| interval        	| string       	| 否   	| 截图间隔时间，默认 `6`，单位秒     	|
| resize          	| string       	| 否   	| 截图保存尺寸，默认原图大小，格式宽x高，如 `640x480` |

** 响应信息 **

- 任务提交成功：返回 `200`，响应体是流最近关键帧的识别结果。例如：

```
{
    "service": "upyun-temp",
	"status_code": 200,
    "source": "upyun.jpg",
	"result": {
		"porn": { 
			"label": 0,
			"rate": 0.9833183288574219,
	    	"review": false  
		} 
	},
    "task_id": "7b01610c7fa700b56cd7c80be5e08d5c"
	"error": ""
}
```

- 任务提交失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

<a name="live_notify_url"></a>
** 周期性处理 **

之后，系统会周期去获取流，并提取最近关键帧进行识别，识别结果向 `notify_url` 发送 `HTTP POST` 请求，请求体是回调信息。

```
curl -X POST \
    <notify_url> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
	# 其他参数...
```

** 回调信息 **

回调信息为 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| service	   		| string    | 图片存储的服务名                                     		|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)   	|
| source        	| string    | 图片路径                                            		|
| result 			| json  	| 识别结果集，包含 `porn`										|
| porn 				| json  	| 鉴黄结果，包含 `label`、`rate`、`review`						|
| label  			| integer  	| 图片被判定的分类，可能值 `0`、`1`，`0` 表示正常，`1` 表示色情 	|
| rate  			| float   	| 图片被判定为某个分类的概率，介于 `[0-1]` 之间	 		|
| review  			| boolean  	| 是否需要人工复审，`true` 表示需要，`false` 表示不需要 			|
| task_id       	| string    | 任务对应的 `task_id`                                    	|
| error       		| string   	| 错误信息，空字符串表示无错误信息   							|

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 举例 **

```
{
    "service": "upyun-temp",
	"status_code": 200,
    "source": "upyun.jpg",
	"result": {
		"porn": { 
			"label": 0,
			"rate": 0.9833183288574219,
	    	"review": false  
		} 
	},
    "task_id": "7b01610c7fa700b56cd7c80be5e08d5c"
	"error": ""
}
```

---------

<a name="live_cancel"></a>
#### 取消任务

以 `POST` 方法向 `http://p1.api.upyun.com/<service>/liveaudit/cancel` 提交任务。特别地，`<service>` 需替换为具体的服务名。

```
curl -X POST \
    http://p1.api.upyun.com/<service>/liveaudit/create \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
    -d "task_id=<task_id>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 图片存储的服务名         			|
| task_id        	| string       	| 是   	| 任务 ID, 任务创建时系统返回的任务 ID	|

** 响应信息 **

- 任务提交成功：返回 `200`。例如：

```
{"status": 200}
```

- 任务提交失败：返回相应的出错信息，具体请参阅「[状态码表](#status)」。

** 注 **

- 如果系统对流操作失败次数过多，如流已经停止, 会自动触发取消操作。

---------

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        							|
|---------------|---------------------------------------|
| 200         	| 处理成功    							|
| 400         	| 缺失必填参数，ErrMissingRequiredArgument   |
| 400         	| 参数类型错误，ErrType   |
| 400         	| `interval` 参数错误，ErrInterval    	|
| 403         	| 任务已存在，ErrRepeat   				|
| 404         	| 文件不存在/直播流不存在/任务不存在    	|
| 413         	| 文件大小超过 10M  						|
| 415         	| 文件不是图片    						|
| 5xx         	| 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/about_contact.html)或您的商务经理 |

---------

### 准确率

从某业务系统随机抽取 24 万张图片，先进行机器审核，再对结果进行人工复查两遍，最终确定准确率是 99.68%。即 99.68% 的机器判断是准确的，类似于人工审核的准确率，可以代替人工进行审核。

<!--

| 图片类型 		| 图片总数	| 检测结果                        									| 准确率 	|
|---------------|-----------|-------------------------------------------------------------------|-----------|
| 人脸（正常）	| 12282 	| (0, 0.2]: 11387, (0.2, 0.5]: 642, (0.5, 0.8]: 200, (0.8, 1]: 53   | 98%		|
| 宠物（正常）   	| 6825  	| (0, 0.2]: 6608, (0.2, 0.5]: 163, (0.5, 0.8]: 45, (0.8, 1]: 9      | 99%		|
| 风景（正常）	| 17369 	| (0, 0.2]: 17284, (0.2, 0.5]: 58, (0.5, 0.8]: 23, (0.8, 1]: 4      | 99.8%		|
| 色情   		| 16006 	| (0, 0.2]: 1, (0.2, 0.5]: 16, (0.5, 0.8]: 362, (0.8, 1]: 15627 	| 97.63%	|

** 注 **

- 人脸、宠物、风景正常图片准确率计算公式是： (0,0.8] 图片数/图片总数。色情图片准确率计算公式是：(0.8,1] 图片数/图片总数。
- 测试的样本选自实际业务系统，且是第一次使用。

-->

---------

## 智能鉴黄

### 图片

- 如果是新增图片，可以使用[上传预处理](#image_upload_process)接口，把图片上传至又拍云存储的同时，进行鉴黄。

- 如果图片已经存在又拍云存储，可以使用[直接处理](#image_submit_task)接口，进行鉴黄。

### 视频点播

- 如果是新增视频，可以使用[上传预处理](#video_upload_process)接口，把视频上传至又拍云存储的同时，进行鉴黄。

- 如果视频已经存在又拍云存储 ，可以使用[直接处理](#video_submit_task)接口，进行鉴黄。

- 系统按间隔（`interval`）对点播进行截图（关键帧提取），然后对截图进行鉴黄。默认间隔是 6s。如果视频是高敏内容，间隔可以设置的更小。

- 鉴黄结果中有截图时间点信息，它可以帮助定位违规内容的位置，和查看识别内容的位置。

### 视频直播

- 根据直播特性，为视频直播设计了专门的接口，包括[创建任务](#live_create)接口和[取消任务](#live_cancel)接口。

- 系统按间隔（`interval`）对直播流进行截图（关键帧提取），然后对截图进行鉴黄。默认间隔是 6s。如果流是高敏内容，间隔可以设置的更小。

- 没有任务查询接口，需要客户端记录直播流与任务（`task_id`）的关系。

<!--

## 功能

### 色情识别

色情，描述性行为和色情场景、女性裸露敏感部位、男性裸露下体等。

<img src="http://p.upyun.com/docs/cloud/porno2.jpg" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/porno3.png" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/porno4.jpg" height="180" target="_blank" title="查看" />

正常，非色情图片

<img src="http://p.upyun.com/docs/cloud/normal1.jpg" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/normal2.jpg" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/normal3.jpg" height="180" target="_blank" title="查看" />





### 直播鉴黄

### 图片鉴黄

### 点播鉴黄

### 涉政识别

### 违禁识别

-->

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
