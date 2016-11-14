## 概述

本文档适用于阅读者了解如何接入又拍云手机流量平台，以达到第三方企业应用平台与本平台实现数据交互的目的。主要面对双方（又拍云手机流量平台与企业应用）的数据交互模式、约定、实现方式以及对接口的功能、报文格式以及使用场景进行详细的描述。

## 使用说明


**客户接入流程:**

1.联系我司商务（Tel：0571-89775132）完成商务洽谈

2.登录[又拍云控制台](https://console.upyun.com/login/)，前往 `工具箱>手机流量营销页面` 进行开通

3.开通成功后，通过流量营销控制台配置回调地址

4.出口 IP 出于安全考虑暂不支持控制台配置，可联系我司商务完成生产环境联调

**订单业务受理流程:**

![pic](http://up-static.b0.upaiyun.com/phone-traffic/example.jpg)



**接口概述:**

又拍云手机流量平台所有的参数传递都是通过标准的 JSON 格式提交,所有的请求响应都是通过 JSON 格式返回。

又拍云手机流量平台需要与企业应用系统进行信息交换与同步，下图为本 API 提供的接口方法:


|接口名称 |对应方法 |对应方向 |接口描述 |
|-|-|-|-|
|获取 token |getToken |企业 -> 流量平台	|获取 token（后面接口需要） |
|下订单 |createOrder |企业 -> 流量营销 |提交流量订单 |
|查询订单 |getOrderStatus |企业 -> 流量营销	|查询订单的充值状态 |
|充值状态回调 |- |	流量营销 -> 企业 |	又拍云手机流量充值平台将订单充值结果返回给企业的应用系统 |
|号码归属地查询 |getMobileInfo |企业 -> 流量营销 |手机号信息（运营商、归属地） |

## 基础接口

**1.1 获取 token:**


地址	 |/getToken
:--------------------     | :-------
方法	 |POST
header |Content-Type为 `application/json`


参数列表：

|Label |Description |Type |Required
-|-|-|-
appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true`
appsecret |鉴权密钥，由又拍云手机流量平台提供 |`string` |`true`


返回：

```json
{
    "code": "返回的状态码，详见错误码说明",
    "token": "返回的 token,若获取失败则不返回",
    "info": "返回说明"
}
```


描述:

**此接口只支持 HTTPS 环境**

客户获取 token 参数,后续接口需要应用到 token 参数进行身份识别 token 具有时间有效性,由流量平台设置,默认为获取 token 后 24 小时。


备注:

**友情提示: 如果生产环境中使用多台服务器，请使用一台服务器获取 token 或使用最后一次获取到的 token（由于每次获取都会使 token 值刷新，所以最后一次获取的 Token 值才是有效的）。**



**1.2 创建订单:**


地址	 |/createOrder
:--------------------     | :-------
方法	 |POST
header |Content-Type为`application/json`

参数列表：

|Label |Description |Type |Required
-|-|-|-
appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true`
phone |需要申请流量包的手机号码，一次只允许一个手机号码，需要进行 AES(AES/CBC/PKCS5Padding) 加密,加密后的结果通过 Base64 做转码传递，加密的密钥是最新获取的 token,加密向量为当前账户的 appkey, ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`
pcode |流量包的 ID，目前只支持每次一个流量包 |`string` |`true`
extno |客户自定义的订单号，长度小于 30，保证每次唯一	 |`string` |`true`
sign |签名串，签名规则见 description, ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`
pscope |用于区分全国通用流量和省内流量,全国流量参数为"中国",省内流量为中国再加省份,如"广东", 默认情况下为全国流量,参数为"中国"	 |`string` |`false`
ptime |用于对流量时段的区分,如果闲时流量为 0,全时段流量为 1,默认情况下为全时段流量,值为 1 |`integer` |`false`
pstandard |用于区分网络类型,2G/3G/4G 通用套餐为 7,2G/3G 通用套餐额为 3,2G 套餐为 1,默认为 2G/3G/4G 通用套餐,值为 7 |`integer` |`false`

返回：

```json
{
    "code": "返回的状态码，详见错误码说明,如果返回超时,不一定下单失败,需要查询订单状态",
    "extno": "客户自定义的订单流水号，原样返回",
    "orderno": "流量平台生成的订单号,若获取失败可能不返回",
    "info": "返回说明信息"
}
```

描述:

下单接口，企业客户发起流量包订购请求，又拍云手机流量平台完成合法性校验之后，并向运营商发送下单请求，同步返回下单结果。

sign 签名算法说明：

企业往流量平台发送请求中，必须将签名 sign 作为参数，([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)), 具体说明如下:

1. 将所有请求参数按照参数名升序排序;

2. 排序后，按请求参数名及参数值相互连接组成字符串并在最后增加 "token"（小写）和当前 token 值, 如：parm1value1parm2value2…tokenTOKENVALUE(示例中的token值为TOKENVALUE)

3. 对拼接后的字符串进行 SHA1 指纹运算后转成二进制数组；

4. 对二进制数组转成 16 进制的字符串作为 sign 参数值。

5. 将 sign 签名参数和其他请求参数一起发送给接口。

6. 范例：
    * 根据签名算法，将所有请求参数名与参数值按照参数名升序排序后拼接成字符串 appkeydfsdfs34r879wef3extnosde3jsef3ksdf32fsdf232fpcodeCMCC_10phone4a2954e3118afed8adad7dce3cd37tokenJjjveRLP7nFniKSs;
    * 对以上拼接的字符串进行SHA1签名算法，将签名值转化为十六进制的编码串：7acf2a069db286f2c75f16ed41bdff52a0e5b5ea

备注:

**注意:成功状态码：200; 特殊处理状态码：410,411,428,429,511（必须再次查询订单确认）；其它错误码为失败。对于我们系统返回的异常信息（非标准 JSON 结果）和请求超时无响应等结果，需再次查询订单确认结果。**



**1.3 充值状态回调:**


地址	 |/客户企业提供回调地址
:--------------------     | :-------
方法	 |POST
header |Content-Type为`application/json`

参数列表：

|Label |Description |Type |Required
-|-|-|-
extno |客户自定义的订单流水号，原样返回 |`string` |`true`
orderno |流量平台生成的订单号 |`string` |`true`
code |返回的状态码，详见错误码说明 |`string` |`true`
info |返回说明信息 |`string` |`true`
sign |签名串，签名规则为 "code" 字符串拼接实际的 code 值,加上 "extno" 字符串拼接实际的 extno 值,加上 "info" 字符串拼接实际的 info 值,加上 "orderno" 字符串拼接实际的 orderno 值, 最后拼接 "TOKEN"（大写）串和实际的 token 值然后将拼接后的字符串进行 sha1 签名,实例可参考 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`

返回：

```json
{
    "info": "1"
}
```

描述:

  又拍云手机流量平台将运营商的订单充值结果异步返回给客户，客户接收后返回 JSON 格式的 {'info':'1'} 给流量平台以确认接收到回调,如果又拍云手机流量平台没有接收到正确格式的确认信息，会每隔 1 分钟再次回调给客户，最多重新回调 3 次

备注:

**注意:成功状态码：200；失败状态码：430，530；其它错误码都为充值中。对于系统返回的异常信息（非标准 JSON 结果）和请求超时无响应等结果，需做充值中处理。**


**1.4.1 查询订单充值状态:**


地址	 |/getOrderStatus
:--------------------     | :-------
方法	 |POST
header |Content-Type为`application/json`

参数列表：

|Label |Description |Type |Required
-|-|-|-
extno |客户提交的订单流水号 |`string` |`true`
appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true`
sign |签名串，签名规则为 "appkey" 字符串拼接实际的 appkey 值,加上 "extno" 字符串拼接实际的 extno 值,如果传了 ordertime,请加上 "ordertime" 拼接 ordertime 的值,如果没有传则无需处理,最后拼接 "TOKEN"（大写）串和实际的 token 值,然后将拼接后的字符串进行 sha1 签名,实例可参考 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`
ordertime |该订单创建时间。该参数为可选参数,格式为 "YYYY-MM-DD HH:mm:ss",为保证有效性,我们会在给定 orderTime 前后 1 个小时范围内查询;如果未传该参数,我们将在最近一周的订单中为你查询	 |`string` |`false`

返回：

```json
{
    "code": "返回的状态码，详见错误码说明",
    "extno": "客户自定义的订单流水号，原样返回",
    "info": "返回说明信息"
}
```

描述:

订单充值详情查询接口

备注:

**注意:成功状态码：200；失败状态码：430，516，530；其它错误码都为充值中。对于系统返回的异常信息（非标准 JSON 结果）和请求超时无响应等结果，需做充值中处理。**

**1.4.2 查询订单(旧接口,将废弃):**


地址	 |/searchOrder
:--------------------     | :-------
方法	 |POST
header |Content-Type为`application/json`

参数列表：

|Label |Description |Type |Required
-|-|-|-
extno |客户自定义的订单流水号 |`string` |`true`

返回：

```json
{
    "code": "返回的状态码，详见错误码说明",
    "extno": "客户自定义的订单流水号，原样返回",
    "info": "返回说明信息"
}
```

描述:

订单充值详情查询接口

备注:

**注意:成功状态码：200；失败状态码：430，516，530；其它错误码都为充值中。对于系统返回的异常信息（非标准 JSON 结果）和请求超时无响应等结果，需做充值中处理。**


**1.5 查询金额**

地址	 |/getCusBalance
:--------------------     | :-------
方法	 |POST
header |Content-Type为`application/json`

参数列表：

|Label |Description |Type |Required
-|-|-|-
appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true`
sign |签名串，签名规则为 "appkey" 字符串拼接实际的 appkey 值,再拼接 "TOKEN"（大写）串和实际的 token 值,然后将拼接后的字符串进行 sha1 签名,实例可参考 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip))  |`string` |`true`

