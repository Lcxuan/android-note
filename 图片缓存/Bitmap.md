## Bitmap

Bitmap是Android系统中的图像处理的最重要类之一。用它可以获取图像文件信息，进行图像剪切、旋转、缩放等操作，并且可以指定格式来保存图像文件。

### 常用方法

| 方法名                                                       | 描述                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| void recycle()                                               | 回收位图占用的内存控件，将位图标记为Dead                   |
| boolean isRecycled()                                         | 判断位图内存是否已经释放                                   |
| int getWidth()                                               | 获取位图的宽度                                             |
| int getHeight()                                              | 获取位图的高度                                             |
| boolean isMutable()                                          | 图片是否可以修改                                           |
| int getScaledWidth(Canvas canvas)                            | 获取指定密度转换后的图像的宽度                             |
| int getScaledHeight(Canvas canvas)                           | 获取指定密度转换后的图像的高度                             |
| boolean compress(CompressFormat format，int quality，OutputStream stream)<br />format：可以为Bitmap.CompressFormat.PNG或者Bitmap.CompressFormat.JPEG<br />quality：画质，0-100，0表示最低画质，100表示最高画质 | 按指定的图片格式以及画质，将图片转换为输出流               |
| Bitmap createBitmap(Bitmap src)                              | 以src为原图生成不可变的新图像                              |
| Bitmap createScaledBitmap(Bitmap src，int dstWidth，int dstHeight，boolean filter) | 以src为原图，创建新的图像，并指定新图像的高度以及是否可变  |
| Bitmap createBitmap(int width，int height，Config config)    | 创建指定格式、大小的位图                                   |
| Bitmap createBitmap(Bitmap source，int x，int y，int width，int height) | 以source为原图，创建新的图片，指定起始坐标以及新图像的高度 |