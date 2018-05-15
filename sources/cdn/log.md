##日志分析

可根据时间粒度，查看相应访问域名下热门文件、热门客户端、热门引用文件、资源状态码、文件大小、热门 IP 等维度 top1000 的统计分析数据。日志分析是以天为粒度进行分析的，第二天凌晨会开始进行日志分析工作，正常情况下，第二天早晨 09:00:00 之前会统计计算完毕。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，在置顶菜单进入 `工具箱`，找到 `日志管理` 栏目，选择 `日志分析` 选项。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-log-analysis.png" height="490" width="800" />

----------

##日志下载

支持以小时粒度的日志下载，可收集详细的终端访问日志；日志文件延迟 1 小时，可以在日志管理模块查询到 1 小时之前的日志文件。

日志命名规则：时间段-访问域名-随机数.gz，日志命名示例：
> 10_00-impress-demo.b0.upaiyun.com-9571272666104702574.gz

**日志格式说明**

    $remote_addr - $remote_user [$time_local] "$request_method $scheme://$http_host$uri$querystring $server_protocol" $status $body_bytes_sent "$http_referer" "$http_user_agent" $content_type $request_content_length $cache_hit $source_code $is_dynamic $cache_control $request_time $edge_server_ip

**详细参数说明**

|   序号     |    字段          |   解释 |
|------------|------------|-----------------|
| 1   | $remote_addr |    客户端 IP       |
| 2   | $remote_user         | HTTP Auth 的用户名  |
| 3   | $time_local	            | 访问时间 |
| 4   | $request_method	   | 访问类型 GET | POST | PUT | DELETE ...|
| 5   | $scheme	           | 应用协议 |
| 6   | $http_host	          | 访问域名    |
| 7   | $uri	           | 请求 URI     |
| 8   | $querystring	   |  请求参数    |
| 9   | $server_protocol	        | HTTP 协议版本  |
| 10  | $status	         | 返回状态码     |
| 11  | $body_bytes_sent	  | 发送 body 字节数|
| 12  | $http_referer	     | 请求来源 referer	 |
| 13  | $http_user_agent	 | 客户端信息 |
| 14  | $content_type	 | 内容类型|
| 15  | $request_content_length	| 客户端向服务端发起请求的内容大小|
| 16  | $cache_hit	     | 是否 CDN 缓存命中|
| 17  | $source_code	 | 源站返回状态码|
| 18  | $is_dynamic	     | 是否动态请求|
| 19  | $cache_control	 | 缓存控制头信息|
| 20  | $request_time	   | 请求处理耗时|
| 21  | $edge_server_ip	   | CDN 边缘节点 IP|


**日志样例**

    122.96.42.186 - - [13/Dec/2016:00:00:02 +0800] "GET http://impress-demo.b0.upaiyun.com/kphone/icons/weihui_icon3.png?v=1.0 HTTP/1.1" 200 1851 "-" "Mozilla/4.0" "image/png" 0 Hit "C/200" Static "max-age=900" 0.014 153.101.64.13
    

**字段说明**

|   序号     |    字段          |   示例值   |
|------------|------------|-----------------|
| 1   | 客户端 IP   |    122.96.42.186        |
| 2   | HTTP Auth 的用户名        | - |
| 3  | 访问时间             | [13/Dec/2016:00:00:02 +0800] |
| 4   | 客户端请求方法            | GET    |
| 5       | 请求协议           | HTTP    |
| 6       | 访问域名           | impress-demo.b0.upaiyun.com   |
| 7       | 请求 URI           | /kphone/icons/weihui_icon3.png    |
| 8       | 请求参数          |  v=1.0   |
| 9   | HTTP 协议版本           | HTTP/1.1  |
| 10 | 返回状态码           | 200    |
| 11     | 发送 body 字节数  | 1851    |
| 12   | Referer 请求头     | - |
| 13    | 客户端信息 | Mozilla/4.0 |
| 14    | 内容类型 | image/png |
| 15    | 客户端向服务端发起请求的内容大小 | 0 |
| 16   | 缓存命中状态             | Hit    |
| 17    | 源站返回状态码         | C/200 |
| 18    | 动态请求标记             | Static   |
| 19    | 缓存控制头信息            | max-age=900  |
| 20    | 请求处理耗时（单位s）   | 0.014  |
| 21    | CDN 边缘节点 IP            | 153.101.64.13  |

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，在置顶菜单进入 `工具箱`，找到 `日志管理` 栏目，选择 `日志下载` 选项。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-log-download.png" height="490" width="800" />


关于日志分析和下载视频教程请点击 [日志管理视频教程](https://techs.b0.upaiyun.com/videos/cdnpage/upyunlog.html)。


----------

如有疑问请 [联系我们](https://www.upyun.com/contact)





