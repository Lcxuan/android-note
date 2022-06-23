## Shape

工程文件中有一个drawable目录，里面可以存放一些静态图片资源文件【.png、.9.png、.jpg、.gif】或者一些可绘制对象资源子类型的xml文件

- stroke：代表的是边框
  
  - android:width：边框宽度
  
  - android:color：边框颜色
  
  - dashGap：虚线间距
  
  - dashWidth：虚线长度

- corners：代表的是圆角，如果不设置，默认为直角
  
  - android:radius：圆角半径

- gradient：渐变色
  
  - android:startColor：开始颜色
  
  - android:endColor：结束颜色
  
  - android:centerColor：中间颜色
  
  - android:angle：渐变色的角度，必须是45倍的整倍数，默认为0【从左到右】
  
  - android:type：渐变类型
    
    - linear：线性渐变
    
    - radial：圆形渐变
    
    - swee

XML中使用

```xml
   <TextView
        android:id="@+id/tv1"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:background="@drawable/shape_btn_2_normal"
        android:gravity="center"
        android:text="RFDev 圆角背景TextView 1"
        android:textColor="#ffffff" />
```

代码中使用

```java
Resources res = getResources();
Drawable shape = ResourcesCompat.getDrawable(res, R.drawable.shape_btn_2_normal, getTheme());

TextView tv = (TextView)findViewById(R.id.tv1);
tv.setBackground(shape);
```



虚线实现

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="line">
    <size android:height="1dp" />
    <stroke
        android:width="1dp"
        android:color="#474c57"
        android:dashWidth="1dp"
        android:dashGap="2dp" />
</shape>
```

关掉activity的硬件加速`android:hardwareAccelerated="false"`，否则会显示一条直线。

```xml
<activity
    android:name="com.rustfisher.basic4.UserFeedbackAct"
    android:hardwareAccelerated="false"
    android:screenOrientation="portrait">

</activity>
```
