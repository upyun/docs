这一部分将说明如何快速使用`PHP`向又拍云上创建的服务上传一份文件，通过这个例子，你将学会如何生成签名并调用我们的`REST API`进行工作。

讲解例子之前，假设你已经注册了又拍云的帐号，并创建了服务，分配了操作员。示例中我们架设使用一下服务和操作员（虚构，请先自行注册和创建又拍云服务）：

- 服务名:`demobucket`
- 操作员帐号:`operator`
- 操作员密码:`operatorpassword`


## 准备文件
本示例目标是将本地文件 `assets/bar.txt` 上传到 `demobucket` 服务，并重命名为 `foo.txt`。
所以最终的服务端路径是 `$uri = '/demobucket/foo.txt'`。

```php
    $bucketName = 'demobucket';
    //被上传的文件路径
    $filePath = 'assets/bar.txt';
    $fileSize = filesize($filePath);
    //文件上传到服务器的服务端路径
    $serverPath = 'foo.txt';
    $uri = "/$bucketName/$serverPath";
```

## 生成签名

示例中使用安全的签名认证，强烈建议你在实际的生产环境中都使用这种方式来调用我们的 API。
上传文件接口需要通过 HTTP `PUT` 方法调用，签名生成可以参考如下方式：

```php
    //生成签名时间。得到的日期格式如：Thu, 11 Jul 2014 05:34:12 GMT
    $date = gmdate('D, d M Y H:i:s \G\M\T');
    //本示例只上传了文件，所以 HTTP BODY 长度等于文件长度
    $contentLength = $fileSize;
    $sign = md5("PUT&{$uri}&{$date}&{$contentLength}&".md5($operatorPwd));
```

## 发送请求

将上一步生成的签名，放到 HTTP 消息头中。如果没有签名或者签名错误，则会导致接口调用失败，服务端会返回 HTTP 状态码 `401`

格式如下：
```php
    $headers = array(
        "Expect:",
        "Date: ".$date, // header 中使用的时间必须和生成签名的时间相同
        "Authorization: UpYun $operatorName:".$sign
    );
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
```

## 完整代码

通过 php `curl` 发送`put`请求，调用接口。倘若接口返回的 HTTP 状态码等于`200`，说明接口调用成功
- `CURLOPT_INFILE` 设置上传文件的句柄
- `CURLOPT_INFILESIZE` 设置上传文件大小

最终项目结构如下

```
/-
 |-assets
   |-bar.txt
 |-upload.php
```

`upload.php`完整代码如下：
```php
<?php
    $bucketName   = 'demobucket';
    $operatorName = 'operator';
    $operatorPwd  = 'operatorpassword';

    //被上传的文件路径
    $filePath = 'assets/bar.txt';
    $fileSize = filesize($filePath);
    //文件上传到服务器的服务端路径
    $serverPath = 'foo.txt';
    $uri = "/$bucketName/$serverPath";


    //生成签名时间。得到的日期格式如：Thu, 11 Jul 2014 05:34:12 GMT
    $date = gmdate('D, d M Y H:i:s \G\M\T');
    $sign = md5("PUT&{$uri}&{$date}&{$fileSize}&".md5($operatorPwd));

    $ch = curl_init('http://v0.api.upyun.com' . $uri);

    $headers = array(
        "Expect:",
        "Date: ".$date, // header 中需要使用生成签名的时间
        "Authorization: UpYun $operatorName:".$sign
    );
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt($ch, CURLOPT_PUT, true);

    $fh = fopen($filePath, 'rb');
    curl_setopt($ch, CURLOPT_INFILE, $fh);
    curl_setopt($ch, CURLOPT_INFILESIZE, $fileSize);
    curl_setopt($ch, CURLOPT_TIMEOUT, 60);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);

    $result = curl_exec($ch);
    if(curl_getinfo($ch, CURLINFO_HTTP_CODE) === 200) {
        //"上传成功"

    } else {
        $errorMessage = sprintf("UPYUN API ERROR:%s", $result);
        echo $errorMessage;
    }
    curl_close($ch);
```

至此，您已经学会了如何基于 PHP，使用又拍云的 HTTP REST API 上传文件了，您可以访问 [HTTP REST API 文档](/api/rest_api/) 深入学习 REST API 的其他功能和参数。

或者，您也可以前往 [资源下载](/download/#sdk) 页面下载我们所提供的 SDK，来加速你的开发流程。
