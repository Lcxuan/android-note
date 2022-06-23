##  HTTP协议访问网络

### 使用HttpURLConnection

需要设置两个权限：INTERNET、ACCESS_NETWORK_STATE

并且需要设置安全策略，在res -> 新建xml目录 -> 新建network_security_config.xml文件，内容如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="ture" />
</network-security-config>
```



获取HttpURLConnection实例：

```java
URL url = new URL("http://www.baidu.com");
//获取HttpURLConnection实例
HttpURLConnection connection = (HttpURLConnection) url.openConnection();
```

设置Http请求所使用的方法，常用有两种：GET和POST：

```java
//设置请求所使用的方法
connection.setRequestMethod("GET");
```

设置连接超时和读取超时：

```java
//设置连接超时毫秒
connection.setConnectTimeout(8000);
//设置读取超时毫秒
connection.setReadTimeout(8000);
```

获取响应码：

```java
//获取响应码
int responseCode = connection.getResponseCode();
```

获取服务器返回的输入流：

```java
//获取服务器返回的输入流
InputStream inputStream = connection.getInputStream();
```

将HTTP连接关闭：

```java
//将Http连接关闭
connection.disconnect();
```

代码示例：

**布局**

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/request"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="发送请求" />

    <TextView
        android:id="@+id/response_text"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

</LinearLayout>
```

代码实现：

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView responseText;
    private Button request;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        request = findViewById(R.id.request);
        responseText = findViewById(R.id.response_text);
        request.setOnClickListener(this);
    }

    private void showResponse(StringBuilder response){
        //在主线程中更新
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                responseText.setText(response);
            }
        });
    }

    @Override
    public void onClick(View v) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                HttpURLConnection connection = null;
                BufferedReader reader = null;
                try {
                    //获取HttpURLConnection实例
                    URL url = new URL("https://www.baidu.com/");
                    connection = (HttpURLConnection) url.openConnection();
                    //设置请求的方法
                    connection.setRequestMethod("GET");

                    //设置连接超时和读取超时
                    connection.setConnectTimeout(8000);
                    connection.setReadTimeout(8000);

                    //获取响应码
                    int responseCode = connection.getResponseCode();
                    if (responseCode == HttpURLConnection.HTTP_OK){
                        //获取服务器返回得输入流
                        InputStream inputStream = connection.getInputStream();

                        reader = new BufferedReader(new InputStreamReader(inputStream));
                        StringBuilder result = new StringBuilder();
                        String len = null;
                        while ((len = reader.readLine()) != null){
                            result.append(len);
                        }
                        showResponse(result);
                        
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    if (reader != null){
                        try {
                            reader.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                    if (connection != null){
                        connection.disconnect();
                    }
                }
            }
        }).start();
    }
}
```



当需要提交数据给服务器时，需要讲HTTP请求的方法改成POST，并在获取输入流之前把提交的数据写出即可，代码如下：

```java
connection.setRequestMethod("POST");
DataOutputStream dataOutputStream = new DataOutputStream(connection.getOutputStream());
dataOutputStream.writeBytes("username=admin&password=123456");
```

