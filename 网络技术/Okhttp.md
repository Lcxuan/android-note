## OKhttp

需要设置两个权限：INTERNET、ACCESS_NETWORK_STATE

并且需要设置安全策略，在res -> 新建xml目录 -> 新建network_security_config.xml文件，内容如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="ture" />
</network-security-config>
```



### 具体用法

首先需要创建一个OkHttpClient的实例，代码如下：

```java
//创建OkHttpClient实例
OkHttpClient okHttpClient = new OkHttpClient();
```

发送一条空的HTTP请求，并没有实际作用，代码如下：

```java
//发送空的Http请求
Request request = new Request.Builder().build();
```

通过url()方法来设置目标的网络地址，代码如下：

```java
Request request = new Request.Builder()
    .url("https://www.baidu.com")
    .build();
```



#### 同步操作

通过OkHttpClient的实例调用newCall()方法来创建一个Call对象，并调用execute()方法执行同步操作来发送请求来获取服务器返回的数据，代码如下：

```java
Response response = okHttpClient.newCall(request).execute();
```

其中Response对象就是服务器返回的数据，可以通过以下方法来得到返回的具体内容，代码如下：

```java
String responseData = response.body().string();
```



#### 异步操作

通过OkHttpClient的实例调用newCall()方法来创建一个Call对象，并调用enqueue()方法执行异步操作来发送请求来获取服务器返回的数据，代码如下：

```java
client.newCall(request).enqueue(new Callback() {
    @Override   //失败
    public void onFailure(@NotNull Call call, @NotNull IOException e) {

    }

    @Override   //成功
    public void onResponse(@NotNull Call call, @NotNull Response response) throws IOException {
        
    }
});
```



#### POST请求

当使用POST请求时，需要先构建出一个RequestBody对象来存储需要提交的参数，代码如下：

```java
FormBody formBody = new FormBody.Builder()
    .add("username", "admin")
    .add("password", "123456")
    .build();
```

接着需要在Request.Builder中调用post()方法，将RequestBody对象传入，代码如下：

```java
Request request = new Request.Builder()
    .url("https://www.baidu.com")
    .post(formBody)
    .build();
```



### 示例代码

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private Button btn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btn = findViewById(R.id.btn);
        btn.setOnClickListener(this);

    }

    @Override
    public void onClick(View v) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                OkHttpClient client = new OkHttpClient();

                Request request = new Request.Builder()
                        .url("https://www.baidu.com")
                        .build();

                try {
                    Response response = client.newCall(request).execute();
                    String result = response.body().string();
                    Log.e("TAG", "OKHttp:" + result);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```

