## 不能使用CREATE TABLE AS SELECT

+ 时间：2019.04.25
+ 场景：进行数据迁移的时候，使用```CREATE TABLE AS SELECT```语法，提示错误```Error : Statement violates GTID consistency: CREATE TABLE ... SELECT.```
+ 原因：由于```CREATE TABLE AS SELECT```语句会生成两个sql，一个是DDL创建表SQL，一个是insert into 插入数据的sql。由于DDL会导致自动提交，所以这个sql至少需要两个GTID，但是GTID模式下，只能给这个sql生成一个GTID，如果强制执行会导致和上面更新非事务引擎一样的结果。
+ 解决：将```CREATE TABLE AS SELECT```拆分成2个sql
	+ ```CREATE TABLE xxx LIKE yyy```
	+ ```INSERT INTO TABLE xxx SELECT * FROM yyy```
	+ 可以通过```show global variables like '%gtid%';```来查询是否开启了gtid
		```
            binlog_gtid_simple_recovery	ON
            enforce_gtid_consistency	ON
            gtid_executed	b3d4b0d0-d672-11e8-9878-506b4b3e4b36:1-5043275
            gtid_executed_compression_period	1000
            gtid_mode	ON -- 开启了
            gtid_owned	
            gtid_purged	b3d4b0d0-d672-11e8-9878-506b4b3e4b36:1-4995131
            opt_rds_last_error_gtid	ON
            session_track_gtids	OFF
		```
+ 注意：

  + mysql任何事物在执行到一个DDL的情况下，会自动产生一次commit，这会导致一个源事务被分割成2个，进一步导致无法全部回滚


## Reference
1. [mysql的GTID模式对create table as select的限制](https://blog.csdn.net/qq_40809549/article/details/79975081)
2. [mysql GTID 主从复制模式](https://www.cnblogs.com/luckcs/articles/6295992.html)
3. [MySQL--当事务遇到DDL命令](https://www.cnblogs.com/TeyGao/p/7622155.html)