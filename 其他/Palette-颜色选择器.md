## Palette-颜色选择器

从**Bitmap**中提取颜色，然后给控件设置颜色



### 实现步骤

1. 将图片转换成Bitmap
2. 构建Palette颜色提取器
3. 获取颜色画板



**注意：使用Palette颜色提取器需要引入依赖，搜索palette即可**



### 布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".PaletteActivity">

    <androidx.appcompat.widget.Toolbar
        android:id="@+id/paletteToolBar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#00777a">

        <TextView
            android:id="@+id/paletteTextView"
            android:layout_gravity="center"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="文字"
            />

    </androidx.appcompat.widget.Toolbar>

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="centerCrop"
        android:src="@drawable/m01" />

</LinearLayout>
```



### 功能实现

```java
public class PaletteActivity extends AppCompatActivity {

    Toolbar toolbar;
    ImageView imageView;
    TextView textView;

    //设置图片数组
    int[] imageArr = new int[]{
            R.drawable.m01,
            R.drawable.m02,
            R.drawable.m03,
            R.drawable.m04,
    };

    //图片开始下标
    int index = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_palette);

        toolbar = findViewById(R.id.paletteToolBar);
        imageView = findViewById(R.id.imageView);
        textView = findViewById(R.id.paletteTextView);

        imageView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                index++;
                index = index % imageArr.length;
                imageView.setImageResource(imageArr[index]);

                //1. 将图片转换成Bitmap
                Bitmap bitmap = BitmapFactory.decodeResource(getResources(), imageArr[index]);

                //2. 构建Palette颜色提取器
                Palette.Builder builder = Palette.from(bitmap);
 
                //3. 获取颜色画板
                Palette.Swatch swatch = builder.generate().getVibrantSwatch();

                if (swatch != null){
                    toolbar.setBackgroundColor(swatch.getRgb());
                    textView.setTextColor(swatch.getBodyTextColor());
                }
            }
        });
    }
}
```

