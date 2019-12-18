### 一、MySQL逻辑架构
	MySQL的逻辑架构分为连接层、服务层、存储引擎层和存储层。

![MySQL逻辑架构](https://img-blog.csdnimg.cn/20191218085313209.bmp?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)
		
		    和其它数据库相比，MySQL有点与众不同，它的架构可以在多种不同场景中应用并发挥良好作用。主要体现在存储引擎的架构上，插件式
	    的存储引擎架构将查询处理和其它的系统任务以及数据的存储提取相分离。这种架构可以根据业务的需求和实际需要选择合适的存储引擎。

1.连接层(Connectors)

		    最上层是一些客户端和连接服务，包含本地socket通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信。主要完成一些类似于
		连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现
		基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。
 
2.服务层(MySQL Server)

			第二层架构主要完成大多少的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎
		的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺
		序，是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量
		读操作的环境中能够很好的提升系统的性能。
 
3.引擎层(Pluggable Storage Engines)

			存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，
		这样我们可以根据自己的实际需要进行选取。
 
4.存储层(FileSystem)

			数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

###  二、如何用命令查看存储引擎？
  **1、查看Mysql现在已提供什么存储引擎:**
  
  		mysql> show engines;
  
  **2、查看Mysql当前默认的存储引擎:**
  
		mysql> show variables like '%storage_engine%';

### 三、MyISAM和InnoDB的区别

| 对比项 | MyISAM |  InnoDB|
|--|--|--|
|主外键  |不支持  |支持 |
| 事务 | 不支持  |支持 |
| 行表锁 |表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作  |行锁,操作时只锁某一行，不对其它行有影响，适合高并发的操作 |
| 缓存 | 只缓存索引，不缓存真实数据 |不仅缓存索引还要缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 表空间 | 小 | 大|
|关注点  | 性能 | 事务|
| 默认安装 |Y  | Y|

### 四、MySQL语句执行顺序(一般情况下)
![SQL语句执行顺序](https://img-blog.csdnimg.cn/20191218092934133.bmp?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)
### 五、7种Join

1、   A、B两表共有
	
 	select * from tbl_emp a inner join tbl_dept b on a.deptId = b.id;
 
2、 A、B两表共有+A的独有

	 select * from tbl_emp a left join tbl_dept b on a.deptId = b.id;
 
3、 A、B两表共有+B的独有

	 select * from tbl_emp a right join tbl_dept b on a.deptId = b.id;
 
4、 A的独有 

	 select * from tbl_emp a left join tbl_dept b on a.deptId = b.id where b.id is null; 
 
5 、B的独有

	 select * from tbl_emp a right join tbl_dept b on a.deptId = b.id where a.deptId is null; #B的独有
 
6 、AB全有

	#MySQL Full Join的实现 因为MySQL不支持FULL JOIN,下面是替代方法
	#left join + union(可去除重复数据)+ right join
	
	SELECT * FROM tbl_emp A LEFT JOIN tbl_dept B ON A.deptId = B.id
	UNION
	SELECT * FROM tbl_emp A RIGHT JOIN tbl_dept B ON A.deptId = B.id
 
7、 A的独有+B的独有

	SELECT * FROM tbl_emp A LEFT JOIN tbl_dept B ON A.deptId = B.id WHERE B.`id` IS NULL
	UNION
	SELECT * FROM tbl_emp A RIGHT JOIN tbl_dept B ON A.deptId = B.id WHERE A.`deptId` IS NULL;

### 六、索引
1、索引是什么？
	
		MySQL官方对索引的定义为：索引（Index）是帮助MySQL高效获取数据的数据结构。所以索引是一种数据结构。索引的目的在于提高查询效率。
		
		数据本身之外，数据库还维护着一个满足特定查找算法的数据结构，这些数据结构以某种方式指向数据，这样就可以在这些数据结构的基础上实
	现高级查找算法，这种数据结构就是索引。
	
		一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上，我们平常所说的索引，如果没有特别指明，
	都是指B树(多路搜索树，并不一定是二叉的)结构组织的索引。
	
		通过索引列对数据进行排序，降低数据排序的成本，降低了CPU的消耗。

		实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录，所以索引列也是要占用空间的，虽然索引大大提高了查询速度，
	同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新
	添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息。
	
		索引只是提高效率的一个因素，如果你的MySQL有大数据量的表，就需要花时间研究建立最优秀的索引，或优化查询语句。

2、索引分类

	Ⅰ、单值索引：即一个索引只包含单个列，一个表可以有多个单列索引；

	Ⅱ、唯一索引：索引列的值必须唯一，但允许有空值；

	Ⅲ、复合索引：即一个索引包含多个列；

3、索引基本语法

	Ⅰ、创建：
	       (1) CREATE  [UNIQUE ] INDEX indexName ON mytable(columnname(length)); 
	       (2) ALTER TABLE 表名 ADD  [UNIQUE ]  INDEX [indexName] ON (columnname(length)) ;
	             
				ALTER TABLE tbl_name ADD PRIMARY KEY (column_list): 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
				ALTER TABLE tbl_name ADD UNIQUE index_name (column_list): 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。
				ALTER TABLE tbl_name ADD INDEX index_name (column_list)；
	Ⅱ、删除：
	       DROP INDEX [indexName] ON mytable; 
	Ⅲ、查看：
			SHOW INDEX FROM table_name\G；
	


