## 账号申请
相关账号创建及认证可参考 CDN 服务[快速入门](http://docs.upyun.com/cdn/guide/)。

## 服务创建
登录又拍云控制台，进入`服务`标签栏、点击`创建服务`，在弹框中直播加速服务栏下点击`立即创建`。

   ![创建服务-1](http://image-pro.b0.upaiyun.com/product/uplive/image/build-bucket.png!800w)

### 又拍源
对于即将创建的直播服务有两种源站模式，当推流至又拍时，请选择`源站类型`为`又拍云`。此时，填写完服务名称，及预先备案好的`推流域名`与`播放域名`，并选择性地填写`接入点`，则直播服务创建成功。

![创建又拍云源](http://image-pro.b0.upaiyun.com/product/uplive/image/upyun-config.png!800w)

### 自主源
当直播流不是推送到又拍，而是回客户源获取时，请选择`自主源`源站类型，并设置好回源地址，且只需填写`播放域名`，选择性填写`接入点`。

![创建自主源](http://image-pro.b0.upaiyun.com/product/uplive/image/guest-config.png!800w)

信息填写完毕正确后，点击创建，即创建成功。创建成功后做 cname 解析，具体流程可参考 CDN 服务下[CNAME 解析](http://docs.upyun.com/cdn/guide/#cname)。

## 推流测试
CNAME 解析生效后，可进行推拉流测试。例如使用 OBS 推流，设置好推流地址。

   ![obs 推流](http://image-pro.b0.upaiyun.com/product/uplive/image/obs-1.png!800w)

   点击确定后进入推流窗口，选择好需要推流的内容，比如媒体源，再点击开始串流。
   
   ![obs 推流](http://image-pro.b0.upaiyun.com/product/uplive/image/obs-2.png!800w)
> 不同版本及不同平台的 obs 可能略有不同，根据具体情况而定。  

## 播放测试
播放对应推流，例如使用 VLC 播放，填写相应的 rtmp 播放地址。

   ![obs 推流](http://image-pro.b0.upaiyun.com/product/uplive/image/obs-3.png!800w)

