##访问控制

**IP 禁用**

假如需要禁用某个 IP 或者某个 IP 段，则需要进行该功能配置。特别地，IP 防盗链目前暂时只支持黑名单逻辑。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > IP 禁用，点击「管理」按钮即可开始配置。如下图所示：

![upyun-cdn-access-ip](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-ip.png_/fw/800)

注意事项：

1.支持 `*` 通配符，如 `10.11.12.*` 将禁止 `10.11.12.0~10.11.12.255` 的 IP 访问

2.禁用 IP 上限 100 条，被禁止的 IP 访问资源时，将提示 403

**地区访问限制**

地区访问限制是指根据加速网站的需求，允许或禁止特定区域的终端用户对网站资源的访问。即网站指定地区，允许该地区终端用户访问，而限制其他地区用户访问，或者相反。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 地区访问限制，点击「管理」按钮即可开始配置。如下图所示：

![upyun-cdn-access-region-limit](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-region-limit.png_/fw/800)

详细配置请参见 [地区访问限制用户指南](https://blog.upyun.com/?p=886)。

**Referer 防盗链**

当请求发送到 CDN 服务器后，CDN 服务器检查 HTTP 请求头中的 Referer 字段的信息，禁止或者允许符合特定规则的 Referer 的请求。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 域名防盗链，点击「管理」按钮即可开始配置。如下图所示：

![upyun-cdn-acces-referer](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-acces-referer.png_/fw/800)

注意事项：

1.白名单：仅允许名单中的域名网站访问文件，其他域名网站都不允许访问

2.黑名单：仅禁止名单中的域名网站访问文件，其他域名网站都允许访问

3.支持 `*` 通配符，比如白名单的 `*upyun.com` 将允许 `www.upyun.com`、`abcupyun.com` 等网站访问

4.特别地，默认 `允许 Referer 为空` 这种情况会绕过防盗链的逻辑，若这里启用 `禁止 Referer 为空`，那么这类请求就直接被禁止访问了

**User-Agent 防盗链**

只允许特定的浏览器或者带有特殊 `User-Agent` 标识的专属的客户端进行访问。目前暂时只支持白名单逻辑。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > 客户端防盗链，点击「管理」按钮即可开始配置。如下图所示：

![upyun-cdn-access-user-agent](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-user-agent.png_/fw/800)

注意事项：

1.添加数量上限 50 条

2.`User-Agent` 不区分大小写，且支持 `*` 通配符

3.`User-Agent` 防盗链的优先级高于 Referer 防盗链


**Token 防盗链**

可设置签名过期时间来控制文件的访问时限。Token 防盗链的目的是使得每个请求的 URL 都具有一定的“时效性”，因此 URL 本身需要携带过期时间相关的信息，同时还需要确保这个过期时间，不能被恶意修改，因此采用 md5 算法，将密文（通常是一个普通字符串）、过期时间、文件路径（过期时间和文件路径通常是有对应关系的，因此也参与 md5 的计算）等信息所计算的 md5 值也加入到 URL 中，CDN 节点在验证请求时，除了验证过期时间，同时还会验证该 md5 值是否匹配，对于不匹配的 md5，说明 URL 被篡改，即使请求未过期也需要禁止服务。

实现原理

![upyun-cdn-access-token](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-token.png_/fw/800)

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 防盗链 > Token 防盗链，点击「管理」按钮即可开始配置。如下图所示：

![upyun-cdn-access-token-miyao](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-token-miyao.png_/fw/800)

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

![upyun-cdn-access-back-to-source-auth](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-back-to-source-auth.png_/fw/800)

详细配置请参见 [回源鉴权用户指南](https://blog.upyun.com/?p=877)。

**外链功能**

通过管理后台，可以一键禁止整个服务域名下所有资源的访问，特别地，此时会响应 405 状态码给客户端。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 基础配置 > 外链功能，关闭外链功能。如下图所示：

![upyun-cdn-access-wailian](http://upyun-assets.b0.upaiyun.com/docs/cdn/access-control/upyun-cdn-access-wailian.png_/fw/800)

注意事项：

1.服务创建完毕之后，外链功能默认开启
2.关闭外链功能，会导致资源无法访问，请谨慎操作


----------

##安全防护

**WAF 防护**

WAF 防护主要防护的是来自对网站源站的动态数据攻击，可防护的攻击类型包括 SQL 注入、XSS 攻击、CSRF 攻击、恶意爬虫、扫描器、远程文件包含等攻击。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > WAF 防护，开启 WAF 保护即可。如下图所示：

![upyun-cdn-security-waf](http://upyun-assets.b0.upaiyun.com/docs/cdn/security-protection/upyun-cdn-security-waf.png_/fw/800)

注意事项：适用于动态资源加速，全静态资源加速建议不启用该功能

**CC 防护**

CC 防护主要是针对网络安全领域中的 CC 攻击而进行的一种应用层攻击防护，该功能通过自定义匹配规则对目标资源进行监控，当请求频率达到触发频率时对疑似攻击请求进行校验，校验通过则允许访问；校验不通过，则直接禁止访问。


配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 云安全 > CC 防护，点击「管理」按钮即可开始配置。如下图所示：

![upyun-cdn-security-cc](http://upyun-assets.b0.upaiyun.com/docs/cdn/security-protection/upyun-cdn-security-cc.png_/fw/800)

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

![upyun-cdn-security-request-limit](http://upyun-assets.b0.upaiyun.com/docs/cdn/security-protection/upyun-cdn-security-request-limit.png_/fw/800)


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

![upyun-cdn-performance-daimayasuo](http://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-performance-daimayasuo.png_/fw/800)

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

![upyun-cdn-performance-param-management](http://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-performance-param-management.png_/fw/800)

注意：参数跟随功能，可以配合缓存配置功能来使用。


----------


##视频拖拉

视频拖拉功能用于视频点播的增强体验，方便最终用户通过拖拉播放器的进度条，观看任意起始位置的视频，而无需从头观看。该功能还可以扩展为跳过片头、续播、预播放等功能。发送拖拉播放进度时，客户端发起的请求类似 `http://video.upyun.com/test.mp4?start=100` 这样的 URL 请求。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 视频拖拉，点击管理即可开始配置。如下图所示：

![upyun-cdn-video-drag](http://upyun-assets.b0.upaiyun.com/docs/cdn/video-drag/upyun-cdn-video-drag.png_/fw/800)

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

![upyun-cdn-monitor-storage](http://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-storage.png_/fw/800)

 - 终端用户访问资源；
 - 又拍云 CDN 节点从用户自主源站获取资源；
 - 返回资源给终端，并持久化存储到又拍云存储平台；
 - 终端用户再次访问时，将直接从又拍云获取资源，不再回源获取；
 - 随着终端用户的不断访问，源站的资源逐步自动存储到又拍云存储平台。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 镜像存储，点击开启即可开始配置。如下图所示：

![upyun-cdn-monitor-storage-management](http://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-storage-management.png_/fw/800)

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

![upyun-cdn-cors-config](http://upyun-assets.b0.upaiyun.com/docs/cdn/cors-config/upyun-cdn-cors-config.png_/fw/800)

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


##自定义 SSL 服务

又拍云自定义 SSL 服务建立在自主研发的内容分发网络的基础之上，全面支持了 HTTPS 协议加速。通过会话重用及会话保持、在线证书状态检查协议优化、SSL 协议握手优化等技术手段，全面提升了 HTTPS 协议加速性能；在 [又拍云管理控制台](https://console.upyun.com/login/)，用户可自主化部署和管理 SSL 证书，并一键开启 HTTPS 加速服务。

配置引导

如用户已有证书，可以直接自行添加证书进行部署即可；如果尚未申请证书，可以去第三方机构申请证书或者使用又拍云默认域名证书。证书管理模块主要用于管理用户的 SSL 证书，方便用户录入、查看及应用 SSL 证书。证书管理及相关配置，参见如下步骤：

第一步：进入 SSL 服务，工具箱 -> SSL 服务

![upyun-cdn-custom-ssl](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-ssl/upyun-cdn-custom-ssl.png_/fw/800)

第二步：添加 SSL 证书，点击右上角的 `添加 SSL 证书` ，如下图所示：

![upyun-cdn-custom-ssl-inputssl](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-ssl/upyun-cdn-custom-ssl-inputssl.png_/fw/800)

粘贴证书内容:

![upyun-cdn-custom-ssl-inputssl1](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-ssl/upyun-cdn-custom-ssl-inputssl1.png_/fw/800)

点击 `保持` 之后，可查看 SSL 证书信息如下图所示：

![upyun-cdn-custom-ssl-see-ssl](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-ssl/upyun-cdn-custom-ssl-see-ssl.png_/fw/800)

第三步：在证书列表中，选择刚添加的证书，点击 `管理` ，选择开启 HTTPS 访问 ，如下图所示：

![upyun-cdn-custom-ssl-management](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-ssl/upyun-cdn-custom-ssl-management.png_/fw/800)

HTTPS 配置：可开启 HTTPS 访问和强制 HTTPS

![upyun-cdn-custom-ssl-config](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-ssl/upyun-cdn-custom-ssl-config.png_/fw/800)

注意事项：

又拍云自定义 SSL 服务是基于 SNI 技术实现的，因此某些不支持 SNI 的浏览器或客户端访问可能出现问题。支持 SNI 的客户端列表如下：

 - Internet Explorer 7 and later
 - Firefox 2
 - Opera 8 with TLS 1.1 enabled
 - Google Chrome:
 - Supported on Vista and later by default
 - OS X 10.5.7 in Chrome Version 5.0.342.0 and later
 - Safari 2.1 and later (requires OS X 10.5.6 and later or Windows Vista
   and later)
 - Mobile Safari for iOS 4.0
 - Android 3.0 (Honeycomb) and later
 - Windows Phone 7

声明：运行在 Windows XP 上的所有版本的 Internet Explorer 都不支持 SNI 。详细请参考[这里](http://serverfault.com/questions/109800/multiple-ssl-domains-on-the-same-ip-address-and-same-port)。


##自定义 Rewrite

自定义 Rewrite 功能是又拍云 Rewrite DSL（ Domain Specific Language） 实现对 URL 的重写、修改请求头及请求参数等逻辑。该功能主要面向开发者使用。Rewrite 规则支持函数、变量、字符串常量，用户可以将这些自由组合，完成对请求的重写。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 自定义 Rewrite，点击管理即可开始配置。如下图所示：

![upyun-cdn-custom-rewrite](http://upyun-assets.b0.upaiyun.com/docs/cdn/custom-rewrite/upyun-cdn-custom-rewrite.png_/fw/800)

**规则说明**

    -- JSON
    [
      {
        "rule": "$WHEN($MATCH($LOWER($_HEADER_cache_control), only-if-cached))$DEL_REQ_HEADER(Cache-Control)",
        "pattern": "",
        "break": false
      },
      {
        "rule": "/movies/$1",
        "pattern": "/download/(.*)$",
        "break": true
      },
      {
        "rule": "$WHEN($EQ($_HOST, 'images.foo.com'))/$1",
        "pattern": "^/images/(.*)$",
        "break": true
      }
    ]

其中，` pattern`  为对当前请求 URI 进行匹配的 ` PCRE`  正则表达式，匹配后，产生 ` $1, $2 ...`  这样的变量；` rule ` 为当前的 rewrite 规则，` break`  表示如果此次 rewrite 成功后是否要终止剩下的的 rewrite 过程。

>  在管理后台对规则进行配置时，`pattern `对应于 URI 提取正则，`rule` 对应于 Rewrite 规则，`break` 对应于 `break` 选项，`调试模式 `选项是默认打开的。

**调试模式**

由于 rewrite 过程会对当前请求产生副作用，这是非常危险的，例如随意的填写一条 rewrite 规则：
 > /foo

此规则会将所有请求的 URI 改写成 `/foo`，这样客户端所有请求的响应就变成 404 了， 这是您极不愿意看到的结果。我们允许对 rewrite 规则进行调试，即新增加一条规则时，`调试模式` 开关会默认打开，这样添加规则后，即使当前请求命中该 rewrite 规则， rewrite 过程也不会生效，只有当包含以下请求头时，rewrite 过程才会生效：
> X-Upyun-Rewrite-Preview: true

使用命令行工具 curl 即可对规则调试：

> curl -H "X-Upyun-Rewrite-Preview: true" http://your-domain/foo/bar.html -v

这样经过调试，确定该 rewrite 过程符合预期后，即可将 `调试模式` 关闭，此时该 rewrite 过程会对所有命中的请求生效。

**函数**

函数调用以 ` $ ` 开头，后跟一组大写字母，字母之间可以包含下划线 `_`，函数需要的参 数放在` () `中，以 `,` 分隔。和 Lua 中的函数调用一样，rewrite 中的函数参数个数不 能少于要求的参数个数，否则视为语法错误，然后终止 rewrite 过程，多余的参数会被求值，但不影响调用。函数调用是有上下文的，譬如 `$WHEN` 这个函数，参数是 `bool` 类 型，参数中有不成立的条件（`false`）时，会终止 rewrite 过程。支持的函数有：

函数                         | 含义
:--------------------       | :-------
`$WHEN(E1, E2, ...)`        | 所有条件都成立时才进行 `rewrite`，并返回空字符串
`$NOT(E)`                   | `E` 不成立时返回 `true`， 否则返回 `false`
`$ALL(E1, E2, ...)`         | 所有条件都成立时返回 `true`，否则返回 `false`，参数个数不限
`$ANY(E1, E2, ...)`         | 其中一个条件成立时返回 `true`，否则返回 `false`，参数个数不限
`$OR(E1, E2, ...)`          | 值为第一个为真的表达式
`$SELECT(E1, E2, E3)`       | `E1` 为真时值为 `E2`，否则为 `E3`
`$CAPTURE(E1, E2, E3)`      | 按 `E2` pattern 对 `E1` 进行捕获，`E3` 表示是否忽略大小写，捕获后生成 `$n.m` 形式的变量，`n` 为当前 `CAPTURE` 出现的次序，`m` 表示匹配分组；`E2` 为空时，表示对 `E1` 进行捕获，此时 `E1` 对应 `$n.0`
`$ENCODE_BASE64(E)`         | 按 base64 编码压缩，例如：`$ENCODE_BASE64($_GET_foo)`
`$DECODE_BASE64(E)`         | 按 base64 编码解压，例如：`$DECODE_BASE64($_HEADER_foo)`
`$MD5(E)`                   | 计算 `E` 的 md5 值
`$SUB(E1, from, to)`        | 字符串截取，从 `from` 到 `to`
`$GT(E1, E2)`               | 数字比较，是否大于，返回 `true` 或者 `false`
`$GE(E1, E2)`               | 数字比较，是否大于等于，返回 `true` 或者 `false`
`$EQ(E1, E2)`               | 字符串是否相等，返回 `true` 或者 `false`
`$UPPER(E)`                 | 将 `E` 转换为大写
`$LOWER(E)`                 | 将 `E` 转换为小写
`$MATCH(E1, E2)`            | PCRE 匹配，`E2` 为要匹配的 pattern，返回 `true` 或者 `false`
`$PCALL(E)`                 | 保护模式下解析 `E`，失败时返回空字符串
`$ADD_REQ_HEADER(E1, E2)`   | 添加请求头 `E1` 为 `E2`
`$DEL_REQ_HEADER(E)`        | 删除请求头 `E`
`$ADD_RSP_HEADER(E1, E2)`   | 添加响应头 `E1` 为 `E2`
`$REDIRECT(E1, E2)`         | 重定向地址到 `E1`，状态码为 `E2(301, 302)`
`$EXIT(E1, E2)`             | 以状态码 `E1` 退出，响应体为 `E2`
`$ADD(E1, E2)`              | 数字相加，返回 `E1 + E2` 结果数值
`$MOD(E1, E2)`              | 数字取余，返回 `E1 % E2` 结果数值，其中 `E2` 不能为 `0`
`$MSUB(E1, E2)`             | 数字相减，返回 `E1 - E2` 结果数值
`$MULTIPLY(E1, E2)`         | 数字相乘，返回 `E1 * E2` 结果数值
`$DIVIDE(E1, E2)`           | 数字相除，返回 `E1 / E2` 结果数值，其中 `E2` 不能为 `0`
`$BYTE(E)`                  | 返回字符 `E` 的 ASCII 编码值
`$FLOOR(E)`                 | 返回小于或等于数字 `E` 的最大整数
`$CEIL(E)`                  | 返回大于或等于数字 `E` 的最小整数
`$LIMIT_RATE_AFTER(E1, E2)` | 限速策略设置，发送 `E1` 大小单位为 `E2` 字节数据后，进行限速，`E2` 可选值有：`k`（KB)，`m`（MB）
`$LIMIT_RATE(E1, E2)`       | 限速策略设置，按 `E1` 大小每秒进行限速，单位为 `E2`，`E2` 可选值有：`k`（KB)，`m`（MB）
`$DEL_ARG(E)`               | 删除请求参数项 `E`
`$INT(E1, E2, E3)`          | 进制转换，将 `E2` 进制的数字（字符串形式） `E1` 转换成 `E3` 进制并返回，`E2`, `E3` 可选，`E2` 默认为 `10`
`$RANDI(E1, E2)`            | 返回大于 `E1` 并小于 `E2` 的随机数值
`$UNIXT(y, m, d, h, min, sec)` | 指定年，月，日，小时，分钟，秒，返回相应的 UNIX TIME
`$LEN(E)`                   | 返回字符串 `E` 的长度
`$SET_METHOD(E)`            | 修改当前 HTTP 请求方法为 `E`，`E` 可选值有：`GET`, `HEAD`, `PUT`, `POST`, `DELETE`, `OPTIONS`, `PATCH`; 注 1
`$SET_BODY(E)`              | 修改当前 HTTP 请求体的内容为 `E`，仅在 `PUT` 或 `POST` 请求下有效

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

除了能勾选 `break 选项` 指定是否 `break`，也可以直接在 rewrite 规则最后加上 `$$`
表示 `break`。

**Rewrite 示例**

rewrite 规则                                                       | 含义
:--------------------                                              | :-------
`$WHEN($MATCH($_URI, '^/foo/.*'))$ADD_REQ_HEADER(X-Foo, bar)`      | 在请求 URI 匹配 `^/foo/.*` 的情况下，添加请求头 `X-Foo: bar`
`$WHEN($EQ($_HOST, 'foo.com'))$ADD_REQ_HEADER(X-Foo, bar)`         | 在请求 Host 为 `foo.com` 的情况下，添加请求头 `X-Foo: bar`
`$WHEN($MATCH($_URI, '^/foo/'),$NOT($_HEADER_referer))$EXIT(403)` | 在请求的 URI 以 `/foo/` 开头并且没有 Referer 请求头时，返回 403


----------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
