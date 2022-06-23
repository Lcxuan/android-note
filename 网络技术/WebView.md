### 加载网页

- 加载URL（网络或者本地assets文件夹下的html文件）
- 加载HTML代码
- Native和Javascript相互调用



### 加载网络URL

```java
webview.loadUrl("www.baidu.com");
```



### 加载assets下的html文件

```java
webview.loadUrl("file:///android_asset/test.html");
```



### 加载html代码

- webview.loadData();
- webview.loadDataWithBaseURL();



### 网页的前进后退

1. 判断是否能后退：webview.canGoBack();
2. 后退：webview.goBack();
3. 判断是否能前进：webview.canGoForward();
4. 前进：webview.goForward();
5. 判断是否能后退或者前进：webview.canGoBackOrForward(int steps);
6. 后退或者前进：webview.goBackOrForward(int steps);



按下返回键，默认是退出当前Activity，如果希望webview是页面后退，可以使用键盘点击

```java
```

