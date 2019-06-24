## Fastjson内部非静态类无法反序列化

+ 时间：2019.06.24
+ 场景：
  + 反序列化的时候，指定的Class属于一个内部非静态类
  + 反序列化报错：
    ```
    com.alibaba.fastjson.JSONException: create instance error, class 
    ...
    com.talcloud.resource.test.es.EsTest$Account
    Caused by: java.lang.NullPointerException
    at com.alibaba.fastjson.parser.deserializer.JavaBeanDeserializer.createInstance(JavaBeanDeserializer.java:201)
  ... 38 more
    ```

+ 原因：
  + 内部非静态类无法直接实例化，导致反序列化，无法创建对应对象

+ 解决方案：
  1. 将内部类改后普通的java类
  2. 或者，将类定义成静态的

+ Reference:
1. [内部类反序列化问题(fastjson exception: create instance error)](https://blog.csdn.net/xktxoo/article/details/78175997)
2. [fastjson转对象失败](https://github.com/alibaba/fastjson/issues/302)