
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

	    (1) InnoDB存储引擎
		InnoDB是MySQL的默认事务型引擎，它被设计用来处理大量的短期(short-lived)事务。除非有非常特别的原因需要
		使用其他的存储引擎，否则应该优先考虑InnoDB引擎。
 
		(2) MyISAM存储引擎
		MyISAM提供了大量的特性，包括全文索引、压缩、空间函数(GIS)等，但MyISAM不支持事务和行级锁，有一个毫无疑
		问的缺陷就是崩溃后无法安全恢复。
 
		(3) Archive引擎
		Archive档案存储引擎只支持INSERT和SELECT操作，在MySQL5.1之前不支持索引。
		Archive表适合日志和数据采集类应用。
		根据英文的测试结论来看，Archive表比MyISAM表要小大约75%，比支持事务处理的InnoDB表小大约83%。
 
		(4) Blackhole引擎
		Blackhole引擎没有实现任何存储机制，它会丢弃所有插入的数据，不做任何保存。但服务器会记录Blackhole表的日
		志，所以可以用于复制数据到备库，或者简单地记录到日志。但这种应用方式会碰到很多问题，因此并不推荐。 
 
		(5) CSV引擎 
		CSV引擎可以将普通的CSV文件作为MySQL的表来处理，但不支持索引。
		CSV引擎可以作为一种数据交换的机制，非常有用。
		CSV存储的数据直接可以在操作系统里，用文本编辑器，或者excel读取。
 
		(6) Memory引擎
		如果需要快速地访问数据，并且这些数据不会被修改，重启以后丢失也没有关系，那么使用Memory表是非常有用。
		Memory表至少比MyISAM表要快一个数量级。
 
		(7) Federated引擎
		Federated引擎是访问其他MySQL服务器的一个代理，尽管该引擎看起来提供了一种很好的跨服务器的灵活性，但也经
		常带来问题，因此默认是禁用的。
 
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
4、避免索引失效的几个注意点

	Ⅰ、全值匹配；
	Ⅱ、最佳左前缀法则  如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且不跳过索引中的列；
	Ⅲ、不在索引列上做任何操作（计算、函数、(自动or手动)类型转换），会导致索引失效而转向全表扫描；
	Ⅳ、存储引擎不能使用索引中范围条件右边的列；
	Ⅴ、尽量使用覆盖索引(只访问索引的查询(索引列和查询列一致))，减少select *；
	Ⅵ、mysql 在使用不等于(!= 或者<>)的时候无法使用索引会导致全表扫描；
	Ⅶ、注意null/not null对索引的可能影响 ;
	Ⅷ、like以通配符开头('%abc...')mysql索引失效会变成全表扫描的操作；
	Ⅸ、字符串不加单引号索引失效；
	Ⅹ、少用or,用它来连接时会索引失效； 
	
 【优化口诀】
 
 &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;全职匹配我最爱，最左前缀要遵守；
 
  &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;带头大哥不能死，中间兄弟不能断；
 
  &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;索引列上少计算，范围之后全失效；
 
  &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;LIKE百分写最右，覆盖索引不写*；
 
  &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;不等空值还有OR，索引影响要注意；
 
  &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;VAR引号不可丢， SQL优化有诀窍。
	
### 七、执行计划分析
&emsp;&emsp;使用EXPLAIN关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈。

&emsp;&emsp;合理查看执行计划可以得知语句执行时表的读取顺序、数据读取操作的操作类型、哪些索引可以使用、哪些索引被实际使用、表之间的引用、每张表有多少行被优化器查询等信息。

&emsp;&emsp;使用方式 ： explain + SQL

