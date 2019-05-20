## spring get方法被无故调用

+ 时间：2019.05.13
+ 场景：
	+ 服务方提供的一个接口的请求对象中的```getInfo()```方法
+ 原因：
	+ 通过feign调用后端服务
	+ 请求的对象包含如下代码：
		```
		public ResourceInfo getInfo() {
            if (resId == null) {
                throw new BusiException("resId不能为空");
            }
		}
		```
	+ Spring将返回的response使用Jackson转换成json的时候，默认遍历public的```get```方法，然后根据方法的名称来生成json的对象，而不是通过反射获取对象的内部真实的变量名称

+ 解决方案：
  + getInfo名称改成非get命名格式的方法

+ Reference
1. [Jackson序列化Java对象时注意](https://blog.csdn.net/yu75567218/article/details/44040121)
2. [jackson json 关于序列化与反序列化时json中字段名称的问题](https://www.oschina.net/question/1446005_162675)