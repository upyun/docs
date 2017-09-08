##域名接入（自有源)

如果您的网站或应用使用的是自有源站或者第三方存储源，您可以通过自主源的方式接入 CDN 服务。此时您的源站无需做任何改动，只需要走完下面的配置流程并在 DNS 解析商处修改 CNAME 记录即可享受 CDN 服务，具体操作参见如下步骤：

####第一步：创建服务

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「CDN 服务」，点击 「创建服务」。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun_cdn_create_service.png" height="s470" width="800" />

####第二步：基本配置

该步骤包括服务名称、加速域名、应用场景等选项的配置，为必选项，详细配置参见如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun_cdn_basic_info.png" height="470" width="800" />

1.服务名称：唯一标识服务，例如：image-upyun-com，一个服务下面可以绑定多个自有域名，命名要求如下：

>  服务名称仅限 5～20 位；
必须以小写英文字符开头，仅支持小写英文字符、数字、中划线组合。

2.加速域名：填写此次需要配置的加速域名。具体要求如下：

> 加速域名已在工信部备案；
待加速域名尚未在又拍云 CDN 平台配置。

3.应用场景：可选项包括网页图片、文件下载、音视频点播、动态内容、全站加速，阐述如下：

 - 网页图片：适用于电商类、网站类、游戏图片类静态小文件等业务。
 - 文件下载：适用于游戏安装包、音视频原文件下载、手机固件分发等业务。
 - 音视频点播：适合音、视频文件较多的在线点播业务。
 - 动态内容：适合 PHP、JSP、ASP 等动态类资源较多的业务。
 - 全站加速：适合动、静态内容未做明确区分的业务，又拍云自动实现动静分离。
 
####第三步：回源配置

