## TabLayout

主要实现顶部滑动效果

用于一个Activity上有多个界面的展示



**一般和ViewPager使用**



### 设置布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".TabLayoutActivity">

    <!-- 设置TabLayout -->
    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </com.google.android.material.tabs.TabLayout>

    <!-- 设置ViewPager -->
    <androidx.viewpager.widget.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```



### 设置切换

```java
public class TabLayoutActivity extends FragmentActivity {

    private TabLayout tabLayout;
    private ViewPager viewPager;

    //定义tabLayout显示的文字数组
    String[] tabList = new String[]{
            "新闻", "财经", "娱乐"
    };

    //定义存放Fragment的集合
    List<Fragment> fragmentArrayList = new ArrayList();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tab_layout);

        tabLayout = findViewById(R.id.tabLayout);
        viewPager = findViewById(R.id.viewPager);

        //将所有Fragment添加到集合中
        fragmentArrayList.add(new TabLayout1Fragment());
        fragmentArrayList.add(new TabLayout2Fragment());
        fragmentArrayList.add(new TabLayout3Fragment());

        //设置ViewPager适配器
        viewPager.setAdapter(new ViewPagerAdapter(getSupportFragmentManager(), 0, fragmentArrayList));

        //将TabLayout和ViewPager关联
        tabLayout.setupWithViewPager(viewPager);
    }

    class ViewPagerAdapter extends FragmentPagerAdapter {

        private List<Fragment> fragmentList;

        public ViewPagerAdapter(@NonNull @NotNull FragmentManager fm, int behavior, List<Fragment> fragments) {
            super(fm, behavior);

            this.fragmentList = fragments;
        }

        @NonNull
        @NotNull
        @Override   //获取每一项
        public Fragment getItem(int position) {
            return fragmentList.get(position);
        }

        @Override   //获取所有
        public int getCount() {
            return fragmentList.size();
        }

        @Nullable
        @org.jetbrains.annotations.Nullable
        @Override   //获取页面标题
        public CharSequence getPageTitle(int position) {
            return tabList[position];
        }
    }
}
```



