## LiveData

**JetPack提供的一种响应式编程组件，它可以包含任何类型的数据，并在数据发生变化的时候通知给观察者。**

LiveData特别适合与ViewModel结合使用



### 基本用法

之前我们编写的那个计数器虽然功能非常简单，但是其实是存在问题的。目前的逻辑是，当每次点击 "Plus One"按钮时，都会先给ViewModel中的计数+1，然后立即获取最新的计数。这种方式在单线程中确实可以正常的工作，但是如果ViewModel的内部开启了线程去执行一些耗时的操作，那么在点击按钮后就立即去获取最新的数据，得到的还是之前的数据。



然而LiveData可以包含任何类型的数据，并在数据发生变化的时候通知给观察者。就是说如果计数器中的计数使用LiveData来包装，然后在Activity中去观察它，就可以主动的将数据变化通知给Activity了

修改ViewModel中的代码，代码如下：

```java
public class MyViewModel extends ViewModel {

    //MutableLiveData是一种可变的LiveData，主要有3种读写数据的方法
    //getValue()：用户获取LiveData中包含的数据
    //setValue()：用于给LiveData设置数据，但是只能在主线程中调用
    //postValue()：用于在非主线程中给LiveData设置数据
    public MutableLiveData<Integer> count = new MutableLiveData<>();

    public MyViewModel(int count) {
        this.count.setValue(count);
    }

    //将计算器加一
    public void setPlusOne(int n){
        this.count.setValue(this.count.getValue() + n);
    }

    //将计算器清零
    public void clear(){
        this.count.setValue(0);
    }
}
```

ViewModelFactory的代码不用修改，代码如下：

```java
public class MyViewModelFactory implements ViewModelProvider.Factory {

    private int count;

    public MyViewModelFactory(int count) {
        this.count = count;
    }

    @NonNull
    @Override
    public <T extends ViewModel> T create(@NonNull Class<T> modelClass) {
        return (T) new MyViewModel(count);
    }
}
```

现在已经借助LiveData将ViewModel中的写法修改了，接下来修改MainActivity，代码如下：

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView infoText;
    private Button plusOne;
    private Button clearBtn;
    private SharedPreferences preferences;
    private SharedPreferences.Editor edit;
    private MyViewModel myViewModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        infoText = findViewById(R.id.infoText);
        plusOne = findViewById(R.id.plusOne);
        clearBtn = findViewById(R.id.clearBtn);

        preferences = getPreferences(Context.MODE_PRIVATE);
        edit = preferences.edit();
        int count = preferences.getInt("count", 0);

        myViewModel = new ViewModelProvider(this, new MyViewModelFactory(count)).get(MyViewModel.class);
        
        //调用ViewModel.count的observe()方法来观察数据的变化
        //由于ViewModel中的count变量已经变化成一个LiveData对象，现在任何LiveData对象都可以调用它的observe()方法来观察数据的变化
        //observe()方法接收两个参数：
        //1. 第一个参数：LifecycleOwner对象，直接传this即可
        //2. 一个observe接口，当count中包含的数据发送变化时，就会回调这里
        //注意：在子线程中给LiveData设置数据，一定要调用postValue()方法
        myViewModel.count.observe(this, new Observer<Integer>() {
            @Override
            public void onChanged(Integer integer) {
                infoText.setText(String.valueOf(integer));
            }
        });

        plusOne.setOnClickListener(this);
        clearBtn.setOnClickListener(this);
    }

    //修改文件中的计数
    private void setPreferences(int value){
        edit.putInt("count", value);
        edit.commit();
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.plusOne:
                myViewModel.setPlusOne(1);
                setPreferences(myViewModel.count.getValue());
                break;
            case R.id.clearBtn:
                myViewModel.clear();
                setPreferences(myViewModel.count.getValue());
                break;
        }
    }
}
```

