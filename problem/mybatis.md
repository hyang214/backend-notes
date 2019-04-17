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









