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

&emsp;&emsp;&emsp;5、类型（Type）：对应于数据库中的表；每个索引可以有一个或多个Type,Type是Index中的一个逻辑数据分类，每个Type下的Document具有相同的字段(field)，比如一个博客系统，有一个索引，可以定义用户数据Type,博客数据Type,评论数据Type,每个Type下包含多个document。

&emsp;&emsp;&emsp;6、文档（Document）:对应于数据库中的行，最小数据单元，通常用Json数据结构表示；

&emsp;&emsp;&emsp;7、字段（Feild）：最小数据单位，一个Document里面有多个Feild，每个Feild就是一个数据字段；

&emsp;&emsp;&emsp;8、映射（Mapping）：数据如何存放到索引对象上的映射配置，包括数据类型、是否存储、是否分词等；

&emsp;Ⅳ、特点：

&emsp;&emsp;&emsp;1、既可以作为大型分布式集群技术处理PB级数据服务大公司，也可以单独运行在单机服务器上服务小公司；

&emsp;&emsp;&emsp;2、合并了全文检索、数据分析、和分布式技术，商用的数据分析软件、分布式数据库、结构化检索和分析；

&emsp;&emsp;&emsp;3、部署容易，操作简单；

&emsp;&emsp;&emsp;4、相比传统数据库在很多领域不够用(事务、各种联机事务型操作)，但也有特别之处：全文检索、同义词处理、相关度排名、复杂数据处理分析、海量数据的近实时性处理等。


                                                                        
