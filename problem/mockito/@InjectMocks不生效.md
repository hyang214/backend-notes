## @InjectMocks不生效

+ 时间：2019.07.16
+ 场景：
	+ 使用mockito进行测试
	+ 运行使用```@RunWith(MockitoJUnitRunner.class)```
	+ 通过```@Mock```生成mock的依赖
	+ 通过```@InjectMocks```将依赖注入
	+ 但是运行中，并没有注入成功

+ 原因：
	+ 需要注入的service对应的field没有设置```Setter```方法
	+ ~~mockito只支持通过```Setter```方法注入，而不会通过反射~~
+ 更新
	+ 根据官方文档，mockito是支持field injection的([Annotation Type InjectMocks](https://static.javadoc.io/org.mockito/mockito-core/3.0.0/org/mockito/InjectMocks.html))
	+ 修改代码之后，确实能正常注入，不能复现当初出现的问题

+ 解决方案：
	+ ~~为service需要注入的field增加一个setter方法~~








