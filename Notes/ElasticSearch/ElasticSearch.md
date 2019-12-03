## ElasticSearch(分布式全文检索搜索引擎)
&emsp;Ⅰ、全文检索：

&emsp;&emsp;&emsp;计算机索引程序通过扫描文章中的每一个词，对每一个词建立一个索引（记录出现的次数和位置），当用户查询时，检索程序根据索引进行查找，
并将查找结果反馈给用户。

                 数据库中数据                    切词                      倒排索引

               id       string                                         关键词    id            
               1        生化危机    --->       生化  危机     --->       生化      1
               2        人生苦短               人生  苦短                危机      1，3
               3        人生危机               人生  危机                苦短      2
                                                                        人生      2，3
&emsp;Ⅱ、ElasticSearch基于lucence,隐藏复杂性；

&emsp;Ⅲ、核心概念：

&emsp;&emsp;&emsp;1、近实时

&emsp;&emsp;&emsp;2、集群（cluster）

&emsp;&emsp;&emsp;3、节点（Node）

&emsp;&emsp;&emsp;4、索引（Index）：索引包含一堆有相似结构的文档数据,对应于数据库中的数据库；

&emsp;&emsp;&emsp;5、类型（Type）：对应于数据库中的表；

&emsp;&emsp;&emsp;每个索引可以有一个或多个Type,Type是Index中的一个逻辑数据分类，一个Type下的Document具有相同的字段（field），比如一个博客系统，有一个索引，可以定义用户数据Type,博客数据Type,评论数据Type,每个Type下包含多个document。
                                                                        
