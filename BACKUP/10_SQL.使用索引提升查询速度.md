# [SQL 使用索引提升查询速度](https://github.com/bigbosschenyibo/gitblog/issues/10)

### SELECT COUNT(*)为什么特别慢：
**提问：**
项目中使用sqlite数据库，我有一个表数据已经在百万条，在执行一个带条件的select count(*) from table_name where conditions 后发现结果返回需要两秒左右，我该如何优化。
**回答：**
针对您提到的SQLite数据库中执行带条件的`SELECT COUNT(*)`查询性能问题，以下是一些优化建议：

1. **创建索引**：对于经常作为查询条件的列，创建索引可以显著提高查询效率。根据搜索结果，索引是提高查询性能的关键，它们允许数据库快速访问表中的行。您可以使用`CREATE INDEX`语句为表中经常用于条件查询的列创建索引，例如：
   ```sql
   CREATE INDEX idx_conditions ON table_name(column_name);
   ```
   其中`column_name`是您经常在`WHERE`子句中使用的列名。

2. **使用`EXPLAIN QUERY PLAN`分析查询计划**：通过分析查询计划，您可以了解SQLite如何执行查询，并识别潜在的瓶颈。使用`EXPLAIN QUERY PLAN`命令可以帮助您查看查询执行计划，从而优化查询：
   ```sql
   EXPLAIN QUERY PLAN SELECT * FROM table_name WHERE conditions;
   ```

3. **优化查询**：避免不必要的计算和减少数据检索开销。例如，只选择必要的列而不是使用`SELECT *`，这可以提高性能：
   ```sql
   SELECT COUNT(*) FROM table_name WHERE conditions;
   ```

4. **考虑使用`COUNT(*)`替代`COUNT(column_name)`**：在SQLite中，使用`COUNT(*)`通常比使用`COUNT(column_name)`要快，因为它避免了对表中每一行进行计算。

5. **数据库分区**：对于非常大的表，考虑将它们分区成更小的部分。分区可以基于范围、列表或哈希等标准，每个分区独立操作，这允许更快的数据检索和操作。

6. **调整内存设置**：增加缓存大小可以减少磁盘读写次数，提高性能。您可以使用`PRAGMA cache_size`命令调整缓存大小。

7. **使用事务**：对于需要执行多个操作的场景，使用事务可以将多个操作合并为一个工作单元，减少单个操作的开销，从而提高性能。

8. **考虑硬件性能**：使用更快的存储解决方案，如SSD，可以提高读写速度，从而提高SQLite性能。

通过实施上述优化措施，您应该能够显著提高SQLite数据库中`SELECT COUNT(*)`查询的性能。
### 如何使用EXPLAIN QUERY PLAN？
当你使用`EXPLAIN QUERY PLAN`命令分析SQLite查询时，输出结果将提供关于查询如何执行的详细信息。以下是如何解读这些结果的一些关键点：

1. **表和索引的使用**：输出会显示是否使用了表、视图、子查询的名称，以及是否使用了索引或自动索引。如果使用了索引，还会显示是哪个索引被使用，以及WHERE子句中哪些项用于索引。

2. **扫描类型**：你可以看到是进行了全表扫描（`SCAN TABLE`）还是索引查找（`SEARCH TABLE USING INDEX`）。全表扫描意味着数据库将检查表中的每一行，而索引查找则意味着数据库将使用索引来快速定位行。

3. **覆盖索引**：如果查询可以使用覆盖索引（即索引中包含了查询所需的所有列），输出会显示这一点。覆盖索引可以减少数据库访问数据行的次数，提高查询效率。

4. **WHERE子句的使用**：输出会显示WHERE子句中的哪些条件被用于索引查找。例如，如果WHERE子句中有`a=1`，并且有一个索引`i1`在列`a`上，输出可能会显示`SEARCH TABLE USING INDEX i1 (a=?)`。

5. **连接操作**：对于包含连接（JOIN）的查询，输出会显示每个连接操作的顺序和类型。SQLite使用嵌套循环实现所有连接，输出将显示每个循环的顺序和使用的表或索引。

6. **子查询**：如果查询包含子查询，输出会显示这些子查询的执行方式，比如是否为标量子查询（返回单个值）或相关子查询（其值依赖于外部查询的当前行）。

7. **临时排序B树**：如果查询包含`ORDER BY`、`GROUP BY`或`DISTINCT`子句，SQLite可能需要使用临时B树结构对输出行进行排序。如果需要临时B树，输出会显示`USE TEMP B-TREE FOR xxx`，其中`xxx`是`ORDER BY`、`GROUP BY`或`DISTINCT`之一。

通过分析这些信息，你可以识别查询的性能瓶颈，并采取相应的优化措施，比如添加或调整索引、重写查询以利用索引、或者改变查询逻辑以减少全表扫描。
### 参考：
[SQLite 索引（Index）](https://www.runoob.com/sqlite/sqlite-index.html)
[五百万数据 mysql count（*）优化](https://blog.csdn.net/MariaOzawa/article/details/115603713)