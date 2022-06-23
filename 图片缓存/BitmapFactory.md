## BitmapFactory

### Option参数类

| 方法                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| boolean inJustDecodeBounds       | 如果设置为true，不获取图片，不分配内存，但会返回图片的高度宽度信息 |
| int inSampleSize                 | 图片缩放的倍数                                               |
| int outWidth                     | 获取图片的宽度值                                             |
| int outHeight                    | 获取图片的高度值                                             |
| int inDensity                    | 用于位图的像素压缩比                                         |
| int inTargetDensity              | 用于目标位图的像素压缩比（要生成的位图）                     |
| byte[] inTempStorage             | 创建临时文件， 将图片存储                                    |
| boolean inScaled                 | 设置为true时进行图片压缩，从inDensity到inTargetDensity       |
| boolean inDither                 | 如果为true，解码器尝试抖动解码                               |
| Bitmap.Config inPreferredConfig  | 设置解码器                                                   |
| String outMimeType               | 设置解码图像                                                 |
| boolean inPurgeable              | 当存储Pixel的内存控件在系统内存不足时是否可以被回收          |
| boolean inInputShareable         | inPurgeable为true情况下才能生效，是否可以共享一个InputStream |
| boolean inPreferQualityOverSpeed | 为true则优先保证Bitmap质量其次时解码速度                     |
| boolean inMutable                | 配置Bitmap是否可以更改，比如：在Bitmap上隔几个像素加一条线段 |
| int inScreenDensity              | 当前屏幕的像素密度                                           |

### 工厂方法

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Bitmap decodeFile(String pathName，Options opts)             | 从文件读取图片                                               |
| Bitmap decodeFile(String pathName)                           |                                                              |
| Bitmap decodeStream(InputStream is)                          | 从输入流读取图片                                             |
| Bitmap decodeStream(InputStream is，Rect outPadding，Options opts) |                                                              |
| Bitmap decodeResource(Recources res，int id)                 | 从资源文件读取图片                                           |
| Bitmap decodeResource(Recource res，int id，Options opts)    |                                                              |
| Bitmap decodeByteArray(byte[] data，int offset，int length)  | 从数组读取图片                                               |
| Bitmap decodeByteArray(byte[] data，int offset，int length，Options opts) |                                                              |
| Bitmap decodeFileDescriptor(FileDescriptor fd)               | 从文件读取文件与decodeFile不同的是这个直接调用JNI函数进行读取，效率较高 |
| Bitmap decodeFileDescriptor(FileDescriptor fd，Rect outPadding，Options opts) |                                                              |

