## 自定义 SSL

> 管理后台：工具箱 > SSL 服务

> 源站类型：全部

----

### 自定义 SSL 证书

用户可以上传自己的 SSL 证书到 UPYUN，并管理自定义域名的 HTTPS 访问。

另外，UPYUN 自定义 SSL 基于 SNI 实现，因此某些不支持 SNI 的旧式浏览器访问可能会有些问题。支持 SNI 的部分客户端列表如下：

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

## 缓存刷新

对于 `UPYUN 存储` 而言，文件的替换和删除操作，我们均会自动提交缓存刷新任务进行全网刷新，特别地，原图的替换和删除，我们会自动将其对应的所有缩略图版本号也进行刷新。正常情况下全网 5 分钟内就能刷新完毕，针对漏刷的情况，可以在管理后台进行手动刷新。

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
* 对使用 `UPYUN 存储` 的文件缓存刷新，会在 1 小时之内完成；对 `自主源站` 的文件缓存刷新，会在 5 ~ 10 分钟内完成。

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

若开启 `参数跟随` 特性后，不同参数对应的缓存也是不同的，因此可以很方便通过参数的变换来绕过缓存，从而达到资源被动刷新的目的。但对于参数不跟随的情况来说，不同参数并不会产生不同的缓存拷贝，例如以下两条请求在 UPYUN CDN 均为命中同一个资源：

```
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?foo=123&bar=456
```

因此，在主动刷新的基础上，我们额外对于这类请求开放了一个特殊的参数，即 `_upv`，带上这个参数就能绕过这个限制，直接传递到缓存服务器去，然后依次穿透到客户源站，例如：

```
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upv=02
```

注：_upv 参数取值范围：只接受 00 - 99，即 2 位 {0 - 9} 的数字，其他任何值都无效。

## 缩略图处理

### 自定义版本

> 管理后台：服务 > 功能配置 > 云处理 > 自定义版本

> 源站类型：全部

----

用户可以根据业务需求在管理后台配置不同的自定义缩略图版本，当然也可以创建图片信息版本获取图片 EXIF 等信息。

* 间隔标识符有 3 种可选，分别是：`!`（感叹号）、`-`（中划线）和 `_`（下划线）
* 自定义版本使用方式： `http://绑定域名/原图路径+间隔标志符+自定义版本名称`

### URL 动态缩略图

> 源站类型：全部

----

基于 URL 的动态缩略图功能是对自定义版本号方式的一个补充，它比后者更加灵活和方便。即可以直接在 URL 上进行缩略图具体参数的配置，特别地，为了区别正常的缩略图版本提取逻辑，这里我们规定当间隔符后面紧跟 `/` 的时候，那么其后面（包括 `/` 本身）的字符串即为动态缩略图配置，例如：

```
http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png_/fw/800/format/webp
```

特别说明：对于设置了原图保护的图片，URL 动态缩路图功能失效。

#### 相关参数说明

| 特殊参数指令  | 示例值 | 说明 |
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
* 注 2：高斯模糊参数 `<radius>` 是模糊半径，取值范围是 `[0, 99]`，`<sigma>` 是正态分布的标准差，必须大于 0。

## 页面优化

### 代码压缩

> 管理后台：服务 > 高级功能 > 代码压缩

> 源站类型：全部

----

目前仅支持 HTML 代码的优化，开启后，会自动去除 HTML 文件中非必要的字符（空白、注释等），自动压缩文件大小，提升访问速度。

### 文件合并

> 源站类型：全部

---

`UPYUN 存储` 和 `自主源站` CDN 加速服务均支持 JS，CSS Combo（组合特性），即可以把多个 JS, CSS 请求合并成一个。演示如下：

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

----

开启后，能够将源站的数据平滑地迁移到 UPYUN，实现服务的无缝切换。

## 原图保护密钥

> 源站类型：UPYUN 存储

----

* 仅对配置了缩略图版本号的 `UPYUN 存储` 源站有效
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
