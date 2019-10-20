## 包含emoji导致写数据失败

+ 时间：2019.10.18
+ 场景：
	+ 插入记录，报错
	```
	[2019-10-20 22:47:10] [HY000][1366] Incorrect string value: '\xF0\x9F\x8C\x88' for column 'content' at row 1
	```

+ 原因：
	+ 写入的内容中包含emoji (🌈)
	+ 🌈在UTF-8中需要用4个字节来表示(\xF0\x9F\x8C\x88)
	+ mysql的UTF-8只支持三个字节的存储，而一般字符是三个字节，但是emoji表情是4个字节，所以存储不了
	+ 
	
+ 解决：
	+ mysql为了解决这个问题，在5.5.3版本之后转而支持了存储4个字节的utf8字符，字符集为utf8mb4
	+ 查看字符集```show full columns from content;```
	+ 修改字符集```ALTER TABLE content MODIFY content VARCHAR(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci```


### Reference
1. [MySQL插入emoji表情错误的3种解决方案，Incorrect string value: '\xF0\x9F\x98\x84'](https://blog.csdn.net/dmw412724/article/details/81119325)