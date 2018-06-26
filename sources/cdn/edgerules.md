## 功能概述

又拍云 CDN 边缘网络中的智能可扩展应用程序规则可以帮助您简化内容分发业务逻辑，并提升终端用户访问体验。该规则可以快速部署且配置简单，可极大降低业务实现成本。网站及 Web 应用开发者或者安全工程师可以快速创建边缘规则集来提升网站安全及分发性能。例如，这些场景可能包括：

 - 通过 URL 改写，可美化网站 URL，提升网站 SEO
 - 为了节省更多访问带宽，限制网站请求下载速度
 - 控制请求 URL 的结构、HTTP 头域等
 - 自定义源站错误页面，提升终端用户体验
 - 限制特定客户端的访问行为，合理进行访问控制；
 
您可以根据实际使用场景，合理选择功能项。

###使用方式

EdgeRules 根据不同用户背景划分了两种不同的使用方式，一种是通用模式，一种是编程模式。

1）通用模式：适用于没有开发基础的用户且对又拍云 EdgeRules 语法规则不熟悉，该模式使用方便快捷，用户体验会更好一些，参考如下截图：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_shiyong_tongyong.png "height="470" width="800" />


2）编程模式：适用于开发者，对 EdgeRules 语法规则有很深了解的用户，相对通用模式来说，编程模式会更灵活，功能会更强大一些。参照如下截图：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_shiyong_biancheng.png "height="470" width="800" />


###条件判断

默认情况下，EdgeRules 是根据服务维度生效的，您也可以添加条件判断，当条件满足某些特定要求时，才会触发后面的功能选项。例如：

> 当请求 HOST 为 test.example.com 时，添加 HTTP 响应头 CDN 为 UPYUN。

这些条件过滤器包括：

 - 请求 URI
 - HTTP 请求头
 - 请求方法
 - 请求协议
 - 请求参数
 - 查询字符串
 - POST 参数
 - Cookie 变量
 - 客户端 IP 
 - 国家

为了配合条件判断使用，支持的运算符包括：

 - 等于
 - 不等于
 - 正则匹配
 - 正则不匹配
 - 正则匹配（不区分大小写）
 - 正则不匹配（不区分大小写）

