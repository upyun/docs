## 快速入门

又拍云处理（文档转换）实现把 PDF，PPT，WORD，EXCEL 文档转换为一组 PNG 图片。

您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，并确保您已经创建 [云存储服务](/api/quick_start/)。

---------

## 开发者指南
### 上传预处理

在上传文件时，提供预处理参数。待文件上传完成后，自动进行异步文档转换，处理完成后，回调通知用户处理结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多只允许包含一个任务。

** apps 参数结构 **

```
apps = [
    {
        "name": "uconvert",                             // 必选，任务名称，`uconvert` 表示文档转换
        "save_as": "/save_as/upyun_file",               // 必选，图片保存路径与名字，生成图片自动在文件名后添加 `-<index>.png`
        "density": 100,                                 // 可选，像素密度，越大图片越清晰
        "width" : 1024,                                 // 可选，宽度，大小随宽度等比例缩放
    }
]
```

`save_as`、`density`、`width` 参数解释说明，见 [tasks 参数说明](#tasks)。

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。详见 [回调通知](#notify_url)。

### 云存储中文档的转换

** 请求方法 **

对已经存在云存储中的文档，以 `POST` 方法向 `http://p0.api.upyun.com/pretreatment/` 提交处理任务，响应中返回任务 `task_id`。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <Operator>:<Signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>" \
    -H "Content-MD5: <Content-MD5>" \
    -d "service=<service>" \
    -d "notify_url=<notify_url>" \
    -d "app_name=uconvert" \
    -d "tasks=<base64 编码后的任务字符串>"
```

** 认证鉴权 **

`Authorization` 详见 [签名认证](/cloud/authorization/#_1)。

** 请求参数 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| service       	| string       	| 是   	| 文档所在的服务名         			|
| notify_url        | string       	| 是   	| 回调通知地址，详见 [回调通知](#notify_url)    	|
| tasks             | string       	| 是   	| 任务信息，详见 [tasks 参数说明](#tasks)  	|
| app_name          | string       	| 是   	| 处理功能，`uconvert` 表示文档转换 			|


<a name="tasks"></a>
** tasks 参数说明 **

| 参数       		| 类型       	| 必选  	| 说明                              	|
|-------------------|---------------|-------|-----------------------------------|
| source       		| string       	| 是   	| 文档存放的路径，最大支持 `10M` 文件大小               |
| save_as           | string       	| 是   	| 图片保存路径。生成的图片会自动添加 `-<index>.png` 后缀 |
| density          	| integer      	| 否   	| 图片的像素密度，范围 `[2-200]`，默认为 `100`，越大图片越清晰 |
| width          	| integer      	| 否   	| 图片的宽度，图片大小随宽度等比例缩放，范围 `[0-2048]`，默认自适应 |

1\. 按 JSON 格式组装任务，一次请求 `tasks` 最多可以提交 10 个任务。

```
[
    {
        "source": "/source/upyun.ppt",
        "save_as": "/save_as/upyun",
        "density": 100,                    
        "width" : 1024,                 
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

- 任务提交失败：返回相应的出错信息，具体请参阅 [状态码表](#status)。

<a name="notify_url"></a>
** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。

** 回调信息 **

回调信息为 JSON 字符串，参数名及说明如下：

| 参数       		| 类型   	| 说明                                                      	|
|-------------------|-----------|-----------------------------------------------------------|
| service           | string    | 文档所在的服务名                                     	     |
| source            | string    | 文档路径                                            	    |
| task_id           | string    | 任务对应的 `task_id`                                       |
| paths             | list      | 所有生成图片的路径列表                                      |
| status_code       | integer   | 处理结果状态码，`200` 表示成功处理，详见 [状态码表](#status)  |
| error             | string    | 错误信息，空字符串表示无错误信息                             |

** 回调签名 **

`Authorization` 详见 [签名认证](/cloud/authorization/#_1)。

** 举例 **

```
{
    "error":"",
    "paths":["/save_as/upyun-0.png", "/save_as/upyun-1.png"],
    "service":"upyun",
    "source":"/source/upyun.ppt",
    "status_code":200,
    "task_id":"216fb4f8f9bc03dc6a19c4cf6aa903a4"
}
```

<a name="status"></a>
### 状态码表

| 状态码    		| 说明        							|
|---------------|---------------------------------------|
| 200         	| 处理成功    							|
| 400         	| 文档转换失败                           |
| 413         	| 参数错误，文档类型不支持                    |
| 5xx         	| 服务端错误。如遇此类错误，请反馈给 [售后](https://www.upyun.com/contact)或您的商务经理 |

---------