## 快速入门

又拍云处理（异步图片拼接）基于云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建[云存储服务](/api/quick_start/)。

价格方面，免费。

---------

## 开发者指南

图片拼接是把云存储中的多张图片拼接在一起，拼接方式多种多样。

<a name="submit_task"></a>
### 云存储中的图片拼接

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
    -d "app_name=jigsaw" \
    -d "tasks=<base64 编码后的任务字符串>"
```

** 认证鉴权 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 图片所在的服务名         			|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#notify_url)    |
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#tasks)  	|
| app_name          | string       	| 是   	| 处理功能，`jigsaw` 表示图片拼接      |


<a name="tasks"></a>
** tasks 参数说明 **

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。** 任务参数见[功能](#function) **。

```
[
	{
        "image_matrix": [						// 需要拼接的图片路径和拼接方式
            ["/image/top.jpg"],
            ["/image/bottom.jpg"]
        ],
        "x-gmkerl-thumb": "/format/jpg",		// 其他的图片处理功能
        "save_as": "/jigsaw/case1.jpg"		// 拼接后图片的保存路径
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
| task_id      	| string    | 任务 ID                             				|
| service  		| string    | 图片所在的服务名                               |
| status_code  	| integer   | 处理结果状态码，`200` 表示成功处理              				|
| path         	| string    | 图片保存路径                              				|
| error        	| string    | 错误信息，空字符串表示无错误信息        						|

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

---------

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        							|
|---------------|---------------------------------------|
| 200         	| 处理成功    							|
| 400         	| 无效任务								|
| 5xx         	| 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/contact)或您的商务经理|

---------

<a name="function"></a>
## 功能

### 图片拼接（jigsaw）

| 参数       		| 类型      		| 必选  	| 说明                                   	|
|-------------------|---------------|-------|-------------------------------------------|
| image_matrix    	| [][]string  	| 是   	| 需要拼接的图片路径和拼接方式 |
| save_as         	| string      	| 是   	| 拼接后图片的保存路径 |
| x-gmkerl-thumb  	| string     	| 否   	| 对拼接后的图片进行[图片处理](/cloud/image/#function) |

** 说明 **

- `image_matrix` 是个二维数组，图片拼接的方式根据二维数组来，第一维数组，表示行，第二维数组，表示列，合起来就是按行、按列拼接。具体按行（填第一维数组，第二维为空），按列（填第二维数组，第一维为空），按行列拼接，根据需求决定。
- 图片拼接最多允许 10 张图片，即 `image_matrix` 最多允许 10 个 URI。
- 默认情况下，拼接产生的图片格式为 PNG 格式，如果需要调整图片格式，可通过设置 `x-gmkerkl-thumb` 做格式转换。

---------

如有疑问请 [联系我们](https://www.upyun.com/contact)
