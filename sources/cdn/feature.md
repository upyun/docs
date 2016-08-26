## 自定义 SSL

> 管理后台：工具箱 > SSL 服务

> 源站类型：全部

----

### 自定义 SSL 证书

用户可以上传自己的 SSL 证书到又拍云，并管理自定义域名的 HTTPS 访问。

另外，又拍云自定义 SSL 基于 SNI 实现，因此某些不支持 SNI 的旧式浏览器访问可能会有些问题。支持 SNI 的部分客户端列表如下：

* 桌面客户端
    * Internet Explorer 7 and later
    * Firefox 2
    * Opera 8 with TLS 1.1 enabled
    * Google Chrome:
        * Supported on Vista and later by default
        * OS X 10.5.7 in Chrome Version 5.0.342.0 and later
    * Safari 2.1 and later (requires OS X 10.5.6 and later or Windows Vista and later)

* 移动客户端
    * Mobile Safari for iOS 4.0
    * Android 3.0 (Honeycomb) and later
    * Windows Phone 7

**注意：**运行在 Windows XP 上的所有版本的 Internet Explorer 都不支持 SNI 。请参考[这里](http://serverfault.com/questions/109800/multiple-ssl-domains-on-the-same-ip-address-and-same-port)
### 强制 HTTPS 跳转

开启强制 HTTPS 跳转后，该域名下所有 HTTP 请求均会 301 跳转成 HTTPS 请求。

## 防盗链

> 管理后台：服务 > 防盗链

> 源站类型：全部

----

### IP 禁用

```
IP 防盗链目前暂时只支持黑名单逻辑。
```

* 若需要禁止某些 IP 访问，则可以使用 IP 禁用功能
* 支持 * 通配符，如 `10.11.12.*` 将禁止 `10.11.12.0~10.11.12.255` 的 IP 访问

### 域名防盗链

* 白名单：仅允许名单中的域名网站访问文件，其他域名网站都不允许访问
* 黑名单：仅禁止名单中的域名网站访问文件，其他域名网站都允许访问
* 支持 * 通配符，比如白名单的 `*upyun.com` 将允许 `www.upyun.com`、`abcupyun.com` 等网站访问

> 禁止 Referer 为空

特别地，默认 Referer 为空这种情况会绕过域名防盗链的逻辑。 若这里启用 `禁止 Referer 为空`，那么这类请求就直接被禁止访问了。

### 客户端白名单

```
客户端防盗链目前暂时只支持白名单逻辑。
```

* 适合手机 APP 和客户端软件等可自定义 User Agent 的应用使用
* User Agent 不区分大小写
* 支持 * 通配符

### Token 防盗链

Token 防盗链适合下载类网站或应用使用，可设置签名过期时间来控制文件的访问时限。

> Token 防盗链原理图

![](https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-token.png)

> 签名方式说明

签名：

```
_upt = MD5(token 密匙&etime&URI){中间 8 位} + etime
```

参数：

* token 密匙：用户所填的密钥
* etime：过期时间，必须是 UNIX TIME 格式，如： `1378092990`
* URI：请求地址（不包含?及后面的 Query String），如： `/dir/pic.jpg`

备注：

* 对于使用 Cookie 类无法给每个链接进行单独签名的情况，可使用 / 作为 URI 进行统一的泛签名，如：

```
MD5(token 密匙&过期时间&/){中间 8 位} + 过期时间
```

* 注意：泛签名在过期时间内，对服务域名内的所有链接资源都有效

#### 目录级别 Token 防盗链：_upp 参数

```
+----------------------+------------------------------------------------------------+
|                      | Ex. uri = '/a/b/c.jpg', ex_time = now() + 600              |
|    token secret      |                                                            |
|                      | _upt = md5(token_secret&ex_time&uri)[12:20] + ex_time      |
+----------------------+------------------------------------------------------------+
```

`_upp` 参数是用来配合 Token 防盗链的 `_upt` 参数使用的，以便支持用户可自定义目录级别进行签名，默认情况下 `_upt` 均以完整的 URI 进行签名，当 `_upp = 2` 那么就以相应的 `uri = '/a/b/'` 来进行签名。这样就很容易实现对某个目录的防盗链权限控制了。

> 示例

```
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upp=2&_upt=abcdefgh1370000600
```

例如对于以上这条结合 `_upp` 参数的 Token 防盗链请求来说，对应 `_upt` 参数正确的签名应按如下公式进行计算（其中被签名 URI 此时为 `/docs/cdn/`）：

```
MD5(token 密匙&过期时间&/docs/cdn/){中间 8 位} + 过期时间
```

> 详细说明

1. 其值应为正整数，设置范围 `0 <= x <= 20`，超过该范围或其它不合法的字符均视为无效值，此时仍然按照 URI 全路径进行匹配.
2. 当 `_upp = 0` 时，总是匹配 `/`；其它 `_upp = 1,2,3 ...` 分别对应一级目录，二级目录，三级目录 ...

例如，对于 `uri = '/a/b/c/foo.jpg'` 来说：

```
[v] /a/b/c/foo.jpg => '/a/b/c/foo.jpg'

[v] /a/b/c/foo.jpg?_upp=0 => '/'
[v] /a/b/c/foo.jpg?_upp=1 => '/a/'
[v] /a/b/c/foo.jpg?_upp=2 => '/a/b/'
[v] /a/b/c/foo.jpg?_upp=3 => '/a/b/c/'

[x] /a/b/c/foo.jpg?_upp=4 => '/a/b/c/foo.jpg'
[x] /a/b/c/foo.jpg?_upp=-1 => '/a/b/c/foo.jpg'
[x] /a/b/c/foo.jpg?_upp=abc => '/a/b/c/foo.jpg'
```

### 回源鉴权

回源鉴权是一种高级的防盗链方式，适用于对防盗链接有很高实时性要求的场景，另外地，CDN 厂商无法实现的特殊防盗链方式也推荐使用回源鉴权的方式。大概原理就是： CDN 边缘节点每次接受到请求之后，都需要回客户源站进行验证，验证通过之后才认为是合法请求，CDN 可以继续提供服务。具体配置参考[这里](https://blog.upyun.com/?p=877)

### 地区访问限制

地区访问限制是指根据加速网站的需求，允许或禁止特定区域的终端用户对网站资源的访问。即网站指定地区，允许该地区终端用户访问，而限制其他地区用户访问，或者相反。应用场景主要是一些音视频资源的播放受版权许可限制，只能在指定的区域播放，例如：某些视频节目资源只有在国内播放的版权，因此需要仅允许国内地区播放。具体配置参考[这里](https://blog.upyun.com/?p=886)

## 缓存刷新

对于 `又拍云存储` 而言，文件的替换和删除操作，我们均会自动提交缓存刷新任务进行全网刷新，特别地，原图的替换和删除，我们会自动将其对应的所有缩略图版本号也进行刷新。正常情况下全网 5 分钟内就能刷新完毕，针对漏刷的情况，可以在管理后台进行手动刷新。

对于 `自主源站` 而言，除了能够在管理后台手动刷新外，我们还提供了 [缓存刷新 API 接口](http://docs.upyun.com/api/purge/) 和全网刷新功能。

### 全路径刷新

> 管理后台：刷新 > URL 刷新

> 源站类型：全部

----

直接输入完整 URL 地址进行刷新。

### 规则匹配批量刷新

> 管理后台：刷新 > 规则刷新

> 源站类型：全部

----

功能说明：

* 规则匹配批量刷新后，新的请求只能访问原文件，因此将影响访问速度，请谨慎使用。
* 待刷新域名需绝对匹配，支持泛域名。
* 对使用 `又拍云存储` 的文件缓存刷新，会在 1 小时之内完成；对 `自主源站` 的文件缓存刷新，会在 5 ~ 10 分钟内完成。

待刷新规则说明：

* 规则中可以包含有最多一个 * 用于代替任意长度的任意字符，如
    * `/*.jpg`
    * `/d/e/*`
    * `/a/b/*.zip`
    * `/m/foo.png`
* 可以对刷新 URL 的查询参数做规则限定，支持以下四种情况（多个参数时，顺序不敏感）：
    * 不限查询参数：`/m/n*` 将会刷新符合 `/m/n*` 规则的 URL，无论 URL 是否携带参数
    * 限定参数的 KEY：`/m/n/*?foo` 将会刷新符合 `/m/n*` 规则，同时包含 foo 参数的 URL，但不限 foo 参数的值
    * 限定 KEY 和 VALUE：例如 `/m/n/*?foo=bar` 将只会刷新符合 `/m/n*` 规则，同时包含 foo 参数且值为 bar 的 URL
    * 不准携带查询参数：`/m/n*?` 将只会刷新符合 `/m/n*` 规则，且不携带任何查询参数的 URL

### 全网刷新

> 管理后台：刷新 > 全网刷新

> 源站类型：自主源站

----

全网刷新顾名思义，我们会对整个服务域名下所有的 URL 进行缓存刷新操作，即时生效。

重要说明：

* 每次刷新间隔至少 10 分钟，一天只能刷新 5 次缓存
* 全网缓存刷新会对源站产生一定压力，请谨慎操作

### _upv 参数

> 源站类型：自主源站

若开启 `参数跟随` 特性后，不同参数对应的缓存也是不同的，因此可以很方便通过参数的变换来绕过缓存，从而达到资源被动刷新的目的。但对于参数不跟随的情况来说，不同参数并不会产生不同的缓存拷贝，例如以下两条请求在又拍云 CDN 均为命中同一个资源：

```
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?foo=123&bar=456
```

因此，在主动刷新的基础上，我们额外对于这类请求开放了一个特殊的参数，即 `_upv`，带上这个参数就能绕过这个限制，直接传递到缓存服务器去，然后依次穿透到客户源站，例如：

```
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upv=02
```

注：_upv 参数取值范围：只接受 00 - 99，即 2 位 {0 - 9} 的数字，其他任何值都无效。

## 图片处理

### 自定义版本

> 管理后台：服务 > 功能配置 > 云处理 > 自定义版本

> 源站类型：全部

----

用户可以根据业务需要在管理后台配置不同的缩略图版本，对图片进行「缩略图」、「水印」、「旋转」、「格式转换」等处理，当然也可以创建图片信息版本获取图片基本信息和 EXIF 信息。

* 自定义版本的调用格式为： `图片 URL + 间隔标识符 + 自定义版本名称`
* 间隔标识符有 3 种可选，分别是：`!`（感叹号/默认值）、`-`（中划线）和 `_`（下划线）

例如：

间隔标识符为：*`_`*，缩略图版本名称为： *`width200`*

使用方式为：

```
http://upyun-assets.b0.upaiyun.com/docs/guide/sample.jpg_width200
```

### URL 作图

> 源站类型：全部

----

URL 作图是类似自定义版本作图的一种图片处理使用方式，它跟自定义版本作图不一样的是间隔标识符后面跟的是作图参数，用户使用时可以根据需要随意的搭配作图参数，因而，它更加灵活和方便。

例如：

```
http://upyun-assets.b0.upaiyun.com/docs/guide/sample.jpg_/fw/200/format/webp
```

#### 相关参数说明

| 特殊参数指令     | 示例值         | 说明     |
| --------        | --------      | -------- |
| /sq/            | 100           | 方块图，值为其大小 |
| /fw/            | 100           | 限定宽度，高度自适应 |
| /fh/            | 100           | 限定高度，宽度自适应                                                                          |
| /fwfh/          | 100x200       | 限定宽度或高度，宽高不足时不缩放 |
| /both/          | 100x200       | 固定宽度和高度，宽高不足时强行缩放 |
| /max/           | 100           | 限定最长边，短边自适应 |
| /min/           | 100           | 限定最短边，长边自适应 |
| /scale/         | 50            | 等比例缩小，可选范围 [1, 99] |
| /gifto/         | true               | 多帧 GIF 图转为单帧的 GIF |
| /rotate/        | auto, 90, 180, 270           | 根据设置角度旋转，特别地，`auto` 表示根据 EXIF 自动旋转 |
| /crop/          | `<width>x<height>a<x>a<y>`（具体请参见注 1） | 裁剪，其中 `w`、`h` 分别表示裁剪后的宽和高，`x`、`y` 表示左上角坐标 |
| /unsharp/       | true                         | 锐化 |
| /compress/      | true                         | 压缩优化 |
| /quality/       | 75                           | 设置压缩质量，可选范围[1, 99] |
| /format/        | jpg, png, webp               | 输出格式 |
| /gaussblur/     | `<radius>x<sigma>`（具体请参见注 2）         | 高斯模糊，`radius` 为模糊半径，`sigma` 为标准差 |
| /progressive/   | true                         | 渐进加载 |
| /canvas/        | `<width>x<height>a<x>a<y>`（具体参见注 1） | 画布，其中 `w`、`h` 分别表示画布的宽和高，`x`、`y` 表示左上角坐标 |
| /cvscolor/      | RRGGBBAA                     | 画布颜色，前六位为 RGB 值，最后两位为透明度，可选范围 [0, 255]，特比地，参数值都为 16 进制表示 |
| /border/        | `<width>x<height>`           | 边框， 其中 `w`、`h` 分别表示边框的宽和高 |
| /brdcolor/      | RRGGBBAA                     | 边框颜色，前六位为 RGB 值，最后两位为透明度，可选范围 [0, 255]，特比地，参数值都为 16 进制表示 |

* 注 1：由于 `/crop/` 和 `/canvas/` 参数中的 `+` 会被浏览器转义，这里特别地用 `a` 代替 `+` 字符；`a => add`。
* 注 2：高斯模糊参数 `<radius>` 是模糊半径，取值范围是 `[0, 50]`，`<sigma>` 是正态分布的标准差，必须大于 0。
* 更多作图参数，请参考 [作图参数](/cloud/image/#_7)；完整的图片处理文档，请参考 [图片处理](/cloud/image/)。

## 页面优化

### 代码压缩

> 管理后台：服务 > 高级功能 > 代码压缩

> 源站类型：全部

----

目前仅支持 HTML 代码的优化，开启后，会自动去除 HTML 文件中非必要的字符（空白、注释等），自动压缩文件大小，提升访问速度。

### 文件合并

> 源站类型：全部

---

`又拍云存储` 和 `自主源站` CDN 加速服务均支持 JS，CSS Combo（组合特性），即可以把多个 JS, CSS 请求合并成一个。演示如下：

> 特别地，我们以 `!!` 作为分隔符。

[http://upyun-assets.b0.upaiyun.com/foo/!!a.js,b.js](http://upyun-assets.b0.upaiyun.com/foo/!!a.js,b.js)

一个引用了如上 URL 的 Demo:

[http://upyun-assets.b0.upaiyun.com/jscombo.html](http://upyun-assets.b0.upaiyun.com/jscombo.html)

## 安全防护

> 管理后台：服务 > 云安全

> 源站类型：自主源站

----

### WAF 保护

能有效拦截跨站攻击、SQL 注入和其他代码执行等多种攻击方式，保护源站安全。

### HTTP 请求体大小限制

限制单次请求体的内容大小，能有效保证请求的安全性。

## 源站资源迁移

> 管理后台：服务 > 高级功能 > 镜像存储（源站资源迁移）

> 源站类型：自主源站
> 注意：镜像迁移的文件缓存时间需要大于24小时。
----

开启后，能够将源站的数据平滑地迁移到又拍云，实现服务的无缝切换。

## 原图保护密钥

> 源站类型：又拍云存储

----

* 仅对配置了缩略图版本号的 `又拍云存储` 源站有效
* 若缩略图版本号和原图保护密钥一致，那么原图保护密钥优先级高于缩略图版本号

## 文件另存为：_upd 参数

> 源站类型：全部

部分客户可能希望在浏览器中访问某个文件资源时，例如图片，能够直接下载而不是由浏览器根据特定格式打开该文件，那么，利用此参数就可以强制浏览器触发下载行为，同时该参数的值会作为文件下载后保存到本地的文件名。特别地，当其值为 true 时，保持原文件名不变。

特别地，`参数跟随` 开启的情况下，此参数无效。

具体示例如下：

```
$ curl http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upd=true -I

> HTTP/1.1 200 OK
> Content-Type: image/png
> Content-Length: 57371
> ...
> Accept-Ranges: bytes
> Content-Disposition: attachment;
```

```
$ curl http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upd=abc.png -I

> HTTP/1.1 200 OK
> Content-Type: image/png
> Content-Length: 57371
> ...
> Accept-Ranges: bytes
> Content-Disposition: attachment; filename="abc.png"
```

注： 除了一般的 ASCII 字符，`_upd` 参数也支持中文文件名。

## CORS 跨域配置

> 管理后台：服务 > 高级功能 > CORS 跨域共享

> 源站类型：全部

----

关于 CORS：[HTTP访问控制(CORS)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)。

用户可以在又拍云控制台配置符合自己站点的跨域请求策略。目前可以控制的 CORS 响应头有：

1. Access-Control-Allow-Origin
2. Access-Control-Allow-Methods
3. Access-Control-Allow-Headers
4. Access-Control-Expose-Headers
5. Access-Control-Max-Age

在管理后台的 CORS 配置面板中有以下选项：

- `允许的域`：表示允许发起跨域请求的域，即对允许的域发起的跨域请求会有 `Access-Control-Allow-Origin: http(s)://your-allow-origin.com` 这个响应头，
*注意*：在配置 `允许的域` 时，可以使用 `*` 通配符配置域，例如 `*` 表示允许所有的域，`*.upyun.com`，表示允许所有以 `.upyun.com` 结尾的域；如果不使用通配符，则需对域添加上对应的协议，例如 `http://www.upyun.com` 或者 `https://www.upyun.com`
- `Methods`：表示允许的跨域请求的方法，在当前请求的域被允许后，还要检查当前请求的方法是否被允许
- `Allow Headers`：对应 `Access-Control-Allow-Headers` 响应头
- `Expose Headers`：对应 `Access-Control-Expose-Headers` 响应头
- `缓存时间`：对应 `Access-Control-Max-Age` 响应头

## Rewrite 规则

> 管理后台：服务 > 高级功能 > Rewrite 配置

> 源站类型：全部

----

Rewrite 规则 DSL 支持函数，变量，字符串常量，用户可以将这些自由组合，完成对请求的重写。

### Rewrite 规则说明
例如：

```
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
```

其中，`pattern` 为对当前请求 URI 进行匹配的 `PCRE` 正则表达式，匹配后，产生 `$1,
$2 ...` 这样的变量；`rule` 为当前的 rewrite 规则，`break` 表示如果此次 rewrite
成功后是否要终止剩下的的 rewrite 过程。

> 在管理后台对规则进行配置时，`pattern` 对应于 `URI 提取正则`，`rule` 对应于
`Rewrite 规则`，`break` 对应于 `break 选项`，`调试模式` 选项是默认打开的。

#### 调试模式
由于 rewrite 过程会对当前请求产生副作用，*这是非常危险的*，例如随意的填写一条 rewrite
规则：

```
/foo
```

此规则会将所有请求的 URI 改写成 `/foo`，这样客户端所有请求的响应就变成 404 了，
这是您极不愿意看到的结果。我们允许对 rewrite 规则进行调试，即新增加一条规则时，`
调试模式` 开关会默认打开，这样添加规则后，即使当前请求命中该 rewrite 规则，
rewrite 过程也不会生效，只有当包含以下请求头时，rewrite 过程才会生效：

```
X-Upyun-Rewrite-Preview: true
```

使用命令行工具 `curl` 即可对规则调试：

```
curl -H "X-Upyun-Rewrite-Preview: true" http://your-domain/foo/bar.html -v
```

这样经过调试，确定该 rewrite 过程符合预期后，即可将 `调试模式` 关闭，此时该
rewrite 过程会对所有命中的请求生效。

#### 函数
函数调用以 `$` 开头，后跟一组大写字母，字母之间可以包含下划线 `_`，函数需要的参
数放在 `()` 中，以 `,` 分隔。和 Lua 中的函数调用一样，rewrite 中的函数参数个数不
能少于要求的参数个数，否则视为语法错误，然后终止 rewrite 过程，多余的参数会被求
值，但不影响调用。函数调用是有上下文的，譬如 `$WHEN` 这个函数，参数是 `bool` 类
型，参数中有不成立的条件（`false`）时，会终止 rewrite 过程。

#### 变量
变量以 `$_` 开头，这些变量都是对此次请求上下文中一些参数的映射，譬如 `$_HOST` 对
应此次请求头中的 `Host` 字段，`$_GET_foo` 对应此次请求 URL 参数 foo 的值，等等。
若将变量内插到重写后的 URL 中，譬如 rewrite `/$_GET_foo/bar`，如果请求参数中没有
`foo` 则视为此次 rewrite 失败；若将变量放在函数中，则由该函数确定返回的值，例如
`$NOT($_GET_foo)`，如果参数中包含 `foo`，则返回 `false`，否则返回`true`。

#### 字符串常量
字符串常量有两种形式，第一种就是普通的字符串例如 `/foo/bar`，但是如果字符串中包
含了一些特殊字符，例如空白字符将被省略，例如 `$(),'` 这些字符有特殊的用途，不能
被直接使用，要使用这些特殊的字符，要加 `\` 前缀对其转义，例如 `/foo/bar\,`；第二
种是加单引号的字符串 `''`，单引号中的字符只有 `\` 是特殊转义字符前缀，其他的都视
为普通字符，例如这样一条 rewrite 规则 `/foo/'$\\,\''`，rewrite 过后对应
`/foo/$\,'`。

#### break
除了能勾选 `break 选项` 指定是否 `break`，也可以直接在 rewrite 规则最后加上 `$$`
表示 `break`。


### Rewrite 变量和函数列表
`$N` 指 `PCRE` 正则匹配到的第 `N` 个分组：

 rule          | pattern
 :---          | :------
 /html/$1.html | `/(.*).html$`
 /movies/$1    | `/downloads/(.*\\.mp4)$`
 /$1/$2        | `/foo/([^/]+)/bar/(.*)`

规则中支持插入变量和函数，目前支持的变量有：

变量                  | 含义
:-------------------- | :-------
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
`$_METHOD`            | GET/POST/PUT
`$_SCHEME`            | HTTP/HTTPS
`$_TIME`              | 当前的 UNIX 时间（秒）

支持的函数有：

函数                          | 含义
:--------------------         | :-------
`$WHEN(E1, E2, ...)`          | 所有条件都成立时才进行 `rewrite`，并返回空字符串
`$NOT(E)`                     | `E` 不成立时返回 `true`， 否则返回 `false`
`$ALL(E1, E2, ...)`           | 所有条件都成立时返回 `true`，否则返回 `false`，参数个数不限
`$ANY(E1, E2, ...)`           | 其中一个条件成立时返回 `true`，否则返回 `false`，参数个数不限
`$OR(E1, E2, ...)`            | 值为第一个为真的表达式
`$SELECT(E1, E2, E3)`         | `E1` 为真时值为 `E2`，否则为 `E3`
`$ENCODE_BASE64(E)`           | 按 base64 编码压缩，例如：`$ENCODE_BASE64($_GET_foo)`
`$DECODE_BASE64(E)`           | 按 base64 编码解压，例如：`$DECODE_BASE64($_HEADER_foo)`
`$MD5(E)`                     | 计算 `E` 的 md5 值
`$SUB(E1, from, to)`          | 字符串截取，从 `from` 到 `to`
`$GT(E1, E2)`                 | 数字比较，是否大于，返回 `true` 或者 `false`
`$GE(E1, E2)`                 | 数字比较，是否大于等于，返回 `true` 或者 `false`
`$EQ(E1, E2)`                 | 字符串是否相等，返回 `true` 或者 `false`
`$UPPER(E)`                   | 将 `E` 转换为大写
`$LOWER(E)`                   | 将 `E` 转换为小写
`$MATCH(E1, E2, E3)`          | PCRE 匹配，`E2` 为要匹配的 pattern，返回 `true` 或者 `false`，`E3` 不为空时表示忽略大小写
`$PCALL(E)`                   | 保护模式下解析 `E`，失败时返回空字符串
`$ADD_REQ_HEADER(E1, E2)`     | 添加请求头 `E1` 为 `E2`
`$DEL_REQ_HEADER(E1)`         | 删除请求头 `E1`
`$ADD_RSP_HEADER(E1, E2, E3)` | 添加响应头 `E1` 为 `E2`, `E3` 不为空时表示会覆盖掉已有的响应头
`$DEL_RSP_HEADER(E1)`         | 删除响应头 `E1`
`$DEL_ARG(E1)`                | 删除请求参数 `E1`
`$REDIRECT(E1, E2)`           | 重定向地址到 `E1`，状态码为 `E2(301, 302)`
`$EXIT(E1, E2)`               | 以状态码 `E1` 退出，响应体为 `E2`
`$RANDI(E1, E2)`              | 随机返回 `[E1, E2]` 之间的整数，`E2` 为空时，返回 `[0, E1]` 之间的整数
`$REDIRECT404(E1)`           | `E1` 为需要跳转的 `URL`，当访问一个状态码是 `404`的文件，会 `302` 跳转到 `E1` 所匹配的 `URL` 去

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

### Rewrite 示例
rewrite 规则                                                       | 含义
:--------------------                                              | :-------
`$WHEN($MATCH($_URI, '^/foo/.*'))$ADD_REQ_HEADER(X-Foo, bar)`      | 在请求 URI 匹配 `^/foo/.*` 的情况下，添加请求头 `X-Foo: bar`
`$WHEN($EQ($_HOST, 'foo.com'))$ADD_REQ_HEADER(X-Foo, bar)`         | 在请求 Host 为 `foo.com` 的情况下，添加请求头 `X-Foo: bar`
`$WHEN($MATCH($_URI, '^/foo/'), $NOT($_HEADER_referer))$EXIT(403)` | 在请求的 URI 以 `/foo/` 开头并且没有 Referer 请求头时，返回 403

#### 案例说明

##### 1. 强制指定后缀文件从 HTTPS 方式访问
----

假如用户已经开启了 `SSL`，并且配置了证书，现在客户的需求是：当访问到的文件后缀是
`js` 或者 `css` 文件时，跳转到 `HTTPS`，其他文件则正常通过 `HTTP` 协议访问，那么我们在后
台则可以这样配置：

Rewrite 规则                                                        | URI 提取正则
:--------------------                                               | :-------
`$WHEN($1, $EQ($_SCHEME, http))$REDIRECT(https://$_HOST$_URI, 302)` | `\.(js|css)$`


##### 2. 设置原图返回 404
----

要求返回原图时返回 404，所有 `jpg`、`png`、`gif` 图片只能加缩略图版本访问，这个如果是在上传时可以增加 `Content-Secret` 参数设置原图保护，但是如果已经上传完后的图片要进行设置，则需要遍历所有图片文件增加这个参数才可以，这样非常麻烦，而且非常耗时，通过 `rewrite` 规则则可以轻松搞定，假如客户的间隔符是`!`，可以使用如下 `rewrite` 规则实现：

Rewrite 规则                                                        | URI 提取正则
:--------------------                                               | :-------
`$WHEN($1, $NOT($MATCH($_URI, !)))$EXIT(404)` | `\.(jpg|png|gif)$`


##### 3. 访问图片自动跳转到缩略图
----

目录 `/d/new/` 下的图片在访问原图时改写成版本号为 `nrimg` 的缩略图，! 是间隔符，正则 `^(/)d/new/` 表示匹配 `/d/new/` 目录，其中 `(/)` 在匹配后对应 `$1` 变量，这样就可以使用 `WHEN` 函数进行判断了，
正则 `[^!]+` 表示匹配不含有 `!` 的 URI，也就是原图请求，正则 `^(/)d/new/[^!]+$` 就表示匹配 /d/new/ 目录下的原图请求：


Rewrite 规则                                                        | URI 提取正则
:--------------------                                               | :-------
`$WHEN($1)$_URI!nrimg` | `^(/)d/new/[^!]+$`

##### 4. 对某个域名下特定规则的 `URL` 进行限速设置
----

有如下链接

    http://test.upyun.com/E5FE74240A5EFF409C33DC5901307461-10.mp4

匹配 `test.upyun.com` 这个域名下所有文件名为 `10.mp4` 这样的文件, 设置最大速度下载了 `20M` 以后然后限速到 `100KB/s`

Rewrite 规则                                                        | URI 提取正则
:--------------------                                               | :-------
`$WHEN($1, $EQ($_HOST, 'test.upyun.com'))$LIMIT_RATE_AFTER(20, m)$LIMIT_RATE(100, k)` | `^(/).+-10\.mp4$`


