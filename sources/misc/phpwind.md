Phpwind 用户需要使用 UPYUN 有以下两个方法：

## 通过论坛自带的远程附件功能

使用 phpwind 的远程附件功能，可将论坛附件全部存放到 UPYUN 上。经过 phpwind 官方测试，可正常使用。

1. 前期准备：

    * 在 UPYUN 上创建一个文件类空间。 注意：必须是文件类空间，不能是图片类空间，phpwind 附件含多种类型文件

    * 获取刚才创建空间的 FTP 帐号信息

2. 设置步骤：
    ![](http://upyun-assets.b0.upaiyun.com/docs/misc/pw.png)

3. 论坛原有附件需要通过 ftp 上传到云存储空间的相应位置

4. 到这一步，恭喜您，您的附件已经托管到 UPYUN，这下您不用担心附件的安全及性能问题了。


## 安装使用 UPYUN 官方插件
###  v0.3

* UPYUN 附件助手（PW）v0.3 下载地址：[Zip 压缩包](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_upyun_v0.3.zip) | [7z 压缩包](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_upyun_v0.3.7z)
* UPYUN 附件助手（PW）v0.3 安装使用手册：[Word 版](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_readme_v0.3.docx) | [Pdf 版](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_readme_v0.3.pdf)

### v0.2

* UPYUN 附件助手（PW）v0.2 下载地址：[Zip 压缩包](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_upyun_v0.2.zip) | [7z 压缩包](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_upyun_v0.2.7z)
* UPYUN 附件助手（PW）v0.2 安装使用手册： [Word 版](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_readme_v0.2.docx) | [Pdf 版](http://upyun-assets.b0.upaiyun.com/docs/phpwind-plugin/pw_readme_v0.2.pdf)


### 版本更新说明

**v0.3 更新说明**
完善了新版的兼容问题（针对 20130227 升级包），20130227 升级包前的版本请使用 0.2 版

**v0.2 更新说明**
将原有 FTP 远程附件存储模式修改成 UPYUN 远程附件云存储方式，更高速稳定上传附件和图片到 UPYUN；

**v0.1 更新说明**
无需进行繁琐的远程附件设置，仅需在插件中设置空间，操作员，密码，访问域名，远程目录等信息即可使用 UPYUN 服务；
