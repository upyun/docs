Discuz 用户需要使用 UPYUN 有以下两个方法：

## 通过论坛自带的远程附件功能

使用 Discuz 的远程附件功能，可将论坛附件全部存放到 UPYUN 上。

**具体可按如下步骤：**

1. 前期准备：

    * 在 UPYUN 上创建一个文件类空间。 注意：必须是文件类空间，不能是图片类空间，discuz附件含多种类型文件

    * 获取空间的 FTP 帐号信息。

2. 设置步骤：

    注：目前版本的discuz绑定 UPYUN ，经过discuz官方测试，须增加2行测试代码，discuz官方已在最新的版本中增加，会在下次发布时更新。

    目前您可以手动修改代码或跳过测试远程附件即可。 手动添加方法见帖子最后。

    在论坛后台找到设置远程附件功能，管理后台 => 全局 => 上传设置（或附件设置） => 远程附件：选择“启用远程附件”，然后设置 FTP 帐号信息，如下图所示：
    ![](http://our80.b0.upaiyun.com/wiki/Discuz_%E7%A8%8B%E5%BA%8F%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E5%8F%88%E6%8B%8D%E4%BA%91%E5%AD%98%E5%82%A8_files/u3_normal.png)

3. 论坛原有的附件处理

    如果您想把论坛原有的附件也搬到 UPYUN ，您只要2步操作：

    a、把原有附件通过 FTP 上传到云存储空间的相应位置

    b、把附件表的 remote 字段的属性改为1

4. 到这一步，恭喜您，您的附件已经托管到 UPYUN ，这下您不用担心附件的安全及性能问题了。

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


### Discuz X3

#### 插件下载

* 最新版插件(v0.1) 下载：[Zip 压缩包](http://our80.b0.upaiyun.com/wiki_download/dz3_upyun_v0.1.zip) | [7z 压缩包](http://our80.b0.upaiyun.com/wiki_download/dz3_upyun_v0.1.7z)
* 最新版插件(v0.1) 安装使用手册下载：[Word 版](http://our80.b0.upaiyun.com/wiki_download/dz3_readme_v0.1.docx) | [Pdf 版](http://our80.b0.upaiyun.com/wiki_download/dz3_readme_v0.1.pdf)

#### 版本更新说明

**v0.1 更新说明**

- 代替原有远程附件繁琐的设置步骤，仅需在插件中设置空间，操作员，密码等信息即可使用 UPYUN 服务；
- 将 Discuz 自带的 Ftp 上传核心整体替换成 Http 上传，提升上传速度、稳定性和兼容性；
- 解决部分客户由于虚拟主机权限限制导致无法使用 Discuz 自带 Ftp 远程附件的问题；
- 上传的图片和文件分空间存放，并可指定不同域名；
- 解决下载论坛非图片附件二次流量问题；
- 支持本地图片、文件备份；
- 非图片附件支持 token 防盗链；
- 附带 API 接入点测速小工具；
- 增加手动 API 接入点设置功能，根据测速工具检测结果，选择最快的 API 接入点；


### Discuz X2.5

#### 插件下载

* 最新版插件(v0.4.1) 下载：[Zip 压缩包](http://our80.b0.upaiyun.com/wiki_download/dz_upyun_v0.4.1.zip) | [7z 压缩包](http://our80.b0.upaiyun.com/wiki_download/dz_upyun_v0.4.1.7z)
* 最新版插件(v0.4.1) 安装使用手册下载：[Word 版](http://our80.b0.upaiyun.com/wiki_download/readme_v0.4.1.docx) | [Pdf 版](http://our80.b0.upaiyun.com/wiki_download/readme_v0.4.1.pdf)

#### 版本更新说明

**v0.4.1 更新说明**

- 优化对PHP 5.2.x的支持，解决PHP 5.2.x版本用户diy模块和图片更新出错、帖子无法推送的bug。
- PHP 5.3.x版本用户可不更新此版本插件

**v0.4 更新说明**

- 新增手动API接入点设置功能，根据测速工具检测结果，选择最快的API接入点；
- 新增API接入点测速小工具；
- 优化图片类附件下载，实现浏览器下载保存图片附件（之前版本可能会存在浏览器直接打开预览图片的情况）；
- 优化附件下载后文件名命名，避免下载后附件文件名被修改成随机乱码。

**v0.3 更新说明**

- 将Discuz自带的Ftp上传核心整体替换成Http上传，提升上传速度、稳定性和兼容性；
- 解决部分由于虚拟主机权限限制导致无法使用远程附件的问题；

**v0.2更新说明**

- 优化上传代码，提升上传速度和效率；
- 修复discuz官方远程附件中无法删除远端空间文件的Bug；
- 由于discuz官方远程附件代码Bug，无法支持自定义路径，所以插件暂时取消自定义文件路径设置功能
- 设置表单中添加密钥默认有效时间900秒。

**v0.1功能介绍**

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
