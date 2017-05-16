又拍云 CDN 服务支持多项自定义配置，您可以根据自身业务需要进行设置，优化您的 CDN 加速效果或配合实现某些业务逻辑。

##1. 配置概览

**回源配置**

配置项                        | 相关说明
:--------------------       | :-------
[回源HOST](https://docs.upyun.com/cdn/config/#21-host)        | 自定义回源 HOST 设置
[回源协议](https://docs.upyun.com/cdn/config/#22)                  | 选择回源协议，比如：HTTP、HTTPS、协议跟随
[线路配置](https://docs.upyun.com/cdn/config/#23)         | 回源地址、端口号、主备线路等高级设置

**缓存配置**


配置项                        | 相关说明
:--------------------       | :-------
[配置引导](https://docs.upyun.com/cdn/config/#31)        | 缓存配置功能入口及相关说明
[自定义缓存](https://docs.upyun.com/cdn/config/#32)                  | 针对资源文件设置自定义缓存过期时间
[不缓存配置](https://docs.upyun.com/cdn/config/#33)         | 针对资源文件强制不缓存
[默认缓存策略](https://docs.upyun.com/cdn/config/#34)         | CDN 节点遵循的默认缓存过期策略
[缓存优先级](https://docs.upyun.com/cdn/config/#35)         | 缓存优先级及相关说明
[动静自动分离](https://docs.upyun.com/cdn/config/#36)         | CDN 如何判断动态资源和静态资源

**访问控制**

配置项                        | 相关说明
:--------------------       | :-------
[IP 黑白名单](https://docs.upyun.com/cdn/config/#41-ip)        | 禁止或允许最终用户 IP 访问
[地区访问限制](https://docs.upyun.com/cdn/config/#42)                  | 根据地区维度来限制最终用户访问，支持白名单和黑名单
[Referer 防盗链](https://docs.upyun.com/cdn/config/#43-referer)         | Referer 防盗链配置，支持白名单和黑名单
[User-Agent 防盗链](https://docs.upyun.com/cdn/config/#44-user-agent)         | User-Agent 防盗链配置，支持白名单和黑名单
[Token 防盗链](https://docs.upyun.com/cdn/config/#45-token)         | Token 防盗链配置引导，也即时间戳防盗链
[回源鉴权](https://docs.upyun.com/cdn/config/#46)         | 回源鉴权功能说明及配置引导，属于高级防盗链模式
[外链功能](https://docs.upyun.com/cdn/config/#47)         | 禁止用户访问，支持服务维度

**安全防护**

配置项                        | 相关说明
:--------------------       | :-------
[WAF 防护](https://docs.upyun.com/cdn/config/#51-waf)        | WEB 应用防火墙功能配置及说明
[CC 防护](https://docs.upyun.com/cdn/config/#52-cc)                  | CC 防护配置，可防护部分 DDoS 攻击
[HTTP 请求大小限制](https://docs.upyun.com/cdn/config/#53-http)         | HTTP 请求大小限制说明，可限制部分非常请求
[IP 访问限制](https://docs.upyun.com/cdn/config/#54-ip)         | IP 访问频率限制，可限制部分应用层攻击


**性能优化**

配置项                        | 相关说明
:--------------------       | :-------
[GZIP压缩](https://docs.upyun.com/cdn/config/#61-gzip)        |智能压缩功能说明，默认开启，无需配置
[代码压缩](https://docs.upyun.com/cdn/config/#62)                  | 代码压缩功能说明及配置，可减少资源文件大小
[参数跟随](https://docs.upyun.com/cdn/config/#63)         | 可配置忽略参数缓存或保留参数缓存
[重定向优化](https://docs.upyun.com/cdn/config/#64)         | 回源 302 跟随配置，可有效减少网络消耗
[文件合并](https://docs.upyun.com/cdn/config/#65)         | 可将多个 JS 和 CSS 文件合并，可有效减少网络消耗


**分段缓存**

配置项                        | 相关说明
:--------------------       | :-------
[配置引导](https://docs.upyun.com/cdn/config/#71)        | 分段缓存功能配置引导及说明
[配置效果](https://docs.upyun.com/cdn/config/#72)                  | 分段缓存在开启和未开启状态下的说明
[注意事项](https://docs.upyun.com/cdn/config/#73)         | 分段缓存特性配置的注意事项及说明


**视频拖拉**

配置项                        | 相关说明
:--------------------       | :-------
[配置引导](https://docs.upyun.com/cdn/config/#81)        | 功能配置引导及说明
[注意事项](https://docs.upyun.com/cdn/config/#82)         | 视频拖拉功能满足的条件及注意事项

**镜像存储**

配置项                        | 相关说明
:--------------------       | :-------
[实现流程](https://docs.upyun.com/cdn/config/#91)        | 镜像存储功能实现原理及说明
[配置引导](https://docs.upyun.com/cdn/config/#92)        | 镜像存储功能配置引导及说明
[注意事项](https://docs.upyun.com/cdn/config/#93)         | 镜像存储功能注意事项及说明


**跨域配置**

配置项                        | 相关说明
:--------------------       | :-------
[配置引导](https://docs.upyun.com/cdn/config/#101)        | CORS 跨域功能配置引导及说明
[注意事项](https://docs.upyun.com/cdn/config/#102)         | CORS 跨域功注意事项及说明

**HTTPS配置**

配置项                        | 相关说明
:--------------------       | :-------
[配置引导](https://docs.upyun.com/cdn/config/#111)        | HTTPS 配置入口引导
[自有证书添加](https://docs.upyun.com/cdn/config/#112)         | 添加客户自有证书并部署
[SSL证书管理](https://docs.upyun.com/cdn/config/#113-ssl)         | SSL 证书的切换，HTTPS 开启、强制 HTTPS 配置
[HTTP/2 介绍](https://docs.upyun.com/cdn/config/#114-http2)         | HTTP/2 协议介绍、支持情况以及说明
[注意事项](https://docs.upyun.com/cdn/config/#115)         | HTTPS 配置的相关说明及注意事项

**其他配置**

配置项                        | 相关说明
:--------------------       | :-------
[文件另存为](https://docs.upyun.com/cdn/config/#121)        | 通过特定参数来设置文件另存为响应头
[传递最终用户 IP](https://docs.upyun.com/cdn/config/#122-ip)         | 传递最终用户 IP 到源站，各种规则说明
[自定义提示图](https://docs.upyun.com/cdn/config/#123)         | 针对 403、404、405 状态码响应自定义提示图

##2. 回源配置

####2.1 回源HOST

该配置是指 CDN 节点回源过程中所需访问的源站服务器 WEB 站点资源的访问域名。CDN 节点回源时会在 HTTP 请求头 HOST 字段里替换该域名，以支持源服务器可通过此 HOST 做的特殊处理逻辑，一般是域名形式的字符串，当然也支持任意字符串形式。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「基础配置」页面，找到「回源配置」配置项，点击管理按钮进入管理页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-host.png" height="470" width="800" />

如上截图所示，回源 HOST 配置为：`src.image.upyun.com`

1.回源 HOST 为可选配置项，默认为空。特别地，如果该自定义值为空，子域名的回源 HOST 为所配置的加速域名，泛域名回源 HOST 为访问域名；

2.自定义配置：您可以根据业务情况配置回源 HOST，满足特定业务需求；

**注意事项**

1.回源 HOST 配置目前仅支持使用自有源接入的 CDN 服务；

2.配置自定义回源 HOST 之后，请保证该域名可以正常访问，否则会出现回源失败的情况；

3.回源地址和回源 HOST 的区别：回源地址是 CDN 节点回源时能够找到对应的源站服务器，源站可能有多个 WEB 站点，回源 HOST 则指明了资源所在的站点。

####2.2 回源协议

回源协议顾名思义也就是 CDN 节点回源使用的协议。默认情况下，我们都会以 HTTP 协议回源，您也可以采用 HTTPS 协议回源。另外一个特别的选项是协议跟随，当启用该特性时，回源协议始终会和客户端访问 CDN 的协议保持一致，即客户端用 HTTP 协议访问我们 CDN，那么回源时我们也会用 HTTP 协议进行回源，同样，若采用 HTTPS 访问，那么回源也是 HTTPS。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「基础配置」页面，找到「回源配置」配置项，点击管理按钮进入管理页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-xieyi.png" height="470" width="800" />

特别地，当我们选择 HTTP 协议回源时，回源地址的端口号默认为 80，而选择 HTTPS 协议回源时，端口号默认为 443。当然，你也可以手动指定特殊的端口号，例如 8080 或 8443。

**注意事项**

1.特别地，回源地址的端口号配置将仅作用于回源协议为 HTTP 的情况，默认 80，当然你也可以自定义为其他值；

2.而回源协议为 HTTPS 时，端口号则始终固定为 443，且目前暂时不支持修改此固定值；

3.若之前选择 HTTPS 协议回源 且自定义设置过端口号的用户需要注意，切换到 `协议跟随` 模式下时，自定义的端口号将仅作用于回源协议为 HTTP 的情况。


####2.3 线路配置

线路配置包括回源地址、端口号、主备线路、轮询权重、最大失败次数、静默时间等高级设置。其中回源地址则表示源站实际可访问的网络地址，可填 IP 地址或域名，如果是域名地址，那么 CDN 在回源时会对该域名地址进行 DNS 解析，然后针对解析出来的 IP 地址进行访问，因此若解析失败将会导致回源异常。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「基础配置」页面，找到「回源配置」配置项，点击管理按钮进入管理页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-line-config.png" height="470" width="800" />

以下将重点介绍主备线路、轮询权重、最大失败次数和静默时间：

 - 主、备线路
 
回源地址配置中除了默认的 `主线路` 外，我们还支持 `备份线路`  的配置。默认情况下， `主线路` 多个回源地址会以 RR 轮询方式进行调度，当且仅当所有 `主线路` 回源地址均不可达时，此时我们将使用 `备份线路` 进行转发，若 `备份线路` 有多个回源地址，那么负载均衡方式也和 `主线路` 一样，以 Round-Robin 轮询（详情请点击[链接](https://en.wikipedia.org/wiki/Round-robin_scheduling)）方式进行调度。

 - 轮询权重

默认情况下，轮询权重都为 1，表示所有回源地址轮询到的概率均等。当然也可以如上图那样自定义轮询权重，我们看到主线路两个回源地址权重分别是 1 和 2，此时主线路集群在轮询调度中会有 1/3 的概率回 `192.168.11.1:80` 这个地址，而回 `192.168.11.2:80` 这个地址的概率则为 2/3。即当前回源地址轮询到的概率可按（当前节点权重/集群所有节点权重之和）这个公式计算。

 - 最大失败次数和静默时间

表示在一个 `静默时间` 内如果对应源站的异常次数累计达到当前设置的 `最大失败次数` ，那么在下一个  `静默时间` 内，CDN 就会认为这个源站宕机了，即在这段时间内不会再将请求转发给它。特别地，当 `最大失败次数` 其值为 0 时，表示不会进行失败累计，`静默时间` 也同时失效。

合理设置最大失败次数和静默时间，有助于在源站异常的情况下最大程度减少对线上请求的影响。这里实际上跟 Nginx 核心的 upstream 模块实现机制类似，进一步了解可参考 [链接](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server) 。

 
##3. 缓存配置

缓存配置是指又拍云 CDN 加速节点（包括边缘节点和中心节点）在缓存您的资源文件时遵循的一套过期规则。当资源文件处于过期状态时，此时用户请求会由节点发送至源站，重新获取资源内容并缓存至节点，同时返回给最终用户；当资源内容处于未过期状态时，您的用户请求到达节点后，会由节点直接响应最终用户的请求。合理的配置资源文件缓存时间，能够有效的提升缓存命中率，降低回源率，节省您的源站带宽。

####3.1 配置引导

登录 [CDN控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入：服务 > 功能配置 > 基础配置 > 缓存配置，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-management.png" height="470" width="800" />

####3.2 自定义缓存配置

您可以根据自身业务需求，在配置界面的右上角，点击右上角的 `添加` 按钮进行资源文件缓存过期时间的配置，CDN 支持三种维度的缓存时间配置规则：

**根据文件类型设置缓存策略**

您可以根据模板填充文件类型后缀，根据文件类型来设置缓存时间，如下示例所示：

> /*.(txt,doc,wri,docs)   缓存时间 1 天；

设置完成后，域名下所有匹配 `.txt .doc .wri .docs` 的文本资源，在节点的缓存有效时间均为 1 天。

**根据文件目录设置缓存策略**

您可以自定义配置文件路径，根据文件路径来设置缓存时间，如下示例所示：

> /dir/*   缓存时间 12 小时

**全局缓存过期时间配置**

全局缓存策略适合源站全是静态资源的客户，可以针对该服务一键开启配置缓存过期时间，开启请谨慎。详细配置可参照如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-global.png" height="490" width="800" />

设置完成后，该服务下所有域名匹配的资源，在节点的缓存有效时间均为 7 天。

注意事项：

1.全局缓存在自定义缓存配置里面的优先级是最低的，且优先级不可调节，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-priority.png" height="490" width="800" />

2.全局缓存设置有一个单独的开关，需要开启之后，才可以进行配置。

**默认配置模板**

特别地，我们针对自定义缓存配置，默认规划了模板，并且预设了缓存时间，您可以根据具体情况进行修改。具体模板如下：

    首页：/  缓存 12 小时；
    目录页：/*/    缓存 12 小时；
    图片文件：/*.(jpg,jpeg,png,bmp,gif,psd,ico,tga,imb,tiff)      缓存 7 天；
    文本文件：/*.(txt,doc,wri,docs,css,js,dot,xml,log,bat,csv)    缓存 1 天；
    音视频文件：/*.(wav,mp3,wmv,rmi,aac,mp4,rmvb,mkv,avi,flv,swf,rm,mov,movie,qt)  缓存  7 天；
    下载类文件：/*.(exe,ios,apk,ipa,pxl,sis,cab,deb,rar,zip,gzip,tar,7z,bzip2,dmg,gz,wim,tbz,tpz,z,jar)      缓存 7 天；

####3.3 不缓存配置

根据业务情况，可以针对一些动态资源文件（ 例如：php、jsp、asp、do、cgi、action、json 等）强制配置不缓存， 详细配置可参照如下所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-no-cache.png" height="490" width="800" />


> /*.(asp,php,jsp,do,json)     配置不缓存

####3.4 默认缓存策略

在没有匹配到自定义缓存规则且源站也没有返回任何有效缓存头的情况下，我们的默认配置策略如下：

1.针对静态资源，所有正常状态码（大于等于 200 小于 400）均缓存 8  天。特别地，301 响应缓存 2 小时，302 响应缓存 20 分钟

2.针对动态资源，程序会自动识别，则不进行缓存

3.对于其他大于等于 400 的不正常响应，则不进行缓存
 
####3.5 缓存优先级

请根据具体缓存优先级情况合理配置缓存策略，又拍云 CDN 加速平台目前的缓存优先级如下：

> 不缓存配置（后台设置） > 自定义缓存配置（后台配置） > 源站缓存配置 > 默认缓存策略。

注意事项：

1.源站缓存配置，指 Cache-Control 和 Expires 请求头的设置

2.以上缓存配置优先级目前不支持顺序调节，后续我们会考虑支持

**特别声明**

针对使用又拍云存储服务的客户，我们暂时不支持缓存配置的自定义设置，我们默认给出了一个默认的缓存配置规则，具体如下：

> 对于正常的状态码（大于等于 200 小于 400）：8 天

> 对于其他大于等于 400 的不正常响应缓存 5 分钟


####3.6 动静自动分离

针对使用 `自主源` 的 CDN 服务而言，当没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头时，我们就会对回源内容进行动静内容识别。特别地，我们会根据响应头中的 ` Content-Type` 字段和 URL 中的文件后缀名来判断是否是静态内容，其中 `Content-Type` 匹配优先级高于文件后缀名。

**参考列表**

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

##4. 访问控制

####4.1 IP 黑白名单

若需要禁止某些 IP 或只允许某些 IP 访问，则可以使用 IP 黑白名单功能；

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > IP 黑白名单，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-ip-control.png" height="470" width="800" />


**注意事项**

1.支持 `*` 通配符，如 `10.11.12.*` ，将禁止或允许 `10.11.12.0 ~ 10.11.12.255` 的 IP 访问；

2.IP 黑白名单上限 100 条，被禁止的 IP 访问资源时，将提示 403；

3.IP 黑名单和 IP 白名单不能够同时配置，特别地，配置完白名单之后，其他都为 IP 黑名单，请谨慎操作。

####4.2 地区访问限制

地区访问限制是指根据加速网站的需求，允许或禁止特定区域的终端用户对网站资源的访问。即网站指定地区，允许该地区终端用户访问，而限制其他地区用户访问，或者相反。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 地区访问限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-region-limit.png" height="470" width="800" />

详细配置请参见 [地区访问限制用户指南](https://blog.upyun.com/?p=886)。

**注意事项**

1.地区访问限制配置粒度，中国地区支持省份、运营商（包括：电信、铁通、移动、联通、鹏博士、教育网、科技网），海外地区支持到运营商；

2.地区白名单和黑名单不能同时配置，特别地，配置完白名单后，其他地区都为黑名单，请谨慎操作。

####4.3 Referer 防盗链

当请求发送到 CDN 节点后，CDN 节点检查 HTTP 请求头中的 Referer 字段的信息，禁止或者允许符合特定规则的 Referer 的请求。

**配置引导**

登陆 [ CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 域名防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-acces-referer.png" height="470" width="800" />

**注意事项**

1.白名单：仅允许名单中的域名网站访问文件，其他域名网站都不允许访问

2.黑名单：仅禁止名单中的域名网站访问文件，其他域名网站都允许访问

3.支持 `*` 通配符，比如白名单的 `*upyun.com` 将允许 `www.upyun.com`、`abcupyun.com` 等网站访问

4.特别地，默认 `允许 Referer 为空` 这种情况会绕过防盗链的逻辑，若这里启用 `禁止 Referer 为空`，那么这类请求就直接被禁止访问了。

####4.4 User-Agent 防盗链

允许或禁用特定的浏览器或者带有特殊 `User-Agent` 标识的专属的客户端。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 客户端防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-user-agent.png" height="470" width="800" />

**注意事项**

1.添加数量上限 50 条

2.`User-Agent` 不区分大小写，且支持 `*` 通配符；

3.`User-Agent` 防盗链的优先级高于 Referer 防盗链。


####4.5 Token 防盗链

可设置签名过期时间来控制文件的访问时限。Token 防盗链的目的是使得每个请求的 URL 都具有一定的“时效性”，因此 URL 本身需要携带过期时间相关的信息，同时还需要确保这个过期时间，不能被恶意修改，因此采用 md5 算法，将密文（通常是一个普通字符串）、过期时间、文件路径（过期时间和文件路径通常是有对应关系的，因此也参与 md5 的计算）等信息所计算的 md5 值也加入到 URL 中，CDN 节点在验证请求时，除了验证过期时间，同时还会验证该 md5 值是否匹配，对于不匹配的 md5，说明 URL 被篡改，即使请求未过期也需要禁止服务。

**实现原理**

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-token.png" height="470" width="700" />


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > Token 防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-token-miyao.png" height="470" width="800" />


> 签名方式

签名：

    _upt = MD5(token 密钥 & etime & URI){中间 8 位} + etime

参数：

 - token 密钥：用户所填的密钥
 - etime：过期时间，必须是 UNIX TIME 格式，如： 1378092990
 - URI：请求地址（不包含?及后面的 Query String），如： /dir/pic.jpg

> 示例

    设置图片链接 http://<bucket>/dir/pic.jpg  10 分钟有效
    当前 Unix 时间 = 1370000000
    etime = 1370000000 + 600 = 1370000600
    URI = '/dir/pic.jpg'
    sign = MD5(token 密钥&etime&URI) = xxxxxxxxxxxxabcdefghyyyyyyyyyyyy
    _upt = MD5(token 密钥&etime&URI){中间 8 位} + etime = abcdefgh1370000600

    该签名拼接在 URL 地址或用户 Cookie 中，均可起到防盗链作用:
    URL: http://<bucket>/dir/pic.jpg?_upt=abcdefgh1370000600
    Cookie: _upt=abcdefgh1370000600;

>代码实例 (PHP)

    <?php
    $etime = time() + 600; // 授权十分钟后过期
    $key = 'token 密钥';   // token 防盗链密钥
    $path = '/dir/pic.jpg'; // 图片相对路径
    $sign = substr(md5($key.'&'.$etime.'&'.$path), 12,8).$etime;
    ?>

####4.6 回源鉴权

回源鉴权是一种高级的防盗链方式，适用于对防盗链有很高实时性要求的场景。大概原理是 CDN 边缘节点每次接受到请求之后，都需要回客户源站进行验证，验证通过之后才认为是合法请求，这样 CDN 可以继续提供服务。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 回源鉴权，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-back-to-source-auth.png" height="470" width="800" />

详细配置请参见 [回源鉴权用户指南](https://blog.upyun.com/?p=877)。

####4.7 外链功能

通过管理后台，可以一键禁止整个服务域名下所有资源的访问，特别地，此时会响应 405 状态码给客户端。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 基础配置 > 外链功能，关闭外链功能。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-wailian.png" height="470" width="800" />


**注意事项**

1.服务创建完毕之后，外链功能默认开启；

2.关闭外链功能，会导致资源无法访问，请谨慎操作.

----------

##5. 安全防护

####5.1 WAF 防护

WAF 防护主要防护的是来自对网站源站的动态数据攻击，可防护的攻击类型包括 SQL 注入、XSS 攻击、CSRF 攻击、恶意爬虫、扫描器、远程文件包含等攻击。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > WAF 防护，开启 WAF 保护即可。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-waf.png" height="470" width="800" />


**注意事项**

1.适用于动态资源加速，全静态资源加速建议不启用该功能；

####5.2 CC 防护

CC 防护主要是针对网络安全领域中的 CC 攻击而进行的一种应用层攻击防护，该功能通过自定义匹配规则对目标资源进行监控，当请求频率达到触发频率时对疑似攻击请求进行校验，校验通过则允许访问；校验不通过，则直接禁止访问。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > CC 防护，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-cc.png" height="470" width="800" />


具体配置可以走如下四个步骤：

 - 第一步：添加防护策略，设置 URI 匹配规则，例如：/image/*.jpg
 - 第二步：填写访问频率阈值，例如：500次/分钟，在智能防护模式下才有效
 - 第三步：设置 IP 白名单，如果要绕过 CC 防护策略，此处可进行设置
 - 第四步：选择 CC 防护模式，包括强制防护、智能防护、暂停防护，当正在遭受攻击时，建议选择强制防护模式

**注意事项**

1.CC 防护分为强制防护和智能防护两种模式，在智能防护模式下，则会匹配 URI 规则，请求一旦达到访问频率阈值，则会触发 JavaScript 校验，校验通过则可正常访问，校验不通过则直接拒绝。在强制防护模式下，则会忽略访问频率阈值，直接触发 JavaScript 校验，以此类推;

2.在智能防护模式下，访问阈值设置得过低，可能不会触发 JavaScript 校验，建议阈值设置高一些。

####5.3 HTTP 请求大小限制

可限制单次请求体的内容大小，能有效保证请求的安全性，默认是 512M 。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > HTTP 请求大小限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-request-limit.png" height="470" width="800" />


####5.4 IP 访问限制

可根据单个 IP 的访问频率来进行防御，在正常的情况下，用户访问网站是有一定频率的，可通过匹配自定义的限制规则对单个 IP 进行监控，将超过设定阈值的 IP 的访问进行拦截或者延迟响应，从而达到安全防护效果。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > IP 访问限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-ip-limit.png" height="470" width="800" />


配置步骤如下：

第一步：填写访问的资源路径规则，支持 `＊` 通配符，如：`/a/*.mp4`、`/b/*.flv`

第二步：选择限制方式：`直接禁止`或`延迟响应`

第三步：设置访问频率及相应的时间限制

**注意事项**

1.直接禁止、延时响应都是以 60s 为一个计数周期，即上一个 60s 的计数不会累计叠加到下一个 60s 内;

2.禁止访问时间的设置需大于等于 60s，延迟响应时间的设置需小于等于 30s;

3.请勿将访问频率值设置过低，以免影响正常用户的访问请求。

详细配置请参见 [IP 访问限制用户指南](https://blog.upyun.com/?p=896)。

----------

##6. 性能优化

####6.1 GZIP 压缩

又拍云 CDN 默认开启了 GZIP 压缩的相关特性，具体对应的 NGINX 配置如下：

```
gzip on;
gzip_min_length 256;
gzip_types <见下面的列表>;
gzip_disable "MSIE [1-6]\.";
gzip_vary on;
```

触发实际的 GZIP 压缩行为需要同时满足如下条件：

1、Content-Type 满足以下列表其中之一：

```
text/plain
text/javascript
text/css
text/xml
text/x-component
application/javascript
application/x-javascript
application/xml
application/json
application/xhtml+xml
application/rss+xml
application/atom+xml
application/x-font-ttf
application/vnd.ms-fontobject
image/svg+xml
image/x-icon
font/opentype

text/html -- default
```
2、Content-Length 大于 256 字节；
3、客户端请求头带了 `Accept-Encoding: gzip`。

####6.2 代码压缩

开启后，会自动去除 HTML 文件中非必要的字符（空白、注释等），自动压缩文件大小，提升访问速度。特别地，目前仅支持 HTML 页面的优化。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 代码压缩，开启开关即可，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-performance-daimayasuo.png" height="470" width="800" />


####6.3 参数跟随

参数跟随功能包括三种模式，分别是 `参数不跟随` 、`全程跟随` 、`回源跟随` ，系统默认为 `全程跟随` 模式。一般情况下，根据业务情况，建议选择 `参数不跟随` 来提高资源文件缓存命中率。详细介绍如下：

**参数不跟随**

> 开启后，系统会过滤请求中的查询字符串，并且查询字符串不会传递给源站

**全程跟随**
> 开启后，将不再过滤请求中的查询字符串，并将查询字符串传递给源站

**回源跟随**

> 开启后，会过滤请求中的查询字符串，在访问回源阶段会将查询字符串传递给源站


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 基础配置 > 参数跟随，点击管理即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-performance-param-management.png" height="470" width="800" />

注意：参数跟随功能，可以配合缓存配置功能来使用。


####6.4 重定向优化

当 CDN 节点回源站时，若源站响应的状态码为 301/302，CDN 节点对重定向之后的目标 URL （也即 301/302 响应头 `Location` 字段对应的信息）发起请求，将获取后的内容响应给最终用户，并在 CDN 节点进行缓存。这样向最终用户屏蔽了重定向过程，免去了最终用户再次向重定向后的 URL 重新发起请求的连接时间，从而加快了访问速度。

**原理介绍**

一般情况下，当 CDN 节点回源站请求文件时，若源站返回的是 HTTP 301/302 的响应，CDN 节点会将该响应返回给客户端，客户端根据响应头中的 `Location` 头信息进行再次请求。配置了重定向优化功能之后，访问流程如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-302redirect-yuanli.png" height="300" width="700" />

1）客户端向 CDN 节点发起请求并传递至源站，例如：`http://example.com/123.html`；

2）源站给 CDN 节点响应 301/302 状态码，并指明重定向后的 URL 为 `http://example.com/456.html`

3）CDN 节点对重定向之后对目标 URL 发起请求；

4）源站响应内容给最终用户，并将内容在 CDN 节点进行缓存；

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 重定向优化，滑动开关即可开启该功能。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-redirect-performance.png" height="470" width="800" />


####6.5 文件合并

 `又拍云存储 ` 和  `自主源站 ` CDN 加速服务均支持 JS，CSS Combo（组合特性），即可以把多个 JS, CSS 请求合并成一个。
 

**如何使用**

> 特别地，我们以 `!!` 作为分隔符。

    http://upyun-assets.b0.upaiyun.com/foo/!!a.js,b.js

一个引用了如上 URL 的 Demo:

    http://upyun-assets.b0.upaiyun.com/jscombo.html
    
####6.6 WebP 自适应

WebP 自适应方案是 CDN 平台智能判断客户端浏览器是否支持 WebP 解码，如支持则返回 WebP 格式图片，否则返回原图，客户端以及源站无需任何改动。

**如何实现**

WebP 自适应大致的处理流程如下：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-auto_webp_theory.png"  />

1.CDN 如何判断客户端是否支持 WebP ?

该部分目前是通过 HTTP  Accept 头来判断的，如果支持，则返回 WebP 副本并进行缓存；如果不支持，则返回原图。

2.CDN 如何实现实时图片格式转换？
 
针对用户源站并非 WebP 格式图片的时候，CDN 层需要支持将原图图片的实时转换为 WebP 格式副本，这个在 CDN 层面是无缝支持的。流程是这样的：

 - 客户端浏览器请求一个图片资源，例如：http://webp.example.com/test.png;
 - CDN 通过 Accept 头已经判断客户端浏览器支持 WebP 格式的图片；
 - CDN 回用户源站取回原图并将原图实时转为 WebP 格式的图片，并响应给客户端浏览器。

这里值得强调的是，又拍云 CDN 已经无缝兼容了各种作图场景和访问方式，具体包括：

 - 原图访问 ，示例：/a.jpg
 - 自定义版本号作图，示例：/a.jpg!123
 - URL 作图 ，示例：/a.jpg!/format/webp
 - 自定义版本号 + URL 作图：/a.jpg!123/format/webp

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > WebP 自适应，开启即可。如下图所示：：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-auto_webp.png" height="490" width="800" />


**注意事项**

 - CDN 平台会根据请求头 Accept: image/webp 来智能判断客户端是否支持 WebP 解码，不支持的客户端，会默认返回原始图片；
 - 已经使用了又拍云自定义版本作图、URL 作图、自定义版本作图＋URL 作图的请求也无缝支持该特性；
 - 在 CDN 节点未缓存 WebP 副本的情况下，CDN 平台会实时生成 WebP 副本并返回。


更多了解，请参照：[如何通过 WebP 自适应方案减少图片资源大小](https://blog.upyun.com/?p=1539)
    
----------

##7. 分段缓存

又拍云 CDN 服务可提供分段缓存功能配置，开启该功能可有效降低大文件回源率，提升响应速度，与此同时，可以提高文件在 CDN 节点的缓存命中率。特别地，开启该特性之后，CDN 节点会以 Range 请求回源。

####7.1 配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级服务 > 分段缓存，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-slice-cache.png" height="470" width="800" />

默认配置：该特性会默认关闭，您可以有选择性的开启该特性。特别地，我们针对分段缓存预置了文件后缀模板，也即可以根据文件后缀或者通配符规则来配置是否开启分段缓存功能。默认预置后缀如下：
```
视频：AVI、MP4、FLV、MOV、3GP、ASF、WMV、MPG、F4V、M4V、MKV、VOB、M3U8、TS 、RMVB、DAT、RM
音频：MP3、OGG、M4A、WMA、AIF、AU
安装包：APK、ZIP、EXE、RAR、IPA、PXL、DEB、SIS、SISX、JAR、XAP
游戏下载：SO、CUE、CCD、MDS、IMG、7Z
其它：CAB、DHP、GZ、ISO、MPQ、PBCV、PXL、QNP、R00、XY、XY2
```

####7.2 配置效果

假设最终用户发起了资源请求，请求的 URL 为：`http://www.example.com/download/game.zip`，CDN 节点收到请求后，若命中则响应对应分段文件给最终用户，未命中的分段 CDN 节点则回源发起 Range 请求获取资源文件。

**开启分段缓存**

 - 若最终用户发起资源请求，CDN 节点上已经命中该分段，则直接响应给最终用户
 - 若 CDN 节点未命中缓存，则 CDN 节点回源使用 Range 请求，分段获取资源文件

**关闭分段缓存**

 - 节点文件缓存过期之后，会向源站获取整个资源文件

**分段缓存预加载**

 - 开启分段缓存功能之后，该特性会默认开启。当最终用户请求第一个分段文件时，我们会提前去下载后面几个分段文件，进而提高文件下载速度

####7.3 注意事项

1.源站需要支持 Range 请求，否则会导致回源失败；严格来说，我们只通过 `Accept-Ranges: bytes` 这个头来判断源支不支持 Range 请求 ，否则即使开启了分段缓存特性，也不会生效；

2.该功能配置后不会立即生效，需在文件缓存过期或者文件缓存被主动刷新之后方可生效；

3.该功能仅针对静态文件，动态内容开启分段缓存会可能会发生错误，请谨慎使用；

4.该功能开启后，资源在 CDN 节点上会进行分段缓存，如果要立马关闭该特性，可通过手动刷新文件来解决；

5.如果您已经使用又拍云对象存储服务，该特性已经默认开启，无需做任何配置；

----------

##8. 视频拖拉

视频拖拉功能用于视频点播的增强体验，方便最终用户通过拖拉播放器的进度条，观看任意起始位置的视频，而无需从头观看。该功能还可以扩展为跳过片头、续播、预播放等功能。发送拖拉播放进度时，客户端发起的请求类似 `http://video.upyun.com/test.mp4?start=100` 这样的 URL 请求。

####8.1 配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 视频拖拉，点击管理即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-video-drag.png" height="470" width="800" />

详细配置请参见 [「支持从任意起点观看」视频拖拉用户指南](https://blog.upyun.com/?p=873)

####8.2 注意事项

1.为了保证正常拖拉，播放器可以发起约定格式的请求，例如：`http://example.com/video/test.mp4?start=xxx&end=xxx`；

2.视频文件需含有关键帧，这是实现拖拉的前提条件，关键帧太少或者没有关键帧，视频文件需要重新编码；

3.视频文件 `metedata` 中缺少关键帧的索引信息，如 FLV 缺少 `keyframes` 信息，MP4 缺少 `seekpoint` 信息，或缺失 `moov box`，都会导致拖拉失败；

4.支持根据不同资源路径添加配置，最多仅支持 20 条；

5.目前支持文件格式有：MP4 和 FLV，MP4 支持按时间拖拉，FLV 支持按字节拖拉。


----------

##9. 镜像存储

使用又拍云 CDN 服务时，源站类型选择的是 `自主源`  模式（也即客户的网站资源都托管在自己的服务器或者第三方存储平台上）的话，可以开启镜像存储功能，可以将网站数据平滑迁移至又拍云存储平台，并减小网站回源压力。

####9.1 实现流程

实现机制可以参照如下流程：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-storage.png" height="300" width="500" />


 - 终端用户访问资源；
 - 又拍云 CDN 节点从用户自主源站获取资源；
 - 返回资源给终端，并持久化存储到又拍云存储平台；
 - 终端用户再次访问时，将直接从又拍云获取资源，不再回源获取；
 - 随着终端用户的不断访问，源站的资源逐步自动存储到又拍云存储平台。

####9.2 配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 镜像存储，点击开启即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor-storage-management.png" height="470" width="800" />

又拍云镜像存储功能包括三种状态，分别是开启、暂停、关闭，详细描述如下：

**开启状态**

开启之后，当 CDN 节点缓存失效时，会优先回又拍云存储获取资源，如果又拍云存储没有命中资源，则直接回自有源站获取资源并响应给最终用户，与此同时，资源会被扔进镜像存储队列并进行存储，下一个相同的资源请求过来时则会直接由存储命中的资源直接提供服务。

**暂停状态**

直接关闭镜像存储功能，可能会导致回源压力过大，我们会先进入暂停状态，该状态下不会进行资源迁移动作，访问资源时，仍然先访问又拍云存储，如果存储上没有命中资源，则会访问自主源站。

**关闭状态**

该功能关闭之后，新的请求过来的话，CDN 节点不会回又拍云存储平台获取资源，则会直接回客户源站获取资源。

####9.3 注意事项

1.当用户更新了源站资源时，为了保证请求访问到的资源是最新的，此时需要通过 API 或 FTP 的方式对已经存储在又拍云存储上的文件进行删除和替换操作

2.当镜像存储功能处于暂停状态时，您可以通过 API 接口逐步删除又拍云上对应的文件，将回源流量平滑迁移至源站，或调整源站带宽，最后再执行关闭操作


----------

##10. 跨域配置
跨域资源共享 （ Cross-Origin Resource Sharing）是一种网络浏览器的技术规范，他为  Web 服务器定义了一种方式，允许网页从不同的域访问其资源。跨源资源共享标准通过新增一系列 HTTP 头，让服务器能声明那些来源可以通过浏览器访问该服务器上的资源。此时需要在 Response Header 中增加跨域相关配置，这样就可以使得资源的安全访问成为可能。

####10.1 配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > CORS 跨域共享，点击管理即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cors-config.png" height="470" width="800" />

**Access-Control-Allow-Origin**

返回的资源需要有一个 `Access-Control-Allow-Origin` 头信息，也可以设置通配符＊，允许被所有域使用。语法如下:
>  Access-Control-Allow-Origin: <origin> | *

举个栗子，允许来自 `http://www.upyun.com` 的请求，你可以这样指定:
>  Access-Control-Allow-Origin: http://www.upyun.com

**Access-Control-Allow-Methods**

表示允许的跨域请求的方法，在当前请求的域被允许后，还要检查当前请求的方法是否被允许，示例如下：
> Access-Control-Allow-Methods: POST, GET, OPTIONS

**Access-Control-Allow-Headers**

也是在响应预检请求的时候使用，用来指明在实际的请求中，可以使用哪些自定义 HTTP 请求头，比如：
> Access-Control-Allow-Headers: X-PINGOTHER，X-UPYUN

这样在实际的请求里,请求头信息里就可以有这么一条：

> X-PINGOTHER: hello

**Access-Control-Expose-Headers**

设置浏览器允许访问的服务器的头信息的白名单，示例如下：

> Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header

这样，`X-My-Custom-Header` 和 `X-Another-Custom-Header` 这两个头信息，都可以被浏览器得到。

**Access-Control-Max-Age**

这个头告诉我们这次预请求的结果的有效期是多久，单位为秒，设置示例如下：
> Access-Control-Max-Age: 864000

表明在 86400s（10天）内，对该资源的跨域访问不再发送另外一条预请求。

####10.2 注意事项

1.最多支持添加 10 条规则，匹配时从第一条开始逐条匹配，以最早匹配上的规则为准

2.`允许的域`、`Allow Header` 、`Expose Header` 最多允许 20 行

3.`允许的域` 及 ` Methods ` 建议根据需要使用最小的配置来保证安全性，多条规则间不要有覆盖和冲突

4.`允许的域` 需带上完整的域信息，即指定 https 或者 http，或者 `*://foo.com` 这样的形式

5.`Allow Header`、`Expose Header` 不允许使用通配符，大小写不敏感

6.`缓存时间` 如果没有特殊情况可以酌情设置得大一点，默认为 86400 秒，最大为 604800 秒

----------

##11. HTTPS配置

又拍云 HTTPS 加速服务建立在自主研发的内容分发网络基础之上，全面支持了 HTTPS 协议加速。通过会话重用及会话保持、在线证书状态检查、协议优化、SSL 协议握手优化、HTTP/2 等技术手段，全面提升了 HTTPS 协议加速性能。

####11.1 配置引导

第一步：依次进入 服务 > 功能配置 > HTTPS, 点击 `管理` 即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https1.png" height="470" width="800" />

第二步：可针对单个域名选择相应的证书，然后保存。如果无证书可选，可前往[ SSL 证书服务](https://console.upyun.com/toolbox/ssl/)平台添加域名的自有证书；用户也可直接在[ SSL 证书申购](https://console.upyun.com/toolbox/createCertificate/)平台申购 Symantec、GeoTrust、TrustAsia、Let’s Encrypt 的各类 SSL 证书，其中 TrustAsia、Let’s Encrypt 的 DV SSL 单域名证书可免费申请。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https2.png" height="470" width="800" />

第三步：可选择是否开启 HTTPS 访问和强制 HTTPS 功能。在开启 HTTPS 访问后，可通过点击 `修改` 切换域名所对应的证书，点击 `详情` 可查看相应证书的详细信息；

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https3.png" height="470" width="800" />

“ HTTPS 访问”：开启该功能后，客户端才能使用 HTTPS 协议进行访问，默认关闭。

“强制 HTTPS ”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

备注：其中 `HTTPS 配置` 也可以在 `SSL 证书管理`里面配置，参见下文`SSL 证书管理`部分。

####11.2 自有证书添加

如果域名当前已有证书，又拍云提供了自有证书的上传方式，无需人工协助，安全便捷。

第一步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 证书管理 -> 添加自有证书 

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl8.png" height="470" width="800" />

第二步：粘贴证书内容:

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl9.png" height="470" width="800" />

点击 `保存` 之后，可查看 SSL 证书信息如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl10.png" height="470" width="800" />

####11.3 SSL 证书管理

在[证书管理](https://console.upyun.com/toolbox/ssl/)列表中，可根据需求选择申购成功并已部署于 CDN 平台的证书或已上传的自有证书，点击 `HTTPS 配置`进行操作 ，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl11.png" height="470" width="800" />

“HTTPS 访问”：开启该功能后，客户端使用 HTTPS 协议访问时才能成功，默认关闭。

“强制 HTTPS”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

####11.4 HTTP/2 介绍

HTTP/2 即超文本传输协议 2.0，是下一代 HTTP 协议。它由国际互联网工程任务组 （IETF）的 Hypertext Transfer Protocol Bis (httpbis) 工作小组进行开发，以 SPDY 为原型，经过两年多的讨论和完善最终确定。

HTTP/2 优势如下：

1）HTTP/2 采用二进制格式传输数据，其在协议的解析和优化扩展上带来更多的优势和可能。

2）HTTP/2 对消息头采用 HPACK 进行压缩传输，能够节省消息头占用的网络的流量。

3）多路复用，简单说就是所有的请求可以通过一个 TCP 连接并发完成。

4）Server Push：服务端能够更快的把资源推送给客户端。

又拍云 CDN 当前已全平台支持 HTTP/2，并已默认开启。又因 HTTP/2 是在 HTTPS 协议的基础上实现的，所以只要使用又拍云 HTTPS 加速服务的域名，都可免费享受 HTTP/2 服务，无需做任何特殊配置，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https4.png" height="470" width="800" />

####11.5 注意事项

又拍云 HTTPS 加速服务是基于 SNI 技术实现的，因此某些不支持 SNI 的浏览器或客户端访问可能出现问题。支持 SNI 的客户端列表如下：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https5.png" height="470" width="800" />

声明：运行在 Windows XP 上的所有版本的 Internet Explorer 都不支持 SNI ，详细请参考[这里](http://serverfault.com/questions/109800/multiple-ssl-domains-on-the-same-ip-address-and-same-port)。


----------

##12. 其他配置

####12.1 文件另存为
又拍云 CDN 服务支持对响应头 `Content-Disposition` 字段的特殊设置，也即在请求 URL 中加入 `_upd` 参数可对 `Content-Disposition` 进行特殊化配置。通过对该参数的配置就可以强制浏览器触发下载行为，同时该参数的值会作为文件下载后保存到本地的文件名。特别地，当其值为 `true` 时，保持原文件名不变。

**配置引导**

示例一：

    请求参数中加入 “_upd=true” ，代表添加响应头为： Content-Disposition: attachment
```
$ curl http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upd=true -I

> HTTP/1.1 200 OK
> Content-Type: image/png
> Content-Length: 57371
> ...
> Accept-Ranges: bytes
> Content-Disposition: attachment;

```

示例一：

    请求参数中加入 “_upd=abc.png”，代表添加响应头为： Content-Disposition: attachment; filename="abc.png"

```
$ curl http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upd=abc.png -I

> HTTP/1.1 200 OK
> Content-Type: image/png
> Content-Length: 57371
> ...
> Accept-Ranges: bytes
> Content-Disposition: attachment; filename="abc.png"

```

**注意事项**

特别地， `参数跟随` 开启的情况下，此参数无效。

----------


####12.2 传递最终用户 IP

又拍云 CDN 回客户源的时候会带上 `X-Real-IP` 和 `X-Forwarded-For` 的请求头下去，值为用户实际访问 CDN 的来源 IP 地址。特别地，为了兼容部分服务端程序，我们额外还提供了 `Client-IP` 请求头的支持，其值和 `X-Real-IP`、`X-Forwarded-For` 相同。

**如何使用**

1、X-Real-IP 传递用户 IP

使用该方式传递最终用户 IP ，需要服务端代码进行一些改造，网站需要根据使用编程语言的不同，修改相应的代码模块，才可以传递最终用户 IP。代码示例如下：

示例一：PHP 代码¶

     <?php
            $ip = $_SERVER["HTTP_X_REAL_IP"];
            echo $ip;
     ?>

示例二： Nginx 配置¶

    server
        {
            listen 80;
            add_header X-Real-IP $http_x_real_ip;
        }


2、X-Forward-For 传递用户 IP

回源请求头会默认传递 `X-Forwarded-For` 的值，用户网站无需任何改造。

**注意事项**

1、新增加速服务时我们会默认使用 `X-Real-IP` 和 `X-Forwarded-For` 方式，网站只需要按照 “如何使用” 章节中，对原先的用户 IP 获取代码进行替换即可；

2、由于 `X-Real-IP`  是又拍云 CDN 服务特有的回源请求头 ，故终止 CDN 后，网站需将获取用户 IP 的代码修改为原始代码；

3、在选择使用 `X-Forwarded-For` 进行最终用户 IP 传递时  ，`X-Real-IP`、`Client-IP`也是同时传递的；

----------

####12.3 自定义提示图

支持自定义错误状态设置，目前支持 403、404、405 错误码自定义提示图的设置。如未设置，则默认遵循又拍云 CDN 服务默认错误状态提示图。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级服务 > 自定义提示图，在此栏目下进行相应图片的提示。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-custom-map.png" height="470" width="800" />

> 403 提示图

当用户设置了例如 IP 禁用、Referer 等防盗链配置并且当前的请求与规则不匹配时，即会响应 403 给客户端，表示该次请求被禁止。另外，若该服务设置了自定义 403 提示图，那么此类情况下就会响应一个 302 跳转到相应的图片。

> 404 提示图

当请求的文件不存在时，会响应 404 状态码，可通过自定义提示图来展示。

> 405 提示图

正常使用中的服务，若被人为地设置为禁止外链，此时访问就会响应 405 状态码。同样，405 也支持自定义提示图设置。


**注意事项**

1、若此处未设置任何自定义提示图，CDN 服务会默认响应一个错误状态码提示图；

2、此处自定义提示图的文件大小限制在 30KB 以内；

----------
