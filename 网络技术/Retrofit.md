## Retrofit使用

官方地址：[Retrofit (square.github.io)](https://square.github.io/retrofit/)

```java
//两个依赖
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
```

**添加权限**

```java
<uses-permission android:name="android.permission.INTERNET"/>
```

**添加安全策略**

资源文件中添加xml文件夹， 新建network_security_config的xml文件

```xml
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

同步请求：execute

异步请求：enqueue

### 实现步骤

1. 接口申明
2. 创建Retrofit对象
3. 请求接口

**1、接口申明**

```java
public interface HttpService {
    //post方式接口
    @FormUrlEncoded
    @POST("user/register")
    Call<ResponseBody> register(@Field("username") String username, @Field("password") String password, @Field("repassword") String repassword);

    //get方式接口
    @GET("project/list/1/json")
    Call<ResponseBody> getList(@Query("cid") int cid);
}
```

**2、创建Retrofit对象**

```java
Retrofit retrofit = new Retrofit.Builder()
    .addConverterFactory(GsonConverterFactory.create())
    .baseUrl("https://www.wanandroid.com/")
    .build();

HttpService httpService = build.create(HttpService.class);
```

### post请求

1、同步请求

```java
new Thread(){
    @Override
    public void run() {
        try {
            Response<ResponseBody> register = httpService.register("test12345671231", "1234567890", "1234567890").execute();
            Log.v("test", register.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}.start();
```

2、异步请求

```java
httpService.register("test12312312", "1234567890", "1234567890").enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        try {
            Log.v("test", response.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {

    }
});
```

### get请求

1、同步请求

```java
new Thread(){
    @Override
    public void run() {
        try {
            Response<ResponseBody> getList = httpService.getList(294).execute();
            Log.v("test", getList.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}.start();
```

2、异步请求

```java
httpService.getList(294).enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        try {
            Log.v("test", response.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {

    }
});
```

### Retroft转换器

在接收到服务器的响应后，无论是OkHttp还是Retrofit都只能接收到String类型的数据

在实际开发中，可以使用GSON库进行反序列化的操作

1、创建Bean类

```java
public class WanAndroidBean {
    private int errorCode;
    private String errorMsg;
    private Data data;

    public int getErrorCode() {
        return errorCode;
    }

    public void setErrorCode(int errorCode) {
        this.errorCode = errorCode;
    }

    public String getErrorMsg() {
        return errorMsg;
    }

    public void setErrorMsg(String errorMsg) {
        this.errorMsg = errorMsg;
    }

    public Data getData() {
        return data;
    }

    public void setData(Data data) {
        this.data = data;
    }

    public class Data{
        private boolean admin;
        private List<String> chapterTops;
        private int coinCount;
        private List<Integer> collectIds;
        private String email;
        private String icon;
        private long id;
        private String nickname;
        private String password;
        private String publicName;
        private String token;
        private int type;
        private String username;

        public void setAdmin(boolean admin) {
            this.admin = admin;
        }

        public boolean getAdmin() {
            return admin;
        }

        public void setChapterTops(List<String> chapterTops) {
            this.chapterTops = chapterTops;
        }

        public List<String> getChapterTops() {
            return chapterTops;
        }

        public void setCoinCount(int coinCount) {
            this.coinCount = coinCount;
        }

        public int getCoinCount() {
            return coinCount;
        }

        public void setCollectIds(List<Integer> collectIds) {
            this.collectIds = collectIds;
        }

        public List<Integer> getCollectIds() {
            return collectIds;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getEmail() {
            return email;
        }

        public void setIcon(String icon) {
            this.icon = icon;
        }

        public String getIcon() {
            return icon;
        }

        public void setId(long id) {
            this.id = id;
        }

        public long getId() {
            return id;
        }

        public void setNickname(String nickname) {
            this.nickname = nickname;
        }

        public String getNickname() {
            return nickname;
        }

        public void setPassword(String password) {
            this.password = password;
        }

        public String getPassword() {
            return password;
        }

        public void setPublicName(String publicName) {
            this.publicName = publicName;
        }

        public String getPublicName() {
            return publicName;
        }

        public void setToken(String token) {
            this.token = token;
        }

        public String getToken() {
            return token;
        }

        public void setType(int type) {
            this.type = type;
        }

        public int getType() {
            return type;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        public String getUsername() {
            return username;
        }

        @Override
        public String toString() {
            return "Data{" +
                    "admin=" + admin +
                    ", chapterTops=" + chapterTops +
                    ", coinCount=" + coinCount +
                    ", collectIds=" + collectIds +
                    ", email='" + email + '\'' +
                    ", icon='" + icon + '\'' +
                    ", id=" + id +
                    ", nickname='" + nickname + '\'' +
                    ", password='" + password + '\'' +
                    ", publicName='" + publicName + '\'' +
                    ", token='" + token + '\'' +
                    ", type=" + type +
                    ", username='" + username + '\'' +
                    '}';
        }
    }

    @Override
    public String toString() {
        return "WanAndroidBean{" +
                "errorCode=" + errorCode +
                ", errorMsg='" + errorMsg + '\'' +
                ", data=" + data +
                '}';
    }
}
```

2、获取接口数据

```java
Retrofit build = new Retrofit.Builder()
    .baseUrl("https://www.wanandroid.com/")
    .addConverterFactory(GsonConverterFactory.create())    //添加转换器
    .build();
HttpService httpService = build.create(HttpService.class);

new Thread(){
    @Override
    public void run() {
        try {
            Response<WanAndroidBean> response = httpService.login("test123", "1").execute();
            WanAndroidBean body = response.body();
            WanAndroidBean.Data data = body.getData();
            Log.v("test", data.getNickname());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}.start();
```

### Retroft适配器

Retrofit的接口方法返回类型必须是Call，如果能将Call改为RxJava中的Flowable，对于嵌套的情况，可以得到非常方便优雅的解决，这就是适配器的功能

适配器可以帮助我们转换为其他类型

**引入两个包依赖包**

```java
implementation 'com.squareup.retrofit2:adapter-rxjava3:2.9.0'
implementation 'io.reactivex.rxjava3:rxandroid:3.0.0'
```

### 多种场景

但是显然服务器不可能总是给我们提供静态类型的接口，在很多场景下，接口地址中的部分内容可能会是动态变化的，比如如下的接口地址：

```java
GET http://example.com/<page>/get_data.json
```

代码示例：

```java
public interface AppService {

    @GET("{page}/get_data.json")
    Call<App> getData(@Path("page") int page);
}
```

例如如下的接口地址：

```java
GET http://example.com/get_data.json?u=<user>&t=<token>
```

代码示例：

```java
public interface AppService {

    @GET("get_data.json")
    Call<App> getData(@Query("u") String user, @Query("t") String token);
}
```

HTTP并不是只有GET请求这一种类型，而是有很多种，其中比较常用的有GET、POST、PUT、PATCH、DELETE这几种。它们之间的分工也很明确，简单概括的话，GET请求用于从服务器获取数据，POST请求用于向服务器提交数据，PUT和PATCH请求用于修改服务器上的数据，DELETE请求用于删除服务器上的数据。

比如服务器提供了如下接口地址：

```java
DELETE http://example.com/data/<id>
```

代码示例：

```java
public interface AppService {

    @DELETE("data/{id}")
    Call<ResponseBody> deleteData(@Path("id") int id);
}
```

使用ResponseBody，表示Retrofifit能够接收任意类型的响应数据，并且不会对响应数据进行解析

使用POST请求来提交数据，需要将数据放到HTTP请求的body部分，这个功能在Retrofifit中可以借助@Body注解来完成，代码如下：

```java
public interface AppService {

    @POST("data/create")
    Call<ResponseBody> createData(@Body Data data);
}
```

当Retrofifit发出POST请求时，就会自动将Data对象中的数据转换成JSON格式的文本，并放到HTTP请求的body部分，服务器在收到请求之后只需要从body中将这部分数据解析出来

有些服务器接口还可能会要求我们在HTTP请求的header中指定参数，比如：

```java
GET http://example.com/get_data.json
User-Agent: okhttp
Cache-Control: max-age=0
```

静态header声明：

```java
public interface AppService {

    @Headers({"User-Agent: okhttp", "Cache-Control: max-age=0"})
    @GET("get_data.json")
    Call<App> getAppData();
}
```

动态header声明：

```java
public interface AppService {

    @GET("get_data.json")
    Call<App> getAppData(@Header("User-Agent") String userAgent, @Header("Cache-Control") String cacheControl);
}
```
