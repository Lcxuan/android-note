## 全局获取Context

我们平时写代码的时候，会发现有很多的地方会使用到Context，例如：弹出Toast时、启动Activity时、发送广播时和操作数据库的时候...

但是有些时候，我们获取Context是非常的困难的，这个使用我们就可以使用Appcation类来制作一个全局的Context



首先创建一个`MyApplication类`，继承Application，代码如下：

```java
public class MyApplication extends Application {
    
}
```



接下来我们可以重写onCreate()方法，通过调用`getApplicationContext()方法`获取一个全局的Context，并返回一个静态方法来获取这个Context，代码如下：

```java
public class MyApplication extends Application {

    private static Context context;

    @Override
    public void onCreate() {
        super.onCreate();

        context = getApplicationContext();
    }

    public static Context getContext() {
        return context;
    }
}
```



接下来，我们需要告知系统，当程序启动时应该初始化我们的`MyApplication类`，在AndroidManifest.xml文件的<application>标签下指定，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.lcxuan.basketballcalculate" >

    <application
        android:name=".MyApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.BasketballCalculate" >
        <activity android:name=".MainActivity" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

