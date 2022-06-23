## ViewBinding

视图绑定，可以更加轻松地编写与视图交互的代码，在模块中启动视图绑定之后，系统会为该模块中的每个XML布局文件生成一个绑定类。

该绑定类的实例包含对在相应布局中具有ID的所有视图的直接引用



### 启动视图

如果需要在某个模块中启动视图绑定，需要在其`build.gradke`文件中编写如下代码：

```java
android {
    viewBinding{
        enabled = true
    }
}
```



如果出现以下编译报错，则使用下面方式启动视图绑定：

```java
DSL element 'android.viewBinding.enabled' is obsolete and has been replaced with 'android.buildFeatures.viewBinding'. It will be removed in version 7.0 of the Android Gradle plugin.
```

在其`build.gradke`文件中编写如下代码来启动视图绑定：

```java
android {
    buildFeatures{
        viewBinding = true
    }
}
```



### 设置说明

如果希望在生成绑定类时忽略某个布局文件，将`tools.viewBindingIgnore="true"`属性添加到相应布局文件的根视图中：

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    ...
    tools:viewBindingIgnore="true">

</LinearLayout>
```



### 用法

为某个模块启动视图绑定功能后，系统会为该模块中包含的每个XML布局文件生成一个绑定类

例如：某个布局文件名称：`activity_main.xml`，所生成的绑定类名层为`ActivityMainBinding`

每个绑定类还包含一个getRoot()方法，用于为相应布局文件的根视图提供直接引用



### 在Activity中使用视图绑定

如果需要设置绑定类的实例供Activity使用，在`onCreate()`方法中执行以下步骤：

- 调用生成的绑定类中包含的静态`inflate()`方法
- 通过调用`getRoot()`方法获取对根视图的引用
- 将根视图传递到`setContentView()`，使其成为屏幕上的活动视图

```java
public class MainActivity extends AppCompatActivity {

    private ActivityMainBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding = ActivityMainBinding.inflate(getLayoutInflater());
        View view = binding.getRoot();
        setContentView(view);
    }
}
```

现在就可以使用该绑定类的实例来引用任何视图：

```java
binding.btnClick.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Toast.makeText(MainActivity.this, "点击了....", Toast.LENGTH_SHORT).show();
    }
});
```



### 在Fragment中使用视图绑定

```java
public class MyFragment extends Fragment {

    private FragmentMyBinding binding;

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {

        binding = FragmentMyBinding.inflate(inflater, container, false);
        View view = binding.getRoot();

        binding.textView.setText("这是我的一个Fragment");

        return view;
    }

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        binding = null;
    }
}
```

