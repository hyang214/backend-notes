## spring+lombok导致序列化名称和po不一致

+ 时间：2019.05.16
+ 场景：
	+ 定义了一个对象，包含字段```qId```，返回给前端的时候json中的字段是```qid```，第二个字母变成了小写
+ 原因：
	+ 使用Lombok的```@Data```注解来生成```getter/setter```
	+ 对于驼峰命名，且第一个部分只有一个字母的情况下，如xYyy，生成的```getter/setter```，是按照```getXyyy/setXyyy```的格式的方式命名
	+ Spring将返回的response使用Jackson转换成json的时候，默认遍历public的```get```方法，然后根据方法的名称来生成json的对象，而不是通过反射获取对象的内部真实的变量名称

+ 解决方案：
  + 手动生成getter方法
  + 在getter方法上，加入指定序列化名称的注解```@JsonProperty(value = "qId")```

+ Reference
1. [Jackson序列化Java对象时注意](https://blog.csdn.net/yu75567218/article/details/44040121)
2. [jackson json 关于序列化与反序列化时json中字段名称的问题](https://www.oschina.net/question/1446005_162675)