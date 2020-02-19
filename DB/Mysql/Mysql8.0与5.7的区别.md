# MySql8.0和5.7的区别
### 隐藏索引
- 隐藏
```
> ALTER TABLE t ALTER INDEX i INVISIBLE;
```
- 恢复
```
> ALTER TABLE t ALTER INDEX i VISIBLE;
```

### 设置持久化配置
```
SET GLOBAL max_connections = 500;
```
> 重启后失效

```
> SET PERSIST max_connections = 500;
```
> MySQL 会将该命令的配置保存到数据目录下的 mysqld-auto.cnf 文件中，下次启动时会读取该文件，用其中的配置来覆盖缺省的配置文件。
### UTF-8编码
Mysql8.0的编码为utf8mb4

### 通用表表达式
#### 嵌入式表查询
- 原:
```
SELECT t1.*, t2.* FROM
	 (SELECT col1 FROM table1) t1,
	 (SELECT col2 FROM table2) t2;
```
- 现：cte
```
WITH
	 t1 AS (SELECT col1 FROM table1),
	 t2 AS (SELECT col2 FROM table2)
	SELECT t1.*, t2.* 
	FROM t1, t2;
```
#### 开窗函数rank()
例：
```
SELECT *, RANK() OVER w AS `rank` FROM classes 
WINDOW w AS (ORDER BY stu_count)
```