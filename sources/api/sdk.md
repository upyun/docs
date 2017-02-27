## PHP SDK

又拍云 PHP SDK 托管在 [github](https://github.com/upyun/php-sdk) ，它包含如下功能：1）存储管理；2）图片处理；3）音视频处理；4）缓存刷新。本段介绍 PHP SDK 的使用。

### 开发准备

** 运行环境 **

PHP SDK version 3.0，需要 PHP 版本 >= 5.5。您可以通过 `php -v` 命令查看当前的 PHP 版本。

** 安装 **

- Composer 方式

[Composer](http://www.phpcomposer.com/) 是 PHP 的依赖管理工具。您可以在自己的项目中声明所依赖的外部工具库，Composer 会自动帮您安装这些依赖的库文件。

在项目根目录运行：

```shell
composer require upyun/sdk
```

- 源码方式

如果使用 Composer 方式较慢，可以直接下载[ SDK 包](https://github.com/upyun/php-sdk/releases)，放到项目根目录，然后把包导入工程中。

### 初始化

Config（配置）和 Upyun（连接）是 PHP SDK 的初始化类，在操作云存储、云处理之前，需要先实例化它们。

```php
<?php
// 包含文件
require_once('vendor/autoload.php');

use Upyun\Upyun;
use Upyun\Config;

// 创建实例
$bucketConfig = new Config('您的服务名', '您的操作员名', '您的操作员密码');
$client = new Upyun($bucketConfig);
?>
```

说明，为了避免重复，后续 「功能使用」 中的 DEMO 不再包含这段代码，请在使用时注意。


### 功能使用

列表：[文件上传](#php_file_upload)、[文件删除](#php_file_delete)、[目录创建和删除](#php_operator_dir)、[列目录](#php_list_dir)、[获取文件信息](#php_get_fileinfo)、[获取服务容量](#php_get_storage)

<a name="php_file_upload"></a>
** 文件上传、图片处理（同步） **

```
<?php
// 省略初始化代码

// 读文件
$file = fopen('/root/13.jpg', 'r');			
// 上传文件
$res = $client->write('/up/dd.jpg', $file);
// 打印上传结果
print_r($res);
?>
```

说明，`/root/13.jpg` 为本地文件绝对路径，`/up/dd.jpg` 为保存到又拍云存储的路径及文件名。

```
<?php
// 省略初始化代码

// 读文件
$file = fopen('/root/13.jpg', 'r');
// 添加作图参数
$tasks = array('x-gmkerl-thumb' => '/format/png/fw/100');
// 上传文件
$res = $client->write('/up/dd.png', $file, $tasks);
// 打印上传结果
print_r($res);
?>
```

说明，上传图片同时，对图片进行处理，把处理后的图片存进存储。具体详见[图片处理（同步）](/cloud/image/#sync_upload_process)。

<!--
<a name="php_form_upload"></a>
** 表单上传、图片处理（同步） **

<a name="php_Multi_upload"></a>
** 断点续传上传 **


REST API

```
<?php

// 判断文件是否存在
$res=$client->has('/up/dd.mp4');
if($res==true) echo "true";
else if($res==false) echo "false";
else echo $res;

?>
```

** 上传预处理 **

图片处理（同步）、图片处理（异步）、音视频处理（异步）、内容审核（异步）
-->

<a name="php_file_delete"></a>
** 文件删除 **

```
<?php
// 省略初始化代码

// 单文件删除
$res=$client->delete('/up/dd.txt');
echo $res;
?>
```

说明，`/up/dd.txt` 为文件在又拍云存储的路径。结果为 `1`，表示删除成功。异常直接抛出。

<a name="php_operator_dir"></a>
** 目录创建和删除 **

```
<?php
// 省略初始化代码

// 创建目录
$res=$client->createDir('/up/tt/ss');
echo $res;

// 删除空目录
$res=$client->deleteDir('/up/tt/ss');
echo $res;
?>
```

说明，`/up/tt/ss` 为目录的路径。结果为 `1`，表示成功。异常直接抛出。

<!--
<a name="php_list_dir"></a>
** 列目录 **
-->

<a name="php_get_fileinfo"></a>
** 获取文件信息 **

```
<?php
// 省略初始化代码

$res=$client->info('/up/dd.jpg');
print_r($res);
?>
```

说明，`/up/dd.jpg` 为文件在又拍云存储的路径。结果以数组形式返回，内容包括：文件类型、文件大小和文件最后修改时间。

<a name="php_get_storage"></a>
** 获取服务容量 **

```
<?php
// 省略初始化代码

$res=$client->usage();
echo $res;
?>
```

说明，结果为容量数值，单位字节。异常直接抛出。


## Java SDK

又拍云 Java SDK 托管在 [github](https://github.com/upyun/java-sdk) ，它包含如下功能：1）存储管理；2）图片处理；3）音视频处理；4）缓存刷新。本段介绍 Java SDK 的使用。

### 开发准备

** 运行环境 **

Java SDK version 3.0+，需要 Java 版本 >= 6。可以通过 `java -version` 命令查看当前的 Java 版本。

** 安装 **

- Maven 方式

[Maven](https://zh.wikipedia.org/wiki/Apache_Maven) 是一个项目管理及自动构建工具。您可以在自己的项目中声明所依赖的外部工具库，Maven 会自动帮您安装这些依赖的库文件。

```shell
<dependency>
  <groupId>com.upyun</groupId>
  <artifactId>java-sdk</artifactId>
  <version>3.13</version>
</dependency>
```

- 源码方式

除了 Maven 方式外，还可以直接下载[ SDK 包](https://github.com/upyun/java-sdk/releases)，放到项目根目录，然后把包导入工程中。

### 初始化

UpYun 是 Java SDK 的初始化类，在操作云存储、云处理之前，需要先实例化它。

```
// 创建实例
UpYun upyun = new UpYun('您的服务名', '您的操作员名', '您的操作员密码');

// 可选属性1，是否开启 debug 模式，默认不开启
upyun.setDebug(false);
// 可选属性2，超时时间，默认 30s
upyun.setTimeout(30);
```

说明，为了避免重复，后续 「功能使用」 中的 DEMO 不再包含初始化这段代码，请在使用时注意。

### 功能使用

列表：[文件上传](#java_file_upload)、[表单上传](#java_form_upload)、[文件删除](#java_file_delete)、[目录创建和删除](#java_operator_dir)、[列目录](#java_list_dir)、[获取文件信息](#java_get_fileinfo)、[获取服务容量](#java_get_storage)

<a name="java_file_upload"></a>
** 文件上传、批量上传 **

REST API、FORM API

```
// 文件上传
upyun.setContentMD5(UpYun.md5('/root/13.jpg')); 	// 计算文件 MD5，如果文件太大或计算不便，可以不计算
boolean result4 = upyun.writeFile('/up/dd.jpg', '/root/13.jpg');
```

说明，`/root/13.jpg` 为本地文件绝对路径，`/up/dd.jpg` 为保存到又拍云存储的路径及文件名。

<a name="java_form_upload"></a>
** 表单上传 **

```
// 定义保存路径
String savePath = "/uploads/{year}{mon}{day}/{random32}{.suffix}";
paramsMap.put(Params.SAVE_KEY, savePath);

// 上传文件
FormUploader uploader = new FormUploader(BUCKET_NAME, APIKEY, null);
Result result = uploader.upload(paramsMap, file);	// file（File 类型） 表示一个待上传的文件
```

说明，`savePath` 的解释详见 [FORM API 路径设置](/api/form_api/#save-key)。

<!--
<a name="java_Multi_upload"></a>
** 断点续传上传 **

REST API
图片处理（同步）、图片处理（异步）、音视频处理（异步）、内容审核（异步）
-->

** 上传预处理 **

```
// 文件上传
boolean result3 = upyun.writeFile('/up/dd.jpg', '/root/13.jpg');

// 图片上传预处理（同步）
params.put(“x-gmkerl-thumb”,"/fw/300/unsharp/true/quality/80/format/png")
```

<a name="java_file_delete"></a>
** 文件删除 **

```
// 单个文件删除
boolean result = upyun.deleteFile('/up/dd.jpg');
```

说明，`/up/dd.jpg` 为文件的路径。

<a name="java_operator_dir"></a>
** 目录创建和删除 **

```
// 创建目录
boolean result = upyun.mkDir('/up/tt/');

// 删除目录
boolean result = upyun.rmDir('/up/tt/');
```

说明，`/up/tt/` 为目录的路径。

<a name="java_list_dir"></a>
** 列目录 **

```
List<UpYun.FolderItem> items = upyun.readDir('/up/tt/');
```

说明，`/up/tt/` 为目录的路径。结果返回目录下前 10000 个文件列表，超出 10000 个的不会返回，如果需要列出来，需要使用分页参数循环获取，直到取完。

<a name="java_get_fileinfo"></a>
** 获取文件信息 **

```
Map<String,String> result = upyun.getFileInfo('/up/dd.jpg');
```

说明，`/up/dd.jpg` 为文件在又拍云存储的路径。结果以 Map 形式返回，内容包括：文件类型、文件大小和文件最后修改时间。

<a name="java_get_storage"></a>
** 获取空间容量 **

```
long usage = upyun.getBucketUsage();
```

说明，结果为容量数值，单位字节。

## Python SDK

又拍云 Python SDK 托管在 [github](https://github.com/upyun/python-sdk) ，它包含如下功能：1）存储管理；2）图片处理；3）音视频处理；4）缓存刷新。本段介绍 Python SDK 的使用。

### 开发准备

** 运行环境 **

- Python SDK version 2.2+，适用于 Python 2.6、2.7、3.3、3.4 版本。可以通过 `python -V` 命令查看当前的 Python 版本。
- Python SDK version 2.3+，不再直接使用默认标准库 httplib，使用基于 httplib 的 requests 这个第三方库。

** 安装 **

- pip 方式

[pip](https://en.wikipedia.org/wiki/Pip_(package_manager)) 是 Python 的依赖管理工具。您可以在自己的项目中声明所依赖的外部工具库，pip 会自动帮您安装这些依赖的库文件。

安装 requests

```shell
pip install requests
```

安装 Python SDK

```shell
pip install upyun
```

运行测试用例，判断安装成功与否

```
# 配置参数
export UPYUN_BUCKET=<bucket>
export UPYUN_USERNAME=<username>
export UPYUN_PASSWORD=<password>

# 执行测试用例
make init test
```

- 源码方式

除了 pip 方式外，还可以直接下载 [SDK 包](https://github.com/upyun/python-sdk/releases)，放到项目根目录，然后把包导入工程中。

### 初始化

UpYun 是 Python SDK 的初始化类，在操作云存储、云处理之前，需要先实例化它。

```
# 包含文件
import upyun

# 创建实例
up = upyun.UpYun('您的服务名', '您的操作员名', '您的操作员密码')
```

说明，为了避免重复，后续 「功能使用」 中的 DEMO 不再包含初始化这段代码，请在使用时注意。

### 功能使用

列表：[文件上传](#py_file_upload)、[表单上传](#py_form_upload)、[断点续传上传](#py_Multi_upload)、[文件删除](#py_file_delete)、[目录创建和删除](#py_operator_dir)、[列目录](#py_list_dir)、[获取文件信息](#py_get_fileinfo)、[获取服务容量](#py_get_storage)

<a name="py_file_upload"></a>
** 文件上传、图片处理（同步） **

```
/*
* 参数说明
* checksum，表示是否进行 MD5 校验，如果是小文件，建议进行，为 True；如果是大文件，建议不进行，为 False
* headers，表示需要的头信息，见 REST API 上传文件
*/
headers = { 'x-gmkerl-thumb': '/fw/300' }

with open('dd.png', 'rb') as f:
    res = up.put('/up/dd.png', f, checksum=True, headers=headers)
```

说明，上传成功，如果是图片，返回包含图片宽、高、格式、帧数的 Python Dict 对象，如果不是，是空 Python Dict 对象；上传失败，抛出异常。

<a name="py_form_upload"></a>
** 表单上传、图片处理（同步） **

```
// 设定参数
kwargs = { 'allow-file-type': 'jpg,jpeg,png',
           'notify-url': 'http://notify-url/',
		   'x-gmkerl-thumb':'/fw/300' }

// checksum，表示是否进行 MD5 校验，如果是小文件，建议进行，为 True；如果是大文件，建议不进行，为 False
with open('dd.png', 'rb') as f:
    res = up.put('/up/dd.png', f, checksum=True, form=True, **kwargs)
```

说明，上传成功，返回内容见 FORM API 的[通知设置](/api/form_api/#notify_return)；上传失败，抛出异常。

<a name="py_Multi_upload"></a>
** 断点续传上传 **

```
from upyun import FileStore
from upyun import print_reporter

/*
* 参数说明
* need_resume，表示是否需要断点续传，默认 False
* headers，表示需要的头信息，，见 REST API 断点续传
* store，表示断点信息存储位置，默认 memory_store。memory_store 表示存放在内存，FileStore() 表示存放在文件，默认是 ~/.up-python-resume/
* reporter，表示报告上传进度，默认忽略。print_reporter 只是 print 上传进度，有其他需要的用户可以自行实现
*/
with open('dd.png', 'rb') as f:
    res = up.put('/up/dd.png', f, checksum=True, need_resume=True, headers={}, store="memory_store", reporter=print_reporter)
```

说明，上传成功，返回空 Python Dict 对象；上传失败，抛出异常。

<!--
** 上传预处理 **

图片处理（同步）、图片处理（异步）、音视频处理（异步）、内容审核（异步）
-->

<a name="py_file_delete"></a>
** 文件删除 **

```
up.delete('/up/dd.png')	
```

说明，成功，返回 Python None 对象；失败，抛出异常。

<a name="py_operator_dir"></a>
** 目录创建和删除**

```
up.mkdir('/up/temp/')	// 创建

up.delete('/up/temp/')	// 删除
```

说明，成功，返回 Python None 对象；失败，抛出异常。注，目录删除只能删除空目录。

<a name="py_list_dir"></a>
** 列目录 **

```
res = up.getlist('/up/', limit=100, order='asc', begin='')
```

说明，获取成功，返回 Python List 对象；获取失败，抛出异常。

<a name="py_get_fileinfo"></a>
** 获取文件信息 **

```
res = up.getinfo('/up/dd.png')
print res['file-type']
print res['file-size']
print res['file-date']
```

说明，获取成功，返回 Python List 对象，内容包括：文件类型、文件大小和文件最后修改时间；获取失败，抛出异常。

<a name="py_get_storage"></a>
** 获取服务容量 **

```
res = up.usage()
```

说明，获取成功，返回 Python String 对象，单位 Bytes；获取失败，抛出异常。