## 准备工作

为了快速完成注册和配置过程，您需要提前准备如下事项：

1.一个有效的邮箱地址，主要用于密码重置找回、邮箱快捷登陆、域名审核反馈等，您可以在后期进行更换；

2.一个有效的个人手机号码，主要用于密码重置找回、账号快捷登陆、获取服务通知（如余额不足提醒和账户欠费提醒），您可以在后期进行更换

3.至少需要准备一个回源 IP 地址或者域名，需要保证该源站可以正常提供服务，您可以在后期添加更多回源地址

4.提供需要加速的网站访问域名，需要保证该访问域名可正常提供服务，并且已经备案（ 未备案将无法配置成功 ）
 
----------

##创建帐号

打开 [注册界面](https://console.upyun.com/register/)，创建一个免费的帐号或者在 [又拍云官网](http://www.upyun.com) 页面右上角点击「免费注册」，进入如下界面：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-register.png" height="490" width="800" />

您可根据自身的情况选择 “企业” 或 “个人” 类型，请填写完整的注册信息（ 注意阅读又拍云服务条款 ），并确保电子邮件地址正确，以保障您能够正常接收到服务通知。
    
    
----------

##帐号认证

注册完毕后，登录 [又拍云管理控制台](https://console.upyun.com/login/)，您需要进行企业或个人认证，认证完成后方可创建服务，否则将无法使用又拍云服务。账户认证可参见如下截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-identy-Auth.png" height="490" width="800" />


**企业认证：**

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-enterprise-certification.png" height="490" width="800" />

**个人认证：**

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-personal-certification.png" height="490" width="800" />

提交后，需要等待审核通过，届时会通过站内信、邮件进行通知。

----------

##创建服务

帐户身份认证完成之后，您就可以开始配置您的初始服务了，具体操作如下步骤所示：

1.选择服务类型，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-choose-server.png" height="490" width="800" />

2.正式创建服务，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-create-server.png" height="490" width="800" />

详细配置项包括：

 - 根据需要选择加速服务类型，包括 `全网加速` 和 `直播加速` 服务

 - 根据需要选择源站类型，包括 `自主源` 和 `又拍云` 

 - 根据您的业务情况，选择一个合适的服务名，例如：upyun_image

 - 填写需要加速的网站访问域名，必须要保证该域名已经备案，否则无法通过审核

 - 根据网站回源信息进行配置（ IP 地址或者域名 ）

 - 确认域名配置信息及状态，当域名状态为“服务中”时，配置才会生效
 

详细配置可点击如下教学视频链接：

- [自主源站配置教学视频](https://techs.b0.upaiyun.com/videos/cdnpage/create_dynamic_cdn_and_cache_setting.html)
   
- [又拍云存储配置教学视频](https://techs.b0.upaiyun.com/videos/cdnpage/create_cdn_and_use_storage_services.html)


----------


##配置测试

假设上述配置已全部完成，您的域名为 `test1.upyun.com`，配置如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-test1.png" height="490" width="800" />

1.您可通过 ping 加速域名的 CNAME 地址 ` cptest123.b0.aicdn.com` ，获取 CDN 节点 IP 为 `183.158.35.58` ( 示例 IP 为杭州电信节点出口 IP )。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-test2.png" height="350" width="800" />


2.然后打开终端的 Hosts 文件，将 `183.158.35.58  test.yourdomain.com`  写入其中并保存（ 注意：测试完成后，进行删除 )。

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-test3.png" height="350" width="800" />

3.保存以后，打开 Chrome 浏览器，按 F12 键开始抓包，访问域名测试链接，例如 `http:// test.yourdomain.com/image/123.png`，如果成功访问即可证明上述加速域名配置正确，抓包如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-config-test4.png" height="490" width="800" />


----------


##缓存验证

该步骤主要测试加速域名在 CDN 加速平台上的缓存时间配置是否生效，具体验证可以参见如下方法：

1.如下图所以，后缀为 PNG 类型的图片，配置的缓存时间为 691200s。

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-auth.png" height="490" width="800" />

2.在步骤三的基础上，打开 Chrome 浏览器，将要访问的 URL 输入浏览器，点击回车，抓包如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-auth1.png" height="490" width="800" />

3.可以看到 `max-age=691200`，进而证明缓存配置策略已经生效。


----------


##CNAME 解析

如果步骤三、四测试都正常，则可进行加速域名 CNAME 的切换。

1.获取 CNAME 域名

打开域名配置，CNAME 地址如下图红框中所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cname-test1.png" height="490" width="800" />


2.修改 CNAME 记录

登入域名的 DNS 服务商网站，修改 CNAME 记录，具体配置方法可参见如下链接：

[DNSPod CNAME 接入 CDN](https://support.dnspod.cn/Kb/showarticle/tsid/32/?spm=5176.doc27112.2.16.GAMn1f)

[新网 CNAME 接入 CDN](http://www.xinnet.com/service/cjwt/domain/guanli/1164.html?spm=5176.doc27112.2.17.GAMn1f)

[万网 CNAME 接入 CDN](https://help.aliyun.com/document_detail/29725.html?spm=5176.doc27112.2.15.jhFGwZ)

3、验证 CNAME 配置是否生效

因 DNS 解析记录都有缓存时间，CNAME 的生效时间一般是 600s，可通过 ping 所配置的加速域名，检验 CNAME 配置是否生效，如果后缀显示为 aicdn.com，则证明 CNAME 配置已生效，即加速业务正式开始启用。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cname-test2.png" height="350" width="800" />

----------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)