如希望通过编程模式来使用变量，更多了解请参考语法规则章节 [变量](https://docs.upyun.com/cdn/edgerules/#_8) 及 [函数](https://docs.upyun.com/cdn/edgerules/#_7) 的介绍。


###功能选择

根据使用场景，我们针对【通用模式】配备了 7 大功能，具体功能列表包括：

 - 添加 HTTP 头部（包括请求头和响应头）
 - 删除 HTTP 头部（包括请求头和响应头）
 - 边缘重定向
 - URL 改写
 - 请求限速
 - 访问控制
 - 自定义错误页面

###其他选项

**优先级**

又拍云 EdgeRules 默认是按照先后顺序执行的。理论上优先级在前面的规则将会优先执行，您需要根据业务需求来合理调整规则的优先级。更多细节了解请参考【配置指南】章节的 [规则优先级](https://docs.upyun.com/cdn/edgerules/#_12) 介绍。

**break**

该开关默认为关闭状态。开启状态下，如果此条规则命中则终止匹配剩下的规则，也即会跳出整个规则集，请结合业务需求合理开启和关闭。

**状态**

1）启用

启用状态下，规则会在几秒钟内下发部署到全网 CDN 节点，及时生效。请确保业务测试成功之后，再调整为启用状态。

2）关闭

关闭状态下，规则不会在全网 CDN 节点进行部署。请根据业务情况，有选择性的关闭规则。

3）测试

规则默认为测试状态，平台会自动获取本地 IP 地址，也即规则只会对该客户端 IP 生效，您也可以换成其他 IP 地址进行测试。当且仅当测试满足业务要求之后，你才可以将规则调整为启用状态。


## 语法规则

当您希望通过编程模式来自行编写边缘规则时，你需要对又拍云边缘规则的语法规则进行详细了解，方可自行编写规则。本文将围绕函数、变量、字符串常量、break 进行详细介绍。

###函数

函数调用以 ` $ ` 开头，后跟一组大写字母，字母之间可以包含下划线 `_`，函数需要的参数放在` () `中，以 `,` 分隔。如果没有特别说明，边缘规则 中的函数参数个数不能少于要求的参数个数，否则视为语法错误，然后终止规则执行过程，多余的参数会被求值，但不影响调用。函数调用是有上下文的，譬如 `$WHEN` 这个函数，参数是 `bool` 类 型，参数中有不成立的条件 `false` 时，会终止本次规则执行过程。支持的函数有：

> 条件选择和判断

函数                         | 含义
:--------------------       | :-------
`$WHEN(E1, E2, ...)`        | 所有条件都成立时才会执行规则，并返回空字符串
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
`$DEL_RSP_HEADER(E)`        | 删除响应头 `E`
`$DEL_ARG(E)`               | 删除请求参数项 `E`
`$SET_METHOD(E)`            | 修改当前 HTTP 请求方法为 `E`，`E` 可选值有：`GET`, `HEAD`, `PUT`, `POST`, `DELETE`, `OPTIONS`, `PATCH`; 注 1
`$SET_BODY(E)`              | 修改当前 HTTP 请求体的内容为 `E`，仅在 `PUT` 或 `POST` 请求下有效
`$REDIRECT(E1, E2)`         | 重定向地址到 `E1`，状态码为 `E2(301, 302)`
`$EXIT(E1, E2)`             | 以状态码 `E1` 退出，响应体为 `E2`

 注: 删除响应头函数不能删除 CDN 自带的一些字段，比如 via、age、server、connection、x-cache、x-request-id、x-source、content-length、transfer-encoding等字段是有保护的。

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
`$GSUB(E1,E2,E3,N)`        | `E1`为要处理的字符串，`E2`为要替换的字符串，`E3`为替换成的字符串（可以为空，表示删除`E1`），`N`为从字符串开始的替换`E1`的个数，空或小于零表示所有，函数返回处理后的字符串
`$EQ(E1, E2)`               | 字符串是否相等，返回 `true` 或者 `false`
`$UPPER(E)`                 | 将 `E` 转换为大写
`$LOWER(E)`                 | 将 `E` 转换为小写
`$LEN(E)`                   | 返回字符串 `E` 的长度

> 通用功能类函数

函数                         | 含义
:--------------------       | :-------
`$ENCODE_BASE64(E)`         | 将字符串 E 按 base64 编码压缩
`$DECODE_BASE64(E)`         | 将字符串 E 按 base64 编码解压
`$ESCAPE(E)`         | 将字符串 E 按 URL 编码处理，默认按 UTF-8 编码；需要注意的是，`'`, `*`, `)`, `(`, `!` 不会被转义。
`$UNESCAPE(E)`         | 将字符串 E 按 URL 解码处理，默认按 UTF-8 解码
`$MD5(E)`                   | 计算 `E` 的 md5 值
`$MD5_BIN（E）`              | 计算 `E` 的 md5 值，并以二进制数据输出
`$RANDI(E1, E2)`            | 返回大于 `E1` 并小于 `E2` 的随机数值
`$UNIXT(y, m, d, h, min, sec)` | 指定年，月，日，小时，分钟，秒，返回相应的 UNIX TIME
`$INT(E1, E2, E3)`          | 进制转换，将 `E2` 进制的数字（字符串形式） `E1` 转换成 `E3` 进制并返回，`E2`, `E3` 可选，`E2` 默认为 `10`
`$STRF_TIME(E1,E2 )`        | 时间格式转换，表示把时间进行格式化成年月日时分，`E1` 代表 UNIX 时间戳，可以使用变量 `$_TIME`来获取当前时间；`E2` 为间隔符，为可选项，不填时格式为 `201802230955`；间隔符为 `-`时，格式为`2018-02-23-09-55`


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

###变量

变量以 `$_` 开头，这些变量都是对此次请求上下文中一些参数的映射，譬如 `$_HOST` 对应此次请求头中的 `Host` 字段，`$_GET_foo` 对应此次请求 URL 参数 foo 的值，等等。若将变量内插到重写后的 URL 中，譬如 URL 改写 `/$_GET_foo/bar`，如果请求参数中没有 `foo`则视为此次改写 失败；

若将变量放在函数中，则由该函数确定返回的值，例如 `$NOT($_GET_foo)`，如果参数中包含 `foo`，则返回 `false`，否则返回`true`。目前支持的变量有：

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
`$_SCHEME`            | http/https，注意此处全为小写
`$_TIME`              | 获取当前服务器时间，格式为 UNIX TIME
`$_BODY`              | 获取当前请求的 BODY（请求体）内容，大小限制为 16KB
`$_GEOIP_COUNTRY_CODE`| 国家代码，可参照维基百科： https://en.wikipedia.org/wiki/ISO_3166-2



###字符串常量

字符串常量有两种形式，第一种就是普通的字符串例如`/foo/bar`，但是如果字符串中包含了一些特殊字符，例如空白字符将被省略，例如 `$(),'` 这些字符有特殊的用途，不能被直接使用，要使用这些特殊的字符，要加 `\` 前缀对其转义，例如 `/foo/bar\,`；第二种是加单引号的字符串 `''`，单引号中的字符只有 `\` 是特殊转义字符前缀，其他的都视为普通字符，例如这样一条改写规则 `/foo/'$\\,\''`，改写过后对应 `/foo/$\,'`。

###break

除了能勾选 `break 选项` 指定是否 `break`，也可以直接在边缘规则最后加上 `$$` 表示 `break`。


## 配置指南

在这个章节，我们会介绍如何添加和编辑边缘规则集，主要大纲包括：

 - 认识规则管理界面
 - 规则优先级
 - URI 字符串提取
 - 变量使用
 - 执行动作
 - 规则测试

###认识规则管理界面

登陆 [CDN 控制台][1]，依次进入：服务管理 > 功能配置 > 边缘规则 > EdgeRules，即可进入边缘规则管理界面。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_rule_management.png "height="470" width="800" />

在规则管理界面，针对每一条规则，控制台提供了如下字段信息：

字段                  | 描述
:-------------------- | :-------
优先级             | 指定规则执行的先后顺序
规则名称            | 该条规则的名称，方便管理标识
break         | 规则命中之后，是否跳出所有的规则集
状态        | 包括启用、关闭、测试三种状态
编辑         | 编辑规则
删除         | 删除规则
更多     | 包括启用、关闭、测试三个选项

###规则优先级

理论上，所有规则的优先级是按照先后顺序来执行的，优先级越靠前则会被优先执行，这个仅适用于添加 HTTP 头部、删除 HTTP 头部、访问控制、请求限速、自定义错误页面、边缘重定向。针对 URL 改写动作，即使 URL 改写的规则放在前面， 还是会等执行完其他动作之后，才会去执行 URL 改写动作。

这里根据具体示例进行说明，如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_rule_youxianji.png "height="470" width="800" />

特别地：当匹配的条件都相同时，例如：当请求 URI 都为 `/123.mp4` 时，按照优先级顺序出现了如下 5 条规则：

第 1 条规则：当请求的 URI 为 `/123.mp4` 时，改写到 `/beibei.mp4`

第 2 条规则：当请求的 URI 为 `/123.mp4` 时，添加 HTTP 响应头 CDN

第 3 条规则：当请求的 URI 为 `/123.mp4` 时，边缘重定向到 `https://www.upyun.com/`

第 4 条规则：当请求的 URI 为 `/123.mp4` 时，进行访问控制，响应 403 状态码

第 5 条规则：当请求的 URI 为 `/123.mp4` 时，进行请求限速，限速到 `200KB/s` 。

按照当前的设计逻辑，第 1 条规则不会被立马执行，会等待后面所有的规则执行完毕之后，才会进行 URL 改写的动作。按照规则优先级，会依次执行第 2 条规则，然后再执行第 3 条规则，通过 curl 发起命令测试，测试结果为：

     GET /123.mp4 HTTP/1.1
     Host: wuxuejun2018.b0.upaiyun.com
     User-Agent: curl/7.43.0
     Accept: */*
     
     HTTP/1.1 302 Moved Temporarily
     Server: marco/1.10
     Date: Wed, 20 Dec 2017 09:28:28 GMT
     Content-Type: text/html
     Content-Length: 159
     Connection: keep-alive
     Location: https://www.upyun.com
     Via: M.ctn-zj-lna3-019
     X-Request-Id: 32c1c154c192027be98887ad63c3b08c
     CDN: UPYUN

按照测试结果，依次命中了第 2 条和 第 3 条规则。

可以看出，第 1 条、第 4 条、第 5 条都没有被执行，那是因为第 1 条属于 URL 改写，不会立马执行；第 3 条为边缘重定向，直接 302 到 `https://www.upyun.com` 了，跳出了整个规则集，从而接下来的规则不会被执行，包括 URL 改写。

特别注意的是，类似边缘重定向这类直接跳出所有规则集的行为，还包括：

 - 自定义错误页面
 - 访问控制

也就是说，当执行完边缘重定向、自定义错误页面、访问控制等动作之后，会直接跳出整个规则集，不会执行接下来的匹配动作。
 

###URI 字符串提取

所谓 URI 字符串提取，也即在执行边缘重定向以及 URL 改写动作时，根据业务需求，可能会提取原始请求 URI 中的字符串。其中 URI 字符串提取支持 PCRE 正则表达式，匹配后，产生 `$1`, `$2` ... 这样的变量。

URI 字符串提取正则表达式：

    ^/pay/([0-9]+)/([0-9]+)/(.*?).html$
    
根据以上的 PCRE 正则表达式，分别会产生 3 个 字符串，为  `$1`、`$2`、`$3`。分别对应了以上正则表达式里面的从左到右 `()` 里面的字符串。

URL 改写规则：

    /pay.php?payid=$1&categoryid=$2

配置截图可参考：

1）通用模式

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_uri_string_tiqu.png "height="470" width="800" />

2）编程模式

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_uri_string_tiqu2.png "height="470" width="800" />


通过执行以上规则之后，原始请求和改写后的地址分别如下：

原始请求：

    http://example.com/pay/25/8/...


改写后地址：

    http://example.com/pay.php?payid=25&categoryid=8...


###变量使用

在进行 URL 改写和边缘重定向动作时，除了可以提取 URI 中的 字符串，您还可以获取原始请求中的变量。这些变量包括：

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
`$_SCHEME`            | http/https，注意此处全为小写
`$_TIME`              | 获取当前服务器时间，格式为 UNIX TIME
`$_BODY`              | 获取当前请求的 BODY（请求体）内容，大小限制为 16KB
`$_GEOIP_COUNTRY_CODE`| 国家代码，可参照维基百科： https://en.wikipedia.org/wiki/ISO_3166-2

为了更加方便理解，下面将通过一个应用示例来展开介绍：

**示例：URL 改写**

例如：某个 URL 为 `https://www.example.com/index.htm`

需求：希望通过 URL 改写动作，将客户端 IP (这里以 `192.168.1.1` 为例) 以及请求方法（实际的请求方法为 GET）通过查询字符串传递到源站后端；

改写规则可以为：

    /index.php?ip=$_IP&method=$_METHOD

则改写后的完整 URL 为：

    https://example.com/index.php?ip=192.168.1.1&method=GET
    

配置截图可参考(通用模式):

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_bianliang_shiyong.png "height="470" width="800" />


###执行动作

**添加 HTTP 头部**

通过使用边缘规则的【添加 HTTP 响应头部】功能，当您的用户请求业务资源时，可以在返回的【响应消息】中添加您配置的头部，这些头部可能包括：

 - Cache-Control
 - Content-Disposition
 - Content-Language
 - Access-Control-Allow-Origin
 - Content-Type
 
当然，您也可以根据业务要求，自定义响应字段。例如：CDN：UPYUN

具体使用截图如下截图所示：

1）通用模式

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_add_resp_header1.png "height="470" width="800" />

2）编程模式

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_add_resp_header2.png "height="470" width="800" />


从截图可以看出，添加 HTTP 头部，您需要分别填写：

 - Header 域名：Access-Control-Allow-Origin
 - Header 域值：*

另外，有【覆盖原有响应头】的配置，默认为否，可以根据具体情况来勾选。当勾选时，会覆盖掉原始的响应头信息。
 
 
**删除 HTTP 头部**

通过使用边缘规则的【删除 HTTP 响应头】功能，您可以删除掉不必要的响应字段。如隐藏掉一些并不安全的响应头部信息。例如：

- X-Powered-By

具体配置参见如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_del_resp_header.png "height="470" width="800" />

注意事项：又拍云 CDN 内部不能删除的响应头包括：

    Via
    Age
    Server
    connection
    X-cache
    x-source
    X-request-id
    Content-range
    Content-length
    Transfer-encoding


**边缘重定向**

通过使用边缘规则的【边缘重定向】功能，当匹配到条件时，CDN 会响应一个 301/302 请求给客户端，重定向状态码支持 301 和 302。

具体的配置可参照如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_edge_redirect.png" height="470" width="800" />

当条件满足：请求 HOST 为 XXX 并且客户端 User-Agent 正则匹配 XXX 时，则执行重定向动作，301 重定向到：

    https://test.com/$1/123.html



**自定义错误页面**

通过使用边缘规则的【自动错误页面】功能。使用自定义源站错误页面，而非 CDN 默认指定的错误页面。当前支持的源站错误信息状态码包括：

 - 404
 - 500
 - 501
 - 502
 - 503
 - 504

这里以 404 错误页面为例，当源站响应 404 状态码时，则 302 重定向到指定 404 页面。具体的配置可参照如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_custom_error_page.png" height="470" width="800" />

**URL 改写**

配合网站将动态 URL 转换为语义 URL，改善搜索引擎索引机制，提高网站 SEO 排名。将原始请求 `http://www.example.com/pay/25/8/...` 进行 URL 改写。 

相关配置如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_url_rewrite.png" height="470" width="800" />


**请求限速**

通过使用边缘规则的【请求限速】功能，在不影响终端用户体验的情况下，将请求速度控制在合理范围之内，节省 CDN 带宽。

具体配置如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_request_limite_rate.png" height="470" width="800" />

规则释义：开始 `20MB` 不限速，后面限制到 `800KB/s`。

**访问控制**

灵活针对请求内容进行访问控制，可以响应 403/404 状态码。这里针对 `User-Agent` 进行过滤，比如：来自搜狗等爬虫的请求直接响应 403 状态码。具体的配置如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_access_control.png" height="470" width="800" />

 
###规则测试

由于边缘规则的 URL 改写、边缘重定向、访问控制等动作是有副作用的，为了安全起见，
每一条规则默认都在测试状态下。测试状态下，平台会自动获取客户端本地 IP 地址，也即规则仅针对该 IP 地址生效。您也可以更换其他 IP 地址进行测试。具体的配置截图如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/edgerules/edgerules_rule_test.png" height="470" width="800" />


## 编程模式（案例）

###1、自定义防盗链

需求描述：将请求 URL 按照一定的算法规则实现 token 防盗链，使用边缘规则来实现，会更加灵活和高效。token 防盗链详解请参见：

    https://blog.upyun.com/?p=1177

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
  "rule": "$WHEN($MATCH($_URI, '.ts$'),$NOT($EQ($MD5('abc'$_GET_t$_URI),$LOWER($_GET_key))))$EXIT(403)",
  "pattern": ""
}
```
规则释义：

第一条：当文件后缀为 `ts` 时，判断请求 URL 里面是否存在 `t` 和` key` 参数，如果其中一个不存在，则返回 403；

第二条：当文件后缀为 `ts` 时，判断当前 CDN 节点 UNIX 时间是否大于请求参数里面的时间戳（也即参数 t 的值），否则返回 401；

第三条：当文件后缀为 `ts` 时，CDN 节点按照约定的算法规则生成验证信息（也即 MD5 值），判断生成的 MD5 值和请求参数 `key` 的值是否一致，不一致则返回 403；

###2、边缘重定向

示例一：匹配 `cookie` 进行跳转

```
"rule": "$WHEN($NOT($EQ($_URI, /live.html)), $NOT($_COOKIE_token))$REDIRECT(http://test.example.com/no_token.html,301)",
"pattern": ""
```

规则释义：首先匹配请求 `URI` 是否为 `/live.html`,当 `cookie` 中 `token` 值为空时，则跳转到指定到地址 `http://test.example.com/no_token.html`。

###3、URL 改写

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
"rule": "/pay.php?productid=$1&categoryid=$2",
"pattern": "^pay/([0-9]+)/([0-9]+)/(.*?).html$"
```

规则释义：当解析的 url 符合规则 `^pay/([0-9]+)/([0-9]+)/(.*?).html$ `，那么将请求导向到 `/pay.php?productid=$1&categoryid=$2 `。

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


###4、请求/响应头修改

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


###5、URL 限速

假如请求的 URL 为：`http://test.example.com/mp4/4E10F356C0FEAD359C33DC5901307461-10.mp4` ，需要对该类型文件进行限速，限速要求为：前 20MB 不限速，20MB 之后限速 800 KB/s，规则可这样编写：

```
"rule": "$WHEN($1, $EQ($_HOST, 'test.example.com'))$LIMIT_RATE_AFTER(20, m)$LIMIT_RATE(800, k)",
"pattern": "^(/).+-10\.mp4$"
```

规则释义：当 `$1` 为真，且满足请求 `HOST` 为 `test.example.com ` 时 ，开始 20MB 不限速，后面限制到 `800KB/s`。

###6、大小写转换

该部分会使用到`$LOWER(E)` 函数，直接看规则：

```
"rule": "$WHEN($MATCH($LOWER($_URI), /uptest.png)) /UPtest.png",
"pattern": ""
```

规则释义：首先是获取请求 `URI` ，使用 `$LOWER(E)` 函数将 `URI` 转换为小写，和 `/uptest.png` 进行匹配，匹配成功，则直接将 `URI` 改写为`/UPtest.png`。如果需要将字符串转换为大写，使用 `$UPPER(E)` 函数。

###7、数值比较

也即将字符串或者数字进行比较。例如： `A` 和 `B` 进行比较，是否大于、大于等于、相等等逻辑，参见如下规则示例：

```
"rule": "$GT($_TIME,$ADD($INT($_GET_t,16,10),3600))",
"pattern": ""
```

规则释义：将请求 URL 中的 t 参数 由 16 进制转换为 10 进制，然后加上 3600 ，将相加得到的值和当前系统时间进行比较。这里使用到了 `$GT(E1，E2）` 函数，也即 `E1` 是否大于 `E2`，大于即返回 true 。另外，等于使用 `$EQ(E1，E2）` 函数，大于等于使用 `$GE(E1，E2）` 函数。


----------

如有疑问请 [联系我们](https://www.upyun.com/contact)




  [1]: https://console.upyun.com/login/
