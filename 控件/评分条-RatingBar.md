## 评分条-RatingBar

**重要属性**

- android:numstars：星星个数
- android:rating：默认点亮星星个数
- android:stepSize：步进数，每次增加多少个星
- android:islndicator:
- android:progressTint：设置星星颜色



RatingBar.getRating()：获取当前选择的星数

```java
String content = "选择的星数：" + ratingBar.getRating();
```



