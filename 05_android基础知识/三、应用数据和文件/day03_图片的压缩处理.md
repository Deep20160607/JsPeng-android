PNG 压缩、如低端机图片使用 RGB565 解码

1、位深和色深有什么区别，他们是一个东西吗？
2、为什么Bitmap不能直接保存，Bitmap和PNG、JPG到底是什么关系？
3、图片占用的内存大小公式：图片分辨率 * 每个像素点大小，这种说法正确吗？
4、为什么有时候同一个 app，app 内的同个界面上的同张图片，但在不同设备上所耗内存却不一样？
5、同一张图片，在界面上显示的控件大小不同时，它的内存大小也会跟随着改变吗？


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
具体操作: https://mp.csdn.net/mp_blog/creation/editor/78903818

## png图片压缩原理
PNG的全称叫便携式网络图型（Portable Network Graphics）是目前最流行的网络传输和展示的图片格式
- 无损压缩：PNG图片采取了基于LZ77派生算法对文件进行压缩，使得它压缩比率更高，生成的文件体积更小，并且不损失数据。
- 体积小：它利用特殊的编码方法标记重复出现的数据，使得同样格式的图片，PNG图片文件的体积更小。网络通讯中因受带宽制约，在保证图片清晰、逼真的前提下，优先选择PNG格式的图片。
- 支持透明效果：PNG支持对原图像定义256个透明层次，使得图像的边缘能与任何背景平滑融合，这种功能是GIF和JPEG没有的。

