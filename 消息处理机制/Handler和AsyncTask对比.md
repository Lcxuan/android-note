## AsyncTask和Handler对比

### AsyncTask

Android提供的轻量级的一部类，可以直接继承AsyncTask，在类中实现异步操作，并提供接口反馈当前异步执行的程度（可以通过接口实现UI进度更新），最后反馈执行的结果给UI主线程

#### 优点

简单、快捷、过程可控

#### 缺点

在使用多个异步操作和需要进行UI变更时，就会变得复杂



### Handler

Handler异步实现时，涉及Handler、Looper、Message、Thread四个对象，实现异步的流程时主线程启动Thread（子线程），子线程运行并生成Message通过Handler发送出去，然后Looper去除消息队列中的Message再分发给Handler进行UI变更

#### 优点

结构清晰、功能定义明确。对于多个后台任务时，简单、清晰

#### 缺点

在单个后台异步处理时，显得代码过多，结果过于复杂（相对性）