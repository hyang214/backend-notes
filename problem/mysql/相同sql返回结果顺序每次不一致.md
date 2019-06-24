## 相同Sql每次执行结果不一致

+ 时间：2019.06.24
+ 场景：
	+ 相同的sql，其中包含有```order by {orderBy}```和```limit {startIndex}, {pageSize}```
	+ 全部查询结果包含超过```pageSize```数目的具有相同```orderBy```值的记录
	+ 每次查询出来的结果不一致，且出现分页数据重复

+ 原因：
	+ mysql 5.7之后，Limit row_count与order by混用，mysql会找到排序的row_count行后立马返回，而不是排序整个查询结果再返回；
	+ 如果order by的字段有多个行都有相同的值，mysql是会随机的顺序返回查询结果的，具体依赖对应的执行计划。也就是说如果排序的列是无序的，那么排序的结果行的顺序也是不确定的。
	+ mysql 5.6之后，优化器在遇到order by limit语句的时候，会使用了priority queue来存储结果，只保留n条记录，而priority queue使用了堆排序的排序方法，而堆排序是一个不稳定的排序方法，也就是相同的值可能排序出来的结果和读出来的数据顺序不一致

+ 解决：
	+ 额外在增加一个排序字段，作为兜底

### Reference
1. [MySQL order by limit 分页数据重复问题](https://juejin.im/post/5af9537bf265da0b9e652dea)
2. [mysql orderby limit 翻页数据重复的问题](https://blog.csdn.net/zilaike/article/details/79807711)
3. [Mysql order by与limit混用陷阱](https://www.jianshu.com/p/ead491db9749)
4. [同样的sql，mysql 每次查询结果顺序不一致](https://www.cnblogs.com/hulkCoder/p/5978961.html)