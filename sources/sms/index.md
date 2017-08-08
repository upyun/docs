## 1. 介绍
#### 1.1 又拍云短信介绍
又拍云短信服务通过对网络服务资源统一管理和调度，三网合一，全网覆盖，采用行业专用通道，为用户提供企业级短信送达服务。

#### 1.2 又拍云短信组成
又拍云目前可提供的短信通道分为两大类：免审签名通道与普通签名通道，两种通道按类型又分为行业短信与营销短信。

短信的组成由短信模板内容+短信签名组成，短信长度限制为 350 字。

#### 1.3 又拍云短信模板
1.&nbsp;又拍云短信模板分为两种，行业短信与营销短信模板，两类模板最大的区别就是营销短信尾部须添加“退订回T、退订回TD或退订回N”字样。

2.&nbsp;又拍云短信提供的模板短信由固定内容与多个变量构成，变量格式为（{$var1}，{$var2}…），变量内容可以是汉字、字母、数字或者特殊字符自合。

3.&nbsp;已审核通过的短信模板，若需修改模板重新提交审核，在审核期间无法使用，需重新审核通过才可使用。

4.&nbsp;每种通道可分别创建模板上限为 40 个。

5.&nbsp;行业短信模板中，单个手机号每天能接收的短信次数为 10 次，营销短信无限制。

6.&nbsp;短信内容不能含有政治敏感字眼和“黄赌毒”等低俗敏感字样，出现违法违规或损害相关他人权益的，平台将保留最终追究的权利。

7.&nbsp;不支持全变量短信模板，请将短信内容的结构安排合理。

8.&nbsp;请您避免在短信正文中使用 “【 ” ， “ 】”，“<”，“>” 等特殊字符。

#### 1.4 又拍云短信签名
1.&nbsp;每种通道的短信签名最多可创建 20 个签名。

2.&nbsp;单个签名长度介于 3-8 个字符之间；

3.&nbsp;不能含违禁关键字，不能有任何特殊符号（如-.+.#);

4.&nbsp;无需添加【】符号，系统将自动生成。

#### 1.5 计费标准
每条短信内容（包括但不限于标点符号、英文字符、汉字等）长度为70个字符（包含签名）如发送短信内容超出 70 个字符，将以每 67 字符（包括但不限于标点符号、英文字符、汉字等）为一条短信进行累计计费。单条短信字符数最大值为 350 个字。短信服务的付费方式为短信资源包计费，按照短信发送成功量进行短信条数的扣除，发送多少即扣除短信资源包内对应的条数。

#### 1.6 购买流程
进入管理后台中【工具箱】-【短信服务】-【账号充值】查看，线下充值购买。

<br>
## 2. 短信接入

#### 2.1 接入说明
短信服务属于收费功能，用户可根据自身需求通过线下购买短信资源包进行使用，不支持自选短信包。

#### 2.2 接入步骤
1.&nbsp;注册又拍云账号（已有又拍云账号无需注册）

2.&nbsp;进行实名认证

3.&nbsp;创建模板

4.&nbsp;发送短信

#### 2.3 注册又拍云账号
用户若需开通短信服务，需先在官网注册又拍云账号，官网地址如下: <https://www.upyun.com/>,创建完成后完成实名认证，即可申请开通。（已有账号需完成实名认证后即可开通短信服务）

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E6%B3%A8%E5%86%8C%E7%95%8C%E9%9D%A2.png" width="830" />

#### 2.4 实名认证
注册成功又拍云账号之后，即可通过后台的【工具箱】-【短信服务】入口进入短信服务界面。短信服务需完成实名认证后才可开通，个人用户需完成个人实名认证，企业用户需完成企业实名认证。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E7%9F%AD%E4%BF%A1%E5%85%A5%E5%8F%A3%E7%95%8C%E9%9D%A2.png" width="830" />

实名认证界面

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E5%AE%9E%E5%90%8D%E8%AE%A4%E8%AF%81%E7%95%8C%E9%9D%A2.png" width="830" />

