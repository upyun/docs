## 快速入门

又拍云处理（文件拉取）基于云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建[云存储服务](/api/quick_start)。

---------

## 开发者指南

文件拉取流程如下：
![UPYUN Spider](http://upyundocs.b0.upaiyun.com/img/spider.png)

特别地，本服务旨在为用户提供更便捷的资源上传方式，请您在提交资源链接时确保您拥有该资源的使用权或所有权。

---------

<a name="submit_task"></a>
### 提交处理任务

** 请求方法 **

对需要拉取的文件，以 `POST` 方法向 `http://p0.api.upyun.com/pretreatment/` 提交处理任务，响应中返回任务 `task_id`。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
	-H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
    -d "notify_url=<notify_url>" \
    -d "app_name=spiderman" \
    -d "tasks=<base64 编码后的任务字符串>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 文件上传的服务名         			|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#notify_url)    |
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#tasks)  	|
| app_name          | string       	| 是   	| 处理功能，`spiderman` 表示文件拉取      |

<a name="tasks"></a>
** tasks 参数说明 **

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。** 任务参数见[功能](#function) **。

```
[
	{
		"url": "http://www.upyun.com/index.html",              // 需要拉取文件的 URL
        "random": false,                                       // 是否追加随机数, 默认 false
        "overwrite": true,                                     // 是否覆盖，默认 true
		"save_as": "/site/index.html",                         // 保存路径
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

回调信息是 JSON 字符串，参数名及说明如下：

| 参数       	| 类型   	| 说明                                                      	|
|---------------|-----------|-----------------------------------------------------------|
| task_id      	| string    | 任务对的任务 ID                             				|
| service  		| string    | 文件上传的服务名                               |
| status_code  	| integer   | 处理结果状态码，`200` 表示成功处理              				|
| path         	| string    | 文件上传保存路径                              				|
| error        	| string    | 错误信息，空字符串表示无错误信息        						|

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

---------

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        							|
|---------------|---------------------------------------|
| 200         	| 处理成功    							|
| 400         	| 参数错误								|
| 404         	| 拉取的文件不存在    					|
| 5xx         	| 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/about_contact.html)或您的商务经理|

---------

<a name="function"></a>
## 功能

### 文件拉取（spiderman）

| 参数       		| 类型      	| 必选  	| 说明                                   	|
|-------------------|-----------|-------|-------------------------------------------|
| url               | string    | 是   	| 需要拉取文件的 URL 地址         				|
| save_as           | string    | 是   	| 文件上传存放路径                          	|
| overwrite         | boolean   | 否   	| 如果文件存在，是否覆盖，默认 `true`      		|
| content-md5    	| String   	| 否  	| 上传文件的 MD5 值，如果请求中文件太大计算 MD5 不方便，可以为空        |
| content-type   	| String  	| 否  	| 文件类型，默认使用文件扩展名作为文件类型                         |
| content-secret 	| String  	| 否  	| 文件密钥，用于保护文件，防止文件被直接访问，见 [Content-Secret 参数说明](/api/rest_api/#Content-Secret) |
| x-upyun-meta-x 	| String   	| 否  	| 文件元信息，见 [Metadata](/api/rest_api/#metadata)           |
| x-gmkerl-thumb	 	| string    | 否   	| 图片处理参数，默认不进行图片处理。详见[上传预处理（同步）](/cloud/image/#sync_upload_process)  |
| random            | boolean   | 否   	| 是否在 `url` 后追加随机数，追加格式 `?<random>`，默认 `false`。见「注」|


** 注 **

-  `url` 是指需要拉取文件的 URL 地址，如果文件存在缓存，`random` 参数可以避免我们错误重试或者您重复提交请求时获取到缓存内容。
- 如果拉取文件过大，可能会超时导致拉取失败。

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)