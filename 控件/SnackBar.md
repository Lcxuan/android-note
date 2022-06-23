## SnackBar

主要用于代替Toast

主要区别SnackBar可以滑动退出，也可以处理用户交互事件



### 功能代码

```java
Snackbar.make(view, "点击了", Snackbar.LENGTH_SHORT)
    .setAction("确定", new View.OnClickListener() {   //设置交互
        @Override
        public void onClick(View v) {
			//逻辑代码.
        }
    })
    .setActionTextColor(Color.GREEN)    //设置交互字体颜色
    .show();	//显示
```

