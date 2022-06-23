## 解析JSON格式数据

### JSONObject

代码示例：

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
                        .url("http://192.168.1.116/get_data.json")
                        .build();

                try {
                    Response response = client.newCall(request).execute();
                    String result = response.body().string();

                    parseJSONByJSONObject(result);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    private void parseJSONByJSONObject(String jsonData){
        try {
            JSONArray jsonArray = new JSONArray(jsonData);
            for (int i = 0; i < jsonArray.length(); i++) {
                JSONObject jsonObject = jsonArray.getJSONObject(i);
                int id = jsonObject.getInt("id");
                String name = jsonObject.getString("name");
                String version = jsonObject.getString("version");

                Log.e("TAG", "parseJSONByJSONObject: " + id);
                Log.e("TAG", "parseJSONByJSONObject: " + name);
                Log.e("TAG", "parseJSONByJSONObject: " + version);
            }
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
}
```



### GSON

例如：一段JSON格式的数据如下：

```json
{
    "name": "Tom",
    "age" :	20
}
```

接着我们可以定义一个Person类，并加入name和age两个字段，然后只需简单调用如下代码就可以讲JSON数据自动解析成一个Person对象：

```java
Gson gson = new Gson();
Person person = gson.fromJson(jsonData, Person.class);
```

如果需要解析的是一段JSON数组，需要借助TypeToken将需要解析的数据类型传入到formJson()方法中，代码如下：

```java
List<Person> people = gson.fromJson(jsonData, new TypeToken<List<Person>>(){}.getType());
```



代码示例

创建App实体类：

```java
public class App {
    private String id;
    private String name;
    private String version;

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getVersion() {
        return version;
    }
}
```

功能代码：

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
                        .url("http://192.168.1.116/get_data.json")
                        .build();
                try {
                    Response response = client.newCall(request).execute();
                    String result = response.body().string();

                    parseJSONByGSON(result);

                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    private void parseJSONByGSON(String jsonData){
        Gson gson = new Gson();
        List<App> appList = gson.fromJson(jsonData, new TypeToken<List<App>>(){}.getType());
        for (App app: appList) {
            Log.e("TAG", "id：" + app.getId());
            Log.e("TAG", "name：" + app.getName());
            Log.e("TAG", "version：" + app.getVersion());
        }
    }
}
```