#### 2.5 创建模板
创建模板可分别创建普通签名通道与免审签名通道的模板，模板类型分为行业短信与营销短信，<span style="color:red">特别注意的是营销短信尾部须添加“退订回T、退订回TD或退订回N”字样，否则会发送失败。</span>
用户点击管理后台的【工具箱】-【短信服务】-【模板】-【创建模板】进行模板创建。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E6%A8%A1%E6%9D%BF%E5%88%97%E8%A1%A8%E7%95%8C%E9%9D%A2.png" width="830" />

用户创建好模板之后，管理员进行审核（免签通道无需报备签名，只需审核模板，非特殊情况下，审核时间为工作日内的 1-3 小时；普通通道非特殊情况下审核时间为 1-3 个工作日，若签名已报备过，则也是工作日内的 1-3 小时），审核通过后用户可以使用后台导入界面或调用发送短信接口进行发送。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E5%88%9B%E5%BB%BA%E6%A8%A1%E6%9D%BF%E7%95%8C%E9%9D%A2.png" width="830" />

#### 2.6 发送短信
在模板审核通过之后，就可以通过后台导入界面或调用接口（具体接口使用方式见接口文档）进行短信发送。

用户通过点击管理后台的【导入】-【搜索】-【导入文件】-【提交】进行短信发送。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E9%80%89%E6%8B%A9%E6%A8%A1%E6%9D%BF%E7%95%8C%E9%9D%A2.png" width="830" />

选择具体所需的通道、短信类型以及模板之后，就可以通过导入 txt 或者 csv 格式的文本文件进行发送。文本格式第一列为手机号，2-5 列填变量参数，无变量参数留空，txt 与 csv 需用英文“,”分隔各列。文本格式如下图：

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E5%AF%BC%E5%85%A5%E6%89%8B%E6%9C%BA%E5%8F%B7%E6%A0%B7%E4%BE%8B%E7%95%8C%E9%9D%A2.png" width="830" />

导入完文件后，点提交即可完成短信发送。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E5%8F%91%E9%80%81%E7%9F%AD%E4%BF%A1%E7%95%8C%E9%9D%A2.png" width="830" />

<br>
## 3. 数据统计
#### 3.1 短信资源包
又拍云短信服务属于预付费服务，需提前购买短信资源包之后方可使用。购买短信资源包之后，金额会自动转化为短信可使用条数。在统计用户短信条数时，若短信发送失败，则不扣除使用条数。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E7%9F%AD%E4%BF%A1%E6%80%BB%E8%A7%88%E7%95%8C%E9%9D%A2.png" width="830" />

#### 3.2 报表统计
在发送明细中，可以根据所发送短信的手机号、发送状态、时间，查询所有短信的发送状态。

<img src="http://upyun-assets.b0.upaiyun.com/docs/sms/%E6%8A%A5%E8%A1%A8%E6%98%8E%E7%BB%86%E7%95%8C%E9%9D%A2.png" width="830" />

<br>
## 4. 常见问题
1.&nbsp;短信模板最多支持创建多少个？

答：单个通道下的模板上限为40个，即普通签名通道与免审签名通道的模板上限各为40个。

2.&nbsp;短信超出字数如何收费？
答：每条短信内容（包括但不限于标点符号、英文字符、汉字等）长度为70个字符（包含签名）如发送短信内容超出70个字符，将以每67字符（包括但不限于标点符号、英文字符、汉字等）为一条短信进行累计计费。单条短信字符数最大值为350个字。

3.&nbsp;短信模板如何创建？
答：在管理后台【短信服务】-【模板】-【创建模板】中创建。

4.&nbsp;短信资源包可退款吗？
答：短信资源包在购买成功后不支持退款，建议您按需购买。

5.&nbsp;短信发送成功，但实际未能收到短信是什么原因？

答：

1）手机是否长时间未关机，可以重启下；

2）是否是双卡双待手机，换个卡槽试试；

3）是都安装了安全软件，短信被拦截到垃圾信箱中；

4）是都处于停机状态；

5）出于黑名单状态，投诉过运营商，或退订过业务；

6.&nbsp;短信发送失败，还会扣费吗？

答：不会，短信发送失败的话不会扣费。

