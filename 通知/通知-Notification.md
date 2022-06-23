## Notofication-通知

Android系统中比较有特色的一个功能，当某个应用程序希望向用户发出一些提示消息，而该应用程序又不再前台运行时，可以借助通知来实现。

首先会表现为一个图标的形式，当用户向下滑动时，展示除通知具体的内容

### 通知渠道

就是每条通知都要属于一个对应的渠道。每个应用程序都可以自由地创建当前应用拥有哪些通知渠道，但是这些通知渠道的控制权是掌握在用户受伤的。用户可以自由地选择这些通知渠道的重要程度，是否响铃、是否震动或者是否要关闭这个渠道的通知

首先需要一个NotificationManager来管理通知，可以调用Context中getSystemService()方法获取。getSystemService()方法接收一个字符串参数，用于确定获取系统的哪个服务。传入Context.NOTIFICATION_SERVICE即可

```java
NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
```

接下来要使用`NotificationChannel`类构建一个通知渠道，并调用`NotificationManager.createNotificationChannel()`方法完成创建。

由于`NotificationChannel`类和`NotificationManager.createNotificationChannel()`方法都是Android8.0新增的API，因此我们使用中需要版本判断

```java
if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
    //参数一：渠道ID
    //参数二：渠道名称
    //参数三：重要等级
    //IMPORTANCE_NONE：关闭通知
    //IMPORTANCE_MIN：开启通知, 不会弹出, 没有提示音, 状态栏中无显示
    //IMPORTANCE_LOW：开启通知, 不会弹出, 不发出提示音, 状态栏中显示
    //IMPORTANCE_DEFAULT：开启通知, 不会弹出, 发出提示音, 状态栏中显示
    //IMPORTANCE_HIGH：开启通知, 会弹出, 发出提示音, 状态栏中显示
    NotificationChannel channel = new NotificationChannel("channelId", "channelName", NotificationManager.IMPORTANCE_DEFAULT);

       //创建渠道
    notificationManager.createNotificationChannel(channel);
}
```

### 基本用法

通知的用法比较灵活，可以在Activity里创建，也可以在广播接收器里创建，也可以在服务中创建。

相比于广播接收器和服务，在Activity中创建通知的场景比较少，因为一般只有当程序进入到后台的时候才需要通知

#### 实现步骤

AndroidX库中提供了一个NotificationCompat类，使用这个类的构造器创建Notification对象

```java
Notification notification = new NotificationCompat.Builder(this, "channelId").buile();
```

`NotificationCompat.Builder()`构造器中接收两个参数：

- 第一个参数：Context
- 第二个参数：渠道ID，需要和我们创建渠道时指定的渠道ID相匹配才行

当然，上述的代码只是创建一个空的Notification对象，并没有什么实际作用，可以在最终buile()方法之前设置方法创建一个丰富的Notfication对象

```java
Notification notification = new NotificationCompat.Builder(this, "channelId")
    .setContentTitle("This is content title")
    .setContentText("This is content text")
    .setSmallIcon(R.drawable.ic_launcher_background)
    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.ic_launcher_background))
    .build();
```

.setContentTitle()：用于指定通知的标题

.setContentText()：用于指定通知的正文内容

.setSmallIcon()：用于设置通知的小图标

.setLargeIcon()：用于设置通知的大图标

最终只需要调用`NotificationManager`的`notify()`方法就可以让通知显示出来，`notify()`方法接收两个参数：

- 第一个参数：id，要保证每个通知指定的id都是不同的
- 第二个参数：Notification对象

```java
notificationManager.notify(1, notification);
```

### 具体实现

新建一个NotificationTest项目，并修改activity_main.xml中的代码，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/send_notice"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send Notice"/>

</LinearLayout>
```

接下来修改MainActivity代码，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    Button sendNotice;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        sendNotice = findViewById(R.id.send_notice);

        //获取NotificationManager管理通知
        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);


        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
            //参数一：渠道ID
            //参数二：渠道名称
            //参数三：重要等级
            //IMPORTANCE_NONE：关闭通知
            //IMPORTANCE_MIN：开启通知, 不会弹出, 没有提示音, 状态栏中无显示
            //IMPORTANCE_LOW：开启通知, 不会弹出, 不发出提示音, 状态栏中显示
            //IMPORTANCE_DEFAULT：开启通知, 不会弹出, 发出提示音, 状态栏中显示
            //IMPORTANCE_HIGH：开启通知, 会弹出, 发出提示音, 状态栏中显示
            NotificationChannel channel = new NotificationChannel("channelId", "渠道1", NotificationManager.IMPORTANCE_DEFAULT);
            notificationManager.createNotificationChannel(channel);
        }

        sendNotice.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Notification notification = new NotificationCompat.Builder(MainActivity.this, "channelId")
                        .setContentTitle("This is content title")
                        .setContentText("This is content text")
                        .setSmallIcon(R.drawable.ic_launcher_background)
                        .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.ic_launcher_background))
                        .build();

                notificationManager.notify(1, notification);
            }
        });
    }
}
```

