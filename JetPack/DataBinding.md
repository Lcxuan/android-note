## DataBinding

DataBinding是谷歌官方发布的一个框架，即为**数据绑定**，是MVVM模式在Android上的一种实现，**用于降低布局和逻辑的耦合性，使代码逻辑更加清晰**。

DataBinding**可以省去我们一直依赖的findViewById()步骤，大量减少Activity内的代码，并且可以将数据单向或双向绑定到layout文件中**，有助于防止内存泄漏，而且能自动进行空检测以避免空指针异常。

可以使用声明性格式将布局中的界面组件绑定到应用中的数据源

启用DataBinding的方法使在对应的Model的**build.gradle文件的android属性**中加入以下代码，代码如下：

```
dataBinding {
	enabled = true
}
```



### 使用数据绑定

使用DataBinding将布局页面发生变化，在布局页面中按住 `Alt + Enter`，将页面发生改变，代码如下：

使用@{}：编写java代码

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="data"
            type="com.lcxuan.databindingtest.MyViewModel" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context=".MainActivity">

        <TextView
            android:id="@+id/textView"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center_horizontal"
            android:textSize="24sp"
            android:text="@{String.valueOf(data.count)}"/>

        <Button
            android:id="@+id/btn"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:onClick="@{()->data.addCount()}"
            android:text="Btn" />

    </LinearLayout>
</layout>
```



创建MyViewModel类，并继承ViewModel，实现ViewModel + LiveData + DataBinding，代码如下：

```java
public class MyViewModel extends ViewModel {

    private MutableLiveData<Integer> count;

    public MutableLiveData<Integer> getCount() {
        if (count == null){
            count = new MutableLiveData<>();
            count.setValue(0);
        }
        return count;
    }

    public void addCount(){
        this.count.setValue(this.count.getValue() + 1);
    }
}
```



在Activity中通过DataBindingUtil设置布局文件，省略原先Activity的setContentVuew()方法，通过获取的binding.setData()设置布局文件中的<data></data>数据，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    ActivityMainBinding binding;
    MyViewModel myViewModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main);

        myViewModel = new ViewModelProvider(this).get(MyViewModel.class);
        binding.setData(myViewModel);
        binding.setLifecycleOwner(this);
    }
}
```



### 绑定数据

系统会为每个布局文件生成一个绑定类。默认情况下，类名称基于布局文件的名称。

例如：布局文件名为：activity_main.xml文件，生成的绑定类为ActivityMainBinding

代码如下：

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityMainBinding dataBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
    }
}
```



### 表达式语言

#### Null合并运算符

如果左边运算数不是null，则Null合并运算符（??）选择左边运算数，如果左边运算数为null，则选择右边运算数

```xml
android:text="@{user.displayName ?? user.lastName}"
```

相当于：

```xml
android:text="@{user.displayName != null ? user.displayName : user.lastName}"
```



#### 属性引用

```xml
android:text="@{user.lastName}"
```



#### 视图引用

表达式可以通过以下语法按ID引用布局中的其他视图：

```xml
android:text="@{exampleInfo.text}"
```

**注意：绑定类将ID转换为驼峰式大小写**

例如：TextView视图引用同一布局中的EditText视图中输入的文字：

```xml
<EditText
          android:id="@+id/example_info"
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:maxLength="10"/>

<TextView
          android:id="@+id/example_output"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="@{exampleInfo.text}"/>
```



#### 字符串字面量

可以使用单引号括住属性值，这样就可以在表达式找那个使用双引号，代码如下：

```xml
android:text='@{map["firstName"]}'
```

也可以使用双引号括住属性值，但是如果这样左，需要使用反单引号`将字符串字面量括起来

```xml
android:text="@{map[`firstName`]}"
```



### 事件处理

有两种方式：

- 方法引用
- 监听器引用

#### 方法引用

首先创建一个要调用的方法，这里要调用的方法名称作为值，代码如下：

```java
public class MyHandlers {
    public void onClickRequest(View view){
        Log.e("TAG", "onClickRequest: 请求成功");
    }
}
```

接着将方法分配给事件，这里使用方法引用，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="myHandler"
            type="com.lcxuan.databinding.MyHandlers" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity"
        android:orientation="vertical">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="点击请求"
            android:onClick="@{myHandler::onClickRequest}"/>

    </LinearLayout>
</layout>
```

接着在主方法中进行数据绑定，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityMainBinding dataBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        dataBinding.setMyHandler(new MyHandlers());
    }
}
```



#### 监听器绑定

首先创建一个方法，代码如下：

