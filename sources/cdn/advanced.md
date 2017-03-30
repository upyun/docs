##访问控制

**IP 禁用**

假如需要禁用某个 IP 或者某个 IP 段，则需要进行该功能配置。特别地，IP 防盗链目前暂时只支持黑名单逻辑。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > IP 禁用，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-ip.png" height="490" width="800" />


注意事项：

1.支持 `*` 通配符，如 `10.11.12.*` 将禁止 `10.11.12.0~10.11.12.255` 的 IP 访问

2.禁用 IP 上限 100 条，被禁止的 IP 访问资源时，将提示 403

**地区访问限制**

地区访问限制是指根据加速网站的需求，允许或禁止特定区域的终端用户对网站资源的访问。即网站指定地区，允许该地区终端用户访问，而限制其他地区用户访问，或者相反。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 地区访问限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-region-limit.png" height="490" width="800" />


详细配置请参见 [地区访问限制用户指南](https://blog.upyun.com/?p=886)。

**Referer 防盗链**

当请求发送到 CDN 服务器后，CDN 服务器检查 HTTP 请求头中的 Referer 字段的信息，禁止或者允许符合特定规则的 Referer 的请求。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 域名防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-acces-referer.png" height="490" width="800" />


注意事项：

1.白名单：仅允许名单中的域名网站访问文件，其他域名网站都不允许访问

2.黑名单：仅禁止名单中的域名网站访问文件，其他域名网站都允许访问

3.支持 `*` 通配符，比如白名单的 `*upyun.com` 将允许 `www.upyun.com`、`abcupyun.com` 等网站访问

4.特别地，默认 `允许 Referer 为空` 这种情况会绕过防盗链的逻辑，若这里启用 `禁止 Referer 为空`，那么这类请求就直接被禁止访问了

**User-Agent 防盗链**

只允许特定的浏览器或者带有特殊 `User-Agent` 标识的专属的客户端进行访问。目前暂时只支持白名单逻辑。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 客户端防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-user-agent.png" height="490" width="800" />


注意事项：

1.添加数量上限 50 条

2.`User-Agent` 不区分大小写，且支持 `*` 通配符

3.`User-Agent` 防盗链的优先级高于 Referer 防盗链


**Token 防盗链**

可设置签名过期时间来控制文件的访问时限。Token 防盗链的目的是使得每个请求的 URL 都具有一定的“时效性”，因此 URL 本身需要携带过期时间相关的信息，同时还需要确保这个过期时间，不能被恶意修改，因此采用 md5 算法，将密文（通常是一个普通字符串）、过期时间、文件路径（过期时间和文件路径通常是有对应关系的，因此也参与 md5 的计算）等信息所计算的 md5 值也加入到 URL 中，CDN 节点在验证请求时，除了验证过期时间，同时还会验证该 md5 值是否匹配，对于不匹配的 md5，说明 URL 被篡改，即使请求未过期也需要禁止服务。

实现原理

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-token.png" height="490" width="800" />


配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > Token 防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-token-miyao.png" height="490" width="800" />


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

**回源鉴权**

回源鉴权是一种高级的防盗链方式，适用于对防盗链有很高实时性要求的场景。大概原理是 CDN 边缘节点每次接受到请求之后，都需要回客户源站进行验证，验证通过之后才认为是合法请求，这样 CDN 可以继续提供服务。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 回源鉴权，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-back-to-source-auth.png" height="490" width="800" />


详细配置请参见 [回源鉴权用户指南](https://blog.upyun.com/?p=877)。

**外链功能**

通过管理后台，可以一键禁止整个服务域名下所有资源的访问，特别地，此时会响应 405 状态码给客户端。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 基础配置 > 外链功能，关闭外链功能。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-wailian.png" height="490" width="800" />


注意事项：

1.服务创建完毕之后，外链功能默认开启

2.关闭外链功能，会导致资源无法访问，请谨慎操作


----------

##安全防护

**WAF 防护**

WAF 防护主要防护的是来自对网站源站的动态数据攻击，可防护的攻击类型包括 SQL 注入、XSS 攻击、CSRF 攻击、恶意爬虫、扫描器、远程文件包含等攻击。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > WAF 防护，开启 WAF 保护即可。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-waf.png" height="490" width="800" />


注意事项：适用于动态资源加速，全静态资源加速建议不启用该功能

**CC 防护**

CC 防护主要是针对网络安全领域中的 CC 攻击而进行的一种应用层攻击防护，该功能通过自定义匹配规则对目标资源进行监控，当请求频率达到触发频率时对疑似攻击请求进行校验，校验通过则允许访问；校验不通过，则直接禁止访问。


配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > CC 防护，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-cc.png" height="490" width="800" />


具体配置可以走如下四个步骤：

 - 第一步：添加防护策略，设置 URI 匹配规则，例如：/image/*.jpg
 - 第二步：填写访问频率阈值，例如：500次/分钟，在智能防护模式下才有效
 - 第三步：设置 IP 白名单，如果要绕过 CC 防护策略，此处可进行设置
 - 第四步：选择 CC 防护模式，包括强制防护、智能防护、暂停防护，当正在遭受攻击时，建议选择强制防护模式

注意事项：

1.CC 防护分为强制防护和智能防护两种模式，在智能防护模式下，则会匹配 URI 规则，请求一旦达到访问频率阈值，则会触发 JavaScript 校验，校验通过则可正常访问，校验不通过则直接拒绝。在强制防护模式下，则会忽略访问频率阈值，直接触发 JavaScript 校验，以此类推

2.在智能防护模式下，访问阈值设置得过低，可能不会触发 JavaScript 校验，建议阈值设置高一些

**HTTP 请求大小限制**

可限制单次请求体的内容大小，能有效保证请求的安全性，默认是 512M 。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > HTTP 请求大小限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-request-limit.png" height="490" width="800" />


**IP 访问限制**

可根据单个 IP 的访问频率来进行防御，在正常的情况下，用户访问网站是有一定频率的，可通过匹配自定义的限制规则对单个 IP 进行监控，将超过设定阈值的 IP 的访问进行拦截或者延迟响应，从而达到安全防护效果。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > IP 访问限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-ip-limit.png" height="490" width="800" />


配置步骤如下：

第一步：填写访问的资源路径规则，支持 `＊` 通配符，如：`/a/*.mp4`、`/b/*.flv`

第二步：选择限制方式：`直接禁止`或`延迟响应`

第三步：设置访问频率及相应的时间限制


注意事项：

1.直接禁止、延时响应都是以 60s 为一个计数周期，即上一个 60s 的计数不会累计叠加到下一个 60s 内

2.禁止访问时间的设置需大于等于 60s，延迟响应时间的设置需小于等于 30s

3.请勿将访问频率值设置过低，以免影响正常用户的访问请求

详细配置请参见 [IP 访问限制用户指南](https://blog.upyun.com/?p=896)。

----------

##HTTPS 配置 

又拍云 HTTPS 加速服务建立在自主研发的内容分发网络基础之上，全面支持了 HTTPS 协议加速。通过会话重用及会话保持、在线证书状态检查、协议优化、SSL 协议握手优化、HTTP/2 等技术手段，全面提升了 HTTPS 协议加速性能。

###配置引导

第一步：依次进入 服务 > 功能配置 > HTTPS, 点击 `管理` 即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https1.png" height="490" width="800" />

第二步：可针对单个域名选择相应的证书，然后保存。如果无证书可选，可前往[ SSL 证书服务](https://console.upyun.com/toolbox/ssl/)平台添加域名的自有证书；用户也可直接在[ SSL 证书申购](https://console.upyun.com/toolbox/createCertificate/)平台申购 Symantec、GeoTrust、TrustAsia、Let’s Encrypt 的各类 SSL 证书，其中 TrustAsia、Let’s Encrypt 的 DV SSL 单域名证书可免费申请。

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https2.png" height="490" width="800" />

第三步：可选择是否开启 HTTPS 访问和强制 HTTPS 功能。在开启 HTTPS 访问后，可通过点击 `修改` 切换域名所对应的证书，点击 `详情` 可查看相应证书的详细信息；

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https3.png" height="490" width="800" />

“ HTTPS 访问”：开启该功能后，客户端才能使用 HTTPS 协议进行访问，默认关闭。

“强制 HTTPS ”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

备注：其中 `HTTPS 配置` 也可以在 `SSL 证书管理`里面配置，参见下文`SSL 证书管理`部分。

###自有证书上传

如果域名当前已有证书，又拍云提供了自有证书的上传方式，无需人工协助，安全便捷。

第一步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 证书管理 -> 添加自有证书 

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl8.png" height="490" width="800" />

第二步：粘贴证书内容:

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl9.png" height="490" width="800" />

点击 `保存` 之后，可查看 SSL 证书信息如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl10.png" height="490" width="800" />

###SSL 证书管理

在[证书管理](https://console.upyun.com/toolbox/ssl/)列表中，可根据需求选择申购成功并已部署于 CDN 平台的证书或已上传的自有证书，点击 `HTTPS 配置`进行操作 ，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl11.png" height="490" width="800" />

“HTTPS 访问”：开启该功能后，客户端使用 HTTPS 协议访问时才能成功，默认关闭。

“强制 HTTPS”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

###HTTP/2

HTTP/2 即超文本传输协议 2.0，是下一代 HTTP 协议。它由国际互联网工程任务组 （IETF）的 Hypertext Transfer Protocol Bis (httpbis) 工作小组进行开发，以 SPDY 为原型，经过两年多的讨论和完善最终确定。

HTTP/2 优势如下：

1）HTTP/2 采用二进制格式传输数据，其在协议的解析和优化扩展上带来更多的优势和可能。

2）HTTP/2 对消息头采用 HPACK 进行压缩传输，能够节省消息头占用的网络的流量。

3）多路复用，简单说就是所有的请求可以通过一个 TCP 连接并发完成。

4）Server Push：服务端能够更快的把资源推送给客户端。

又拍云 CDN 当前已全平台支持 HTTP/2，并已默认开启。又因 HTTP/2 是在 HTTPS 协议的基础上实现的，所以只要使用又拍云 HTTPS 加速服务的域名，都可免费享受 HTTP/2 服务，无需做任何特殊配置，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https4.png" height="490" width="800" />

###注意事项

又拍云 HTTPS 加速服务是基于 SNI 技术实现的，因此某些不支持 SNI 的浏览器或客户端访问可能出现问题。支持 SNI 的客户端列表如下：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https5.png" height="490" width="800" />

声明：运行在 Windows XP 上的所有版本的 Internet Explorer 都不支持 SNI ，详细请参考[这里](http://serverfault.com/questions/109800/multiple-ssl-domains-on-the-same-ip-address-and-same-port)。


----------

##性能优化

**GZIP 压缩**

目前又拍云 CDN 开启了 GZIP 压缩的相关特性，具体对应的 NGINX 配置如下：

```
gzip on;
gzip_min_length 256;
gzip_types <见下面的列表>;
gzip_disable "MSIE [1-6]\.";
gzip_vary on;
```

触发实际的 GZIP 压缩行为需要同时满足如下条件：

> `Content-Type` 满足以下列表其中之一：

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

>`Content-Length` 大于 256 字节

> 客户端请求头带了 `Accept-Encoding: gzip`

**代码压缩**

开启后，会自动去除 HTML 文件中非必要的字符（空白、注释等），自动压缩文件大小，提升访问速度。特别地，目前仅支持 HTML 代码的优化。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 代码压缩，开启开关即可，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-performance-daimayasuo.png" height="490" width="800" />


**参数跟随**

参数跟随功能包括三种模式，分别是 `参数不跟随` 、`全程跟随` 、`回源跟随` ，系统默认为 `全程跟随` 模式。一般情况下，根据业务情况，建议选择 `参数不跟随` 来提高资源文件缓存命中率。详细介绍如下：

参数不跟随：

> 开启后，系统会过滤请求中的查询字符串，并且查询字符串不会传递给源站

全程跟随：
> 开启后，将不再过滤请求中的查询字符串，并将查询字符串传递给源站

回源跟随：

> 开启后，会过滤请求中的查询字符串，在访问回源阶段会将查询字符串传递给源站


配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 基础配置 > 参数跟随，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-performance-param-management.png" height="490" width="800" />

注意：参数跟随功能，可以配合缓存配置功能来使用。


**重定向优化**

当 CDN 节点回源站时，若源站响应的状态码为 301/302，CDN 节点对重定向之后的目标 URL （也即 301/302 响应头 `Location` 字段对应的信息）发起请求，将获取后的内容响应给最终用户，并在 CDN 节点进行缓存。这样向最终用户屏蔽了重定向过程，免去了最终用户再次向重定向后的 URL 重新发起请求的连接时间，从而加快了访问速度。

原理介绍

一般情况下，当 CDN 节点回源站请求文件时，若源站返回的是 HTTP 301/302 的响应，CDN 节点会将该响应返回给客户端，客户端根据响应头中的 `Location` 头信息进行再次请求。配置了重定向优化功能之后，访问流程如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-302redirect-yuanli.png" height="300" width="700" />

1）客户端向 CDN 节点发起请求并传递至源站，例如：`http://example.com/123.html`；

2）源站给 CDN 节点响应 301/302 状态码，并指明重定向后的 URL 为 `http://example.com/456.html`

3）CDN 节点对重定向之后对目标 URL 发起请求；

4）源站响应内容给最终用户，并将内容在 CDN 节点进行缓存；

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 重定向优化，滑动开关即可开启该功能。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-redirect-performance.png" height="490" width="800" />


**文件合并**

 `又拍云存储 ` 和  `自主源站 ` CDN 加速服务均支持 JS，CSS Combo（组合特性），即可以把多个 JS, CSS 请求合并成一个。
 

如何使用

> 特别地，我们以 `!!` 作为分隔符。

    http://upyun-assets.b0.upaiyun.com/foo/!!a.js,b.js

一个引用了如上 URL 的 Demo:

    http://upyun-assets.b0.upaiyun.com/jscombo.html

----------

## 分段缓存

又拍云 CDN 服务可提供分段缓存功能配置，开启该功能可有效降低大文件回源率，提升响应速度，与此同时，可以提高文件在 CDN 节点的缓存命中率。特别地，开启该特性之后，CDN 节点会以 Range 请求回源。

**配置引导**

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级服务 > 分段缓存，点击「管理」按钮即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-slice-cache.png" height="490" width="800" />

默认配置：该特性会默认关闭，您可以有选择性的开启该特性。特别地，我们针对分段缓存预置了文件后缀模板，也即可以根据文件后缀或者通配符规则来配置是否开启分段缓存功能。默认预置后缀如下：
```
视频：AVI、MP4、FLV、MOV、3GP、ASF、WMV、MPG、F4V、M4V、MKV、VOB、M3U8、TS 、RMVB、DAT、RM
音频：MP3、OGG、M4A、WMA、AIF、AU
安装包：APK、ZIP、EXE、RAR、IPA、PXL、DEB、SIS、SISX、JAR、XAP
游戏下载：SO、CUE、CCD、MDS、IMG、7Z
其它：CAB、DHP、GZ、ISO、MPQ、PBCV、PXL、QNP、R00、XY、XY2
```

**配置效果**

假设最终用户发起了资源请求，请求的 URL 为：`http://www.example.com/download/game.zip`，CDN 节点收到请求后，若命中则响应对应分段文件给最终用户，未命中的分段 CDN 节点则回源发起 Range 请求获取资源文件。

开启分段缓存：

 - 若最终用户发起资源请求，CDN 节点上已经命中该分段，则直接响应给最终用户
 - 若 CDN 节点未命中缓存，则 CDN 节点回源使用 Range 请求，分段获取资源文件

关闭分段缓存：

 - 节点文件缓存过期之后，会向源站获取整个资源文件

分段预加载：

 - 开启分段缓存功能之后，该特性会默认开启。当最终用户请求第一个分段文件时，我们会提前去下载后面几个分段文件，进而提高文件下载速度

**注意事项**

1、源站需要支持 Range 请求，否则会导致回源失败；严格来说，我们只通过 `Accept-Ranges: bytes` 这个头来判断源支不支持 Range 请求 ，否则即使开启了分段缓存特性，也不会生效；

2、该功能配置后不会立即生效，需在文件缓存过期或者文件缓存被主动刷新之后方可生效；

3、该功能仅针对静态文件，动态内容开启分段缓存会可能会发生错误，请谨慎使用；

4、该功能开启后，资源在 CDN 节点上会进行分段缓存，如果要立马关闭该特性，可通过手动刷新文件来解决；

5、如果您已经使用又拍云对象存储服务，该特性已经默认开启，无需做任何配置；

----------

##视频拖拉

视频拖拉功能用于视频点播的增强体验，方便最终用户通过拖拉播放器的进度条，观看任意起始位置的视频，而无需从头观看。该功能还可以扩展为跳过片头、续播、预播放等功能。发送拖拉播放进度时，客户端发起的请求类似 `http://video.upyun.com/test.mp4?start=100` 这样的 URL 请求。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 视频拖拉，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-video-drag.png" height="490" width="800" />

详细配置请参见 [「支持从任意起点观看」视频拖拉用户指南](https://blog.upyun.com/?p=873)

注意事项：

1.为了保证正常拖拉，播放器可以发起约定格式的请求，例如：`http://example.com/video/test.mp4?start=xxx&end=xxx`

2.视频文件需含有关键帧，这是实现拖拉的前提条件，关键帧太少或者没有关键帧，视频文件需要重新编码

3.视频文件 `metedata` 中缺少关键帧的索引信息，如 FLV 缺少 `keyframes` 信息，MP4 缺少 `seekpoint` 信息，或缺失 `moov box`，都会导致拖拉失败

4.支持根据不同资源路径添加配置，最多仅支持 20 条

5.目前支持文件格式有：MP4 和 FLV，MP4 支持按时间拖拉，FLV 支持按字节拖拉


----------

##镜像存储

使用又拍云 CDN 服务时，源站类型选择的是 `自主源`  模式（也即客户的网站资源都托管在自己的服务器或者第三方存储平台上）的话，可以开启镜像存储功能，可以将网站数据平滑迁移至又拍云存储平台，并减小网站回源比例。


**实现流程**

实现机制可以参照如下流程：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-storage.png" height="300" width="500" />


 - 终端用户访问资源；
 - 又拍云 CDN 节点从用户自主源站获取资源；
 - 返回资源给终端，并持久化存储到又拍云存储平台；
 - 终端用户再次访问时，将直接从又拍云获取资源，不再回源获取；
 - 随着终端用户的不断访问，源站的资源逐步自动存储到又拍云存储平台。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 镜像存储，点击开启即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-monitor-storage-management.png" height="490" width="800" />

又拍云镜像存储功能包括三种状态，分别是开启、暂停、关闭，详细描述如下：

**开启状态**

开启之后，当 CDN 节点缓存失效时，会优先回又拍云存储获取资源，如果又拍云存储没有命中资源，则直接回自有源站获取资源并响应给最终用户，与此同时，资源会被扔进镜像存储队列并进行存储，下一个相同的资源请求过来时则会直接由存储命中的资源直接提供服务。


**暂停状态**

直接关闭镜像存储功能，可能会导致回源压力过大，我们会先进入暂停状态，该状态下不会进行资源迁移动作，访问资源时，仍然先访问又拍云存储，如果存储上没有命中资源，则会访问自主源站。

**关闭状态**

该功能关闭之后，新的请求过来的话，CDN 节点不会回又拍云存储平台获取资源，则会直接回客户源站获取资源。


**注意事项**

1.当用户更新了源站资源时，为了保证请求访问到的资源是最新的，此时需要通过 API 或 FTP 的方式对已经存储在又拍云存储上的文件进行删除和替换操作

2.当镜像存储功能处于暂停状态时，您可以通过 API 接口逐步删除又拍云上对应的文件，将回源流量平滑迁移至源站，或调整源站带宽，最后再执行关闭操作


----------


##跨域配置

跨域资源共享 （ Cross-Origin Resource Sharing）是一种网络浏览器的技术规范，他为  Web 服务器定义了一种方式，允许网页从不同的域访问其资源。跨源资源共享标准通过新增一系列 HTTP 头，让服务器能声明那些来源可以通过浏览器访问该服务器上的资源。此时需要在 Response Header 中增加跨域相关配置，这样就可以使得资源的安全访问成为可能。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > CORS 跨域共享，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cors-config.png" height="490" width="800" />

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


注意事项：


1）最多支持添加 10 条规则，匹配时从第一条开始逐条匹配，以最早匹配上的规则为准

2）`允许的域`、`Allow Header` 、`Expose Header` 最多允许 20 行

3）`允许的域` 及 ` Methods ` 建议根据需要使用最小的配置来保证安全性，多条规则间不要有覆盖和冲突

4）`允许的域` 需带上完整的域信息，即指定 https 或者 http，或者 `*://foo.com` 这样的形式

5）`Allow Header`、`Expose Header` 不允许使用通配符，大小写不敏感

6） `缓存时间` 如果没有特殊情况可以酌情设置得大一点，默认为 86400 秒，最大为 604800 秒

----------

## 文件另存为

又拍云 CDN 服务支持对响应头 `Content-Disposition` 字段的特殊设置，也即在请求 URL 中加入 `_upd` 参数可对 `Content-Disposition` 进行特殊化配置。通过对该参数的配置就可以强制浏览器触发下载行为，同时该参数的值会作为文件下载后保存到本地的文件名。特别地，当其值为 `true` 时，保持原文件名不变。

配置引导

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

注意事项

特别地， `参数跟随` 开启的情况下，此参数无效。

----------

## 传递最终用户 IP

又拍云 CDN 回客户源的时候会带上 `X-Real-IP` 和 `X-Forwarded-For` 的请求头下去，值为用户实际访问 CDN 的来源 IP 地址。特别地，为了兼容部分服务端程序，我们额外还提供了 `Client-IP` 请求头的支持，其值和 `X-Real-IP`、`X-Forwarded-For` 相同。

如何使用

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

注意事项

1、新增加速服务时我们会默认使用 `X-Real-IP` 和 `X-Forwarded-For` 方式，网站只需要按照 “如何使用” 章节中，对原先的用户 IP 获取代码进行替换即可；

2、由于 `X-Real-IP`  是又拍云 CDN 服务特有的回源请求头 ，故终止 CDN 后，网站需将获取用户 IP 的代码修改为原始代码；

3、在选择使用 `X-Forwarded-For` 进行最终用户 IP 传递时  ，`X-Real-IP`、`Client-IP`也是同时传递的；

----------

## 自定义提示图

支持自定义错误状态设置，目前支持 403、404、405 错误码自定义提示图的设置。如未设置，则默认遵循又拍云 CDN 服务默认错误状态提示图。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级服务 > 自定义提示图，在此栏目下进行相应图片的提示。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-custom-map.png" height="490" width="800" />

> 403 提示图


当用户设置了例如 IP 禁用、Referer 等防盗链配置并且当前的请求与规则不匹配时，即会响应 403 给客户端，表示该次请求被禁止。另外，若该服务设置了自定义 403 提示图，那么此类情况下就会响应一个 302 跳转到相应的图片。

> 404 提示图

当请求的文件不存在时，会响应 404 状态码，可通过自定义提示图来展示。

> 405 提示图

正常使用中的服务，若被人为地设置为禁止外链，此时访问就会响应 405 状态码。同样，405 也支持自定义提示图设置。


注意事项

1、若此处未设置任何自定义提示图，CDN 服务会默认响应一个错误状态码提示图；

2、此处自定义提示图的文件大小限制在 30KB 以内；

----------

##自定义 Rewrite

###功能简介

自定义 rewrite 是基于 DSL ( Domain Specific Language ) 理念来设计的，主要面向开发者使用。充分利用又拍云 CDN ( Content Delivery Network )分布式边缘网络的性能及规模，通过又拍云管理控制台可轻松创建 rewrite 规则，规则支持函数、变量、字符串常量，用户可以将这些自由组合，可以实现对 URL 的改写、重定向、自定义 HTTP 头、请求禁止等处理逻辑。

###应用场景

- 提高安全性：可以有效的避免一些参数名、ID 等完全暴露在用户面前；
 
- URL 美化：去除了那些比如 `*.do` 之类的后缀名、长长的参数串等，可以自己组织精简更能反映访问模块内容的 URL；
 
- 提高网站 SEO：通过 URL Rewrite 之后，更有利于搜索引擎的收入，通过对 URL 的一些优化，可以使搜索引擎更好的识别与收录网站的信息；
 
- HTTP 头改写：新增、删除 HTTP 请求头，新增、删除 HTTP 响应头等处理逻辑；
 
- 防盗链设置：可以很灵活、高效的编写防盗链规则和处理逻辑，可以实现高强度的防盗链规则；
 
- 边缘重定向：可以根据特殊的业务逻辑，触发某个条件之后，可将请求重定向到特定的地址等等；
 
 
###规则说明

为了更好的理解 rewrite 规则，本节围绕 rewrite 规则配置项和规则示例来进行详细说明，先来了解下配置截图，如下截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-custom-rewrite-config.png" height="380" width="800" />

如上所示，配置一条完整的 rewrite 规则需要完成 ` Rewrite 规则` 、` URI 提取正则` 、` break` 、` 调试模式` 、` 备注` 这 5 个选项的配置，其中 ` Rewrite 规则` 为必填项，`URI 提取正则` 和` 备注` 为可填项，` break`  和 `调试模式` 为控制开关，可根据需要来进行开启和关闭。

下面我们来结合配置好的规则示例来说明下每个配置项的作用，如下为一组 rewrite 规则集，包括两条 rewrite 规则：

    -- JSON
    [
    rules[
    {
        rule :  $WHEN($EQ($_HOST, 'images.foo.com'))/$1,
        pattern :  ^/images/(.*)$
        break :  true,
        debug :  true,
        description :  URL改写,
          
    },
    {
        rule :  /movies/$1,
        pattern :  /download/(.*)$
        break :  true,
        debug :  true,
        description :  目录改写,
    
    },
    ]

通过以上规则集的了解，下面我们针对规则集里面的每个部分做相应的介绍和解释：

` pattern ` 为对当前请求 URI 进行匹配的 `PCRE`  正则表达式，匹配后，产生 ` $1, $2 ...`  这样的变量，对应配置项里面的` URI 提取正则`；

` rule ` rewrite 规则主要组成部分，代表最终的处理逻辑，详细请看语法规则部分，对应配置项里面的 `rewrite 规则`；

` break ` 表示如果此次 rewrite 成功后是否要终止剩下的的 rewrite 过程；

` debug ` 是一个调试开关，默认开启，对应配置截图里面的 ` 调试模式 `；

` description ` 用来描述该规则的用途； 对应配置项里面的 ` 备注 ` 。

**调试模式**

由于 rewrite 过程会对当前请求产生副作用，这是非常危险的，例如随意的填写一条 rewrite 规则：
 > /foo

此规则会将所有请求的 URI 改写成 `/foo`，这样客户端所有请求的响应就变成 404 了， 这是您极不愿意看到的结果。我们允许对 rewrite 规则进行调试，即新增加一条规则时，`调试模式` 开关会默认打开，这样添加规则后，即使当前请求命中该 rewrite 规则， rewrite 过程也不会生效，只有当包含以下请求头时，rewrite 过程才会生效：

> X-Upyun-Rewrite-Preview: true

使用命令行工具 curl 即可对规则调试：

> curl -H "X-Upyun-Rewrite-Preview: true" http://your-domain/foo/bar.html -v

这样经过调试，确定该 rewrite 过程符合预期后，即可将 `调试模式` 关闭，此时该 rewrite 过程会对所有命中的请求生效。

注意：特别地，调试模式目前仅针对 URI 和 ARGS 修改以及一些函数有效，受 `调试模式` 开关影响的 rewrite 操作有：

```
 - $LIMIT_RATE_AFTER()
 - $LIMIT_RATE
 - $DEL_REQ_HEADER(E)
 - $REDIRECT(E1,E2)
 - $DEL_ARG(E)
 - $EXIT(E1,E2)
 - $ADD_REQ_HEADER
 - 修改 URI 和 ARGS 的操作
```

###语法规则

**函数**

函数调用以 ` $ ` 开头，后跟一组大写字母，字母之间可以包含下划线 `_`，函数需要的参 数放在` () `中，以 `,` 分隔。如果没有特别说明，rewrite 中的函数参数个数不能少于要求的参数个数，否则视为语法错误，然后终止 rewrite 过程，多余的参数会被求值，但不影响调用。函数调用是有上下文的，譬如 `$WHEN` 这个函数，参数是 `bool` 类 型，参数中有不成立的条件 `false` 时，会终止 rewrite 过程。支持的函数有：

> 条件选择和判断

函数                         | 含义
:--------------------       | :-------
`$WHEN(E1, E2, ...)`        | 所有条件都成立时才进行 `rewrite`，并返回空字符串
`$NOT(E)`                   | `E` 不成立时返回 `true`， 否则返回 `false`
`$ALL(E1, E2, ...)`         | 所有条件都成立时返回 `true`，否则返回 `false`，参数个数不限
`$ANY(E1, E2, ...)`         | 其中一个条件成立时返回 `true`，否则返回 `false`，参数个数不限
`$OR(E1, E2, ...)`          | 值为第一个为真的表达式
`$SELECT(E1, E2, E3)`       | `E1` 为真时值为 `E2`，否则为 `E3`
`$PCALL(E)`                 | 保护模式下解析 `E`，失败时返回空字符串

> 字符匹配和捕获

函数                         | 含义
:--------------------       | :-------
`$MATCH(E1, E2)`            | PCRE 匹配，`E2` 为要匹配的 pattern，返回 `true` 或者 `false`
`$CAPTURE(E1, E2, E3)`      | 按 `E2` pattern 对 `E1` 进行捕获，`E3` 表示是否忽略大小写，捕获后生成 `$n.m` 形式的变量，`n` 为当前 `CAPTURE` 出现的次序，`m` 表示匹配分组；`E2` 为空时，表示对 `E1` 进行捕获，此时 `E1` 对应 `$n.0`

> 请求/响应修改

函数                         | 含义
:--------------------       | :-------
`$ADD_REQ_HEADER(E1, E2)`   | 添加请求头 `E1` 为 `E2`
`$DEL_REQ_HEADER(E)`        | 删除请求头 `E`
`$ADD_RSP_HEADER(E1, E2)`   | 添加响应头 `E1` 为 `E2`
`$DEL_ARG(E)`               | 删除请求参数项 `E`
`$SET_METHOD(E)`            | 修改当前 HTTP 请求方法为 `E`，`E` 可选值有：`GET`, `HEAD`, `PUT`, `POST`, `DELETE`, `OPTIONS`, `PATCH`; 注 1
`$SET_BODY(E)`              | 修改当前 HTTP 请求体的内容为 `E`，仅在 `PUT` 或 `POST` 请求下有效
`$REDIRECT(E1, E2)`         | 重定向地址到 `E1`，状态码为 `E2(301, 302)`
`$EXIT(E1, E2)`             | 以状态码 `E1` 退出，响应体为 `E2`

> 数值计算

函数                         | 含义
:--------------------       | :-------
`$ADD(E1, E2)`              | 数字相加，返回 `E1 + E2` 结果数值
`$MOD(E1, E2)`              | 数字取余，返回 `E1 % E2` 结果数值，其中 `E2` 不能为 `0`
`$MSUB(E1, E2)`             | 数字相减，返回 `E1 - E2` 结果数值
`$MULTIPLY(E1, E2)`         | 数字相乘，返回 `E1 * E2` 结果数值
`$DIVIDE(E1, E2)`           | 数字相除，返回 `E1 / E2` 结果数值，其中 `E2` 不能为 `0`
`$BYTE(E)`                  | 返回字符 `E` 的 ASCII 编码值
`$FLOOR(E)`                 | 返回小于或等于数字 `E` 的最大整数
`$CEIL(E)`                  | 返回大于或等于数字 `E` 的最小整数
`$GT(E1, E2)`               | 数字比较，是否大于，返回 `true` 或者 `false`
`$GE(E1, E2)`               | 数字比较，是否大于等于，返回 `true` 或者 `false`

> 字符串操作

函数                         | 含义
:--------------------       | :-------
`$SUB(E1, from, to)`        | 字符串截取，从 `from` 到 `to`
`$EQ(E1, E2)`               | 字符串是否相等，返回 `true` 或者 `false`
`$UPPER(E)`                 | 将 `E` 转换为大写
`$LOWER(E)`                 | 将 `E` 转换为小写
`$LEN(E)`                   | 返回字符串 `E` 的长度

> 通用功能类函数

函数                         | 含义
:--------------------       | :-------
`$ENCODE_BASE64(E)`         | 将字符串 E 按 base64 编码压缩
`$DECODE_BASE64(E)`         | 将字符串 E 按 base64 编码解压
`$MD5(E)`                   | 计算 `E` 的 md5 值
`$RANDI(E1, E2)`            | 返回大于 `E1` 并小于 `E2` 的随机数值
`$UNIXT(y, m, d, h, min, sec)` | 指定年，月，日，小时，分钟，秒，返回相应的 UNIX TIME
`$INT(E1, E2, E3)`          | 进制转换，将 `E2` 进制的数字（字符串形式） `E1` 转换成 `E3` 进制并返回，`E2`, `E3` 可选，`E2` 默认为 `10`

> 限速相关

函数                         | 含义
:--------------------       | :-------
`$LIMIT_RATE_AFTER(E1, E2)` | 限速策略设置，发送 `E1` 大小单位为 `E2` 字节数据后，进行限速，`E2` 可选值有：`k`（KB)，`m`（MB）
`$LIMIT_RATE(E1, E2)`       | 限速策略设置，按 `E1` 大小每秒进行限速，单位为 `E2`，`E2` 可选值有：`k`（KB)，`m`（MB）

注 1：若当前请求带有请求体的情况下，`$SET_METHOD(E)` 不允许设置为除 `PUT` 和 `POST` 以外的方法。

其中 `E[n]` 代表合法的表达式，也就是说函数可以相互嵌套，例如规则可以为：

`/$SUB($DECODE_BASE64($_HEADER_foo), $_GET_from, $_GET_to)/`；布尔函数包括 `WHEN
, ALL, ANY` 都是短路的，譬如 `$WHEN($ALL(), $EXIT(403))` 不会返回 `403`，因为
`$ALL()` 的值为 `false`

使用 `''` 能对变量进行转义，使其不被解释，例如以下规则：

```
/foo/'$_HOST'
```

会被转换为:

```
/foo/$_HOST
```

对于含有请求参数的规则，原先的请求参数会附加到后面，如果不希望这样，可以放置一个
'?' 在规则的最后面，例如这样：

```
/foo?bar=$1?
```

特别地，如果重写后的 URL 没有参数，那么此时需要在最后加两个 `?`，例如这样：

```
/foo??
```

**变量**

变量以 `$_` 开头，这些变量都是对此次请求上下文中一些参数的映射，譬如 `$_HOST` 对应此次请求头中的 `Host` 字段，`$_GET_foo` 对应此次请求 URL 参数 foo 的值，等等。若将变量内插到重写后的 URL 中，譬如 rewrite `/$_GET_foo/bar`，如果请求参数中没有 `foo`则视为此次 rewrite 失败；若将变量放在函数中，则由该函数确定返回的值，例如 `$NOT($_GET_foo)`，如果参数中包含 `foo`，则返回 `false`，否则返回`true`。目前支持的变量有：

变量                  | 含义
:-------------------- | :-------
`$_IP`                | 客户端 IP
`$_HOST`              | 请求头中的 Host 字段
`$_HOST_n`            | `$_HOST_n` 指 Host 中的第 n 个子域，例如对于 foo.bar.baz.com：`$_HOST_1 = foo，$_HOST_2 = bar，$_HOST_3 = baz`
`$_GET_name`          | 使用 Query String 中的变量（无需 urldecode）
`$_POST_name`         | 使用 POST 表单中的变量（只支持 www form ）
`$_HEADER_name`       | 使用 Header 中的值，注意 name 全为小写
`$_COOKIE_name`       | 使用 Cookie 中的字段值
`$_RANDOM`            | 随机字符串，字符集为 `[a-zA-Z0-9]`，默认 8 位
`$_RANDOM_n`          | `n` 位随机字符串，其中 `1 <= n <= 32`
`$_URI`               | 请求的 URI，不包含参数
`$_QUERY`             | Query String，不带前缀 '?'
`$_METHOD`            | GET/POST/PUT/POST/DELETE/OPTIONS/PATCH
`$_SCHEME`            | HTTP/HTTPS
`$_TIME`              | 获取当前服务器时间，格式为 UNIX TIME
`$_BODY`              | 获取当前请求的 BODY（请求体）内容，大小限制为 16KB

**字符串常量**

字符串常量有两种形式，第一种就是普通的字符串例如`/foo/bar`，但是如果字符串中包含了一些特殊字符，例如空白字符将被省略，例如 `$(),'` 这些字符有特殊的用途，不能被直接使用，要使用这些特殊的字符，要加 `\` 前缀对其转义，例如 `/foo/bar\,`；第二种是加单引号的字符串 `''`，单引号中的字符只有 `\` 是特殊转义字符前缀，其他的都视为普通字符，例如这样一条 rewrite 规则 `/foo/'$\\,\''`，rewrite 过后对应 `/foo/$\,'`。

**break**

除了能勾选 `break 选项` 指定是否 `break`，也可以直接在 rewrite 规则最后加上 `$$` 表示 `break`。


###配置引导

登陆 [管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 自定义 Rewrite，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-custom-rewrite.png" height="490" width="800" />


###经典案例

**1、自定义防盗链**

需求描述：将请求 URL 按照一定的算法规则实现 token 防盗链，使用自定义 rewrite 来实现，会更加灵活和高效。token 防盗链详解请参见：https://blog.upyun.com/?p=1177

请求 URL 示例：

```
/test.ts?key=68ddbe535557d6630a19cebde0cb9252&t=1481106349
```

以上请求 URL 是按照如下算法生成的，参见如下算法说明：

```
<?php
$etime = time() + 600; // 授权 10 分钟后过期 
$key = 'abc'; // token 防盗链密钥
$path = '/test.ts'; // 文件相对路径
$url = $path.'?key='.md5($key.$etime.$path).'&t='.$etime;
echo $url; 
?>
```

CDN 节点需要进行两层判断，一层就是对时间戳进行比较，另外一层就是按照约定的规则生成验证信息并进行比较，看是否一致。按照要求，可编写如下规则集来实现以上要求：

```
rules:
{
  "rule": "$WHEN($MATCH($_URI, '.ts$'),$OR($NOT($_GET_t),$NOT($_GET_key)))$EXIT(403)",
  "pattern": ""
}, 
{
   "rule": "$WHEN($MATCH($_URI, '.ts$'),$GT($_TIME, $_GET_t))$EXIT(401)",
   "pattern": ""
}, 
{
  "rule": "$WHEN(($MATCH($_URI, '.ts$'),$NOT($EQ($MD5('abc'$_GET_t$_URI),$LOWER($_GET_key))))$EXIT(403)",
  "pattern": ""
}
```
规则释义：

第一条：当文件后缀为 ts 时，判断请求 URL 里面是否存在 t 和 key 参数，如果其中一个不存在，则返回 403；

第二条：当文件后缀为 ts 时，判断当前 CDN 节点 UNIX 时间是否大于请求参数里面的时间戳（也即参数 t 的值），否则返回 401；

第三条：当文件后缀为 ts 时，CDN 节点按照约定的算法规则生成验证信息（也即 MD5 值），判断生成的 MD5 值和请求参数 key 的值是否一致，不一致则返回 403；

**2、边缘重定向**

示例一：匹配 `cookie` 进行跳转

```
"rule": "$WHEN($NOT($EQ($_URI, /live.html)), $NOT($_COOKIE_token))$REDIRECT(http://test.example.com/no_token.html)",
"pattern": ""
```

规则释义：首先匹配请求 `URI` 是否为 `/live.html`,当 `cookie` 中 `token` 值为空时，则跳转到指定到地址 `http://test.example.com/no_token.html`。

**3、URL 改写**

示例一：目录及参数改写

将请求 URL 转换为带参数的动态 URL，例如请求的 URL 为：
```
http://example.com/pay/25/8/...
```

需要 CDN 边缘节点转换为如下请求：

```
http://example.com/pay.php?payid=25&categoryid=8...
```
这个时候，`pattern` 部分需要提取目录数字，需要生成 `$1` 和 `$2` 这样的变量，如下规则所示：

```
"rule": "/product.php?productid=$1&categoryid=$2",
"pattern": "^product/([0-9]+)/([0-9]+)/(.*?).html$"
```

规则释义：当解析的 url 符合规则 `^product/([0-9]+)/([0-9]+)/(.*?).html$ `，那么将请求导向到 `/product.php?productid=$1&categoryid=$2 `。

```
也即将 http://example.com/pay/25/8/... 转换为 http://example.com/pay.php?payid=25&categoryid=8...
```

示例二：文件名改写

```
pattern: /(.*)/playlist\.m3u8$
rule: /$1'.m3u8'
```

规则释义：当访问地址为 `http://domain/app/stream/playlist.m3u8` 时，将访问地址改写为 `http://domain/app/stream.m3u8`。

应用场景：在直播应用场景中，因为客户端机制无法或者不方便升级的情况，可以通过 URL 改写，将 `/stream/playlist.m3u8` 改为 `/stream.m3u8`，其中 `app` 代表发布点，`stream` 代表流名。


**4、请求/响应头修改**

示例一：添加、删除请求头

```
"rule": "$ADD_REQ_HEADER(X-From-Cdn, upyun)",
"pattern": ""
```
规则释义：添加请求头 `X-From-Cdn:upyun`，识别 CDN 厂商。

```
"rule": "$DEL_REQ_HEADER(Accept-Encoding) ",
"pattern": ""
```
规则释义：删除请求头 `Accept-Encoding` ，无论终端是否发起 `gzip` 的请求，都进行 `ungzip` 的返回。


示例二：添加响应头

```
"rule": "$ADD_RSP_HEADER(CDN, UPYUN, 1) ",
"pattern": ""
```

规则释义：添加响应头 `CDN`  为 `UPYUN`, `1` 表示会覆盖掉已有的响应头，通过响应头也可以识别 CDN 厂商。


**5、URL 限速**

假如请求的 URL 为：`http://test.example.com/mp4/4E10F356C0FEAD359C33DC5901307461-10.mp4` ，需要对该类型文件进行限速，限速要求为：前 20MB 不限速，20MB 之后限速 800 KB/s，规则可这样编写：

```
"rule": "$WHEN($1, $EQ($_HOST, 'test.example.com'))$LIMIT_RATE_AFTER(20, m)$LIMIT_RATE(800, k)",
"pattern": "^(/).+-10\.mp4$"
```

规则释义：当 `$1` 为真，且满足请求 `HOST` 为 `test.example.com ` 时 ，开始 20MB 不限速，后面限制到 `800KB/s`。

**6、大小写转换**

该部分会使用到`$LOWER(E)` 函数，直接看规则：

```
"rule": "$WHEN($MATCH($LOWER($_URI), /uptest.png)) /UPtest.png",
"pattern": ""
```

规则释义：首先是获取请求 `URI` ，使用 `$LOWER(E)` 函数将 `URI` 转换为小写，和 `/uptest.png` 进行匹配，匹配成功，则直接将 `URI` 改写为`/UPtest.png`。如果需要将字符串转换为大写，使用 `$UPPER(E)` 函数。

**7、数值比较**

也即将字符串或者数字进行比较。例如： `A` 和 `B` 进行比较，是否大于、大于等于、相等等逻辑，参见如下规则示例：

```
"rule": "$GT($_TIME,$ADD($INT($_GET_t,16,10),3600))",
"pattern": ""
```

规则释义：将请求 URL 中的 t 参数 由 16 进制转换为 10 进制，然后加上 3600 ，将相加得到的值和当前系统时间进行比较。这里使用到了 `$GT(E1，E2）` 函数，也即 `E1` 是否大于 `E2`，大于即返回 true 。另外，等于使用 `$EQ(E1，E2）` 函数，大于等于使用 `$GE(E1，E2）` 函数。


----------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
