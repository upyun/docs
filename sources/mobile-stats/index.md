## 平台概述
又拍云手机流量营销平台，整合移动、电信、联通三大运营商流量资源，将强大的流量营销服务，通过接口等形式提供给商家合作伙伴，帮助商家开展品牌宣传、APP/游戏/微信公众号/网站的拉新与促活等多种营销活动。

通过接入又拍云流量营销平台，商家可以获得更多流量、用户和收益，而商家提供的活动也让用户获得了三网通用流量和更加丰富的活动体验。

## 产品特点
1.三网通用，成本优势，覆盖面广

2.快速对接、全流水、易对账、精结算

## 应用场景
1.APP 下载赠流量

2.微信公众号增粉

3.奖品礼券

4.流量加油站

5.积分兑换流量

6.活动比赛送流量

7.流量红包

8.员工福利

## 快速入门

1.请联系商务（Tel:0571-89775132）确定您的应用场景需求，获取对应流量报价

2.注册又拍云账号，如果您已有账号，请登录 [又拍云控制台](https://console.upyun.com/login/) 完成企业/个人认证

3.前往导航 > 工具箱 > 手机流量营销页面，开通该服务（开通邮件中包含接口地址）

4.通过流量营销控制台配置回调地址

5.出口 IP 出于安全考虑暂不支持控制台配置，可联系我司商务完成生产环境联调

**业务交互流程:**

<img src="http://up-static.b0.upaiyun.com/phone-traffic/upyun-mobile-traffic.png" width="830" />

**接口概述:**

又拍云手机流量平台接口入口地址统一为：https://ptp-api.upyun.com ，所有的参数传递都是通过标准的 JSON 格式提交，所有的请求响应都是通过 JSON 格式返回。下图为本API提供的接口方法:


|接口名称 |对应方法 |对应方向 |接口描述 |
| --- | --- | --- | --- |
| 获取Token      | refreshToken  | 企业 -> 流量平台 | 获取Token（后面接口需要）                                |
| 下订单         | chargeOrder   | 企业 -> 流量营销 | 提交流量订单                                             |
| 查询订单       | seekOrder     | 企业 -> 流量营销 | 查询订单的充值状态                                       |
| 充值状态回调   | -             | 流量营销 -> 企业 | 又拍云手机流量充值平台将订单充值结果返回给企业的应用系统 |

## 基础接口

**1.1 获取Token:**


| 地址	 | /refreshToken |
| -------------------- | ------- |
| 方法	 | POST |
| header | Content-Type为`application/json` |


参数列表：

| Label | Description | Type | Required |
| --- | --- | --- | --- |
| appkey | 鉴权账号，由又拍云手机流量平台提供 | `string` | `true` |
| appsecret | 鉴权密钥，由又拍云手机流量平台提供 | `string` | `true` |

请求示例：
```
curl https://ptp-api.upyun.com/refreshToken -H "Content-Type: application/json" -d '{"appkey": "your_appkey_put_here", "appsecret": "your_appsecret_put_here"}'
```

返回：

```json
{
    "code": "返回的状态码，详见错误码说明",
    "token": "返回的token,若获取失败则不返回",
    "info": "返回说明"
}
```


描述:

客户获取token参数,后续接口需要应用到token参数进行身份识别token具有时间有效性,由流量平台设置,默认为获取token后24小时。


备注:

**友情提示: 如果生产环境中使用多台服务器，请使用一台服务器获取token或使用最后一次获取到的token（由于每次获取都会使token值刷新，所以最后一次获取的token值才是有效的）。**



**1.2 创建订单:**


| 地址	 | /chargeOrder |
| --- | --- |
| 方法    |  POST |
| header | Content-Type为`application/json` |

参数列表：

|Label |Description |Type |Required|
| --- | --- | --- | --- |
|appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true`|
|mobile |需要申请流量包的手机号码，一次只允许一个手机号码，需要进行AES(AES/CBC/PKCS5Padding)加密,加密后的结果通过base64做转码传递，加密的密钥是最新获取的token,加密向量为当前账户的appkey, ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`|
|prodcode |流量包的ID，目前只支持每次一个流量包 |`string` |`true`|
|custno |客户自定义的订单号，长度小于30，保证每次唯一	 |`string` |`true`|
|sign |签名串，签名规则见description, ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`|

返回：

```json
{
    "code": "返回的状态码，详见错误码说明,如果返回超时,不一定下单失败,需要查询订单状态",
    "custno": "客户自定义的订单流水号，原样返回",
    "orderno": "流量平台生成的订单号,若获取失败可能不返回",
    "info": "返回说明信息"
}
```

描述:

下单接口，企业客户发起流量包订购请求，又拍云手机流量平台完成合法性校验之后，并向运营商发送下单请求，同步返回下单结果。

sign签名算法说明：

企业往流量平台发送请求中，必须将签名sign作为参数，([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)), 具体说明如下:

1. 将所有请求参数按照参数名升序排序;

2. 排序后，按请求参数名及参数值相互连接组成字符串并在最后增加"token"(小写)和当前token值, 如：parm1value1parm2value2…tokenTOKENVALUE(示例中的token值为TOKENVALUE)

3. 对拼接后的字符串进行SHA1指纹运算后转成二进制数组；

4. 对二进制数组转成16进制的字符串作为sign参数值。

5. 将sign签名参数和其他请求参数一起发送给接口。

6. 范例：

    * 根据签名算法，将所有请求参数名与参数值按照参数名升序排序后拼接成字符串 `appkey3P83lWwkoV15yZVTcustno20151123114702mobile8mBGFNfe1o/rzAx2Ost2IQ==prodcodeCMCC_10tokenVqHAab3JYXBDkCoO`（注意手机号是 aes 加密再 base64 编码后的）
    * 对以上拼接的字符串进行SHA1签名算法，将签名值转化为十六进制的编码串：`86ee7d160f03b54f27c99fbfebaa276c8a1a3017`

备注:

**注意:成功状态码：200; 特殊处理状态码：410,411,428,429,511（必须再次查询订单确认）；其它错误码为失败。对于我们系统返回的异常信息（非标准JSON结果）和请求超时无响应等结果，需再次查询订单确认结果。**



**1.3 充值状态回调:**


| 地址	 |/客户企业提供回调地址|
| --------------------     | ------- |
|方法	   | POST |
|header |Content-Type为`application/json` |

参数列表：

|Label |Description |Type |Required
| --- | --- | --- | --- |
| custno |客户自定义的订单流水号，原样返回 |`string` |`true`|
| orderno |流量平台生成的订单号 |`string` |`true`|
| code |返回的状态码，详见错误码说明 |`string` |`true`|
| info |返回说明信息 |`string` |`true`|
| sign |签名串，签名规则为"code"字符串拼接实际的code值,加上"custno"字符串拼接实际的custno值,加上"info"字符串拼接实际的info值,加上"orderno"字符串拼接实际的orderno值, 最后拼接"token"(小写)串和实际的token值然后将拼接后的字符串进行sha1签名,实例可参考 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`|

返回：

```json
{
    "info": "1"
}
```

描述:

  又拍云手机流量平台将运营商的订单充值结果异步返回给客户，客户接收后返回json格式的{'info':'1'}给流量平台以确认接收到回调,如果又拍云手机流量平台没有接收到正确格式的确认信息，会每隔1分钟再次回调给客户，最多重新回调3次

备注:

**注意:成功状态码：200；失败状态码：430，530；其它错误码都为充值中。对于系统返回的异常信息（非标准JSON结果）和请求超时无响应等结果，需做充值中处理。**


**1.4.1 查询订单充值状态:**


| 地址	 | /seekOrder |
|--------------------     | ------- |
|方法	 |POST |
|header |Content-Type为`application/json` |

参数列表：

|Label |Description |Type |Required |
| --- | --- | --- | --- |
| custno |客户提交的订单流水号 |`string` |`true` |
| appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true` |
sign |签名串，签名规则为"appkey"字符串拼接实际的appkey值,加上"custno"字符串拼接实际的custno值,如果传了requesttime,请加上"requesttime"拼接requesttime的值,如果没有传则无需处理,最后拼接"TOKEN"(大写)串和实际的token值,然后将拼接后的字符串进行sha1签名,实例可参考 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |`string` |`true`|
requesttime |该订单创建时间。该参数为可选参数,格式为"YYYY-MM-DD HH:mm:ss",为保证有效性,我们会在给定requesttime前后1个小时范围内查询;如果未传该参数,我们将在最近一周的订单中为你查询	 |`string` |`false` |

返回：

```json
{
    "code": "返回的状态码，详见错误码说明",
    "custno": "客户自定义的订单流水号，原样返回",
    "info": "返回说明信息"
}
```

描述:

订单充值详情查询接口

备注:

**注意:成功状态码：200；失败状态码：430，516，530；其它错误码都为充值中。对于系统返回的异常信息（非标准JSON结果）和请求超时无响应等结果，需做充值中处理。**

**1.5 查询金额**

|地址	 |/getMyBalance
|--------------------     | ------- |
|方法	 |POST|
|header |Content-Type为`application/json`|

参数列表：

|Label |Description |Type |Required|
|---|---|---|---|
|appkey |鉴权账号，由又拍云手机流量平台提供 |`string` |`true`|
|sign |签名串，签名规则为"appkey"字符串拼接实际的appkey值,再拼接"TOKEN"(大写)串和实际的token值,然后将拼接后的字符串进行sha1签名,实例可参考 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip))  |`string` |`true`|

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

查询当前账户的金额,客户应该使用availBalance,其它参数仅仅提供参考.

备注:

## 错误码说明

**请特别留意各接口备注说明**

|错误码   | 说明             | 错误码   | 说明 |
|:------- | :-------         | :------- | :------- |
|200      | 成功             | 201      | 充值中 |
|410      | 运营商返回超时   | 422      | 用户状态异常 |
|430      | 订购/充值失败    | 502      | sign签名错误 |
|503      | 余额不足         | 504      | 非法的回调地址 |
|505      | 手机号不合法     | 506      | 用户不能订购该套餐 |
|507      | 接口参数异常     | 508      | 令牌鉴权不通过 |
|509      | 客户未认证授权   | 510      | 查询太频繁 |
|511      | 内部异常         | 512      | 订单号重复 |
|513      | 运营商请求超时   | 514      | ip不在白名单 |
|515      | 该客户不存在     | 516      | 不存在此订单 |
|517      | 无法识别的手机号 | 519      | 当前appkey或appsecret错误 |
|520      | 活动不存在       | 526      | 合同不在有效期 |
|527      | token不在有效期  | 530      | 订购/充值失败 |
|531      | 签名不通过       | 532      | 订单号长度超过30 |
|533      | aes加密有误      |          |



