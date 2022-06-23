## SharePreferences

Android提供的数据持久化的一种手段，适合单进程、小批量的数据存储与访问



### 实现步骤

1、创建一个SharePreferences对象

2、实例化SharePreferences对象

3、数据操作



### 1、创建一个SharePreferences对象

```java
//步骤一：创建一个SharePreferences对象
SharedPreferences sharedPreferences = getSharedPreferences("data", Context.MODE_PRIVATE);
```



### 2、实例化SharePreferences.Editor对象

```java
//步骤二：实例化SharePreferences.Editor对象
SharedPreferences.Editor edit = sharedPreferences.edit();
```



### 3、数据操作

```java
//1. 存储数据
edit.putString("data", "123");
edit.commit();

//2. 读取数据
sharedPreferences.getString("data", "");

//3. 删除数据
edit.remove("data");
edit.commit();
```
