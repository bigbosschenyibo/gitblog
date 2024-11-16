# [SQL fast-bulk-inserts-in-sqlite3](https://github.com/bigbosschenyibo/gitblog/issues/11)

我有一个sqlite表 大概有20列 模拟查询100W条数据大约需要耗时24分钟
如何使插入速度变快?我们需要注意以下几点：
1. 将插入操作放到一个事务中执行
2. 屏蔽掉所有索引 然后在执行完事务后 重启索引
3. 通过数据库命令PRAGMA 进行处理 比如关闭回滚机制 关闭文件同步 使用内存操作等提升插入性能
4. 数据量过大 考虑分片并在多线程中执行
### 快速了解[PRAGMA](https://system.data.sqlite.org/index.html/doc/dc206da59f/Doc/Extra/pragma.html)?
``` sql
-- search all columns of table
PRAGMA table_info(eventrecordinfo);
-- search all indexs of table
PRAGMA index_list(eventrecordinfo);
-- search the index info
PRAGMA index_info(eventrecord_time_query_index);
```

### 参考
[Towards Inserting One Billion Rows in SQLite Under A Minute](https://avi.im/blag/2021/fast-sqlite-inserts/)
[Fast way to insert rows in SQLite](https://sqlite.org/forum/info/f832398c19d30a4a)