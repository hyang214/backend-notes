## 补主键，对应字段有null值

+ 时间：2019.05.28
+ 场景：
  + 某张生产环境的表的id字段出现了NULL值

+ 原因：
  + 通过```show create table TABLE_NAME```查看之后，发现id字段未被定为主键，且未定义为自增

+ 解决：
  + 将id字段改成自增```alter table TABLE_NAME modify column `id` int(11) NOT NULL AUTO_INCREMENT```
        + 报错：```Incorrect table definition; there can be only one auto column and it must be defined as a key```
        + 要求自增的必须是一个key
  + 将id改成自增的同时，将其改成主键```alter table TABLE_NAME modify column `id` int(11) NOT NULL AUTO_INCREMENT primary key```
    + 报错：```ALTER TABLE causes auto_increment resequencing, resulting in duplicate entry '883934' for key 'PRIMARY'```
      + 由于已经有不少值为null，导致不能执行
  + 通过```show create table TABLE_NAME```查看原表DDL，然后将id改成主键且自增，然后通过这个sql建立表 NEW_TABLE_NAME
  + 通过```insert into NEW_TABLE_NAME select * from TABLE_NAME;```将数据迁移到新表
  + 通过```RENAME TABLE NEW_TABLE_NAME TO TABLE_NAME, TABLE_NAME TO BAK_TABLE_NAME;```完成数据迁移

+ 注意：
  + 这种方式处理过程中，如果有新的数据写入，会导致数据不一致
  + 应该在rename之后，再对比下数据，保证数据一致

## Reference
1. [ERROR 1062 (23000): ALTER TABLE causes auto_increment resequencing, resulting in duplicate entry '1'](https://blog.csdn.net/evils798/article/details/77478099)
2. 