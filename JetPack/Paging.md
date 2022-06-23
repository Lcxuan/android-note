## Paging

Paging可以帮助我们一次加载和显示多个小的数据块。按需载入部分数据会减少网络带宽和系统资源的使用量



### 设置

需要将以下代码添加到`build.gradle`文件中：

```
dependencies {
  def paging_version = "2.1.2"

  implementation "androidx.paging:paging-runtime:$paging_version"
  testImplementation "androidx.paging:paging-common:$paging_version"
  implementation "androidx.paging:paging-rxjava2:$paging_version"
}
```



