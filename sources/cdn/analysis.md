##统计分析

为了更加透明的展示终端用户访问 CDN 以及 CDN 回源的访问情况，又拍云 CDN 服务从带宽、流量、请求数等维度进行了数据统计和分析，方便用户查看和分析，具体功能项参见如下描述：

 - 带宽及流量查询
您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，带宽及流量的曲线图，以及运营商、地区分布及排名情况；
 
 - 请求数查询：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，用户请求数的曲线图；

 - 回源统计：您可以查看指定时间区间、域名、服务名等条件下，带宽及流量曲线以及源站流量分布；

 - 查询明细数据：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，查询每日的流量、请求量、静态请求、动态请求、HTTPS 请求、峰值带宽、峰值带宽时间等明细数据。

 - 导出 Excel 报表：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，可以导出 5 分钟粒度详细的请求数、流量、带宽、峰值带宽时间等详细数据。


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，在置顶菜单进入 `统计` 栏目，可查询如上统计分析数据：

查询示例一：查询带宽及请求数曲线图

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-bandwidth-and-request.png" height="490" width="800" />


查询示例二：查询流量地区及运营商分布及排行

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-flow-ranking.png" height="490" width="800" />


查询示例三：查询明细数据

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-flow-details.png" height="490" width="800" />

查询示例四：回源带宽及请求

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-back-to-source-statistics.png" height="490" width="800" />


相关说明：

1.以天为单位查询数据时，您可以查询 5 分钟时间粒度的数据

2.时间区间为大于等于 2 天时，您可以查询 1 小时时间粒度的数据

----------

##性能监控

通过性能监控，可以简单、直观地了解到 CDN 服务质量情况，及时发现和定位问题，并协助解决问题。在界面的主窗口中，您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，对应服务的健康度、缓存命中率、平均下载速度等数据。具体功能项描述如下：

 - 健康度：统计所有请求的 HTTP 状态码，计算 2xx 和 3xx 请求次数所占的比例
 
 - 缓存命中率：统计所有请求数中，命中缓存的请求数所占的比例
 
 - 平均下载速度：可以根据地区和运营商来区分资源文件下载速度和请求耗时
 
 - 拦截攻击次数：拦截的跨站攻击、SQL 注入和其他代码执行等多种攻击的数量（仅限开启 WAF 功能）
 

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，在置顶菜单进入 ` 监控` 栏目，可查询如上性能监测数据，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor.png" height="490" width="800" />


查询示例一：服务状况历史数据（健康度）

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor-health.png" height="490" width="800" />


查询示例二：健康度详情（回源访问）

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor-health-back-to-source.png" height="490" width="800" />


查询示例三：服务状况历史数据（缓存命中率）

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor-hit.png" height="490" width="800" />


查询示例四：地区平均下载速度

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor-speed.png" height="490" width="800" />


----------




