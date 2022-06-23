## GreenDao数据库框架

一款 开源的面向Android的轻便、快捷的ORM（对象关系映射：object Relational Mapping）框架，将Java对象映射到SQLite数据库中

我们操作数据库的时候，不再需要编写复杂的SQL语句

在性能方面，GreenDao针对Android进行了高度优化，最小的内存开销、依赖体积小同时支持数据库加密



### 注解

@Entity：表明这个实体类会在数据库中生成一个与之相对应的表

@Id：对应数据表中的id字段

@NotNull：设置数据表当前列不能为空

@Unique：表明该属性在数据库中只能由唯一值



### 实现步骤

#### 1、新建实体类



#### 2、Make Project编译工程



#### 3、初始化GreenDAO



#### 4、获取DaoSession



#### 5、通过DaoSession操作数据库



