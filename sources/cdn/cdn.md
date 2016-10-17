# CDN产品文档中心

---

----------

产品概述
====

 - 产品简介

又拍云 CDN ( Content Delivery Network，内容交付网络 ) 服务是遍布全球的内容交付网络。内容交付网络是一种通过部署在互联网上的高度分布式的服务器加速平台，代替了传统以 Web Server 为中心的数据传输模式，可直接响应最终用户（客户端）的 Web 内容请求。通过内容交付网络，可将网站的静态资源，如：图片、JavaScript、CSS、网页等内容发布到最接近最终用户（客户端）的网络“ 边缘 ”，配合精准智能调度和边缘缓存，使得最终用户都能够快速安全可靠的访问到网站资源，以确保最终用户得到最快的页面加载时间和最佳的性能。与此同时，可大幅降低源站压力。

又拍云内容交付网络结合优质的网络节点资源、网络服务器技术以及传输路由技术优势，构建了下一代 CDN 加速平台。我们的服务器软件可有效处理每秒百万次的请求，我们力求让 CDN 服务更加简单、高效、安全，尽一切可能帮助用户解决互联网上可能影响数据传输速度和稳定性的瓶颈和环节。

 - CDN原理

假设您的业务访问域名为 www.abc.com，当该域名接入到又拍 CDN 服务之后，您的最终用户发起的 HTTP 请求，实际的访问流程如下图所示：

![test.png-216.7kB][1]
 
- 主要功能

![功能.png-143.8kB][2]

 - 资源分布

截至 2016 年 10 月，又拍云国内 CDN 节点达到 150 多个，覆盖 26 个省份，10 多个运营商，带宽总量超过 1.5TB。2015 年底，又拍云 CDN 节点资源已覆盖新加坡、台湾、香港、美国、德国等海外地区，全面支持海外加速，推进全球化布局。



产品架构
====

整个产品架构分为客户端、又拍云 CDN 网络、企业源站、管理控制台等角色及模块组成:

![产品架构.png-250.7kB][3]

产品构架如上图所示：

客户端：客户端通过 DNS 解析可以快速接入到又拍云 CDN 网络，CDN 智能调度系统可以响应一个离用户最近的 CDN 边缘节点 ip 给客户端，客户端可以达到就近访问的目的。

CDN 网络：又拍云 CDN 网络包括边缘节点集群、中心节点集群、智能调度系统等模块和基础设施组成，节点集群除了具备高性能的缓存设备，还具备四层和七层的负载均衡功能。

企业源站：企业源站可以是企业自建的源站或者托管在第三方存储的静态资源，CDN 网络作为反向代理会根据客户端的请求回源站请求资源文件并将数据资源进行缓存。

管理控制台：用户可通过又拍云管理控制台、API 的方式，管理托管在又拍云 CDN 网络上的资源及服务，通过更多的统计数据展示，使得 CDN 的服务更加方便快捷。

产品优势
====

自助操作，提升效率

相对传统 CDN 厂商，又拍云完全实现全自助化配置，客户可自主进行功能的配置（域名配置、回源策略、缓存规则等），极大提升了工作效率。

资源丰富，全网覆盖

又拍云拥有 150 多个国内优质节点，全网 4000 多台服务器，实现无差异的网络覆盖，全节点全资源提供服务，真正实现全网加速；网络资源实现本地化运营商覆盖，支持带宽、存储、节点的弹性扩展，避免突发访问激增，打造了目前国内覆盖率最高的内容分发网络，让用户随时随地享受云加速。

节约成本，简化运营

用户通过又拍云 CDN 的分发加速，免去了自身架设内容分发的时间与投入，更能获得非常专业的运维保障服务，简化了客户网站的系统维护，更便捷的对全网进行分发。

分析数据，精细监控

又拍云针对所有客户，实现全透明化监控，服务质量可通过又拍云管理控制平台一览无余，为客户业务核心导向提供重要依据。

运营稳定，服务专业

又拍云 CDN 作为网络加速服务商，拥有丰富的 CDN 运营经验，深刻理解互联网基础架构，并提供了7*24小时无间断的运营服务，保证任何时刻都可以为客户解疑答惑。

应用场景
====

又拍云全网加速服务完美融合了又拍云对象存储和 CDN 服务，可以针对网页图片、下载安装包、音视频等文件提供数据托管、分发加速及处理于一体的一站式解决方案。业务使用场景如下：

