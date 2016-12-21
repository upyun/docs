## 开始使用又拍云存储

又拍云存储是面向对象的存储服务，它与又拍云 CDN 无缝衔接在一起，在您使用它的同时，也在使用又拍云 CDN。在使用又拍云存储之前，请确保您已经注册了又拍云账号并完成实名验证。

---------

## 创建服务

登录又拍云控制台，在 「服务」 中，创建 「全网加速」 服务。

<a href="http://upyun-assets.b0.upaiyun.com/docs/storage/create_service.png"  target="_blank" title="查看大图"><img src="http://upyun-assets.b0.upaiyun.com/docs/storage/create_service.png" height="490" width="800" alt="创建服务名" /></a>

选择 「源站类型」 为 「又拍云」 ，并为该服务创建或授权操作员。

<a href="http://upyun-assets.b0.upaiyun.com/docs/storage/create_service2.png"  target="_blank" title="查看大图"><img src="http://upyun-assets.b0.upaiyun.com/docs/storage/create_service2.png" height="490" width="800" alt="创建服务名" /></a>

点击 「创建」，云存储服务创建成功。

---------

## 服务使用

又拍云存储提供了多种方式使用服务，您可以根据情况，选择合适的方式。

- 图形化工具：[FTP/FTPS](/api/developer_tools/#ftpftps)
- 命令行工具：[UPX](/api/developer_tools/#upx)，[cURL](/api/developer_tools/#curl)
- SDK：[各语言 SDK](/download/) 
- API：[REST API](/api/rest_api/) ，[FORM API](/api/form_api/)

---------

<a name="operator_make"></a>
## 操作员使用

- 在[创建服务](#_2)时，创建或授权操作员。
- 在「服务」>「配置」>「基础配置」>「已授权操作员」中，创建或授权操作员。
- 在「账号」>「账号管理」>「操作员管理」中，管理操作员。
- 操作员的解释说明，见[基本概念](/api/#concept)。

---------

## 删除服务

登录又拍云控制台，从 「服务」 中进入需要删除服务的 「配置」，在 「高级功能」 里面，可以删除服务名。

<a href="http://upyun-assets.b0.upaiyun.com/docs/storage/delete_service.png"  target="_blank" title="查看大图"><img src="http://upyun-assets.b0.upaiyun.com/docs/storage/delete_service.png" height="490" width="800" alt="创建服务名" /></a>

特别地，服务删除前，需要清空服务下的数据；服务删除后，七天内不能创建同名服务。

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)