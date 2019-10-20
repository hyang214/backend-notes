## 多参数方法mock的部分参数使用了具体值

+ 时间：2019.07.16
+ 场景：
	+ 使用mockito进行测试
	+ 有如下代码，对服务的一个多参数方法进行mock
		```
		when(redisService.hgetall(any(), AuthKeyPair.class)).thenReturn(Maps.newHashMap());
		```
	+ 其中一个参数是匹配，一个是实际值，报如下异常：
		```
		Invalid use of argument matchers!
2 matchers expected, 1 recorded:
-> at com.bigggernews.anebe.service.impl.AuthServiceImplTest.getPublicKey01(AuthServiceImplTest.java:38)
This exception may occur if matchers are combined with raw values:
    //incorrect:
    someMethod(anyObject(), "raw String");
When using matchers, all arguments have to be provided by matchers.
For example:
    //correct:
    someMethod(anyObject(), eq("String by matcher"));
For more info see javadoc for Matchers class.
		```
	
+ 原因：
	
+ 错误信息已经很名单了，mockito不支持多个参数的时候，匹配和实际值混用
	
+ 解决方案：
	
	+ 将实际值，转换为eq(实际值)

