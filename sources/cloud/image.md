## 快速入门

又拍云处理（图片处理）基于 CDN 或云存储服务，您在使用它之前，请确保您已经注册又拍云账号并完成实名验证，请确保您已经创建 [CDN 服务](/cdn/guide/)或[云存储服务](/api/quick_start/)。

收费方面，图片处理完全免费。

WebP 演示 DEMO：[让您的图片瘦身 70%](https://www.upyun.com/webp.html)。

---------

## 开发者指南

### URL 作图

通过 URL 访问图片时，对图片进行处理，并把处理后的图片返回。

** URL 作图的格式 **

```
图片 URL + 间隔标识符 + 参数或缩略图版本
```

间隔标识符说明，见[间隔标识符](#tag)；参数说明，见[功能](#function)；缩略图版本说明，见[缩略图版本](#thumb)。

** 例如 **

图片 URL 是 <a href="https://p.upyun.com/docs/cloud/demo.jpg" target="_blank" title="查看">`https://p.upyun.com/docs/cloud/demo.jpg`</a>，

间隔标识符是 `!`，

功能是 `格式转换成 webp`，

访问的 URL 是 <a href="https://p.upyun.com/docs/cloud/demo.jpg!/format/webp" target="_blank" title="查看">`https://p.upyun.com/docs/cloud/demo.jpg!/format/webp`</a>。

---------

<a name="tag"></a>
### 间隔标识符

用于分隔图片 URL 和处理信息，有 3 种可选，分别是：`!`（感叹号/默认值）、`-`（中划线）和 `_`（下划线），可登录又拍云<a href="https://console.upyun.com/services/" target="_blank">控制台</a>，在 「服务」 > 「功能配置」 > 「云处理」 中设置。

---------

<a name="thumb"></a>
###　缩略图版本

缩略图版本在又拍云<a href="https://console.upyun.com/services/" target="_blank">控制台</a>，「服务」 > 「功能配置」 > 「云处理」 中创建，它里面包含许多图片处理功能。如下图：

<a href="http://upyun-assets.b0.upaiyun.com/docs/process/image_version.png"  target="_blank" title="查看大图"><img src="http://upyun-assets.b0.upaiyun.com/docs/process/image_version.png" height="490" width="800" alt="缩略图版本" /></a>


** 例如 **

缩略图版本名称是 `upyun520`，

访问的 URL 是 <a href="https://p.upyun.com/docs/cloud/demo.jpg!upyun520" target="_blank" title="查看">`https://p.upyun.com/docs/cloud/demo.jpg!upyun520`</a>。

其中，`!` 是[间隔标识符](#tag)。

---------

<a name="sync_upload_process"></a>
### 上传预处理（同步）

在上传图片时，对上传的图片进行处理，并保存处理后的图片到又拍云存储。

支持的 API： [REST API](/api/rest_api/#_2) | [FORM API](/api/form_api/#upload_args) | [文件拉取](/cloud/spider/)。

参数名是 `x-gmkerl-thumb`，参数值是 `参数或缩略图版本`。处理参数见[功能](#function)。

** 举例 **

例 1：转换图片格式为 png

```
x-gmkerl-thumb: /format/png
```

例 2：限定图片宽度为 300px、锐化、压缩质量 80、存储为 png 格式（参数不区分先后顺序）

```
x-gmkerl-thumb: /fw/300/unsharp/true/quality/80/format/png
```

---------

<a name="async_upload_process"></a>
### 上传预处理（异步）

在上传图片时，对上传的图片进行处理，生成一张或多张新的图片，同时保存上传的图片到又拍云存储。任务以异步的方式处理，处理完成后，回调通知用户处理结果。

支持的 API： [FORM API](/api/form_api/#upload_args)。

参数名是 `apps`，参数值是 JSON 字符串。一个 `apps` 最多允许包含 10 个图片处理任务。处理参数见[功能](#function)。

** apps 参数结构 **

```
apps = [
	{
		"name": "thumb",                        // 异步任务名称，必填。thumb 表示异步图片处理服务
        "x-gmkerl-thumb": "<参数或缩略图版本>",	// 必填
	    "x-gmkerl-split": "<w>x<h>",				//图片分块，按宽x高把图片分成数块
        "save_as": "<save_as>",           		// 结果图片保存路径，选填
		"notify_url": "<notify_url>"     		// 回调地址，不填时使用上传参数中的  notify_url
  	},
	{                                              // 举例
        "name": "thumb",
        "x-gmkerl-thumb": "/fw/300/quality/95",    
        "save_as": "/path/to/fw_300.jpg",          
        "notify_url": "http://your/notify/url"     
  	},
	......
]
```

** 注 **

- `x-gmkerl-split` 会在 `x-gmkerl-thumb` 的基础上把图片分成数块，用户可以根据回调信息中的行号跟列号来确定分块后的图片在原图中的位置。
- `x-gmkerl-split` 会输出多个图片，各图片存放路径为 `$save_as-图片序号.文件后缀名`。例如， `save_as` 为 `/upyun/img.jpg`，输出 3 张图片的存放路径为 `/upyun/img.jpg-1.jpg` 、 `/upyun/img.jpg-2.jpg` 、 `/upyun/img.jpg-3.jpg`。

** 回调通知 **

任务处理完成后，向 `notify_url` 地址发送回调通知。回调信息为 JSON 格式，字段名及含义如下：

|  字段名       |   类型    |          说明             |
|---------------|---------- |---------------------------|
| `task_id`     |  string   | 异步处理任务 ID（在上传时返回）|
| `service` 	|  string   | 文件所在空间名 				|
| `status_code` |  integer  | 处理结果状态码，`200` 表示成功处理 |
| `imginfo`     |  map      | 输出单张图片信息 			|
| `imginfos`    |  array    | 输出多张图片信息 			|
| `error`       |  string   | 错误信息   				|

`imginfo` 、 `imginfos` 中包含信息如下：

|  字段名   |   类型    |          说明             |
|-----------|-----------|---------------------------|
| `path`    |  string   |  作图结果保存路径（`save_as`参数指定）         |
| `type`    |  string   |  图片类型        |
| `width`   |  integer  |  图片宽度        |
| `height`  |  integer  |  图片高度        |
| `frame`   |  integer  |  图片帧数        |
| `row`     |  integer  |  相对于左上角的行号，从 `0` 开始，仅当图片分块时有效 |
| `column`  |  integer  |  相对于左上角的列号，从 `0` 开始，仅当图片分块时有效 |

** 举例 **

例 1：输出单张图片的回调信息

```json
{
    "task_id": "b52c96bea30646abf8170f333bbd42b9",
    "service": "upyun-demo",
    "status_code": 200,
    "imginfo": {
        "path": "/images/upyun.jpg",
        "type": "jpg",
        "width": 720,
        "height": 360,
        "frames": 1
    }
}
```

例 2：输出多张图片（使用了[图片分块 x-gmkerl-split](#async_process)） 的回调信息

```json
{
    "task_id": "b52c96bea30646abf8170f333bbd42b9",
    "service": "upyun-demo",
    "status_code": 200,
    "imginfos": [
      {
        "path": "/images/upyun.jpg-1.jpg",
        "type": "jpg",
        "width": 72,
        "height": 36,
        "frames": 1,
        "row": 0,
        "column": 0
      },
      {
        "path": "/images/upyun.jpg-2.jpg",
        "type": "jpg",
        "width": 72,
        "height": 36,
        "frames": 1,
        "row": 0,
        "column": 1
      }
    ]
}
```

---------

### 原图保护秘钥

- 上传图片时，保护秘钥通过 [REST API](/api/rest_api/#_2) | [FORM API](/api/form_api/#_2) 的 `Content-Secret` 设置。
- 已经存在存储的图片，可以通过编辑 [Metadata](/api/rest_api/#metadata_2) 设置保护密钥。

图片添加保护秘钥后，访问时需要带上 「[间隔标识符](#tag)」 和 「保护密钥」，才能访问图片。

例如，保护秘钥是 `abc`，

访问图片 URL 是 <a href="https://p.upyun.com/docs/cloud/secret.jpg!abc" target="_blank" title="查看">`https://p.upyun.com/docs/cloud/secret.jpg!abc`</a>。

如果保护秘钥跟缩略图版本名称相同，当作保护密钥。特别地，对有保护秘钥的图片进行处理时，不需要加保护秘钥。

---------

### 混合使用

假设存在缩略图版本 `upyun520`，现在针对某类图片需要调整缩略图版本的配置，比如限定宽度为 500px（`/fw/500`），可以使用缩略图版本与参数混合，通过参数对缩略图版本进行动态调整。因为，参数的优先级高于缩略图版本。

URL 作图混合使用是：

<a href="https://p.upyun.com/docs/cloud/demo.jpg!upyun520/fw/500" target="_blank" title="查看">
```
https://p.upyun.com/docs/cloud/demo.jpg!upyun520/fw/500
```
</a> 

上传预处理混合使用是： 

```
x-gmkerl-thumb: upyun520/fw/500
```

特别地，参数只能出现在缩略图版本的后面，如 `/fw/500` 出现在 `upyun520` 后面。

---------

### 状态码表

|  状态码  	|   说明        										|
|-----------|---------------------------------------------------|
| 200       | 处理成功    |
| 400       | 参数错误，返回 JSON 中包含具体错误信息    |
| 404       | 指定 Profile 信息不存，Not Profile    |
| 405       | 图片数据无效，Not an image    |
| 405       | 图片宽高超出限制，Image Width/Height Limit Error    |
| 406       | 非图片获取图片基本信息时数据无效，Not an image    |
| 409       | 图片大小超出限制，限制为 2 亿像素，Limit Exceeded    |
| 413       | 请求处理超时，A Big Image!    |
| 5xx       | 服务端错误。如遇此类错误，请反馈给[售后](https://www.upyun.com/about_contact.html)或您的商务经理 |

上传预处理的更多错误码，请查询 [API 错误码表](/api/errno/#api)。

---------

### 支持格式

| 类型      		| 值      														|
|---------------|-----------------------------------------------------------------------|
| 输入格式 		| JPG、JPEG、PNG、WebP、动态 WebP、GIF、动态 GIF、BMP、SVG等 			| 
| 输出格式 		| JPG、PNG、WebP、动态 WebP 								|

** 其他约束 **

- 图片宽或高最大不能超过 2 万像素，「宽 * 高 * 帧数」最大不能超过 2 亿。

---------

### WebP 专题

WebP 演示 DEMO：[让您的图片瘦身 70%](https://www.upyun.com/webp.html)。

JPG 格式图片转成 WebP：www.domain.com/a.jpg!/format/webp

PNG 格式图片转成 WebP：www.domain.com/a.png!/format/webp

GIF/动态 GIF 格式图片转成 WebP：www.domain.com/a.gif!/format/webp

`!` 是间隔标识符，具体说明见[间隔标识符](#tag)；`/format/webp` 是格式转换成 WebP，具体说明见[结果输出](#output)。

---------

<a name="function"></a>
## 功能

### 缩小&放大

| 参数 				 | 值                 	| 说明                                                     |
|--------------------|----------------------|----------------------------------------------------------|
| `/fw/<width>`      | 宽，如 300           	| 限定宽度，高度自适应                                   |
| `/fh/<height>`     | 高，如 200           	| 限定高度，宽度自适应                                    |
| `/max/<max>`   	 | 最长边，如 200       	| 限定最长边，短边自适应                                 |
| `/min/<min>`   	 | 最短边，如 200        	| 限定最短边，长边自适应                                   |
| `/fwfh/<w>x<h>`  	 | 宽x高，如 300x200     | 限定宽度或高度，宽高不足时不缩放  			          |
| `/both/<w>x<h>`  	 | 宽x高，如 300x200     | 固定宽度和高度，宽高不足时居中裁剪再缩放 <br /> 特别地，配合 `/force/true` 使用时，宽高不足时只缩放，不裁剪   |
| `/sq/<w>`          | 宽或高，如 300        	| 图片缩放成正方形，宽高相等                      		|
| `/scale/<scale>`   | 缩放比例，如 50       	| 宽高等比例缩放，取值范围 `[1-1000]`                  	|
| `/wscale/<wscale>` | 宽度缩放比例，如 200   | 宽度按比例缩放，高度不变，取值范围 `[1-1000]`         	|
| `/hscale/<hscale>` | 高度缩放比例，如 200   | 高度按比例缩放，宽度不变，取值范围 `[1-1000]`          	|
| `/fxfn/<w>x<h>`    | 宽x高，如 300x200     | 限定长边或短边，进行等比缩放，不裁剪               	|
| `/fxfn2/<w>x<h>`   | 宽x高，如 300x200     | 限定长边或短边的最小值，进行等比缩放，不裁剪               |
| `/fp/<integer>`    | 宽高像素积，如 200000 	| 宽高等比例缩放，直到宽高像素积小于但最接近指定值，取值范围 `[1-25000000]` |
| `/force/<boolean>` | true                 | 不支持放大的参数，指定 `/force/` 为 `true` 进行放大，默认 `false` |

** 注 **

- `fwfh` 当原图宽与期望缩放的宽的比例大于原图高与期望缩放的高的比例时，是 `fw`；否则，是 `fh`。
- `scale` 值取 `20` 时，缩小后的图片宽高是原图宽高的 `20%`；取 `200` 时，放大后的图片宽高是原图宽高的 `200%`。
- 特别地，大部分参数默认不支持放大，如果需要放大，请指定 `/force/true` 。
- `<w>x<h>` 中的 `x` 是小写字母 x，不是乘号。

---------

<a name="crop"></a>
### 裁剪

| 参数 					  	| 值   			    			| 说明                     			|
|---------------------------|-------------------------------|-----------------------------------|
| `/crop/<w>x<h>a<x>a<y>`  	| 宽x高axay，如 300x200a80a60 	| 缩小或放大前进行裁剪 				|
| `/clip/<w>x<h>a<x>a<y>`  	| 宽x高axay，如 300x200a80a60 	| 缩小或放大后进行裁剪					|
| `/gravity/<gravity>`		| 位置，如 north  				| 裁剪开始的方位，默认 `northwest`，详见[方位说明](#align_gravity) |
| `/roundrect/<roundrect>`  | 圆角半径，如 20					| 裁剪时对四角进行圆化（圆角裁剪），默认 `10`|

** 注 **

- `<w>x<h>` 中的 `x` 是小写字母 x，不是乘号。当 `<w>x<h>` 是 `0x0` 时，自动根据偏移量计算裁剪图片宽、高。
- `a<x>s<y>` 中的 `<x>`、`<y>` 表示偏移量，`a`、`s` 表示正、负，`x` 正负判断依据是：往 `east` 方向偏移，为 `a`；往 `west` 方向偏移，为 `s`; `y` 正负判断依据是：往 `south` 方向偏移，为 `a`；往 `north` 方向偏移，为 `s`。

---------

### 水印

** 图片水印 **

| 参数        			| 值                          						| 说明                         	|
|-----------------------|---------------------------------------------------|-------------------------------|
| `/watermark/url/<url>`| 编码字符串，如 `L3BhdGgvdG8vd2F0ZXJtYXJrLnBuZw==` 	| 水印图片的 URI，示例为 `/path/to/watermark.png` 的 Base64 编码字符串。特别地，水印图片必须放在图片所在服务名下。  |
| `/align/<align>`     	| 位置，如 north                                   	| 水印图片放置方位，默认 `northwest`，详见[方位说明](#align_gravity)       |
| `/margin/<x>x<y>`    	| 横偏移x纵偏移，如 15x10                           	| 水印图片横纵相对偏移，默认 `20x20`        |
| `/opacity/<opacity>`  | 透明度，如 90                                    	| 水印图片透明度，默认 `100`，取值范围 `[0-100]`，值越大越不透明，`0` 完全透明，`100` 完全不透明 |
| `/percent/<integer>`	| 百分比值，如 50                                    	| 水印图片自适应原图短边的比例，取值范围 `[0-100]`，默认 `0`，`0` 表示不设置该参数 |
| `/animate/<boolean>`	| true                                            	| 允许对动态图片加水印，默认 `false`  |


** 文字水印 **

| 参数         			| 值                   				| 说明                         |
|-----------------------|-----------------------------------|---------------------------------------|
| `/watermark//text/<text>`| 文字内容，如 `5L2g5aW977yB`        	| 文字内容，示例中为 `你好！`的 Base64 编码字符串。     |
| `/size/<size>`      	| 大小，如 16                        | 文字大小，单位 px，默认 `32`               |
| `/font/<font>`      	| 字体，如 simsun（宋体）            	| 文字字体，默认 `simsun`。字体使用时，需要用参数名。参数名见[字体列表](#font_list)  |
| `/color/<color>`     	| RRGGBB，如 FF0000（红色）          	| 字体颜色，默认 `000000（黑色）`            |
| `/border/<border>`    | RRGGBBAA，如 FF000000（不透明红色） 	| 文字描边，默认 `FFFFFFFF（透明白色）`，详见 [border 说明](#border) |
| `/align/<align>`     	| 位置，如 north                     | 文字放置方位，默认 `northwest`，详见[方位说明](#align_gravity)			|
| `/margin/<x>x<y>`    	| 横偏移x纵偏移，如 15x10             | 文字横纵相对偏移，默认 `20x20`             |
| `/opacity/<opacity>`  | 透明度，如 90                      	| 文字透明度，默认 `100`，取值范围 `[0-100]`，值越大越不透明，`0` 完全透明，`100` 完全不透明 |
| `/animate/<boolean>` 	| true                              | 允许对动态图片加水印，默认 `false`   		|


** 多个水印 **

把图片水印或文字水印参数重复。例如，一个文字水印 + 一个图片水印：

<a href="https://p.upyun.com/docs/cloud/demo.jpg!/watermark/text/5L2g5aW977yB/font/simhei/watermark/url/L2RvY3MvY2xvdWQvdXB5dW4tbG9nby5wbmc=/align/southeast" target="_blank" title="查看演示">
```
/watermark/text/5L2g5aW977yB/font/simhei/watermark/url/L2RvY3MvY2xvdWQvdXB5dW4tbG9nby5wbmc=/align/southeast
```
</a>

特别地，水印个数越多，处理耗时越长，建议不要超过 3 个。

** 注 **

- 图片水印的 `url` 需要 base64 编码，并把编码后的字符串中的 `/`（斜杠）替换成 `|`（竖线）。
- <a name="align_gravity"></a>`align` 或 `gravity` 的 9 个方位：

```
    northwest     |     north      |     northeast
                  |                |
                  |                |
    --------------+----------------+--------------
                  |                |
    west          |     center     |          east
                  |                |
    --------------+----------------+--------------
                  |                |
                  |                |
    southwest     |     south      |     southeast
```
- 文字水印的 `text` 需要 base64 编码，并把编码后的字符串中的 `/`（斜杠）替换成 `|`（竖线）。
- 文字水印的中含中文内容（`text`）时，字体（`font`）请使用中文字体，否则会乱码。
- <a name="font_list"></a>字体列表：

| 名称             	| 类型      	| 参数名   		|
|-------------------|-----------|---------------|
| 宋体              	| 中文字体   | simsun      	|
| 黑体              	| 中文字体   | simhei      	|
| 楷体              	| 中文字体   | simkai      	|
| 隶书              	| 中文字体   | simli       	|
| 幼圆              	| 中文字体   | simyou      	|
| 仿宋          		| 中文字体   | simfang     	|
| 简体中文          	| 中文字体   | sc          	|
| 繁体中文          	| 中文字体   | tc          	|
| Arial            	| 英文字体   | arial       	|
| Georgia          	| 英文字体   | georgia     	|
| Helvetica        	| 英文字体   | helvetica   	|
| Times-New-Roman  	| 英文字体   | roman       	|

- <a name="border"></a>`border` 的值 `RRGGBBAA`，`RRGGBB` 表示边框颜色；`AA` 表示不透明度，取值 `[0-255]`，值越大越透明，`00` 表示完全不透明，`FF` 表示完全透明。
- `<x>x<y>` 中，连接 `<x>`与`<y>`是小写字母 x，不是乘号。

---------

<a name="rotate"></a>
### 旋转

| 参数       		| 值                		| 说明   		            |
|-------------------|-----------------------|---------------------------|
| `/rotate/<固定值>` | auto                 	| 自动扶正                   |
| `/rotate/<angle>`	| `(0, 360]`，如 90°  	| 按角度旋转                 	|

** 翻转 **

| 参数        			| 值                		| 说明          			                	|
|-----------------------|-----------------------|-------------------------------------------|
| `/flip/<orientation>` | 方向，如 `left,right`  | 翻转方向，可选值：`left,right`、`top,down` 	|

** 注 **

- `left,right` 表示从左向右翻转，`top,down` 表示从上向下翻转。

---------

### 锐化

提高图片模糊部位的清晰度或聚焦程度。

| 参数        			| 值                	|          说明               |
|-----------------------|-------------------|-----------------------------|
| `/unsharp/<boolean>`  | true              | 锐化，默认 `false`            |

---------

### 高斯模糊

减少图片噪音和降低图片细节层次，使图片模糊化。

| 参数         					| 值                    	| 说明                  	|
|-------------------------------|-----------------------|-----------------------|
| `/gaussblur/<radius>x<sigma>` | 模糊半径x标准差，如 5x2 	| 高斯模糊 				|

** 注 **

- `0 <= radius <= 50` 且 `radius` 是整数，`sigma` 是正整数。当 `radius = 0` 时，`raduis` 根据 `sigma` 自动计算产生。
- `<radius>x<sigma>` 中的 `x` 是小写字母 x，不是乘号。

---------

### 边框

为图片添加边框，支持设置边框颜色。

| 参数      					| 值                              	| 说明			                             |
|---------------------------|-----------------------------------|--------------------------------------------|
| `/border/<w>x<h>`    		| 宽x高，如 3x2        				| 边框尺寸，`w` 表示左右边框宽度，`h` 表示上下边框宽度  |
| `/brdcolor/<brdcolor>`	| RRGGBBAA，如 FF000000（红色不透明） 	| 边框颜色和透明度，默认 `FFFFFF00（白色不透明）` |

** 注 **

- `brdcolor` 的值 `RRGGBBAA`，`RRGGBB` 表示边框颜色；`AA` 表示不透明度，取值 `[0-255]`，值越大越透明，`00` 表示完全不透明，`FF` 表示完全透明。
- `<w>x<h>` 中的 `x` 是小写字母 x，不是乘号。

---------

### 画布

为图片添加画布，相当于把图片放入画布中。

| 参数        				| 值                                	| 说明                                           |
|---------------------------|-----------------------------------|---------------------------------------------|
| `/canvas/<w>x<h>a<x>a<y>`	| 宽x高axay，如 600x400a50a20 		| 画布尺寸，`w` 表示画布宽，`h` 表示画布高，`x`、`y` 表示图片左上角在画布中的坐标，如果 `x`、`y` 不存在，则图片在画布中间  |
| `/cvscolor/<cvscolor>`	| RRGGBBAA，如 FF000000（红色不透明） 	| 边框颜色和透明度，默认 FFFFFF00（白色不透明）   |

** 注 **

- `cvscolor` 的值 `RRGGBBAA`，`RRGGBB` 表示边框颜色；`AA` 表示不透明度，取值 `[0-255]`，值越大越透明，`00` 表示完全不透明，`FF` 表示完全透明。
- `<w>x<h>` 中的 `x` 是小写字母 x，不是乘号。

---------

<a name="attribute"></a>
### 属性获取

| 参数       		| 值             	| 说明             					|
|-------------------|-------------------|----------------------------------- |
| `/info`     		| 无               	| 获取基本信息（包括宽、高、格式、帧数）  |
| `/meta`     		| 无               	| 获取 EXIF 信息 + 基本信息      |
| `/meta/<固定值>`  	| iptc             	| 获取 IPTC 信息 + 基本信息      |
| `/meta/<固定值>`  	| all              	| 获取图片所有信息，当前是 EXIF 信息 + IPTC 信息 + 基本信息      |

** 注 **

- 上传预处理时，响应信息中默认包含图片基本信息（`info`），获取其他图片信息，见 [FORM API](/api/form_api/#_2) 中的 `x-gmkerl-type` 参数。
- `/meta/all` 获取到的信息会随着平台支持的信息增多而变化，请在使用时注意。

---------

<a name="output"></a>
### 结果输出

| 参数       				| 值     		 	| 说明            				 |
|---------------------------|-------------------|--------------------------------|
| `/format/<format>`      	| 图片格式，如 jpg	| 输出格式，可选值 `jpg`、`png`、`webp`。`webp` 包含动态 WebP      |
| `/quality/<quality>`     	| 整数值，如 75  		| 设置压缩质量，可选范围`[1-99]`   |
| `/compress/<boolean>`    	| true          	| JPG 、 PNG 大小压缩优化，默认 `false`      	|
| `/coalesce/<boolean>`    	| false         	| 是否填充动态 GIF 图像中共同部分，默认 `true`，见「注」  |
| `/progressive/<boolean>` 	| true          	| JPG 图片渐进式加载，图片加载从模糊到清晰 			|
| `/noicc/<boolean>`       	| true          	| 清除图片 ICC 信息，默认 `false` 					|
| `/strip/<boolean>`       	| true          	| 去除图片所有元信息，包括 EXIF 、ICC 等。默认 `false`  |
| `/gifto/<boolean>`       	| true          	| 多帧 GIF 图片转为单帧 GIF 图片  					|
| `/exifswitch/<boolean>`  	| true          	| 保留 EXIF 信息 ，默认会删除 EXIF 信息      	|

** 注 **

- 一般来说，`quality（质量）` 是 `75`，在这个值压缩大小与图片质量损失得到平衡。
- `/compress/true` 会对 JPG/PNG 进行一次压缩以减少图片体积，同时稍微延长了图片处理时间、降低了图片质量。
- `/noicc/true` 会导致图片质量轻微的下降。
- 大部分动态 GIF 可以通过省略相邻帧之间共同部分来优化图片体积。如果 `coalesce` 参数为 `false`，当对动态 GIF 做缩略或放大时，可能会导致图片质量下降。 

---------

### 主题色提取

| 参数			            | 值	                   	| 说明                                       	|
|---------------------------|-----------------------|-----------------------------------------------|
| `/excolor/<integer>`      | 颜色数量，如 128      	| 提取的颜色数量。可选值：`[1-4096]`               |
| `/exformat/<exformat>` 	| 颜色进制，如 hex       	| 返回颜色的进制，默认 `dec`。可选值：`dec`、`hex`  |

** 注 **

- 以 JSON 格式返回颜色。`hex（十六进制）` 表示颜色为 `0xRRGGBB`，如 `0xff00aa`；`dec（十进制）` 表示颜色为 `12345678`。

---------

### 静态图渐变

| 参数            				| 值                              	| 说明		                      |
|-------------------------------|-----------------------------------|------------------------------------|
| `/gdori/<orientation>`		| 方向，如 top,down（自上而下）       	| 渐变方向             |
| `/gdpos/<gdpos>`        		| 开始位置,结束位置，如 10,100        	| 渐变从开始位置至结束位置。单位像素（px）  |
| `/gdstartcolor/<gdstartcolor>`| RRGGBBAA，如 FF000000（红色不透明） 	| 开始位置颜色及透明度   |
| `/gdstopcolor/<gdstopcolor>`  | RRGGBBAA，如 FF000000（红色不透明） 	| 开始位置颜色及透明度   |

** 注 **

- `gdori` 取值：`top,down`（自上而下）、`bottom,up`（自下而上）、`left,right`（自左向右）、`right,left`（自右向左）。
- `RRGGBBAA`：`RRGGBB` 表示边框颜色；`AA` 表示不透明度，取值 `[0-255]`，值越大越透明，`00` 表示完全不透明，`FF` 表示完全透明。

---------

如有疑问请 [联系我们](https://www.upyun.com/about_contact.html)