7.&nbsp;短信支持三网都可发送吗？

答：支持。

8.&nbsp;变量中是否可以直接写网站链接？

答：可以。

<br>
## 5. 接口文档

####5.1 短信模板新增接口
<b>接口地址</b>

    POST https://sms-api.upyun.com/api/templates

<b>参数定义</b>

| 参数 | 说明 |  Required（必填）|
| --- | --- | --- |
| title | 模板标题(少于12个字) | ✔️ |
| content | 模板内容（模版签名+模版内容+2少于350个字）|  ✔️ |
| temp_sign | 模版签名（3～8个字）| ✔️ |
| sign_location | 模版签名位置 (top,bottom) | ✔️ |
| type | 模板类型 (industry, marketing) | ✔️ |
| report_type | 审核类型（vip, general）| ✔️ |

<b>响应</b>

创建成功返回响应码和内容。

    201 Created
    {
      "title": "title",
      "temp_sign": "sign",
      "content": "content",
      "sign_location": "top",
      "type": "industry",
      "status": "review",
      "updated_at": "2017-04-10T02:58:12.000Z",
      "report_type": "vip",
      "id": 4,
      "owner": {
        "id": 1,
        "name": "taevas",
        "general": 8000,
        "vip": 0,
        "industry": 4,
        "marketing": 0
      }
    }

<b>响应值说明</b>

| 参数 | 说明 |
| --- | --- |
| title | 模板标题 |
| temp_sign | 模板签名 |
| content | 模板内容 |
| status | 模板状态（审核中、通过、失败) |
| type | 短信类型(营销、行业) |
| updated_at | 创建时间 |
| report_type | 审核类型（免签, 普通）|
| id | 模板编号|
| owner | 创建者信息 |
| owner.id | 用户编号 |
| owner.name | 用户昵称 |
| owner.general | 普通短信剩余条数 |
| owner.vip | 免签短信剩余条数 |
| owner.industry | 已发送行业短信条数 |
| owner.marketing    | 已发送营销短信条数 |

<b>请求示例</b>

    POST https://sms-api.upyun.com/api/templates HTTP/1.1
    Authorization: <your token>
    {
      "title": "title",
      "content": "content",
      "temp_sign": "sign",
      "sign_location": "top",
      "type": "industry",
      "report_type": "vip"
    }

####5.2 短信模板删除接口
<b>接口地址</b>

    DELETE https://sms-api.upyun.com/api/templates/{template_id}

<b>参数定义</b>

| 参数 | 说明 |  Required（必填）|
| --- | --- | --- |
| template_id | 模板编号 | ✔️ |

<b>响应</b>

    204 No Content

<b>请求示例</b>

    DELETE https://sms-api.upyun.com/api/templates/4
    Authorization: <your token>

####5.3 短信模板修改接口
<b>接口地址</b>

    PATCH /api/templates/{template_id}

<b>参数定义</b>

| 参数 | 说明 |  Required（必填）|
| --- | --- | --- |
| template_id | 模板编号 | ✔️ |
| title | 模板标题(少于12个字) |  |
| content | 模板内容（模版签名+模版内容+2少于350个字）|  |
| temp_sign | 模版签名（3～8个字）|  |
| sign_location | 模版签名位置 (top,bottom) |  |

<b>响应</b>

    200 OK
    {
      "title": "hello",
      "temp_sign": "sign",
      "content": "content",
      "sign_location": "top",
      "type": "industry",
      "status": "review",
      "updated_at": "2017-04-10T03:31:18.000Z",
      "id": 3,
      "report_type": "vip",
      "owner": {
        "id": 1,
        "name": "taevas",
        "general": 8000,
        "vip": 0,
        "industry": 4,
        "marketing": 0
      }
    }

<b>响应值说明</b>

| 参数 | 说明 |
| --- | --- |
| title | 模板标题 |
| temp_sign | 模板签名 |
| content | 模板内容 |
| status | 模板状态（审核中、通过、失败) |
| type | 短信类型(营销、行业) |
| updated_at | 创建时间 |
| report_type | 审核类型（免签, 普通）|
| id | 模板编号|
| owner | 创建者信息 |
| owner.id | 用户编号 |
| owner.name | 用户昵称 |
| owner.general | 普通短信剩余条数 |
| owner.vip | 免签短信剩余条数 |
| owner.industry | 已发送行业短信条数 |
| owner.marketing    | 已发送营销短信条数 |

