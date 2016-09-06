当源站的资源发生变更后，可以调用该 API 接口告知又拍云刷新缓存，缓存更新后，用户访问时会获取到更新后的资源。

特别地，使用又拍云存储会在资源变更后，自动刷新缓存，省去使用自主源站缓存刷新这一步。

## API 地址

```
http://purge.upyun.com/purge/
```

## 请求方法

```sh
curl -X POST \
    http://purge.upyun.com/purge/ \
    -H "Authorization: <your_authorization>" \
    -H "Date: <date>" \
    -F purge=<url-list>
```

其中，请求头部的 `Authorization` 的头部签名算法如下所示：

1. 将需要刷新的文件 URL（以 *`http://`* 开头） 以 *`\n`* 为间隔符连接
2. 将第 1 步所得字符串、空间名、日期（GMT 格式）、经过 MD5 处理后的操作员密码用 `&` 拼接
3. 将第 2 步所得字符串进行 MD5 编码处理，所得记为 `sign`,
4. 最后，将空间名，操作员名和第 3 步所得的 `sign` 以如下格式作为请求头参数传递：

```
Authorization: UpYun <bucket-name>:<operator-name>:<sign>
```

另外，需将待刷新的 URL 列表在以换行符 *`\n`* 连接后以表单字段 `purge` 随请求发送。


## 响应状态

状态码 | 说明
---------|--------
200 | json 格式的字符串{invalid\_domain\_of\_url:不属于自己域名的 url 列表}
401 | 无认证头部信息（Authorization）（提示信息：Need Authorization Header）
401 | 无认证头部信息（Date）（提示信息：Need Date Header）
401 | 操作员不存在（提示信息：User is not exists）
401 | 空间名不存在（提示信息：Bucket is not exists）
401 | 请求时间超出 30 分钟（提示信息：Date offset error）
401 | 签名错误（提示信息：Sign error）
406 | 每分钟刷新 URL 个数超过 600 限制
