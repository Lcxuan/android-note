### 基本使用

BitmapFactotory提供了四种方法来加载Bitmap

#### 第一种方式：使用decodeFile从文件中加载Bitmap

例如：

1. 通过Intent打开本地图片或者照片

2. 在onActivityResult中获取图片Uri
3. 根据获取的Uri获取图片的路径
4. 根据路径解析Bitmap bitmap = BitmapFactory.decodeFile(path);



#### 第二种方式：从本地资源中加载Bitmap

例如：

```java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.ic_launcher_background);
```



#### 第三种方式：使用decodeStream从输入流中加载Bitmap

例如：

1. 开启异步线程去获取网络图片
2. 网络返回InputStream
3. 解析：Bitmap bitmap = BitmapFactory.decodeStream(stream);【注意：这是一个耗时操作，要在子线程中执行】



#### 第四种方式：使用decodeByteArray从字节数组种加载

例如：

1. 开启异步线程去获取网络图片
2. 网络返回InputStream
3. 将返回的InputStream转换成byte[]
4. 解析：Bitmap bitmap = BitmapFactory.decodeByteArray(myByte, 0, myByte.length);



这里注意：decodeFile和decodeResource间接调用decodeStream方法

### 高效加载Bitmap

我们在使用Bitmap时，经常会遇到内存溢出【即OOM】的情况，这时因为图片太大或者Android系统对单个应用施加的内存限制等原因造成的。

所有我们需要对Bitmap进行高效的加载

高效加载Bitmap的方法非常简单：就是使用系统提供给我们的Options类来处理Bitmap



BitmapFactory.Options一些参数说明：

- inSampleSize：表示采样大小。用于将图片缩小加载出来，以免占用太大的内存，适合用于缩略图
- inJustDecodeBounds：当为true时，执行decodeXXX方法时，BitmapFactory只会解析图片的原始宽高信息，并不会真正加载图片
- inPreferredConfig：用于配置图片解码的方式，对应类型Bitmap.Config。如果非null，则会使用它来解码图片。默认值为：Bitmap.Config.ARGB_8888
- inBitmap：在图片加载的时候可以使用之前已经创建的Bitmap，以便节省内存



通过BitmapFactory.Options按照一定的采样率来加载缩小后的图片，然后再ImageView中使用缩小的图片这样就会降低内存占用，避免OOM，提高加载Bitmap的性能

以上的其实就是我们常说的图片尺寸压缩。尺寸压缩式压缩图片的像素，一张图片所占内存的大小、图片类型、宽高，通过改变三个值减少图片所占的内存，防止OOM，但是这样的方法可能会使图片失真

Android色彩模式（也是Config解析说明）：

- Bitmap.Config.ALPHA_8：每像素占用1byte内存
- Bitmap.Config.ARGB_4444：每像素占用2byte内存
- Bitmap.Config.ARGB_8888：每像素占用4byte内存【**默认，这种色彩模式色彩最细腻、显示质量最高，但是占用内存最大**】
- Bitmap.Config.RGB_565：每像素占用2byte内存【**一般使用这种**】



#### 1、采样率inSampleSize

inSampleSize必须大于1的时候才会有效果，且采样率同时作用于宽和高

- inSampleSize = 1时，采样后的图片为图片的原始大小
- inSampleSize = 2时，采样后的图片的宽高均为原始图片宽高的 1 / 2，这时像素为原始图片的1 / 2，占用内存也为原始图片的  1 / 2
- inSampleSize的取值应该为2的倍数，否则会向下取整，取最接近2的幂指数，例如：inSampleSize = 3时，系统会取inSampleSize = 2

例如一张1024 * 1024，模式为ARGB_8888，设置inSampleSize = 2，原始占用内存大小为4M，采样后的图片占用内存大小为：

```java
（1024 / 2）*（1024 / 2）* 4 = 1MB
```



#### 2、获取采样率应该遵循以下步骤

1. 将BitmapFactory.Options的inJustDecodeBounds参数设置为true，这时执行decodeXXX方法时，BitmapFactory只会解析出图片的原始高度信息，并不会加载真正的图片
2. 从BitmapFactory.Options取出图片的原始宽高信息
3. 选取合适的采样率
4. 将BitmapFactory.Options的inJustDecodeBounds参数设置false并重新加载图片

#### 3、代码示例：

代码示例1：

