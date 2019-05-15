## Date 反序列化错误

+ 时间：2019.05.13
+ 场景：通过feign调用远程的接口，返回的对象解析date的时候报错，错误信息如下：

```
    JSON parse error: Cannot deserialize value of type `java.util.Date` from String "2019-05-13 14:59:55": not a valid representation (error: Failed to parse Date value '2019-05-13 14:59:55': Cannot parse date "2019-05-13 14:59:55": while it seems to fit format 'yyyy-MM-dd'T'HH:mm:ss.SSSZ', parsing fails (leniency? null)); nested exception is com.fasterxml.jackson.databind.exc.InvalidFormatException: Cannot deserialize value of type `java.util.Date` from String "2019-05-13 14:59:55": not a valid representation (error: Failed to parse Date value '2019-05-13 14:59:55': Cannot parse date "2019-05-13 14:59:55": while it seems to fit format 'yyyy-MM-dd'T'HH:mm:ss.SSSZ', parsing fails (leniency? null))
     at [Source: (ByteArrayInputStream); line: 1, column: 279] (through reference chain: com.tal.seg.ts.common.base.pojo.Response["data"]->java.util.ArrayList[0]->com.tal.seg.content.library.common.ResourceCenterVO["createdAt"])
```

+ 原因：
1. Spring模式使用**Jackson**进行序列化，而**Jackson**对于日期只支持如下的格式
	+ "yyyy-MM-dd'T'HH:mm:ss.SSSZ"；
	+ "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"；
	+ "yyyy-MM-dd";
	+ "EEE, dd MMM yyyy HH:mm:ss zzz";
	+ long类型的时间戳;
2. 而后端服务提供放，指定了日期的序列化格式，所以Date会按照```yyyy-MM-dd HH:mm:ss```的格式序列化
    ```
    spring.jackson.date-format=yyyy-MM-dd HH:mm:ss
    spring.jackson.time-zone=Asia/Chongqing
    ```
3. 返回的对象ResourceCenterVO的Date类型field "createdAt" 并相应的没有指定序列化格式，所以按照默认的格式进行解析，结果报错

+ 解决：
	+ 由于是后端服务，对应的格式定义已经上线了，因此不能去掉格式定义的配置，以防其他系统出错；
	+ 由于调用方，也对外提供服务，因此，调用方加上全局的格式配置也有风险；
	+ 调用方自行定义一个返回类，复刻原返回类，但是对其中的Date对象，加入序列化格式定义

+ Reference
1. [Spring Mvc使用Jackson进行json转对象时，遇到的字符串转日期的异常处理（Can not deserialize value of type Date from String）](https://blog.csdn.net/qq906627950/article/details/79503801)
2. [关于jackson中时间字符串的转换](https://blog.csdn.net/masquejava/article/details/10556281)
3. [Jackson Annotation 格式化日期问题](https://shuidongliu.iteye.com/blog/2177332)