1、网页图片加速

网页及小文件加速服务帮助客户提升网站的用户访问速度和服务可用性，通过将需要访问的静态内容分发到位于网络边缘的各个缓存服务器中，将用户请求自动分配到距离用户最近的服务器上。建议结合又拍云对象存储服务，可以有效提升内容加载速度，轻松搞定网站图片、短视频等内容分发。

2、动态内容加速

又拍云动态内容加速融合在全网加速服务中，可智能判断 php、jsp、asp 等动态资源文件，自动实现动静分离。通过智能路由、内容压缩、协议优化等技术实现动态内容的加速，完美解决电商购物、在线旅游等网站在登陆、提交订单等动态请求访问速度慢的问题，可进一步提高客户业务转化率。

3、大文件下载加速

又拍云全网加速服务针对网络游戏、软件下载类网站及应用等大文件分发提供了一站式解决方案。由于此类网站及应用存在带宽突发并且由于大文件下载会导致源站服务器承受非常大的压力，又拍云丰富的带宽资源并且结合又拍云对象存储服务，可完美解决大文件带宽突发带来的一系列问题。

4、视频点播加速

又拍云全网加速服务融合了视频点播加速和异步音视频处理服务，针对流媒体点播、音视频网站及应用，又拍云可以提供高速、低价的数据传输服务，帮助在线视频网站、在线教育网站等以流媒体为主的企业提供稳定、高质量、低价的视频分发服务，让您的用户在全国任何地方都可以享受到优质的视频体验。

5、自定义SSL加速

又拍云自定义SSL 加速服务可满足电子商务、在线教育、银行保险等网站在 HTTPS 加速方面的需求，并可以完全满足自主化配置。SSL 服务器数字证书可凭借高强度签名算法，结合服务器端的加密协议，完成 Web 访问客户到服务器间的 https 加密传输，有效防止 Web 传输中的数据劫持，为网站访问者提供真实、有效、安全的网站内容提供方身份信息，确保用户可放心的浏览网站相关内容。


使用指南
====

## 入门指南 ##

 - 准备工作

为了快速完成注册和配置过程，您需要提前准备如下事项：
1.一个有效的邮箱地址，主要用于密码重置找回、邮箱快捷登陆、域名审核反馈等，您可以在后期进行更换；
2.一个有效的个人手机号码，主要用于密码重置找回、账号快捷登陆、获取服务通知（如余额不足提醒和账户欠费提醒），您可以在后期进行更换；
3.如果您使用的是［自主源站］，至少需要准备一个回源 IP 地址或者域名，并且需要保证该源站可以正常提供服务。您可以在后期添加更多回源地址；
4.提供需要加速的网站访问域名，并要保证该访问域名可正常提供服务，并且已经备案（未备案将无法配置成功）；


准备工作做完之后，本文档将介绍如何快速开始使用CDN服务，主要步骤有：
1、创建账户
2、账户认证
3、创建服务
4、配置测试
5、缓存验证
6、绑定CNAME

步骤一：创建账户

打开注册界面（ https://console.upyun.com/register/ ） 创建一个免费的账户或者在又拍云官网 （www.upyun.com） 页面右上角点击 「免费注册」  ：

![步骤一.png-36.7kB][4]

您可根据自身的情况选择 “企业” 或 “个人” 类型，请填写完整的注册信息（注意阅读又拍云服务条款），并确保电子邮件地址正确，以保障您能够正常接收到服务通知。
    
步骤二：账户认证

注册完毕后，登录又拍云管理控制台。您需要进行企业／个人认证，认证完成后方可创建服务，否则将无法使用又拍云服务。账户认证如下截图所示：
    ![步骤二.png-31.4kB][5]
    
企业认证：
![企业认证.png-49.2kB][6]
个人认证：
![个人认证.png-19.7kB][7]
提交后，需要等待审核通过，届时会通过站内信、邮件进行通知。

步骤三：创建服务

创建的帐户身份认证完成之后，您就可以开始配置您的初始服务了，具体包括：
1.根据需要选择加速服务类型，包括 [全网加速] 和 [直播加速] 服务； 
![步骤三.png-70.3kB][8]
2.根据需要选择源站类型，包括 [自主源] 和 [又拍云] ；
3.根据您的业务情况，选择一个合适的服务名，例如：upyun_image；
4.选择需要加速的网站访问域名（必须已备案）；
5.根据网站回源信息进行配置（IP地址或者域名）；
6.确认域名配置信息及状态。域名配置完成以后，可在服务列表中进行查看，并可以对域名配置进行修改；注意：当域名状态为“服务中”时，配置才会生效。
![确认域名信息.png-38kB][9]

