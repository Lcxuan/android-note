## Service

使用Service可以在后台执行长时间的操作，并且Service并不与用户UI进行交互

其他的组件可以启动Service，即使切换其他的应用，仍可以在后台执行

一个组件可以与Service进行绑定和交互，甚至跨进行通信

### 配置Service

#### 1、继承Service类

```java
public class MyService extends Service {
    //另外一个组件通过与服务绑定时【bindService()】，调用
    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    //另外一个组件与服务解除绑定时【unbindService()】，调用
    @Override
    public boolean onUnbind(Intent intent) {
        return super.onUnbind(intent);
    }

    //当旧组件与服务解绑后，另外一个新的组件与服务绑定，onUnbind()为true时，调用
    @Override
    public void onRebind(Intent intent) {
        super.onRebind(intent);
    }

    //首次创建服务时，调用
    //只会调用一次
    @Override
    public void onCreate() {
        super.onCreate();
    }

    //另外一个组件请求启动服务时【startService()】时，调用
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        return super.onStartCommand(intent, flags, startId);
    }

    //销毁
    @Override
    public void onDestroy() {
        super.onDestroy();
    }
}
```

#### 2、配置Service

在AndroidManifest.xml文件的<application></application>标签中编写

```xml
<service android:name=".service.MyService"></service>
```

### 启动和关闭Service

#### 启动Service

```java
Intent intent = new Intent(this, ServiceName.class);
startService(intent);
```

#### 停止Service

```java
Intent intent = new Intent(this, ServiceName.class);
stopService(intent);
```

#### Service和Activity传输数据

布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".service.ServiceActivity">

    <Button
        android:id="@+id/startService"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="开启Service"
        android:onClick="startService"/>

</LinearLayout>
```

Activity文件

```java
public class ServiceActivity extends AppCompatActivity {

    Intent intent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_service);
        intent = new Intent(this, MyService.class);
        intent.putExtra("data", "这是使用Services传输的数据");
    }

    public void startService(View view) {
        startService(intent);
    }
}
```

Service文件

```java
public class MyService extends Service {
    //另外一个组件通过与服务绑定时【bindService()】，调用
    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        Log.v("TestService", "onBind");
        return null;
    }

    //另外一个组件与服务解除绑定时【unbindService()】，调用
    @Override
    public boolean onUnbind(Intent intent) {
        Log.v("TestService", "onUnbind");
        return super.onUnbind(intent);
    }

    //当旧组件与服务解绑后，另外一个新的组件与服务绑定，onUnbind()为true时，调用
    @Override
    public void onRebind(Intent intent) {
        Log.v("TestService", "onRebind");
        super.onRebind(intent);
    }

    //首次创建服务时，调用
    //只会调用一次
    @Override
    public void onCreate() {
        Log.v("TestService", "onCreate");
        super.onCreate();
    }

    //另外一个组件请求启动服务时【startService()】时，调用
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.v("TestService", "onStartCommand");

        //获取传输过来的数据
        String str = intent.getStringExtra("data");

        return super.onStartCommand(intent, flags, startId);
    }

    //销毁
    @Override
    public void onDestroy() {
        Log.v("TestService", "onDestroy");
        super.onDestroy();
    }
}
```

### 绑定和解绑Service

#### 绑定Service

```java
Intent intent = new Intent(this, ServiceName.class);
ServiceConnection serviceConnection = new ServiceConnection() {
    //绑定Service成功时调用
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {

    }

    //绑定Service失败时调用
    @Override
    public void onServiceDisconnected(ComponentName name) {

    }
};
bindService(Intent service，ServiceConnection conn，int flags);
```

#### 解绑Service

```java
unbindService(serviceConnection);
```

#### Service和Activity实现互相传输数据

布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".service.demo2.ServiceActivity">

    <Button
        android:id="@+id/bindService"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="绑定Service"
        android:onClick="bindService"/>

    <Button
        android:id="@+id/unbindService"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="解除绑定Service"
        android:onClick="unbindService"/>

    <Button
        android:id="@+id/setActivityToService"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="设置Activity传输到Service中"
        android:onClick="setActivityToService"/>

    <Button
        android:id="@+id/getServiceToActivity"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="获取Service的值到Activity中"
        android:onClick="getServiceToActivity"/>

</LinearLayout>
```

Activity文件

```java
public class ServiceActivity extends AppCompatActivity {

    Intent intent;

    MyServer2.MyBind myBind;
    ServiceConnection serviceConnection = new ServiceConnection() {
        //绑定Service成功时调用
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            myBind = (MyServer2.MyBind) service;
        }

        //绑定Service失败时调用
        @Override
        public void onServiceDisconnected(ComponentName name) {

        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_service2);

        intent = new Intent(this, MyServer2.class);
    }

    public void bindService(View view) {
        bindService(intent, serviceConnection, BIND_AUTO_CREATE);
    }

    public void unbindService(View view) {
        unbindService(serviceConnection);
    }


    public void setActivityToService(View view) {
        myBind.setValue(10);
    }

    public void getServiceToActivity(View view) {
        Toast.makeText(this, myBind.getValue() + "", Toast.LENGTH_SHORT).show();
    }
}
```

Service文件

```java
package com.lcxuan.study2.service.demo2;

import android.app.Service;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Binder;
import android.os.IBinder;
import android.util.Log;

import androidx.annotation.Nullable;

public class MyServer2 extends Service {

    int value;
    boolean flag;

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        Log.v("TestBindService", "绑定Service成功");
        return new MyBind();
    }

    @Override
    public boolean onUnbind(Intent intent) {
        Log.v("TestBindService", "解除绑定Service");
        return super.onUnbind(intent);
    }

    public class MyBind extends Binder{
        public void setValue(int i){
            value = i;
        }

        public int getValue(){
            return value;
        }
    }

    @Override
    public void onCreate() {
        flag = true;
        super.onCreate();
        new Thread(){
            @Override
            public void run() {
                super.run();
                while (flag){
                    value++;
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }.start();
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        flag = false;
    }
}
```

### IntentService【已淘汰】
