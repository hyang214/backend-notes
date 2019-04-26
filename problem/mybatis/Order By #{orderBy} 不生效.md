## Order By #{orderBy} 不生效
+ 时间：2019.04.10
+ 场景：服务层向mapper中传入的排序字段，但是未生效
```
    <select id="pageQuery"
            parameterType="..." resultMap="layoutResult">
        select * from tmp_table
        <if test="orderBy != null and orderBy !='' ">
            order by #{orderBy}
        </if>
        LIMIT #{startIndex}, #{pageSize}
    </select>
```
+ 原因：mybatis中 
	+ **#{}**中是将传入的值当做字符串的形式，解析为一个 JDBC 预编译语句（prepared statement）的参数标记符，一个 #{ } 被解析为一个参数占位符 
	+ **${}**中是将传入的值当做SQL的形式，仅仅为一个纯碎的 string 替换，在动态 SQL 解析阶段将会进行变量替换
+ 解决：使用 $
+ Reference: [Mybatis 中$与#的区别](https://www.cnblogs.com/hellokitty1/p/6007801.html)








