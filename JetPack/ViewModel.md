## ViewModel

JetPack中最重要的组件之一

在传统的开发模式下，Activity的任务实在是太重了，既要负责逻辑处理，又要控制UI展示，甚至还得处理网络回调。

而**ViewModel的一个重要的作用就是可以帮助Activity分担一部分的工作，它是专用用于存放与界面相关的数据**。也就是说，只要是界面上能看得到的数据，相关变量都应该存放在ViewModel中。

**ViewModel还有一个特性：保证手机屏幕在发送旋转的时候不会被重新创建，只有当Activity退出的时候才会跟着Activity一起销毁**

因为我们都知道，当手机发送横竖屏旋转时，Activity也会被创新创建，同时存放在Activity的数据也会丢失



### 基本用法

添加依赖：

```
implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'
```



实现计算器功能，新建一个MainViewModel类，继承ViewModel，定义一个用于计算的count变量，代码如下：

```java
public class MainViewModel extends ViewModel {
    int count = 0;
}
```

接着在布局上添加一个按钮，每次点击一次按钮就加一，并且把最新的计算显示在界面上，修改activity_main.xml代码，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/infoText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:textSize="32sp"/>

    <Button
        android:id="@+id/plusOne"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:text="加一"/>

</LinearLayout>
```

然后修改MainActivity代码，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    private TextView infoText;
    private Button plusOne;
    private MainViewModel viewModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        viewModel = new ViewModelProvider(this).get(MainViewModel.class);

        infoText = findViewById(R.id.infoText);
        plusOne = findViewById(R.id.plusOne);

        plusOne.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                viewModel.count++;
                refreshCounter();
            }
        });

        refreshCounter();
    }

    private void refreshCounter(){
        infoText.setText(String.valueOf(viewModel.count));
    }
}
```

这里通过ViewModelProvider获取ViewModel的实例，具体规则如下：

```java
ViewModelProvider(<Activity或Fragment实例>).get(<ViewModel>.class);
```



### ViewModel传递参数

由于所有ViewModel的实例都是通过ViewModelProvider来获取的，因此我们没有任何地方可以向ViewModel的构造函数中传递参数

因此我们需要借助**ViewModelProvider.Factory可以实现传递参数**



现在的计算器虽然在屏幕旋转中不会丢失数据，但是退出程序之后再重新打开

首先修改MainViewModel中的代码，创建一个构造方法，将count传入，将传入的参数传入给count属性，代码如下：

```java
public class MainViewModel extends ViewModel {
    int count = 0;

    public MainViewModel(int countReserved){
        this.count = countReserved;
    }
}
```

接着新建一个MainViewModelFactory，并继承ViewModelProvider.Factory，创建一个构造方法和重写create()方法，代码如下：

```java
public class MainViewModelFactory implements ViewModelProvider.Factory {

    private int count;

    public MainViewModelFactory(int count){
        this.count = count;
    }

    @NonNull
    @Override
    public <T extends ViewModel> T create(@NonNull Class<T> modelClass) {
        return (T) new MainViewModel(count);
    }
}
```



接着修改布局，修改activity_main.xml布局代码，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/infoText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:textSize="32sp"/>

    <Button
        android:id="@+id/plusOne"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:text="加一"/>

    <Button
        android:id="@+id/clearBtn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:text="Clear"/>

</LinearLayout>
```

接着修改MainActivity代码，代码如下：

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView infoText;
    private Button plusOne, clearBtn;
    private MainViewModel viewModel;
    private SharedPreferences preferences;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //初始化View
        infoText = findViewById(R.id.infoText);
        plusOne = findViewById(R.id.plusOne);
        clearBtn = findViewById(R.id.clearBtn);

        //获取文件中存储的count字段
        preferences = getPreferences(Context.MODE_PRIVATE);
        int count = preferences.getInt("count", 0);

        //获取ViewModel，使用MainViewModelFactoryl
        viewModel = new ViewModelProvider(this, new MainViewModelFactory(count)).get(MainViewModel.class);

        refreshCounter();

        plusOne.setOnClickListener(this);
        clearBtn.setOnClickListener(this);
    }

    //刷新Count
    private void refreshCounter(){
        SharedPreferences.Editor edit = preferences.edit();
        edit.putInt("count", viewModel.count);
        edit.commit();
        infoText.setText(String.valueOf(viewModel.count));
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.plusOne:
                viewModel.count++;
                refreshCounter();
                break;
            case R.id.clearBtn:
                viewModel.count = 0;
                refreshCounter();
                break;
        }
    }
}
```

