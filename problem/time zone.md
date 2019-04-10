# Time Zone 问题记录

## 数据库的记录与本地时间相差14个小时
+ 时间：2019.04.10
+ 场景：SpringBoot 应用向数据库中写时间数据，值是在应用层提供```new Date()```赋值的，在应用层中数据为GMT+8的展示，但是落库后，数据的值相比与应用层的值，晚了14个小时
+ 原因：数据库时区设置有问题
  + SpringBoot 应用层，new Date()带有时区，是正确的值
  + java.util.Date在Mybatis的写值的时候，会被转换为java.sql.Timestamp，详见```org.apache.ibatis.type.DateTypeHandler#setNonNullParameter```，Timestamp没有时区的信息，相当于被转换成了GMT+0的时间信息，相比而言，慢了8个小时
  + 传输给数据库的值，是一个GMT+0的时间戳，数据库写入的时候，按照数据库对应的时区设置，进行转换然后写入
  + 时间戳变成了慢14个小时的值
+ 解决：数据库链接带上 时区限制，**serverTimezone=GMT%2B8**，将时区指定为东8区
```
jdbc:mysql://dbUrl:3306/table_name?useTimezone=true&serverTimezone=GMT%2B8
```

+ Reference: [SpringBoot时间戳与MySql数据库记录相差14小时排错](http://www.cnblogs.com/jason1990/archive/2018/11/28/10032181.html)