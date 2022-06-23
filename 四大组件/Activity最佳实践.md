假设SecondActivity中需要两个重要的参数，需要在启动的时候就传过来，因此我们可能会写出这样的代码

```java
Intent intent = new Intent(Firstactivity.this, Secondactivity.class);
intent.putextra("param1", "datal");
intent.putextra("param2", "data2");
startactivity(intent);
```



但是在实际的开发中，有可能SecondActivity不是由我自己来开发的，所以我们可能不知道传什么样的参数过来，因此我们在开发中可以这样写：

```java
pulic class SecondActivity extend Activity{
    public static void actionStart(Context context, String data1, String data2){
        Intent intent = new Intent(context, Secondactivity.class);
		intent.putextra("param1", datal);
		intent.putextra("param2", data2);
		startactivity(intent);
    }
}
```

以上的代码就可以完成对Intent的构建，另外所有SecondActivity中需要的数据都是通过actionStart()方法的参数传递过来的，然后把他们存储Intent中，最后调用startActivity()方法启动SecondActivity