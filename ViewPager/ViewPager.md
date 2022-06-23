## ViewPager

它的效果就是滑动切换View的效果



### 创建适配器

首先，需要创建一个适配器，并且需要继承`PageAdapter`，代码如下：

```java
public class ViewPagerAdapter extends PagerAdapter {
    
    //返回的数量
    @Override
    public int getCount() {
        return 0;
    }

    //为给定的位置创建页面
    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return false;
    }

    //显示View
    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {
        return super.instantiateItem(container, position);
    }

    //销毁View
    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        super.destroyItem(container, position, object);
    }
}
```



### 设置适配器

```java
viewPager.setAdapter(new ViewPagerAdapter());
```



### 设置监听器

```java
viewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {

            //页面滚动时
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            //页面被选中
            @Override
            public void onPageSelected(int position) {

            }

            //操作屏幕时发生的变化
            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
```

三个方法的执行顺序为：用手指拖动翻页时，最先执行一遍onPageScrollStateChanged，然后不断执行onPageScrolled，放手指的时候，直接立即执行一次onPageScrollStateChanged，然后立即执行一次onPageSelected，然后再不断执行onPageScrollStateChanged，最后执行一次onPageScrollStateChanged。



### 代码示例

首先，创建显示的ViewPager的Item布局，依次创建3个布局，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/black">

</LinearLayout>
```



布局实现，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```



创建适配器，代码如下：

```java
public class ViewPagerAdapter extends PagerAdapter {

    List<View> viewList;

    public ViewPagerAdapter(List<View> viewList) {
        this.viewList = viewList;
    }

    //返回的数量
    @Override
    public int getCount() {
        return viewList.size();
    }

    //为给定的位置创建页面
    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return view == object;
    }

    //显示View
    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {
        container.addView(viewList.get(position));
        return viewList.get(position);
    }

    //销毁View
    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        container.removeView(viewList.get(position));
    }
}
```



设置适配器，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    private ViewPager viewPager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initView();

        List<View> viewList = new ArrayList<>();
        View view1 = View.inflate(this, R.layout.item_viewpager_1, null);
        View view2 = View.inflate(this, R.layout.item_viewpager_2, null);
        View view3 = View.inflate(this, R.layout.item_viewpager_3, null);
        viewList.add(view1);
        viewList.add(view2);
        viewList.add(view3);

        viewPager.setAdapter(new ViewPagerAdapter(viewList));
    }

    private void initView() {
        viewPager = findViewById(R.id.viewPager);
    }
}
```

