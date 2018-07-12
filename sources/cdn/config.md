又拍云 CDN 服务支持多项功能自定义配置，您可以根据自身业务需要进行设置，优化您的 CDN 加速效果或配合实现某些业务逻辑。

##1.域名管理

###1.1 基本信息

**功能说明**

通过该模块，可以查看服务的基本信息，包括：服务名称、创建时间、服务状态、应用场景等相关信息。此处可以快速获取服务的 CNAME 地址，方便进行 CDN 切换。

**查看引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入「域名管理」功能模块，找到「基本信息」一栏，即可查询服务基本信息，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-basic-info.png "height="470" width="800" />

###1.2 加速域名

**功能说明**

在同一个服务下面，如果回源地址相同（ 也即共用同一个源站 ），此时可以绑定多个自有域名。除常规形式的域名外，还支持绑定多个泛域名，比如：*.yourdomain.com。特别地，其中 * 最多支持匹配 4 层：

    [√] *.yourdomain.com => foo.yourdomain.com
    [√] *.yourdomain.com => bar.yourdomain.com
    [√] *.yourdomain.com => foo.bar.yourdomain.com
    [√] *.yourdomain.com => foo.bar.baz.yourdomain.com
    [√] *.yourdomain.com => foo.bar.baz.qux.yourdomain.com
    [x] *.yourdomain.com => foo.bar.baz.qux.quxx.yourdomain.com
    [√] *.bar.yourdomain.com => foo.bar.yourdomain.com
    [√] *.bar.yourdomain.com => baz.foo.bar.yourdomain.com
    [x] *.bar.yourdomain.com => foo.bar.baz.yourdomain.com

提示：以上 [√] 特指泛域名对应的正确示例，反之 [x] 为不正确的。

**操作引导**

