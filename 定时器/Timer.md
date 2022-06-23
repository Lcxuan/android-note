## Timer定时器的使用

在Android上有时候可能有些任务不是当时就执行了，而是过了规定的时间在执行此次任务，这时就可以使用到定时器了

```java
new Timer().schedule(new TimerTask() {
    @Override
    public void run() {
        Log.e("LCXUANTEST", "2秒后执行的定时器");
    }
}, 2000);
```



### 定时器的请求周期

其实定时器如果不销毁会一直执行的，但是如果定时器如果一直执行的话那么我们的程序时根本撑不了多长的事件，所以我们定时器用户要及时的关闭

```java
timer.cancel();
```

