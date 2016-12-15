## FTP/FTPS

通过 FTP/FTPS 客户端，可以连接云存储的 FTP/FTPS 服务器，实现对云存储的管理。推荐使用 [FileZilla 客户端](https://www.filezilla.cn/download) 。

**FTP/FTPS 登录方式**

> 地址（主机)：

> * 智能选路（推荐）：v0.ftp.upyun.com
> * 电信线路：v1.ftp.upyun.com
> * 联通线路：v2.ftp.upyun.com
> * 移动线路：v3.ftp.upyun.com

> 加密：选择 `不加密` 使用 FTP，选择 `显式的 FTP over TLS` 或 `TLS/SSL 显式加密` 使用 FTPS

> 用户名：`操作员名/服务名`，如 *`operator/mybucket`*

> 密码：操作员的密码

> 端口：21

本文演示 Windows 下的 FTPS 使用。

---------

### 登录服务

打开 FileZilla，按 FTP/FTPS 使用说明进行登录。如下图：

![FTPS 登录](http://upyun-assets.b0.upaiyun.com/docs/storage/ftps_login.png)

---------

### 上传文件 

选定需要上传的文件，拖动到云存储服务区域。拖动前，请注意云存储目录是否正确。

![上传文件](http://upyun-assets.b0.upaiyun.com/docs/storage/ftps_upload.png_/fw/800)

---------

### 下载文件

选定需要下载的文件，拖动到本地目录文件区域。拖动前，请注意云存储目录是否正确。

![下载文件](http://upyun-assets.b0.upaiyun.com/docs/storage/ftps_download.png_/fw/800)

---------

### 删除文件

选定需要删除的文件、目录，点击右键，进行删除。

![删除文件](http://upyun-assets.b0.upaiyun.com/docs/storage/ftps_delete.png_/fw/800)

---------

### 退出服务

关闭连接或退出 FileZilla 即可。

---------

## UPX （命令行工具）

UPX 是又拍云专为开发者设计，基于命令行的云存储管理工具。通过它，可以实现文件上下传、增量文件同步、目录创建删除、文件删除（包括异步文件批量删除）。

在使用 UPX 之前，需要下载它，[点此访问到 Github 下载 UPX](https://github.com/polym/upx)。下载它后，通过终端使用。

本文演示 Windows DOS 窗口下 UPX 使用，Linux 的使用请[观看视频](https://techs.b0.upaiyun.com/videos/cdnpage/upx.html)。 

---------

### 语法

```sh
upx [global options] command [command options]
```

**选项**

| command 			|        含义                                  	   |
|-------------------|----------------------------------------------------|
| login	      		| 登录又拍云存储                 					   |
| pwd	     		| 显示当前所处目录                         				|
| ls      		    | 显示当前目录中的文件和目录信息，加 `–d`， 仅显示目录信息		|
| cd	    		| 改变工作目录（进入一个目录），加 `..`，回到上一级目录 		|
| get	      		| 下载一个文件或目录										|
| put	      		| 上传一个文件或目录										|
| sync        		| 增量文件同步，类似 rsync								    |
| mkdir       		| 创建目录												|
| rm          		| 删除文件，支持通配符 `*`；加 `–d`，删除目录；加 `–a`，删除目录和文件；加 `–async`，异步方式删除文件或目录 	|
| info	      		| 显示登录的服务名、操作员信息和当前所在目录				|
| logout      		| 退出又拍云存储										|

| global options	|        含义                                  	   |
|-------------------|----------------------------------------------------|
| -h					| 显示帮助信息										|
| -v					| 显示 UPX 版本信息或处理过程信息						|

---------

### 访问服务

![访问服务](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_login.png)

```
// 登录方式一
upx-windows-amd64-v0.1.4.exe login
// 依次输入服务名、操作员、操作员的密码
ServiceName: <bucket>
Operator: <operator>
Password: <operator_password>

// 登录方式二
upx-windows-amd64-v0.1.4.exe login <bucket> <operator> <operator_password>
```
![访问服务](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_login2.png)

```
// 登录方式三，用于多操作员、多服务登录
upx-windows-amd64-v0.1.4.exe login <bucket> <operator> <operator_password>
// 生成基于服务名、操作员、操作员密码的秘钥
upx-windows-amd64-v0.1.4.exe auth <bucket> <operator> <operator_password>
// 执行操作时，加上 --auth <key>，操作基于秘钥的身份信息进行
upx-windows-amd64-v0.1.4.exe info --auth <key>
```

---------

### 上传文件

![上传文件](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_upload.png)

```
// 上传文件
upx-windows-amd64-v0.1.4.exe put <本地路径> <云存储路径>
// 获取服务当前目录下的文件（含目录）列表
upx-windows-amd64-v0.1.4.exe ls
```

---------

### 下载文件

![下载文件](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_download.png)

```
// 下载文件
upx-windows-amd64-v0.1.4.exe get <云存储路径> <本地路径>
```

---------

### 增量文件同步

![增量文件同步](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_sync.png)

```
// 增量文件同步,加 -v,显示增量文件同步过程信息
upx-windows-amd64-v0.1.4.exe sync <本地目录> <云存储目录> -v
```

---------

### 创建目录

![创建目录](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_create_directory.png)

```
// 显示当前所在目录
upx-windows-amd64-v0.1.4.exe pwd
// 在当前目录下，创建目录
upx-windows-amd64-v0.1.4.exe mkdir <目录名称>
```

---------

### 删除文件、目录

文件或目录删除后，不可恢复，请慎重操作！

![删除文件、目录](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_delete.png)

```
// 删除文件
upx-windows-amd64-v0.1.4.exe rm <文件名>
// 加 -d，删除目录
upx-windows-amd64-v0.1.4.exe rm -d <目录名>
// 异步文件删除，通配符匹配，批量删除
upx-windows-amd64-v0.1.4.exe rm --async /*
```

---------

### 退出服务

![退出服务](http://upyun-assets.b0.upaiyun.com/docs/storage/upx_logout.png)

```
// 退出服务
upx-windows-amd64-v0.1.4.exe logout
```

---------

## cURL（命令行工具）

cURL 是一个利用 URL 语法在命令行下工作的文件传输工具。借助 cURL，使用 [REST API](/api/rest_api/) 、[基本认证](/api/authorization/)进行存储管理，包括列目录、文件上下传、目录创建删除、文件删除等。

在使用 cURL 之前，请确保您的终端支持它。

本文演示 Windows DOS 窗口下 cURL 使用，Linux 的使用请[观看视频](https://techs.b0.upaiyun.com/videos/cdnpage/curl_http_rest_api.html)。 

---------

### 访问服务

![访问服务](http://upyun-assets.b0.upaiyun.com/docs/storage/curl_access.png)

```
// 访问服务，列出服务根目录下文件信息，如果没有文件信息，则为空
curl http://v0.api.upyun.com/<bucket>/ -u <operator>:<password> -v
```

---------

### 上传文件

![上传文件](http://upyun-assets.b0.upaiyun.com/docs/storage/curl_upload.png)

```
// 上传文件
curl -T <上传文件路径> http://v0.api.upyun.com/<bucket>/<文件路径> -u <operator>:<password> -v
```

---------

### 下载文件

![下载文件](http://upyun-assets.b0.upaiyun.com/docs/storage/curl_download.png)

```
// 下载文件
curl http://v0.api.upyun.com/<bucket>/<文件> -o <文件本地保存路径> -u <operator>:<password> -v
```

---------

### 获取文件信息

![获取文件信息](http://upyun-assets.b0.upaiyun.com/docs/storage/curl_info.png)

```
//获取文件/目录信息
curl http://v0.api.upyun.com/<bucket>/<文件/目录名> -X HEAD -u <operator>:<password> -v
// 返回信息
x-upyun-file-type: <file 表示文件，folder 表示文件夹>	
x-upyun-file-size: <文件/文件夹大小>
x-upyun-file-date: <创建时间，Unix 时间戳>
```

---------

### 删除文件

![删除文件](http://upyun-assets.b0.upaiyun.com/docs/storage/curl_delete.png)

```
//删除文件/目录，目录必须为空目录，才能删除
curl http://v0.api.upyun.com/<bucket>/<文件/目录名> -X DELETE -u <operator>:<password> -v
```

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)



