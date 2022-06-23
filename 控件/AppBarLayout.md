## AppBarLayout

一种支持响应手势的布局

用于ToolBar**收缩和展开的效果**

可以实现界面向下滚动ToolBar收缩



### 实现布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"

    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar"
        android:layout_height="wrap_content"
        android:layout_width="match_parent">

        <androidx.appcompat.widget.Toolbar
            android:layout_height="?attr/actionBarSize"
            android:layout_width="match_parent"
            app:layout_scrollFlags="scroll|enterAlways">

            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:src="@drawable/navigationview_header_image"/>

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="首页"/>

        </androidx.appcompat.widget.Toolbar>

        <com.google.android.material.tabs.TabLayout
            android:id="@+id/tabs"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_scrollFlags="scroll|enterAlways"

            app:tabMode="scrollable">

            <com.google.android.material.tabs.TabItem
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                android:text="Tab1" />

            <com.google.android.material.tabs.TabItem
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                android:text="Tab2" />

            <com.google.android.material.tabs.TabItem
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                android:text="Tab3" />
        </com.google.android.material.tabs.TabLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fillViewport="true"
        android:clipToPadding="true"
        app:layout_behavior="com.google.android.material.appbar.AppBarLayout$ScrollingViewBehavior">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            tools:context=".AppBarLayoutActivity">

            <androidx.viewpager.widget.ViewPager
                android:id="@+id/appBarLayoutViewPager"
                android:layout_width="match_parent"
                android:layout_height="match_parent"/>

        </LinearLayout>
    </androidx.core.widget.NestedScrollView>

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:src="@android:drawable/ic_input_add"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp" />"
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```



### 功能实现

```java
public class AppBarLayoutActivity extends AppCompatActivity {

    TabLayout tabLayout;
    ViewPager viewPager;

    //定义标题数组
    String[] titleArr = new String[]{
            "新闻", "财经", "娱乐"
    };

    //定义切换的Fragment集合
    List<Fragment> fragmentList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_app_bar_layout);

        tabLayout = findViewById(R.id.tabs);
        viewPager = findViewById(R.id.appBarLayoutViewPager);

        fragmentList.add(new AppBarLayout1Fragment());
        fragmentList.add(new AppBarLayout2Fragment());
        fragmentList.add(new AppBarLayout3Fragment());

        //设置Viewpager适配器
        viewPager.setAdapter(new AppBarLayoutViewpagerAdapter(getSupportFragmentManager(), 0, fragmentList));

        //tabLayout和ViewPager关联
        tabLayout.setupWithViewPager(viewPager);

    }

    class AppBarLayoutViewpagerAdapter extends FragmentPagerAdapter{

        List<Fragment> fragments;

        public AppBarLayoutViewpagerAdapter(@NonNull @NotNull FragmentManager fm, int behavior, List<Fragment> fragmentList) {
            super(fm, behavior);
            this.fragments = fragmentList;
        }

        @NonNull
        @NotNull
        @Override
        public Fragment getItem(int position) {
            return fragments.get(position);
        }

        @Override
        public int getCount() {
            return fragments.size();
        }

        @Nullable
        @org.jetbrains.annotations.Nullable
        @Override
        public CharSequence getPageTitle(int position) {
            return titleArr[position];
        }
    }
}
```