该步骤详细配置可参见如下教学视频：

1.自主源站配置教学视频：
https://techs.b0.upaiyun.com/videos/cdnpage/create_dynamic_cdn_and_cache_setting.html
2.又拍云存储配置教学视频：
https://techs.b0.upaiyun.com/videos/cdnpage/create_cdn_and_use_storage_services.html
备注：此处可以嵌入两个播放器页面，方便用户点击查看。

步骤四：配置测试

假设上述配置已全部完成，您的域名为：test.yourdomain.com，配置如下：
![测试配置.png-36.5kB][10]
1.您可通过 ping 加速域名的 CNAME 地址 cptest123.b0.aicdn.com，获取 CDN 节点 ip 为：183.134.101.194 ( 示例 ip 为宁波电信节点出口 ip )。
![ping.png-51.6kB][11]
2.然后打开本地电脑的 hosts 文件（路径为 C:\Windows\System32\drivers\etc ），将183.134.101.194  test.yourdomain.com  写入其中并保存（ 注意：测试完成后，进行删除 )。
![host.png-48.1kB][12]
3.保存以后，打开chrome浏览器，按 F12 键开始抓包，访问域名测试链接，例如：http:// test.yourdomain.com/image/test.jpg，如果成功访问即可证明上述加速域名配置正确，抓包如下图所示：
![抓包.png-15.5kB][13]

步骤五：缓存验证
如下图所以，后缀为 jpg 类型的图片，配置的缓存时间为 7200s。
![缓存配置.png-23.5kB][14]
在步骤三的基础上，打开 chrome 浏览器，将要访问的 URL 输入浏览器，点击回车，抓包如下图所示，可以看到 max-age=7200，进而证明缓存配置成功。
![cache.png-73.2kB][15]

步骤六：绑定CNAME

如果步骤三、四测试都正常，则可进行加速域名 CNAME 的切换。
1.获取 CNAME 地址。打开域名配置，CNAME 地址如下图红框中所示：
![cname.png-22.5kB][16]

2.修改 CNAME 记录
登入域名的 DNS 服务商网站，修改 CNAME 记录，具体配置方法可参考：
DNSPod :https://support.dnspod.cn/Kb/showarticle/tsid/32/?spm=5176.doc27112.2.16.GAMn1f
新网：http://www.xinnet.com/service/cjwt/domain/guanli/1164.html?spm=5176.doc27112.2.17.GAMn1f

3、验证 CNAME 配置是否生效
因 DNS 解析记录都有缓存时间，CNAME 的生效时间一般是 600s，可通过 ping 所配置的加速域名，检验 CNAME 配置是否生效，如果后缀显示为 aicdn.com，则证明 CNAME 配置已生效，即加速业务正式开始启用。
![yanzheng.png-61.6kB][17]


基础设置
====

第一步：创建服务
--------

源站类型
----

功能介绍

目前源站类型包括［又拍云］和［自主源］。其中［又拍云］表示资源都存放在又拍云分布式对象存储服务上，同时附加了 CDN 服务的支持，因此也拥有绝大多数又拍云 CDN 的功能；而［自主源］则表示资源都存放在客户自己的源站或第三方云存储服务上，仅使用又拍云 CDN 服务。

配置引导

• 如果您的资源都托管在第三方云存储平台或者自有源上，请选择 “自主源”；
• 如果您准备把静态资源都托管在又拍云对象存储平台上，请选择 “又拍云”；

如下图所示：
![源站类型.png-16.7kB][18]

----------

回源配置
----
回源配置功能界面如下截图所示：

![回源配置.png-34.3kB][19]

1、回源HOST
--------
功能介绍

顾名思义就是 CDN 节点回源过程中所需访问的源站服务器上的域名。CDN节点回源时会在HTTP 请求头中 HOST 字段里填写该域名，以支持源服务器可通过此 HOST 做的特殊逻辑，一般是域名形式的字符串，当然也支持任意字符串形式。

配置引导

• 回源HOST为可选配置项，特别地，如果该自定义值为空，则该服务下的加速域名就会作为回源HOST；
• 自定义：可以根据需要修改回源host，请保证该域名可以正确访问，否则会出现回源失败的情况。

