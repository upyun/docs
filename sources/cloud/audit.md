## 快速入门

又拍云处理（内容审核）基于 CDN 或云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建 [CDN 服务](/cdn/guide/)或[云存储服务](/api/quick_start/)。

收费方面，现在免费公测中，后续会进行收费。

---------

## 开发者指南

<a name="submit_task"></a>
### 提交处理任务

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
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#notify_url)    	|
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#tasks)  	|
| app_name          | string       	| 是   	| 固定值，`imgaudit`			      	|


<a name="tasks"></a>
** tasks 参数说明 **

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。** 任务参数见[功能](#function) **。

```
[
	{
		"source": "/a.jpg",     // 需要审核的图片
	},
  	{
		"source": "/b.jpg",   	// 需要审核的图片
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

---------

<a name="notify_url"></a>
### 回调通知 

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

回调信息为 JSON 格式，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| service	   		| string    | 图片所在的服务名                                     		|
| status_code   	| integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)   	|
| source        	| string    | 图片路径                                            		|
| result 			| json  	| 识别结果集，包含 `porn`										|
| porn 				| json  	| 色情识别结果，包含 `label`、`rate`、`review`					|
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
}
```

---------

<a name="async_upload_process"></a>
### 上传预处理

![上传预处理](http://uprocess.b0.upaiyun.com/docs/cloud/audit.png)

在上传图片时，对上传的图片进行识别。任务以异步的方式处理，处理完成后，回调通知用户识别结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多允许包含一个识别任务，每张图片只需识别一次。

** apps 参数结构 **

```
apps = [
    {                                               // 异步内容审核任务
        "name": "imgaudit",                         // 异步任务名称，必填。imgaudit 表示异步内容审核服务
        "notify_url": "<notify_url>"                // 回调地址，不填时使用上传参数中的 notify_url
    },
	{ 												// 举例			
	    "name": "imgaudit",                                         
	    "notify_url": "http://www.yourdomain.com/notify/url",                  
	},
    ......
]
```

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见「[回调通知](#notify_url)」。

---------

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        							|
|---------------|---------------------------------------|
| 200         	| 处理成功    							|
| 404         	| 文件不存在    							|
| 413         	| 文件大小超过 10M  						|
| 415         	| 文件不是图片    						|
| 5xx         	| 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/about_contact.html)或您的商务经理 |

---------

### 测试准确率

根据常见图片内容，选择主要图片类型进行色情识别，识别的准确率如下：

| 图片类型 		| 图片总数	| 检测结果                        									| 准确率 	|
|---------------|-----------|-------------------------------------------------------------------|-----------|
| 人脸（正常）	| 12282 	| (0, 0.2]: 11387, (0.2, 0.5]: 642, (0.5, 0.8]: 200, (0.8, 1]: 53   | 98%		|
| 宠物（正常）   	| 6825  	| (0, 0.2]: 6608, (0.2, 0.5]: 163, (0.5, 0.8]: 45, (0.8, 1]: 9      | 99%		|
| 风景（正常）	| 17369 	| (0, 0.2]: 17284, (0.2, 0.5]: 58, (0.5, 0.8]: 23, (0.8, 1]: 4      | 99.8%		|
| 色情   		| 16006 	| (0, 0.2]: 1, (0.2, 0.5]: 16, (0.5, 0.8]: 362, (0.8, 1]: 15627 	| 97.63%	|

** 注 **

- 人脸、宠物、风景正常图片准确率计算公式是： (0,0.8] 图片数/图片总数。色情图片准确率计算公式是：(0.8,1] 图片数/图片总数。
- 测试的样本选自实际业务系统，且是第一次使用。

---------

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

<!--

### 涉政识别

### 违禁识别

-->

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
