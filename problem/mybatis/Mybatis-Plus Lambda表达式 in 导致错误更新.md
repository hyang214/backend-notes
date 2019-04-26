## Mybatis-Plus Lambda表达式 in 导致错误更新
+ 时间：2019.04.09
+ 场景：使用mybatis-plus的lambda进行过滤，且使用了**in**，但是对应的集合为空
```
	QueryWrapper<TsPaperTask> query = new QueryWrapper<>();
    query.lambda().in(SomePO::getId, ids);
```
+ 原因：当**in**比较的集合为空的时候，sql并不会报错，而是当做没有该条件，导致where条件失效，最终导致更新记录出错
+ 解决：执行之前，校验**ids**是否为空集合