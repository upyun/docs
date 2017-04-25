##功能简介

自定义 rewrite 是基于 DSL ( Domain Specific Language ) 理念来设计的，主要面向开发者使用。充分利用又拍云 CDN ( Content Delivery Network )分布式边缘网络的性能及规模，通过又拍云管理控制台可轻松创建 rewrite 规则，规则支持函数、变量、字符串常量，用户可以将这些自由组合，可以实现对 URL 的改写、重定向、自定义 HTTP 头、请求禁止等处理逻辑。

##应用场景

####提高安全性

可以有效的避免一些参数名、ID 等完全暴露在用户面前；
 
####URL 美化
去除了那些比如 `*.do` 之类的后缀名、长长的参数串等，可以自己组织精简更能反映访问模块内容的 URL；
 
####提高网站 SEO
通过 URL Rewrite 之后，更有利于搜索引擎的收入，通过对 URL 的一些优化，可以使搜索引擎更好的识别与收录网站的信息；
 
####HTTP 头改写

新增、删除 HTTP 请求头，新增、删除 HTTP 响应头等处理逻辑；
 
####防盗链设置

可以很灵活、高效的编写防盗链规则和处理逻辑，可以实现高强度的防盗链规则；
 
####边缘重定向

可以根据特殊的业务逻辑，触发某个条件之后，可将请求重定向到特定的地址等等；
 
##规则说明

####基础概念

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

####调试模式

由于 rewrite 过程会对当前请求产生副作用，这是非常危险的，例如随意的填写一条 rewrite 规则：
 > /foo

此规则会将所有请求的 URI 改写成 `/foo`，这样客户端所有请求的响应就变成 404 了， 这是您极不愿意看到的结果。我们允许对 rewrite 规则进行调试，即新增加一条规则时，`调试模式` 开关会默认打开，这样添加规则后，即使当前请求命中该 rewrite 规则， rewrite 过程也不会生效，只有当包含以下请求头时，rewrite 过程才会生效：

> X-Upyun-Rewrite-Preview: true

使用命令行工具 curl 即可对规则调试：

> curl -H "X-Upyun-Rewrite-Preview: true" http://your-domain/foo/bar.html -v

这样经过调试，确定该 rewrite 过程符合预期后，即可将 `调试模式` 关闭，此时该 rewrite 过程会对所有命中的请求生效。

####注意事项

特别地，调试模式目前仅针对 URI 和 ARGS 修改以及一些函数有效，受 `调试模式` 开关影响的 rewrite 操作有：

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

##语法规则

####函数

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

####变量

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

####字符串常量

字符串常量有两种形式，第一种就是普通的字符串例如`/foo/bar`，但是如果字符串中包含了一些特殊字符，例如空白字符将被省略，例如 `$(),'` 这些字符有特殊的用途，不能被直接使用，要使用这些特殊的字符，要加 `\` 前缀对其转义，例如 `/foo/bar\,`；第二种是加单引号的字符串 `''`，单引号中的字符只有 `\` 是特殊转义字符前缀，其他的都视为普通字符，例如这样一条 rewrite 规则 `/foo/'$\\,\''`，rewrite 过后对应 `/foo/$\,'`。

####break

除了能勾选 `break 选项` 指定是否 `break`，也可以直接在 rewrite 规则最后加上 `$$` 表示 `break`。


##配置引导

登陆 [管理控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 高级功能 > 自定义 Rewrite，点击管理即可开始配置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-custom-rewrite.png" height="490" width="800" />


##经典案例

####1、自定义防盗链

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

####2、边缘重定向

示例一：匹配 `cookie` 进行跳转

```
"rule": "$WHEN($NOT($EQ($_URI, /live.html)), $NOT($_COOKIE_token))$REDIRECT(http://test.example.com/no_token.html)",
"pattern": ""
```

规则释义：首先匹配请求 `URI` 是否为 `/live.html`,当 `cookie` 中 `token` 值为空时，则跳转到指定到地址 `http://test.example.com/no_token.html`。

####3、URL 改写

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


####4、请求/响应头修改

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


####5、URL 限速

假如请求的 URL 为：`http://test.example.com/mp4/4E10F356C0FEAD359C33DC5901307461-10.mp4` ，需要对该类型文件进行限速，限速要求为：前 20MB 不限速，20MB 之后限速 800 KB/s，规则可这样编写：

```
"rule": "$WHEN($1, $EQ($_HOST, 'test.example.com'))$LIMIT_RATE_AFTER(20, m)$LIMIT_RATE(800, k)",
"pattern": "^(/).+-10\.mp4$"
```

规则释义：当 `$1` 为真，且满足请求 `HOST` 为 `test.example.com ` 时 ，开始 20MB 不限速，后面限制到 `800KB/s`。

####6、大小写转换

该部分会使用到`$LOWER(E)` 函数，直接看规则：

```
"rule": "$WHEN($MATCH($LOWER($_URI), /uptest.png)) /UPtest.png",
"pattern": ""
```

规则释义：首先是获取请求 `URI` ，使用 `$LOWER(E)` 函数将 `URI` 转换为小写，和 `/uptest.png` 进行匹配，匹配成功，则直接将 `URI` 改写为`/UPtest.png`。如果需要将字符串转换为大写，使用 `$UPPER(E)` 函数。

####7、数值比较

也即将字符串或者数字进行比较。例如： `A` 和 `B` 进行比较，是否大于、大于等于、相等等逻辑，参见如下规则示例：

```
"rule": "$GT($_TIME,$ADD($INT($_GET_t,16,10),3600))",
"pattern": ""
```

规则释义：将请求 URL 中的 t 参数 由 16 进制转换为 10 进制，然后加上 3600 ，将相加得到的值和当前系统时间进行比较。这里使用到了 `$GT(E1，E2）` 函数，也即 `E1` 是否大于 `E2`，大于即返回 true 。另外，等于使用 `$EQ(E1，E2）` 函数，大于等于使用 `$GE(E1，E2）` 函数。


----------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)



