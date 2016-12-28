##产品介绍

HTTPS（全称：HyperText Transfer Protocol over Secure Socket Layer），其实 HTTPS 并不是一个新鲜协议，Google 很早就开始启用了，初衷是为了保证数据安全。 近两年，Google、Apple、Facebook 等这样的互联网巨头，不谋而合地开始大力推行 HTTPS， 国外的大型互联网公司很多也都已经启用了全站 HTTPS，这也是未来互联网发展的趋势。为鼓励全球网站的 HTTPS 实现，Google 甚至调整了搜索引擎算法，让采用 HTTPS 的网站在搜索中排名更靠前。想必在不久的将来，全网 HTTPS 势在必行。

但是，如果网站想使用 HTTPS 服务，则必须为域名从数字证书授权机构（CA，Certificate Authority）申请相关的 SSL 证书，并部署于源站服务器上才能有效。为此，又拍云提供了 SSL 证书的一站式服务，用户可直接在又拍云 [SSL 证书服务](https://console.upyun.com/toolbox/ssl/)平台申购各类 SSL 证书（包括免费申购 DV SSL 证书）;同时支持上传自有证书，全部自动化管理及部署，帮助客户以最低的成本，享受最优的服务，完成网站 HTTP 到 HTTPS 的转换，为用户提供优质的 HTTPS 加速解决方案。

##名词解释

**HTTPS**

HTTPS（全称：Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的 HTTP 通道，即 HTTP 的安全版。HTTPS 的安全基础是 SSL/TLS，它提供了身份验证与加密通讯的方法，现在被广泛用于万维网上安全敏感的通讯，比如交易、支付等。

**SSL（Secure Socket Layer，安全套接字层）**

SSL 由为 Netscape 公司所研发，用以保障在 Internet 上数据传输之安全，利用数据加密 (Encryption) 技术，可确保数据在网络上传输过程中不会被截取。SSL 协议位于 TCP/IP 协议与各种应用层协议之间，为数据通讯提供安全支持。

**TLS（Transport Layer Security，传输层安全）**

传输层加密协议，其前身是 SSL 协议， 1999 年经过 IETF（The Internet Engineering Task Force 国际互联网工程任务组） 讨论和规范后，改名为 TLS。发展至今已经有 TLS 1.0、TLS 1.1、TLS 1.2  三个版本。TLS 1.3 改动会比较大，还在草案阶段，目前使用最广泛的是 TLS 1.1、TLS 1.2。

**SSL 证书**

SSL 证书就是遵守 SSL 协议的服务器数字证书，通过验证域名、服务器身份后，由受信任的数字证书授权机构 CA 颁发，具有服务器身份验证和数据传输加密等功能。

**CA**

数字证书授权机构 （CA，Certificate Authority） 是负责发放和管理数字证书的权威机构，并作为电子商务交易中受信任的第三方，承担公钥体系中公钥的合法性检验的责任。

**域名型 SSL 证书（DV SSL）**

即证书颁布机构只对域名的所有者（一般是域名管理员邮箱，比如admin@hotmail.com）进行在线检查，通常是发送验证邮件给域名管理员或以该域名结尾的邮箱。

**企业型 SSL 证书（OV SSL）**

是要购买者提交组织机构资料和单位授权信等在官方注册的凭证，证书颁发机构在签发 SSL 证书前不仅仅要检验域名所有权，还必须对这些资料的真实合法性进行多方查验，只有通过验证的才能颁发 SSL 证书。

**增强型 SSL 证书（EV SSL）**

与其他 SSL 证书一样，都是基于 SSL/TLS 安全协议，但是验证流程更加具体详细，验证步骤更多，这样一来证书所绑定的网站就更加的可靠、可信。它跟普通 SSL 证书的区别也是明显的，安全浏览器的地址栏变绿，如果是不受信的 SSL 证书则拒绝显示，如果是钓鱼网站，地址栏则会变成红色，以警示用户。
    
##产品优势
**免费申请 SSL 证书**

又拍云提供了免费申请证书的方案，用户可通过 [SSL 证书服务](https://console.upyun.com/toolbox/ssl/)平台，免费申请 Let’s Encrypt 的 DV SSL 证书，实现了用户零成本全站部署 HTTPS。并且支持证书到期自动续签，用户无需担心证书过期时间，降低维护成本。

**国际领先 CA 机构合作（即将上线）**

又拍云联合亚洲诚信与国际顶级数字证书提供商 Symantec（赛门铁克）合作，即将推出免费域名型 SSL 证书（DV SSL）、付费企业型 SSL 证书（OV SSL）、付费增强型 SSL 证书（EV SSL）的申购；

Symantec是全球最大的信息安全服务提供商， 其颁发的证书能被所有浏览器支持与信任，可确保用户访问时浏览器给予正确的网页安全提示，确保了用户利益最大化，让用户真正感到安全可靠。

TrustAsia®（亚洲诚信）是亚数信息科技（上海）有限公司应用于信息安全领域的品牌，
专业为企业提供包含数字证书在内的所有网络安全服务，是Symantec™（赛门铁克）的白金合作伙伴。

**一键完成证书申请与部署**

用户只需提交域名，填写必要信息，点击申请即可完成证书的申购；并且用户无需手动操作，系统自动将证书部署于 CDN 平台，与又拍云 CDN 产品相融合，一键实现全站 HTTPS 加速服务。

**简单易用，管理方便**

无论是用户上传的自有证书，还是通过又拍云申购的各类证书，都可在 [SSL 证书服务](https://console.upyun.com/toolbox/ssl/)中统一管理，方便用户对证书进行各种操作，并且全程无需人工协助，公钥、私钥更加安全，做到让用户省心、放心，同时提升了用户的工作效率。

**提升网站搜索排名**

Google 在 2014 年发布的官方博客中指出，已经调整其搜索引擎算法，采用 HTTPS 加密的网站在搜索结果中的排名将会更高。其目标非常简单，就是要鼓励全球网站采用安全度更高的HTTPS以保证访客安全。因此网站采用 HTTPS 协议，可吸引更多的用户进行访问，提升网站的价值，增创营收。


##配置引导

又拍云与国际证书提供商 Let’s Encrypt 合作，为客户提供 DV SSL 证书的免费申请，操作简单方便，一键完成证书的申请与部署。如果域名当前已有证书，又拍云也提供了自有证书的上传方式，无需人工协助，安全便捷。

> *联合亚洲诚信与国际顶尖数字证书提供商 Symantec 合作，提供免费 DV SSL 证书，付费 OV SSL 、EV SSL 证书的申购。即将上线，敬请期待！*

详细操作步骤如下：

**一、DV SSL 证书免费申请**

第一步：首先需要在 CDN 平台[创建服务](http://docs.upyun.com/cdn/basic/#_1),并绑定需要申请证书的域名；

第二步：前往域名所在服务商处修改 [CNAME 记录](http://docs.upyun.com/cdn/guide/#cname)，如未修改 CNAME 记录，则会导致证书申请失败；

第三步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 证书申购

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl1.png" height="490" width="800" />

第四步：点击右上角的 `申购证书` ，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl2.png" height="490" width="800" />

第五步 填写域名

此处填写的域名，必须是已备案，并且已经完成了上述第一步、第二步中操作的域名；

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl3.jpeg" height="490" width="800" />

第六步，验证域名所有权

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl4.png" height="490" width="800" />

第七步，提交申请，等待系统审核，颁发证书

此时，申请的证书有三种状态：申购成功、申购中、申购失败，可通过点击 `查看`，浏览相关信息
如果证书申购失败，原因有多种，可根据提示重新进行操作，完成证书申购。如多次尝试都无法成功申购证书，也可通过提交工单，寻求帮助；

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl5.png" height="490" width="800" />
<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl6.png" height="490" width="800" />

第八步，证书申请成功后，系统会自动将证书部署于又拍云 CDN 平台，并与相应域名进行绑定，用户无需手动操作。同时可进入[证书管理](https://console.upyun.com/toolbox/ssl/)界面，对证书进行配置操作

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl7.png" height="490" width="800" />

**二、上传域名自有证书**

第一步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 证书管理 -> 添加自有证书 

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl8.png" height="490" width="800" />

第二步：粘贴证书内容:

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl9.png" height="490" width="800" />

点击 `保存` 之后，可查看 SSL 证书信息如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl10.png" height="490" width="800" />

**三、证书管理**

在证书管理列表中，可根据需求选择申购的证书或上传的自有证书，点击 `HTTPS 配置`进行操作 ，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl11.png" height="490" width="800" />

“HTTPS 访问”：开启该功能后，客户端使用 HTTPS 协议访问时才能成功，默认关闭。

“强制 HTTPS”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

##注意事项

1、申请 Let’s Encrypt DV SSL 证书的域名，必须是已备案，且已经在又拍云 CDN 平台[创建了服务](http://docs.upyun.com/cdn/guide/#_4)，并绑定了此域名。

2、申购证书之前，客户需要将域名所有区域全部[ CNAME ](http://docs.upyun.com/cdn/guide/#cname)到又拍云平台，否则有可能导致证书申请失败。

3、如果证书申购失败，除因 CNAME 原因导致的失败，其他失败情况下，可多次尝试重新提交申请，如多次都未申请成功，则可提交工单寻求帮助。

4、目前只支持单一域名的申请，不支持泛域名申请，不支持带下划线（_）的域名申请，申请数量不限。
