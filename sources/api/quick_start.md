## 开始使用又拍云云存储

又拍云云存储是面向对象的存储服务，它与又拍云 CDN 无缝衔接在一起，在您使用它的同时，也在使用又拍云 CDN。在使用又拍云云存储之前，请确保您已经注册了又拍云账号并完成实名验证。


## 创建服务

登录又拍云控制台，在 “服务”中，创建 “全网加速” 服务。

![创建服务名](http://upyun-assets.b0.upaiyun.com/docs/storage/create_service.png_/fw/800)

选择 “源站类型” 为 “又拍云” ，并为该服务创建或配置 “操作员”。

![创建服务名](http://upyun-assets.b0.upaiyun.com/docs/storage/create_service2.png_/fw/800)

点击 “创建”，云存储服务创建成功。

## 服务使用

又拍云云存储提供了多种方式使用服务，您可以根据情况，选择合适的方式。

- 图形化工具：[FTP/FTPS](/api/developer_tools/#ftpftps)
- 命令行工具：[UPX](/api/developer_tools/#upx)，[cURL](/api/developer_tools/#curl)
- SDK：[各语言 SDK](/download/) 
- API：[REST API](/api/rest_api/) ，[FORM API](/api/form_api/)

## 删除服务

登录又拍云控制台，从 “服务” 中进入需要删除服务的 “配置”，在 “配置” 的 “高级功能” 里面，可以删除服务名。

![删除服务](http://upyun-assets.b0.upaiyun.com/docs/storage/delete_service.png_/fw/800)

特别地，服务删除前，需要清空服务下的数据；服务删除后，七天内不能创建同名服务。