<b>请求示例</b>

    PATCH https://sms-api.upyun.com/api/templates/3
    Authorization: <your token>
    {
      "title": "hello"
    }

####5.4 查询模板接口
<b>接口地址</b>

    GET https://sms-api.upyun.com/api/templates

<b>参数定义</b>

| 参数 | 说明 |  Required（必填）|
| --- | --- | --- |
| status | 模板状态 (review, success, defeat) |  |
| type | 模板类型 (industry, marketing) |  |
| report_type | 审核类型（vip, general）|  |

<b>响应</b>

    200 OK
    {
      "templates": [
        {
          "title": "hello",
          "temp_sign": "sign",
          "content": "content",
          "sign_location": "top",
          "type": "industry",
          "status": "review",
          "updated_at": "2017-04-10T03:31:18.000Z",
          "id": 3,
          "report_type": "vip",
          "owner": {
            "id": 1,
            "name": "taevas",
            "general": 8000,
            "vip": 0,
            "industry": 4,
            "marketing": 0
          }
        }
      ]
    }

<b>响应值说明</b>

| 参数 | 说明 |
| --- | --- |
| title | 模板标题 |
| temp_sign | 模板签名 |
| content | 模板内容 |
| status | 模板状态（审核中、通过、失败) |
| type | 短信类型(营销、行业) |
| updated_at | 创建时间 |
| report_type | 审核类型（免签, 普通）|
| id | 模板编号|
| owner | 创建者信息 |
| owner.id | 用户编号 |
| owner.name | 用户昵称 |
| owner.general | 普通短信剩余条数 |
| owner.vip | 免签短信剩余条数 |
| owner.industry | 已发送行业短信条数 |
| owner.marketing    | 已发送营销短信条数 |

<b>请求示例</b>

    GET https://sms-api.upyun.com/api/templates?type=industry&report_type=vip
    Authorization: <your token>

####5.5 短信发送接口
<b>接口地址</b>

    POST https://sms-api.upyun.com/api/messages

<b>参数定义</b>

| 参数 | 说明 |  Required（必填）|
| --- | --- | --- |
| mobile | 手机号，逗号分隔 | ✔️ |
| template_id | 模板编号 | ✔️ |
| vars | 短信参数（以 `|` 分隔）|  |

<b>响应</b>

    200 OK
    {
      "message_ids": [
        {
          "error_code": "ILLEGAL_MOBILE",
          "mobile": "15757"
        },
        {
          "message_id": 30,
          "mobile": "15757654321”
        }
      ]
    }

<b>响应值说明</b>

| 参数 | 说明 |
| --- | --- |
| message_ids | 给所有手机号发送短信的结果 |
| error_code | 错误情况 |
| message_id | message 编号 |
| mobile | 手机号 |

<b>请求示例</b>

    POST https://sms-api.upyun.com/api/messages
    Authorization: <your token>
    {
      "template_id": 2,
      "mobile": "15757,15757654321”
    }

####5.6 短信报表查询接口
<b>接口地址</b>

    GET https://sms-api.upyun.com/api/messages

<b>参数定义</b>

| 参数 | 说明 |  Required（必填）|
| --- | --- | --- |
| from | 起始时间 |  |
| to | 结束时间 |  |
| mobile | 手机号 |  |
| status | 消息发送的状态 |  |
| page | 页码 |  |
| per_page | 每页显示数量 |  |

