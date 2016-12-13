##创建服务

**第一步：选择服务类型**

登录 [又拍云管理控制台](https://console.upyun.com/login/)，在控制台页面，分别点击右上角的 `服务` 、`创建服务` 等按钮，选择服务类型。根据业务情况，可选择 `全网加速服务` 或 `直播加速服务` 。具体操作如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-choose-server.png" height="490" width="800" />


**第二步：填写配置信息**

选择服务类型之后，则进入服务创建阶段，需要进行源站类型、回源配置、操作员设置等步骤。具体配置界面可参见如下图所示：

创建服务：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-create-server.png" height="490" width="800" />

创建操作员：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-create-operation.png" height="490" width="800" />


**源站类型**

目前源站类型包括 `又拍云 ` 和 `自主源`。其中 `又拍云` 表示资源都存放在又拍云分布式对象存储服务上，同时附加了 CDN 服务的支持，因此也拥有绝大多数又拍云 CDN 的功能；而 `自主源` 则表示资源都存放在客户自己的源站或第三方云存储服务上，仅使用又拍云 CDN 服务。

配置引导

系统默认会选择 `自主源`，请根据具体业务需求进行选择。配置界面如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-choose-orion.png" height="490" width="800" />


> 如果您的资源都托管在第三方云存储平台或者自有源上，请选择 `自主源`
   
> 如果您准备把静态资源都托管在又拍云对象存储平台上，请选择 `又拍云`

**回源配置**

回源配置是 CDN 服务创建最重要的环节，请谨慎配置。也可去功能配置界面进行设置，依次进入：服务 > 功能配置 > 基础配置 > 回源配置，点击管理按钮即可配置。如下截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-orion-management.png" height="490" width="800" />

**回源 Host**

该配置是指 CDN 节点回源过程中所需访问的源站服务器上的域名。CDN 节点回源时会在 HTTP 请求头中 Host 字段里填写该域名，以支持源服务器可通过此 Host 做的特殊处理逻辑，一般是域名形式的字符串，当然也支持任意字符串形式。

配置引导

1.回源 Host 为可选配置项，特别地，如果该自定义值为空，则该服务下的加速域名就会作为回源HOST

2.自定义：可以根据需要修改回源 Host，请保证该域名可以正确访问，否则会出现回源失败的情况



**回源协议**

默认情况下，我们都会以 HTTP 协议回源，您也可以采用 HTTPS 协议回源。另外一个特别的选项是协议跟随，当启用该特性时，回源协议始终会和客户端访问 CDN 的协议保持一致，即客户端用 HTTP 协议访问我们 CDN，那么回源时我们也会用 HTTP 协议进行回源，同样，若采用 HTTPS 访问，那么回源也是 HTTPS。

配置引导

当我们选择 HTTP 协议回源时，回源地址的端口号默认为 80，而选择 HTTPS 协议回源时，端口号默认为 443。当然，你也可以手动指定特殊的端口号，例如 8080 或 8443。

注意事项

选择 `协议跟随` 后，回源地址的端口号配置将仅作用于回源协议为 HTTP 的情况，默认 80，当然你也可以自定义为其他值，而回源协议为 HTTPS 时端口号则始终固定为 443，且目前暂时不支持修改此固定值。因此，若之前选择 HTTPS 协议回源 且自定义设置过端口号的用户需要注意，切换到 `协议跟随` 模式下时，自定义的端口号将仅作用于回源协议为 HTTP 的情况。


**线路配置**

线路配置包括回源地址、端口号、主备线路、轮询权重、最大失败次数、静默时间等高级设置。其中回源地址则表示源站实际可访问的网络地址，可填 IP 地址或域名地址，如果是域名地址，那么 CDN 在回源时会对该域名地址进行 DNS 解析，然后针对解析出来的 IP 地址进行访问，因此若解析失败将会导致回源异常。

配置引导

配置界面如下截图所示，以下将重点介绍主备线路、轮询权重、最大失败次数和静默时间。

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-line-config.png" height="490" width="800" />


 - 主、备线路
 
回源地址配置中除了默认的 `主线路` 外，我们还支持 `备份线路`  的配置。默认情况下， `主线路` 多个回源地址会以 RR 轮询方式进行调度，当且仅当所有 `主线路` 回源地址均不可达时，此时我们将使用 `备份线路` 进行转发，若 `备份线路` 有多个回源地址，那么负载均衡方式也和 `主线路` 一样，以 Round-Robin 轮询（详情请点击[链接](https://en.wikipedia.org/wiki/Round-robin_scheduling)）方式进行调度。


 - 轮询权重

默认情况下，轮询权重都为 1，表示所有回源地址轮询到的概率均等。当然也可以如上图那样自定义轮询权重，我们看到主线路两个回源地址权重分别是 1 和 2，此时主线路集群在轮询调度中会有 1/3 的概率回 `192.168.11.1:80` 这个地址，而回 `192.168.11.2:80` 这个地址的概率则为 2/3。即当前回源地址轮询到的概率可按（当前节点权重/集群所有节点权重之和）这个公式计算。

 - 最大失败次数和静默时间

表示在一个 `静默时间 (fail_timeout) ` 内如果对应源站的异常次数累计达到当前设置的 `最大失败次数 (max_fails)` ，那么在下一个  `静默时间 (fail_timeout)` 内，CDN 就会认为这个源站宕机了，即在这段时间内不会再将请求转发给它。特别地，当 `最大失败次数 (max_fails)` 其值为 0 时，表示不会进行失败累计，`静默时间 (fails_timeout)` 也同时失效。

合理设置最大失败次数和静默时间，有助于在源站异常的情况下最大程度减少对线上请求的影响。这里实际上跟 Nginx 核心的 upstream 模块实现机制类似，进一步了解可参考 [链接](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server) 。
 

**创建操作员**

当您在创建服务的过程中，源站类型选择为 `又拍云` 时 ，则创建和授权操作员是一个必须的步骤。您可以创建一到多个操作员，满足不同的管理需求，可用于对存储在又拍云的资源进行 API 上传、删除时的认证授权和 FTP 登录等。

配置引导

您可以选择新创建一个操作员，也可以授权已有操作员，并且可以针对权限进行设置，包括可读取、可写入、可删除。配置界面如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-create-operation.png" height="490" width="800" />


你也可以在管理后台右上角的帐号下拉菜单中选择 `账户管理－> 操作员管理 -> 编辑` 进行设置。


**域名绑定**

针对一个 CDN 服务，假如回源地址相同，可以绑定多个自定义域名( 目前最多可绑定 10 个域名 )。特别地，需要确保该域名已经备案成功，否则无法通过域名绑定审核。另外，审核成功后会以邮件形式进行通知。

配置引导

1.进入功能配置界面，选择 `加速域名` 模块，点击 `域名绑定` 按钮，添加域名绑定。

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-domain-binding.png" height="490" width="800" />


 2.除常规形式的域名外（包括顶级域名），我们还支持泛域名绑定，比如 `*.yourdomain.com`；特别地，其中 `*` 最多支持匹配
    4 层：

```
[v] *.yourdomain.com => foo.yourdomain.com
[v] *.yourdomain.com => bar.yourdomain.com
[v] *.yourdomain.com => foo.bar.yourdomain.com
[v] *.yourdomain.com => foo.bar.baz.yourdomain.com
[v] *.yourdomain.com => foo.bar.baz.qux.yourdomain.com
[x] *.yourdomain.com => foo.bar.baz.qux.quxx.yourdomain.com

[v] *.bar.yourdomain.com => foo.bar.yourdomain.com
[v] *.bar.yourdomain.com => baz.foo.bar.yourdomain.com
[x] *.bar.yourdomain.com => foo.bar.baz.yourdomain.com
```
3.添加域名绑定后，需要到域名服务商的 DNS 解析管理中，将域名的 CNAME 解析到 `<bucket>.b0.aicdn.com`。


**第三步：查看服务状态**

以上步骤都配置完成之后，需要查看所创建的服务以及绑定的域名是否处于正常状态，否则无法正常提供服务。

配置引导

1.首先进入服务管理列表，确保所选服务处于 `服务中` 状态，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-server-status.png" height="490" width="800" />

2.选择相应服务，在管理界面，点击 `配置` 按钮进入 `加速域名` 选项，查看绑定的域名是否是 `正常` 状态。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-domain-status.png" height="490" width="800" />


----------

##缓存配置

缓存配置是指又拍云 CDN 加速节点（包括边缘节点和中心节点）在缓存您的资源文件时遵循的一套过期规则。当资源文件处于过期状态时，此时用户请求会由节点发送至源站，重新获取资源内容并缓存至节点，同时返回给最终用户；当资源内容处于未过期状态时，您的用户请求到达节点后，会由节点直接响应最终用户的请求。合理的配置资源文件缓存时间，能够有效的提升缓存命中率，降低回源率，节省您的源站带宽。

配置引导

登录 [又拍云管理控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入：服务 > 功能配置 > 基础配置 > 缓存配置，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-management.png" height="490" width="800" />

**自定义配置**

您可以根据自身业务需求，在配置界面的右上角，点击右上角的 `添加` 按钮进行资源文件缓存时间的配置，CDN 支持三种维度的缓存时间配置规则：

*根据文件类型设置缓存策略*

您可以根据模板填充文件类型后缀，根据文件类型来设置缓存时间，如下示例所示：

> /*.(txt,doc,wri,docs)   缓存时间 1 天；

设置完成后，域名下所有匹配 `.txt .doc .wri .docs` 的文本资源，在节点的缓存有效时间均为 1 天。

*根据文件目录设置缓存策略*

您可以自定义配置文件路径，根据文件路径来设置缓存时间，如下示例所示：

> /dir/*   缓存时间 12 小时

*全局配置*

全局缓存策略适合源站全是静态资源的客户，可以针对该服务一键开启配置缓存时间，开启请谨慎。详细配置可参照如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-global.png" height="490" width="800" />

设置完成后，该服务下所有域名匹配的资源，在节点的缓存有效时间均为 7 天。

注意事项：

1.全局缓存在自定义缓存配置里面的优先级是最低的，且优先级不可调节，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-priority.png" height="490" width="800" />

2.全局缓存设置有一个单独的开关，需要开启之后，才可以进行配置

*默认模板*

特别地，我们针对自定义缓存配置，默认规划了模板，并且预设了缓存时间，您可以根据具体情况进行修改。具体模板如下：

    首页：/  缓存 12 小时；
    目录页：/*/    缓存 12 小时；
    图片文件：/*.(jpg,jpeg,png,bmp,gif,psd,ico,tga,imb,tiff)      缓存 7 天；
    文本文件：/*.(txt,doc,wri,docs,css,js,dot,xml,log,bat,csv)    缓存 1 天；
    音视频文件：/*.(wav,mp3,wmv,rmi,aac,mp4,rmvb,mkv,avi,flv,swf,rm,mov,movie,qt)  缓存  7 天；
    下载类文件：/*.(exe,ios,apk,ipa,pxl,sis,cab,deb,rar,zip,gzip,tar,7z,bzip2,dmg,gz,wim,tbz,tpz,z,jar)      缓存 7 天；

**不缓存配置**

根据业务情况，可以针对一些动态资源文件（ 例如：php、jsp、asp、do、cgi、action、json等）强制配置不缓存， 详细配置可参照如下所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-no-cache.png" height="490" width="800" />


> /*.(asp,php,jsp,do,json)     配置不缓存

**默认缓存策略**

在没有匹配到自定义缓存规则且源站也没有返回任何有效缓存头的情况下，我们的默认配置策略如下：

> 针对静态资源，所有正常状态码（大于等于 200 小于 400）均缓存 8  天。特别地，301 响应缓存 2 小时，302 响应缓存 20 分钟

> 针对动态资源，程序会自动识别，则不进行缓存

> 对于其他大于等于 400 的不正常响应，则不进行缓存
 
**缓存优先级**

请根据具体缓存优先级情况合理配置缓存策略，又拍云 CDN 加速平台目前的缓存优先级如下：

> 不缓存配置（后台设置） > 自定义缓存配置（后台配置） > 源站缓存配置 > 默认缓存策略。

注意事项：

1.源站缓存配置，指 Cache-Control 和 Expires 请求头的设置

2.以上缓存配置优先级目前不支持顺序调节，后续我们会考虑支持

**特别声明**

针对使用又拍云存储服务的客户，我们暂时不支持缓存配置的自定义设置，我们默认给出了一个默认的缓存配置规则，具体如下：

> 对于正常的状态码（大于等于 200 小于 400）：8 天

> 对于其他大于等于 400 的不正常响应缓存 5 分钟


**动静自动分离**

针对 `自主源` 的 CDN 服务而言，当没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头时，我们就会对回源内容进行动静识别。特别地，我们会根据响应头中的 ` Content-Type` 字段和 URL 中的文件后缀名来判断是否是静态内容，其中 `Content-Type` 匹配优先级高于文件后缀名。

参考列表

以下是我们维护的一份 `Content-Type` 和文件后缀名列表，若满足匹配我们就认为是静态内容：

> Content-Type 列表

```
text/css
text/javascript
application/javascript
application/x-javascript

image/gif
image/png
image/jpeg
image/webp
image/tiff
image/x-icon
image/x-ms-bmp
image/svg+xml

application/font-woff
application/java-archive
application/msword
application/pdf
application/postscript
application/zip
application/octet-stream

application/vnd.openxmlformats-officedocument.wordprocessingml.document
application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
application/vnd.openxmlformats-officedocument.presentationml.presentation

audio/mpeg
audio/ogg
audio/x-m4a

video/mp4
video/x-flv
video/x-m4v
```

> 文件后缀名列表

|css |js |jpg |jpeg |gif |ico |png |bmp |pict |csv |
|-|-|-|-|-|-|-|-|-|-|
|doc |pdf |pls |ppt |tif |tiff |eps |ejs |swf |midi |
|mida |ttf |eot |woff |otf |svg |svgz |webp |docx |xlsx |
|xls |pptx |ps |class |jar |bz2 |bzip |exe |flv |gzip |
|rar |rtf |tgz |gz |txt |zip |mp3 |mp4 |ogg |m4a |
|m4v | apk | | | | | | | | |


----------


##缓存刷新

上一个章节我们介绍了缓存配置，当 CDN 的资源文件缓存过期之后会回源获取文件并缓存，但是这是一个被动更新的过程，资源更新受限于缓存过期时间。当源站资源更新之后需要快速回源下发到 CDN 节点时，可以使用缓存刷新功能来实现，这是一种主动更新的方式。

配置引导

登录 [又拍云管理控制台](https://console.upyun.com/login/)，进入 `刷新` 栏目，您可以选择 `URL 刷新`、`规则刷新`来强制回源更新文件及目录。您也可以在 `操作记录` 选项查询刷新进度及相关状态。

**URL 刷新**

当源站有少量文件更新时，可以使用 `URL 刷新` 来主动删除 CDN 节点上的资源文件。新的请求进来时，CDN 节点会回源获取文件并将新的资源文件缓存至 CDN 节点，所以这是一个主动更新的过程。操作界面如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-url-refresh.png" height="490" width="800" />


注意事项：

1.每行一个URL地址，每个 URL 地址以 http://或https://开头，例如：`http://example.com/image/test.jpg`

2.每次输入不超过 50 个，刷新任务全网生效时间大约 5 到 10 分钟

**规则刷新**

当源站有针对资源进行批量更新时，可以通过 `规则刷新` 来刷新节点缓存文件。实际上，规则刷新并没有删除节点资源文件，规则一旦生效之后，缓存在 CDN 节点的文件会被致过期，新的请求会回源校验一次，如果源站文件有更新，则将新的文件响应给最终用户并替换节点上的旧文件。操作界面如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-rule-refresh.png" height="490" width="800" />


规则示例：

    http://example.com/image/＊
    http://example.com/image/＊.jpg
    http://example.com/image/＊!upyun（ 适用于自定义缩略图版本 ）


注意事项：

1.每行一个路径规则，每个路径规则以 http:// 或 https:// 开头，每次输入不超过 10
   个，例如：`http://example.com/image/＊`
   
2.规则刷新主要用于对使用客户自主源站的 CDN 服务，执行后会在 10 分钟内生效

特别声明：

1.如您使用了又拍云对象存储服务时，如果您通过 `规则刷新` 来刷新缓存的话，基于平台保护，`规则刷新` 生效时间是 1 个小时，请谨慎操作

2.特别地，使用了又拍云对象存储服务时，存储中资源更新（替换或删除）时会自动触发 CDN 节点刷新缓存，会在 5 分钟内生效，所以您无需进行手动刷新操作

**URL 预热**

当源站有新增新的资源文件时，可将这些新文件提前缓存至 CDN 节点，最终用户第一次访问这些资源文件时， CDN 节点不需要回源站请求文件资源了。一方面可减少用户突发访问时的回源量，另一方面可以加快资源文件的首次访问速度。操作界面如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-url-preheat.png" height="490" width="800" />


注意事项：建议不要同时预热太多资源文件，否则会对源站造成回源压力

**操作记录**

进行 `URL 刷新` 、 `规则刷新` 、`URL 预热` 操作之后，界面窗口会立马跳至操作记录界面，您可以实时查看当前操作的进度反馈及相关状态。您也可以查看历史操作记录，我们默认保留了 3 天的数据。

----------

##统计分析


为了更加透明的展示终端用户访问 CDN 以及 CDN 回源的访问情况，又拍云 CDN 服务从带宽、流量、请求数等维度进行了数据统计和分析，方便用户查看和分析，具体功能项参见如下描述：

 - 带宽及流量查询：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，带宽及流量的曲线图，以及运营商、地区分布及排名情况；

 - 请求数查询：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，用户请求数的曲线图；

 - 回源统计：您可以查看指定时间区间、域名、服务名等条件下，带宽及流量曲线以及源站流量分布；

 - 查询明细数据：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，查询每日的流量、请求量、静态请求、动态请求、HTTPS 请求、峰值带宽、峰值带宽时间等明细数据。

 - 导出 Excel 报表：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，可以导出 5 分钟粒度详细的请求数、流量、带宽、峰值带宽时间等详细数据。


配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，在置顶菜单进入 `统计` 栏目，可查询如上统计分析数据：

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

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，在置顶菜单进入 ` 监控` 栏目，可查询如上性能监测数据，如下图所示：

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

##日志分析

可根据时间粒度，查看相应访问域名下热门文件、热门客户端、热门引用文件、资源状态码、文件大小、热门 IP 等维度 top1000 的统计分析数据。日志分析是以天为粒度进行分析的，第二天凌晨会开始进行日志分析工作，正常情况下，第二天早晨 09:00:00 之前会统计计算完毕。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，在置顶菜单进入 `工具箱`，找到 `日志管理` 栏目，选择 `日志分析` 选项。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-log-analysis.png" height="490" width="800" />


----------

##日志下载

支持以小时粒度的日志下载，可收集详细的终端访问日志；日志文件延迟 1 小时，可以在日志管理模块查询到 1 小时之前的日志文件。

日志命名规则：时间段-访问域名-随机数.gz，日志命名示例：
> 10_00-impress-demo.b0.upaiyun.com-9571272666104702574.gz

**日志样例**


    122.96.42.186 - - [13/Dec/2016:00:00:02 +0800] "GET http://impress-demo.b0.upaiyun.com/kphone/icons/weihui_icon3.png?v=1.0 HTTP/1.1" 200 1851 "-" "Mozilla/4.0" "image/png" 0 Hit "C/200" Static "max-age=900" 0.014 153.101.64.13
    

**字段说明**

|   序号     |    字段          |   示例值   |
|------------|------------|-----------------|
| 1   | 客户端 IP 地址   |    122.96.42.186        |
| 2   | 客户端用户名         | - |
| 3  | 访问时间与时区             | [13/Dec/2016:00:00:02 +0800] |
| 4   | 客户端请求方法            | GET    |
| 5       | 请求协议           | HTTP    |
| 6       | 访问域名           | impress-demo.b0.upaiyun.com   |
| 7       | 客户端请求 URI           | /kphone/icons/weihui_icon3.png    |
| 8       | 请求参数          |  v=1.0   |
| 9   | 请求协议版本信息           | HTTP/1.1  |
| 10 | 响应状态码           | 200    |
| 11     | 发送给客户端的字节数  | 1851    |
| 12   | Referer 请求头     | - |
| 13    | User-Agent 头 | Mozilla/4.0 |
| 14    | 内容类型 | image/png |
| 15    | 请求内容大小 | 0 |
| 16   | 缓存命中状态             | Hit    |
| 17    | 源站类型 (U: 云存储 C：自主源)/状态码          | C/200 |
| 18    | 动态请求标记             | Static   |
| 19    | 缓存时间             | max-age=900  |
| 20    | 请求处理时间（单位s）   | 0.014  |
| 21    | CDN 边缘节点 IP            | 153.101.64.13  |

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，在置顶菜单进入 `工具箱`，找到 `日志管理` 栏目，选择 `日志下载` 选项。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-log-download.png" height="490" width="800" />


关于日志分析和下载视频教程请点击 [日志管理视频教程](https://techs.b0.upaiyun.com/videos/cdnpage/upyunlog.html)。


----------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)


