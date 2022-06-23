## 随时关闭

### 实现方法

实现一个集合类将所有的Activity进行管理、实现添加和删除Activity、删除所有Activity的方法

### 集合类实现

```java
public class ActivityControl {
    private static List<Activity> activityList = new ArrayList<>();

    //添加Activity到集合中
    public static void addActivity(Activity activity){
        activityList.add(activity);
    }

    //移除集合中的Activity
    public static void removeActivity(Activity activity){
        activityList.remove(activity);
    }

    //关闭集合中所有Activity
    public static void finishAll(){
        for (Activity activity : activityList) {
            if (!activity.isFinishing()){
                activity.finish();
            }
        }
        //杀掉当前的进程
        //killProcess()方法用于杀掉一个进程，接收一个进程id的参数，可以通过myPiy()方法获取
        android.os.Process.killProcess(android.os.Process.myPid());
    }
}
```

接着在Activity中的onCreate()和onDestroy()方法中分别调用addActivity()、removeActivity()方法

或者实现一个Base类，继承Activity类，在Base类中重写onCreate()和onDestroy()方法，分别调用addActivity()、removeActivity()，在实现的Activity类中继承Base类

