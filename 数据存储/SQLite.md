## SQLite

一款轻型的数据库，遵守ACID的关系型数据库管理系统，设计目标是嵌入式的



### 特点

1. 轻量级
2. 独立性
3. 隔离性
4. 跨平台
5. 多语言接口
6. 安全性



### SQLite数据库的创建

提供了SQLiteOpenHelper抽象类，通过继承该类完成数据库的创建



### SQLite数据库的操作

通过SQL语句完成数据库的增删改查



### 常用SQL语句

insert into 表名 values(字段);

delete form 表名 where 条件

update 表名 set 字段 = ? where 条件

select * from 表名 where 条件



### 实现步骤

1、新建Sqlite类，并且继承SQLiteOpenHelper

2、实例化Sqlite类，并且设置读写权限

3、数据库操作



#### 1、新建Sqlite类，并且继承SQLiteOpenHelper

```java
public class MySqlite extends SQLiteOpenHelper {
    public MySqlite(@Nullable Context context) {
        //context   上下文
        //name      数据库名称
        //factory   一般为空
        //version   版本
        super(context, "mySqlite.db", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        String sql = "create table user( id Integer, name varchar(50), age Integer)";
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```



#### 2、实例化Sqlite类，并且设置读写权限

```java
MySqlite mySqlite = new MySqlite(getApplicationContext());
database = mySqlite.getWritableDatabase();
```



#### 3、数据库操作

查询操作

```java
//1. 定义查询sql
String selectSql = "select * from user where id = ?";
//2. 使用rawQuery进行查询
Cursor cursor = database.rawQuery(selectSql, new String[]{findNumber.getText().toString()});
//3. moveToNext判断游标是否还能移动到下一个
while (cursor.moveToNext()){
    Integer userId = cursor.getInt(cursor.getColumnIndex("id"));
    String name = cursor.getString(cursor.getColumnIndex("name"));
    Integer age = cursor.getInt(cursor.getColumnIndex("age"));
    show.setText("编号：" + userId + "\t 名称：" + name + "\t年龄" + age);
}
```



添加操作

```java
//1. 定义插入sql
String insertSql = "insert into user(id, name, age) values(?, ?, ?)";
//2. 使用execSQL执行执行sql
database.execSQL(insertSql, new Object[]{
    Integer.parseInt(number.getText().toString()),
    name.getText().toString(),
    Integer.parseInt(age.getText().toString())
});
```



删除操作

```java
//1. 定义删除sql
String deleteSql = "delete from user where id = ?";
//2. 使用execSQL执行执行sql
database.execSQL(deleteSql, new Object[]{
    number.getText().toString()
});
```



修改操作

```java
//1. 定义修改sql
String updateSql = "update user set name = ? where id = ?";
//2. 使用execSQL执行执行sql
database.execSQL(updateSql, new Object[]{
    name.getText().toString(),
    number.getText().toString()
});
```