### 通知的详细信息

一般我们看到程序发送过来的程序，会下意思的认为这条通知是可以点击的。但是，此时我们去点击这条通知时，会发现没有任何效果。

如果想要实现点击后的效果，还需要使用`PendingIntent`来进行相应的代码设置

`PendingIntent`和`Intent`有些类似，都可以指明某一个“意图”，都可以用来启动Activity、启动Service以及发送广播等

不同的是，`Intent`倾向于立即执行某个操作，而`PendingIntent`倾向于某个合适的时机执行某个操作，所以可以将`PengdingIntent`理解为延迟执行的`Intent`

主要提供几个静态方法用于获取`PendingIntent`的实例，例如：getActivity()方法、getBroadcast()方法和getService()方法。这几个方法接收的参数是相同的

- 第一个参数：Context
- 第二个参数：一般用不到，传入0即可
- 第三个参数：Intent对象，可以通过这个对象构建`PendingIntent`意图
- 第四个参数：用于确定`PendingIntent`的行为
  - FLAG_ONE_SHOT： 表示返回的PendingIntent仅能执行一次，执行完后自动消失
  - FLAG_NO_CREATE： 表示如果描述的PendingIntent不存在，并不创建相应的PendingIntent，而是返回NULL
  - FLAG_CANCEL_CURRENT： 表示相应的PendingIntent已经存在，则取消前者，然后创建新的PendingIntent
  - FLAG_UPDATE_CURRENT： 表示更新的PendingIntent，如果构建的PendingIntent已经存在，则替换它【常用】

并且`NotificationCompat.Builder()`这个构造器还可以连缀一个`setContentIntent()`方法，接收的参数就是`PendingIntent`对象，当用户点击这条通知时就会执行相应的逻辑

此时我们新建一个NotificationActivity，修改activity_notification.xml文件中的代码，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".NotificationActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="24sp"
        android:text="This is notification layout"/>

</LinearLayout>
```

接着修改MainActivity中的代码，给通知加上点击功能，代码如下：

```java
sendNotice.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent(MainActivity.this, NotificationActivity.class);
        PendingIntent pendingIntent = PendingIntent.getActivity(MainActivity.this, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);

        Notification notification = new NotificationCompat.Builder(MainActivity.this, "channelId")
            .setContentTitle("This is content title")
            .setContentText("This is content text")
            .setSmallIcon(R.drawable.ic_launcher_background)
            .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.ic_launcher_background))
                .setContentIntent(pendingIntent)
            .build();

        notificationManager.notify(1, notification);
    }
});
```

此时点击通知之后就会发现跳转到NotificationActivity中了，但是系统状态栏上的通知还没有消失，这个是因为我们没有在代码上对该通知进行取消，它就会一直显示在系统状态栏上

解决方法有两种：

- 第一种就是在`NotificationCompat.Builder()`这个构造器上在连缀一个`setAutoCancel()`方法
- 另外一种就是显示调用`NotificationManager.cancel()`方法将它取消

**第一种写法：**

```java
Notification notification = new NotificationCompat.Builder(MainActivity.this, "channelId")
    .setContentTitle("This is content title")
    .setContentText("This is content text")
    .setSmallIcon(R.drawable.ic_launcher_background)
    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.ic_launcher_background))
    .setContentIntent(pendingIntent)
    .setAutoCancel(true)    //true表示当点击这个通知的时候，通知自动取消
    .build();
```

**第二种写法：**

在点击通知之后的页面中，修改代码，代码如下：

```java
public class NotificationActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_notification);

        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        //这里传入的1和我们发送通知的1要一致
        notificationManager.cancel(1);
    }
}
```

### 通知进阶

setStyle()：允许我们构建出富文本的通知内容。也就是说通知中不光可以有文字和图标，还可以包含其他东西

### 开启悬浮通知权限

即允许在屏幕上方弹出通知

在AndroidManifest.xml文件中添加权限，权限代码如下：

```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```
