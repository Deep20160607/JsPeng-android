PNG 压缩、如低端机图片使用 RGB565 解码

1、位深和色深有什么区别，他们是一个东西吗？
2、为什么Bitmap不能直接保存，Bitmap和PNG、JPG到底是什么关系？
3、图片占用的内存大小公式：图片分辨率 * 每个像素点大小，这种说法正确吗？
4、为什么有时候同一个 app，app 内的同个界面上的同张图片，但在不同设备上所耗内存却不一样？
5、同一张图片，在界面上显示的控件大小不同时，它的内存大小也会跟随着改变吗？

jpg、gif、png和svg用于web上，我们该如何选择最合适的图像格式:https://juejin.cn/post/6912217043009798157
***
- 有损(Lossless)和无损(Lossy)：一般我们聊到图片的时候经常会听到别人提到有损或者无损的字眼，那么什么是有损和无损呢？无损就是图片在压缩保存后虽然占用的存储更小了，但是图像的呈现质量依然如旧；而有损则是相反，图像随着一次又一次的压缩后，质量会变得越来越差。
- 索引色(Indexed color)和直接色(Direct color)：按照不同的颜色深度可以将颜色分为索引色和直接色。索引色就是图像作者指定图片的用色总共不超过256种；而直接色则是对颜色使用没有限制，可以存在成千上万的颜色值。
- 光栅格式(raster)和矢量格式(vector)：图像根据信息的表示方式可以分为光栅图和矢量图。光栅图也叫作位图，点阵图或者像素图，图的最小单位是由一个个带颜色的像素组合而成，在 Photoshop 里把图片放到最大，将看到许许多多的像素方块，所以光栅图在伸缩的时候图像可能会失真；而矢量图则是以由点、线和一些几何图形为基础，通过数学计算来排列组合而成，这种图在伸缩的时候能完好的保护质量。
***

图片压缩:质量压缩、采样率压缩
https://cloud.tencent.com/developer/article/1006307
https://cloud.tencent.com/developer/article/1006352

```java
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
//quality 为0～100，0表示最小体积，100表示最高质量，对应体积也是最大
bitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
```

###邻近采样###
```java
BitmapFactory.Options options = new BitmapFactory.Options();
//或者 inDensity 搭配 inTargetDensity 使用，算法和 inSampleSize 一样
options.inSampleSize = 2;
Bitmap bitmap = BitmapFactory.decodeFile("/sdcard/test.png");
Bitmap compress = BitmapFactory.decodeFile("/sdcard/test.png", options);
```

###双线性采样###
```java
Bitmap bitmap = BitmapFactory.decodeFile("/sdcard/test.png");
Bitmap compress = Bitmap.createScaledBitmap(bitmap, bitmap.getWidth()/2, bitmap.getHeight()/2, true);
或者直接使用 matrix 进行缩放

Bitmap bitmap = BitmapFactory.decodeFile("/sdcard/test.png");
Matrix matrix = new Matrix();
matrix.setScale(0.5f, 0.5f);
bm = Bitmap.createBitmap(bitmap, 0, 0, bit.getWidth(), bit.getHeight(), matrix, true);
```
具体操作: https://blog.csdn.net/zizhan8723/article/details/78903818?spm=1001.2014.3001.5501

## png图片压缩原理
PNG的全称叫便携式网络图型（Portable Network Graphics）是目前最流行的网络传输和展示的图片格式
- 无损压缩：PNG图片采取了基于LZ77派生算法对文件进行压缩，使得它压缩比率更高，生成的文件体积更小，并且不损失数据。
- 体积小：它利用特殊的编码方法标记重复出现的数据，使得同样格式的图片，PNG图片文件的体积更小。网络通讯中因受带宽制约，在保证图片清晰、逼真的前提下，优先选择PNG格式的图片。
- 支持透明效果：PNG支持对原图像定义256个透明层次，使得图像的边缘能与任何背景平滑融合，这种功能是GIF和JPEG没有的。

