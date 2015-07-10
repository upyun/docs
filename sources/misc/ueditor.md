[UEditor](http://ueditor.baidu.com/website/)用户现在也可以把 UPYUN 服务集成进去，轻松把图片和附件上传到你的 UPYUN 空间。

## 更新说明

V0.2 更新：

* 优化 0.1 版的代码，增加完善了错误判断；

## 使用须知

* 已经安装部署百度编辑器 UEditor
* 工具是基于 UPYUN 服务的，使用需要有有效的 UPYUN 帐号，还没有帐号的用户，可以[点击免费注册账号](https://console.upyun.com/#/register/)


## 安装说明

1\. 官网下载工具包[upyun_ueditor.zip](http://upyun-assets.b0.upaiyun.com/docs/ueditor-plugin/upyun_ueditor.zip)；

2\. 解压到任意目录，可得文件夹 upyun-ueditor；

3\.  复制文件 upyun-ueditor/upyun_config.php、upyun-ueditor/upyun.class.php、upyun-ueditor/editor_config.js、upyun-ueditor/index.php 到所部署的 UEditor 根目录下，复制文件 upyun-ueditor/php/fileUp.php、upyun-ueditor/php/imageUp.php 到所部署的 UEditor 根目录下的 php 目录；

4\.  配置 upyun-ueditor/upyun_config.php 中的 UPYUN 空间授权信息

```php
 //UPYUN 图片空间授权
 $img_bucketname = "";    //图片空间名称
 $img_username = "";     //图片空间授权管理员帐号
 $img_password = "";  //图片空间授权管理员密码
 //UPYUN 文件空间授权
 $file_bucketname = "";    //文件空间名称
 $file_username = "";     //文件空间授权管理员帐号
 $file_password = "";  //文件空间授权管理员密码
```
说明：如果不需要将图片和非图片类文件分空间存储，则只需将图片空间和文件空间相关信息填写成同一个文件类空间即可（必须为文件类空间）

5\.  配置 upyun-ueditor/editor_config.js 中的空间名

```php
 var img_bucketname = "";     //UPYUN 图片空间名
 var file_bucketname = "";     //UPYUN 文件空间名
```

说明：如果不需要将图片和非图片类文件分空间存储，则只需将图片空间和文件空间相关信息填写成同一个文件类空间即可（必须为文件类空间）


## 相关下载

* [集成工具包下载](http://upyun-assets.b0.upaiyun.com/docs/ueditor-plugin/upyun_ueditor.zip)
* 安装使用说明文档：
    * [PDF 版本](http://upyun-assets.b0.upaiyun.com/docs/ueditor-plugin/upyun_ueditor__readme_v0.2.pdf)
    * [WORD 版本](http://upyun-assets.b0.upaiyun.com/docs/ueditor-plugin/upyun_ueditor__readme_v0.2.docx)
