## Fragment

### 静态Fragment用法

Activity布局中使用<fragment></fragment>标签

在fragment标签使用属性name添加fragment类



#### 实现步骤

1. 自定义fragment布局：创建类继承Fragment，重写onCreateView()
2. 在Activity布局中使用<fragment></fragment>标签使用属性name添加自定义fragment布局
3. 使用getSupportFragmentManager()获取fragment管理器



### 动态Fragment用法

#### 实现步骤

1. 自定义Fragment类，继承Fragment类或子类，实现onCreateView()方法，通过inflater.inflate加载布局文件并返回
2. 获取FragmentManager，通过getSupportFragmentManager()
3. 获取事务对象FragmentTransaction并开启，通过getSupportFragmentManager().beginTransaction()
4. 调用add()、remove()、relace()、hide()、show()方法
5. 使用commit()方法提交事务



在Fragment中可以使用getActivity()获取所在的Activity



在Activity中可以使用getSupportFragmentManager的findFragmentById()或者findFragmentByTag()获取Fragment

获取到Fragment后可以使用getView.findViewId()获取到fragment中的控件



Fragment功能实现

```java
public class DynamicFragment extends Fragment {
    @Nullable
    @org.jetbrains.annotations.Nullable
    @Override
    public View onCreateView(@NonNull @NotNull LayoutInflater inflater, @Nullable @org.jetbrains.annotations.Nullable ViewGroup container, @Nullable @org.jetbrains.annotations.Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.dynamicfragment_layout, container, false);
        return view;
    }
}
```



Fragment布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#f5222d"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="这是动态加载的Fragment"
        android:textColor="#ffffff"
        android:textSize="35sp"/>

</LinearLayout>
```



Activity实现

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    //获取自定义的fragment对象
    DynamicFragment dynamicFragment = new DynamicFragment();
    FragmentManager supportFragmentManager = getSupportFragmentManager();
    //获取事务对象
    FragmentTransaction fragmentTransaction = supportFragmentManager.beginTransaction();
    //在容器中添加Fragment
    fragmentTransaction.add(R.id.rl_fragment, dynamicFragment);
    //将事务添加到回退栈
    fragmentTransaction.addToBackStack(null);
    //提交事务
    fragmentTransaction.commit();

}
```





### Activity传递数据给Fragment

#### 实现步骤

1. Activity中创建Fragment对象，调用setArguments(bundle)方法存储值

2. Fragment中调用getArguments()获取传递的Bundle对象解析获取具体值



Activity功能实现

```java
@Override    
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    //获取自定义的fragment对象
    DynamicFragment dynamicFragment = new DynamicFragment();

    //使用Bundle对象传递数据
    Bundle bundle = new Bundle();
    //绑定对象
    bundle.putString("name", "jack");
    dynamicFragment.setArguments(bundle);

    FragmentManager supportFragmentManager = getSupportFragmentManager();
    //获取事务对象
    FragmentTransaction fragmentTransaction = supportFragmentManager.beginTransaction();
    //在容器中添加Fragment
    fragmentTransaction.add(R.id.rl_fragment, dynamicFragment);
    //提交事务
    fragmentTransaction.commit();
}
```



Fragemnt实现

```java
@Nullable
@org.jetbrains.annotations.Nullable
@Override
public View onCreateView(@NonNull @NotNull LayoutInflater inflater, @Nullable @org.jetbrains.annotations.Nullable ViewGroup container, @Nullable @org.jetbrains.annotations.Nullable Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.dynamicfragment_layout, container, false);
    //获取绑定的值
    String name = (String) getArguments().get("name");
    System.out.println("name:" + name);
    return view;
}
```



### Fragment传递数据给Activity

#### 实现步骤

1. Fragment中定义传值的回调接口，在生命周期的onAttach()方法中获取接口的实现

2. Fragment需要传值的位置调用接口回调方法

3. Activity实现回调接口重写回调方法获取传递的值



1、定义一个接口，定义传递的方法

```java
//定义一个接口，定义要向Activity传递的方法
public interface OnNewItemAddedListener{
    public void newItemAdd(String content);
}
```



2、声明接口的引用变量

```java
//声明接口引用变量
private OnNewItemAddedListener onNewItemAddedListener;
```



3、重写onAttach

```java
@Override
public void onAttach(@NonNull @NotNull Context context) {
    super.onAttach(context);
    //要求该Fragment 所附着的Activity 必须实现这个方法
    onNewItemAddedListener = (OnNewItemAddedListener) context;
}
```



4、fragment中使用该方法

```java
@Nullable
@org.jetbrains.annotations.Nullable
@Override
public View onCreateView(@NonNull @org.jetbrains.annotations.NotNull LayoutInflater inflater, @Nullable @org.jetbrains.annotations.Nullable ViewGroup container, @Nullable @org.jetbrains.annotations.Nullable Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.new_item_fragment, container, false);
    String content = "这是传给Activity的数据";
    //调用接口的方法
    onNewItemAddedListener.newItemAdd(content);
    return view;
}
```



5、在Activity中继承该Fragment

```java
public class MainActivity extends AppCompatActivity implements NewItemFragment.OnNewItemAddedListener {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override //实现接口的方法，可以获取从Fragment传送过来的内容, 对该内容就行操作
    public void newItemAdd(String content) {
        //实现的操作...
    }
}
```



### Fragment懒加载

使用onHiddenChanged

```java
public class Fragment1 extends Fragment {

    //判断是否已经进行懒加载，避免重复加载
    private boolean isLoad = false;

    //判断当前Fragment是否可见
    private boolean isHidden = true;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_1, container, false);
    }

    @Override
    public void onHiddenChanged(boolean hidden) {
        super.onHiddenChanged(hidden);
        Log.e("TAG123", "onHiddenChanged: " + hidden);
        isHidden = hidden;
        lazyLoad();
    }

    @Override
    public void onResume() {
        super.onResume();
        lazyLoad();
    }

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        isLoad = false;
    }

    private void lazyLoad(){
        if (!isLoad && !isHidden){
            isLoad = true;
        }
    }
}
```