```java
imageView = findViewById(R.id.imageView);

//获取Options对象
BitmapFactory.Options options = new BitmapFactory.Options();

//将inJustDecodeBounds设置为true，获取宽度
options.inJustDecodeBounds = true;

//解析图片的原始高度和宽度
BitmapFactory.decodeResource(getResources(), R.drawable.header, options);

//设置图片解码方式
options.inPreferredConfig = Bitmap.Config.RGB_565;

//获取宽高
int width = options.outWidth;
int height = options.outHeight;
Log.i("TAG", "原始高度：" + height + ", 原始宽度：" + width);
int halfWidth = width / 2;
int halfHeight = height / 2;
Log.i("TAG", "修改后的高度：" + halfHeight + ", 修改后宽度：" + halfWidth);

//设置合适的采样率
int inSampleSize = 1;
while((halfHeight / inSampleSize) >= 100 && (halfWidth / inSampleSize) >= 100){
    inSampleSize *= 2;
    Log.e("TAG", "inSampleSize：" + inSampleSize);
}
//将inJustDecodeBounds设置为false，获取所有的信息
options.inJustDecodeBounds = false;
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.header, options);
imageView.setImageBitmap(bitmap);
```

代码示例2：

```java
//获取屏幕的高度和宽度
DisplayMetrics displayMetrics = getResources().getDisplayMetrics();
int height = displayMetrics.heightPixels;
int width = displayMetrics.widthPixels;
Log.e("lcxuan", "屏幕宽度：" + width + ",屏幕高度：" + height);

//获取Options类对象
BitmapFactory.Options options = new BitmapFactory.Options();
//将inJustDecodeBounds设置为true，只获取宽度和高度信息
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.drawable.header, options);

//获取图片的宽度和高度
int outHeight = options.outHeight;
int outWidth = options.outWidth;

//判断当前照片的宽度或者高度是否大于屏幕的宽度和高度
if (outWidth > width || outHeight > height){
    //将当前照片的宽度除于屏幕的宽度，并进行四舍五入
    int widthIndex = Math.round((float) outWidth / (float) width);
    //将当前照片的高度除于屏幕的高度，并进行四舍五入
    int heightIndex = Math.round((float) outHeight / (float) height);
    //获取进行运算后的width和height之中的最大的数赋值给inSampleSize参数
    options.inSampleSize = Math.max(widthIndex, heightIndex);
}

//将inJustDecodeBounds设置为false，获取所有的信息
options.inJustDecodeBounds = false;
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.header, options);
imageView.setImageBitmap(bitmap);
```

代码示例3：

```java
//获取Options类对象
BitmapFactory.Options options = new BitmapFactory.Options();

int i = 1;
Bitmap bitmap = null;
//使用循环找出最合适的inSampleSize进行输出
while (true){
    try {
        options.inSampleSize = i;
        bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.header, options);
        break;
    }catch (Exception e){
        i *= 2;
    }
}
Log.e("lcxuan", "inSampleSize：" + i);
imageView.setImageBitmap(bitmap);
```



### 创建图片的副本

在res目录下加载进来的Bitmap是不可以被修改的

所以，我们我们可以通过创建新的Bitmap，并将原图的内容写入到新的Bitmap中，修改新Bitmap中的内容，最后将新的Bitmap放入到控件中即可

```java
imageView = findViewById(R.id.imageView);

//在res目录下加载进来的Bitmap，是不能被修改的
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.header);
//imageView.setImageBitmap(bitmap);

//这里直接修改res目录下加载进行的Bitmap会报错
//bitmap.setPixel(30, 30, Color.RED);
//imageView.setImageBitmap(bitmap);

//如果需要修改res目录下加载进来的Bitmap，需要创建一个新的并且可以修改的Bitmap
//第三个参数：图片的配置信息
Bitmap createBitmap = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), bitmap.getConfig());

//创建一个画布，我们需要通过这个画布修改Bitmap中的内容
Canvas canvas = new Canvas(createBitmap);

//创建一个画笔，我们需要使用这个画笔在画布中进行绘制
Paint paint = new Paint();

//创建一个Matrix对象，通过这个对象对图片进行处理，例如：位移、缩放、旋转等操作
Matrix matrix = new Matrix();

//将res目录下原图的Bitmap的内容画入我们新创建的Bitmap中，即：createBitmap
//执行完这项操作createBitmap中就已经有数据了
canvas.drawBitmap(bitmap, matrix, paint);

//修改图片上的内容
for (int i = 0; i < 1000; i++) {
    createBitmap.setPixel(100, 100 + i, Color.BLUE);
}

imageView.setImageBitmap(createBitmap);
```



### 图片处理-Matrix

主要是使用Matrix类，可以实现旋转、平移、缩放

多次调用set，最近一次会把之前的效果清除掉

如果想在之前效果基础上进一步处理图片，需要调用postXXX方法

#### 旋转

```java
Matrix matrix = new Matrix();
matrix.setRotate(90, createBitmap.getWidth() / 2, createBitmap.getHeight() / 2);
```

#### 平移

```java
Matrix matrix = new Matrix();
matrix.setTranslate(30, 0);
```

#### 缩放

```java
Matrix matrix = new Matrix();
//如果传入一个负数，就会有镜像的效果
matrix.setScale(1.5f, 1.5f);
```

