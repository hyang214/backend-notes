## Resource Bundle中文信息乱码

+ 时间：2019.07.05
+ 场景：
	+ 使用ResourceBundle来存储i18n的文本
	+ 对应内容为中文的情况，返回的是乱码

+ 原因：
	+ ResourceBundle是按照```ISO8859```来读取原属性文件的

+ 解决方案：
	+ 方案一，将内容手动进行一次编码转换
	```
	String originKeyValue = ...;
	String keyValue = new String(originKeyValue.getBytes("ISO-8859-1"), "UTF-8");
	```
	+ 方案二，在Resource Bundle中使用Unicode来写内容，而不是文字本身

### Reference
1. [ResourceBundle中的中文乱码问题](https://blog.csdn.net/Brookes/article/details/1508539?utm_source=blogxgwz6)






