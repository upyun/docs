异步文件拉取服务采用主动获取文件的方式，帮助用户把存储在用户源站的文件迁移至又拍云存储，文件迁移完成后，以回调的方式通知用户拉取结果。

异步文件拉取流程如下：
![UPYUN Spider](http://upyundocs.b0.upaiyun.com/img/spider.png)

特别地，本服务旨在为用户提供更便捷的资源上传方式，请您在提交资源链接时确保您拥有该资源的使用权或所有权。

> 注：文中所有 `<>` 标注的字段，均需根据实际情况进行替换。



## 使用方法

### 请求方法

以 `POST` 方式向 `http://p0.api.upyun.com/pretreatment/` 提交处理请求，又拍云接受请求后立即返回任务的 `task_id`，处理完成后通过回调的方式通知用户。

例如：

```
curl -X POST \
    http://p0.api.upyun.com/pretreatment/ \
    -H "Authorization: UPYUN <operator>:<signature>" \
    -H "Date: <Wed, 29 Oct 2014 02:26:58 GMT>"
    -d "bucket_name=<bucket_name>" \
    -d "notify_url=<notify_url>" \
    -d "app_name=spiderman" \
    -d "tasks=<base64 编码后的任务字符串>"
```

### 请求参数

|        参数       |    类型       | 必选     |   说明                           |
|-------------------|--------------|------|---------------------------------------|
| bucket_name       | string       |  是   | 文件所在空间名称                     |
| notify_url        | string       |  是   | 回调通知地址                         |
| tasks             | string       |  是   | 处理任务信息，详见下                 |
| app_name          | string       |  是   | 任务所使用的云处理程序，文件拉取为 spiderman |


`tasks` 参数通过下面三个步骤生成：

1\. 组装业务参数。一次最多可以提交 10 个处理任务。

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

2\. 把组装好的参数转换为 JSON 字符串。

3\. 对 JSON 字符串进行 base64 编码处理。

### 授权认证

授权认证的方法与异步音视频处理授权认证方法一致，请参考 [授权认证](http://docs.upyun.com/cloud/av/#_4)。


### 回调通知

处理完成后，系统根据提交任务时的 `notify_url` 参数，将处理结果转换成 JSON 字符串，并以 `HTTP POST` 请求进行回调通知。

** 回调通知参数 **

|     参数     |    类型   |    说明                                                                                                      |
|--------------|-----------|--------------------------------------------- |
| task_id      | string    | 任务对应的 TaskId                             |
| bucket_name  | string    | 文件所在的空间名                               |
| status_code  | integer   | 处理结果状态码，200 表示成功处理                |
| path         | string    | 文件保存路径                                  |
| error        | string    | 处理错误信息描述，空字符串表示没有错误           |


## 处理参数

|        参数        |    类型   |    说明                                                             |
|-------------------|-----------|--------------------------------------------------------------------|
| url               | string    | 需要拉取文件的 url 地址，如 `http://www.upyun.com/index.html`         |
| x-gmkerl-thumb    | string    | 图片处理参数，非必填项，默认为空。详见 [上传作图](/cloud/image/#_2)      |
| random            | bool      | 是否追加随机数，默认 `false`                                           |
| overwrite         | bool      | 是否覆盖，默认 `true`                                               |
| save_as           | string    | 文件存放路径，必填项， 如 `/site/index.html`                          |

注：

- 如果拉取的资源过大，可能会超时导致拉取失败。