&emsp;&emsp;执行计划包含的信息：id | select_type | table | type | possible_keys | key | key_len | ref | rows | extra

	Ⅰ、id    select查询的序列号,包含一组数字，表示查询中执行select子句或操作表的顺序
	
	    三种情况 ：①id相同，执行顺序由上至下
	    		  ②id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行
		  		  ③id相同不同，同时存在，id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行
	
	Ⅱ、select_type    查询的类型，主要是用于区别普通查询、联合查询、子查询等的复杂查询
	
	    ① simple  简单的 select 查询,查询中不包含子查询或者UNION
	    ② primary  查询中若包含任何复杂的子部分，最外层查询则被标记为primary
	    ③ subquery  在SELECT或WHERE列表中包含了子查询
	    ④ derived  在FROM列表中包含的子查询被标记为DERIVED(衍生)，MySQL会递归执行这些子查询, 把结果放在临时表里。
	    ⑤ union  若第二个SELECT出现在UNION之后，则被标记为UNION；若UNION包含在FROM子句的子查询中,外层SELECT将被标记为：DERIVED
	    ⑥ uoion result  从UNION表获取结果的SELECT

	Ⅲ、table   显示SQL是关于哪张表的
	
	Ⅳ、type  显示查询使用了何种类型，从最好到最差依次是：system>const>eq_ref>ref>range>index>ALL，一般来说，
			  得保证查询至少达到range级别，最好能达到ref。

		① system   表只有一行记录（等于系统表），这是const类型的特列，平时不会出现，这个也可以忽略不计;
		② const 表示通过索引一次就找到了,const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快
		如将主键置于where列表中，MySQL就能将该查询转换为一个常量;
		③ eq_ref   唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描;
		④ ref   非唯一性索引扫描，返回匹配某个单独值的所有行.本质上也是一种索引访问，它返回所有匹配某个单独值
		的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体;
		⑤ range   只检索给定范围的行,使用一个索引来选择行。key 列显示使用了哪个索引,一般就是在你的where语句中
		出现了between、<、>、in等的查询,这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而
		结束语另一点，不用扫描全部索引;
		⑥ index  遍历索引数据，index与ALL区别为index类型只遍历索引树。这通常比ALL快，因为索引文件通常比数
		据文件小。(也就是说虽然all和Index都是读全表，但index是从索引中读取的，而all是从硬盘中读的）;
		⑦ all   全表扫描，将遍历全表以找到匹配的行。

	Ⅴ、possible_keys  显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，
					   但不一定被查询实际使用。

	Ⅵ、key  实际使用的索引。如果为NULL，则没有使用索引;查询中若使用了覆盖索引，则该索引和查询的select字段重叠。

	Ⅶ、key_len  表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精确性的情况下，长度越短
				 越好。
				 key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是
				 通过表内检索出的。

	Ⅷ、ref  显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值.

	Ⅸ、rows  根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数.

	Ⅹ、extra   包含不适合在其他列中显示但十分重要的额外信息.
	
			   ① Using filesort  说明mysql会对数据使用一个外部的索引排序(会将数据读取到内存，但不能一次全部读
			   					 取，需要分段读取并最后合并结果)，而不是按照表内的索引顺序进行读取。MySQL中无
			   					 法利用索引完成的排序操作称为“文件排序”，性能不好，需要进行优化。
			   					 可能因为order by的顺序没有复用索引的建立顺序，没有保持一致，即索引覆盖。
			   ② Using temporary  使了用临时表保存中间结果,MySQL在对查询结果排序时使用临时表。常见于排序 
			   					  order by 和分组查询 group by，性能极不好，需要进行优化。
			   					  可能因为group by之前未进行order by排序。
			   ③ USING index  表示相应的select操作中使用了覆盖索引(Covering Index)，避免访问了表的数据行，
			   				  效率不错！如果同时出现using where，表明索引被用来执行索引键值的查找;如果没有同
			   				  时出现using where，表明索引用来读取数据而非执行查找动作。
			   ④ Using where   表明使用了where过滤
			   ⑤ using join buffer  使用了连接缓存
			   ⑥ impossible where  where子句的值总是false，不能用来获取任何元组 		  
		
### 八、慢查询日志

&emsp;&emsp;**MySQL的慢查询日志是MySQL提供的一种日志记录，它用来记录在MySQL中响应时间超过阈值的语句，具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。。**

&emsp;&emsp;long_query_time的默认值为10，意思是运行10秒以上的语句。可以通过慢查询日志查看哪些SQL超出了我们的最大忍耐时间值，比如一条sql执行超过5秒钟，我们就算慢SQL，希望能收集超过5秒的sql，结合之前explain进行全面分析。

&emsp;&emsp;默认情况下，MySQL数据库没有开启慢查询日志，需要我们手动来设置这个参数。
 
&emsp;&emsp;当然，如果不是调优需要的话，一般不建议启动该参数，因为开启慢查询日志会或多或少带来一定的性能影响。慢查询日志支持将日志记录写入文件。

&emsp;&emsp;默认情况下slow_query_log的值为OFF，表示慢查询日志是禁用的，可以通过设置slow_query_log的值来开启。
 
		查看慢查询日志是否开启 ： SHOW VARIABLES LIKE '%slow_query_log%' ;
		
		使用set global slow_query_log=1;开启了慢查询日志只对当前数据库生效，如果MySQL重启后则会失效。

		如果要永久生效，就必须修改配置文件my.cnf（其它系统变量也是如此）；
 
		修改my.cnf文件，[mysqld]下增加或修改参数；
		slow_query_log 和slow_query_log_file后，然后重启MySQL服务器。也即将如下两行配置进my.cnf文件；
 
			slow_query_log =1
			slow_query_log_file=/var/lib/mysql/atguigu-slow.log
 
			关于慢查询的参数slow_query_log_file ，它指定慢查询日志文件的存放路径，系统默认会给一个缺省的文件
		host_name-slow.log（如果没有指定参数slow_query_log_file的话）；

		查看当前多少秒算慢 ： SHOW VARIABLES LIKE 'long_query_time%' ;

		查询当前系统中有多少条慢查询记录 ： show global status like '%Slow_queries%' ;

&emsp;&emsp;在生产环境中，如果要手工分析日志，查找、分析SQL，显然是个体力活，MySQL提供了日志分析工具mysqldumpslow。下面是几个常用指令参考：

		得到返回记录集最多的10个SQL
		mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log
 
		得到访问次数最多的10个SQL
		mysqldumpslow -s c -t 10 /var/lib/mysql/atguigu-slow.log
 
		得到按照时间排序的前10条里面含有左连接的查询语句
		mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/atguigu-slow.log
 
		另外建议在使用这些命令时结合 | 和more 使用 ，否则有可能出现爆屏情况
		mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log | more

### 九、主从复制
#### 1、复制的基本原理

**MySQL复制过程分成三步：**

&emsp;1 master将改变记录到二进制日志（binary log）。这些记录过程叫做二进制日志事件，binary log events；

&emsp;2 slave将master的binary log events拷贝到它的中继日志（relay log）；

&emsp;3 slave重做中继日志中的事件，将改变应用到自己的数据库中。 MySQL复制是异步的且串行化的。

![MySQL数据复制过程图解](https://img-blog.csdnimg.cn/20191220094150259.bmp?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

#### 2、复制的基本原则

&emsp;&emsp;1、每个slave只有一个master

&emsp;&emsp;2、每个slave只能有一个唯一的服务器ID

&emsp;&emsp;3、每个master可以有多个salve




 
 
 

			   					  
