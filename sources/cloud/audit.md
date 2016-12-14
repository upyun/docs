## 快速入门

又拍云云处理（图片处理）基于 CDN 或云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建 [CDN 服务](/cdn/guide/)或[云存储服务](/api/quick_start/)。

收费方面，现在免费公测中，后续会进行收费。

---------

## 开发者指南

<a name="async_upload_process"></a>
### 上传预处理

在上传图片时，对上传的图片进行鉴别。任务以异步的方式处理，处理完成后，回调通知用户鉴别结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多允许包含一个鉴别任务，每张图片只需鉴别一次。

** apps 参数结构 **

```
apps = [
    {                                               // 异步鉴别任务
        "name": "imgaudit",                         // 异步任务名称，必填。imgaudit 表示异步图片鉴别服务
        "notify_url": "<notify_url>"                // 回调地址，不填时使用上传参数中的 notify_url
    },
	{ 												// 举例			
	    "name": "imgaudit",                                         
	    "notify_url": "http://www.yourdomain.com/notify/url",                  
	},
    ......
]
```

### 回调通知 

任务处理完成后，向 `notify_url` 发送 `HTTP POST` 请求，请求体是回调信息。

```
curl -X POST \
    <notify_url> \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "bucket_name=<bucket_name>" \
	# 其他参数...
```

** 回调信息 **

回调信息为 JSON 格式，参数名及说明如下：

| 参数       	| 类型   	| 说明                                                      	|
|---------------|-----------|-----------------------------------------------------------|
| bucket_name   | string    | 图片所在的服务名                                     		|
| status_code   | integer   | 处理结果状态码，`200` 表示成功处理，详见[状态码表](#status)   	|
| source        | string    | 图片路径                                            		|
| label  		| integer  	| 图片被机器判定为某个分类的概率，`0` 表示正常的概率，`1` 表示涉黄的概率 |
| task_id       | string    | 任务对应的 `task_id`                                    	|
| error       	| string   	| 错误信息，空字符串表示无错误信息   							|

<!--

| label  		| integer  	| 图片被机器判定的分类，可能值 `0`、`1`，`0` 表示正常，`1` 表示涉黄 	|
| rate  		| float   	| 图片被机器判定为某个分类的概率，概率介于 `[0-1]` 之间，您可以根据您的需求确定需要人工复审的界限 |
| review  		| boolean  	| 是否需要人工复审，`true` 表示需要，`false` 表示不需要 			|

-->

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 举例 **

```
{
	"labels": {
        "0": 0.9833183288574219,
        "1": 0.016681667417287827
    },
    "status_code": 200,
    "bucket_name": "upyun-temp",
    "task_id": "7b01610c7fa700b56cd7c80be5e08d5c",
    "source": "upyun.jpg"
}
```
<!--
    "label": 0,
	"rate": 0.9833183288574219,
    "review": false
-->

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

当前，支持涉黄鉴别。后续会陆续上线广告、涉政、暴恐、违禁品鉴别。


### 涉黄鉴别

色情，裸露生殖器官或者含有性暗示的图片

<img src="http://p.upyun.com/docs/cloud/porno2.jpg" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/porno3.png" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/porno4.jpg" height="180" target="_blank" title="查看" />

正常，非色情图片

<img src="http://p.upyun.com/docs/cloud/normal1.jpg" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/normal2.jpg" height="180" target="_blank" title="查看" />
<img src="http://p.upyun.com/docs/cloud/normal3.jpg" height="180" target="_blank" title="查看" />

<!--

### 小广告鉴别

### 涉政鉴别

### 血暴鉴别

### 违禁品鉴别

-->

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
