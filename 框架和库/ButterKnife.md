## ButterKnife

一个专注于Android系统的View注入框架



仓库地址：[ButterKnife仓库地址](https://github.com/JakeWharton/butterknife)



### 控件绑定

```java
@BindView(R.id.textView)
TextView textView;
```



### 绑定处理

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_butter_knife);
    
    //绑定处理
    ButterKnife.bind(this);
}
```



### 事件绑定

```java
@OnClick(R.id.button)
public void onClick(View view){
    switch (view.getId()){
        case R.id.button:
            Toast.makeText(this, "点击按钮", Toast.LENGTH_SHORT).show();
            break;
    }
}
```