----------

2、回源方式
------
功能介绍

默认情况下，我们都会以 HTTP 协议回源，您也可以采用 HTTPS 协议回源。另外一个特别的选项是协议跟随，当启用该特性时，回源协议始终会和客户端访问 CDN 的协议保持一致，即客户端用 HTTP 协议访问我们 CDN，那么回源时我们也会用 HTTP 协议进行回源，同样，若 HTTPS 访问，那么回源也是 HTTPS。

配置引导

当我们选择 HTTP 协议回源时，回源地址的端口号默认为 80，而选择 HTTPS 协议回源时，端口号默认为 443。当然，你也可以手动指定特殊的端口号，例如 8080 或 8443。

注意事项

选择【协议跟随】后，回源地址的端口号配置将仅作用于回源协议为 HTTP 的情况，默认 80，当然你也可以自定义为其他值，而回源协议为 HTTPS 时端口号则始终固定为 443，且目前暂时不支持修改此固定值。因此，若之前选择 HTTPS 协议回源 且自定义设置过端口号的用户需要注意，切换到 协议跟随模式下时，自定义的端口号将仅作用于回源协议为 HTTP 的情况。

----------

3、线路配置
------

功能介绍

线路配置包括回源地址、端口号、主备线路、轮询权重、最大失败次数、静默时间等高级等设置。其中回源地址则表示源站实际可访问的网络地址，可填 IP 地址或域名地址，如果是域名地址，那么 CDN 在回源时会对该域名地址进行 DNS 解析，然后针对解析出来的 IP 地址进行访问，因此若解析失败将会导致回源异常。

配置引导

![配置引导.png-32.2kB][20]

 - 主、备线路
 
回源地址配置中除了默认的 主线路 外，我们还支持 备份线路 的配置。默认情况下，主线路多个回源地址会以 RR 轮询方式进行调度，当且仅当所有主线路回源地址均不可达时，此时我们将使用备份线路进行转发，若备份线路有多个回源地址，那么负载均衡方式也和主线路一样，以 Round-Robin 轮询（https://en.wikipedia.org/wiki/Round-robin_scheduling）方式进行调度。

 - 轮询权重

默认情况下，轮询权重都为 1，表示所有回源地址轮询到的概率均等。当然也可以如上图那样自定义轮询权重，我们看到主线路两个回源地址权重分别是 1 和 2，此时主线路集群在轮询调度中会有 1/3 的概率选中192.168.11.1:80这个节点，而选中192.168.11.2:80节点进行转发的概率则为 2/3。即当前节点轮询到的概率可按（当前节点权重/集群所有节点权重之和）这个公式计算。

 - 最大失败次数和静默时间

表示在一个【静默时间(fail_timeout)】内如果对应源站的异常次数累计达到当前设置的【最大失败次数 (max_fails)】，那么在下一个【静默时间 (fail_timeout)】内，我们就认为这个源站宕机了，即在这段时间内不会再将请求转发给它。特别地，当【最大失败次数 (max_fails)】其值为 0 时，表示不会进行失败累计，【静默时间 (fails_timeout)】也同时失效。

合理设置最大失败次数和静默时间，有助于在源站异常的情况下最大程度减少对线上请求的影响。这里实际上跟 Nginx 核心的 upstream 模块实现机制类似，以下文档可供进一步参考： http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server
 
 
----------

域名绑定
----

功能介绍

在单个服务里面，我们支持自定义域名的绑定，前提是该域名已经备案成功，否则无法通过域名绑定审核。另外，审核成功后会以邮件形式进行通知。

配置引导

![域名绑定.png-40.4kB][21]

除常规形式的域名外（包括顶级域名），我们还支持泛域名绑定，比如 *.yourdomain.com；特别地，其中 * 最多支持匹配 4 层：

[v]*.yourdomain.com => foo.yourdomain.com

[v]*.yourdomain.com => bar.yourdomain.com

[v]*.yourdomain.com => foo.bar.yourdomain.com

[v]*.yourdomain.com => foo.bar.baz.yourdomain.com

[v]*.yourdomain.com => foo.bar.baz.qux.yourdomain.com

[x] *.yourdomain.com=> foo.bar.baz.qux.quxx.yourdomain.com

[v]*.bar.yourdomain.com => foo.bar.yourdomain.com

