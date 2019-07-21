## n+1问题
为了查询主数据，1次查询，查询出n个记录，从这n个记录里查询从记录，又需要n次，这就是1+n问题。
解决方法是：
	懒加载、使用join、二级缓存

## 为什么SELECT * FROM table WHERE field = null不能匹配空的字段
在MySQL中所有与NULL的比较返回的都是false，所以使用=null或!=null并不能得到效果
MySQL中处理NULL使用IS NULL和IS NOT NULL运算符
mysql
## 什么是CRQS和Command-QuerySeperation原则有什么区别
CRQS(CommandQueryResponsibilitySegregation)是读写责任分离，责任是根据具体业务来的，读不仅仅是指的数据库意义上的读操作，而是根据业务需求，为复杂业务时的读操作，专门建立数据库以供直接读取去展示界面。
Command-QuerySeperation是读写分离，是针对数据库层面的，主数据库写，从数据库读

## 分页优化 这个好好看有点多、难

## 索引优化
一般来说但我们指定一个表的主键，如果这个表之前没有聚集索引，而且主键没有强制指定非聚集索引，则SQL会默认在主键上建立聚集索引，但理论上(但是不推荐)任何一列字段都可以是聚集索引。
**聚集索引和非聚集索引**
一个表里只能有一个聚集索引，而非聚集索引可以存在多个
聚集索引存储记录是物理上连续存在，而非聚集索引是逻辑上的连续，物理存储并不连续
聚集索引的表记录排列顺序和索引排列顺序一致，而非聚集索引不一致。
聚集索引插入数据时速度要慢，查询速度要快
索引是通过二叉树的形式描述的，聚集索引的叶节点是最终的数据节点，但是非聚集索引的叶节点仍然是索引节点

## InnoDB、MyISAM、MEMORY区别
InnoDB是一个健全的事务型存储引擎，这种存储引擎已经被很多互联网公司使用，为用户操作非常大的数据存储提供了强大的解决方案。InnoDB还引入了行级锁定和外键约束。当表更新密集时适合使用InnoDB。一般来说，如果需要事务支持且有较高的并发读取频率，InnoDB是不错的选择
用MyISAM引擎的表独立于操作系统，意味着他有很好的可移植性。每当我们建立一个引擎的表的时候，就会在本地磁盘上建立三个文件，文件名就是表名.frm、.MYD、.MYI。MyISAM表无法处理事务，这就意味着有事务处理需求的表，不能使用MyISAM。MyISAM适合选择密集型和插入密集型的表。
使用MEMORY引擎的出发点就是速度。为了得到最快的响应时间，使用系统内存作为逻辑存储介质，虽然性能很高，但是一旦mysqld守护进程崩溃，所有的memory数据都会丢失。且它要求存储在MEMORY数据表的数据是长度不变的类型，这意味着不能使用Blob和Text这样的长度可变的数据类型(但是varchar是ok的，它在MySQL内部当做长度固定不变的CHAR类型)。一般如果你的数据较小且被频繁的访问或数据是临时且要求必须立即可用，数据即使丢失也没有影响，则考虑使用MEMORY。
## InnoDB和MyISAM在事务、锁级别各自的适用场景
InnoDB是非事务安全型的，MyISAM是非事务安全型的
InnoDB是行级锁，MyISAM是表级锁
## mysql的各种锁，select时怎么加排它锁
乐观锁：数据库没有自带，需要自己实现，在表中的数据进行操作时(更新)，先给数据表加一个版本(version)字段，每操作一次，将那条记录的版本号加1。也就是先查询出那条记录，获取出version字段,如果要对那条记录进行操作(更新),则先判断此刻version的值是否与刚刚查询出来时的version的值相等，如果相等，则说明这段期间，没有其他程序对其进行操作，则可以执行更新，将version字段的值加1；如果更新时发现此刻的version值与刚刚获取出来的version的值不相等，则说明这段期间已经有其他程序对其进行操作了，则不进行更新操作。
悲观锁：共享锁和排他锁
共享锁(S锁)：多个不同的事务对同一个资源共享同一个锁，都能访问到数据只能读不能修改。使用方法是在操作后面加上lock in share mode
排他锁(X锁)：一个事务获取一个资源的排他锁，其他事务不能再获取其他锁，获取排他锁的事务可以对数据进行读取和修改。使用方法是在操作后面加上for update。InnoDB默认情况下，update、delete、insert都会加入排他锁。
## 非关系型数据库和关系型数据库区别、优势
## 数据库的读写分离、主从复制、主从复制分析的七个问题
## 使用explain优化sql和索引
在select前加上explain使用
返回结果
id:select查询序列号
select_type:
	simple 最简单的select，没有union和子查询
	primary 有子查询的时候最外面的子查询
	union union语句的第二个
	dependent union union中的第二个或后面的select语句
	union result union的结果
table:
	输出的行所使用的表
type:
	连接的类型
possible_keys: 可能应用在这张表中的索引
key: 实际使用的索引,null代表没有使用索引
key_len: 使用的索引的长度，在不损失精确性的情况下，长度越短越好
ref:
	索引的那一列被使用了
rows:
	mysql认为必须检查的用来返回请求数据的行数，数值越大越不好，说明索引没有用好
extra:
	mysql是如何解析查询的
	值using temporary和use filesort是最差的情况，意味着mysql根本不能使用查询
## varchar & char
varchar空间可变，但是容易产生碎片
char没有碎片问题，但是空间往往占用大
## MySQL慢查询怎么解决
开启慢查询日志，方法是取消下面代码的注释
#log_slow_queries = var/log/mysql/mysql-slow.log
#long_query_time = 2
#log-queries-not-using-indexes
使用日志分析工具
## MySQL高并发环境解决方案
分库、分表：单点分布到多点数据库降低数据库压力
分布式：负载均衡、读写分离、集群等等
增加二级缓存

## select count(*) 和 select count(1)和count(列名)的原理、效应
	count(*)包含了所有的列在统计结果时不会忽略列值为NULL
	count(1)包含了忽略所有列，用1代表，在统计结果时不会忽略列值为NULL
	count(列名)只包含列名那一行，在统计结果的时候会忽略列值为NULL
	执行效率上：
		列名为主键 select count(列名)最快
		不为主键 select count(1)快
		多个且无主键 select count(1)比select count(*)快
	但是听说sql对select count(*)有优化，所以基本上count(1)和count(*)没差别

