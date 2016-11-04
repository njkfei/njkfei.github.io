title: MySql编码规范
date: 2016.10.20
categories: mysql #分类
tags: [mysql,性能]
toc: true
---
## 前言
* mysql是互联网事实上的标准
* mysql性能对付中小型网站足够了，在没有到性能瓶颈前，可以先不上缓存系统。
* MySQL的查询速度依赖良好的索引设计，因此索引对于高性能至关重要。合理的索引会加快查询速度，不合理的索引会降低速度。

## 规则
### 表设计
1. 库名、表名、字段名必须使用小写字母，“_”分割。
1. 库名、表名、字段名必须不超过12个字符。
1. 库名、表名、字段名见名知意,建议使用名词而不是动词。
1. 强制使用InnoDB存储引擎。
1. 必须有惟一键
1. 存储精确浮点数必须使用DECIMAL替代FLOAT和DOUBLE。
1. 建议使用UNSIGNED存储非负数值。
1. 建议使用INT UNSIGNED存储IPV4。
1. 整形定义中不添加长度，比如使用INT，而不是INT(4)。
1. 使用短数据类型，比如取值范围为0-80时，使用TINYINT UNSIGNED。
1. 不建议使用ENUM类型，使用TINYINT来代替。
1. 尽可能不使用TEXT、BLOB类型。
1. VARCHAR(N)，N表示的是字符数不是字节数，比如VARCHAR(255)，可以最大可存储255个汉字，需要根据实际的宽度来选择N。
. VARCHAR(N)，N尽可能小，因为MySQL一个表中所有的VARCHAR字段最大长度是65535个字节，进行排序和创建临时表一类的内存操作时，会使用N的长度申请内存。
1. 表字符集选择UTF8。
1. 使用VARBINARY存储变长字符串。
1. 存储年使用YEAR类型。
1. 存储日期使用DATE类型。
1. 存储时间（精确到秒）建议使用TIMESTAMP类型，因为TIMESTAMP使用4字节，DATETIME使用8个字节。
1. 建议字段定义为NOT NULL。
1. 将过大字段拆分到其他表中。
1. 禁止在数据库中使用VARBINARY、BLOB存储图片、文件等。
1. 表结构变更需要通知DBA审核。

### 索引
1. 非唯一索引必须按照“idx_字段名称_字段名称[_字段名]”进行命名。
1. 唯一索引必须按照“uniq_字段名称_字段名称[_字段名]”进行命名。
1. 索引名称必须使用小写。
1. 索引中的字段数建议不超过5个。
1. 单张表的索引数量控制在5个以内。
1. 唯一键由3个以下字段组成，并且字段都是整形时，使用唯一键作为主键。
1. 没有唯一键或者唯一键不符合5中的条件时，使用自增（或者通过发号器获取）id作为主键。
1. 唯一键不和主键重复。
1. 索引字段的顺序需要考虑字段值去重之后的个数，个数多的放在前面。
1. ORDER BY，GROUP BY，DISTINCT的字段需要添加在索引的后面。
1. 使用EXPLAIN判断SQL语句是否合理使用索引，尽量避免extra列出现：Using File Sort，Using Temporary。
1. UPDATE、DELETE语句需要根据WHERE条件添加索引。
1. 不建议使用%前缀模糊查询，例如LIKE “%weibo”。
1. 对长度过长的VARCHAR字段建立索引时，添加crc32或者MD5 
1. Hash字段，对Hash字段建立索引。
1. 合理创建联合索引（避免冗余），(a,b,c) 相当于 (a) 、(a,b) 、(a,b,c)。
1. 合理利用覆盖索引。
1. SQL变更需要确认索引是否需要变更并通知DBA。

### SQL语句

1. 使用prepared statement，可以提供性能并且避免SQL注入。
1. SQL语句中IN包含的值不应过多。
1. UPDATE、DELETE语句不使用LIMIT。
1. WHERE条件中必须使用合适的类型，避免MySQL进行隐式类型转化。
1. SELECT语句只获取需要的字段。
1. SELECT、INSERT语句必须显式的指明字段名称，不使用SELECT *，不使用INSERT INTO table()。
1. 使用SELECT column_name1, column_name2 FROM table WHERE [condition]而不是SELECT column_name1 FROM table WHERE [condition]和SELECT column_name2 FROM table WHERE [condition]。
1. WHERE条件中的非等值条件（IN、BETWEEN、<、<=、>、>=）会导致后面的条件使用不了索引。
1. 避免在SQL语句进行数学运算或者函数运算，容易将业务逻辑和DB耦合在一起。
1. INSERT语句使用batch提交（INSERT INTO table VALUES(),(),()……），values的个数不应过多。
1. 避免使用存储过程、触发器、函数等，容易将业务逻辑和DB耦合在一起，并且MySQL的存储过程、触发器、函数中存在一定的bug。
1. 避免使用JOIN。
1. 使用合理的SQL语句减少与数据库的交互次数。
1. 不使用ORDER BY RAND()，使用其他方法替换。
1. 建议使用合理的分页方式以提高分页的效率。
1. 统计表中记录数时使用COUNT(*)，而不是COUNT(primary_key)和COUNT(1)。
1. 禁止在从库上执行后台管理和统计类型功能的QUERY。

### 散表

1. 对于字段类型简单，字段数不多表，单表数据量建议控制在5000w以下。
1. 可以结合使用hash1、range、lookup table进行散表。
1. 散表如果使用md5（或者类似的hash算法）进行散表，表名后缀使用16进制，比如user_ff。
1. 推荐使用CRC32求余（或者类似的算术算法）进行散表，表名后缀使用数字，数字必须从0开始并等宽，比如散100张表，后缀从00-99。
1. 使用时间散表，表名后缀必须使用特定格式，比如按日散表user_20110209、按月散表user_201102。

### 其他

1. 批量导入、导出数据需要DBA进行审查，并在执行过程中观察服务。
1. 批量更新数据，如update,delete 操作，需要DBA进行审查，并在执行过程中观察服务。
1. 产品出现非数据库平台运维导致的问题和故障时，如前端被抓站，请及时通知DBA，便于维护服务稳定。
1. 业务部门程序出现bug等影响数据库服务的问题,请及时通知DBA，便于维护服务稳定。
1. 业务部门推广活动，请提前通知DBA进行服务和访问评估。
1. 如果出现业务部门人为误操作导致数据丢失，需要恢复数据，请在第一时间通知DBA，并提供准确时间，误操作语句等重要线索。


## 感谢
[MySQL开发规范](http://www.imcjd.com/?p=1237)