[v]*.bar.yourdomain.com => baz.foo.bar.yourdomain.com

[x]*.bar.yourdomain.com => foo.bar.baz.yourdomain.com

添加域名绑定后，需要到域名服务商的 DNS 解析管理中，将域名 cname 解析到<bucket>.b0.aicdn.com。 

----------

第二步：缓存控制
--------

自定义缓存控制
-------

功能介绍

针对使用【自主源】的客户，可根据自身情况，灵活选择缓存配置
1、后台自定义缓存规则设置优先级最高，特别地，不缓存内容 规则优先级高于［缓存内容］规则；
2、其次是用户源站 Cache-Control 和 Expires 头，若没有匹配到自定义缓存规则，那么此时我们会尽可能遵循源站的缓存配置
3、默认策略，即没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头的情况：

4、缓存策略优先级：不缓存内容规则（后台配置）> 缓存内容规则（后台配置） > 用户源站缓存配置 > 默认缓存策略。

配置引导

针对【自主源】客户， 其自定义缓存规则只影响大于等于200 小于 400 （特别地，301 和 302 除外）的这类正常的状态码响应，缓存配置界面如下：

![缓存管理.png-33.5kB][22]

自定义缓存规则目前只支持 URL 的路径（PATH）和参数（ARGS）部分，暂不支持协议和域名部分，路径部分支持 4 种形式：

•  前缀加星号，例如 *.（jpg,txt）

