## Feign调用null字段未忽略

+ 时间：2019.05.14
+ 场景：
	+ SpringBoot 2.X中，通过Feign调用服务
	+ 根据应用层调用feign之前的对象打印的参数，再通过postman调用后端服务，是okay的
    + 但是dev环境代码直接调用是报错的

+ 原因：
  + ```SpringBoot```默认的序列化工具是``` Jackson```
  + ```Feign```会使用S```pringBoot```中的的```HttpMessageConverter```来进行转换
  	+ ```org.springframework.cloud.openfeign.support.SpringEncoder#encode ```方法中会遍历全部的HttpMessageConverter找到可以合适的转换器(```HttpMessageConverter#canWrite```)
  	+ 其中合适的就是```MappingJackson2HttpMessageConverter```，底层就是通过Jackson来实现序列化
  + Jackson默认配置是null也会输出
  + 所以http请求的时候，body里面的值为null的属性也会输出
  + 服务提供方，对象反序列成了JSONObject，且通过containsKey来判断是否有值，且没有进行null判断，导致使用的时候报NullPointerException

+ 解决方案：
  + ```spring.jackson.default-property-inclusion=non_null``` 序列化的时候，不包含null的属性

+ Reference:
1. [springboot学习（三）——使用HttpMessageConverter进行http序列化和反序列化](https://www.cnblogs.com/page12/p/8166935.html)
2. [jackson springboot null节点忽略配置](https://www.cnblogs.com/liaojie970/p/8067350.html)