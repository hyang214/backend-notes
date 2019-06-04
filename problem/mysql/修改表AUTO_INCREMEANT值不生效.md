## 修改表AUTO_INCREMEANT值不生效

+ 时间：2019.06.03
+ 场景：进行数据迁移的时候，使用```alter table {TABLE_NAME} auto_increment = {num};```语法修改自增值的当前序号，SQL执行为报错，但是实际未生效
+ 原因：
  + 表中存在自增字段的序号 大于```num```的情况
  + 由于表的自增下一个值的计算公式是```new_id = auto_increment_offset + N × auto_increment_increment```，且是的```new_id >= LAST_INSERT_ID()```
  + 所以修改并不会生效
+ 解决：
  + 增大```num```；
  + 或 删除全部大于```num```的记录；
