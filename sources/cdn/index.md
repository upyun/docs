## 概述

又拍云内容分发网络（ UPYUN Content Delivery Network），即又拍云 CDN，通过对用户源站内容进行加速分发，从而提升网站访问速度，适用于网站中的静态内容和其动态部分。除此之外，又拍云 CDN 还拥有缩略图处理、页面优化、安全防护、日志分析等特性。

## 整体架构

![UPYUN CDN Architecture](https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png_/fw/800)

## 源站类型

目前资源获取方式主要有两种：`又拍云存储` 和 `自主源站`。

其中 `又拍云存储` 表示资源都存放在又拍云分布式对象存储服务上，同时附加了 CDN 的支持，因此也拥有绝大多数又拍云 CDN 的功能；而 `自主源站` 则表示资源都存放在客户自己的源站或第三方云存储服务上，仅使用又拍云 CDN 服务。

> 关于动态页面加速

又拍云 CDN 除了静态内容的分发外也适用于全站加速，即加速的内容除了静态部分外还有很多动态页面或数据请求，通常这部分动态请求不需要进行任何服务端缓存，同时，在用户源站没有设置任何缓存策略的情况下，我们会自动对加速内容进行动静分离，设置合理的缓存时间。当然，若资源全部放置在 `又拍云存储` 的话，那么所有内容分发均是静态缓存的。

此外，`自主源站` 的 CDN 服务还默认开启了 `参数跟随` 和 `域名跟随` 特性，后续相关章节会详细介绍。

## 域名绑定

> 管理后台：服务 > 加速域名

> 源站类型：全部

----

我们默认提供了一个三级域下的子域名作为 CDN 访问域名使用，形式为 `<bucket>.b0.upaiyun.com`；当然我们也支持自定义域名的绑定，前提是该域名已经备案成功，否则无法通过域名绑定审核，另外，审核成功后会以邮件形式进行通知。

> 其中 `<bucket>` 请用实际服务名称代替，下同。

以下是关于域名绑定的一些额外（重要）说明：

* 除常规形式的域名外（包括顶级域名），我们还支持泛域名绑定，比如 `*.yourdomain.com`；特别地，其中 `*` 最多支持匹配 4 层：

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

* 添加域名绑定后，需要到域名服务商的 DNS 解析管理中，将域名的 CNAME 解析到 `<bucket>.b0.aicdn.com`。

## 回源配置

> 管理后台：服务 > 基础配置 > 回源配置

> 源站类型：自主源站

----

