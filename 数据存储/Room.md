## Room

Room是一个持久性数据库，Room持久层库在SQLite上提供了一个抽象层，以便充分利用SQLite的强大功能同时，能够流畅地访问数据库，具有以下优势：

- 针对SQL查询的编译时验证
- 可最大限度减少重复和容易出错的样板代码的方便注解
- 简化了数据库迁移路径



### 添加依赖

```gradle
dependencies {
    def room_version = "2.4.2"

    implementation "androidx.room:room-runtime:$room_version"
    annotationProcessor "androidx.room:room-compiler:$room_version"

    // optional - RxJava2 support for Room
    implementation "androidx.room:room-rxjava2:$room_version"

    // optional - RxJava3 support for Room
    implementation "androidx.room:room-rxjava3:$room_version"

    // optional - Guava support for Room, including Optional and ListenableFuture
    implementation "androidx.room:room-guava:$room_version"

    // optional - Test helpers
    testImplementation "androidx.room:room-testing:$room_version"

    // optional - Paging 3 Integration
    implementation "androidx.room:room-paging:2.5.0-alpha01"
}
```



### 三个主要的组建

- 数据库：用于保存数据库，并作为应用持久性数据底层连接的主要访问
- 数据实体：用于表示数据库中的表
- 数据访问对象（DAO）：用于增删改查的数据方法



### 实现案例

#### 数据实体

```java
//指定表名，默认情况下表名为类的名字，可使用tableName指定表名
@Entity
public class User {
    //指定id为主键，并自增
    @PrimaryKey(autoGenerate = true)
    public int id;

    //表示这个字段为一列，默认情况下类名为列名，也可以使用name指定列名
    @ColumnInfo(name = "first_name")
    public String firstName;

    @ColumnInfo(name = "last_name")
    public String lastName;
}
```

#### 数据访问对象（DAO）

```java
//指定数据访问对象DAO
@Dao
public interface UserDao {

    //增加
    @Insert
    void insert(User user);

    //删除
    @Delete
    void delete(User user);

    //更新
    @Update
    void update(User user);
    
    //查找所有
    @Query("select * from user")
    List<User> getAll();

    //查找指定的数据
    @Query("select * from user where first_name = :firstName and last_name = :lastName")
    List<User> getUser(String firstName, String lastName);
}
```

#### 数据库类

```java
//指定数据实体类和版本号
@Database(entities = {User.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    //提供返回一个数据访问对象的方法
    public abstract UserDao userDao();
}
```

#### 使用

```java
AppDatabase database = Room.databaseBuilder(getApplicationContext(), AppDatabase.class, "test.db").build();
UserDao userDao = database.userDao();
List<User> dataAll = userDao.getAll();
for (User user : dataAll) {
    Log.e("TAG", "id: " + user.id);
    Log.e("TAG", "firstName: " + user.firstName);
    Log.e("TAG", "lastName: " + user.lastName);
}
```



### 使用实体定义表

当使用Room存储应用数据时，可以定义实体来表示要存储的对象，每个实体都对应关联的Room数据库中的一个表，并且实体的每个实例都表示表中的一行数据

```java
@Entity
public class User {
    @PrimaryKey(autoGenerate = true)
    public int id;
    
    @ColumnInfo(name = "first_name")
    public String firstName;

    @ColumnInfo(name = "last_name")
    public String lastName;
}
```

将每个Room的实体定义为带有`@Entity`注解的类，默认情况下，Room将类名称用作数据库表名称，如果希望表具有不同的名称，需要设置`@Entity`注解的`tableName`属性，代码如下：

```java
@Entity(tableName = "users")
```

同样的，Room默认使用字段的名称作为数据库中的列名称，如果希望列具有不同的名称，需要设置`@ColumnInfo`注解的`name`属性

**注意：SQLite的表名和列名不区分大小写**



#### 定义主键

每个Room实体都必须定义一个主键，用于唯一标识相应数据表中的每一行，使用`@PrimaryKey`为单个列添加注解：

```java
@PrimaryKey
public int id;
```

- autoGenerate属性：当设置为true，让sqlite生成唯一的ID



#### 定义复合主键

如果需要通过多个列的组合对实体进行唯一标识，通过`@Entity`的`PrimaryKeys`属性中定义复合主键，代码如下：

```java
@Entity(primaryKeys = {"firstName", "lastName"})
public class User{
    public String firstName;
    public String lastName;
}
```



#### 忽略字段

默认情况下，Room会为实体中定义的每个字段创建一个列，如果实体中有不想要的字段，则可以使用`@Ignore`为这些字段添加注解，代码如下：

