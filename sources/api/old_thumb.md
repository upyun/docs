> ** 该文档为旧版作图参数，目前已不再更新，新版请参考 [新版作图参数](/cloud/image/) **


| 参数                             | 说明                                                                                               |
|----------------------------------|----------------------------------------------------------------------------------------------------|
| `x-gmkerl-type`                  | 缩略类型（具体请参见注 1）                                                     |
| `x-gmkerl-value`                 | 缩略类型对应的参数值，单位为像素，须搭配 `x-gmkerl-type` 使用（具体请参见注 2） |
| `x-gmkerl-quality `              | 图片质量，默认 `95`，可选（1~100）                                                              |
| `x-gmkerl-unsharp`               | 图片锐化，默认 `true` 即开启锐化                                                                           |
| `x-gmkerl-thumbnail`             | 在又拍云管理平台创建好缩略图版本该缩略方式包含了所需的缩略参数，参数更简洁，使用更方便            |
| `x-gmkerl-exif-switch`           | 是否保留原图的 EXIF 信息，默认 `false` 即不保留                                                   |
| `x-gmkerl-crop`                  | 格式为 `x,y,width,height`，如：`0,0,100,200` （具体请参见注 3）                   |
| `x-gmkerl-rotate`                | 旋转角度，目前只允许设置：`auto，90，180，270` （具体请参见注 4）         |


**注 1：x-gmkerl-type 值可选列表**

|           值           |                含义                |
|------------------------|------------------------------------|
| `fix_width`              | 限定宽度，高度自适应                |
| `fix_height`             | 限定高度，宽度自适应                |
| `fix_width_or_height`    | 限定宽度和高度，宽高不足时不缩放   |
| `fix_both`               | 固定宽度和高度，宽高不足时强行缩放 |
| `fix_max`                | 限定最长边，短边自适应              |
| `fix_min`                | 限定最短边，长边自适应              |
| `fix_scale`              | 等比例缩放（1-99）                  |

**注 2：x-gmkerl-value 值的使用**

> * 若 `x-gmkerl-type` 指定为 `fix_width_or_height` 或 `fix_both`，则，`x-gmkerl-value` 值的格式为 `<width>x<height>`，如 `480x576`。
> * 若 `x-gmkerl-type`  为其他的类型，则 `x-gmkerl-value` 只需指定单个数字，如 `42`，意为高宽同为 42。

**注 3：x-gmkerl-crop 值的使用**

> * `(x, y)`左上角坐标；`width`：要裁剪的宽度；`height`：要裁剪的高度。x >= 0 && y >=0 && width > 0 && height > 0 且必须是正整型。
> * 裁剪参数`(x,y)`若大于原图大小，则将`(x,y)`重置为`(0,0)`进行裁剪。
> * `width+x` 若大于原图的宽度，则只裁剪到原图的最大宽度为止，不进行空白画布填充。
> * `heigth+y` 若大于原图的高度，则只裁剪到原图的最大高度为止，不进行空白画布填充。

**注 4：x-gmkerl-rotate 值的使用**

> * 若参数设置为`auto`，则根据原图的 EXIF 信息进行旋转（旋转后将修改原图的 EXIF 信息），其他参数则进行强制旋转。
> * 旋转失败时若参数为`auto`，则忽略错误进行保存操作；其他参数则直接返回错误信息。

#### 水印参数
在上传图片文件时，可以通过添加下面的参数进行添加文字水印的处理

| 参数                             | 说明                                                                                               |
|----------------------------------|----------------------------------------------------------------------------------------------------|
| `x-gmkerl-watermark-text`        | 文字水印内容，必须经过 urlencode 处理                                                                                           |
| `x-gmkerl-watermark-font`   | **默认 `simsun`** 文字水印字体（具体请参见注 1）                                                                                       |
| `x-gmkerl-watermark-size`   | **默认 `32`**；文字水印尺寸，取值必须为整数，单位像素                                                                                       |
| `x-gmkerl-watermark-align`       | **默认 `top,left`** 水印对齐方式 （具体请参见注 2） |
| `x-gmkerl-watermark-margin`      | **默认 `20,20`** 水印边距，格式 `x,y`，既 `水平边距,垂直边距`（如 `20,20`），单位像素                                                                                           |
| `x-gmkerl-watermark-opacity`     | **默认 `0`** 水印透明度，取值范围 0 ~ 100 的整数                                                                                        |
| `x-gmkerl-watermark-color`  | **默认 `#000000`** 文字水印颜色，RGB 值                                                                                       |
| `x-gmkerl-watermark-border` | 文字水印边框，默认无边框，（具体请参见注 3）|

**注 1：x-gmkerl-watermark-font 值可选列表**

> 目前支持的中文字体包括：
>
> * `simsun`：宋体(simsun)
> * `simhei`：黑体 (simhei)
> * `simkai`：楷体 (simkai)
> * `simli`：隶书 (simli)
> * `simyou`：幼圆 (simyou)
> * `simfang`：仿宋 (simfang)

**注 2：x-gmkerl-watermark-align 值的使用**

> 格式 `valign,halign`，既 `垂直对齐,水平对齐`（如 `bottom,right`），可选值包括：
>
> * 垂直对齐：`top`，`middle`，`bottom`
> * 水平对齐：`left`，`center`，`right`

**注 3：x-gmkerl-watermark-border 值的使用**

> 格式 `rgba`，既 `RGB 值透明度`，如 `#cccccccc`
