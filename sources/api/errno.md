## 概述

API 请求失败时，服务器会返回一个 json 格式的响应体用于定位具体错误原因。如：
```
{"msg": "file or directory not found", "code": 40400001, "id": "11e4a729d24943409933a5e8314cd32e"}
```
该响应体的字段如下：

* `code`: 错误码。具体含义请见 [API 错误码表](#api)
* `msg`: 对错误的简单描述。
* `id`: 又拍云为该请求生成的唯一 id。

## API 错误码表


| HTTP 状态码 |             返回代码            |                                                含义                                               |
|-------------|---------------------------------|---------------------------------------------------------------------------------------------------|
| 400 |  40011001 | invalid x-gmkerl-type    |
| 400 |  40011002 | invalid x-gmkerl-value    |
| 400 |  40011003 | invalid x-gmkerl-unsharp    |
| 400 |  40011004 | invalid x-gmkerl-quality    |
| 400 |  40011005 | invalid x-gmkerl-exif-switch    |
| 400 |  40011006 | invalid x-gmkerl-gifto    |
| 400 |  40011007 | invalid x-gmkerl-webp    |
| 400 |  40011008 | invalid x-gmkerl-format    |
| 400 |  40011009 | invalid x-gmkerl-crop    |
| 400 |  40011010 | invalid x-gmkerl-rotate    |
| 400 |  40011011 | invalid x-gmkerl-gaussblur    |
| 400 |  40011012 | invalid x-gmkerl-compress    |
| 400 |  40011013 | invalid x-gmkerl-progressive    |
| 400 |  40011014 | invalid x-gmkerl-noicc    |
| 400 |  40011015 | invalid x-gmkerl-watermark-switch    |
| 400 |  40011016 | invalid x-gmkerl-watermark-align    |
| 400 |  40011017 | invalid x-gmkerl-watermark-margin    |
| 400 |  40011018 | invalid x-gmkerl-watermark-opacity    |
| 400 |  40011019 | invalid x-gmkerl-watermark-animate    |
| 400 |  40011020 | invalid x-gmkerl-watermark-font    |
| 400 |  40011021 | invalid x-gmkerl-watermark-text    |
| 400 |  40011022 | invalid x-gmkerl-watermark-color    |
| 400 |  40011023 | invalid x-gmkerl-watermark-border    |
| 400 |  40011024 | invalid x-gmkerl-watermark-size    |
| 400 |  40011025 | invalid x-gmkerl-gradient-orientation    |
| 400 |  40011026 | invalid x-gmkerl-gradient-pos    |
| 400 |  40011027 | invalid x-gmkerl-gradient-startcolor    |
| 400 |  40011028 | invalid x-gmkerl-gradient-stopcolor    |
| 400 |  40011029 | invalid x-gmkerl-extract-func    |
| 400 |  40011030 | invalid x-gmkerl-extract-color-count    |
| 400 |  40011031 | invalid x-gmkerl-extract-offset    |
| 400 |  40011032 | invalid x-gmkerl-extract-palette    |
| 400 |  40011033 | invalid x-gmkerl-canvas    |
| 400 |  40011034 | invalid x-gmkerl-canvas-color    |
| 400 |  40011051 | invalid x-upyun-part-id                  |
| 400 |  40011052 |invalid x-upyun-multi-length                 |
| 400 |  40011053 |invalid x-upyun-multi-type                 |
| 400 |  40011054 | invalid x-upyun-part-size                 |
| 400 |  40011055 | invalid invalid multipart stage              |
| 400 |  40011056 | invalid invalid x-upyun-multi-uuid         |
| 400 |  40011057 | invalid UTF8 Key                      |
| 400 |  40011058 | invalid x-upyun-multi-uuid not found    |
| 400 |  40011059 | file md5 not match                   |
| 400 |  40011060 | invalid x-upyun-part-size                 |
| 400 |  40011061 | part id error                       |
| 400 |  40011062 | part already complete                  | 
| 400 |  40000001 | need save-key    |
| 400 |  40000002 | need body    |
| 400 |  40000003 | need bucket name    |
| 400 |  40000004 | need expiration    |
| 400 |  40000005 | need file    |
| 400 |  40000006 | content md5 not match    |
| 400 |  40000007 | need policy    |
| 400 |  40000008 | need signature    |
| 400 |  40000009 | decode policy error    |
| 400 |  40000010 | data too long for ext-param    |
| 400 |  40000011 | chunked request bodies not supported yet    |
| 400 |  40000012 | write file to fs error    |
| 400 |  40000013 | need content-md5 but no body provided    |
| 400 |  40000014 | need content-md5 but no content provided    |
| 400 |  40000015 | missing required arguments |
| 400 |  40000016 | missing file_hash argument |
| 400 |  40000017 | missing file_blocks argument |
| 400 |  40000018 | too many file blocks |
| 400 |  40000019 | missing file_size argument |
| 400 |  40000020 | missing save_token argument |
| 400 |  40000021 | missing block_index argument |
| 400 |  40000022 | missing block_hash argument |
| 400 |  40000023 | block already exists |
| 400 |  40000024 | block not finished |
| 400 |  40000025 | file size not match |
| 400 |  40000026 | block index out of range |
| 400 |  40000027 | block size too small, at least 100KB |
| 400 |  40000028 | block size too large, at most 5MB |
| 400 |  40000029 | save-key encoding should be utf8 |
| 400 |  40000030 | path encoding should be utf8 |
| 400 |  40000031 | filename should be utf8 |
| 400 |  40000032 | ffmpeg args error |
| 400 |  40000033 | failed to read firstchunk |
| 400 |  40000034 | client error |
| 400 |  40000035 | need purge body |
| 401 |  40100001 | need date header    |
| 401 |  40100002 | date offset error    |
| 401 |  40100003 | unknown realm in authorization header    |
| 401 |  40100004 | need authorization header    |
| 401 |  40100005 | signature error    |
| 401 |  40100006 | user not exists    |
| 401 |  40100007 | user blocked    |
| 401 |  40100008 | user blocked in this bucket    |
| 401 |  40100009 | user password error    |
| 401 |  40100010 | account not exist    |
| 401 |  40100011 | account blocked    |
| 401 |  40100012 | bucket not exist    |
| 401 |  40100013 | bucket blocked    |
| 401 |  40100014 | bucket removed    |
| 401 |  40100015 | bucket read only    |
| 401 |  40100016 | invalid date value in header    |
| 401 |  40100017 | user need permission  |
| 403 |  40300001 | file name contains invalid chars (\r\n\t)    |
| 403 |  40300002 | file path too long    |
| 403 |  40300003 | file name too long    |
| 403 |  40300004 | bucket is full    |
| 403 |  40300005 | directory not empty    |
| 403 |  40300006 | authorization has expired    |
| 403 |  40300007 | content md5 not match    |
| 403 |  40300008 | file too small    |
| 403 |  40300009 | file too large    |
| 403 |  40300010 | file type error    |
| 403 |  40300011 | has no permission to delete    |
| 403 |  40300012 | need content-type    |
| 403 |  40300013 | form api disabled    |
| 403 |  40300014 | order must be asc or desc    |
| 403 |  40300015 | path is not a file, maybe a directory    |
| 403 |  40300016 | content-secret only accept [a-zA-Z0-9]    |
| 403 |  40300017 | no thumb setting found for bucket    |
| 403 |  40300018 | not an image    |
| 403 |  40300019 | image width too small or too big    |
| 403 |  40300020 | image height too small or too big    |
| 403 |  40300021 | image limit exceeded    |
| 403 |  40300022 | invalid image-width-range    |
| 403 |  40300023 | invalid image-height-range    |
| 403 |  40300024 | exceed max size    |
| 403 |  40300025 | wrong content-type header |
| 403 |  40300026 | need content-length header |
| 403 |  40300027 | request body too big |
| 403 |  40300028 | request has expired |
| 403 |  40300029 | purge too much items |
| 404 |  40400001 | file or directory not found    |
| 406 |  40600001 | dir not acceptable    |
| 406 |  40600002 | folder already exists    |
| 408 |  40800001 | read client request timeout |
| 415 |  41500001 | media type error, need content-type    |
| 429 |  42900001 | too many requests    |
| 429 |  42900002 | too many requests of the same uri    |
| 503 |  50300000 | unknown error    |
| 503 |  50300001 | write hub failed    |
| 503 |  50300002 | decode error    |
| 503 |  50300003 | ffmpeg error    |
| 503 |  50300006 | get hub error    |
| 503 |  50300007 | capture file from fs error    |
| 503 |  50300008 | delete hub error    |
| 503 |  50300009 | get body temp file error    |
| 503 |  50300010 | read body temp file error    |
| 503 |  50300011 | capture file from ffmpeg error    |
| 503 |  50300012 | service error in thumb    |
| 503 |  50300013 | put file capture timeout    |
| 503 |  50300014 | get client socket error    |
| 503 |  50300015 | decode image info error    |
| 503 |  50300016 | get image info error    |
| 503 |  50300017 | failed to new md5    |
| 503 |  50300018 | failed to new form    |
| 503 |  50300020 | put file capture error    |
| 503 |  50300021 | put capture broken pipe    |
| 503 |  50300022 | new resource error |
| 503 |  50300023 | connect to db error |
| 503 |  50300024 | get master failed |
| 503 |  50300025 | write info error |
| 503 |  50300026 | get data error |
| 503 |  50300027 | put file to fs error |
| 503 |  50300028 | get file from fs error |
| 503 |  50300029 | unknow error |
| 503 |  50300030 | upstream closed connection |
| 503 |  50300031 | read upstream timeout |
| 503 |  50300032 | wrong data |
| 503 |  50300033 | new resource error |
| 503 |  50300034 | get info errors |
| 503 |  50300035 | decode hub body error  |