```java
@Entity
public class User {
    @PrimaryKey
    public int id;
    
    public String firstName;
    public String lastName;
    
    @Ignore
    Bitmap picture;
}
```

如果实体继承了父实体的字段，则使用`@Entity`属性的`ignoredColumns`属性：

```java
@Entity(ignoredColumns = "picture")
public class RemoteUser extends User {
    @PrimaryKey
    public int id;
    
	public boolean hasVpn;
}
```



### 使用DAO访问数据

#### 常用方法

当使用Room存储应用的数据时，可以通过定义数据访问对象与存储的数据进行交互。每个DAO包含一些方法，这些访问提供对应用数据库的抽象访问权限

可以讲每个DAO定义为一个接口或者一个抽象类，无论哪种情况，都必须使用`@DAO`为DAO添加注解

##### 插入

借助`@Insert`注解，可以定义将其参数插入到数据库中的相应表中，代码如下：

```java
@Dao
public interface UserDao{
    //onConflict：发生冲突执行的操作
    //1. OnConflictStrategy.ABORT：默认，在发送冲突时回滚事务
    //2. OnConflictStrategy.REPLACE：将现有行替换为新行
    //3. OnConflictStrategy.IGNORE：保留现有行
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    public void insertUsers(User... users);

    @Insert
    public void insertBothUsers(User user1, User user2);

    @Insert
    public void insertUsersAndFriends(User user, List<User> friends);
}
```

##### 更新

借助`@Update`注解，可以定义更新数据库表中特定行的方法，与`@Insert`类似，代码如下：

```java
@Dao
public interface UserDao{
    @Update
    public void updateUsers(User... users);
}
```

`@Update`方法可以选择性的返回`int`值，该值表示成功更新的行数

##### 删除

借助`@Delete`注解，可以定义删除数据库表中特定行的方法，与`@Insert`类似，代码如下：

```java
@Dao
public interface UserDao{
    @Delete
    public void deleteUsers(User... users);
}
```

`@Delete`方法可以选择性的返回`int`值，该值表示成功更新的行数



#### 查询方法

使用`@Query`注解，可以编写SQL语句并将其作为DAO方法公开，使用这些查询方法从应用的数据库查询数据

##### 简单查询

```java
@Query("select * from user");
public User[] loadAllUsers();
```



##### 返回表格列的子集

大多数情况下，我们只需要返回查询的表中的列的子集

借助Room，可以从任何查询返回简单对象，前提是可以讲一组结果列映射到返回得对象，例如定义以下对象保存first_name和last_name：

```java
public class NameTuple {
    @ColumnInfo(name = "first_name")
    public String firstName;

    @ColumnInfo(name = "last_name")
    @NonNull
    public String lastName;
}
```

接着，查询方法可以返回该对象，代码如下：

```java
@Query("SELECT first_name, last_name FROM user")
public List<NameTuple> loadFullName();
```

Room 知道该查询会返回 `first_name` 和 `last_name` 列的值，并且这些值会映射到 `NameTuple` 类的字段中。如果查询返回的列未映射到返回的对象中的字段，则 Room 会显示一条警告。



##### 将参数传递给查询

大多数情况下，DAO方法需要接受参数，以便执行过滤操作，Room支持在查询中讲方法参数作为绑定参数，代码如下：

```java
@Query("SELECT * FROM user WHERE age > :minAge")
public User[] loadAllUsersOlderThan(int minAge);
```



##### 将一组参数传递给查询

某些 DAO 方法可能要求您传入数量不定的参数，参数的数量要到运行时才知道。Room 知道参数何时表示集合，并根据提供的参数数量在运行时自动将其展开。

例如，定义了一个方法，该方法返回部分地区的所有用户的相关信息

```java
@Query("SELECT * FROM user WHERE region IN (:regions)")
public List<User> loadUsersFromRegions(List<String> regions);
```



##### 查询多个表

您的部分查询可能需要访问多个表格才能计算出结果。您可以在 SQL 查询中使用 `JOIN` 子句来引用多个表。

以下代码定义了一种方法将三个表联接在一起，以便将当前已出借的图书返回给特定用户：

```java
@Query("SELECT * FROM book " +
       "INNER JOIN loan ON loan.book_id = book.id " +
       "INNER JOIN user ON user.id = loan.user_id " +
       "WHERE user.name LIKE :userName")
public List<Book> findBooksBorrowedByNameSync(String userName);
```

##### 返回多重映射

例如：返回User表和Book表的数据

```java
@Query(
    "SELECT * FROM user" +
    "JOIN book ON user.id = book.user_id"
)
public Map<User, List<Book>> loadUserAndBookNames();
```

