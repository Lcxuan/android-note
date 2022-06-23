## Application

Application和Activity、Service一样都是Android框架的一个系统组件

当Android程序启动时系统会创建一个Application对象，用来存储系统的一些信息

Android系统自动会每个程序运行时创建一个Application类的对象且只创建一个



### 作用

1. 初始化应用程序级别的资源，如：全局对象、环境配置变量、图片资源初始化、推送服务的注册等
2. 数据共享、数据缓存。设置全局共享数据，如全局共享变量、方法等

注意：这些共享数据只在应用程序的生命周期内有效，当改用用程序被杀死，这些数据也会被清空，所以只能存储一些具备临时性的共享数据



通常我们是不需要指定一个Application的，系统会自动帮哦我们创建



### 手动创建Application

创建一个类继承Application并在AndroidManifest.xml文件中的application标签中进行注册【给application标签添加name属性，并添加自己的Application的名字



### Application的生命周期方法

onCreate()

onTerminate()：程序终止的时候执行

onLowMemory()：低内存的时候执行

onTrimMemory()：程序在内存清理的时候执行

onConfigurationChanged()：监听App配置之类的信息，如：屏幕旋转，语言变更等

