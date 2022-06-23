## TextView

### 样式

三个选项：

- normal：没有效果【默认】

- italic：斜体

- bold：粗体

可以同时设置两个特性，例如：斜体并且加粗

```xml
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="bold|italic"
        android:textColor="#000000"
        android:textStyle="bold|italic" />
```

代码中设置：使用TextView的`setTypeface`方法设置字体效果：

```java
tv1.setTypeface(null, Typeface.NORMAL); // 普通

tv1.setTypeface(null, Typeface.BOLD); // 加粗

tv2.setTypeface(null, Typeface.ITALIC); // 斜体

tv3.setTypeface(null, Typeface.BOLD_ITALIC); // 加粗和斜体
```

**注意：TextView设置斜体后，显示效果可能会出现被切掉一块**

可以增加一个占位符`&#x200A;`来撑开TextView的宽度

```xml
<string name="sample_text_1">显示效果&#x200A;</string>
<string name="sample_text_2">像是&#x200A;</string>
<string name="sample_text_3">被切掉了一块&#x200A;</string>
```



### 使用字体

默认情况下，TextView的typeface属性支持三种字体：

- sans【默认】

- serif

- monospace

以上三种字体只支持英文，如果显示中文，显示效果是一样的

```xml
<!-- sans字体 -->
<TextView
    android:text="Hello,World"
    android:typeface="sans" />
```

代码中设置字体：

```xml
 tv.setTypeface(Typeface.SERIF);
 tv.setTypeface(Typeface.SANS_SERIF);
 tv.setTypeface(Typeface.MONOSPACE);
```

引入字体库

需要引入ttf字体文件，将字体文件放入assets/font目录中，代码中使用AssetManager来获取字体：

```java
TextView tv1 = findViewById(R.id.tv1);
Typeface tf = Typeface.createFromAsset(getAssets(), "fonts/otherFont.ttf");
tv1.setTypeface(tf); // 使用字体
```


