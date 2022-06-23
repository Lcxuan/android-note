## ViewPager2

### ViewPager和ViewPager2的区别

在xml中使用方法一样，代码如下：

```xml
<androidx.viewpager2.widget.ViewPager2
	android:id="@+id/viewPager2"
	android:layout_width="match_parent"
	android:layout_height="match_parent"/>
```



可以设置垂直、水平方向，代码如下：

```java
viewPager2.setOrientation(ViewPager2.ORIENTATION_VERTICAL);
```



在ViewPage中使用监听器需要重写三个方法，但是在ViewPager2中可以重写三个方法中的其中一个，代码如下：

```java
viewPager2.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
    @Override
    public void onPageSelected(int position) {
        super.onPageSelected(position);
    }
});
```



适配器使用RecyclerView.Adapter



### 代码示例

首先，创建显示的ViewPager2的Item布局，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/item_viewPager2"
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

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager2"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```

创建适配器，代码如下：

```java
public class ViewPagerAdapter extends RecyclerView.Adapter<ViewPagerAdapter.MyViewHolder> {

    List<Integer> viewList;

    public ViewPagerAdapter(List<Integer> viewList) {
        this.viewList = viewList;
    }

    @NonNull
    @Override
    public ViewPagerAdapter.MyViewHolder onCreateViewHolder(@NonNull  ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_viewpager_1, parent, false);
        return new MyViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewPagerAdapter.MyViewHolder holder, int position) {
        Integer color = viewList.get(position);
        holder.itemViewPager2.setBackgroundColor(color);
    }

    @Override
    public int getItemCount() {
        return viewList.size();
    }

    static class MyViewHolder extends RecyclerView.ViewHolder{

        private LinearLayout itemViewPager2;

        public MyViewHolder(@NonNull View itemView) {
            super(itemView);

            itemViewPager2 = itemView.findViewById(R.id.item_viewPager2);
        }
    }
}
```

设置适配器，代码如下：

```java
public class MainActivity extends AppCompatActivity {

    private ViewPager2 viewPager2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initView();

        viewPager2.setOrientation(ViewPager2.ORIENTATION_HORIZONTAL);
        List<Integer> viewList = new ArrayList<>();
        viewList.add(Color.BLUE);
        viewList.add(Color.BLACK);
        viewList.add(Color.RED);
        viewPager2.setAdapter(new ViewPagerAdapter(viewList));
    }

    private void initView() {
        viewPager2 = findViewById(R.id.viewPager2);
    }
}
```

