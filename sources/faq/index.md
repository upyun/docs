## 概念性 FAQ

**服务回源分几种类型，有哪些区别？**

服务回源总共分为两种：又拍云存储和自主源站。区别如下表所示：


| 功能  | 又拍云存储 | 自主源站 |
|---|---|---|
| CDN 加速  | 支持 | 支持 |
| 可上传文件 | 所有文件 | 不需要上传，又拍云直接从客户源站获取 |
| 单个文件上传限制 | 单个文件最大支持 5GB| - |
| 图片文件像素限制  | 无限制 | 无限制 |
| 防盗链功能 | 支持 | 支持 |
| 绑定独立域名 | 支持 | 支持 |
| 自定义版本 | 支持| 支持 |

**服务类型是否可以更改？**

服务类型一旦创建，将无法更改。

**一个帐号最多可以创建多少个服务？**

一个帐号最多可创建 200 个服务。

**一个服务最多可以绑定多少个域名？**

每个服务最多绑定 10 个域名，绑定的域名必须经过备案，且同一个域名只能绑在一个服务上。

**如何查看我服务里的文件？**

可以使用 FTP 查看，推荐使用客户端：[FileZilla](https://filezilla-project.org/)/[又拍云资源管理器](https://github.com/layerssss/manager-for-upyun)。

**一个帐号最多可以创建多少个操作员？**

一个帐号可创建 50 个操作员，以方便分配给不同部门或项目使用，并且可以灵活交叉授权。

**又拍云存储是否支持 `?v=xxx` 来刷新缓存？**

不支持，对于 `?v=xxx` 参数是忽略的。

**你们支持哪几种防盗链，有什么区别？**

又拍云支持 IP 禁用，域名防盗链，客户端白名单，Token 防盗链，地区访问限制并且可以自定义防盗链提示图。

- **IP 禁用**： 如果不希望某个 IP 或者某段 IP 访问空间，可以使用此功能，最多支持 50 个 IP 或 IP 段；
- **域名防盗链**： 通过设置域名白名单或黑名单，允许或禁止某个网站引用空间文件，最多各支持 50 个黑白名单；
- **客户端白名单**： 对于客户端或手机 APP 的客户，可以通过设置 User Agent 白名单来限制访问；
- **Token 防盗链**： 适合下载类网站或应用，可以设置签名和过期时间来控制空间文件的访问时限。
- **地区访问限制**: 适合针对地区做出访问限制

**如何刷新缓存？**

自主源站缓存刷新有两种：

1. API 接口，调用缓存刷新 API 接口，0~10 分钟更新到全国节点；
2. 在后台刷新，支持完整 URL 刷新、规则刷新、全网刷新。

**又拍云支持 HTTPS 访问吗？**

默认域名默认支持，绑定域名需要上传自己的 SSL 证书到又拍云。   

** 如何删除服务？**    

删除服务需要删除上传到服务的所有文件，然后在后台服务－高级功能－删除     

** 服务想暂时停用一段时间怎么办？ **    

可以先暂时关闭服务的外链禁止访问

## 技术性 FAQ

**为什么有些地区访问又拍云存储的资源无法打开？或者ping 得到的 IP 不是又拍云的？**

请检查本地 DNS 是否是公共的 DNS ，由于本地运营商分配的 DNS 存在数据库更新缓慢以及可能导致被恶意劫持等隐患，建议更换为公共的 DNS 地址，提升访问速度以及稳定性，常见的公共 DNS 地址：

>
> [国内推荐]  
>
> 114.114.114.114  
> 223.5.5.5       
> 223.6.6.6 
>
> [海外推荐] 
>
> 8.8.8.8   
>

 DNS 的更改可以参考：[各操作系统如何设置DNS](http://www.alidns.com/setup/#windows)

**我使用又拍云的默认域名访问资源，结果在腾讯 QQ 或 chrome 等浏览器访问提示资源存在安全隐患是什么原因？**

我们强烈建议客户使用已备案的自定义域名作为访问存储资源的域名，默认域名不建议作为长期的资源访问域名使用。 

**使用 API 上传，提示：400 data too long for ext-param？**

额外参数内容过长。

**使用 API 上传，提示：401 signature error？**

签名错误。

可能情况：

1. 签名格式错误；
2. 服务器时间错误，请检查服务器的时间是否与世界时间有较大偏差。
3. 递交到表单 API 接口的 policy 信息中缺少空间名；
4. 递交到表单 API 接口的 policy 信息中缺少 save-key 字段；
5. 递交到表单 API 接口的 policy 信息中缺少 expiration 字段；
6. 表单签名密匙错误（请到空间管理后台获取表单 API 签名密匙）。

**使用 API 上传，提示：401 need date header？**

发起的请求缺少 Date 头信息。

可能情况：

发起的接口请求 HEADER 中确少 Date 参数；

**注意：**部分 HTTP Client 开发包在发起 GET 请求时不自动加入 Date 参数。

**使用 API 上传，提示：401 date offset error？**

发起请求的服务器时间错误。

可能情况：

服务器时间错误，请检查服务器的时间是否与世界时间有较大偏差。

**使用 API 上传，提示：401 user blocked？**

操作员被禁用。

**使用 API 上传，提示：403 not an image？**

不是正确的图片内容。

可能情况：

上传非图片文件，但是使用了图片处理参数

** 使用 API 上传，提示 ：401  user need permission **    

操作员需要授权

可能情况：

操作员未授权相应操作的权限

** 上传文件的时候报 400 40011xxx invalid x-gmkerl-xxx **

无效参数

可能情况： 

参数使用错误

** 删除文件的时候 403 40300011 has no permission to delete **

没有权限删除

可能情况：  

操作员没有授权删除权限



**使用阿里云ECS作为源站，进行自主源站加速后响应头一直提示 `C／error 503 contention reset peer？`**

源站超时导致的。

可能情况：

阿里云云盾防护拦截了我们的回源 IP ，建议关闭云盾或者添加云盾白名单 `0.0.0.0 。

**使用 API 上传，提示：403 bucket blocked？**

空间被禁用或者没有进行实名认证。

**使用 API 上传，提示：403 authorization has expired？**

不接受请求，授权已过期。

可能情况：

1. 在 policy 信息中设置 expiration 授权过期时间过短；
2. 服务器时间与世界标准时间偏差过大。

**使用 API 上传，提示：403 form api disabled？**

不接受请求，空间的表单 API 上传功能未开启。

**使用 API 上传，提示：403 file too small？**

不接受请求，上传的文件容量过小。

可能情况：

在 policy 信息中设置的 content-length-range 参数是否过大。

**使用 API 上传，提示：403 file type error？**

不接受请求，上传的文件类型不被允许。

可能情况：

在 policy 信息中设置的 allow-file-type 不包含当前上传的文件类型。

**使用 API 上传，提示：403 content md5 not match？**

不接受请求，上传的文件 md5 校验错误。

可能情况：

在 policy 信息中设置的 content-md5 校验值与表单接收到的文件 md5 不一致。

**使用 API 上传，提示：403 image width too small or too big？**

不接受请求，上传的图片文件宽度过小或过大。

可能情况：

在 policy 信息中设置的 image-width-range 参数是否过大或过小。

**使用 API 上传，提示：403 image height too small or too big？**

不接受请求，上传的图片文件高度过小或过大。

可能情况：

在 policy 信息中设置的 image-height-range 参数是否过大或过小。

**使用 API，提示：404 Not Found？**

请求的文件不存在。

可能情况：

1. 在上传文件过程中遇到 404 错误，请检查上传文件路径的目录路径是否存在；
2. 在下载文件过程中遇到 404 错误，请检查文件名和路径是否存在。

**使用 API，提示：406 dir not acceptable？**

不接受请求。

可能情况：

1. 在上传文件过程中遇到 406 错误，请检查上传文件名是否已经被用于存在的目录；
2. 在上传文件过程中遇到 406 错误，如有设置 Content-MD5 校验，可能是又拍云服务器端收到的文件内容 MD5 不一致。请检查 API 接口请求时设置的 Content-MD5 值；
3. 在创建目录过程中遇到 406 错误，请检查目录名是否已经被用于存在的文件。

**使用 API 上传，提示：503 System Error？**

系统错误。

可能情况：

1. 又拍云系统正在维护中；
2. 又拍云系统出现故障，请与客服联系。

## FTP

**使用 FTP 登录提示：无法解析 v0.ftp.upyun.com 的地址？**

可能情况：

本地 DNS 有问题，可尝试更换公共 DNS。

**使用 FTP 登录提示：530 Authentication failed. Disconnecting. 无法连接到服务器？**

可能情况：

1. 用户名（操作员/空间名，斜杠为必须）错误；
2. 密码（操作员密码）错误；
3. 操作员没有授权给空间。

**使用 FTP 上传较大文件：进度是 100% 确没有显示上传完毕？**

请耐心等待 15 秒左右，此时系统正在把接收到的文件写入到相应的存储服务中，文件写入完毕后，才会显示上传完成。

**FTP 看到的时间与当前时间（北京时间）不一致？**

又拍云服务器采用 GMT 时间，为 UTC+0 区，和北京时间相差 8 个小时。

**FTP 看到上传的中文文件名为乱码？**

又拍云的 FTP 编码为 UTF-8，请更改 FTP 编码设置为 UTF-8。

**FTP 中可以看到图片，但外链访问 404？**

可能情况有：

1. 使用了原图保护功能，需要带密钥访问；
2. 上传完成前有过访问，会有 5 分钟 404 缓存，需要刷新缓存或等待缓存过期后更新；
3. URL 中含有和后台间隔符相同的字符。

**为什么 FTP 不能给目录重命名？**

支持空目录及文件重命名，暂不支持非空目录重命名。

**FTP 如何移动及复制文件及目录？**

支持空目录及文件移动，暂不支持非目录移动，暂不支持目录、文件复制。

**为什么 FTP 上传会少几个字节不完整？**

由于 FTP 的特性，不检查数据完整性，如网络波动导致一部分字节丢失也是可能的，可重传或采用 API 方式上传。

**FTP 提示：425 Rejected Data Connection From Foreign Address 并读取目录失败？**

可能情况：

运营商提供的 IP 发生了变化，FTP 探测到前后不一致，出于安全考虑，断掉了连接，这个是 FTP 协议决定的。如果无法改变这个 IP 地址变动的情况, 可以尝试使用使用代理来避免这种情况发生。

### 插件

**织梦 CMS 是否可以使用又拍云，有没有插件？**

暂无织梦 CMS 的插件；理论上支持 FTP 协议的 CMS 都是可以使用又拍云存储，但织梦 CMS 本身将远程附件当做备份处理，静态文件的引用是不引用远程附件的；所以织梦 CMS 可以使用又拍云存储，但只能是把又拍云存储当备份。

**Discuz 是否可以本地保留附件？**

如果你使用插件，插件最后是有选项设置是否开启本地备份儿功能的；如果你使用远程附件功能，本地是不保存的。

**为什么 Discuz 远程附件的测试 FTP 按钮提示删除失败，没有权限的提示？**

该功能与又拍云不兼容，确认填写配置信息无误后，可以直接保存设置，发帖测试。

**为什么用 FTP 上传的文件，WordPress 的媒体库看不到？**

因为媒体库只控制由自身上传的文件，并没有获取远程目录的功能，所以 WordPress 的媒体库看不到 FTP 上传的文件

**怎么将 Wordpress 的数据迁移到又拍云？**

将网站根目录下 /wp-content/uploads 下的数据上传到又拍云，安装又拍云 Wordpress 的插件（安装请参考安装手册），正确填写设置，保存配置，更新数据库。


**为什么安装 Wordpress 后，上传一个文件，又拍云存储空间会多几个？**

这是由于 Wordpress 的存储方式是一张原图直接分割成多张缩略存储，上传附件时是将原图以及缩略图一并上传的。

**PHPWind 安装又拍云插件，头像还是保存本地？**

PHPWind 头像存储还需要另外安装插件设置；具体参考安装手册第四项，头像存储方式设置。

**为什么 PHPWind 安装插件后，上传文件后，文件外链还是本地地址？**

检查配置信息是否正确，清理 PHPWind 缓存。

**怎么通过日志看动态请求还是静态请求**

后台下载日志后，打开日志查看 Static 字段就是静态，反之 Dynamic 就是动态的。

**为什么下载又拍云官方的 UEditor 编辑器，配置以后，上传不了？**

检查 UEditor 的权限问题，UEditor 下附件目录 uploads 至少要有写的权限。

### 其它

**图片访问显示：403 Forbidden？**

开启了防盗链功能。

可能情况：

1. 使用了 Token 防盗链，访问时没有加参数：_upt；
2. 使用了 IP 禁用功能，IP 被禁了；
3. 使用了域名防盗链功能，不在允许的域名范围内；
4. 使用了客户端白名单功能，不在白名单范围内。

**图片在本地看着是正的，传到了又拍云，某些浏览器访问是倒的？**

图片本身并不是正的，部分软件或浏览器具有自动旋转识别功能，可通过查看图片 EXIF 信息，确定图片的具体旋转情况。

**说明：**参数 Orientation，（1：0 度 6：顺时针 90 度 8：逆时针 90 度 3：180 度）。

**原图加密功能不生效？**

可能情况：

原图保护密钥包含特殊字符：新版本规定只能使用“a-zA-Z0-9”的字符组合。

**添加水印不成功？**

检查水印图片尺寸任意边是否超过了生成图片尺寸的 50%，超过不能添加水印。

**文件更新了，为什么访问还是老图？**

文件更新后，又拍云存储需要更新全国节点的缓存，这个时间需要 0~10 分钟。一般情况下，能够做到实时更新。10 分钟后还是无法获取新图，请检查以下两种情况：

1. 浏览器可能存在缓存，请清除浏览器缓存后，再强制刷新;
2. 若是自主源站的情况，源数据更新后是否有调用过 API 接口，通知又拍云存储进行缓存刷新。

**自主源站服务，访问文件一直没刷新成功，响应头中显示 `x-source:U/200` 请问如何解决？ **

 可能原因:

 这种情况一般是开启了镜像功能导致源站文件被永久保存在了 CDN 中，此时的自助源站已经是一个存储服务，如果误操作，先 FTP 登录到这个服务中删除文件，然后关闭镜像功能后全网刷新。

** 文件上传成功后，为什么访问返回 400 状态码，提示网站未备案？ **

请确认域名是否解析到我们提供的解析地址

** 如何通过 curl 上传文件？**

curl -T /local_img.jpg http://v0.api.upyun.com/bucket/img.jpg -u operator:operator_pwd -v

>/local_img.jpg 本地上传文件的路径和文件名
>/bucket 服务名
>operator:operator_pwd 操作员：操作员密码

** 文件上传成功后，如何通过浏览器去查看文件？ **

可以直接通过外链去访问下载 http://<服务名>.b0.upaiyun.com/<文件路径>/<文件名>

** 如何去直接下载一个在页面显示的文件 **

在 URL 后面添加 ?_upd=true 参数实现

** 文件上传成功后，如何确认文件是否更新？**

确认文件响应头中的 Last-Modified 的时间是否是您更新文件的时间。

** 访问 405 Not Allowed **

外链没有开启

** 操作员都有哪些权限？是否可以登陆账号？**

操作员是没有办法登陆账号的，操作员只是对文件进行操作，上传、删除等

** 想全网接入，如何在接入的时候减少服务器的压力？ **

可以在接入之前，将一些静态文件进行预热，可以减少文件的回源压力，提高缓存命中率。

## 帐号 FAQ

** 帐号注册时提交申请之后跳出手机或邮箱已存在？**    

表示您之前已经用该手机或邮箱注册登记过帐号，若您不记得帐号，可以联系售后 `QQ200576786` 帮您查看。

** 我忘记密码了怎么办？**   

手机或邮箱已经验证过的，直接在登录页面点击忘记密码，即可通过手机短信，语音电话或者邮箱重置一个新的密码，然后登录即可；若邮箱未验证，可以用注册邮箱发送用户名和验证邮件到 `support@upyun.com` 人工验证邮箱，若手机号未验证，可以用注册手机拨打 `0571-81020204` 人工验证手机号，验证之后可通过邮箱或手机重置密码。

** 帐号登录进去提示帐号被冻结或者禁用了怎么办？**  

请联系客服 `QQ200576786` 帮您处理。

** 企业帐号和个人帐号有何区别？**   

不论您选择企业还是个人，功能及产品的价格都是没有区别的。帐号类型一旦选定便不能修改，企业帐号的认证需提交营业执照照片或扫描件认证，个人帐号的认证是提交本人手持身份证正反面照片，选择自己对应申请的帐号类型填写正确信息提交申请即可。

** 实名认证为什么没有通过？ **  

可以在您帐号管理的实名认证页面或者后台的站内信查看具体原因。

** 我刚给帐号充值了,怎么开发票呢？**  

联系您的销售经理帮您开发票，若您不知道销售经理的联系方式，可以联系客服 `QQ200576786`，提供您的帐号和联系方式，客服会联系您的销售经理给您开发票的。   

** 注册帐号时，手机收不到验证码。**

您先查看下手机是否出于正常服务状态，没有欠费，且通信正常。如果手机是处于正常服务中，且收发其他短信都正常，请联系客服 `QQ200576786` 查看具体原因。   


** 我提交了实名认证怎么还没审核？ ** 

您好，工作人员会在一个工作日内审核完成，若超过一个工作日，请联系客服 `QQ200576786` 帮您处理。  


## 视频教程

### CDN 服务

| 视频类别 | 视频名称                                     | 时长     |
| ---- | ---------------------------------------- | ------ |
| CDN  | [创建和使用CDN存储服务](https://techs.b0.upaiyun.com/videos/cdnpage/create_cdn_and_use_storage_services.html) | 9分24秒  |
| CDN  | [创建动态CDN和设置缓存](https://techs.b0.upaiyun.com/videos/cdnpage/create_dynamic_cdn_and_cache_setting.html) | 15分04秒 |
| CDN  | [创建和使用缩略图](https://techs.b0.upaiyun.com/videos/cdnpage/Picture_processing.html) | 8分03秒  |
| CDN  | [设置SSL证书](https://techs.b0.upaiyun.com/videos/cdnpage/ssl_setting.html) | 5分54秒  |
| CDN  | [缓存刷新功能](https://techs.b0.upaiyun.com/videos/cdnpage/chache_Refresh.html) | 3分42秒  |
| 插件   | [设置 Discuz 插件](https://techs.b0.upaiyun.com/videos/cdnpage/Discuz.html) | 8分29秒  |
| CDN  | [设置防盗链](https://techs.b0.upaiyun.com/videos/cdnpage/Denyurl.html) | 12分43秒 |
| 插件   | [设置 WordPress 插件](https://techs.b0.upaiyun.com/videos/cdnpage/wordpress.html) | 4分43秒  |
| CDN  | [融合云配置](https://techs.b0.upaiyun.com/videos/cdnpage/upyun_oss.html) | 5分     |
| CDN  | [URL动态作图](https://techs.b0.upaiyun.com/videos/cdnpage/Dynamic_mapping.html) | 2分41秒  |
| SDK  | [HTTP REST API的介绍和JAVA SDK的使用](https://techs.b0.upaiyun.com/videos/cdnpage/http_rest_api_and_javasdk.html) | 10分23秒 |
| SDK  | [CURL 操作 HTTP](https://techs.b0.upaiyun.com/videos/cdnpage/curl_http_rest_api.html) | 7分15秒  |
| SDK  | [音视频处理SDK](https://techs.b0.upaiyun.com/videos/cdnpage/av-pretreatment-php-sdk.html) | 6分09秒  |
| CDN  | [七牛镜像回源功能使用](https://techs.b0.upaiyun.com/videos/cdnpage/qiuniuoraliossmirror.html) | 6分19秒  |
| CDN  | [CORS 跨域功能使用](https://techs.b0.upaiyun.com/videos/cdnpage/CORS.html) | 3分01秒  |
| SDK  | [Python SDK的使用 ](https://techs.b0.upaiyun.com/videos/cdnpage/Python_Sdk.html) | 17分25秒 |
| FTP  | [又拍云资源管理器的使用](https://techs.b0.upaiyun.com/videos/cdnpage/upyun_manager.html) | 2分38秒  |
| CDN  | [阿里云云盾设置](https://techs.b0.upaiyun.com/videos/cdnpage/yundun.html) | 1分04秒  |
| 日志   | [日志分析和日志下载](https://techs.b0.upaiyun.com/videos/cdnpage/upyunlog.html)                    | 7分2    |
| CDN  | [又拍云的统计功能](https://techs.b0.upaiyun.com/videos/cdnpage/upyuncount.html) | 3分35秒  |
| CDN  | [又拍云监控系统简介](https://techs.b0.upaiyun.com/videos/cdnpage/jiankong.html) | 3分05秒  |
| CDN  | [Rewrite规则的使用](https://techs.b0.upaiyun.com/videos/cdnpage/rewrite.html) | 4分03秒  |
| 账号   | [实名认证](https://techs.b0.upaiyun.com/videos/cdnpage/gerenrenzheng.html) | 2分     |
| CDN |[HTTP2简介](https://techs.b0.upaiyun.com/videos/cdnpage/HTTP2.html)|2分49秒|
|工具|[wireshark的使用](https://techs.b0.upaiyun.com/videos/cdnpage/wireshark.html)|5分58秒|
|工具|[tcpudmp的使用](https://techs.b0.upaiyun.com/videos/cdnpage/tcpdump.html)|8分56秒|
|工具|[upx的使用](https://techs.b0.upaiyun.com/videos/cdnpage/upx.html)|6分56秒|
|插件|[ueditor的使用](https://techs.b0.upaiyun.com/videos/cdnpage/ueditor.html)|7分55秒|

### 直播服务

| 视频类别 | 视频名称                                     | 时长     |
| ---- | ---------------------------------------- | ------ |
| 直播   | [创建和使用又拍云自主源站的直播服务](https://techs.b0.upaiyun.com/videos/cdnpage/upyunzb1.html) | 10分    |
| 直播   | [创建和使用自主源直播服务](https://techs.b0.upaiyun.com/videos/cdnpage/upyunzzzb.html) | 4分51秒  |
| 直播   | [直播转码和录制](https://techs.b0.upaiyun.com/videos/cdnpage/zb-format.html) | 10分23秒 |
| 直播   | [安卓拉流播放器使用指南](https://techs.b0.upaiyun.com/videos/cdnpage/andriod-player-sdk.html) | 3分32秒  |
| 直播   | [直播防盗链的使用](https://techs.b0.upaiyun.com/videos/cdnpage/live_push_token.html) | 6分54秒  |