返回：

```json
{
    "code": "返回的状态码，详见错误码说明",
    "balance": "余额(总充值金额减去已完成交易的余额)",
    "freeze": "冻结金额(正在交易的订单总金额)",
    "availBalance": "可用余额(余额减去冻结金额)"
}
```

描述:

查询当前账户的金额,客户应该使用 availBalance ,其它参数仅仅提供参考.

备注:

## 错误码说明

**请特别留意各接口备注说明**

错误码 |说明 |错误码 |说明
:-------  | :------- | :------- | :-------
200 |成功 |201 |充值中
402 |访问量已达上限	 |404	 |超出最大开通数量
406	 |用户不能订购该套餐	 |408	 |运营商令牌鉴权不通过
410	 |运营商返回超时	 |411	 |内部异常
412	 |参数错误	 |413	 |认证失败
414	 |IP 不在白名单	 |417	 |手机号与产品省份归属地不同
418	 |订购请求重复提交	 |420	 |用户已停机
421	 |用户已停机	 |422	 |用户状态异常
423	 |用户是黑名单 |	424	 |获取号码归属地失败
425	 |用户当月订购流量包已到达叠加上限	 |428	 |网络错误
429	 |其他错误	 |430	 |订购/充值失败
431	 |流量值错误	 |432	 |账户余额不足
433	 |手机号与运营商不一致	 |434	 |暂时无法充值
435	 |运营商维护期 |	502 |	sign 签名错误
503	 |余额不足 |	504 |	非法的回调地址
505	 |手机号不合法 |	506	 |用户不能订购该套餐
507	 |接口参数异常 |	508 |	令牌鉴权不通过
509	 |客户未认证授权	 |510 |	查询太频繁
511	 |内部异常	 |512 |	订单号重复
513	 |运营商请求超时 |	514	 |IP 不在白名单
515	 |该客户不存在 |	516 |	不存在此订单
517	 |无法识别的手机号	 |519 |	当前 appkey 或 appsecret 错误
520	 |活动不存在	 |526 |	合同不在有效期
527	 |token 不在有效期 |	530	 |订购/充值失败
531	 |签名不通过 |	532	 |订单号长度超过 30
533	 |AES 加密有误	 |	 |



