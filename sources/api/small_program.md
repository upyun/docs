## 1.前言

微信小程序是一种全新的连接用户与服务的方式，它可以在微信内被便捷地获取和传播，拥有类似 App 的使用体验。微信小程序的出现，开启了新的跨平台移动应用方式，又拍云推出了[微信小程序 SDK](https://github.com/upyun/wechat-sdk)，帮助开发者减轻后端部署、运维、证书配置等繁琐工作，提升小程序性能的同时，减少开发成本。

为了方便微信小程序开发者便捷使用又拍云的云服务，本文案将会结合又拍云对象存储、SSL 证书、CDN 加速、图片处理、音视频处理等服务，快速构建具备云端能力的专属小程序。


## 2.准备工作

**2.1 注册微信小程序**

开发小程序的第一步，您需要注册一个微信小程序的账号，通过此账号您可以管理您的小程序，微信小程序账号注册地址请移步[微信公众平台](https://mp.weixin.qq.com/)，详细使用指南请参见微信小程序[简易教程](https://mp.weixin.qq.com/debug/wxadoc/dev/index.html)文档。


**2.2 又拍云账户实名认证**

为了顺利使用又拍云的云服务，您需要在又拍云注册一个账号，并进行账户的实名认证。打开[注册界面](https://console.upyun.com/register/)或者在又拍云官网页面右上角点击「注册」按钮。更多了解，请参考云服务[快速入门](http://docs.upyun.com/cdn/guide/)指南。

**2.3 购买云服务器和云数据库**

为了更好的运行服务端代码，您还需要购买业务服务器和云数据库，具体配置根据您的业务需求而定。又拍云暂未提供该服务，这里推荐又拍云战略合作伙伴的云端网络的[云主机服务](https://www.verycloud.cn/cloud/hostinfo)，或者自行采购并进行托管。


## 3.域名及证书申请

**3.1 域名申请及备案**

搭建企业级的微信小程序，需要注册自有域名并进行备案。如果您已经注册企业域名并且已备案，请忽略本步骤。


**3.2 SSL 证书申请**

又拍云提供了 Symantec、GeoTrust、TrustAsia、Let’s Encrypt 的各类 DV/OV/EV SSL 证书的申购服务，其中 TrustAsia、Let’s Encrypt DV SSL 单域名证书可免费申请。立即购买请点击[ SSL 证书购买](https://console.upyun.com/toolbox/createCertificate/)，详细操作步骤可参见[快速入门](https://docs.upyun.com/cdn/ssl/#_18)。如下图为 SSL 证书申购界面：


<img src="http://upyun-assets.b0.upaiyun.com/docs/ssl/certificate/buy1.png" height="400" width="750" />


**3.3 SSL 证书部署**

SSL 证书部署分为两个部分，您需要在服务端和 CDN 平台同时进行 SSL 证书的部署。

**1）在服务端进行 SSL 证书的部署**

微信小程序必须使用 HTTPS 进行加密通信，所以您需要在源站进行 SSL 证书的部署和配置。

这里以 Nginx 为例，打开 Nginx 根目录，输入命令：

    vim nginx.conf
    
    
具体配置信息参见如下样例：

    server {
    
    listen 443;  #SSL 访问端口号为 443
    server_name  www.example.com  #您的域名
    ...
    ssl on;  #开启 SSL 功能 
    ssl_certificate     /xxx/xxx/www.example.com.pem;  #证书文件 
    ssl_certificate_key /xxx/xxx/www.example.com.key;
    #私钥文件
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #支持的 TLS 协议 
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;  #配置加密套件,推荐配置。

    location / {
            root   html;  #站点目录
            index  index.html index.htm;
        }
    ...
    }

配置完成后，然后通过如下命令来判断配置文件语法是否正确

    sudo nginx -t -c /etc/nginx/nginx.conf

确保配置无误的话，建议重新启动 Nginx。

**2）在又拍云 CDN 平台进行 SSL 证书的部署**

在 CDN 平台进行 SSL 证书的部署请进入 [SSL 证书管理](https://console.upyun.com/toolbox/ssl/)，可一键开启 HTTPS 加速。更多了解请参见[ HTTPS 配置](http://docs.upyun.com/cdn/config/#51-https)文档。


**3.4 通信域名配置**

前往[微信公众平台](https://mp.weixin.qq.com)，依次进入：【设置】-》【开发设置】-》【服务器域名】-》【修改】，使用二级域名完成通信域名设置。配置截图如下图：


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_domain.png" height="400" width="750" />



* request 合法域名：请求的合法域名

* socket 合法域名：socket 通讯的合法域名

* uploadFile 合法域名：资源上传的合法域名

* downloadFile 合法域名：资源下载的合法域名

请根据需求合理配置。关于 `uploadFile` 合法域名配置请参见 `5.4` 基础配置。



## 4.搭建开发环境

**4.1 下载小程序开发者工具**

下载并安装最新版本的[小程序开发者工具](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/download.html)，使用小程序绑定的微信号扫码登录开发者工具。


**4.2 简单操作**


* 微信扫二维码登陆


![MacDown Screenshot](http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_login.png)


* 创建项目

![MacDown Screenshot](http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_create.png)

选择**【小程序项目】**即可添加项目。

详细操作步骤请移步小程序开发者工具[操作指南](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/devtools.html)。

## 5.又拍云小程序上传方案

微信小程序要求安装包大小不超过 2MB，而图片、音频、短视频等多媒体文件往往占据了 App 大部分空间，可以把这些文件上传至云端存储，程序包进行多媒体文件读取，让小程序轻松 “跳脱” 2MB 的容量限制。又拍云存储拥有上传加速，下载加速，不限量存储等功能，满足您文件的无限存储和快速读取。

另外，通过又拍云[微信小程序 SDK](https://github.com/upyun/wechat-sdk)，可轻松将文件上传到又拍云[对象存储服务](https://www.upyun.com/products/file-storage)。

**5.1 下载微信小程序 SDK**

点击[又拍云微信小程序 SDK](https://github.com/upyun/wechat-sdk)进行下载。


**5.2 准备工作**

1) 创建云存储服务，详细操作参见[域名接入(又拍存储源)](http://docs.upyun.com/cdn/service/#_6)，如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun_storage_basic_info.png" height="400" width="750" />


从截图可以得知，服务名称为（也即 bucket）`video-upyun-com`。

2) 为存储服务 `video-upyun-com` 授权一个操作员，详细操作参加文档[操作员使用](http://docs.upyun.com/api/quick_start/#_4)，配置参加截图所示：



<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_opertator.png" height="400" width="750" />


从截图可以看出，操作员名（也即 Operator ）为 `wxj2017`，与此同时请记住操作员密码。


**5.3 上传和部署代码**

该步骤，首先需要下载小程序 [SDK+Demo](https://github.com/upyun/wechat-sdk/tree/master/demo)，获取小程序 Demo 及源码。接下来的操作步骤为：

1）打开第四步安装的微信开发者工具，点击【小程序项目】模块，进入如下界面：


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_create2.png" height="350" width="600" />


2) 点击上图左下角的【添加】按钮，进入如下界面，输入小程序 AppID，项目目录选择上一步下载下来的代码目录：

![MacDown Screenshot](http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_create3.png)

注意：AppID 可以从微信公众号里面获取，如截图所示：


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_appid.png" height="350" width="700" />



3）点击【确定】创建小程序项目，进入微信开发者工具界面：


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_tools.png" height="350" width="700" />



**5.4 基础配置**


1）字段替换

您需要进入 `/pages/index/index.js` 文件，将相关字段进行修改和替换，参见如下截图：


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_upload2.png" height="350" width="700" />


```

const upyun = new Upyun({
  bucket: 'sdkimg',
  operator: 'tester',
  getSignatureUrl: 'http://localhost:8080'
})
```

需要替换的字段为：

* bucket:服务名称
* operator：操作员
* getSignatureUrl：计算 signature 的服务器接口地址

```

upyun.upload({
          localPath: imageSrc,
          remotePath: '/wxapp/demo',
          success: function (res) {
            console.log('uploadImage success, res is:', res)

            wx.showToast({
              title: '上传成功',
              icon: 'success',
              duration: 1000
            })

            self.setData({
              imageSrc
            })
          },

```

需要替换的字段为：

* remotePath：在又拍云存储的文件路径。


2）上传域名配置

登录微信小程序账户并配置 `uploadFile` 合法域名为 `https://v0.api.upyun.com`，配置如截图所示：


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_uploadFile.png" height="350" width="700" />



**5.5 认证鉴权**

云存储服务的认证鉴权详细参见文档[认证鉴权](http://docs.upyun.com/api/authorization/#http-body)。关于认证鉴权部分，可通过如下三个部分来分开介绍：

**1）微信小程序客户端上传 `data` 参数**

可以直接参考微信小程序 `/utils/upyun-wxapp-sdk.js` 中的代码。下面为部分代码示例：

```

function Upyun (options) {
  this.bucket = options.bucket
  this.operator = options.operator
  this.getSignatureUrl = options.getSignatureUrl
}

Upyun.prototype.upload = function (options) {
  var self = this
  if (!options.remotePath) {
    options.remotePath = options.localPath.split('//')[1]
  }
  var date = (new Date()).toGMTString()
  var opts = {
    'save-key': options.remotePath,
    bucket: self.bucket,
    expiration: Math.round(new Date().getTime() / 1000) + 3600,
    date: date
  }
  var policy = Base64.encode(JSON.stringify(opts))
  var data = [ 'POST', '/' + self.bucket, date, policy ].join('&')
  self.getSignature(data, function (err, signature) {
    if (err) {
      options.fail && options.fail(err)
      options.complete && options.complete(err)
      return
    }
    wx.uploadFile({
      url: `https://v0.api.upyun.com/${self.bucket}`,
      filePath: options.localPath,
      name: 'file',
      formData: {
        authorization: `UPYUN ${self.operator}:${signature}`,
        policy: policy
      },
      success: options.success,
      fail: options.fail,
      complete: options.complete
    })
  })
}

```
从代码中可以得知，需要获取的字段包括：

* bucket：服务名称
* save-key：文件保存路径，也即 `remotePath`
* expiration：请求的过期时间，UNIX UTC 时间戳，单位秒
* date：请求日期时间，GMT 格式字符串
* Method：这里为 `POST`
* URI：请求路径，本例为 `/`

最终客户端生成 `data` 参数传递给服务端签名接口（ 也即 `getSignatureUrl` 地址 ），签名服务器生成 `signature` 并返回给客户端。


**2）签名服务端生成 `signature`**

为了考虑安全性，操作员密码不宜直接暴露，`signature` 直接由签名服务端生成，微信小程序需要携带 `data` 参数，具体参见上个步骤的代码。

接口地址字段为 `getSignatureUrl`，在 `5.4` 基础配置中已经有介绍，该接口接受 `GET` 请求，并接受一个参数 `data`，计算并返回带有 `signature` 字段的响应。

`signature` 的计算公式是：


```

Base64 (HMAC-SHA1 (<md5_password>, <data>)

```

其中，md5_password 是操作员密码的 `MD5` 值，操作员密码请妥善保管，不宜暴露。


**3）客户端向又拍云 API 服务端进行认证鉴权**

根据上面两个步骤，最终生成签名信息，详见代码：

```
wx.uploadFile({
      url: `https://v0.api.upyun.com/${self.bucket}`,
      filePath: options.localPath,
      name: 'file',
      formData: {
        authorization: `UPYUN ${self.operator}:${signature}`,
        policy: policy
      },
      success: options.success,
      fail: options.fail,
      complete: options.complete
    })
    
```


**5.6 上传代码**

以上操作步骤进行完之后，您可以上传微信小程序代码至微信小程序服务端。进入微信开发者工具，点击【上传】按钮，填写版本号及项目备注。


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_upload3.png" height="350" width="700" />


进入微信公众平台，依次进入：【开发管理】-》【开发版本】-》选择体验版本。


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_upload5.png" height="350" width="700" />


设置页面路径及参数，具体配置为：pages/index/index，提交之后，进入如下页面。


<img src="http://upyun-assets.b0.upaiyun.com/docs/solution/weixin_upload6.png" height="350" width="700" />

扫描二维码，可以进入微信小程序体验版。


## 6.开通 CDN 服务

为了提升微信小程序的访问体验，访问域名可以进行 CDN 加速。可以在 5.2 中创建的存储服务下直接绑定加速域名。

**6.1 绑定加速域名**

结合前面步骤申请的访问域名以及微信公众平台设置的通信域名，您可以将访问域名在又拍云 CDN 平台进行配置。进入第五步已开通的服务，直接进入功能配置模块进行加速域名的绑定，详情参见[加速域名](http://docs.upyun.com/cdn/config/#12)。


**6.2 一键开启 HTTPS 加速**

进入【功能配置】模块，找到 HTTPS 配置，可以一键开启 HTTPS 加速。更多操作步骤参见[HTTPS 配置](http://docs.upyun.com/cdn/config/#51-https)。


**6.3 将域名切换到 CDN**

具体操作请参见 [CNAME 配置](http://10.0.6.71:8001/cdn/guide/#cname)。


## 7.其他服务


**7.1 图片处理**

结合图片处理的使用方式来合理使用，请参见如下链接。

* [URL 作图](http://docs.upyun.com/cloud/image/#url)
* [缩略图版本](http://docs.upyun.com/cloud/image/#_4)
* [上传预处理（同步）](http://docs.upyun.com/cloud/image/#_5)
* [上传预处理（异步）](http://docs.upyun.com/cloud/image/#_6)


**7.2 音视频处理**

音视频处理的场景包括异步音视频处理和同步音视频处理，详细参见文档：

* [异步音视频处理](http://docs.upyun.com/cloud/av/)
* [同步视频处理](http://docs.upyun.com/cloud/sync_video/)
* [同步音频处理](http://docs.upyun.com/cloud/sync_audio/)

