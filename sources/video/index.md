## 概述

又拍云短视频（UPYUN Short Video）集短视频采集、上传、存储、分发、播放于一体，借助短视频 SDK、上传加速、不限量存储、稳定快速的 CDN 及播放器 SDK，为用户提供专业可靠的短视频解决方案。

![短视频](https://upyun-assets.b0.upaiyun.com/docs/video/short_video.png)


## 解决方案

<a name="short_video_sdk"></a>
### 短视频 SDK

用于短视频的录制，分为拍摄、编辑、合成、上传四部分。

**拍摄**
拍摄视频，可以设置拍摄时长，拍摄的过程可以添加美颜、滤镜、贴纸等。

![拍摄](https://upyun-assets.b0.upaiyun.com/docs/video/paishe.png)


**编辑**
对拍摄的视频进行处理，比如剪辑、加入滤镜、调整背景音乐等。

![编辑](https://upyun-assets.b0.upaiyun.com/docs/video/bianji.png)

**合成**
把编辑好后的视频生成文件。

![合成](https://upyun-assets.b0.upaiyun.com/docs/video/hecheng.png)

**上传**
上传短视频文件到云存储。

![上传](https://upyun-assets.b0.upaiyun.com/docs/video/shangchuan.png)

**SDK 及文档**：[Android 短视频 SDK](https://github.com/upyun/Android-short-video)、[iOS 短视频 SDK](https://github.com/upyun/ios-short-video)


### 云存储

上传上来的短视频存放在云存储。

又拍云存储是对象存储，一份数据写三份，支持不限量存放数据，收费仅按存放数据量收费，相比其他云厂商，少了请求费用、流出流量费用。

另外，和又拍云 CDN 搭配使用，提供 CDN 使用流量 1:1 的免费存储空间，让存储成本更低。


**相关阅读**：[快速入门](https://docs.upyun.com/api/quick_start/)、[开发者工具](https://docs.upyun.com/api/developer_tools/)


### CDN 分发

当用户访问短视频时，通过 CDN进行全国和海外分发，让用户就近访问到短视频。

另外，CDN 会把访问过的短视频缓存起来，下次有用户来访问时，直接把它提供给用户，极大的提升了访问速度。

目前，又拍云 CDN 在国内建设了 200+ 节点，海外建设了 15+ 节点，满足全国各地和海外的访问需求。

**相关阅读**：[快速入门](https://docs.upyun.com/cdn/guide/)、[服务管理](https://docs.upyun.com/cdn/service/)

<a name="player_sdk"></a>
### 播放器 SDK

提供 Android 播放器 SDK 和 iOS 播放器 SDK，支持 Android、iPhone、iPad 等移动设备播放短视频，支持秒播、分段加载、拖拉等功能，省去企业开发播放器的时间。

![播放](https://upyun-assets.b0.upaiyun.com/docs/video/bofang.png)

**相关阅读**：[Android 播放器 SDK](https://github.com/upyun/Android-short-video#3)、[iOS 播放器 SDK](https://github.com/upyun/ios-short-video#3)


### 云端处理

云端处理是个可选的服务，由于终端的资源有限，不能做复杂处理，如果需要复杂的视频处理，需要使用云端处理。

云端短视频处理是异步进行的，提供转码、切片、剪辑、拼接、水印、截图等功能，提供链式处理（顺序执行多个功能），满足复杂处理需要。


**相关阅读**：[异步音视频处理](https://docs.upyun.com/cloud/av/)、[同步视频处理](https://docs.upyun.com/cloud/sync_video/)


## 应用场景

**搞笑幽默**

以糗事百科为例，糗事百科作为幽默搞笑类的巨头，我们为糗事百科提供了上传、云存储、分发、播放，帮助糗事百科构建搞笑短视频服务。

**电商买家卖家秀**

以洋码头为例，使用一整套方案，快速上线了买家卖家短视频秀，相比之前的文字图片评论，短视频展示的信息更丰富、更有趣，用户活跃度更高。

**户外&旅游**

记录美景、户外经历，通过断点续拍、回删功能，把它编辑成一个相册集或者 MV，互相分享。另外，还有搞笑贴纸供您选择。

**美食**

作为一个吃货，怎么少了记录美食呢。通过短视频解决方案，随时随地记录吃美食、制作美食、聚会等的前后过程，让快乐永久。


<a name="charges_notes"></a>
## 收费说明

解决方案按短视频 SDK、云存储、CDN 收费，上传、播放器 SDK 免费。如果有使用云端处理，云端处理需要收费。

**相关阅读**：[价格](https://www.upyun.com/products/short-video#section-pricing)


<a name="SDK_Key"></a>
## SDK 版本 Key 获取

短视频 SDK 提供一个免费版和两个收费版，任何版本使用都需要授权（key）。

免费版的 Key 有效期一年，到期后可以续期。收费版可以免费试用一个月。

获取 Key，请提供应用名称、申请使用的 SDK 版本、使用的平台（安卓、iOS）、bundleID（iOS）、包名（Android）给您的商务经理，或者[联系我们](https://www.upyun.com/contact)。

**相关阅读**：[短视频 SDK 版本](https://www.upyun.com/products/short-video#section-version)



















