文件拉取接口采用主动拉取的方式，将资源保存 UPYUN 存储中。

> 注：文中所有 `<>` 标注的字段，均需根据实际情况进行替换。

## 使用方法

### 请求方法

以 `POST` 方式向 `http://p0.api.upyun.com/pretreatment/` 提交处理请求，UPYUN 接受请求后立即返回任务 `task_id`，处理完成后会通过回调的方式通知用户。

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

授权认证的方法与异步音视频处理授权认证方法完全一致，具体可参考 [授权认证](http://docs.upyun.com/cloud/av/#_4)。


### 回调通知

处理完成后，系统根据提交任务时的 `notify_url` 参数，将结果以 `HTTP POST` 请求进行回调通知。

** 回调通知参数 **

|     参数     |    类型   |    说明                                                                                                      |
|--------------|-----------|--------------------------------------------------------------------------------------------------------------|
| task_id      | string    | 任务对应的 TaskId                             |
| bucket_name  | string    | 文件所在的空间名                              |
| status_code  | integer   | 处理结果状态码，200 表示成功处理              |
| path         | string    | 文件保存路径                                  |
| error        | string    | 处理错误信息描述，空字符串表示没有错误        |


## 处理参数

用于将用户 UPYUN 存储空间内多个文件或文件夹压缩打包成一个压缩文件。

|        参数       |    类型   |    说明                                                                           |
|-------------------|-----------|-----------------------------------------------------------------------------------|
| url               | string    | 需要拉取文件的 url 地址，如 `http://www.upyun.com/index.html`                     |
| random            | bool      | 是否追加随机数，默认 true  |
| overwrite         | bool      | 是否覆盖，默认 false |
| save_as           | string    | 文件存放路径， 如 `/site/index.html` |
