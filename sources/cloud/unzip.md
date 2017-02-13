## 快速入门

又拍云处理（压缩解压缩）基于云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建[云存储服务](/api/quick_start)。

---------

## 开发者指南

<a name="submit_task"></a>
### 提交处理任务

** 请求方法 **

对已经存在云存储中的压缩/解压缩文件，以 `POST` 方法向 `http://p0.api.upyun.com/pretreatment/` 提交处理任务，响应中返回任务 `task_id`。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

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
| service       	| string       	| 是   	| 压缩/解压缩文件所在的服务名         		|
| notify_url        | string       	| 是   	| 回调通知地址，详见[回调通知](#notify_url)           	|
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#tasks)  	|
| app_name          | string       	| 是   	| 处理功能，`compress` 表示压缩，`depress` 表示解压缩      |


<a name="tasks"></a>
** tasks 参数说明 **

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。** 任务参数见[功能](#function) **。

```
[
	{
		"sources": ["/a/1.pdf","/a/b/"],     // 需要压缩的文件路径
		"save_as": "/result/abc.zip"         // 保存路径
	},
  	{
		"sources": ["/e/f","/e/g.jpg"],   		// 需要压缩的文件或目录路径
		"save_as": "/result/e.zip"              // 保存路径
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
| status_code  	| integer   | 处理结果状态码，`200` 表示成功处理              				|
| path         	| string    | 输出文件保存路径                              				|
| error        	| string    | 错误信息，空字符串表示无错误信息        						|

** 回调签名 **

`Authorization` 详见[签名认证](/cloud/authorization/#_1)。

---------

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        							|
|---------------|---------------------------------------|
| 200         	| 处理成功    							|
| 400         	| 文件不合法							 	|
| 404         	| 文件不存在    							|
| 5xx         	| 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/about_contact.html)或您的商务经理|

---------

<a name="function"></a>
## 功能

### 压缩（compress）

将多个文件或文件夹压缩成一个压缩文件。

| 参数       		| 类型      	| 必选  	| 说明                                   	|
|-------------------|-----------|-------|-------------------------------------------|
| sources           | array     | 是   	| 需要被压缩的文件或目录的路径，路径不需要 URI encoding。见「注」        	|
| save_as           | string    | 是   	| 输出文件保存路径     						|
| home_dir          | string    | 否   	| 文件或目录被压缩时可不包含的父目录。默认包含从根开始的全部目录 |
| sources_list      | string    | 否   	| 需要被压缩的文件或目录信息的文件路径。见「注」  |

** 注 **

- `sources` 和 `sources_list` 参数指定的压缩文件最大不能超过 10000 个，总文件大小不能超过 1G。
- 当需要被压缩的文件或目录很多时，可以把这些信息放到一个文件里，一条一行（不需要 URI encoding），再把文件上传到需要处理的服务名下，通过 `sources_list` 参数提交文件 URI ，代替 `sources` 参数进行大量信息提交。
- 当 `sources_list` 参数存在时，`sources` 参数可以不存在。
- 压缩算法: 目前仅支持 `zip`，因此， `save_as` 参数必须以 `.zip` 后缀结尾。
- `home_dir` 会去除指定父目录及父目录下文件，压缩目录层级较深的文件时，可以通过它去除父目录及父目录下文件的干扰。例如：

```json
[
	{
		"sources": ["/a/b/c/source/1.jpg","/a/b/c/source/2.jpg"],
		"save_as": "/result/t.zip",
		"home_dir": "a/b/c"	//压缩时，不包含的目录
	}
]
```

压缩文件的目录结构如下:（ `a/b/c` 父目录已经不存在了，放在 `a/b` 父目录下的文件也已经不存在了）

```
source
|-------1.jpg
|-------2.jpg
```

---------

### 解压缩（depress）

将压缩文件解压到指定目录。

| 参数       		| 类型      	| 必选  	| 说明                                   	|
|-------------------|-----------|-------|-------------------------------------------|
| sources           | string    | 是   	| 需要解压缩文件的路径                        	|
| save_as           | string    | 是   	| 文件解压缩后保存路径（需要为目录）			    |

注：

- `sources` 参数指定的压缩文件最大大小不能超过 5G。
- 解压算法: 目前仅支持 `zip`，因此， `sources` 参数指定的压缩文件必须是 zip 文件。
- 解压缩后，目录中会多一个以 `task_id` 命名的文件，它记录了所有解压缩的文件信息。

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)