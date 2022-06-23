## 解析XML格式数据

当前有一个名为get_data.xml的文件，内容为：

```xml
<apps>
    <app>
        <id>1</id>
        <name>Google Maps</name>
        <version>1.0</version>
    </app>
    <app>
        <id>2</id>
        <name>Chrome</name>
        <version>2.0</version>
    </app>
    <app>
        <id>3</id>
        <name>Google Play</name>
        <version>3.0</version>
    </app>
</apps>
```

### Pull解析方式

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private Button request;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        request = findViewById(R.id.request);
        request.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    URL url = new URL("http://192.168.1.116/get_data.xml");
                    HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                    connection.setRequestMethod("GET");

                    int responseCode = connection.getResponseCode();
                    Log.e("TAG", "请求码: " + responseCode);
                    if (responseCode == HttpURLConnection.HTTP_OK){
                        InputStream inputStream = connection.getInputStream();
                        BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));

                        StringBuilder result = new StringBuilder();
                        String len = null;
                        while ((len = reader.readLine()) != null){
                            result.append(len);
                        }

                        analysisXMLByPull(result);
                    }

                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    private void analysisXMLByPull(StringBuilder result){
        XmlPullParser pullParser = null;
        try {
            //1. 获取Pull解析器
            XmlPullParserFactory factory = XmlPullParserFactory.newInstance();
            pullParser = factory.newPullParser();

            //2. 设置XMl数据源
            pullParser.setInput(new StringReader(result.toString()));

            //3. 获取事件类型
            int eventType = pullParser.getEventType();

            String id = "";
            String name = "";
            String version = "";

            //4. 判断是否到结束节点
            while (eventType != XmlPullParser.END_DOCUMENT){
                String nodeName = pullParser.getName();

                switch (eventType){
                    case XmlPullParser.START_TAG:   //开始解析某个节点
                        try {
                            if ("id".equals(nodeName)){
                                id = pullParser.nextText();
                            }else if ("name".equals(nodeName)){
                                name = pullParser.nextText();
                            }else if ("version".equals(nodeName)){
                                version = pullParser.nextText();
                            }
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                        break;
                    case XmlPullParser.END_TAG:     //完成解析某个节点
                        if ("app".equals(nodeName)){
                            Log.e("TAG", "id：" + id);
                            Log.e("TAG", "name：" + name);
                            Log.e("TAG", "version：" + version);
                        }
                        break;
                }
                try {
                    eventType = pullParser.next();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        } catch (XmlPullParserException e) {
            e.printStackTrace();
        }
    }
}
```



### SAX解析方式

新建一个类继承自DefalutHandler，并重写父类的5个方法，代码如下：

```java
public class MyHandler extends DefaultHandler {
    //在开始XML解析时调用
    @Override
    public void startDocument() throws SAXException {
        
    }

    //在开始解析某个节点时调用
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        
    }

    //在获取节点中内容时调用
    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        
    }

    //在完成解析某个节点的时候调用
    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        
    }

    //在完成整个XML解析的时候调用
    @Override
    public void endDocument() throws SAXException {
        
    }
}
```



获取SAXParserFactory的对象，代码如下：

```java
//获取SAXParserFactory的对象
SAXParserFactory factory = SAXParserFactory.newInstance();
```

获取XMLReader对象，代码如下：

```java
//获取XMLReader对象
XMLReader xmlReader = factory.newSAXParser().getXMLReader();
```

设置继承DefaultHandler的实现类，代码如下：

```java
//设置继承DefaultHandler的实现类
MyHandler myHandler = new MyHandler();
xmlReader.setContentHandler(myHandler);
```

开始执行解析，代码如下：

```java
//开始执行解析
xmlReader.parse(new InputSource(new StringReader(xmlData)));
```

