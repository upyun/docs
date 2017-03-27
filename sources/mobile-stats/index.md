## 平台概述
又拍云手机流量营销平台，整合移动、电信、联通三大运营商流量资源，将强大的流量营销服务，通过接口等形式提供给商家合作伙伴，帮助商家开展品牌宣传、APP/游戏/微信公众号/网站的拉新与促活等多种营销活动。

通过接入又拍云流量营销平台，商家可以获得更多流量、用户和收益，而商家提供的活动也让用户获得了三网通用流量和更加丰富的活动体验。

### 产品特点
1.三网通用，成本优势，覆盖面广

2.快速对接、全流水、易对账、精结算

### 应用场景
1.APP 下载赠流量

2.微信公众号增粉

3.奖品礼券

4.流量加油站

5.积分兑换流量

6.活动比赛送流量

7.流量红包

8.员工福利

### 对接说明

1.请联系商务（Tel:0571-89775132）确定您的应用场景需求，获取对应流量报价

2.注册又拍云账号，如果您已有账号，请登录 [又拍云控制台](https://console.upyun.com/login/) 完成企业/个人认证

3.前往导航 > 工具箱 > 手机流量营销页面，开通该服务（开通邮件中包含接口地址）

4.通过流量营销控制台配置回调地址

5.出口 IP 出于安全考虑暂不支持控制台配置，可联系我司商务完成生产环境联调

**业务交互流程:**

<img src="http://up-static.b0.upaiyun.com/phone-traffic/upyun-mobile-traffic.png" width="830" />

## 接口文档

又拍云手机流量平台接口入口地址统一为：[https://ptp-api.upyun.com](https://ptp-api.upyun.com)，请求接口时，统一使用 `POST` 方法， 文档类型统一为 `Content-Type: application/json`，使用 JSON 格式发送请求， 响应结果也会以 JSON 格式返回；文档中接口的请求地址只包含路径部分，实际请求时，需要加上统一入口地址 [https://ptp-api.upyun.com](https://ptp-api.upyun.com)。文档中描述签名计算时，都是先正序排序（给出的示例字符串已经是排序后的结果），再做 SHA1 运算。

本文档一共描述了四个接口和一个充值回调说明，接口如下：

- 获取签名 `POST /refreshToken`
- 提交订单 `POST /chargeOrder`
- 查询订单状态 `POST /seekOrder`
- 查询余额 `POST /getMyBalance`

针对以上接口，目前提供了 php 版本 sdk，用于提供接口调用示例， 各企业用户可以根据自己的需要进行封装，完善错误处理，下载地址：[点击下载](http://up-static.b0.upaiyun.com/phone-traffic/upyun-mobile-stats.zip)。

### 接口详情

**1.获取计算签名需要的 token**

接口说明

该接口用于获取 token，调用其他接口时，需要使用 token 计算签名。由于 token 需要在后续的接口请求中，全局共享，所以在并发请求的场景下，建议将 token 保存在 Redis/Mysql 等第三方存储中

请求地址

    POST /refreshToken

请求参数

| 名称 | 类型 |  是否必须 |  描述 |
| --- | --- | --- | --- |
| appkey | String |  必须 | appkey，由又拍云提供，可以登录手机流量后台查看 |
| appsecret | String | 必须 |  appsecret，由又拍云提供，可以登录手机流量后台查看 |

请求示例

```
curl https://ptp-api.upyun.com/refreshToken -H "Content-Type: application/json" -d "{\"appkey\": \"your_appkey_put_here\", \"appsecret\": \"your_appsecret_put_here\"}"
```

响应参数

| 名称 | 类型 |   示例值 |  描述 |
| --- | --- | --- | --- |
| code | String |  200 | 响应状态码，200 表示正常，其他详见错误码 |
| token | String | XsdOpxqudzMnw | 用于签名计算需要的 token，token 默认有效期为 24 小时，仅最后一次请求接口生成的 token 才有效|
| info | String | 请求成功 | |

响应示例

```json
{
    "code": "200",
    "token": "XsdOpxqudzMnw",
    "info": "请求成功"
}
```

**2.创建订单**

接口说明

创建手机流量充值订单

请求地址

    POST /chargeOrder

请求参数

| 名称 | 类型 |  是否必须 |  描述 |
| --- | --- | --- | --- |
|appkey | String |  必须 | E1KRHcKd3qoef8V1 |  |
|mobile | String | 必须 |  被充值手机号进行 AES 对称加密再 base64 转码后的值，一次只允许一个手机号码，见[示例程序](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip) |
|prodcode | String |  必须 | 流量包的产品编号 |
|custno | String | 必须 | 客户自定义的订单号，用于客户内部记录订单，长度必须小于30， 且每次充值，编号不重复	 |
|sign | String | 必须 |  根据 token 和请求参数计算的签名，签名规则见算法说明部分 ([示例程序下载](http://up-static.b0.upaiyun.com/phone-traffic/AESCryptSample.zip)) |
|prodtime| Integer |  选填 |流量时段，`1` 表示全时段流量，`0` 表示闲时流量，默认为全时段流量|
|prodstandard| Integer |  选填 | 用于区分网络类型，可选值分别为：`7`表示全部网络类型，`3`表示 2g 和 3g 两种网络类型，`1`表示 2g 网络类型，默认值为`7`|

响应参数

| 名称 | 类型 |   示例值 |  描述 |
| --- | --- | --- | --- |
| code | String | 200 | 响应状态码，200 表示创建订单成功；410 511 必须再次查询订单确认；其他错误码视为创建失败；系统响应超时等其他非正常响应，需要调用查询订单接口确认 |
| custno | String |  20150238829 |  客户订单号，和请求值一致 |
| orderno | String | 2016298237019 | 又拍云平台的订单号 |
| info | String |  充值成功 | |

算法说明

1. 计算手机号的 AES 对称加密，再 base64 转码后的值

2. 将接口请求参数按照请求参数名称正序排序;

3. 将请求参数和请求参数值拼接成字符串：`appkey${YourAppKey}custno${YourCustno}mobile${YourAesEncodedMobile}prodcode${YourPrdcode}token${YourToken`(将字符串中的变量`${...}`部分替换成实际值即可，)

4. 对拼接后的字符串进行SHA1指纹运算，得到的 16 进制字符串 `b5cd94c99e3e576f5e2ca0e66ee3da670cb1c62b` 便是 sign 值

示例

参数拼接后的字符串为：`appkey3P83lWwkoV15yZVTcustno20151123114702mobile8mBGFNfe1o/rzAx2Ost2IQ==prodcodeCMCC_10tokenVqHAab3JYXBDkCoO`（注意手机号是 aes 加密再 base64 编码后的），进行 SHA1 运算得到的 sign 值为：`8b05f52e605c7ab89a895eb730cacab4cbeabd4c`

响应示例

```json
{
    "code": "200",
    "custno": "20150238829",
    "orderno": "2016298237019",
    "info": "充值成功"
}
```

**3.查询订单状态**

接口说明

查询充值订单当前的状态，用于确认是否充值成功

请求地址

    POST /seekOrder

请求参数

| 名称 | 类型 |  是否必须 |  描述 |
| --- | --- | --- | --- |
| custno | String | 必须 | 客户提交的订单号 |
| appkey | String | 必须 | 鉴权账号 |
| sign | String | 必须 | 签名，签名规则为：将 `appkey${YourAppKey}custno${YourCustno}requesttime${YourReqestTime}TOKEN${YourToken}` 中`${...}`变量部分替换为实际值，再进行 SHA1 运算。如果请求参数不包含 `requesttime`，计算签名则不需要该值 |
| requesttime | String | 否 |  查询的时间点，该参数为可选参数，格式为"YYYY-MM-DD HH:mm:ss"，当传递该参数时，我们会在给定时间点的前后 1 个小时范围内查询；如果未传该参数，我们将在最近一周的订单中查询	|

响应参数

| 名称 | 类型 |   示例值 |  描述 |
| --- | --- | --- | --- |
| code | String |  200 | 响应状态码，200 表示充值成功；430 516 530 表示充值失败；511 必须人工再次确认；其他错误码视为充值中 |
| custno | String |  20150238829 |  客户自定义订单号 |
| info | String |  成功 | |

响应示例

```json
{
    "code": "200",
    "custno": "2015823871239",
    "info": "充值成功"
}
```

**4.查询余额**

请求地址

    POST /getMyBalance

请求参数

| 名称 | 类型 |  是否必须 |  描述 |
|---|---|---|---|
| appkey | String |  必须 | |
| sign | String | 必须 | 签名，算法为 `appkey${YourAppKey}TOKEN${Yourtoken}` 中`${...}`变量部分替换实际值，再进行 SHA1 运算得到的字符串便为 sign 值 |

响应参数

| 名称 | 类型 |   示例值 |  描述 |
| --- | --- | --- | --- |
| code | String | 200 | 响应状态码 |
| balance | Float | 100.00 | 账户余额（包含冻结金额） |
| freeze | Float | 20.00 | 冻结金额，正在交易的订单金额 |
| availBalance | Float | 80.00 | 实际可用余额（不包含冻结金额）|

响应示例
```json
{
    "code": "200",
    "balance": 100,
    "freeze": 20,
    "availBalance": 80
}
```

### 充值回调说明

客户需要在又拍云手机流量后台填写充值回调地址，并注意防火墙配置，不能屏蔽又拍云服务器地址。配置成功后，每次充值后，又拍云会向该地址发送 `POST` 请求，请求的数据格式为 `application/json`

请求参数

| 名称 | 类型 |  是否必须 |  描述 |
| --- | --- | --- | --- |
| code | String | 必须 | 响应状态码，200 表示充值成功；430,530 表示充值失败，其他错误码或异常信息表示充值中 |
| orderno | String | 必须 | 又拍云平台的订单号 |
| custno | String | 必须 | 客户自定义订单号 |
| info | String | 必须 | 描述信息 |
| sign | String | 必须 | 签名，客户可以使用该值校验请求是否合法，强烈建议接收充值回调时进行校验。签名规则为：将 `code${Code}custno${YourCustno}info${Info}orderno${OrderNo}token${YourToken}` 字符串中的变量部分`${...}`替换为请求参数中的值，再对得到的字符串进行 SHA1 签名 |

客户接受请求成功后，需要响应如下 `json` 值：

```json
{
    "info": "1"
}
```

否则，又拍云将隔 1 分钟再重试一次，一共回调 3 次



## 错误码说明

**注：需要留意每个接口响应参数说明中的 code 处理方式**

|错误码   | 说明             | 错误码   | 说明 |
|:------- | :-------         | :------- | :------- |
|200      | 成功             | 201      | 充值中 |
|410      | 运营商返回超时   | 422      | 用户状态异常 |
|430      | 订购/充值失败    | 502      | sign签名错误 |
|503      | 余额不足         | 504      | 非法的回调地址 |
|505      | 手机号不合法     | 506      | 用户不能订购该套餐 |
|507      | 接口参数异常     | 508      | 令牌鉴权不通过 |
|509      | 客户未认证授权   | 510      | 查询太频繁 |
|511      | 内部异常，此时对订单的查询、充值等操作等需要联系客户再次确认    | 512      | 订单号重复 |
|513      | 运营商请求超时   | 514      | ip不在白名单 |
|515      | 该客户不存在     | 516      | 不存在此订单 |
|517      | 无法识别的手机号 | 519      | 当前appkey或appsecret错误 |
|520      | 活动不存在       | 526      | 合同不在有效期 |
|527      | token不在有效期  | 530      | 订购/充值失败 |
|531      | 签名不通过       | 532      | 订单号长度超过30 |
|533      | aes加密有误      |          |



