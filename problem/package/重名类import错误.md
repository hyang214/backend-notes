# 重名类import错误

+ 时间：2018.05.13
+ 场景：
1. 同一个类中通过直接引用 和 全量引用2种方式引入了2个名称相同的类
	+ ```import xxx.ResourceCenterVO```
	+ ```import yyy.response.*```
		+ 包含 ResourceCenterVO
1. 定义了使用该类的方法
	+ ```Response<List<ResourceCenterVO>> getResourceInfoByIdList(BatchResourceRequest request)```
1. 本地编译不报错
1. ci编译报错
```
 incompatible types: xxx.Response> cannot be converted to xxx.Response>
[ERROR]
```
	+ ResourceCenterVO是作为泛型注入到 Response中的

+ 原因：
1. 引入了2个相同名称的类，ResourceCenterVO
2. 在使用的时候，按照类名，而不是全路径来使用
3. 编译的时候，只能选择其中一个，而本地选择了正常的，ci选择了错误的

+ 解决：将```.*```改成具体的类的引用，这样就只引用了一个ResourceCenterVO，就不会报错了