需要进行回源协议、回源线路的配置，更多配置介绍请参见 [回源配置](https://docs.upyun.com/cdn/config/#2)。配置界面如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-line-config.png" height="470" width="800" />

1.回源协议：对回源协议进行配置，默认为 HTTP，配置选项包括 HTTP、HTTPS、协议跟随。

2.线路配置：此处包括回源地址、回源端口号的设置，更多设置请参见 [线路配置](https://docs.upyun.com/cdn/config/#23)。

####第四步：状态确认

服务创建成功后，操作界面会提示 CDN 加速服务创建成功，并会自动跳转到该服务的【功能配置】界面，如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-function-config.png" height="470" width="800" />


在功能配置界面，有域名管理、回源管理、缓存配置、性能优化、HTTPS、访问控制、图片处理等功能配置模块，在【域名管理】模块下，您可以针对该服务绑定多个自有域名，请耐心等待域名配置（约 10 分钟），查看域名对应的状态是否为［正常］，如下图所示：


<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-service-state.png" height="470" width="800" />

特别地，又拍云为每一个 CDN 服务提供了默认域名，默认域名格式为：服务名.upaiyun.com，此默认域名仅供测试使用，正式业务环境请绑定自有域名。

####第五步：CNAME 配置

在上一个步骤中，您可查看 CDN 平台为您分配的 CNAME 地址，此时需要去域名 DNS 解析商处，为该域名添加一条 CNAME 记录，待 CNAME 配置生效之后，方可享受 CDN 服务，具体配置详见 [CNAME 解析](https://docs.upyun.com/cdn/guide/#cname)。获取 CNAME 地址的入口有：

1.在【服务管理】界面，将鼠标放置【服务名称】显示区域即可显示 CNAME 地址，可直接复制，如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-service-cname1.png" height="470" width="800" />

2.在【功能配置】界面，选择「域名管理」模块，相应区域可查看 CNAME 地址，如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-service-cname2.png" height="470" width="800" />

##域名接入（又拍存储源）

如果您因为自主源压力较大，您也可以将非结构化数据托管在又拍云对象存储服务上面，使用又拍云存储源，更多了解请点击 [对象存储服务](https://docs.upyun.com/api/)。具体操作参见如下步骤：

####第一步：创建服务

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入「云存储」服务，点击 「创建服务」，如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-create-service.png" height="470" width="800" />

####第二步：基本配置

创建一个存储服务，首先需要配置一个服务名称，另外就是选择一个应用场景，详细配置参见如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun_storage_basic_info.png" height="470" width="800" />

1.服务名称：唯一标识服务，例如：video-upyun-com，一个服务下面可以绑定多个域名。命名要求如下：

> 服务名称仅限 5～20 位； 
必须以小写英文字符开头，仅支持小写英文字符、数字、中划线组合。

2.应用场景：可选项包括网页图片、文件下载、音视频点播，阐述如下：

 - 网页图片：适用于电商类、网站类、游戏图片类静态小文件等业务。
 - 文件下载：适用于游戏安装包、音视频原文件下载、手机固件分发等业务。
 - 音视频点播：适合音、视频文件较多的在线点播业务。

####第三步：创建操作员

针对使用又拍云对象存储作为源的 CDN 服务，需要创建操作员来管理存储在又拍对象存储服务上面的非结构化数据，详细介绍请点击 [操作员](http://docs.upyun.com/api/#concept)，具体配置界面如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-operator-config.png" height="470" width="800" />

关于如何使用操作员管理存储上的非结构化数据，详情参照 [对象存储开发者工具](http://docs.upyun.com/api/developer_tools/)。

####第四步：状态确认

服务创建成功后，操作界面会提示云存储服务配置成功，并会自动跳转到该服务的功能配置界面，如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-function-config.png" height="470" width="800" />

在功能配置界面，有域名管理、内容管理、缓存配置、性能优化、HTTPS、访问控制、图片处理等功能配置模块，在【域名管理】模块下，您可以针对该服务绑定多个自有域名，请耐心等待域名配置（约 10 分钟），查看域名对应的状态是否为［正常］，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-service-state.png" height="470" width="800" />

特别地，又拍云为每一个云存储服务提供了默认域名，默认域名格式为：
服务名.upaiyun.com，此默认域名仅供测试使用，正式业务环境请绑定自有域名。

####第五步：资源管理

以上步骤操作完毕之后，可以使用操作员针对该服务进行资源文件的上传和管理操作，资源文件的上传和管理，请参见 [云存储开发者工具](http://docs.upyun.com/api/developer_tools/)。待资源文件都上传完毕，确保该云存储服务可以正常提供服务之后，就可以开始使用 CDN 服务了。云存储服务内容管理的入口参见如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-content-management.png" height="470" width="800" />

您也可以进入服务的功能配置下的【内容管理】模块进行相关设置，例如：API 密钥、操作员授权等操作，具体使用请参见【管理指南】的详情介绍。

####第六步：CNAME 配置

在上一个步骤中，您可查看 CDN 平台为您分配的 CNAME 域名，此时需要去域名 DNS 解析商处，为该域名添加一条 CNAME 记录，待 CNAME 配置生效之后，方可享受 CDN 服务，具体配置详见 [CNAME 解析](https://docs.upyun.com/cdn/guide/#cname)。获取 CNAME 地址的入口有：

1.在【服务管理】界面，将鼠标放置【服务名称】显示区域即可显示 CNAME 地址，可直接复制，如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-service-cname1.png" height="470" width="800" />

2.在【功能配置】界面，选择「域名管理」模块，相应区域可查看 CNAME 地址，如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-storage-service-cname2.png" height="470" width="800" />

##操作管理

进入 CDN  控制台，在服务管理界面，您可以针对已经创建的 CDN 加速服务进行开启、关闭、删除、全网刷新、统计分析查询等操作。

###开启加速服务

针对已经处于关闭状态的 CDN 加速服务，您可以进行【开启】操作，开启操作会在 1 分钟之内生效，具体操作如下：登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「 服务管理」相应界面。如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-open-service.png" height="470" width="800" />

点击需要开启的 CDN 加速服务，选择【开启】按钮，即可开启。

###关闭加速服务

针对已经处于开启状态的 CDN 加速服务，您可以进行【关闭】操作，关闭操作会在 1 分钟之内生效，具体操作如下：登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「 服务管理」相应界面。如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-close-service.png" height="470" width="800" />

选择需要关闭的 CDN 加速服务，选择【关闭】按钮，即可关闭 CDN 服务。

注意：服务关闭后，所有资源都将无法访问，并返回 405 状态码，请谨慎操作！


###删除加速服务

针对已经处于关闭状态的 CDN 加速服务，您可以进行服务【删除】操作，删除操作会在 1 分钟之内生效，具体操作如下：登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「 服务管理」相应界面。如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-delete-service.png" height="470" width="800" />

选择需要删除的 CDN 加速服务，点击【删除】按钮，即可删除 CDN 服务。

注意事项：

 - 删除时，确保该服务已不存在任何目录；
 - 删除后，7天内无法创建同名服务；
 - 删除不可逆，操作后该服务原有配置均失效。

###全网缓存刷新

如需对服务进行全网缓存刷新，可以通过相应入口进行全网刷新操作。具体操作如下：登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「 服务管理」相应界面。如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-purge-service.png" height="470" width="800" />

选择需要进行全网刷新的 CDN 加速服务，点击【刷新】按钮，即可针对该服务进行全网刷新。

注意：

 - 同一服务每次刷新间隔至少 10 分钟，一天只能刷新 5 次缓存；
 - 全网缓存刷新会对源站产生一定压力，请谨慎操作；

###统计分析查询

如需了解 CDN 服务带宽、流量、请求数等维度的数据统计和分析数据，可以通过该入口进行查询。具体操作如下：登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「 服务管理」相应界面。如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/service/upyun-cdn-%20analysis-service.png" height="470" width="800" />

选择需要进行统计分析查询的 CDN 加速服务，点击【统计】按钮，即可快速进入统计分析页面。
 