•  后缀加星号，例如 /d/e/*

•  中间加星号，例如 /a/b/*.(zip,mp3)

•  不加任何星号，例如 /m/foo.png

动静自动分离
------

功能介绍

针对 自主源站 的 CDN 服务而言，当没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头时，我们就会对回源内容进行动静识别。特别地，我们会根据响应头中的 Content-Type 字段和 URL 中的文件后缀名来判断是否是静态内容，其中 Content-Type 匹配优先级高于文件后缀名，以下是我们维护的一份 Content-Type 和文件后缀名列表，若满足匹配我们就认为是静态内容：

Content-Type 列表

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

文件后缀名列表：

css js jpg jpeg gif ico png bmp pict csv 
doc pdf pls ppt tif tiff eps ejs swf midi 
mida ttf eot woff otf svg svgz webp docx xlsx 
xls pptx ps class jar bz2 bzip exe flv gzip 
rar rtf tgz gz txt zip mp3 mp4 ogg m4a 
m4v apk 

----------

第三步：内容管理
--------

URL刷新
-----

功能介绍

当源站的文件出现变动（覆盖，删除）的时候，删除 CDN 节点上旧的文件缓存。

针对使用【 又拍云存储】的客户 而言，文件的替换和删除操作，我们后台会自动提交缓存刷新任务进行全网刷新，特别地，原图的替换和删除，我们会自动将其对应的所有缩略图版本号也进行刷新。正常情况下全网5 分钟内就能刷新完毕，针对漏刷的情况，可以在管理后台进行手动刷新。

配置引导

![URL刷新.png-23.4kB][23]

----------

规则刷新
----

功能介绍

规则刷新也即目录刷新；当源站有针对资源进行批量更新时，可以使用［规则刷新］更新节点缓存文件。一旦规则生效，规则刷新不会立马删除节点文件，新的请求会回源校验一次，以此来判定源站文件是否更新。

配置引导

1、每行一个路径规则，每个路径规则以 http:// 或 https:// 开头，每次输入不超过 10 个;
2、规则刷新后，新的请求只能访问原文件，因此将影响访问速度，并且会对源站造成一定的压力，请谨慎使用 ;

----------

URL预热
-----

功能介绍

当源站有添加新的资源文件时，可将这些新文件提前预热到 CDN 边缘节点，终端访问时将不需要再回源站，一方面可减少用户突发访问时的回源量，另一方面可以加快文件的首次访问。


配置引导

1、每行一个 URL 地址，每个 URL 地址以 http:// 或 https:// 开头，每次输入不超过 50 个；
2、URL 地址中字母的大小写敏感，错误的大小写输入会导致预热无效；
3、根据用户提交的URL数量、涉及的文件大小、网络状况等因素的影响，预热时间一般在半小时之内可完成；
 
----------

操作记录
----

功能介绍

可以快速查询URL刷新、规则刷新、URL预热等功能提交任务之后的进度反馈。

快速查看

![操作记录.png-79.5kB][24]

----------



第四步：资源监控

第五步：日志管理


  [1]: http://static.zybuluo.com/wuxuejun/bs5jzgkdqpu6a120fu1rwsdj/test.png
  [2]: http://static.zybuluo.com/wuxuejun/9763h75jib1xki0q2lswv13c/%E5%8A%9F%E8%83%BD.png
  [3]: http://static.zybuluo.com/wuxuejun/eldi6wcbox46oqtx0isf1xcf/%E4%BA%A7%E5%93%81%E6%9E%B6%E6%9E%84.png
  [4]: http://static.zybuluo.com/wuxuejun/mq3hbnslyu9k3rn17zl32gx6/%E6%AD%A5%E9%AA%A4%E4%B8%80.png
  [5]: http://static.zybuluo.com/wuxuejun/ju802au90t2ag634lzhgb6ip/%E6%AD%A5%E9%AA%A4%E4%BA%8C.png
  [6]: http://static.zybuluo.com/wuxuejun/6ptkwe2erbzn55lwjfcqotxp/%E4%BC%81%E4%B8%9A%E8%AE%A4%E8%AF%81.png
  [7]: http://static.zybuluo.com/wuxuejun/17djwe4akcx21jb19ngork9h/%E4%B8%AA%E4%BA%BA%E8%AE%A4%E8%AF%81.png
  [8]: http://static.zybuluo.com/wuxuejun/wab15qirhtgx3rg7erxdmr45/%E6%AD%A5%E9%AA%A4%E4%B8%89.png
  [9]: http://static.zybuluo.com/wuxuejun/vgoaas86eafjofs9yn1lgpmu/%E7%A1%AE%E8%AE%A4%E5%9F%9F%E5%90%8D%E4%BF%A1%E6%81%AF.png
  [10]: http://static.zybuluo.com/wuxuejun/u05br8egazvh6ol5icwr26jc/%E6%B5%8B%E8%AF%95%E9%85%8D%E7%BD%AE.png
  [11]: http://static.zybuluo.com/wuxuejun/l3csbsgcuz2t4smfsn7o3ry9/ping.png
  [12]: http://static.zybuluo.com/wuxuejun/pwoxxdjujavvng0xo9s1qtox/host.png
  [13]: http://static.zybuluo.com/wuxuejun/f897x8rv5s0mt1rfbpp2d3ow/%E6%8A%93%E5%8C%85.png
  [14]: http://static.zybuluo.com/wuxuejun/94uyed5m5pvdacncjkldfqap/%E7%BC%93%E5%AD%98%E9%85%8D%E7%BD%AE.png
  [15]: http://static.zybuluo.com/wuxuejun/sgbsb3xjz2gkmiuhfk4fzhjs/cache.png
  [16]: http://static.zybuluo.com/wuxuejun/j97eks9gcj1i463vuj4k7d2i/cname.png
  [17]: http://static.zybuluo.com/wuxuejun/8e2e3880zyl5mxszf5s0uy2k/yanzheng.png
  [18]: http://static.zybuluo.com/wuxuejun/pcti7rjo93ow77ylgo6jlax0/%E6%BA%90%E7%AB%99%E7%B1%BB%E5%9E%8B.png
  [19]: http://static.zybuluo.com/wuxuejun/pgeuxw7ox3u3tujgq5rh3ejw/%E5%9B%9E%E6%BA%90%E9%85%8D%E7%BD%AE.png
  [20]: http://static.zybuluo.com/wuxuejun/i7epy7q8ehu0inkoe0h42997/%E9%85%8D%E7%BD%AE%E5%BC%95%E5%AF%BC.png
  [21]: http://static.zybuluo.com/wuxuejun/2id9v3qmqrsjyrq8svdxhktt/%E5%9F%9F%E5%90%8D%E7%BB%91%E5%AE%9A.png
  [22]: http://static.zybuluo.com/wuxuejun/10417xph969jwesl8il9ontp/%E7%BC%93%E5%AD%98%E7%AE%A1%E7%90%86.png
  [23]: http://static.zybuluo.com/wuxuejun/oevwp0twymdrv36vesvmq3n3/URL%E5%88%B7%E6%96%B0.png
  [24]: http://static.zybuluo.com/wuxuejun/lj3ie2udv1g04w4yedmaf6re/%E6%93%8D%E4%BD%9C%E8%AE%B0%E5%BD%95.png