![CDN Source Settings](https://upyun-assets.b0.upaiyun.com/docs/cdn/cdn-source-settings.png_/fw/600)

### 回源 Host

可以选择 `域名跟随` 或者 `自定义` 回源 Host。

> 首先，先解释下什么是 `回源 Host`，它和 `回源地址` 有什么区别？

`回源 Host` 表示 CDN 回源时 HTTP 请求头中 Host 字段的值，一般是域名形式的字符串，当然也支持任意字符串形式；而 `回源地址` 则表示源站实际可访问的网络地址，可以直接填 IP 地址也可以填写域名地址，如果是域名地址，那么 CDN 在回源时会对该域名地址进行 DNS 解析，然后通过解析出来的 IP 地址再进行访问，因此若解析失败也会导致无法正常回源。

> 域名跟随

其中域名跟随表示该 CDN 服务实际回源域名总是和当前 CDN 访问域名保持一致，例如：

* 若用 `foo.yourdomain.com` 访问，那么回源 Host 就设置为 `foo.yourdomain.com`
* 若用 `bar.yourdomain.com` 访问，那么回源 Host 就设置为 `bar.yourdomain.com`

> 自定义

若选择自定义的方式，那么回源 Host 就设置为此处自定义的值。特别地，如果该自定义值为空，则该服务下的原始域名就会作为回源 Host。

### 回源方式

默认情况，我们总是会以 `HTTP 协议回源`，当然你也可以选择 `HTTPS 协议回源`，那么所有回源请求都将以 HTTPS 协议访问源站，此时源站需要开启 HTTPS 支持并配置可用的证书。特别地，当我们选择 HTTP 协议回源时，回源地址的端口号默认为 `80`，而选择 HTTPS 协议回源时，端口号默认为 `443`，当然，你也可以手动指定特殊的端口号，例如 `8080` 或 `8443`。

> 协议跟随

另外一个特别的选项是 `协议跟随`，当启用该特性时，回源协议始终会和客户端访问 CDN 的协议保持一致，即客户端用 HTTP 协议访问我们 CDN，那么回源时我们也会用 HTTP 协议进行回源，同样，若 HTTPS 访问，那么回源也是 HTTPS。

特别地，选择协议跟随后，回源地址的端口号配置将仅作用于回源协议为 HTTP 的情况，默认 `80`，当然你也可以自定义为其他值，而回源协议为 HTTPS 时端口号则始终固定为 `443`，且目前暂时不支持修改此固定值。因此，若之前选择 `HTTPS 协议回源` 且自定义设置过端口号的用户需要注意，切换到 `协议跟随` 模式下时，自定义的端口号将仅作用于回源协议为 HTTP 的情况。

### 源站线路（多选）

目前我们支持 `电信`、`移动`、`联通`、`BGP` 及 `其它` 5 种源站线路配置。用户可以根据自身源站的网络情况选择合适的源站线路，例如源站有电信和联通双线地址，那么这里可以同时选择 `电信` 和 `联通` 并分别配置上相应回源地址等信息即可。

> 需要特别注意的是，这里的源站线路指的是客户源站自身的线路情况，而不是又拍云CDN 中转机房的回源线路情况。

因此，如果客户源站是 BGP 网络，那么这里直接选 `BGP` 即可，此时，我们任意线路的中转机房都会直接回该源站。如果客户源站是长宽、教育网等小众网络，那么这里可以选择 `其他` 线路，此时，我们就会调度到 BGP 的中转机房进行回源，以此确保回源质量。

### 回源地址

同一个源站线路可以允许配置多个回源地址，其中 `回源地址` 既可以填写 IP 地址也可以填写域名地址，当然，还可以自定义设置 `端口号`。

```
另外，关于回源地址和回源 Host 的区别，以及端口号的默认值情况上面已经详细说明过了，这里就不再展开了。
```

> `线路属性` 和负载均衡策略

同一个源站线路回源地址配置中除了默认的 `主线路` 外，我们还支持 `备份线路` 的配置。默认情况下，主线路多个回源地址会以 RR 轮询方式进行调度，当且仅当所有主线路回源地址均不可达时，此时我们将使用备份线路进行转发，若备份线路有多个回源地址，那么负载均衡方式也和主线路一样，以 [Round-Robin](https://en.wikipedia.org/wiki/Round-robin_scheduling) 轮询方式进行调度。

> 轮询权重

```
轮询权重范围：整数（>=1）
```

默认情况下，轮询权重都为 1，表示所有回源地址轮询到的概率均等。当然也可以如上图那样自定义 `轮询权重`，我们看到主线路两个回源地址权重分别是 1 和 2，此时主线路集群在轮询调度中会有 1/3 的概率选中 `192.168.11.1:80` 这个节点，而选中 `192.168.11.2:80` 节点进行转发的概率则为 2/3。即当前节点轮询到的概率可按 `当前节点权重` / `集群所有节点权重之和` 这个公式计算。

> 最大失败次数和静默时间

```
最大失败次数范围：整数 (>=0) 静默时间范围：整数 (>=1)，单位秒
```

表示在一个 `静默时间 (fail_timeout)` 内如果该节点异常次数累计达到当前设置的 `最大失败次数 (max_fails)`，那么在下一个 `静默时间 (fail_timeout)` 内，我们就认为这个节点宕机了，即在这段时间内不会再将请求转发给它。特别地，当 `最大失败次数 (max_fails)` 其值为 0 时，表示不会进行失败累计，`静默时间 (fails_timeout)` 也同时失效。

合理设置最大失败次数和静默时间，有助于在后端异常的情况下最大程度减少对线上请求的影响。这里实际上跟 Nginx 核心的 upstream 模块实现机制类似，以下文档可供进一步参考： [http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server)

## 参数跟随

> 管理后台：服务 > 基础配置 > 传参跟随

> 源站类型：自主源站

----

功能开启后，将不再过滤请求中的查询字符串，并将查询字符串传递给源站；如果仅加速静态资源，建议关闭该功能。

* 关闭参数跟随：（访问）`/foo.js?a=1&b=2` => （缓存）`/foo.js` => （回源）`/foo.js`
* 开启参数跟随：（访问）`/foo.js?a=1&b=2` => （缓存）`/foo.js?a=1&b=2` => （回源）`/foo.js?a=1&b=2`


## 缓存策略

### 缓存控制逻辑

>又拍云存储

* 对于正常的状态码（大于等于 200 小于 400）：7 天 + 24 小时随机
* 对于其他大于等于 400 的不正常响应缓存 5 分钟

> 自主源站 （优先级从高到低）

```
关于动静分离策略后续章节会提到，这里直接用静态内容部分和动态内容部分区分开来了。
```

* 后台自定义缓存规则设置优先级最高，特别地，`不缓存内容` 规则优先级高于 `特殊缓存内容` 规则
* 其次是用户源站 `Cache-Control` 和 `Expires` 头，若没有匹配到自定义缓存规则，那么此时我们会尽可能遵循源站的缓存配置
* 默认策略，即没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头的情况：
    * 对于 301，302 响应
        1. 静态内容部分 301 缓存 2 小时，302 缓存 20 分钟；动态内容部分则不缓存
    * 对于其他正常状态码（大于等于 200 小于 400）
        1. 静态内容部分缓存 7 天 + 24 小时随机；动态内容部分，不进行缓存
    * 对于其他大于等于 400 的不正常响应均不进行缓存

### 自定义缓存规则

> 管理后台：服务 > 基础配置 > 缓存配置

> 源站类型：自主源站

----

> 特别地，自定义缓存规则只影响大于等于 200 小于 400 （特别地，301 和 302 除外）的这类正常的状态码响应。

自定义缓存规则目前只支持 URL 的路径（PATH）和参数（ARGS）部分，暂不支持协议和域名部分，路径部分支持 4 种形式：

1. 前缀加星号，例如 `*.jpg`
2. 后缀加星号，例如 `/d/e/*`
3. 中间加星号，例如 `/a/b/*.zip`
4. 不加任何星号，例如 `/m/foo.png`

当然，以上 1 - 4 种情况还可以选择附加上参数筛选规则，具体示例如下：

```
/m/n/*?foo&bar=123
```

其中 `?` 表示后面跟随的就是参数筛选规则，参数规则分为两类，一类只有 KEY 没有 VALUE，另一类既有 KEY 也带 VALUE（格式：KEY=VALUE）。对于只有 KEY 的情况，我们会匹配该参数 KEY 所有对应的值以及值为空这种特殊情况；后一种会精确匹配。多个参数规则也可以通过 `&` 符号连接，表示同时需要满足多种筛选规则。特别地，这里我们对参数规则配置顺序不敏感。

另外，还支持只带 `?` 的特殊规则（后面不跟任何参数筛选项），表示参数必须为空才匹配：

```
/m/n/*?
```

### 自动动静分离

针对 `自主源站` 的 CDN 服务而言，当没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头时，我们就会对回源内容进行动静识别，特别地，我们会根据响应头中的 `Content-Type` 字段和 URL 中的文件后缀名来判断是否是静态内容，其中 `Content-Type` 匹配优先级高于文件后缀名，以下是我们维护的一份 `Content-Type` 和文件后缀名列表，若满足匹配我们就认为是静态内容：

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

## GZIP 压缩

目前又拍云CDN 开启了 GZIP 压缩的相关特性，具体对应的 NGINX 配置如下：

```
gzip on;
gzip_min_length 256;
gzip_types <见下面的列表>;
gzip_disable "MSIE [1-6]\.";
gzip_vary on;
```

触发实际的 GZIP 压缩行为需要同时满足如下条件：

* `Content-Type` 满足以下列表其中之一：

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

* `Content-Length` 大于 256 字节
* 客户端请求头带了 `Accept-Encoding: gzip`

## 禁止外链

> 管理后台：服务 > 基础配置 > 外链功能

> 源站类型：全部

通过管理后台，可以一键禁止整个服务域名下所有资源的访问，特别地，此时会响应 405 状态码给客户端。

## 特殊状态码说明

> 403 Forbidden

当用户设置了例如 `IP 禁用`，`域名防盗链`，`客户端白名单`，`Token 防盗链` 等防盗链配置并且当前的请求与规则不匹配时，即会响应 403 给客户端，表示该次请求被禁止。另外，若该服务设置了自定义 403 提示图，那么此类情况下就会响应一个 302 跳转到相应的图片。

> 405 Not Allowed

正常使用中的服务，若被人为地设置为禁止外链，此时访问就会响应 405。同样，405 也支持自定义提示图设置。

> 400 Bad Request

以原始域名或绑定域名访问的时候，若服务不存在或处于禁用状态，其中的不存在可以理解为未创建或已删除，此时访问就会响应 400。特别地，此时 400 响应体是一个带有简单说明文字的 HTML 页面（例如： [http://e.b0.upaiyun.com/](http://e.b0.upaiyun.com/) ），其他情况下的 400 均表示原意，即表示请求格式错误。

## 特殊回源请求头

> `X-Real-IP` 和 `Client-IP`

又拍云 CDN 回客户源的时候会带这个 `X-Real-IP` 的请求头下去，值为用户实际访问 CDN 的来源 IP 地址。特别地，为了兼容部分服务端程序，我们额外还提供了 `Client-IP` 请求头的支持，其值和 `X-Real-IP` 相同。

#### 例1：PHP 代码

```
   <?php
        $ip = $_SERVER["HTTP_X_REAL_IP"];
        echo $ip;
```

#### 例2: Nginx 配置


```
server
    {
        listen 80;
        add_header X-Real-IP $http_x_real_ip; #增加响应头
    }
```
