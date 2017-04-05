##产品介绍

HTTPS（全称：HyperText Transfer Protocol over Secure Socket Layer），其实 HTTPS 并不是一个新鲜协议，Google 很早就开始启用了，初衷是为了保护数据安全。 近两年，Google、Baidu、Facebook 等这样的互联网巨头，不谋而合地开始大力推行 HTTPS， 很多国内外的大型互联网公司也都已经启用了全站 HTTPS，这也是未来互联网发展的趋势。

为鼓励全球网站的 HTTPS 实现，Google 甚至调整了搜索引擎算法，让采用 HTTPS 的网站在搜索中排名更靠前。并且从 2017 年开始，Chrome 浏览器已把采用 HTTP 协议的网站标记为不安全网站，苹果 App Store 中的所有应用也都必须使用 HTTPS 加密连接；当前国内炒的很火热的微信小程序也要求必须使用 HTTPS 协议；新一代的 HTTP/2 协议的支持需以 HTTPS 为基础。因此想必在不久的将来，全网 HTTPS 势在必行。

但是，如果网站想使用 HTTPS 服务，则必须为域名从数字证书授权机构（CA，Certificate Authority）申请相关的 SSL 证书。为此，又拍云提供了 SSL 证书的一站式服务，用户可直接在又拍云 [SSL 证书申购](https://console.upyun.com/toolbox/createCertificate/)平台申购各类 SSL 证书（包括免费申购 DV SSL 证书）；同时，用户也可自愿选择将申购的证书一键部署于又拍云 CDN 平台，因平台全局自动化管理，采用了最优质的 HTTPS 加速解决方案，可帮助用户以最低的成本，享受最优的服务，完成网站 HTTP 到 HTTPS 的转换。

##名词解释

**HTTPS**

HTTPS（全称：HyperText Transfer Protocol over Secure Socket Layer），是以安全为目标的 HTTP 通道，即 HTTP 的安全版。HTTPS 的安全基础是 SSL/TLS，它提供了身份验证与加密通讯的方法，现在被广泛用于万维网上安全敏感的通讯，比如交易、支付等。

**SSL（Secure Socket Layer，安全套接字层）**

SSL 由 Netscape 公司所研发，用以保障在 Internet 上数据传输之安全，利用数据加密 (Encryption) 技术，可确保数据在网络上传输过程中不会被截取。SSL 协议位于 TCP/IP 协议与各种应用层协议之间，为数据通讯提供安全支持。

**TLS（Transport Layer Security，传输层安全）**

传输层加密协议，其前身是 SSL 协议， 1999 年经过 IETF（The Internet Engineering Task Force 国际互联网工程任务组） 讨论和规范后，改名为 TLS。发展至今已经有 TLS 1.0、TLS 1.1、TLS 1.2  三个版本。TLS 1.3 改动会比较大，还处于待发布阶段，目前使用最广泛的是 TLS 1.1、TLS 1.2。

**SSL 证书**

SSL 证书就是遵守 SSL 协议的服务器数字证书，通过验证域名、服务器身份后，由受信任的数字证书授权机构 CA 颁发，具有服务器身份验证和数据传输加密等功能。

**CA**

数字证书授权机构 （CA，Certificate Authority） 是负责发放和管理数字证书的权威机构，并作为电子商务交易中受信任的第三方，承担公钥体系中公钥的合法性检验的责任。

**CSR（Cerificate Signing Request，证书请求文件）**

CSR 是制作 SSL 证书的必要文件，通常由 CA 机构自动生成，用户也可自己创建，在生成 CSR 文件 的同时也会生成私钥（由用户自己保管），用户只需把 CSR 文件提交给 CA，CA 使用其根证书私钥对 CSR 文件签名即生成了用户的证书。 

**RSA**

RSA 公钥加密算法是 1977 年由 Ron Rivest、Adi Shamir、Leonard Adleman 一起提出的，它是第一个能同时用于加密和数字签名的算法，从提出到现今经历了各种攻击的考验，能够抵抗到目前为止已知的绝大多数密码攻击，已被 ISO 推荐为公钥数据加密标准

**ECC**

ECC（Elliptic Curves Cryptography，椭圆曲线加密算法）也是一种公钥加密算法，与主流的 RSA 算法相比，ECC 算法可以使用较短的密钥达到相同的安全程度，其安全性更高、处理速度更快。其数学基础是利用椭圆曲线上的有理点构成 Abel 加法群上椭圆离散对数的计算困难性。

**域名型 SSL 证书（DV SSL）**

即证书颁布机构只对域名的所有者进行在线检查，通常是验证域名下某个指定文件的内容，或者验证与域名相关的某条 TXT 记录；比如访问 [http|https]://www.domain.com/.../test.txt，文件内容： 2016082xxxxx39w7b20nelfa；或添加一条 TXT 记录：www.domain.com –> TXT –> 20170xxxxxqmkiby43hpvy8

**企业型 SSL 证书（OV SSL）**

是要购买者提交组织机构资料和单位授权信等在官方注册的凭证，证书颁发机构在签发 SSL 证书前不仅仅要检验域名所有权，还必须对这些资料的真实合法性进行多方查验，只有通过验证的才能颁发 SSL 证书。

**增强型 SSL 证书（EV SSL）**

与其他 SSL 证书一样，都是基于 SSL/TLS 安全协议，但是验证流程更加具体详细，验证步骤更多，这样一来证书所绑定的网站就更加的可靠、可信。它跟普通 SSL 证书的区别也是明显的，安全浏览器的地址栏变绿，如果是不受信的 SSL 证书则拒绝显示，如果是钓鱼网站，地址栏则会变成红色，以警示用户。
    
##产品功能

**申购 SSL 证书**

又拍云提供了 Symantec、GeoTrust、TrustAsia、Let’s Encrypt 的各类 DV/OV/EV SSL 证书的[申购服务](https://console.upyun.com/toolbox/createCertificate/)，其中 TrustAsia、Let’s Encrypt DV SSL 单域名证书可免费申请。 

**快速签发**

填写相关信息，一键申购，步骤简单方便，流程全自动化处理，无需人工协助，申购周期大大缩短，使证书得到快速签发。

**自助化管理**

又拍云平台即支持申购上述各类证书，同时也支持上传和管理任意机构签发的证书，所有 SSL 证书均自助化管理，集中监测，让用户使用更加放心、安心。

**一键部署**

提供一键部署 SSL 证书到又拍云 CDN 产品的功能，用户可自愿选择，帮助用户更快捷地保护数据安全，完成网站 HTTP 到 HTTPS 的转换。

##产品优势

**免费申请 SSL 证书**

又拍云提供了两款证书的免费申请方案，用户可通过 [SSL 证书申购](https://console.upyun.com/toolbox/createCertificate/)平台，免费申请 Symantec-TrustAsia、Let’s Encrypt 的 DV SSL 证书，帮助用户零成本实现全站部署 HTTPS。并且还实现了 Let’s Encrypt 证书到期自动续签，用户无需担心证书过期时间，降低维护成本。

**国际领先 CA 机构合作**

又拍云携手 TrustAsia 与国际顶级数字证书提供商 Symantec、GeoTrust 合作，推出了域名型 SSL 证书（DV SSL），企业型 SSL 证书（OV SSL）、增强型 SSL 证书（EV SSL）的申购；其中域名型 DV SSL 单域名证书当前免费申购。

Symantec 是全球最大的信息安全服务提供商， 其颁发的证书能被所有浏览器支持与信任，可确保用户访问时浏览器给予正确的网页安全提示，确保了用户利益最大化，让用户真正感到安全可靠。

GeoTrust 是全球第二大数字证书提供商，也是身份认证和信任认证领域的领导者， 该公司各种先进的技术使得任何大小的机构和公司都能安全、低成本地部署 SSL 数字证书和实现各种身份认证。 

TrustAsia®（亚洲诚信）是亚数信息科技（上海）有限公司应用于信息安全领域的品牌，
专业为企业提供包含数字证书在内的所有网络安全服务，是Symantec™（赛门铁克）的白金合作伙伴。

**一键完成证书申请**

用户只需提交域名，填写必要信息，点击申请即可完成证书的申购；对于免费证书，系统会自动将证书部署于 CDN 平台；对于付费证书，用户可按需一键将证书部署于 CDN 平台；两者都可以与又拍云 CDN 产品完美融合，实现全站 HTTPS 加速服务。

**简单易用，管理方便**

无论是用户上传的自有证书，还是通过又拍云申购的各类证书，都可在 [SSL 证书服务](https://console.upyun.com/toolbox/ssl/)中统一管理，方便用户对证书进行各种操作，并且全程无需人工协助，公钥、私钥更加安全，做到让用户省心、放心，同时提升了用户的工作效率。

**提升网站搜索排名**

Google 在 2014 年发布的官方博客中指出，已经调整其搜索引擎算法，采用 HTTPS 加密的网站在搜索结果中的排名将会更高。其目标非常简单，就是要鼓励全球网站采用安全度更高的 HTTPS 以保证访客安全。因此网站采用 HTTPS 协议，可吸引更多的用户进行访问，提升网站的价值，增创营收。

##使用场景

用户从又拍云申购 SSL 证书后，此 SSL 证书可使网站完成由 HTTP 到 HTTPS 的转换，由于 HTTPS 采用了 SSL/TLS 加密层，可保证网站数据的后续传输更加安全可靠，防止流量劫持，防冒充、钓鱼等。

如果用户同时购买了又拍云的 CDN 服务，也可以选择将 SSL 证书部署于 CDN 加速产品中，在实现网站 HTTPS 化的同时，也可享受又拍云优质的 HTTPS 加速解决方案。

##版本迭代

**V1.0**

2016 年 12 月 21 日
let’s encrypt DV SSL 单域名证书免费申请服务上线
在 CDN 全网、直播加速配置中增加了 HTTPS 配置项

**V1.1**

2017 年 2 月 7 日
TrustAsia DV SSL 单域名证书（由 Symantec 签发）免费申请服务上线

**V2.0**

2017 年 3 月 30 日
Symantec、GeoTrust、TrustAsia 的各类付费 DV SSL、OV SSL、EV SSL 证书申购服务上线

##购买指导

###购买流程

通过又拍云购买 SSL 证书，流程更加简洁方便：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/flow1.png" height="490" width="480" />

###价格总览

**价格列表：**

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/price1.png" height="830" width="750" />

**说明：**

一、单域名、泛域名证书，因只涉及到一个域名，所以其单价即上述表格中的价格；

二、对于增强型 EV SSL 证书，只有多域名证书，没有多域名泛域名证书；

三、多域名证书、多域名泛域名证书的价格计算如下：

> Symantec 品牌

计算公式（以一年有效期证书为例）：

    假设：
    
    1年标准域名价格 = A ； 标准域名个数 = B   
    
    1年泛域名价格 = C ；泛域名个数 = D
    
    1年标准域名专业版价格 = E       
    
    1年泛域名专业版价格 = F    
    
    则：
    
    多域名证书价格 = A * B
    
    多域名专业版证书价格 = E * B
    
    多域名泛域名证书价格 = A * B + C * D
    
    多域名泛域名专业版证书价格 = E * B + F * D

> Geotrust、TrustAsia 品牌

默认支持5个标准域名（默认域名中不包含泛域名），计算公式（以一年有效期证书为例）：

    假设：
    
    1年标准域名价格 = A ；标准域名个数 = B 
    
    1年泛域名价格 = C ；泛域名个数 = D
     
    1年标准域名额外价格 = E      
    
    则：
     
    多域名证书价格 = A + E * ( B - 5 )   如果 B - 5 < 0  则按 0 计算
      
    多域名泛域名证书价格 = A + E * ( B - 5 ) + C * D   如果 B - 5 < 0  则按 0 计算
    

###退款服务

**条件**

申购的 SSL 证书已由 CA 机构颁发成功的，则不能退款；
免费申请的域名型（DV SSL）证书不支持退款；
申购的 SSL 证书在信息提交审核前或申购失败后可免费退款；

**流程**

目前暂时仅支持通过提交工单的方式进行退款申请。

###证书吊销

如果用户发现源站服务器被攻破或其他原因导致 SSL 证书私钥泄露，则可以选择吊销此证书；证书吊销后，各浏览器、客户端等将不再信任已吊销的证书，会出现不安全提醒，举例参考[这里](https://revoked.badssl.com/)。

目前暂时仅支持通过提交工单的方式进行证书吊销申请。

##快速入门

又拍云携手 TrustAsia 与 Symantec、GeoTrust 达成战略合作，推出了各类域名型 DV SSL 、企业型 OV SSL 、增强型 EV SSL 证书的申购；其中有效期为一年的 TrustAsia 域名型 DV SSL 单域名证书当前免费申请，原价 1900 元/年。

又拍云还与国际证书提供商 Let’s Encrypt 合作，为用户免费提供了另一款 DV SSL 证书的申请，证书到期会自动续签，操作简单方便，一键完成证书的申请与部署，用户可根据自身情况，多重选择，满足不同场景的需求。

详细操作步骤如下：

###付费证书申购

第一步：[注册](http://docs.upyun.com/cdn/guide/#_2)又拍云账号，并完成[实名认证](http://docs.upyun.com/cdn/guide/#_3)；

第二步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 申购证书；

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl1.png" height="490" width="800" />

第三步：点击右上角的 `申购证书`，如下图所示，选择证书品牌，并选择需要申购的证书类型、有效期等；

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/buy1.png" height="490" width="800" />

第四步：核对申购信息；
   
<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/buy2.png" height="490" width="800" />

第五步：确认付款；

为防止恶意操作，又拍云提供了支付验证功能，付款时，将会发送验证码到[注册](http://docs.upyun.com/cdn/guide/#_2)又拍云账号时所使用的手机号上。

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/buy3.png" height="490" width="800" />

第六步：支付完成以后，订单进入`购买列表`，处于`待完成`状态，

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/buy4.png" height="490" width="800" />

第七步：点击`补全`按钮，填写域名、公司、联系人等信息，

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/write1.png" height="490" width="800" />

说明：申购多域名证书、多域名泛域名证书时，主域名只能填写标准域名。

第八步：信息填写完成，点击提交后，会出现如下两种情况

**（1）如果申购的是 OV 或 EV SSL 证书，则进行**

下载《信息确认函》

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/write2.png" height="490" width="800" />

上传《信息确认函》，上传文件不能大于2M

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/write3.png" height="490" width="800" />

《信息确认函》上传成功后，申购进入 CA 机构信息审核的状态，审核一般需要3～5个工作日

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/write4.png" height="490" width="800" />


**（2）如果申购的是 DV SSL 证书，则进行**

在`证书申购`界面，点击`申请结果`找到对应的申购记录，点击`查看`获取相关验证信息，验证操作可参考[这里](http://docs.upyun.com/cdn/ssl/#dv-ssl)

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/write5.png" height="490" width="800" />

添加好验证信息后，申购进入 CA 机构信息审核验证状态，一般需要 1 个工作日

第九步：查看申请结果，工具箱 -> SSL 证书服务 -> 申购证书；

点击`申请结果`查看申请状态，在证书未颁发成功之前，都可点击`取消`按钮，进行申购信息修改或停止申购。

如果状态显示为`已颁发`，则表明证书已申购成功，此时可进行`下载`、`部署到 CDN` 等操作。

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/write6.png" height="490" width="800" />

如果选择将证书`部署到 CDN`，则可进入[证书管理](https://console.upyun.com/toolbox/ssl/)界面，对证书进行[配置操作](http://docs.upyun.com/cdn/advanced/#ssl)。

###免费证书申购

前一、二、三、四、五、六步与付费证书申购步骤一致；

第七步：在 CDN 平台[创建服务](http://docs.upyun.com/cdn/basic/#_1),并绑定需要申请证书的域名；

第八步：前往域名所在服务商处修改 [CNAME 记录](http://docs.upyun.com/cdn/guide/#cname)，如未修改 CNAME 记录，则会导致证书申请失败；

第九步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 申购证书；

在`申购证书`界面，点击`购买列表`找到对应的申购记录，点击`补全`

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/free1.png" height="490" width="800" />

第十步：填写域名

此处填写的域名，必须是已备案，并且已经完成了上述第七步、第八步中操作的域名；

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/free2.png" height="490" width="800" />

第十一步：验证域名所有权  

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/free3.png" height="490" width="800" />

第十二步：提交申请，等待 CA 机构审核，颁发证书

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/free4.png" height="490" width="800" />

在`申请结果`中，如果申请状态显示为`已颁发`，则表明证书已申购成功

第十三步：证书申请成功后，系统会自动将证书部署于又拍云 CDN 平台，并与相应域名进行绑定，用户无需手动操作。同时可进入[证书管理](https://console.upyun.com/toolbox/ssl/)界面，对证书进行[配置操作](http://docs.upyun.com/cdn/advanced/#ssl)；

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl7.png" height="490" width="800" />

###DV SSL证书验证

当申购的证书为 TrustAsia DV SSL 多域名、泛域名证书时，需要通过以下方式进行域名所有权验证。

**1、DNS 验证**

修改域名的 DNS，添加 `TXT 记录 `，然后 CA 机构通过解析域名的 `TXT 记录`来验证域名所有权

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/dns1.png" height="490" width="800" />

以 DNSPOD 为例， `TXT 记录` 添加的方法如下：

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/dnspod1.png" height="256" width="800" />

**2、文件验证**

在源站服务器上，创建与域名相关的目录及文件，然后 CA 机构通过访问相关文件，并读取文件内容，验证域名所有权

<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/file1.png" height="490" width="800" />

举例：

如果域名是` www.upyun.com`，则 CA 机构进行验证访问的地址是： http|https://www.upyun.com/.well-known/pki-validation/fileauth.txt ；

如果域名是泛域名 `*.upyun.com`，则 CA 机构进行验证访问的地址是： http|https://upyun.com/.well-known/pki-validation/fileauth.txt 。

`fileauth.txt` 文件中的内容为：201703212055513616t9uk2pok3zmu2b7m3nc4c8mkudogfpvyj2w2gebhkypy58

注意：

CA 机构支持 `http` 或 `https` 访问验证链接，但是不支持任何形式的跳转，需要源站服务器直接响应 200 状态码和文件内容。

如果 Window 系统不支持创建 `/.well-known` 目录，则可以通过命令行来创建： `mkdir .well-known`

**3、审核验证**

无论是添加 `TXT 记录`，还是增加`验证文件`，两者操作完成以后，申购就进入了 CA 机构信息审核验证状态，一般需要 1 个工作日，证书颁发成功后，上述两种记录可删除。

##注意事项

1、申请免费的 TrustAsia、Let’s Encrypt DV SSL 证书的域名，必须是已备案，且已经在又拍云 CDN 平台[创建了服务](http://docs.upyun.com/cdn/guide/#_4)，并绑定了此域名。

2、申请 TrustAsia DV SSL 证书时，域名会经过 Symantec CA 机构安全审核，如果审核未通过，原因如下：

DV SSL 证书的申请，无需人工介入，由 CA 机构服务器自动认证快速签发，因此某些 CA 机构会根据其反钓鱼机制，采用较为严格的敏感词来加强审核标准，例如域名中包含 bank、trust 等，都会引起域名审核失败，敏感词的定义由 CA 机构自身规定。

这时请更换其他域名进行申请。

3、申购免费的 TrustAsia、Let’s Encrypt DV SSL 证书之前，客户需要将域名所有区域全部[ CNAME ](http://docs.upyun.com/cdn/guide/#cname)到又拍云平台，否则有可能导致证书申请失败。

4、如果免费 DV SSL 证书申购失败，除因 CNAME 原因导致的失败，其他失败情况下（安全审核失败除外），可多次尝试重新提交申请，如多次都未申请成功，则可提交工单寻求帮助。

5、目前不支持带下划线（_）的域名申请。

6、Symantec、GeoTrust、TrustAsia 的各类 SSL 证书兼容情况：支持市面上所有主流浏览器。

7、Let’s Encrypt DV SSL 证书兼容列表如下所示：

```
Mozilla Firefox >= v2.0
Google Chrome
Internet Explorer on Windows XP SP3 and higher
Microsoft Edge
Android OS >= v2.3.6
Safari >= v4.0 on macOS
Safari on iOS >= v3.1
Debian Linux >= v6
Ubuntu Linux >= v12.04
NSS Library >= v3.11.9
Amazon FireOS (Silk Browser)
Cyanogen > v10
Jolla Sailfish OS > v1.1.2.16
Kindle > v3.4.1
Java 7 >= 7u111
Java 8 >= 8u101

```

更多兼容性信息请参考[这里](https://letsencrypt.org/docs/certificate-compatibility/)

8.Let’s Encrypt DV SSL 证书，在保证域名 CNAME 一直解析到又拍 CDN 平台的前提下，证书到期后会自动续签，无需担心证书过期问题。

Symantec-TrustAsia DV SSL 证书（有效期一年），在[证书管理](https://console.upyun.com/toolbox/ssl/)中可以查看证书的到期时间，证书快要到期时，用户依然可以通过再次申请的方式免费延期。

9、对于 Symantec、GeoTrust、TrustAsia 的各类 SSL 证书，主域名例如 upyun.com 已经成功申请证书，则此证书中已默认添加了 www.upyun.com，即 www.upyun.com 可直接使用此证书，无需重复申请；同理使用 www.upyun.com 申请的证书，主域名 upyun.com 也可使用（上述只针对主域名生效，例如用域名 abc.upyun.com 申请的证书，则 www.abc.upyu.com 不可使用，需要重新申请）。
  
注意：另一款 Let’s Encrypt DV SSL 证书，主域名 upyun.com 和 www.upyun.com 无法共用一张证书，需分别申请。
