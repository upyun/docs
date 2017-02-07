##缓存相关

**CDN 默认的缓存策略是怎样的？**

在没有匹配到自定义缓存规则且源站也没有返回任何有效缓存头的情况下，我们的默认配置策略如下：

1）针对静态资源，所有正常状态码（大于等于 200 小于 400）均缓存 8 天。特别地，301 响应缓存 2 小时，302 响应缓存 20 分钟

2）针对动态资源，程序会自动识别，则不进行缓存

3）对于其他大于等于 400 的不正常响应，则不进行缓存

**CDN 资源是如何控制缓存过期时间的？**

CDN 缓存组件严格遵守 HTTP 的标准协议，具体的缓存时间由 HTTP  响应头里面的 `Cache-Control` 和 `Expires` 响应头控制。

注意：由于 `Cache-Control` 使用的是相对时间来指定的过期时间，所以一般使用 `Cache-Control ` 来指定过期时间。


**缓存配置优先级是怎样的？**

不缓存配置（后台设置） > 自定义缓存配置（后台配置） > 源站缓存配置 > 默认缓存策略。以上缓存配置优先级目前不支持顺序调节，后续我们会考虑支持。

**如何判断设置的缓存时间已经生效？**

可以通过 `Cache-Control：max-age=xxxx` 中 `max-age` 的值和自定义缓存配置的值进行比较即可，如果一致则说明已经生效。详情可参见 [缓存验证](http://docs.upyun.com/cdn/guide/#_6)。

**已经缓存在 CDN 节点的文件如何更新，是实时的么？？**

不是实时的，缓存更新分为主动更新和被动更新两种方式。主动更新主要是指同名文件在源站更新之后，需要在管理控制台手动去刷新文件，或者通过 API 接口完成；被动刷新是等文件在 CDN 节点的缓存过期之后回源拉取最新的文件。

**CDN 可以缓存哪些文件？**

一般情况下，CDN 除了对于 PHP、JSP、ASP 等动态文件不能缓存外 ，其他文件都可以缓存。

**动态网页文件可以缓存吗？**

不可以。又拍云 CDN 会针对文件自动进行动静分离，不会对动态网页进行缓存。

**HTTPS 和 HTTP 可以共享一份缓存资源吗？**

如果在管理控制后台回源方式选择［协议跟随］，就不共享。不开启［协议跟随］，则共享。


##刷新相关

**缓存刷新和文件预热有何区别？**

缓存刷新：是指通过管理控制台或者 API 接口的方式将缓存在 CDN 节点的资源文件删除或致过期处理，新的请求过来之后会回源站服务器获取新的文件。

文件预热：是提前将资源文件缓存至 CDN 边缘节点，新的请求过来之后可以直接命中，无需回源服务器获取资源文件，可以有效降低源站压力，提升最终用户首次访问体验。


**规则刷新和 URL 刷新有什么区别？**

规则刷新：是指源站批量更新文件时，例如某个目录或者某种类型的文件，需要批量刷新文件，可以通过规则刷新来进行。规则示例：`http://example.com/video/*`、`http://example.com/image/*.png`。

URL 刷新：是指源站更新少量资源文件时，可以通过 URL 刷新来进行刷新。示例：`http://example.com/image/logo.png`。

详细介绍，请参见 [缓存刷新](http://docs.upyun.com/cdn/basic/#_3)。


**缓存刷新的生效时间是多少？**

缓存刷新的全网生效时间一般是 `10` 分钟内刷新完成。

**使用了又拍云对象存储服务时，文件更新需要手动刷新吗？**

不需要。使用了又拍云对象存储服务时，存储中资源更新（替换或删除）时会自动触发 CDN 节点刷新缓存，会在 5 分钟内生效，所以您无需进行手动刷新操作。

**发现从 CDN 访问的资源文件和源站资源不一致怎么办？**

通过管理控制台或 API 接口执行手动刷新缓存。

##性能相关

**缓存命中率低的原因可能有哪些？**

1）缓存配置问题，如缓存过期时间设置得较短；

2）`Http Header` 导致无法缓存，请检查源站  `Cache-Control ` 或  `Expires ` 的设置;

3）网站内容基本上都是动态内容，可缓存的内容少;

4）网站访问量低，文件热度较低，导致频繁回源。

**如何查看服务的缓存命中率？**

可以去又拍云管理控制台“监控”模块，可以根据服务、域名、地区、运营商、时间等维度查看历史缓存命中率。详见文档 [性能监控](http://docs.upyun.com/cdn/basic/#_5)。


**如何判断资源文件已经缓存命中？**

可通过工具查看 `Response Headers` 中 `X-Cache` 信息，如果字段值里有至少有一个 `HIT` ，则表明已经命中缓存。例如 `X-Cache:MISS from mix-hz-fdi-169; HIT(R) from ctn-zj-lna2-003`，说明已经命中缓存。

**如何提高文件缓存命中率？**

 1）合理的配置缓存过期时间，根据业务情况，适当提高文件的缓存过期时间
 
 2）在不影响业务访问的情况下，可以配置 `参数不跟随` 特性

**支持 GZIP 压缩吗？**

又拍云 CDN 默认开启了 `GZIP` 压缩特性。支持情况可参见文档 [GZIP 压缩](http://docs.upyun.com/cdn/advanced/#_3)。


**回源带宽过大，如何有效降低回源带宽？**

1）检查缓存过期时间配置是否生效

2）适当提高文件的缓存过期时间，减少回源频度

3）针对热度较高的资源文件，可以提前针对这些文件进行预热操作

4）根据源站情况，合理配置回源线路，具体需要联系技术支持


##功能相关

**什么是静态和动态网页？**

静态网页：网站设计中，纯粹 HTML 格式的网页通常被称为“静态网页”。而对于 CDN  来说静态网页是运行于客户端的程序、网页、插件、组件，例如 HTML 页面、Flash、JavaScript、VBScript 等等，不会随着客户的改变发生变化。

动态网页：是在服务器端运行的程序、网页、组件，它们会随不同客户、不同时间、不同操作，返回不同的网页，例如 ASP、PHP、JSP、ASP、CGI 等，通常动态网页节点不会进行缓存。


**支持网页图片、动态内容、大文件下载、点播加速吗？**

支持。又拍云全网加速默认支持网页图片、动态内容、大文件下载、流媒体点播加速等应用场景。详细介绍请参见 [应用场景](http://docs.upyun.com/cdn/#_5) 。

**CDN 支持泛域名加速么？**

支持。除常规形式的域名外（包括顶级域名），我们还支持泛域名绑定，比如 `*.yourdomain.com`；特别地，其中 `*` 最多支持匹配 4 层：

    [v] *.yourdomain.com => foo.yourdomain.com
    [v] *.yourdomain.com => bar.yourdomain.com
    [v] *.yourdomain.com => foo.bar.yourdomain.com
    [v] *.yourdomain.com => foo.bar.baz.yourdomain.com
    [v] *.yourdomain.com => foo.bar.baz.qux.yourdomain.com
    [x] *.yourdomain.com => foo.bar.baz.qux.quxx.yourdomain.com
    
    [v] *.bar.yourdomain.com => foo.bar.yourdomain.com
    [v] *.bar.yourdomain.com => baz.foo.bar.yourdomain.com
    [x] *.bar.yourdomain.com => foo.bar.baz.yourdomain.com

**CDN 可以针对大文件进行分段缓存吗？**

支持。我们针对已经使用又拍云对象存储服务的客户已经默认开启了分段缓存特性；针对使用自主源站的 CDN 服务，用户可以针对特定服务有选择性的开启和关闭此特性。

**CDN 资源监控的维度包括哪些？**

 1）带宽及流量查询：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，带宽及流量的曲线图，以及运营商、地区分布及排名情况；
 
 2）请求数查询：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，用户请求数的曲线图；
 
 3）回源统计：您可以查看指定时间区间、域名、服务名等条件下，带宽及流量曲线以及源站流量分布；

 4）查询明细数据：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，查询每日的流量、请求量、静态请求、动态请求、HTTPS
   请求、峰值带宽、峰值带宽时间等明细数据；
   
 5）导出 Excel 报表：您可以查询指定时间区间、域名、服务名、地区、运营商等条件下，可以导出 5 分钟粒度详细的请求数、流量、带宽、峰值带宽时间等详细数据。

**如何获取最终用户 IP ？**

又拍云 CDN 回客户源的时候会带上 `X-Real-IP` 和 `X-Forwarded-For` 的请求头下去，值为用户实际访问 CDN 的来源 IP 地址。特别地，为了兼容部分服务端程序，我们额外还提供了 `Client-IP` 请求头的支持，其值和 `X-Real-IP`、`X-Forwarded-For` 相同。

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


**CDN 回源是否支持重定向跟随？**

支持。源站响应 301/302，CDN会自动跟随 302 到指定 location 地址获取文件并进行缓存，加速用户访问体验。

**CDN 是否支持非标准端口的加速，比如 8080？**

支持。默认开启了 80 端口，其他非标准端口需要额外申请才可开放。


**支持视频拖拉吗？**

支持。目前仅支持 FLV 和 MP4 格式的视频头拖拉，其中 FLV 按字节拖拉，MP4 按时间拖拉。更多了解请参见 [视频拖拉文档](http://docs.upyun.com/cdn/advanced/#_4) 。


**是否支持忽略参数缓存？**

支持。默认是参数跟随，需要去管理控制台开启。

**是否支持跨域访问配置？**

支持。详细配置参见 [跨域配置文档](http://docs.upyun.com/cdn/advanced/#_6) 。


##安全相关

**支持哪些防盗链方式？**

初级的防盗链方式包括 IP 禁用、地区访问限制、User-Agent 防盗链、Referer 防盗链；高级的防盗链方式包括 Token 防盗链、回源鉴权。更多了解请参见 [访问控制文档](http://docs.upyun.com/cdn/advanced/#_1)。


**防盗链的设置，对网站有什么影响？**

设置防盗链即设置 User-Agent、Referer、Token 等防盗链，网站访问者只有合法的 User-Agent、Referer、Token 才能进行访问，避免恶意请求导致的流量突增。

**支持哪些安全防护方式？**

支持 WAF 防护、CC 防护、IP 访问限制等应用层安全防护措施。更多了解请参见 [安全防护](http://docs.upyun.com/cdn/advanced/#_2)。

##资源相关

**CDN 服务的节点分布是怎样的？**

截至目前为止，目前在国内 26 个省份部署有 CDN 节点，节点数量超过 150 个，覆盖的运营商包括电信、联通、移动、科技网、教育网、长宽、华数等。

**支持海外加速吗？**

支持。目前又拍云 CDN 服务在香港、台湾、新加坡、美国、德国等地区部署有 CDN 节点。


##证书相关

**支持 HTTPS 加速吗？**

支持。详细介绍请参见 [自定义 SSL 服务文档](http://docs.upyun.com/cdn/advanced/#ssl) 。


**HTTPS 加速支持哪些类型的证书？**

目前又拍云 HTTPS 加速仅支持 PEM 格式的证书，如您申请的是其他格式的证书，需要将其转换为 PEM 格式。如下列举几种比较常见的转换方式：

1）DER 转换为 PEM

证书转化：`opensslx509 -inform der -in your-certificate.cer -out upyun-certificate.pem`

私钥转化：`openssl rsa-inform DER -outform pem -in your-privatekey.der -out upyun-privatekey.pem`

2）P7B 转换为 PEM

证书转化：`openssl pkcs7-print_certs -in your-incertificat.p7b -out upyun-certificate.cer`

获取 `upyun-certificat.cer` 里面[-----BEGIN CERTIFICATE-----， -----ENDCERTIFICATE-----] 的内容作为证书上传。

私钥转化：无私钥

3）PFX 转换为 PEM

证书转化：`openssl pkcs12-in certname.pfx -nokeys -out upyun-cert.pem`

私钥转化：`openssl pkcs12-in certname.pfx -nocerts -out upyun-key.pem -nodes`


**证书上传失败如何处理？**

1）首先确认证书格式是否为 PEM 格式，并且是否符合 `[-----BEGINCERTIFICATE-----，-----ENDCERTIFICATE-----]` 开头结尾、每行 64 个字符、最后一行不超过 64 个字符的证书规则。如果非 PEM 格式，则可按上文中的方式进行转换

2）检查私钥格式是否正确，是否以 `-----BEGIN RSA PRIVATEKEY----- `开头；如果是 `-----BEGIN PRIVATEKEY----- `开头，则可执行 `openssl rsa -in your-old.key -out upyun-new.key` 对私钥进行转换

**什么是 SNI 技术？**

SNI（ServerName Indication）定义在 RFC 4366，是用来改善 SSL(Secure Socket Layer)和 TLS(Transport Layer Security) 的一项特性，在 SSLv3/TLSv1 中被启用。它允许客户端在发起 SSL 握手请求时（即：客户端发出 SSL 请求中的 ClientHello 阶段），就提交请求的 Host 信息，使得服务器能够切换到正确的域并返回相应的证书，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-ssl-sni.png" height="100" width="300" />
 
 如果要使用 SNI ，需要客户端和服务器端同时满足条件，对于当前大多数浏览器来说都以支持，也可参考 [这里](http://serverfault.com/questions/109800/multiple-ssl-domains-on-the-same-ip-address-and-same-port) 
 
<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-ssl-sni1.png" height="200" width="400" />

**支持 HTTP/2 吗？**

又拍云当前已全平台支持 HTTP/2，并已默认开启。

HTTP/2 即超文本传输协议 2.0 ，是下一代 HTTP 协议。它由互联网工程任务组（IETF）的 Hypertext Transfer Protocol Bis (httpbis) 工作小组进行开发，以 SPDY 为原型，经过两年多的讨论和完善最终确定，优势如下：

1）HTTP/2 采用二进制格式传输数据，其在协议的解析和优化扩展上带来更多的优势和可能。

2）HTTP/2 对消息头采用 HPACK 进行压缩传输，能够节省消息头占用的网络的流量。

3）多路复用，简单说就是所有的请求可以通过一个 TCP 连接并发完成。

4）Server Push：服务端能够更快的把资源推送给客户端。

**如果想使用 HTTP/2，需要做特殊配置吗？**

因 HTTP/2 是在 HTTPS 的基础上实现的，如果想使用 HTTP/2，则域名必须支持 HTTPS 访问，所以只需按照 [自定义 SSL 服务](http://docs.upyun.com/cdn/advanced/#ssl) 中的步骤进行配置即可，其他无需做任何特殊配置。
