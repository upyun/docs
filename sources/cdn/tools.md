##IP 检测工具

可通过该功能验证 IP 地址是否属于又拍云 CDN 节点。如果属于又拍云 CDN 节点 IP，可以查看到 IP 所属地区及运营商信息。
 
配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，在置顶菜单进入 `工具箱`，找到 `IP 检测工具` 栏目。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-ip-check.png" height="490" width="800" />

在打开的界面窗口中，输入需要检测的 IP 地址，点击 `检测` 按钮，即可查看检测结果。

----------


##CDN 健康检测

通过该工具，可对比查看国内知名网站的下载速度，并给出又拍云 CDN 下载速度评估结果。与此同时，可显示客户端网络基本信息，方便定位问题。

配置引导

登陆 [又拍云管理控制台](https://console.upyun.com/login/)，在置顶菜单进入 `工具箱`，找到 `CDN 健康检测` 栏目。点击链接之后，会跳转到如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-tools-health.png" height="490" width="800" />


您也可以点击链接 [又拍云 CDN 健康检查工具](http://pubstatic.b0.upaiyun.com/cdn-health.html) 进行查看。

----------

##子账号管理

为了方便不同的操作人员管理、查看不同的服务配置，又拍云推出了子账号功能。主账号可以根据需要创建多个子账号，并为每个子账号分配不同权限，供不同级别的人使用。不同管理人员可以通过子账号，登入又拍云控制台，查看、配置、管理相应的服务、功能。

**配置引导**

**第一步：创建子账号**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「账户管理」页面，找到「子账号」配置项，点击「创建子账号」

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/tools/upyun-cdn-tools-subaccount1.jpg" height="350" width="750" />

**第二步：基本配置**

该步骤包含了创建子账号名称、密码，填写备注等信息。

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/tools/upyun-cdn-tools-subaccount2.jpg" height="350" width="750" />

**第三步：授权**

功能授权界面如截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/tools/upyun-cdn-tools-subaccount3.jpg" height="350" width="750" />


授权此子账号可以查看、管理的功能权限包括：

**CDN**：授权子账号可以查看、管理、配置的 CDN 服务，但此子账号无权新增 CDN 服务。

**云存储**：授权子账号可以查看、管理、配置的云存储服务，但此子账号无权新增云存储服务。

**直播**：授权子账号可以查看的直播服务，但此子账号无权新增直播服务。

**统计**：授权子账号可以查看<mark>所有</mark> CDN、云存储、直播服务的统计数据。

**监控**：授权子账号可以查看<mark>所有</mark> CDN、云存储、直播服务的监控数据。

**刷新**：授权子账号可以针对<mark>所有</mark> CDN、云存储服务进行刷新、预热等操作。


**第四步：登入子账号**

授权某个子账号所有功能，登入后展示如下：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/tools/upyun-cdn-tools-subaccount4.jpg" height="350" width="750" />


**注意事项**

* 子账号具备管理、修改服务配置等权限；可查看所有 CDN、云存储、直播服务的统计、监控数据；可以针对 CDN、云存储所有服务进行刷新、预热操作；所以请谨慎分配。

* 子账号和密码请谨慎保管，一旦泄露，请及时更换。

如有疑问请 [联系我们](https://www.upyun.com/contact)
