##域名接入（自有源）

如果您的网站或应用使用的是自有源站或者第三方存储源，您可以通过自主源的方式接入 CDN 服务。此时您的源站无需做任何改动，只需要走完下面的配置流程并在 DNS 解析商那边修改 CNAME 记录即可享受 CDN 服务，具体操作参见如下步骤：

####第一步：选择服务类型

登陆 [CDN 控制台](https://console.upyun.com/login/)，点击 「创建服务」，选择服务类型为全网加速服务（也即 CDN 服务 ）。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun_cdn_create_service.jpg" height="470" width="800" />

####第二步：填写基本信息

1.服务名称：唯一标识服务，例如：upyun-test123，一个服务下面可以绑定多个域名。命名要求如下：

> 服务名称仅限 5～20 位；  
必须以小写英文字符开头，仅支持小写英文字符、数字、中划线组合。
 
2.源站类型：选择源站类型为“ 自主源 ”；

3.加速域名：填写此次需要配置的加速域名。具体要求如下：

>  加速域名已在工信部备案；  
待加速域名尚未在又拍云 CDN 平台配置。
 
详细配置参见如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun_cdn_basic_info.jpg" height="470" width="800" />

####第三步：填写回源信息

需要进行回源 HOST、回源协议、回源线路的配置，详细介绍请参见 [回源配置](https://docs.upyun.com/cdn/config/#2)。配置界面如下图所示：
<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-line-config.png" height="470" width="800" />

####第四步：服务状态确认

提交成功后，操作界面会提示：全网加速服务配置成功，并会自动跳转到该服务的功能配置界面，如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-state.png" height="470" width="800" />

在此配置界面，您也可以在该服务下绑定多个域名，详细点击 [域名绑定](https://docs.upyun.com/cdn/service/#_4)，请耐心等待域名配置（约 10 分钟），查看域名对应的状态是否为［正常］。

特别地，又拍云为每一个 CDN 服务提供了 [默认域名](https://docs.upyun.com/cdn/service/#_3)，默认域名格式为：
服务名.upaiyun.com，此默认域名仅供测试使用，正式业务环境请绑定自有域名。

####第五步：修改CNAME记录

在上一个步骤中，您可查看 CDN 平台为您分配的 CNAME 域名，此时需要去域名 DNS 解析商处，为该域名添加一条 CNAME 记录，待 CNAME 配置生效之后，方可享受 CDN 服务，具体配置详见 [CNAME 解析](https://docs.upyun.com/cdn/guide/#cname)。


##域名接入（又拍存储源）

如果您因为自主源压力较大，您也可以将非结构化数据托管在又拍云对象存储服务上面，使用又拍云存储源，更多了解请点击 [对象存储服务](https://docs.upyun.com/api/)。具体操作参见如下步骤：


####第一步：选择服务类型

登陆 [CDN 控制台](https://console.upyun.com/login/)，点击 「创建服务」，选择服务类型为全网加速服务（也即 CDN 服务 ）。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun_cdn_create_service.jpg" height="470" width="800" />

####第二步：填写基本信息

1.服务名称：唯一标识服务，例如：upyun-image，一个服务下面可以绑定多个域名。命名要求如下：

> 服务名称仅限 5～20 位； 必须以小写英文字符开头，仅支持小写英文字符、数字、中划线组合。

2.源站类型：选择源站类型为 “又拍云 ”。

详细配置参见如下截图所示：
 
<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-create-service-cunchu.png" height="470" width="800" />

####第三步：创建操作员

针对使用又拍云对象存储作为源的 CDN 服务，需要创建操作员来管理存储在又拍对象存储服务上面的非结构化数据，详细操作和介绍点击 [操作员](https://docs.upyun.com/cdn/service/#_7)。

####第四步：服务状态确认

提交成功后，操作界面会提示：全网加速服务配置成功，并会自动跳转到该服务的功能配置界面，如图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-state.png" height="470" width="800" />

在此配置界面，您也可以在该服务下绑定多个域名，详细点击 [域名绑定](https://docs.upyun.com/cdn/service/#_4)，请耐心等待域名配置（约 10 分钟），查看域名对应的状态是否为［正常］。

特别地，又拍云为每一个 CDN 服务提供了 [默认域名](https://docs.upyun.com/cdn/service/#_3)，默认域名格式为：
服务名.upaiyun.com，此默认域名仅供测试使用，正式业务环境请绑定自有域名。

####第五步：资源文件上传

以上步骤操作完毕之后，可以使用操作员针对该服务进行资源文件的上传和管理操作，资源文件的上传和管理，请参见 [云存储开发者工具](http://docs.upyun.com/api/developer_tools/)。待资源文件都上传完毕，确保该存储空间可以正常提供服务之后，就可以开始使用 CDN 服务了。

####第六步：修改CNAME记录**

在以上步骤中，您可查看 CDN 平台为您分配的 CNAME 域名，此时需要去域名 DNS 解析商处，为该域名添加一条 CNAME 记录，待 CNAME 配置生效之后，方可享受 CDN 服务，具体配置详见 [CNAME 解析](https://docs.upyun.com/cdn/guide/#cname)。

##默认域名

为了方便用户使用，针对每一个 CDN 服务，又拍云提供了默认域名，该默认域名仅供测试使用，正式业务环境请绑定自主域名。相关声明如下：

* 默认域名不收取任何费用，仅供业务功能性测试；
* 默认域名很容易会被微信、QQ 等平台封禁，请谨慎使用；
* 使用默认域名测试 CDN 加速，加速质量得不到保障，不建议使用；
* 已经使用默认域名作为正式业务的用户，建议尽快更换为自主域名；

##域名绑定

针对同一个服务，如果回源地址相同（也即共用同一个源站），此时可以绑定多个自定义域名。

####配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入「功能配置」界面，选择 「加速域名」 模块，点击 「域名绑定」 按钮。如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-domains.png" height="300" width="700" />

####注意事项

* 除常规形式的域名外（ 包括顶级域名 ），我们还支持泛域名绑定，比如 `*.yourdomain.com`；特别地，其中 `*` 最多支持匹配 4 层：

```
[v] *.yourdomain.com => foo.yourdomain.com
[v] *.yourdomain.com => bar.yourdomain.com
[v] *.yourdomain.com => foo.bar.yourdomain.com
[v] *.yourdomain.com => foo.bar.baz.yourdomain.com
[v] *.yourdomain.com => foo.bar.baz.qux.yourdomain.com
[x] *.yourdomain.com => foo.bar.baz.qux.quxx.yourdomain.com

[v] *.bar.yourdomain.com => foo.bar.yourdomain.com
[v] *.bar.yourdomain.com => baz.foo.bar.yourdomain.com
[x] *.bar.yourdomain.com => foo.bar.baz.yourdomain.com
```
* 添加域名绑定后，需要到域名服务商的 DNS 解析管理中，将域名的 CNAME 解析到 `<bucket>.b0.aicdn.com`；
* 特别地，单个服务下面，最多支持绑定 10 个域名，且待绑定的域名需要已备案，否则会无法通过审核。


##服务删除

如果要删除配置和对应的服务，需要执行“服务删除”操作，该操作不可逆，请谨慎操作。

####配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：功能配置 > 高级功能 > 服务删除，点击「删除」 按钮，出现如下图截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-delete.png" height="470" width="800" />


####注意事项

* 服务删除前，需要清空服务下的数据（特针对云存储服务）；
* 删除后，7天内无法创建同名服务；
* 删除不可逆，操作后该服务原有配置均失效。

##海外加速

针对大陆以外地区的最终用户（包括香港、澳门、台湾地区），开启海外加速，会默认解析到又拍云海外的 CDN 加速节点；关闭状态下，会默认解析到国内 CDN 加速节点。

####配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：功能配置 > 加速域名 > 海外加速，可以有选择性的开启和关闭（默认为开启状态）。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-oversea.png" height="470" width="800" />

####注意事项

* 海外加速默认为开启状态，只针对该服务下域名生效；
* 开启状态下，海外请求会默认解析到又拍云海外 CDN 节点，计费按海外地区定价计算；
* 关闭状态下，海外请求会默认解析到又拍云国内 CDN 节点，计费按国内地区定价计算；
* 海外特指除了大陆以外的地区，包括香港、澳门、台湾地区；


##操作员

针对使用又拍云存储服务作为源的 CDN 服务，我们会引入“操作员”的概念，通过操作员，可以去管理您在又拍对象存储上面的非结构化数据，这些操作可能包括：读取、写入、删除。

####基本概念

操作员是您访问对象存储服务的凭证，它由操作员名称和密码组成，操作员名称类似身份标识，密码的作用是签名您的访问请求，以防被篡改。特别地，操作员与服务名是 N:N 的关系，一个服务名可以有多个操作员，一个操作员可以归属于多个服务名。

####操作员权限

操作员权限有 3 种，分别是可读取、可写入、可删除，支持多选。您可以根据需要，为操作员配置合适的权限；也可以创建多个操作员，为不同操作员分配不同权限，供不同级别的人使用。

####配置引导

登陆 [CDN 控制台](https://console.upyun.com/login/)，添加、授权、管理操作员有几个入口，分别是：

1、入口一：在创建服务的过程中，可以创建操作员或授权已有操作员，也可以暂时跳过该步骤，如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-operator1.png" height="400" width="700" />

2、入口二：服务创建完毕之后，去对应的服务下面进行配置，依次进入：功能配置 > 基础配置 > 已授权操作员。此处可以授权已创建好的操作员，也可以新创建操作员并授权。如下截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-operator2.png" height="300" width="700" />

3、入口三：依次进入：账户管理 > 操作员管理，或者点击 [操作员管理](https://console.upyun.com/account/operators/) 进入管理界面。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-service-operator3.png" height="300" width="700" />

通过以上截图可以看出，该入口可以添加操作员，也可以针对操作员进行编辑、禁用、删除操作。

####注意事项

* 操作员的概念是针对使用又拍对象存储作为存储源的 CDN 服务才会使用到，更详细的使用和介绍，请参考[云存储文档](http://docs.upyun.com/api/)；
* 特别的，针对使用自主源的 CDN 服务，开启[镜像存储](https://docs.upyun.com/cdn/config/#9)功能之后，也可以使用操作员对存储在又拍对象存储上的非结构化数据进行管理操作。


 