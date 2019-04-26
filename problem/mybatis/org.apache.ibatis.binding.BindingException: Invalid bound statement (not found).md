## org.apache.ibatis.binding.BindingException: Invalid bound statement (not found)
+ 时间：2019.04.17
+ 场景：新增了一张表结构，代码在执行对应的mapper中的sql的时候报错
+ 原因：未扫码到对应的Mapper
	+ 检查Mapper的namespace是否正确，正确的
	+ 检查sql的result和parameter配置是否正确，正确的
	+ 怀疑是新增的SQL有问题，新增了一个进行测试，报相同问题，确定是整个Mapper没有加载
	+ Mapper配置的扫描路径，路径正确，但是限定了Mapper的文件名格式，找到问题
	```
	mybatis-plus:
  		mapper-locations: classpath:/mapper/*/*Mapper.xml
	```
	+ 由于idea的自动跳转功能中，对应的sql一致能正确匹配，所以没有怀疑是路径有问题

+ 解决：修改Mapper的名称，以\*Mapper.xml的格式为名称
+ Reference:

  1. [Invalid bound statement (not found)错误的可能原因](https://www.cnblogs.com/liaojie970/p/8034525.html)