加速域名的管理有域名绑定、删除域名等操作，您可以通过该模块来管理您的域名。您可登陆 [CDN 控制台](https://console.upyun.com/login/)，进入「域名管理」功能模块，找到「加速域名」一栏，即可进行如下操作：

1.绑定域名

第一步：点击【域名绑定】按钮

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-domain-add.png "height="470" width="800" />

第二步：填写待添加的域名

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-domain-add2.png "height="470" width="800" />

点击【添加】按钮，即可添加待绑定的域名。

第三步：查看已添加域名的状态

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-domain-add3.png" height="470" width="800" />

如果此时已经添加的域名为【正常】状态，则已审核通过，如果为【拒绝】状态，则此时域名可能未备案，未被审核通过。

2.删除域名

第一步：点击【删除】按钮

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-domain-del.png" height="470" width="800" />


第二步：待删除域名确认

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-domain-del2.png" height="470" width="800" />

点击【删除】按钮即可将该域名进行删除。


**注意事项**

 - 一个服务可以绑定多个域名，且最多支持绑定 10 个自有域名，待绑定的域名需要已备案，否则会无法通过审核；
 - 域名审核通常在 1 小时以内完成，审核通过之后的状态为“正常”，请在审核通过之后再进行 CNAME 切换；
 - 新备案域名由于信息同步有延时，请在备案 1-2 天后再进行绑定，否则会导致域名审核时无法查询备案信息而被拒绝；
 - 平台将定期检查域名是否备案，当有一个域名备案过期或失效时，为确保正常访问，域名所在服务将临时解析到又拍云海外加速节点，期间所产生的访问流量将按海外定价计费；7 天之后，如果备案仍为失效，该域名绑定状态将变为“拒绝”状态，CDN 会返回 400 状态码；
 
###1.3 测试域名
 
**功能说明**
 
为了配合又拍云存储以及 CDN 服务使用，又拍云提供了测试域名，仅用于测试服务的可用性。关于测试域名的使用规范及注意事项，这里说明如下：

- 新创建的服务启用新的测试域名（命名规则为：服务名称.test.upcdn.net）；
- 老的存储及 CDN 服务，测试域名的格式为：服务名称.b0.upaiyun.com，仍可继续使用，但是限于测试域名是公共资源，可能会受到其他用户影响，已经用于正式生产环境的用户，请尽快将业务切换到自有绑定的域名；
- 测试域名有诸多限制（例如：限速、限制 CDN 节点数量），不能用于正式的生产环境，仅供测试使用，实际线上业务请绑定自有域名。

**查看引导**

您可登陆 [CDN 控制台](https://console.upyun.com/login/)，进入「域名管理」功能模块，找到「加速域名」一栏，即可查看：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-domain-test.png" height="470" width="800" />


##2.回源管理

###2.1 源站设置

源站设置主要是对资源文件的回源地址进行管理以及配合源站实际资源情况进行一些更高级的设置，包括主备线路、端口号、轮询权重等。其中，回源地址支持 IP 源站和域名源站，例如：

    IP：1.1.1.1
    域名：src.example.com

针对回源地址为域名源站的情况，CDN 节点会进行一次 DNS 解析，解析到的 IP 即为真实的回源地址。

**回源协议**

回源协议顾名思义也就是 CDN 节点回源使用的协议。默认情况下，CDN 会以 HTTP 协议回源，您也可以采用 HTTPS 协议回源。

*配置引导*

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「回源管理」页面，找到「源站设置」配置项，点击【管理】按钮进入管理页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-xieyi.png" height="470" width="800" />

通过配置项可以看到，CDN 有三种回源方式，分别为：

1、HTTP 回源

默认采用 HTTP 协议回源，此时回源地址的端口号默认为 80。

2、HTTPS 回源

也可以选择 HTTPS 协议回源，选择 HTTPS 协议回源时，端口号会变为 443 端口。

3、协议跟随

当启用该特性时，回源协议始终会和客户端访问 CDN 的协议保持一致，即客户端用 HTTP 协议访问 CDN，那么 CDN 回源时也会用 HTTP 协议进行回源，同样，若采用 HTTPS 访问，那么回源也是 HTTPS。

*注意事项*

 - 特别地，回源地址的端口号配置将仅作用于回源协议为 HTTP 的情况，默认 为 80 端口；当然你也可以自定义为其他值，例如 8080 或
   8443。
 - 当回源协议为 HTTPS 时，端口号则始终固定为 443，且目前暂时不支持修改此固定值；
 - 若之前选择 HTTPS 协议回源 且自定义设置过端口号的用户需要注意，切换到 `协议跟随` 模式下时，自定义的端口号将仅作用于回源协议为
   HTTP 的情况。

**线路配置**

线路配置包括回源地址、端口号、主备线路、轮询权重、最大失败次数、静默时间等高级设置。其中回源地址则表示源站实际可访问的网络地址，可填 IP 地址或域名，如果是域名地址，那么 CDN 在回源时会对该域名地址进行 DNS 解析，然后针对解析出来的 IP 地址进行访问，因此若解析失败将会导致回源异常。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「回源管理」页面，找到「源站设置」配置项，点击【管理】按钮进入管理页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-line.png" height="470" width="800" />

以下将重点介绍主备线路、轮询权重、最大失败次数和静默时间：

 - 主、备线路
 
回源地址配置中除了默认的 `主线路` 外，我们还支持 `备份线路`  的配置。默认情况下， `主线路` 多个回源地址会以 RR 轮询方式进行调度，当且仅当所有 `主线路` 回源地址均不可达时，此时我们将使用 `备份线路` 进行转发，若 `备份线路` 有多个回源地址，那么负载均衡方式也和 `主线路` 一样，以 Round-Robin 轮询（详情请点击[链接](https://en.wikipedia.org/wiki/Round-robin_scheduling)）方式进行调度。

 - 轮询权重

默认情况下，轮询权重都为 1，表示所有回源地址轮询到的概率均等。当然也可以如上图那样自定义轮询权重，我们看到主线路两个回源地址权重分别是 1 和 2，此时主线路集群在轮询调度中会有 1/3 的概率回 `192.168.11.1:80` 这个地址，而回 `192.168.11.2:80` 这个地址的概率则为 2/3。即当前回源地址轮询到的概率可按（当前节点权重/集群所有节点权重之和）这个公式计算。

 - 最大失败次数和静默时间

表示在一个 `静默时间` 内如果对应源站的异常次数累计达到当前设置的 `最大失败次数` ，那么在下一个  `静默时间` 内，CDN 就会认为这个源站宕机了，即在这段时间内不会再将请求转发给它。特别地，当 `最大失败次数` 其值为 0 时，表示不会进行失败累计，`静默时间` 也同时失效。

合理设置最大失败次数和静默时间，有助于在源站异常的情况下最大程度减少对线上请求的影响。这里实际上跟 Nginx 核心的 upstream 模块实现机制类似，进一步了解可参考 [链接](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server) 。

**注意事项**

 - 关于主、备回源，当主源出现错误时，POST 以及带 BODY 的 GET 方法的请求不会回备源进行重试操作。


###2.2 回源 Host

**功能说明**

该配置是指 CDN 节点回源过程中所需访问的源站服务器 Web 站点资源的访问域名。CDN 节点回源时会在 HTTP 请求头 Host 字段里替换该域名，以支持源服务器可通过此 Host 做的特殊处理逻辑，一般是域名形式的字符串，当然也支持任意字符串形式。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「回源管理」配置页面，找到「回源 Host」配置项，点击【管理】按钮进入管理页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-host.png" height="470" width="800" />

如上截图所示，回源 Host 配置为：`src.image.upyun.com`

1.回源 Host 为可选配置项，默认为空。特别地，如果该自定义值为空，子域名的回源 Host 为所配置的加速域名，泛域名回源 Host 为访问域名；

2.自定义配置：您可以根据业务情况配置回源 Host，满足特定业务需求；

**注意事项**

 - 回源 Host 配置目前仅支持使用自有源接入的 CDN 服务；
 - 配置自定义回源 Host 之后，请保证该域名可以正常访问，否则会出现回源失败的情况；
 - 回源地址和回源 Host 的区别：回源地址是 CDN 节点回源时能够找到对应的源站服务器，源站可能有多个 Web 站点，回源 Host 则指明了资源所在的站点。

----------

###2.3 源站资源迁移


**功能说明**


源站资源迁移功能的场景是无缝将用户源站静态资源迁移到又拍云存储上来，当下次终端用户访问相同资源时，CDN 将直接返回已经迁移到云存储上的资源文件，而无需回用户自主源，可减小自主源压力，也方便进行资源文件的无缝迁移。

**实现机制**


进行资源迁移需要考虑如下两种场景：


**1）某个资源未迁移之前，会优先回存储，其次回自主源，最终进行资源迁移，详细访问流程参见如下图所示：**

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-no-move.png" height="300" width="600" />

大致的访问流程为：

 - 终端用户向 CDN 请求资源文件，CDN 会优先回又拍云存储获取资源；
 - 此时又拍云存储没有该资源，CDN 节点会从用户自主源站获取资源文件；
 - 最终 CDN 返回资源文件给终端用户，并将资源文件持久化存储到又拍云对象存储上；


**2）某个资源迁移成功之后，直接访问云存储，不再进行资源迁移，访问流程参见如下图所示：**

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-move-success.png" height="300" width="600" />

大致的访问流程是：

 - 终端用户再次访问相同资源文件时，将直接从又拍云存储获取资源，不再回用户自主源获取资源；
 - 随着终端用户的不断访问，源站的静态资源将逐步迁移到又拍云存储源上来。


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，进入 「回源管理」配置页面，找到「源站资源迁移」配置项，点击【管理】按钮，进入如下配置界面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor.png" height="470" width="800" />

 - 第一步：配置资源迁移状态

源站资源迁移功能包括三种状态，分别是开启、暂停、关闭，默认为关闭状态。详细描述如下：


<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-status.png" height="470" width="800" />

**开启状态**

开启之后，当 CDN 节点缓存失效或未命中时，会优先回又拍云存储获取资源，如果又拍云存储返回 4XX、5XX 状态码时，则直接回自有源站获取资源并响应给最终用户。与此同时，资源会被扔进资源迁移队列并进行存储，下一个相同的资源请求过来时则会直接由存储命中的资源直接提供服务。

**暂停状态**

直接关闭源站资源迁移功能，可能会导致回自主源压力过大，在暂停状态下，不会进行资源迁移动作，访问资源时，仍然先访问又拍云存储，如果又拍云存储返回 4XX、5XX 状态码时，则会访问自主源站。也即资源访问的流程不变，只不过不会进行资源的迁移操作，起到了一个缓冲的作用。

**关闭状态**

该功能关闭之后，新的请求过来的话，CDN 节点不会回又拍云存储平台获取资源，而会直接回客户源站获取资源。


 - 第二步：配置资源路径

在配置的过程中，您需要配置资源路径，指定某些资源进行迁移，例如需要指定 `html`、`mp4`、`flv`、`jpg`、`png` 等资源进行迁移。如截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-lujing.png" height="470" width="800" />

配置示例为：

```
/*.html
/*.mp4
/*.flv
/*.jpg
/*.png

```
值得注意的是，该项如未配置，则默认迁移服务下面的所有静态资源文件。


 - 第三步：选择云存储服务
 
该步骤需要确保您已经创建了一个云存储服务，否则无法配置成功。我们会通过下拉列表的形式展示当前账号下已创建的云存储服务，以供选择。如截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/monitor-storage/upyun-cdn-monitor-storage.png" height="470" width="800" />

确保以上配置正确之后，点击配置界面右下角的【确定】按钮即可。

**注意事项**

 - 当用户更新了源站资源时，为了保证请求访问到的资源是最新的，此时需要通过 API 或 FTP 的方式对已经存储在又拍云存储上的文件进行删除和替换操作；

 - 当源站资源迁移功能处于暂停状态时，您可以通过 API
   接口逐步删除又拍云上对应的文件，将回源流量平滑迁移至源站，或调整源站带宽，最后再执行关闭操作。
 - 资源迁移的生效有一个前提条件，那就是资源文件在 CDN 的缓存过期时间必须大于 24 小时，才会执行迁移动作；

----------

###2.4 离线模式

> 功能说明

离线模式是指在网站源服务器未能正常提供服务期间，也即当源站出现宕机、网络中断等情况时，CDN 节点在该时间段内不再回源请求资源文件，而由 CDN 节点直接返回节点上已缓存资源内容，该功能能够有效降低源故障期间带来的损失，提升网站的用户体验。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入「功能配置」和「回源管理」配置界面，找到「离线模式」配置项，如下截图所示：

第一步：依次进入配置管理、回源管理页面

进入功能配置页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config.png" height="470" width="800" />

进入回源管理配置页面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source.png" height="470" width="800" />

第二步：在回源管理模块，找到【离线模式】配置项

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-lixian.png" height="470" width="800" />

点击【确定】按钮，即可开启离线模式功能。


**注意事项**

 - 源站返回 502、504 状态码时、CDN 节点回源连接出错或者响应体超时时，都会触发离线模式；
 - 离线模式的时间周期为资源内容在 CDN 节点的缓存过期时间，在该期间内不再回源请求资源内容；
 - 离线模式只有资源内容在 CDN 节点缓存的情况下才会凑效，否则也会出现资源文件访问失败。

 
###2.5 操作员授权

> 功能说明

针对具备存储特性的 CDN 服务，可以创建操作员，并授权某些操作员对在又拍对象存储服务上面的非结构化数据进行读取、写入、删除等操作。功能说明如下：

 - 可读取：拥有下载文件、获取文件、目录信息及空间使用情况的权限；
 - 可写入：拥有上传文件、创建目录、操作缓存刷新的权限；
 - 可删除：拥有删除文件、删除目录的权限。

> 注意：针对使用自主源的 CDN 服务，只有当开启【源站资源迁移】功能时，才会使用到操作员授权功能。


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入「功能配置」和「回源管理」配置界面，找到「操作员授权」配置项，如下截图所示：

第一步：依次进入配置管理、回源管理页面

> 进入【功能配置】页面

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config.png" height="470" width="800" />

> 进入【回源管理】配置页面

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source.png" height="470" width="800" />

第二步：在【回源管理】模块，找到【操作员授权】配置项

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-operator.png" height="470" width="800" />

> 未创建操作员，需要创建操作员并授权

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-operator1.png" height="470" width="800" />

填写基本信息并选择操作权限，点击【创建并授权】按钮，即可创建操作员并对服务进行授权。

> 已创建操作员，需要对操作员进行授权

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-operator2.png" height="470" width="800" />

在操作员可选列表中，根据业务要求，选择已经创建好的操作员即可，点击【授权】按钮，即可授权。

**注意事项**

 - 操作员权限具备可读取、可写入、可删除等功能，请谨慎分配权限；
 - 操作员账号和密码请谨慎保管，一旦泄露，请及时更换。
 

----------
 
##3.缓存配置

----------

###3.1 缓存过期配置

> 功能说明

缓存配置是指又拍云 CDN 加速节点（包括边缘节点和中心节点）在缓存您的资源文件时遵循的一套过期规则。当资源文件处于过期状态时，此时用户请求会由节点发送至源站，重新获取资源内容并缓存至节点，同时返回给最终用户；当资源内容处于未过期状态时，您的用户请求到达节点后，会由节点直接响应最终用户的请求。合理的配置资源文件缓存时间，能够有效的提升缓存命中率，降低回源率，节省您的源站带宽。

**配置引导**

登录 [CDN控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入：服务管理 > 功能配置 > 缓存配置 > 缓存过期配置，点击【管理】按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-management.png" height="470" width="800" />

**自定义缓存配置**

您可以根据自身业务需求，在配置界面的右上角，点击右上角的【添加】 按钮进行资源文件缓存过期时间的配置，如截图所示：

> 进入缓存过期管理配置界面

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-management1.png" height="470" width="800" />

> 进入自定义缓存过期配置界面

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-management2.png" height="470" width="800" />

在该配置界面，您可以针对模板、缓存时间、资源路径、状态码等进行选择和配置。CDN 支持三种维度的缓存过期规则配置，分别为：

**1.根据文件类型设置缓存过期策略**

您可以根据模板填充文件类型后缀，根据文件类型来设置缓存时间，如下示例所示：

> /*.(txt,doc,wri,docs)   缓存时间 1 天；

设置完成后，域名下所有匹配 `.txt .doc .wri .docs` 的文本资源，在节点的缓存有效时间均为 1 天。

**2.根据文件目录设置缓存过期策略**

您可以自定义配置文件路径，根据文件路径来设置缓存时间，如下示例所示：

> /dir/*   缓存时间 12 小时

**3.全局缓存过期策略配置**

全局缓存策略适合源站全是静态资源的客户，可以针对该服务一键开启配置缓存过期时间，开启请谨慎。详细配置可参照如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-global.png" height="490" width="800" />

设置完成后，该服务下所有域名匹配的资源，在节点的缓存有效时间均为 7 天。

注意事项：

 - 全局缓存在自定义缓存配置里面的优先级是最低的，且优先级不可调节，如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cache-priority.png" height="470" width="800" />

 - 全局缓存设置有一个单独的开关，需要开启之后，才可以进行配置。

> 默认配置模板

特别地，我们针对自定义缓存配置，默认规划了模板，并且预设了缓存时间，您可以根据具体情况进行修改。具体模板如下：

    首页：/  缓存 12 小时；
    目录页：/*/    缓存 12 小时；
    图片文件：/*.(jpg,jpeg,png,bmp,gif,psd,ico,tga,imb,tiff)      缓存 7 天；
    文本文件：/*.(txt,doc,wri,docs,css,js,dot,xml,log,bat,csv)    缓存 1 天；
    音视频文件：/*.(wav,mp3,wmv,rmi,aac,mp4,rmvb,mkv,avi,flv,swf,rm,mov,movie,qt)  缓存  7 天；
    下载类文件：/*.(exe,ios,apk,ipa,pxl,sis,cab,deb,rar,zip,gzip,tar,7z,bzip2,dmg,gz,wim,tbz,tpz,z,jar)      缓存 7 天；

**4.URI 带参数配置缓存过期策略**

除了按照文件类型（文件扩展名）、文件目录以及全局缓存来配置缓存过期时间，您还可以根据 `URI` 带参数来配置缓存过期时间，具体格式如下：

> /dir/*?foo&bar=123      缓存时间 `1` 小时

以上规则中，`?`后面跟随的就是参数筛选规则；`*`特指通配符，如不需配置通配符，可以使用具体资源路径，如下规则所示：

> /dir/index.html?foo&bar=123     缓存时间 `1` 小时

关于 `?`后面的参数筛选规则配置请参考如下分类：

| 分类 | 说明      | 示例 |
|-----|--------------|--------------|
|   只有 `KEY` 没有 `VALUE`   | 匹配该参数 `KEY` 所有对应的值以及值为空这种特殊的情况    | `/dir/*?bar&foo`   | 
|   既有 `KEY` 也有 `VALUE`（格式：K=V）   |  会精确匹配 `KEY` 和 `VALUE`   | `/dir/*?foo=123&bar=456`   | 
|  只有 `？`，不带任何参数  |  表示参数为空  | `/dir/*?`  | 
|  不带 `？`  |  表示不验证参数  | `/dir/*` | 


注意：

* 参数规则配置顺序不敏感，参数之间通过 `&` 进行连接；
* URI 仅限 PATH（路径）以及 ARGS（参数）部分，例如：`/m/n/*?bar&foo=123`。

**不缓存配置**

根据业务情况，可以针对一些动态资源文件（ 例如：php、jsp、asp、do、cgi、action、json 等）强制配置不缓存， 详细配置可参照如下所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-no-cache.png" height="470" width="800" />

> /*.(asp,php,jsp,do,json)     配置不缓存

**默认缓存策略**

在没有匹配到自定义缓存规则且源站也没有返回任何有效缓存头的情况下，我们的默认配置策略如下：

1.针对静态资源，所有正常状态码（大于等于 200 小于 400）均缓存 8  天。特别地，301 响应缓存 2 小时，302 响应缓存 20 分钟

2.针对动态资源，程序会自动识别，则不进行缓存

3.对于其他大于等于 400 的不正常响应，则不进行缓存
 
**缓存优先级**

请根据具体缓存优先级情况合理配置缓存策略，又拍云 CDN 加速平台目前的缓存优先级如下：

> 不缓存配置（后台设置） > 自定义缓存配置（后台配置） > 源站缓存配置 > 默认缓存策略。

注意事项：

 - 源站缓存配置，指 Cache-Control 和 Expires 请求头的设置
 - 以上缓存配置优先级目前不支持顺序调节，后续我们会考虑支持

**特别声明**

针对使用又拍云存储服务的客户，我们暂时不支持缓存配置的自定义设置，我们默认给出了一个默认的缓存配置规则，具体如下：

> 对于正常的状态码（大于等于 200 小于 400）：8 天

> 对于其他大于等于 400 的不正常响应缓存 5 分钟

**动静自动分离**

针对使用 `自有源` 的 CDN 服务而言，当没有匹配到自定义缓存规则且源站也没有返回任何有效的缓存头时，我们就会对回源内容进行动静内容识别。特别地，我们会根据响应头中的 ` Content-Type` 字段和 URL 中的文件后缀名来判断是否是静态内容，其中 `Content-Type` 匹配优先级高于文件后缀名。

*参考列表*

以下是我们维护的一份 `Content-Type` 和文件后缀名列表，若满足匹配我们就认为是静态内容：

> Content-Type 列表

```
text/css
text/javascript
application/javascript
application/x-javascript

image/gif
image/png
image/jpeg
image/webp
image/tiff
image/x-icon
image/x-ms-bmp
image/svg+xml

application/font-woff
application/java-archive
application/msword
application/pdf
application/postscript
application/zip
application/octet-stream

application/vnd.openxmlformats-officedocument.wordprocessingml.document
application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
application/vnd.openxmlformats-officedocument.presentationml.presentation

audio/mpeg
audio/ogg
audio/x-m4a

video/mp4
video/x-flv
video/x-m4v
```

> 文件后缀名列表

|css |js |jpg |jpeg |gif |ico |png |bmp |pict |csv |
|-|-|-|-|-|-|-|-|-|-|
|doc |pdf |pls |ppt |tif |tiff |eps |ejs |swf |midi |
|mida |ttf |eot |woff |otf |svg |svgz |webp |docx |xlsx |
|xls |pptx |ps |class |jar |bz2 |bzip |exe |flv |gzip |
|rar |rtf |tgz |gz |txt |zip |mp3 |mp4 |ogg |m4a |
|m4v | apk | | | | | | | | |

----------

###3.2 参数跟随

> 功能说明

CDN 提供参数过滤配置等选项，您可以根据实际业务情况，控制是否对访问或者回源 URL 中的带 “？” 后的参数进行过滤操作。参数跟随功能包括三种模式，分别是 `参数不跟随` 、`全程跟随` 、`回源跟随` ，系统默认为 `全程跟随` 模式。一般情况下，根据业务情况，建议选择 `参数不跟随` 来提高资源文件缓存命中率。详细介绍如下：

**参数不跟随**

> 说明：开启后，系统会过滤请求中的查询字符串，并且查询字符串不会传递给源站

> 示例：（访问）/foo.js?a=1&b=2 => （缓存）/foo.js => （回源）/foo.js

**全程跟随**

> 说明：开启后，将不再过滤请求中的查询字符串，并将查询字符串传递给源站

> 示例：（访问）/foo.js?a=1&b=2 => （缓存）/foo.js?a=1&b=2 => （回源）/foo.js?a=1&b=2

**回源跟随**

> 说明：开启后，会过滤请求中的查询字符串，在访问回源阶段会将查询字符串传递给源站

> 示例：（访问）/foo.js?a=1&b=2 => （缓存）/foo.js => （回源）/foo.js?a=1&b=2

**配置引导**

登录 [CDN控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入：服务管理 > 功能配置 > 缓存配置 > 参数跟随，点击【管理】按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-performance-param-management.png" height="470" width="800" />

如上图所示，用户可以根据业务情况来合理选项。

注意：参数跟随功能，可以配合缓存过期配置功能来使用。

----------

###3.3 参数顺序识别

> 功能说明

通常 CDN 节点是以整条 URL 为粒度对文件进行缓存的，然而 URL 中参数的不同排列组合使得 CDN 节点会将同一份文件缓存多份，开启参数顺序识别功能之后，可以使得资源文件只缓存一份，从而提高资源文件的缓存命中率。


> 实现原理

例如下方两条 URL，对应的资源文件是相同的，CDN 节点是分开缓存的：

```
https://www.upyun.com/test.jpg?a=1&b=2&c=3
https://www.upyun.com/test.jpg?c=3&b=2&a=1

```

功能开启后，CDN 节点收到用户请求，首先对 URL 中的参数按特有的规则进行排序，然后再查找匹配节点缓存或直接回源获取文件，也就是说下方这些 URL：

```
https://www.upyun.com/test.jpg?a=1&b=2&c=3
https://www.upyun.com/test.jpg?a=1&c=3&b=2
https://www.upyun.com/test.jpg?b=2&a=1&c=3
https://www.upyun.com/test.jpg?b=2&c=3&a=1
https://www.upyun.com/test.jpg?c=3&a=1&b=2
https://www.upyun.com/test.jpg?c=3&b=2&a=1

```

最后的结果是，CDN 节点将只缓存一份：

```
https://www.upyun.com/test.jpg?a=1&b=2&c=3
```

**配置引导**

登录 [CDN控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入：服务管理 > 功能配置 > 缓存配置 > 参数顺序识别，滑动右边的按钮即可开启。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-source-canshushibie.png" height="470" width="800" />

如上图所示，点击【确定】即可开启。

**注意事项**

 - 开启此功能后，因 CDN 节点会将用户访问 URL
   中的参数重新排序后进行缓存匹配，所以将有可能无法匹配之前已缓存的文件，导致回源带宽增大，所以请谨慎开启，建议在刚创建新服务时开启；

 - 该特性依赖于参数跟随特性，也即过滤参数在关闭的状态下才会有效。

----------

###3.4 分段缓存

> 功能说明

分段缓存功能使得缓存的基本单位变成了块，并且在缓存块大小以及回源请求大小上可以采用不同粒度进行控制，最终实现按块缓存，按需回源。

开启该功能可有效降低大文件回源率，也可以提高文件在 CDN 节点的缓存命中率，同时避免了拉取完整文件导致的源站带宽浪费。特别地，开启该特性之后，CDN 节点会以 Range 请求回源。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 缓存配置 > 分段缓存，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-slice-cache.png" height="470" width="800" />

默认配置：该特性会默认开启，您可以有选择性的关闭该特性。特别地，我们针对分段缓存预置了文件后缀模板，默认预置后缀如下：

```
视频：AVI、MP4、FLV、MOV、3GP、ASF、WMV、MPG、F4V、M4V、MKV、VOB、M3U8、TS 、RMVB、DAT、RM
音频：MP3、OGG、M4A、WMA、AIF、AU
安装包：APK、ZIP、EXE、RAR、IPA、PXL、DEB、SIS、SISX、JAR、XAP
游戏下载：SO、CUE、CCD、MDS、IMG、7Z
其它：CAB、DHP、GZ、ISO、MPQ、PBCV、PXL、QNP、R00、XY、XY2
```

追加自定义：为了方便灵活配置，也即可以根据文件后缀或者通配符规则来配置是否开启分段缓存功能，如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-fenduan2.png" height="470" width="800" />

在该配置界面，您可以填写资源路径，然后点击【添加】按钮，保存即可。路径规则支持 * 通配符，例如：

```
/a/*.mp4 
/b/*.apk
/*
/123/*
```

**配置效果**

假设最终用户发起了资源请求，请求的 URL 为：`http://www.example.com/download/game.zip`，CDN 节点收到请求后，若命中则响应对应分段文件给最终用户，未命中的分段 CDN 节点则回源发起 Range 请求获取资源文件。

1.开启分段缓存

 - 若最终用户发起资源请求，CDN 节点上已经命中该分段，则直接响应给最终用户
 - 若 CDN 节点未命中缓存，则 CDN 节点回源使用 Range 请求，分段获取资源文件

2.关闭分段缓存

 - 节点文件缓存过期之后，会向源站获取整个资源文件

3.分段缓存预加载

 - 开启分段缓存功能之后，该特性会默认开启。当最终用户请求第一个分段文件时，CDN 会提前去下载后面几个分段文件，进而提高文件下载速度。

**注意事项**

 - 源站需要支持 Range 请求，否则会导致回源失败；严格来说，我们只通过 `Accept-Ranges: bytes` 这个头来判断源支不支持 Range 请求 ，否则即使开启了分段缓存特性，也不会生效；
 - 该功能配置后不会立即生效，需在文件缓存过期或者文件缓存被主动刷新之后方可生效
 - 该功能仅针对静态文件，动态内容开启分段缓存会可能会发生错误，请谨慎使用；
 - 该功能开启后，资源在 CDN 节点上会进行分段缓存，如果要立马关闭该特性，可通过手动刷新文件来解决；
 - 如果您已经使用又拍云对象存储服务，该特性已经默认开启，无需做任何配置；

----------

###3.5 浏览器缓存

> 功能说明

浏览器缓存用于设置资源文件在客户端（浏览器）的缓存过期时间设置，可自定义需要在浏览器进行特殊缓存的资源路径及缓存时间。资源路径的设置支持按文件路径和文件扩展名设置。

**配置引导**

登录 [CDN控制台](https://console.upyun.com/login/)，选择需要配置的服务，依次进入：服务管理 > 功能配置 > 缓存配置 > 【浏览器缓存】，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-browser.png" height="470" width="800" />

点击界面右边的【管理】按钮即可开始配置：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-browser3.png" height="470" width="800" />

在此配置界面，可以对已配置的缓存策略进行管理操作，也可以点击右上角的【添加配置】按钮，新增配置策略，如图：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-cache-browser4.png" height="470" width="800" />

根据自身要求，添加资源路径以及缓存时间，点击【保存】按钮即可，其中资源路径支持 * 通配符，例如：

```
/a/*.mp4 
/b/*.apk
/*
/123/*
```

也可以为文件后缀格式，例如：

```
/*.(txt,doc,wri,docs,css,js,dot,xml,log,bat,csv)
/*.(wav,mp3,wmv,rmi,aac,mp4,rmvb,mkv,avi,flv,swf,rm,mov,movie,qt)
```

**注意事项**

 - 如果设置的浏览器缓存时间大于文件在 CDN 节点上的缓存过期时间，则此处设置的浏览器缓存过期时间会失效，仍以 CDN
   节点设置的缓存过期时间为准；
 - 浏览器缓存设置默认匹配的状态码范围为：大于等于 200 小于 400，该项配置不会影响 CDN 缓存服务器的行为。

----------

##4. 性能优化

----------

###4.1 WebP 自适应

> 功能说明

WebP 自适应功能是 CDN 平台智能判断客户端浏览器是否支持 WebP 解码，如支持则返回 WebP 格式图片，否则返回原图，客户端以及源站无需任何改动。开启该特性，可以有效节省 CDN 传输带宽，加速图片渲染速度。

> 如何实现？

WebP 自适应大致的处理流程如下：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-auto_webp_theory.png"  />

1.CDN 如何判断客户端是否支持 WebP ?

CDN 是通过 HTTP Accept 头来判断的，如果支持，则返回 WebP 副本并进行缓存；如果不支持，则返回原图。以 Chrome 浏览器为例：

```
accept:image/webp,image/apng,image/*,*/*;q=0.8

```
如上列表可以看出，当 Accept 请求头里面如果有 `image/webp` 字段，则说明客户端支持 WebP 解码。

2.CDN 如何实现实时图片格式转换？
 
针对用户源站并非 WebP 格式图片的时，CDN 层需要支持将原图图片的实时转换为 WebP 格式副本，这个在 CDN 层面是无缝支持的。流程是这样的：

 - 客户端浏览器请求一个图片资源，例如：http://webp.example.com/test.png;
 - CDN 通过 Accept 头已经判断客户端浏览器支持 WebP 格式的图片；
 - CDN 回用户源站取回原图并将原图实时转为 WebP 格式的图片，并响应给客户端浏览器。

这里值得强调的是，又拍云 CDN 已经无缝兼容了各种作图场景和访问方式，具体包括：

 - 原图访问 ，示例：/a.jpg
 - 自定义版本号作图，示例：/a.jpg!123
 - URL 作图 ，示例：/a.jpg!/format/webp
 - 自定义版本号 + URL 作图：/a.jpg!123/format/webp

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > WebP 自适应，开启即可。如下图所示：：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-auto_webp.png" height="470" width="800" />

在配置界面，滑动右边的按钮，在确定对话框下，点击【确定】按钮即可一键开启。

**注意事项**

 - CDN 平台会根据请求头 Accept: image/webp 来智能判断客户端是否支持 WebP 解码，不支持的客户端，会默认返回原始图片，可以无缝切换；
 - 已经使用了又拍云自定义版本作图、URL 作图、自定义版本作图＋URL 作图的请求也无缝支持该特性；
 - 在 CDN 节点未缓存 WebP 副本的情况下，CDN 平台会实时生成 WebP 副本并返回。

更多了解，请参照：[如何通过 WebP 自适应方案减少图片资源大小](https://blog.upyun.com/?p=1539)，[移动端如何使用 WebP（上）](https://blog.upyun.com/?p=1252)、[移动端如何使用 WebP（下）](https://blog.upyun.com/?p=1257)。

----------


###4.2 H.265 自适应

> 功能说明

H.265 自适应功能是 CDN 平台智能判断客户端是否支持 H.265 解码，如支持则返回 H.265 格式视频，否则返回原始视频。通过部署 H.265  视频，可提升终端用户观看体验，减少网络传输带宽。

> 如何实现？

H.265 自适应大致的处理流程如下：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-auto-h2652.png"  />

1.客户端发起 H.265 视频资源请求，例如：http://h265.example.com/test.mp4；

2.CDN 节点判断是否有 H.265 缓存副本，如有则直接响应给客户端；

3.节点如果没有缓存副本，则回源站获取资源，如有，则返回 H.265 视频资源并在 CDN 节点缓存；

4.如果没有，则返回原始视频资源，并缓存 6 分钟，缓存过期之后会回源探测。

> CDN 如何判断客户端是否支持 H.265 解码 ?

CDN 当前识别客户端是否支持 H.265 解码是通过 HTTP 请求头：Accept-Encoding: h265 来判断的，CDN 收到该请求之后，再去判断 CDN 节点是否有 H.265 缓存副本，否则直接返回原始视频资源。请求识别流程如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-auto-h265.png" />

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > H.265 自适应，开启即可。如下图所示：：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-auto-h2653.png" height="470" width="800" />

在配置界面，滑动右边的按钮，在确定对话框下，点击【确定】按钮即可一键开启。

**注意事项**

 - 客户端需要根据约定规范发起 Accept-Encoding: h265 的请求头，CDN 节点会根据此请求头来判断客户端是否支持 H.265 解码；

 - CDN 开启 H.265 自适应之后，是禁止客户端缓存的（以避免外部的代理服务器缓存内容，从而导致后面其他不支持 H.265 格式的播放器下载到 H.265 格式的资源）；

 - 源站为了区分原始视频和 H.265 格式视频，H.265 视频存储路径需要约定规范为：{URL}.h265， 以此来区分原始视频和 H.265 视频。也即在 CDN 节点未缓存 H.265 视频副本的情况下，CDN 回源会带着相应的后缀回源获取 H.265 格式副本文件；

 - 在 CDN 回源未获取到 H.265 视频副本时，会自动请求原始视频文件，并且在 CDN 节点缓存较短的时间，默认为 6 分钟。

----------

###4.3 码率适配限速

**功能说明**

又拍云 CDN 平台支持视频文件的码率适配限速功能，通过自动分析当前视频文件传输的码率，将视频文件的传输速度控制在视频码率范围之内，可以在一定程度上防止高峰期时带宽占用，节省成本。限速举例：一开始不限速，当播放时长超过  3 秒时，进行 1.1 倍码率限速。该功能可支持的配置包括：

* 资源路径：支持 * 作为通配符，如：`/a/*.mp4`、`/*.mp4`；
* 限速策略：指定到达设置的时间之后开始限制视频文件的下载速度，支持的单位包括秒、分钟，推荐值为 3 秒；
* 限速因子：指定下载速度为视频码率的多少倍，推荐值为 1.1；


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > 码率适配限速，点击【管理】按钮，进入功能配置界面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-bit_rate_auto.png" height="400" width="800" />

**1) 添加规则**

依次进行如下操作：

* 滑动开关：将配置界面左上角的开关由关闭状态调整为开启状态；
* 添加规则：点击配置界面右上角的【添加规则】按钮即可添加适配限速规则，配置界面如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-bit_rate_auto_add.png" height="400" width="800" />

在配置界面，依次需要进行资源路径、限速策略、限速因子的配置。

* 资源路径：也即对请求的 URI 进行匹配，匹配成功则进入限速逻辑，支持 `*` 通配符，例如：`/mnt/download/*.mp4`
* 限速策略：表示从某个时刻（下载时间，不是播放时间）开始执行限速策略，支持秒和分钟配置，默认推荐 3 秒；
* 限速因子：用来控制 mp4 文件下载限速的因子，指定下载速度为视频码率的多少倍，推荐值为 1.1；


举例：

假如某视频码率为 1337 Kbps，则视频的下载速度为 :


```
(1337/8)*1.1 = 183 KB/s

```

配置完所有配置项之后，点击左下角的【保存】按钮即可。

**2) 编辑、删除规则**

如下图所示，可以在操作区域点击【编辑】、【删除】按钮即可编辑和删除规则。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-bit-rate-auto-del.png" height="400" width="800" />

**注意事项**

* 当前仅支持 mp4 文件格式视频文件的码率自适应；
* 需要确保 mp4 文件的 moov（ Movie box ）在视频文件的头部，否则会导致自动限速功能失效，一般情况下，moov 会紧随 ftyp（File type box） 出现；
* 视频文件的元数据（ metadata ）过大会导致限速失败，基于用户体验考虑，当前设置的阀值是 512 KB；
* 当配置的限速策略为某时间时（例如：3 秒），则在 3 秒之后不会立马把下载速度降为期望的下载速度，如果从限速开始到到目前为止有发送多余的数据，则某一段时间内会停止发送数据，直到最终的下载速度保持在设定的范围之内。

----------

###4.4 页面压缩

> 功能说明

开启后，会自动去除 HTML 文件中非必要的字符（空白、注释等），自动压缩文件大小，提升访问速度。


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > 页面压缩，开启开关即可，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-page-gzip.png" height="470" width="800" />

在配置界面，滑动右边的按钮，在确定对话框下，点击【确定】按钮即可一键开启。

**注意事项**

特别地，目前仅支持 HTML 页面的优化。

----------

###4.5 HTTP 302 调度

> 功能说明

配合 DNS 调度，基于内容和精准 IP 的精确调度方案，该调度模块可以获取最终用户真实的 IP 地址和内容地址，使用 HTTP 协议里面的 302 跳转功能，分配最优的 CDN 边缘加速节点给最终用户。


> 实现机制

经过 `HTTP 302 调度`之后的资源请求地址的变化参照如下示例：

原始访问地址：

```
http://test.example.com/image/1.png

```
经过 HTTP 302 调度后的 Location 地址：

```
http://60.211.204.130/test.example.com/image/1.png?_marco_sum=XXX&_marco_id=XXX&_marco_from=58.222.18.5

```
其中，`Location` 地址里面的参数 `macro_from` 的值是第一次 DNS 调度解析到的 CDN 边缘节点 IP 地址，`60.211.204.130` 是经过 HTTP 302 调度之后最优的 CDN 边缘加速节点 IP。


**配置引导**


登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 > 功能配置 > 性能优化 > HTTP 302 调度，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-http-302.png" height="470" width="800" />

默认配置：该特性会默认开启，您可以有选择性的关闭该特性。特别地，我们针对 HTTP 302 调度功能预置了文件后缀模板，默认预置后缀如下：

```
视频：AVI、MP4、FLV、MOV、3GP、ASF、WMV、MPG、F4V、M4V、MKV、VOB、M3U8、TS 、RMVB、DAT、RM
音频：MP3、OGG、M4A、WMA、AIF、AU
安装包：APK、ZIP、EXE、RAR、IPA、PXL、DEB、SIS、SISX、JAR、XAP
游戏下载：SO、CUE、CCD、MDS、IMG、7Z
其它：CAB、DHP、GZ、ISO、MPQ、PBCV、PXL、QNP、R00、XY、XY2
```

追加自定义：为了方便灵活配置，也即可以根据文件后缀或者通配符规则来配置是否开启该功能，如截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-http-302-2.png" height="470" width="800" />

在该配置界面，您可以填写资源路径，然后点击【添加】按钮，保存即可。路径规则支持 * 通配符，例如：

```
/a/*.mp4 
/b/*.apk
/*
/123/*
```

**注意事项**

 - 如果该服务下的域名多为小文件加速场景，为了避免 302 跳转造成的额外消耗，不宜开启该功能；
 
 - 使用该功能，需要确认客户端或者播放器是否支持 302 跳转；
 
 - HTTP 302 调度会使得 URL 的地址发生变化，某些应用对 URL 的变化敏感，请谨慎使用。
 

----------

###4.6 重定向跟随


> 功能说明

当 CDN 节点回源站时，若源站响应的状态码为 301/302，CDN 节点对重定向之后的目标 URL （也即 301/302 响应头 `Location` 字段对应的信息）发起请求，将获取后的内容响应给最终用户，并在 CDN 节点进行缓存。这样向最终用户屏蔽了重定向过程，免去了最终用户再次向重定向后的 URL 重新发起请求的连接时间，从而加快了访问速度。

> 原理介绍

一般情况下，当 CDN 节点回源站请求文件时，若源站返回的是 HTTP 301/302 的响应，CDN 节点会将该响应返回给客户端，客户端根据响应头中的 `Location` 头信息进行再次请求。配置了【重定向跟随】功能之后，访问流程如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-302redirect-yuanli.png"  />

1）客户端向 CDN 节点发起请求并传递至源站，例如：`http://example.com/123.html`；

2）源站给 CDN 节点响应 301/302 状态码，并指明重定向后的 URL 为 `http://example.com/456.html`

3）CDN 节点对重定向之后对目标 URL 发起请求；

4）源站响应内容给最终用户，并将内容在 CDN 节点进行缓存；

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > 重定向跟随，滑动开关即可开启该功能。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-302redirect.png" height="470" width="800" />


在配置界面，滑动右边的按钮，在确定对话框下，点击【确定】按钮即可一键开启。

**注意事项**

 - 开启重定向跟随后，最多仅跟随 3 跳，超出限制则会直接返回 301/302 给最终用户；
 
 - 如重定向后的 Location 地址也在又拍云 CDN 加速，我们默认会允许一次访问，超过一次，则会直接响应 403，请谨慎使用。

----------
 
###4.7 视频拖拉

> 功能说明

视频拖拉功能用于视频点播的增强体验，方便最终用户通过拖拉播放器的进度条，观看任意起始位置的视频，而无需从头观看。该功能还可以扩展为跳过片头、续播、预播放等功能。发送拖拉播放进度时，客户端发起的请求类似 `http://video.upyun.com/test.mp4?start=100` 这样的 URL 请求。

目前仅支持 MP4 和 FLV 格式的文件按视频头拖拉，并且 FLV 支持按字节拖拉，MP4 按照时间拖拉。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > 视频拖拉，点击【管理】按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-video-drag.png" height="470" width="800" />

详细配置请参见 [「支持从任意起点观看」视频拖拉用户指南](https://blog.upyun.com/?p=873)

**注意事项**

 - 播放器可以发起约定格式的请求，例如：http://example.com/video/test.mp4?start=xxx&end=xxx；
 
 - 文件需含有关键帧，这是实现拖拉的前提条件；关键帧太少或者没有关键帧，视频文件需要重新编码；
 
 - 视频文件 metedata 中缺少关键帧的索引信息，如 flv 缺少 keyframes 信息，mp4 缺少 seekpoints
   信息，或缺失 moov box，都会导致拖拉失败；

 - 源站需要支持 range 请求，也即针对客户端发起的 range 请求，源站可以正常响应 206 文件分片；

 - 支持根据不同资源路径添加配置，最多仅支持 20 条。

----------

###4.8 海外加速

> 功能说明

根据服务下具体业务情况，可以有选择性的关闭和开启海外加速，默认为开启状态。海外地区特指除中国大陆以外的地区，包括港澳台地区。

开启状态下，海外请求会默认解析到又拍云海外 CDN 节点，计费按海外地区定价计算；

关闭状态下，海外请求会默认解析到又拍云国内 CDN 节点，计费按国内地区定价计算。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 性能优化 > 海外加速，滑动开关即可开启该功能。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/performance/upyun-cdn-config-performance-oversea-accelerate.png
" height="470" width="800" />

在配置界面，滑动右边的按钮，在确定对话框下，点击【确定】按钮即可一键开启。


**注意事项**

关闭状态下，海外请求会解析到国内 CDN 节点，可能会影响到海外地区终端用户的访问体验。


###4.9 Gzip 压缩

> 功能说明

又拍云 CDN 默认开启了 Gzip 压缩的相关特性，具体对应的 NGINX 配置如下：

```
gzip on;
gzip_min_length 256;
gzip_types <见下面的列表>;
gzip_disable "MSIE [1-6]\.";
gzip_vary on;
```

触发实际的 GZIP 压缩行为需要同时满足如下条件：

1、Content-Type 满足以下列表其中之一：

```
text/plain
text/javascript
text/css
text/xml
text/x-component
application/javascript
application/x-javascript
application/xml
application/json
application/xhtml+xml
application/rss+xml
application/atom+xml
application/x-font-ttf
application/vnd.ms-fontobject
image/svg+xml
image/x-icon
font/opentype

text/html -- default
```
2、Content-Length 大于 256 字节；

3、客户端请求头带了 `Accept-Encoding: gzip`。

###4.10 文件合并

 `又拍云存储 ` 和  `自主源站 ` CDN 加速服务均支持 JS，CSS Combo（组合特性），即可以把多个 JS, CSS 请求合并成一个。
 

**如何使用**

> 特别地，我们以 `!!` 作为分隔符。

    http://upyun-assets.b0.upaiyun.com/foo/!!a.js,b.js

一个引用了如上 URL 的 Demo:

    http://upyun-assets.b0.upaiyun.com/jscombo.html

----------

##5.HTTPS 

###5.1 HTTPS 配置

> 功能说明

又拍云 HTTPS 加速服务建立在自主研发的内容分发网络基础之上，全面支持了 HTTPS 协议加速。通过会话重用及会话保持、在线证书状态检查、协议优化、SSL 协议握手优化、HTTP/2 等技术手段，全面提升了 HTTPS 协议加速性能。

**配置引导**

第一步：依次进入：服务管理 > 功能配置 > HTTPS, 点击 `管理` 即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https1.png" height="470" width="800" />

第二步：可针对单个域名选择相应的证书，然后保存。如果无证书可选，可前往[ SSL 证书服务](https://console.upyun.com/toolbox/ssl/)平台添加域名的自有证书；用户也可直接在[ SSL 证书申购](https://console.upyun.com/toolbox/createCertificate/)平台申购 Symantec、GeoTrust、TrustAsia、Let’s Encrypt 的各类 SSL 证书，其中 TrustAsia、Let’s Encrypt 的 DV SSL 单域名证书可免费申请。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https2.png" height="470" width="800" />

第三步：可选择是否开启 HTTPS 访问和强制 HTTPS 功能。在开启 HTTPS 访问后，可通过点击 `修改` 切换域名所对应的证书，点击 `详情` 可查看相应证书的详细信息；

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https3.png" height="470" width="800" />

“ HTTPS 访问”：开启该功能后，客户端才能使用 HTTPS 协议进行访问，默认关闭。

“强制 HTTPS ”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

备注：其中 `HTTPS 配置` 也可以在 `SSL 证书管理`里面配置，参见下文`SSL 证书管理`部分。

**自有证书添加**

如果域名当前已有证书，又拍云提供了自有证书的上传方式，无需人工协助，安全便捷。

第一步：进入 SSL 证书服务，工具箱 -> SSL 证书服务 -> 证书管理 -> 添加自有证书 

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl8.png" height="470" width="800" />

第二步：粘贴证书内容:

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl9.png" height="470" width="800" />

点击 `保存` 之后，可查看 SSL 证书信息如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl10.png" height="470" width="800" />

**SSL 证书管理**

在[证书管理](https://console.upyun.com/toolbox/ssl/)列表中，可根据需求选择申购成功并已部署于 CDN 平台的证书或已上传的自有证书，点击 `HTTPS 配置`进行操作 ，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/ssl/upyun-cdn-ssl11.png" height="470" width="800" />

“HTTPS 访问”：开启该功能后，客户端使用 HTTPS 协议访问时才能成功，默认关闭。

“强制 HTTPS”：勾选此功能后，又拍云边缘节点收到用户的 HTTP 请求后会将其强制跳转到 HTTPS 进行访问。默认：兼容用户的 HTTP 和 HTTPS 请求。

###5.2 HSTS 配置

> 功能说明

HSTS（HTTP Strict Transport Security，HTTP 严格传输安全)，是一套由互联网工程任务组发布的互联网安全策略机制。网站可以通过配置 HSTS，来强制浏览器使用 HTTPS 与网站通信，保障网站更加安全。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > HTTPS, 点击 `管理` 按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-https-hsts.png" height="470" width="800" />

进入规则管理页面，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-https-hsts2.png" height="470" width="800" />

点击【添加规则】按钮，即可开始配置规则：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-https-hsts3.png" height="470" width="800" />

根据要求填写好配置规则，点击【保持】按钮即可。更多关于 HSTS 配置项信息，请参见 [HSTS 助力网站更安全](https://blog.upyun.com/?p=1583) 。

###5.3 HTTP/2 配置

**功能说明**


HTTP/2 即超文本传输协议 2.0，是下一代 HTTP 协议，在 CDN 控制台您可以灵活进行 HTTP/2 特性的开启和关闭。

又拍云 CDN 服务当前已全平台支持 HTTP/2。HTTP/2 是在 HTTPS 协议的基础上实现的，只要使用又拍云 HTTPS 加速服务的域名，都可免费享受 HTTP/2 服务。又拍云控制台关于 HTTP/2 的配置支持三种模式的设置，分别是：

* 开启 HTTP/2 

默认开启 HTTP/2 特性，包括采用二进制格式传输数据、对消息头采用 HPACK 进行压缩传输、多路复用 等特性，但不包括 Server Push 特性。

* 关闭 HTTP/2 

如需关闭 HTTP/2 特性，可以选择关闭，请根据要求合理关闭。

* 开启 HTTP/2 + Server Push 

该模式下，支持 HTTP/2 的所有特性，其中 Server Push 特性需要根据业务需求合理配置。

特别强调的是，Server Push 是 HTTP/2 规范中引入的一种新技术，也即服务端在没有被客户端明确的询问下，抢先的 “推送” 一些网站资源给客户端。该特性只要被正确的使用，可以达到很好的页面访问效果。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > HTTPS > HTTP/2 配置，点击【管理】按钮即可开始配置。如下图所示：

1）开启 HTTP/2 

默认已经开启，如截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-open-http2.png" height="400" width="800" />

注意：当且仅当开启 HTTPS 加速时，HTTP/2 特性才会生效。

2）关闭 HTTP/2 

如需关闭 HTTP/2 特性，可以在控制台关闭，点击【关闭 HTTP/2】按钮，然后点击【确定】即可保存配置。如截图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-close-http2.png" height="400" width="800" />

3）开启 HTTP/2 + Server Push 

如下截图所示，点击【 开启 HTTP/2 + Server Push 】按钮，配置匹配路径以及推送资源，然后点击【确定】即可保存配置。


<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-http2-server-push.png" height="400" width="800" />


其中，在配置项里面，【匹配路径】是必填项，【推送资源】是非必填项。

匹配路径：也即 Server Push 特性仅针对匹配的 URI  生效，示例为：

```
/index.html
/admin.html

```

推送资源：表示匹配 URI 后将要推送的资源的 PATH（不包括 HOST 部分），示例为：


```
/style/index.css
/dyn/index.js

```

需要注意的是当源站有设置 [Link 首部](https://www.w3.org/TR/2017/CR-preload-20171026/#server-push-http-2)时，即使【推送资源】在 CDN 端没有进行自定义设置，也会生效，其中 Link 首部的示例为：

```
Link: </css/styles.css>; rel=preload; as=style

```


**注意事项**

* HTTP/2 特性是在 HTTPS 协议的基础上实现，需要加速域名支持 HTTPS 访问方可支持该特性；
* 使用 Server Push 特性，通常会在源站设置 Link 首部，例如：

```
Link: </css/styles.css>; rel=preload; as=style

```
如果在 CDN 端进行自定义 Server Push 配置，优先级会高于源站设置的 Link 首部。



###5.4 兼容性说明

又拍云 HTTPS 加速服务是基于 SNI 技术实现的，因此某些不支持 SNI 的浏览器或客户端访问可能出现问题。支持 SNI 的客户端列表如下：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-https5.png" height="470" width="800" />

声明：运行在 Windows XP 上的所有版本的 Internet Explorer 都不支持 SNI ，详细请参考[这里](http://serverfault.com/questions/109800/multiple-ssl-domains-on-the-same-ip-address-and-same-port)。

###5.5 TLS 1.3

> 功能说明

`TLS 1.3` 是 TLS 协议中最新、最快和最安全的版本，相比旧版的 TLS 协议增加了多项新功能。通过简化 SSL 握手，提高了建连速度，减少了延迟。并通过移除有安全隐患的加密算法，提高了用户访问的性能、效率和安全性等等。目前 `TLS 1.3` 已在 CDN 全平台支持，您可以根据业务需求合理关闭和开启。

> 如何使用？

**1、如何在浏览器（客户端）中开启 TLS 1.3 ？**

目前最新版本的 Chrome 和 Firefox 都支持 `TLS 1.3`，但是都需要手动开启，主要原因是 `TLS 1.3` 还在草案阶段。

1) 如何在 Firefox 中手动启用 `TLS 1.3` ？

<img src="https://blog.upyun.com/wp-content/uploads/2017/12/firefix.jpg" height="243" width="800" />

Mozilla Firefox 用户可以通过以下方式在 Firefox 中启用 `TLS 1.3` 支持（ 请注意，Nightly 默认支持 `TLS 1.3`，而 Firefox Stable 需要专门配置以支持 TLS 1.3 ）。

 - 在 Firefox 地址栏中加载 `about:config`。如果显示警告屏幕，请确认您要小心，请忽略安全提示；
 - 在搜索区域搜索 `security.tls.version.max`；
 - 通过双击它将首选项的值更改为 4，默认为 3，如上图所示。

2）如何在 Chrome 中手动启动 `TLS 1.3` ？

<img src="https://blog.upyun.com/wp-content/uploads/2017/12/chrome_tls13.jpg" height="329" width="800" />

Google Chrome 用户可以通过以下方式在 Chrome 中启用 `TLS 1.3` 支持（ 注意：`Chrome 62`  之前的版本需要将 ` Maximum TLS version enabled`  改为 `TLS 1.3` ）

 - 在浏览器的地址栏中加载 `chrome://flags/`。这将打开 Web 浏览器的实验页面；
 - 在搜索区域搜索 TLS 或者 tls ，找到 `TLS 1.3`  选项，默认为 Default；
 - 需要将  `TLS 1.3` 改为 `Enabled (Draft)`；
 - 重新启动 Web 浏览器。
 

**2、如何在又拍云 CDN （服务端）控制台开启 `TLS 1.3` ？**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > HTTPS > TLS 1.3, 滑动开关即可开启功能。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-https-tls13.jpg" height="470" width="800" />

----------

##6.访问控制

###6.1 IP 黑白名单

> 功能说明

若需要禁止某些 IP 或只允许某些 IP 访问，则可以使用 IP 黑白名单功能；

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > IP 黑白名单，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-ip.png" height="470" width="800" />

1.配置 IP 黑名单

点击【开启 IP 黑名单】按钮，填写待加入黑名单的单个 IP 或者 IP 段，例如：

```
192.168.1.1
192.168.2.*
192.168.3.*
```
如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-ip-blacklist.png
" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮，即可保存生效。

1.配置 IP 白名单

点击【开启 IP 白名单】按钮，填写待加入白名单的单个 IP 或者 IP 段，例如：

```
193.168.5.1
193.168.6.*
193.168.7.*
```
如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-ip-whitelist.png" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮，即可保存生效。

**注意事项**

1.支持 `*` 通配符，如 `10.11.12.*` ，将禁止或允许 `10.11.12.0 ~ 10.11.12.255` 的 IP 访问；

2.IP 黑白名单上限 100 条，被禁止的 IP 访问资源时，将提示 403；

3.IP 黑名单和 IP 白名单不能够同时配置，特别地，配置完白名单之后，其他都为 IP 黑名单，请谨慎操作。


----------

###6.2 地区访问限制

> 功能说明

地区访问限制是指根据加速网站的需求，允许或禁止特定区域的终端用户对网站资源的访问。即网站指定地区，允许该地区终端用户访问，而限制其他地区用户访问，或者相反。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > 地区访问限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-region.png" height="470" width="800" />

1.配置地区黑名单

点击【启用黑名单】按钮，填写待加入黑名单的国家、地区、运营商信息，例如：

```
1	韩国	
2	泰国
3	澳大利亚
```
如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-region-blacklist.png
" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮，即可保存生效。

2.配置地区白名单

点击【启用白名单】按钮，填写待加入白名单的国家、地区、运营商信息，例如：

```
1	中国 辽宁 电信
2	中国 香港
3	中国 北京
4	中国 广东 电信
```
如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-region-whitelist.png" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮，即可保存生效。更多详细配置请参见 [地区访问限制用户指南](https://blog.upyun.com/?p=886)。

**注意事项**

 - 地区访问限制配置粒度，中国地区支持省份、运营商（包括：电信、铁通、移动、联通、鹏博士、教育网、科技网），海外地区支持到运营商；
 
 - 地区白名单和黑名单不能同时配置，特别地，配置完白名单后，其他地区都为黑名单，请谨慎操作。


 ----------
 
###6.3 Referer 防盗链

> 功能说明

基于 HTTP 协议的 referer 机制，通过 referer 跟踪来源，对来源进行识别和判断，实现原理就是通过 HTTP 请求 Header 中的 referer 字段的值来设置过滤策略，默认情况下，防盗链未启用，无黑/白名单。除此之外，支持允许 referer 为空和禁止 referer 为空的设置，默认情况下，允许 referer 为空。

白名单：仅允许名单中的域名网站访问文件，其他域名网站都不允许访问。

黑名单：仅禁止名单中的域名网站访问文件，其他域名网站都允许访问。

特别地，默认【允许 Referer 为空】 这种情况会绕过防盗链的逻辑；若启用【禁止 Referer 为空】，那么这类请求就直接被禁止访问。

**配置引导**

登陆 [ CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > Referer 防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-referer.png" height="470" width="800" />

1.配置 referer 黑名单

点击【启用黑名单】按钮，填写待加入黑名单域名或者域名段，例如：

```
www.upyun.com
image.upyun.com
*.upyun.com
```
如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-referer-blacklist.png" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮，即可保存生效。

2.配置 referer 白名单

点击【启用白名单】按钮，填写待加入白名单域名或者域名段，例如：

```
www.baidu.com
www.taobao.com
www.163.com
```
如下截图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-referer-whitelist.png" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮，即可保存生效。

**注意事项**

 - 白名单：仅允许名单中的域名网站访问文件，其他域名网站都不允许访问;
 
 - 黑名单：仅禁止名单中的域名网站访问文件，其他域名网站都允许访问;
 
 - 支持 `*` 通配符，比如白名单的 `*upyun.com` 将允许 `www.upyun.com`、`abcupyun.com` 等网站访问;
 
  - 特别地，默认 `允许 Referer 为空` 这种情况会绕过防盗链的逻辑，若这里启用 `禁止 Referer
   为空`，那么这类请求就直接被禁止访问了。

----------

###6.4 User-Agent 防盗链

> 功能说明

CDN 支持针对 HTTP 请求头中的 User-Agent 信息，禁止或者允许符合特定 User-Agent 规则的请求。具体使用的场景，举个例子：

1）某个资源或者页面不希望被 IE 或者 Chrome 浏览器访问，此时您可以配置 User-Agent 黑名单就可以实现；

2）某客户端拥有自己专属的客户端对资源进行下载，该下载工具在请求时，使用了定制的 User-Agent 名称，或只允许此类 User-Agent 请求资源，此时可以配置 User-Agent 白名单就可以实现。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > User-Agent 防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-user-agent.png" height="470" width="800" />

1.User-Agent 白名单配置

选择【开启白名单】按钮，填写待配置的白名单列表，如下所示：

```
Mozilla/4.0 (Windows; MSIE 6.0; Windows NT 5.2)
Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)
Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0)
Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)
Mozilla/5.0 (compatible; WOW64; MSIE 10.0; Windows NT 6.2)
```
配置截图如下：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-user-agent-whitelist.png" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮即可。

2.User-Agent 黑名单配置

选择【开启黑名单】按钮，填写待配置的黑名单列表，如下所示：

```
Mozilla/5.0 (Android; Mobile; rv:14.0) Gecko/14.0 Firefox/14.0
Mozilla/5.0 (Android; Tablet; rv:14.0) Gecko/14.0 Firefox/14.0
Mozilla/5.0 (Windows NT 6.2; WOW64; rv:21.0) Gecko/20100101 Firefox/21.0
Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:21.0) Gecko/20130331 Firefox/21.0
```
配置截图如下：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-user-agent-blacklist.png" height="470" width="800" />

配置完毕，点击右下角的【确定】按钮即可。


**注意事项**

 - 添加 User-Agent 黑白名单规则数量上限各为 50 条，请合理分配；

 - `User-Agent` 不区分大小写，且支持 `*` 通配符；

 - `User-Agent` 防盗链的优先级高于 Referer 防盗链;

 - User-Agent 很容易伪造，因此 User-Agent 防盗链安全性较低，建议结合其他防盗链功能配合使用。

----------

###6.5 Token 防盗链

> 功能说明

Token 防盗链是通过对时间有关的字符串进行签名，将时间、签名信息通过一定的方式传递给 CDN 节点服务器作为判定依据，CDN 边缘节点依据约定的算法判断来访的 URL 是否有访问权限。如果通过，执行下一步；如果不通过，响应 HTTP 403 状态码或者通过 302 跳转到其他 URL。

**实现原理**

Token 防盗链的实现原理可参见如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-token.png" height="380" width="500" />

如上图所示，整个 token 防盗链的实现需要如下几个部分来配合：

1）客户端：负责发送原始请求给客户端业务服务器以及发送带签名的 URL 给 CDN 节点进行验证；

2）业务服务器：根据约定的算法生成带 _upt 参数的 URL 返回给客户端；

3）CDN 节点：负责和客户端进行时间、签名校验；


1、签名参数

```

etime: URL 过期的时间，必须是 UNIX TIME 格式，如：2017/3/9 9:19:0 -> 1489022340

secret: 和 CDN 平台约定的签名密钥，需要在 CDN 管理控制台配置

URI：请求 URL 的路径部分（不包含 ? 及后面的 Query String），如客户端访问的外链地址为 http://xxx.b0.upaiyun.com/path/to/a.jpg?version=1.0，那么 URI 部分则为：/path/to/a.jpg

```

2.算法说明

```
sign = MD5( secret & etime & URI )
_upt = sign { 中间 8 位 }＋etime
_upt = MD5( secret & etime & URI ){ 中间 8 位 } + etime
```

举个例子：假设当前的 UNIX TIME 时间为：1370000000，某图片资源（例如：http://test.example.com/dir/pic.jpg）10 分钟有效，则：

```
etime = 1370000000 + 600 = 1370000600

URI = '/dir/pic.jpg'

sign = MD5( secret & etime & URI ) = xxxxxxxxxxxxabcdefghyyyyyyyyyyyy

_upt = MD5( secret & etime & URI ){ 中间 8 位 } + etime ＝ abcdefgh1370000600

```

最后经过客户端业务服务器生成的 URL 为：

```
http://test.example.com/dir/pic.jpg？_upt=abcdefgh137000060
```

**实现方式**

1.客户端业务服务器生成验证信息

验证信息的生成由业务服务器负责，具体的加密过程需要确认如下事项：

 - 确认过期时间的格式，默认采用 UNIX 时间戳格式
 - 确认验证信息中的密文，用户计算验证信息，需要和 CDN 平台约定
 - 确认验证信息时加入的参数，默认为 URL 的路径部分
 - 根据上文的算法说明计算验证信息，其中请求 URL 中的验证参数为 _upt

2.又拍云 CDN 节点验证过程

 - 根据约定解析取出过期时间，和当前 CDN 节点服务器时间进行比较，确认请求是否过期
 - 根据上文约定好的算法计算方式，计算出 MD5 加密串后，和 URL 中的加密串进行比较，验证加密串是否一致
 - 如果以上两个步骤都验证通过，请求才会被认为是合法的，这时 CDN 会请求资源响应给客户端，否则会被认为是非法请求，直接响应 HTTP
   status code 403

 
**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 管理 > 功能配置 > 访问控制 > Token 防盗链，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-token-miyao.png" height="470" width="800" />

如上截图所示，此处填写的密钥也即与 CDN 平台约定的签名密钥。


**注意事项**

 - 当防盗链涉及的参数（比如：密钥、过期时间）发生变更时，需要在 CDN 控制后台及时调整；
 - 时间戳防盗链的相关参数是经常发生变更的，由于植入到了 URL 中，会影响到节点的缓存，可能会导致一份相同的文件，会因为 URL
   里面的防盗链参数的不同而缓存多份，一般情况下，建议去忽略参数进行缓存。

----------

###6.6 回源鉴权

> 功能说明

回源鉴权是一种高级的防盗链方式，适用于对防盗链有很高实时性要求的场景。大概原理是 CDN 边缘节点每次接受到请求之后，都需要回客户源站进行验证，验证通过之后才认为是合法请求，这样 CDN 可以继续提供服务。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > 回源鉴权，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-access-back-to-source-auth.png" height="470" width="800" />

点击右上角的【添加鉴权配置】，即可进入配置界面，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-source-auth.png
" height="470" width="800" />

> 资源地址

需要进行鉴权的匹配规则集，支持域名级别配置，示例如下：

```
test123.upyun.com/video/*
```

> 服务器地址

也即鉴权 URL ，鉴权业务服务器地址，格式为：

```
scheme://host:port/path
```

示例如下：

```
http://validate.example.com/app/cdnValidate

```

> 请求方法

向鉴权服务器发起请求的方法。目前仅支持 GET 和 POST ，截图选择 GET 方法。

> 鉴权参数

鉴权所需参数，也即请求 URL 里面的参数，假如请求 URL 为：
```
http://test123.upyun.com/video/cdnValidate?accountID=2345&userTD=kadkf&token=fjdkadkf&sign=dfskjdk123
```
则，鉴权参数可为：

```
accountIDD
userTD
token
sign
```
具体参数个数，根据业务情况进行配置。

> 鉴权方式

约定的鉴权通过的状态码或者响应体内容，其中状态码不能为 5XX，响应体内容不能为空。例如：示例鉴权通过的状态码为 200。

> 超时等待时间

鉴权请求的超时时间，默认为 10 秒钟。

> 鉴权服务器连接超时

当服务器失去响应时，此字段生效。为 true 时认为所有请求都通过鉴权，默认是鉴权通过。


**注意事项**

 - 使用该功能，需考虑访问量过大时鉴权服务器的压力；
 - 合法请求时，需在鉴权服务器响应合法的鉴权状态码和响应体，否则会鉴权失败；
 - CDN 节点到鉴权服务器出现网络异常或者网络超时等情况时，会默认鉴权通过；

----------

###6.7 HTTP 请求大小限制

> 功能说明

该功能表示能够提交的最大请求 body 限制，适用于 POST 和 PUT 请求。默认为 512 M，用户可以根据业务实际情况将此值调小，例如设置为 1M。默认情况下，网站服务器是不限制请求 body 大小的，这样很容易会被黑客利用并进行 DDoS 攻击，黑客可以利用这一点往 HTTP POST 的 body 中传递非常大（比如几十M）的请求，可能导致服务端无法对外提供服务。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > HTTP 请求大小限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-request-limit.png" height="470" width="800" />

在配置界面，根据业务要求，填写配置内容，然后点击【确定】按钮即可。

**注意事项**

适用于动态资源加速，全静态资源加速建议不启用该功能。

----------

###6.8 IP 访问限制

> 功能说明

IP 访问限制也即 CDN 边缘节点对终端用户的访问 IP 的访问频率进行统计，在单位时间周期内（目前固定的时间为 60 秒），如果访问频率达到设定的阈值，将主动拦截该 IP 对域名的访问，从而达到访问限制的目的。

当前的单 IP 访问频率只支持针对 CDN 单个边缘节点生效，当达到设定的阈值时，异常访问的客户端 IP 将加入黑名单中，并可以设定生效时间，在该时间周期内，所有该 IP 的访问都会被拦截。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 管理 > 功能配置 > 访问控制 > IP 访问限制，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-ip-limit.png" height="470" width="800" />

配置步骤如下：

第一步：填写访问的资源路径规则，支持 `＊` 通配符，如：`/a/*.mp4`、`/b/*.flv`

第二步：选择限制方式：`直接禁止`

第三步：设置访问频率及相应的时间限制

详细配置请参见 [IP 访问限制用户指南](https://blog.upyun.com/?p=896)。


**注意事项**

 - 直接禁止是以 60s 为一个计数周期，即上一个 60s 的计数不会累计叠加到下一个 60s 内；
 
 - 「访问频率阈值」需根据业务情况慎重设置，过低可能引起正常用户的访问受限，过高则无法有效限制非法访问；
 
 - 请勿将访问频率值设置过低，以免影响正常用户的访问请求。

----------

###6.9 CC 防护

> 功能说明

CC 防护主要是针对网络安全领域中的 CC 攻击而进行的一种应用层攻击防护，该功能通过自定义匹配规则对目标资源进行监控，当请求频率达到触发频率时对疑似攻击请求进行校验，校验通过则允许访问；校验不通过，则直接禁止访问。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制 > CC 防护，点击「管理」按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-cc.png" height="470" width="800" />

点击右上角的【添加防护策略】，出现如下配置界面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-access-control-cc.png" height="470" width="800" />

具体配置可以走如下四个步骤：

 - 第一步：添加防护策略，设置 URI 匹配规则，例如：/video/*
 - 第二步：填写访问频率阈值，例如：500次/分钟，在智能防护模式下才有效
 - 第三步：设置 IP 白名单，如果要绕过 CC 防护策略，此处可进行设置
 - 第四步：选择 CC 防护模式，包括强制防护、智能防护、暂停防护，当正在遭受攻击时，建议选择强制防护模式

> 强制防护

也就是忽略频率阈值，针对匹配的 URI 直接进行校验，校验通过则放行检验不通过则拒绝访问；

> 智能防护

针对匹配到的 URI 进行访问频率阈值的判断，达到阈值则进行校验，校验通过则放行，校验不通过则拒绝访问。

> 暂停防护

防护规则失效，不进行频率统计和请求校验。

**注意事项**

1.CC 防护分为强制防护和智能防护两种模式，在智能防护模式下，则会匹配 URI 规则，请求一旦达到访问频率阈值，则会触发 JavaScript 校验，校验通过则可正常访问，校验不通过则直接拒绝。在强制防护模式下，则会忽略访问频率阈值，直接触发 JavaScript 校验，以此类推;

2.在智能防护模式下，访问阈值设置得过低，可能不会触发 JavaScript 校验，建议阈值设置高一些。

###6.10 WAF 防护

> 功能说明

WAF（Web Application Firewall）的中文名称叫做 Web 应用防火墙 ，是通过执行一系列针对 HTTP/HTTPS 的安全策略来专门为 Web 应用提供保护的一项功能。主要防护的是来自对网站源站的动态数据攻击，可防护的攻击类型包括 SQL 注入、XSS 攻击、CSRF 攻击、恶意爬虫、扫描器、远程文件包含等。


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 管理 > 功能配置 > 访问控制 > WAF 防护，开启 WAF 保护即可。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-security-waf.png" height="470" width="800" />

如上配置界面，点击【确定】按钮即可开启。


**注意事项**

 - 当前版本的 WAF 功能属于免费功能，暂无统计分析类数据，待后续规划；
 - 平台默认规则有限，WAF 功能不确保防护所有的攻击请求；
 - 适用于动态资源加速，全静态资源加速建议不启用该功能。
 

###6.11 跨域配置

> 功能说明

跨域资源共享 （ Cross-Origin Resource Sharing）是一种网络浏览器的技术规范，他为  Web 服务器定义了一种方式，允许网页从不同的域访问其资源。跨源资源共享标准通过新增一系列 HTTP 头，让服务器能声明那些来源可以通过浏览器访问该服务器上的资源。此时需要在 Response Header 中增加跨域相关配置，这样就可以使得资源的安全访问成为可能。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务 管理 > 功能配置 > 访问控制  > CORS 跨域共享，点击【管理】按钮即可开始配置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-cors-config.png" height="470" width="800" />

具体配置字段做如下说明：

**Access-Control-Allow-Origin**

返回的资源需要有一个 `Access-Control-Allow-Origin` 头信息，也可以设置通配符＊，允许被所有域使用。语法如下:
>  Access-Control-Allow-Origin: <origin> | *

举个栗子，允许来自 `http://www.upyun.com` 的请求，你可以这样指定:
>  Access-Control-Allow-Origin: http://www.upyun.com

**Access-Control-Allow-Methods**

表示允许的跨域请求的方法，在当前请求的域被允许后，还要检查当前请求的方法是否被允许，示例如下：
> Access-Control-Allow-Methods: POST, GET, OPTIONS

**Access-Control-Allow-Headers**

也是在响应预检请求的时候使用，用来指明在实际的请求中，可以使用哪些自定义 HTTP 请求头，比如：
> Access-Control-Allow-Headers: X-PINGOTHER，X-UPYUN

这样在实际的请求里,请求头信息里就可以有这么一条：

> X-PINGOTHER: hello

**Access-Control-Expose-Headers**

设置浏览器允许访问的服务器的头信息的白名单，示例如下：

> Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header

这样，`X-My-Custom-Header` 和 `X-Another-Custom-Header` 这两个头信息，都可以被浏览器得到。

**Access-Control-Max-Age**

这个头告诉我们这次预请求的结果的有效期是多久，单位为秒，设置示例如下：
> Access-Control-Max-Age: 864000

表明在 864000s（10天）内，对该资源的跨域访问不再发送另外一条预请求。

**注意事项**

1.最多支持添加 10 条规则，匹配时从第一条开始逐条匹配，以最早匹配上的规则为准

2.`允许的域`、`Allow Header` 、`Expose Header` 最多允许 20 行

3.`允许的域` 及 ` Methods ` 建议根据需要使用最小的配置来保证安全性，多条规则间不要有覆盖和冲突

4.`允许的域` 需带上完整的域信息，即指定 https 或者 http，或者 `*://foo.com` 这样的形式

5.`Allow Header`、`Expose Header` 不允许使用通配符，大小写不敏感

6.`缓存时间` 如果没有特殊情况可以酌情设置得大一点，默认为 86400 秒，最大为 604800 秒

----------

###6.12 自定义页面

**功能说明**

自定义页面功能主要针对 CDN 返回的[默认错误码](http://docs.upyun.com/cdn/config/#9)以及源站返回的错误状态进行自定义页面的设计和编辑，可以进一步提升终端用户体验。如未配置，则默认遵循又拍云 CDN 服务默认错误页面或源站默认的错误页面。CDN 目前提供三种选项的设置：默认页面、自定义页面、美化页面（暂未开放）。以地区访问限制为例：

- 默认：CDN 会默认返回如下 JSON 串格式

```

{
  "code": "40310002",
  "msg": "region is forbidden"
}

```

- 自定义页面：当触发 CDN 防盗链规则时，CDN 会直接返回自行设计和编辑的页面。当触发源站错误码时，会 302 跳转到自行设计的页面。

- 美化页面：又拍云结合业务场景，自行设计的美化页面（暂未开放）。

**1、CDN 业务端支持的配置项包括：**

- IP/地区限制

当在又拍云控制台配置了 IP 黑白名单、地区访问限制功能，触发该规则时会直接返回自定义的页面。

- 防盗链

当在又拍云控制台配置了 User-Agent 防盗链、Referer 防盗链、Token 防盗链功能，触发该规则时会直接返回自定义的页面。

- 回源鉴权

当在又拍云控制台配置了回源鉴权功能，触发规则（包括鉴权失败、鉴权错误）时会直接返回自定义的页面。

- 安全防护

当在又拍云控制台配置了 IP 访问限制、WAF 防护、HTTP 请求大小限制功能，触发规则时会会直接返回自定义的页面。

- 边缘规则

当在又拍云控制台配置了边缘规则访问控制功能，触发规则时会直接返回自定义的页面，当前仅针对 403、404 状态码有效。

- 关闭服务

CDN 服务被关闭时，可以根据要求自定义页面。

**2、源站匹配的状态码包括：**

- 400

表示该请求报文中存在语法错误，导致源站无法理解该请求。

- 403

该状态码表明对请求资源的访问被源站拒绝了。

- 404

该状态码表明源站上无法找到指定的资源。

- 405

指出请求方法对某些特定的资源不允许使用，也即客户端的请求被源站拒绝了。

- 414

表示请求的 URI 过长，源站无法处理时返回此代码。

- 416

客户端包含了一个源站无法满足的 Range 头信息的请求。

- 500

源站遇到了一个未曾遇到的错误（内部服务器错误），导致无法完成对请求的处理。

- 501

源站不支持客户端请求中所要求的功能时返回此代码。

- 502

源站作为网关或者代理服务器尝试执行请求时，从远端服务器接收到错误或者无效的请求。也即错误的网关。

- 503

由于临时的服务器维护或者过载，源站当前无法处理请求。

- 504

源站作为网关或者代理服务器尝试执行请求时，未能及时从远端服务网收到响应。也即网关超时。


**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制  > 自定义页面，在此栏目下可进行相应设置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-custom-error-page.png" height="470" width="800" />

- 第一步

点击【管理】按钮进入配置面板：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-custom-error-page1.png" height="470" width="800" />

- 第二步

点击右上角的【添加规则】，进入规则配置界面：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-custom-error-page2.png" height="470" width="800" />

在规则配置页面，配置项包括：错误来源、匹配类型、响应地址。需要注意的是：响应地址必须要填写 URI，例如：

```
/custom/error-page.html

```

自定义页面的资源所绑定的域名必须是访问域名，否则会访问到错误的位置，也即：当客户端请求为：

```
https://www.example.com/index.html 

```
当触发防盗链规则之后，会 302 跳转（仅支持错误来源为源站的情况，错误来源于 CDN 时，会直接去请求错误页面，不会有 302 跳转过程）到：

```
https://www.example.com/custom/error-page.html

```

也即请求的资源和自定义错误页面必须属于同一源站，并且属于同一加速域名。

- 第三步

在配置管理界面，可以编辑、删除和查看配置规则，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-custom-error-page3.png" height="470" width="800" />

确保配置的规则没有问题之后，点击【确定】按钮即可保存配置。

**注意事项**

 - 自定义页面属于用户源站托管的资源，同常规页面和资源一样，会按照正常的 CDN 加速分发计费。
  
 - 自定义页面的资源所绑定的域名必须是访问域名，否则不会生效，也即：当客户端请求为：https://www.example.com/index.html ；当触发防盗链规则之后，CDN 会直接返回该页面：https://www.example.com/custom/403.html。此时在 CDN 自定义页面规则的响应地址项填写 /custom/403.html 即可。
 
 - 在优先级方面，自定义页面的优先级会高于自定义提示图功能。
 
 - 当错误来源是 CDN 时，此时您的自定义页面是 HTML 页面，需要确保该页面为单页面，如果需要嵌入图片，我们建议将图片通过  base64 的方式将 SVG 图片嵌入到 HTML 网页中。当然，您也可以将图片等资源使用外链的方式来嵌入。

----------

###6.13 自定义提示图

支持自定义错误状态设置，目前支持 403、404、405 错误码自定义提示图的设置。如未设置，则默认遵循又拍云 CDN 服务默认错误状态提示图。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 访问控制  > 自定义提示图，在此栏目下可进行相应设置。如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-custom-map.png" height="470" width="800" />

> 403 提示图

当用户设置了例如 IP 禁用、Referer 等防盗链配置并且当前的请求与规则不匹配时，即会响应 403 给客户端，表示该次请求被禁止。另外，若该服务设置了自定义 403 提示图，那么此类情况下就会响应一个 302 跳转到相应的图片。

> 404 提示图

当请求的文件不存在时，会响应 404 状态码，可通过自定义提示图来展示。

> 405 提示图

正常使用中的服务，若被人为地设置为禁止外链，此时访问就会响应 405 状态码。同样，405 也支持自定义提示图设置。


**注意事项**

 - 若此处未设置任何自定义提示图，CDN 服务会默认响应一个错误状态码提示图；
 
 - 此处自定义提示图的文件大小限制在 30KB 以内；

----------

##7.图片处理

###7.1 间隔标识符

> 功能说明

用于分隔访问地址中的文件路径与处理参数、版本名称。如：http://abc.b0.upaiyun.com/pic.jpg!hi， `pic.jpg` 为文件路径，`hi` 为版本名称，标识符可为半角字符：“!”，“-”，“_” 三种，可以在管理界面中进行更改。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 图片处理 > 间隔标识符，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-custom-image-icon.png" height="470" width="800" />

点击界面右边的【管理】按钮即可设置，设置界面如下：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-custom-map-icon-shezhi.png" height="470" width="800" />

在该配置界面，可以选择感叹号（!）、中划线（-）、下划线（_）作为间隔标识符，默认为感叹号（!）。

**注意事项**

 - 更换间隔标志符后，之前自定义版本的访问地址都将失效，需要使用新设置的间隔标志符才能保证正常访问；
 
 - 为避免自定义版本访问出错，选择的间隔标识符不能用于文件或目录名中。

----------

###7.2 自定义版本

> 功能说明

针对使用 CDN 服务或者云存储服务的用户，可以使用自定义版本作图，具体使用格式参见如下表格。

```
默认绑定域名：image.upyun.com
原图路径：/pic.jpg
间隔标识符：!
缩略图版本名称：small
图片信息版本名称：exif
缩略图访问地址：http://image.upyun.com/pic.jpg!small
原图 EXIF 信息访问地址：http://image.upyun.com/pic.jpg!exif

```

自定义版本使用格式：

```

http://绑定域名/原图路径+间隔标识符+自定义版本名称。

```

详细介绍参见[图片处理](https://docs.upyun.com/cloud/image/)。

**配置引导**

登陆 [CDN 控制台](https://console.upyun.com/login/)，依次进入：服务管理 > 功能配置 > 图片处理 > 自定义版本，在此栏目下可进行相应设置。如下图所示：

<img src="http://upyun-assets.b0.upaiyun.com/docs/cdn/config/pyun-cdn-config-custom-image-create.png" height="470" width="800" />

1.创建缩略图版本

创建缩略图版本，填写基本信息。

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-custom-image-suoluebanben.png" height="470" width="800" />

根据要求，设置水印参数，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-custom-image-shuiyin.png" height="470" width="800" />

根据要求，设置输出信息，如下图所示：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-custom-image-shuchu.png" height="470" width="800" />

最终的访问 URL 可为：

```
http://test.example.com/test.jpg!upyun123
```
其中 `upyun123` 为自定义版本。


2.创建图片信息版本

图片信息版本内容包括：图片基本信息（图片宽度、高度、帧数、格式）和 EXIF 信息，具体设置参见下图：

<img src="https://upyun-assets.b0.upaiyun.com/docs/cdn/config/upyun-cdn-config-custom-image-banben.png" height="470" width="800" />

填写版本名称，点击右下角的【确定】按钮即可。

----------

##8.其他功能

###8.1 文件另存为

> 功能说明

又拍云 CDN 服务支持对响应头 `Content-Disposition` 字段的特殊设置，也即在请求 URL 中加入 `_upd` 参数可对 `Content-Disposition` 进行特殊化配置。通过对该参数的配置就可以强制浏览器触发下载行为，同时该参数的值会作为文件下载后保存到本地的文件名。特别地，当其值为 `true` 时，保持原文件名不变。

**如何使用**

示例一：

    请求参数中加入 “_upd=true” ，代表添加响应头为： Content-Disposition: attachment
```
$ curl http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upd=true -I

> HTTP/1.1 200 OK
> Content-Type: image/png
> Content-Length: 57371
> ...
> Accept-Ranges: bytes
> Content-Disposition: attachment;

```

示例一：

    请求参数中加入 “_upd=abc.png”，代表添加响应头为： Content-Disposition: attachment; filename="abc.png"

```
$ curl http://upyun-assets.b0.upaiyun.com/docs/cdn/upyun-cdn-architecture.png?_upd=abc.png -I

> HTTP/1.1 200 OK
> Content-Type: image/png
> Content-Length: 57371
> ...
> Accept-Ranges: bytes
> Content-Disposition: attachment; filename="abc.png"

```

**注意事项**

特别地， `参数跟随` 开启的情况下，此参数无效。

----------

###8.2 传递最终用户 IP

> 功能说明

又拍云 CDN 回客户源的时候会带上 `X-Real-IP` 和 `X-Forwarded-For` 的请求头下去，值为用户实际访问 CDN 的来源 IP 地址。特别地，为了兼容部分服务端程序，我们额外还提供了 `Client-IP` 请求头的支持，其值和 `X-Real-IP`、`X-Forwarded-For` 相同。

**如何使用**

1、X-Real-IP 传递用户 IP

使用该方式传递最终用户 IP ，需要服务端代码进行一些改造，网站需要根据使用编程语言的不同，修改相应的代码模块，才可以传递最终用户 IP。代码示例如下：

示例一：PHP 代码¶

     <?php
            $ip = $_SERVER["HTTP_X_REAL_IP"];
            echo $ip;
     ?>

示例二： Nginx 配置¶

    server
        {
            listen 80;
            add_header X-Real-IP $http_x_real_ip;
        }


2、X-Forward-For 传递用户 IP

回源请求头会默认传递 `X-Forwarded-For` 的值，用户网站无需任何改造。

**注意事项**

1、新增加速服务时我们会默认使用 `X-Real-IP` 和 `X-Forwarded-For` 方式，网站只需要按照 “如何使用” 章节中，对原先的用户 IP 获取代码进行替换即可；

2、由于 `X-Real-IP`  是又拍云 CDN 服务特有的回源请求头 ，故终止 CDN 后，网站需将获取用户 IP 的代码修改为原始代码；

3、在选择使用 `X-Forwarded-For` 进行最终用户 IP 传递时  ，`X-Real-IP`、`Client-IP`也是同时传递的；

----------

##9.默认错误码

**默认错误码介绍**


针对错误状态（这里特指 CDN 业务端以及回源站相关错误），CDN 默认返回 JSON 串的形式，格式为：

```

{
	"code": "xxxxxxxx",
	"msg": "xxxxxxxxxxxxxxx"
}

```

其中 ` code`  代表错误码，为 8 位数字组成，例如：` 40310012` 代表触发了 IP 黑名单规则；` msg`  代表错误描述，例如：` remote address is forbidden` ，也即客户端 IP 被禁止访问。您可以根据 CDN 返回的错误码和描述来判断触发了何种规则，进而更方便的定位问题。


**默认错误码一览表**


现将 CDN 平台部分可开放的错误码进行如下归类：

**1.访问控制相关**


| 序号 | 错误码（code）     |    错误描述(msg)     |  发生了什么 | 
|---------------------|------------------------|------------------|------------|
|  1  |  40310001   | invalid url |  URL  被禁止访问  | 
|  2  |  40310002  | region is forbidden |  触发了地区访问限制规则  | 
|  3  |  40310003   | too many requests |  触发了 IP 频率限制规则  | 
|  4  |  40310011   | invalid User-Agent header |  触发了 User-Agent 防盗链规则  | 
|  5  |  40310012   | remote address is forbidden |  触发了 IP 黑白名单规则  | 
|  6  |  40310013   | invalid user token |  触发了 Token 防盗链规则  | 
|  7  |  40310014   | invalid Referer header |  触发了 Referer 防盗链规则  | 
|  8  |  40310015   | referer uri is forbidden |  触发了 Referer URI 防盗链规则  | 
|  9  |  40310020   | invalid authentication response body |  鉴权服务器返回的 body 内容不合法，导致鉴权失败  | 
|  10  |  40310021   | invalid authentication response status |  鉴权服务器返回的状态码不合法，导致鉴权失败  | 
|  11  |  40310022   | cyclic authentication requests |  鉴权服务器地址在又拍云进行了 CDN 加速，导致了循环请求  | 
|  12  |  50310020   | authentication service unavailable |  鉴权服务器不可用  | 
|  13  |  40110910   | authentication fails |  回源鉴权失败  | 
|  14  |  40310006   | (api) request body too large |  通过 API 接口上传的文件 body 太大  | 
|  15  |  41310001   | request body too large |  请求的 body 大小超过限制  | 
|  16  |  40010030   | multiple Content-Type header |  触发了 WAF 规则，请求头中含有多个 Content-Type 头部 | 
|  17  |  40310031   | too large form |  触发了 WAF 规则，form 表单太大  | 
|  18  |  40310032   | waf protection rules triggered |  触发了 WAF 规则  | 
|  19  |  xxx10999   | edge rule triggered |  触发边缘规则，xxx 特指状态码 | 
|  20  |  40510004   | invisible domain |  域名触发了敏感信息被禁止访问  | 



**2.HTTP 及平台规范相关**


| 序号 | 错误码（code）     |    错误描述(msg)     |  发生 | 
|---------------------|------------------------|------------------|------------|
|  1  |  41610001   | multiple Range header |  多个 Range 头  | 
|  2  |  41610002   | multiple sections in Range header |  Range 请求中存在多段请求范围  | 
|  3  |  41610003   | illegal range portion |  Range 请求范围非法  | 
|  4  |  40010040   | absent Host header |  请求中缺少 Host 头部，返回工信部错误页面 | 
|  5  |  40010041   | bucket not found |  未绑定加速域名或者服务不存在，返回工信部错误页面  | 
|  6  |  40510001   | invisible bucket |  服务（空间）为关闭状态  | 
|  7  |  41210001   | last-modified or Etag illegal |  Last-Modified 头部或者 ETag 头部非法  | 
|  8  |  40310007   | cyclic request |  触发了循环请求限制策略  | 
|  9 |  50310000   | unknown error |  其他未知的错误 | 



**3.常见回源错误**


| 序号 | 错误码（code）     |    错误描述(msg)     |  发生了什么 | 
|---------------------|------------------------|------------------|------------|
|  1  |  403xx002  | response body too large |  回源响应体太大  | 
|  2  |  502xx001   | no valid source address |  没有合法的回源地址  | 
|  3  |  502xx002   | can't connect to upyun fs |  无法连接 upyun 存储  | 
|  4  |  503xx001   | resolve domain failed |  域名解析错误 | 
|  5  |  503xx002   | resolve domain timeout |  域名解析超时  | 
|  6  |  503xx004   | connection refused |  连接被拒绝  | 
|  7  |  503xx005   | connection reset by peer |  连接被源站重置  | 
|  8  |  503xx006   | handshake failed|  握手失败  | 
|  9  |  503xx007   | client abort|  客户端取消连接 | 
|  10  |  503xx008   | closed |  连接被关闭 | 
|  11  |  503xx009   | broken pipe|  读取数据出现问题 | 
|  12  |  503xx010   | unexpect end of stream|  未知的源站响应结束 | 
|  13  |  503xx015   | certificate host mismatch|  SSL 证书头不匹配 | 
|  14  |  503xx016   | self signed certificate|  SSL 证书为自签名证书 | 
|  15  |  503xx017   | certificate has expired|  SSL 证书过期 | 
|  16  |  504xx001   | connection timed out|  回源连接超时 | 
|  17  |  504xx002   | connection timeout|  连接超时 | 
|  18  |  504xx003   | read timeout|  读取数据超时 | 
|  19  |  504xx004   | send timeout|  发送请求超时 | 

其中 `xx` 可能是 `00`、`01`、`02`、`03`、`04`。`00` 表示 `UNKNOWN`，`01` 表示又拍云存储，`02` 表示用户自主源，`03` 表示 ` CDN 代理层`，`04` 表示 ` 作图服务`。



**如何辨别**


**方式一**

如果您通过浏览器访问，您可以通过浏览器页面查看到一段错误代码，例如：


```
{"code":"40310002","msg":"region is forbidden"}
```

如上错误代码及描述则说明用户在 CDN 侧配置了地区访问限制策略。

**方式二**

通过 curl 命令可以看到 `X-Error-Code`响应字段，则可以看出触发了何种错误。

```

< HTTP/1.1 403 Forbidden
< Server: marco/2.2
< Date: Fri, 18 May 2018 06:39:47 GMT
< Content-Type: application/json
< Connection: keep-alive
< X-Error-Code: 40310002
< X-Request-Id: 6b947a5e14a252909acab1e55df121c2
< Content-Length: 47
< Via: M.pcw-cn-hkg-166
```

通过查看 `X-Error-Code: 40310002`，可以看出触发了地区访问限制规则。

----------
