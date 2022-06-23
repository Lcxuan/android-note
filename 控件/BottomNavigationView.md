## BottomNavigationView

```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    app:menu="@menu/bottom_navigationview"		//设置菜单
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```



可以设置setOnNavigationItemSelectedListener事件进行列表选择



### 顶部导航栏实现

#### 布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".BottomNavigationViewActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/bottom_navigation_view_viewPager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

    </androidx.viewpager.widget.ViewPager>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_navigation_view"
        app:menu="@menu/bottom_navigationview"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
    
</LinearLayout>
```



#### 功能实现

```java
public class BottomNavigationViewActivity extends AppCompatActivity {

    private ViewPager viewPager;
    private BottomNavigationView bottomNavigationView;
    private MenuItem menuItem;

    //创建切换的Fragment集合
    List<Fragment> fragmentList = new ArrayList<>();
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_bottom_navigation_view);

        viewPager = findViewById(R.id.bottom_navigation_view_viewPager);
        bottomNavigationView = findViewById(R.id.bottom_navigation_view);

        fragmentList.add(new BottomNavigationViewFragment1());
        fragmentList.add(new BottomNavigationViewFragment2());
        fragmentList.add(new BottomNavigationViewFragment3());
        fragmentList.add(new BottomNavigationViewFragment4());

        viewPager.setAdapter(new BottomNavigationViewAdapter(getSupportFragmentManager(), 0, fragmentList));

        //将ViewPager和BottomNavigationView关联, 设置setOnNavigationItemSelectedListener事件列表选择
        bottomNavigationView.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull @NotNull MenuItem item) {
                switch (item.getItemId()){
                    case R.id.home:
                        viewPager.setCurrentItem(0);
                        break;
                    case R.id.friend:
                        viewPager.setCurrentItem(1);
                        break;
                    case R.id.friendsCircle:
                        viewPager.setCurrentItem(2);
                        break;
                    case R.id.my:
                        viewPager.setCurrentItem(3);
                        break;
                }

                return true;
            }
        });

        //设置切换ViewPager和BottomNavigationView的关联
        viewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            @Override
            public void onPageSelected(int position) {
                if (menuItem == null){
                    //初始化BottomNavigationView, 默认为0
                    menuItem = bottomNavigationView.getMenu().getItem(0);
                }

                //将上次的选择设置为false, 等待下次选择
                menuItem.setChecked(false);

                //设置最新的选择为true
                menuItem = bottomNavigationView.getMenu().getItem(position);
                menuItem.setChecked(true);
            }

            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
    }

    class BottomNavigationViewAdapter extends FragmentPagerAdapter{

        private List<Fragment> fragments;

        public BottomNavigationViewAdapter(@NonNull @NotNull FragmentManager fm, int behavior, List<Fragment> fragmentList) {
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
    }
}
```

