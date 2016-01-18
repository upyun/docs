Discuz 用户需要使用 UPYUN 有以下两个方法：

## 通过论坛自带的远程附件功能

使用 Discuz 的远程附件功能，可将论坛附件全部存放到 UPYUN 上。

**具体可按如下步骤：**

1. 前期准备：

    * 在 UPYUN 上创建一个文件类空间。 注意：必须是文件类空间，不能是图片类空间，discuz 附件含多种类型文件

    * 获取空间的 FTP 帐号信息。

2. 设置步骤：

    注：目前版本的 discuz 绑定 UPYUN，经过 discuz 官方测试，须增加 2 行测试代码，discuz 官方已在最新的版本中增加，会在下次发布时更新。

    目前您可以手动修改代码或跳过测试远程附件即可。 手动添加方法见帖子最后。

    在论坛后台找到设置远程附件功能，管理后台 => 全局 => 上传设置（或附件设置） => 远程附件：选择“启用远程附件”，然后设置 FTP 帐号信息，如下图所示：
    ![](https://upyun-assets.b0.upaiyun.com/docs/misc/dz.png)

3. 论坛原有的附件处理

    如果您想把论坛原有的附件也搬到 UPYUN，您只要 2 步操作：

    a、把原有附件通过 FTP 上传到云存储空间的相应位置

    b、把附件表的 remote 字段的属性改为 1

4. 到这一步，恭喜您，您的附件已经托管到 UPYUN，这下您不用担心附件的安全及性能问题了。

**附录一：**

* 手动修改测试程序代码：

    打开：source/admincp/admincp_checktools.php

* 找到：

```php
function getremotefile($file) {

    global $_G;

    @set_time_limit(0);

    $str = @implode('', @file($file));

    if(!$str) {

        $str = dfsockopen($file);

    }

    return $str;

}
```

替换为：

```php
function getremotefile($file) {

    global $_G;

    $file = $file.'?'.rand();

    @set_time_limit(0);

    $str = @implode('', @file($file));

    if(!$str) {

        $str = dfsockopen($file);

    }

    return $str;

}
```

* 找到

```php
ftpcmd('delete', $testfile);
```

增加一行:

```php
ftpcmd('delete', 'test/index.htm');
```


## 安装使用 UPYUN 官方插件


### Discuz X3、X3.1、X3.2

#### 插件下载

* 最新版插件(v1.0) 下载：[Github](https://github.com/upyun/discuz-plugin)
* 最新版插件(v1.0) 安装使用手册下载：[Pdf 版](http://upyun-assets.b0.upaiyun.com/docs/discuz-plugin/Discuz%20%E8%AE%BA%E5%9D%9B%20UPYUN%20%E6%8F%92%E4%BB%B6%20-%20v1.0.pdf) | [Html 版](http://upyun-assets.b0.upaiyun.com/docs/discuz-plugin/Discuz%20%E8%AE%BA%E5%9D%9B%20UPYUN%20%E6%8F%92%E4%BB%B6-v1.0.zip)

#### 版本更新说明
** v1.0 更新说明 **

- 不兼容 v0.1 版本
- 简化配置，不再区分文件和图片空间。UPYUN 合并了文件和图片空间
- 增加分块上传支持，大大提升上传的稳定性和速度
- 增加 Discuz X3.1 和 X3.2 版本的支持
- 取消本地图片和文件的备份
- 优化 Token 防盗链（需要将论坛域名绑定到 UPYUN 空间）

**v0.1 更新说明**

- 代替原有远程附件繁琐的设置步骤，仅需在插件中设置空间，操作员，密码等信息即可使用 UPYUN 服务；
- 将 Discuz 自带的 FTP 上传核心整体替换成 HTTP 上传，提升上传速度、稳定性和兼容性；
- 解决部分客户由于虚拟主机权限限制导致无法使用 Discuz 自带 FTP 远程附件的问题；
- 上传的图片和文件分空间存放，并可指定不同域名；
- 解决下载论坛非图片附件二次流量问题；
- 支持本地图片、文件备份；
- 非图片附件支持 token 防盗链；
- 附带 API 接入点测速小工具；
- 增加手动 API 接入点设置功能，根据测速工具检测结果，选择最快的 API 接入点；


### Discuz X2.5

#### 插件下载

* 最新版插件(v0.4.1) 下载：[Zip 压缩包](http://upyun-assets.b0.upaiyun.com/docs/discuz-plugin/dz_upyun_v0.4.1.zip) | [7z 压缩包](http://upyun-assets.b0.upaiyun.com/docs/discuz-plugin/dz_upyun_v0.4.1.7z)
* 最新版插件(v0.4.1) 安装使用手册下载：[Word 版](http://upyun-assets.b0.upaiyun.com/docs/discuz-plugin/readme_v0.4.1.docx) | [Pdf 版](http://upyun-assets.b0.upaiyun.com/docs/discuz-plugin/readme_v0.4.1.pdf)

#### 版本更新说明

**v0.4.1 更新说明**

- 优化对 PHP 5.2.x 的支持，解决 PHP 5.2.x 版本用户 diy 模块和图片更新出错、帖子无法推送的 bug。
- PHP 5.3.x 版本用户可不更新此版本插件

**v0.4 更新说明**

- 新增手动 API 接入点设置功能，根据测速工具检测结果，选择最快的 API 接入点；
- 新增 API 接入点测速小工具；
- 优化图片类附件下载，实现浏览器下载保存图片附件（之前版本可能会存在浏览器直接打开预览图片的情况）；
- 优化附件下载后文件名命名，避免下载后附件文件名被修改成随机乱码。

**v0.3 更新说明**

- 将 Discuz 自带的 FTP 上传核心整体替换成 HTTP 上传，提升上传速度、稳定性和兼容性；
- 解决部分由于虚拟主机权限限制导致无法使用远程附件的问题；

**v0.2 更新说明**

- 优化上传代码，提升上传速度和效率；
- 修复 discuz 官方远程附件中无法删除远端空间文件的 Bug；
- 由于 discuz 官方远程附件代码 Bug，无法支持自定义路径，所以插件暂时取消自定义文件路径设置功能
- 设置表单中添加密钥默认有效时间 900 秒。

**v0.1 功能介绍**

- 代替原有远程附件繁琐的设置步骤，仅需在插件中设置空间，操作员，密码等信息即可使用 UPYUN 服务；
- 上传的图片和文件分空间存放，并可指定不同域名；
- 解决官方远程附件下载时仍然调用本地附件地址的问题；
- 支持本地图片、文件备份；
- 附件下载支持防盗链。


### 常见问答

**问：**如果文件权限没有修改好就直接安装了插件，该怎么办？
**答：**可以卸载插件，然后修改文件权限，再次安装。或者也可以按照安装说明中附件的文件修改说明，手动修改三个文件的代码


**问：**插件正确安装且参数设置正确，但是上传后显示的地址还是本地的？
**答：**尝试进入后台更新论坛数据缓存


**问：**插件中设置了参数，还需要在远程附件中设置么？
**答：**不需要，插件会自动修改远程附件的设置，所以只需要在插件中设置相关参数即可，远程附件设置表单无需修改