```java
public class MyHandlers {
    public void onClickRequest(String val){
        Log.e("TAG", "onClickRequest: " + val);
    }
}
```

接着将方法分配给事件，这里使用监听器绑定，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="myHandler"
            type="com.lcxuan.databinding.MyHandlers" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity"
        android:orientation="vertical">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="点击请求"
            android:onClick="@{()->myHandler.onClickRequest(`你好`)}"/>

    </LinearLayout>
</layout>
```

接着在主方法中进行数据绑定，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityMainBinding dataBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        dataBinding.setMyHandler(new MyHandlers());
    }
}
```



### 二级页面绑定

当一个页面上面有二级页面的时候，可以使用app:需要绑定的数据名

主界面，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="myHandler"
            type="com.lcxuan.databinding.MyHandlers" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity"
        android:orientation="vertical">

        <include
            layout="@layout/sub"
            app:myHandler="@{myHandler}"/>

    </LinearLayout>
</layout>
```

二级界面，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="myHandler"
            type="com.lcxuan.databinding.MyHandlers" />
    </data>

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="这是二级页面的一个Button"
            android:onClick="@{()->myHandler.onClickRequest(`123`)}" />

    </LinearLayout>
</layout>
```



### 绑定适配器

当一些属性需要自定义逻辑时，但是控件却没有setter方法时，我们可以使用`@BindingAdapter`注解进行静态绑定适配器来自定义属性

首先，新建类，实现自定义属性的方法，代码如下：

```java
public class ButtonBindingAdapter {

    //setButtonText为自定义属性的名称
    @BindingAdapter("setButtonText")
    //第一个参数：自定义属性的控件
    //第二个参数：需要传入的参数
    public static void setButton(Button button, String val){
        if (val == null){
            button.setText(null);
        }else {
            button.setText(val);
        }
    }
}
```

布局进行绑定，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="buttonVal"
            type="String" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity"
        android:orientation="vertical">

        <Button
            app:setButtonText="@{buttonVal}"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

    </LinearLayout>
</layout>
```

主方法代码如下：

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityMainBinding dataBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        dataBinding.setButtonVal("这是通过适配器修改的内容");
    }
}
```



### 双向绑定

两种方式：

- 继承Observable方式
- 定义ObservableField方式

#### 继承Observable方式

实现步骤：

1. 实体类继承BaseObservable类
2. 在Getter上使用注解@Bindable
3. 在Setter上调用方法notifyPropertyChanged()方法，例如notifyPropertyChanged(BR.username); //BR会在编译时生成，会根据BR.username去更新UI

实体类实现，代码如下：

```java
//1. 继承BaseObservable类
public class UserViewModel extends BaseObservable {

    private String username;

    //2. 在Getter上使用注解@Bindable
    @Bindable
    public String getUsername(){
        return username;
    }

    //3. 在Setter上调用方法notifyPropertyChanged()方法
    public void setUsername(String username){
        this.username = username;
        Log.e("LCXUANTEST", "修改的内容：" + username);
        notifyPropertyChanged(BR.username);
    }
}
```

XML中使用，如果要使用双向绑定需要使用@={}，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="userViewModel"
            type="com.lcxuan.databinding.twoWay.UserViewModel" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".twoWay.TwoWayBindingActivity"
        android:orientation="vertical">

        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={userViewModel.username}"/>

    </LinearLayout>
</layout>
```

主方法中绑定ViewModel，代码如下：

```java
public class TwoWayBindingActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityTwoWayBindingBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_two_way_binding);
        binding.setUserViewModel(new UserViewModel());
    }
}
```



#### 定义ObservableField方式

实体类实现，代码如下：

```java
public class UserViewModel {
    private ObservableField<String> usernameObservable = new ObservableField<>();

    public String getUsername() {
        return usernameObservable.get();
    }

    public void setUsername(String username) {
        this.usernameObservable.set(username);
        Log.e("LCXUANTEST", "设置的内容：" + username);
    }
}
```

XML中使用，如果要使用双向绑定需要使用@={}，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="userViewModel"
            type="com.lcxuan.databinding.twoWay2.UserViewModel" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context=".twoWay2.TwoWayBindingActivity2">

        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={userViewModel.username}"/>

    </LinearLayout>
</layout>
```

主方法中绑定ViewModel，代码如下：

```java
public class TwoWayBindingActivity2 extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityTwoWayBinding2Binding binding = DataBindingUtil.setContentView(this, R.layout.activity_two_way_binding2);
        binding.setUserViewModel(new UserViewModel());
    }
}
```

