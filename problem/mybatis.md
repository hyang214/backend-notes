# Mybatis问题记录

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


## Mybatis-Plus Lambda表达式 in 导致错误更新
+ 时间：2019.04.09
+ 场景：使用mybatis-plus的lambda进行过滤，且使用了**in**，但是对应的集合为空
```
	QueryWrapper<TsPaperTask> query = new QueryWrapper<>();
    query.lambda().in(SomePO::getId, ids);
```
+ 原因：当**in**比较的集合为空的时候，sql并不会报错，而是当做没有该条件，导致where条件失效，最终导致更新记录出错
+ 解决：执行之前，校验**ids**是否为空集合












