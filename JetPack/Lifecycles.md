## Lifecycles

**在编写Android程序中，可能会遇到需要感知Activity生命周期的情况**，例如：某个界面中发起了一个网络请求，但是请求得到响应时，界面或许已经关闭了，这时就不应该继续对响应的结果进行处理了。

而**Lifecycles就是为了解决这个问题而出现的，它可以让任何一个类都能轻松感知到Activity的生命周期，同时又不需要在Activity进行大量逻辑处理**



### Lifecycles用法

新建一个MyObserver类，并实现LifecycleObserver接口，这里实现接口后不需要重写方法，代码如下：

```java
public class MyObserver implements LifecycleObserver {
    
}
```

在新建的类中定义方法，使用了@OnLifecycleEvent注解感知到Activity的生命周期，代码如下：

```java
public class MyObserver implements LifecycleObserver {

    //Activity被打开
    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    public void activityStart(){
        Log.d("TAG", "MyObserver: " + "activityStart");
    }


    //Activity被关闭
    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    public void activityStop(){
        Log.d("TAG", "MyObserver: " + "activityStop");
    }
}
```



**生命周期类型共有7种：**

- ON_CREATE
- ON_START
- ON_RESUME
- ON_PAUSE
- ON_STOP
- ON_DESTORY
- ON_ANY：表示可以匹配Activity的任何生命周期回调

这里的生命周期类型分别对应Activity中相应的生命周期类型



现在的代码是无法工作的，当Activity的生命周期发送变化时是没人去通知MyObserver的，这时可以使用如下的**LifecycleOwner**结构，让MyObserver获得通知：

```java
lifecycleOwner.getLifecycle().addObserver(new MyObserver())
```

首先调用LifecycleOwner的getLifecycle()方法，得到一个Lifecycles对象，然后调用addObserver()方法来观察LifecycleOwner的生命周期，在将MyObserver的实例传入。



当然，只要你的Activity是继承AppCompatActivity的，或者你的Fragment是继承自androix.fragment.app.Fragment的，那么它的本身就是一个LifecycleOwner实例，也就是说在MianActivity中修改代码，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getLifecycle().addObserver(new MyObserver());
    }
}
```

