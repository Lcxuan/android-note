## Android学习路线

### 第一章 Android应用和开发环境

#### 1.1 Android简介

#### 1.2 Android环境搭建

​	1.2.1 JDK安装

​	1.2.2 安装Android Studio

​	1.2.3 安装Android SDK

#### 1.3 Android Studio用法

​	1.3.1 使用Android模拟器

​	1.3.2 ADB的用法

​	1.3.3 掌握Android的日志工具Log

#### 1.4 Android应用结构分析

​	1.4.1 res目录

​	1.4.2 清单文件：AndroidManifest.xml

​	1.4.3 build.gradle文件



### 第二章 Android应用的界面编程

#### 2.1 UI组件-布局

​	2.1.1 线性布局LinearLayout

​	2.1.2 相对布局RelativeLayout

​	2.1.3 帧布局FrameLayout

​	2.1.4 表格布局Tablelayout

​	2.1.5 约束布局ConstraintLayout

#### 2.2 UI组件-常用控件

​	2.2.1 文本框TextView

​	2.2.2 输入框EditText

​	2.2.3 按钮Button

​	2.2.4 图片ImageView

​	2.2.5 单选按钮RadioButton和复选框CheckBox

​	2.2.6 状态开关按钮ToggleButton和开关Switch

​	2.2.7 时钟AnalogClock和TextClock

​	2.2.8 计时器Chronometer

​	2.2.9 进度条ProgressBar

​	2.2.10 自动文本框AutoCompleteTextView

​	2.2.11 拖动条SeekBar

​	2.2.12 星级评分条RatingBar

​	2.2.13 日历视图CalendarView

​	2.2.14 日期DatePicker和时间选择器TimePicker

​	2.2.15 数值选择器NumberPicker

​	2.2.16 搜索框SearchView

​	2.2.17 滚动视图ScrollView

​	2.2.18 下拉框Spinner

​	2.2.19 堆叠列表StackView

#### 2.3 UI组件-ListView

​	2.3.1 使用ArrayAdapter实现ListView

​	2.3.2 使用SimpleAdapter实现ListView

​	2.3.3 使用BaseAdapter实现ListView

#### 2.4 对话框

​	2.4.1 使用AlertDialog创建对话框

​	2.4.2 简单列表项对话框

​	2.4.3 单选列表项对话框

​	2.4.4 多项列表项对话框

​	2.4.5 使用PopupWindow

​	2.4.6 使用DatePickerDialog、TimePickerDialog

​	2.4.7 使用ProgressDialog创建进度对话框

#### 2.5 比ListView更强大的RecyclerView

​	2.5.1 基本用法

​	2.5.2 实现横向滚动和瀑布流滚动

​	2.5.3 RecyclerView点击事件

#### 2.6 菜单

 2.6.1 选项菜单和子菜单

 2.6.2 使用监听器监听菜单

 2.6.3 创建多选菜单和单选菜单

 2.6.4 设置与菜单项关联的Activity

 2.6.5 上下文菜单

 2.6.6 使用xml文件定义菜单



### 第三章 Android应用的基本组件

#### 3.1 Activity

 3.1.1 Activity基本用法

 3.1.2 使用Intent在Activity之间跳转

 3.1.3 在Activity中使用Toast

 3.1.4 在Activity中使用菜单Menu

 3.1.5 销毁Activity

 3.1.6 Activity生命周期

 3.1.7 Activity启动模式

 3.1.8 Activity最佳实践

#### 3.2 Service

 3.2.1 Android多线程

 3.2.2 Service基本用法

 3.2.3 Service启动和停止

 3.2.4 Activity与Service进行通信

 3.2.5 Service生命周期

#### 3.3 ContentProvider

 3.3.1 ContentProvider简介

 3.3.2 运行时的权限

 3.3.3 ContentProvider基本用法

 3.3.4 创建ContentProvider的步骤

 3.3.5 实现跨程序数据共享

#### 3.4 BroadcastReceiver

 3.4.1 广播机制简介

 3.4.2 动态监听系统广播

 3.4.3 静态监听系统广播

 3.4.4 发送自定义标准广播和有序广播

#### 3.5 Intent

 3.5.1 使用显示Intent

 3.5.2 使用隐式Intent

 3.5.3 更多隐式Intent的用法

 3.5.4 向下一个Activity传递数据

 3.5.5 返回数据给上一个Activity

### 第四章 手机平板兼容

4.1 