<b>响应</b>

    200 OK
    {
      "messages": [
        {
          "id": 4,
          "template_id": 2,
          "report_type": "vip",
          "title": "测试测试",
          "type": "industry",
          "content": "hello，hello",
          "temp_sign": "落子時",
          "mobile": "15757654321”,
          "sent_at": null,
          "created_at": "2017-04-07T08:47:37.000Z",
          "status": "processing",
          "quantity": 1,
          "owner": {
            "id": 1,
            "name": "taevas",
            "general": 8000,
            "vip": 0,
            "industry": 4,
            "marketing": 0
          }
        }
        {
          "id": 2,
          "template_id": 2,
          "report_type": "vip",
          "title": "测试测试",
          "type": "industry",
          "content": "hello，hello【落子時】",
          "temp_sign": "落子時",
          "mobile": "15757123456”,
          "sent_at": "2017-04-06T08:36:01.000Z",
          "created_at": "2017-04-06T08:36:00.000Z",
          "status": "success",
          "quantity": 1,
          "owner": {
            "id": 1,
            "name": "taevas",
            "general": 8000,
            "vip": 0,
            "industry": 4,
            "marketing": 0
          }
        }
      ],
      "total": 2
    }

<b>响应值说明</b>

| 参数 | 说明 |
| --- | --- |
| id | 短信编号 |
| template_id | 模板编号 |
| report_type | 审核类型 |
| title | 模板标题 |
| type | 短信类型(营销、行业) |
| content | 模板内容 |
| temp_sign | 模板签名 |
| mobile | 手机号 |
| sent_at | 发送时间 |
| created_at | 创建时间 |
| status | 短信发送状态 |
| owner | 创建者信息 |
| owner.id | 用户编号 |
| owner.name | 用户昵称 |
| owner.general | 普通短信剩余条数 |
| owner.vip | 免签短信剩余条数 |
| owner.industry | 已发送行业短信条数 |
| owner.marketing    | 已发送营销短信条数 |

<b>请求示例</b>

    curl -H "Authorization: <your token>" https://sms-api.upyun.com/api/messages?mobile=15757654321

##6. 错误码
####6.1 错误码列表

| 错误码 | 说明 | 状态码 |
| --- | --- | --- |
| INVALID_FILE | 文件不存在或大小格式不符合规范 | 400 |
| MISSING_PARAMETER | 必填项为空 | 400 |
| INVALID_CREDENTIALS | 无效的认证 | 401 |
| REQUIRE_TEMPLATE_OWNER | 没有模板的权限 | 403 |
| REQUIRE_SIGNATURE_OWNER | 没有签名的权限 | 403 |
| USER_NOT_FOUND | 用户不存在 | 404 |
| TEMPLATE_NOT_FOUND | 模板不存在 | 404 |
| NOT_FOUND | 页面不存在 | 404 |
| INSUFFICIENT_FUNDS | 短信额度不足，请充值 | 409 |
| MOBILE_LIMIT | 手机号数量上限 | 409 |
| ILLEGAL_MOBILE | 手机号无效 | 409 |
| ILLEGAL_PARAMER | 参数无效 | 409 |
| TEMPLATE_LIMIT | 模板数量达到上限 | 409 |
| TEMPLATE_AUDITING | 模板正在审核，无法使用 | 409 |
| TEMPLATE_NOT_AUDITED | 模板未审核 | 409 |
| ILLEGAL_SIGNATURE | 模版签名长度不合法，必须为3～8个字 | 409 |
| ILLEGAL_TITLE | 模版标题长度不合法，必须小于12个字 | 409 |
| TEMPLATE_FAILED | 模板审核失败，请重新提交 | 409 |
| ILLEGAL_CONTENT_LENGTH | 模版内容长度不合法，签名+内容必须小于348个字  | 409 |
| ILLEGAL_REPORT_TYPE | 无效的审核类型  | 409 |
| ILLEGAL_TEMPLATE_ID | 无效的模板编号 | 422 |
| SMS_SYSTEM_ERROR | SMS 服务异常 | 500 |
| SYSTEM_ERROR | 系统错误 | 500 |

####6.2 错误码响应

    400 Bad Request
    {
      “error_code”: “INVALID_FILE”,
      “message”: “文件不存在或大小格式不符合规范”
    }

####6.3 响应说明
| 参数 | 说明 | 状态码 |
| --- | --- | --- |
| error_code | 仅出现在错误码列表中 |
| message | 额外的描述，不与错误码列表中的说明相匹配 |
